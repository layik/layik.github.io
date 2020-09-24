---
permalink: /spenser.html
---
[Home](https://layik.github.io) | About | Current
<hr/>


# Crunching tens of GBs of CSV

<img src="https://user-images.githubusercontent.com/408568/94144989-c8140d80-fe69-11ea-80ff-99e1de9cbc84.png" alt="spenser screenshot" width="100%">

[SPENSER](https://lida.leeds.ac.uk/research-projects/spenser-synthetic-population-estimation-and-scenario-projection-model/) output is some ~90GB of CSVs. How do we query it to feed into the eAtlas?

The data is saved in CSV files with a name that contains geography and time like: `ssm_E08000022_MSOA11_ppp_2050.csv`. The content of this file using a `head` Unix command is:

```sh
PID,Area,Sex,Age,Ethnicity
0,E02001738,1,1,2
1,E02001738,1,1,2
2,E02001738,1,1,2
3,E02001738,1,1,2
4,E02001738,1,1,2
5,E02001738,1,1,2
6,E02001738,1,1,2
7,E02001738,1,1,2
8,E02001738,1,1,2
```
Combining 12,928 files resulted in 675,447,329 rows of data like above. Processing the files could not be done on 16GB machine.

The task involved heavy processing on a 64GB machine without knowing what the final output would be like. So it was a leap into the dark in any case but the task itself was not easy to process in R, and although I have not tested it, I doubt it would be orders of magnitude faster in Python.

I started with running a simple script of bringing date parameter (year) which was encoded into the file names and just count same rows using a simple R function such as `dplyr::count`.

The main lines of the script looked like this:

```r
year = 2010
csv = read.csv(paste0("file-name-with-year-"+ year + ".csv"))
# remove unique PID
csv$PID = NULL
csv$Year = 2010
# count them
spenser = count(spenser, vars = names(spenser))
```
This simple operation was taking days. R NOT being multithreaded dividing the dataset into chunks was of no immediate benefit either. It looked like we would need a super computer to do this. After this step, we then had to squash the age values (1 to 70+) to some age range like (1-12 => 1, 13-17 => 2 etc...) and count those and the current counts again to get a final count of each row.


Then came R Data Table package. Instead of reading with `read.csv` I used the blazing fast `fread` and though `.N` would be faster I still kept the bit of `dplyr` in there and suddenly within about 12 hours on the 64GB machine, I could collapse about 40GB of data into 1.2GB of plain CSV. So the above chunk would look like:

```sh
Area,Sex,Age,Ethnicity,Year,freq
E02001738,1,1,2,2050,9
```

The magic all happened in two lines where sum of unique rows and previous counts were done in the `j` section of R Data Table:

```r
# convert ages 1-100 to 1-9
# ...

# DT[i, j, by] like SQL:  where | order by   select | update  group by
# 1
csv = csv[, list(freq=.N, tot=sum(freq)), by=eval(names(csv)[1:5])]
# sum and remove freq & tot
# 2
csv = csv[, sum := freq+tot][,!(freq:tot)] # weird again, I know
```

It is not so important to understand the lines here. But just to clarify for those interested: line (1) the `list` pased to `DT` (R Data table) is two functions: one to sum what are now more unique rows due to the conversion of ages and this is happening in `tot=sum(freq)`. The second function is `freq=.N` itself which would then calculate the number of unique rows without the original `freq` column hence `1:5` and excluding `freq` column. Then in line (2) we just sum the number of unique rows and their previous counts to make a final "tot" column.

These steps were all so fast that the time was about 6 hours for the entire detaset.

## Searching through final output

So now that we had something like:
```sh
Area,Sex,Age,Ethnicity,Year,tot
E02001738,1,1,2,2050,9
E02001738,1,2,2,2050,19
E02001738,1,3,3,2050,119
E02001738,1,4,2,2050,1119
...
```
...but we ended up with some 52M of them. How do we search this so fast that the response time would be acceptable. And I mean a data scientist or an analyst somewhere can wait seconds but even that would be too slow considering [UX](https://www.nngroup.com/articles/website-response-times/).

So it would take more than 10 seconds to search through those columns even using `DT` using a subsetting line such as:
`csv[Age == 1 && Sex == 1 && Ethnicity === 2 && Year == 2050,]`

## More encoding

I then experimented with searching one column and obviously that was much faster. Therefore, I combined all the columns (Sex, Age, Ethnicity and Year) into one value and tested again. So the data looked looks like this in its final form:
```sh
Area,SAEY,tot
E02001738,1122050,9
E02001738,1222050,19
E02001738,1332050,119
E02001738,1422050,1119
...
```
Now we can subset the rows with one column: `csv[SAEY == 1122050,]`. I actually experimented with this both as a string and inter/numeric on Python and R Data Table and R Data Table beats Python by half time in the numeric form on the same machine of course. In the string form, the R Data Table is still faster but by a minor margin.

Here is a real result of the server using this command:

```sh
curl -o /dev/null -s -w %{time_total}\\n  http://spenser.geoplumber.com/api/spenser

0.321342
```

The distance was from Leeds, UK to somewhre in Germany with server spec being:

```sh
Vendor ID:           GenuineIntel
CPU family:          6
Model:               85
Model name:          Intel Xeon Processor (Skylake, IBRS)
Stepping:            4
CPU MHz:             2100.000
BogoMIPS:            4200.00
Hypervisor vendor:   KVM
Virtualization type: full
L1d cache:           32K
L1i cache:           32K
L2 cache:            4096K
L3 cache:            16384K
```

The front-end is managed by [eAtlas](https://github.com/layik/eAtlas) where about ~51mb geometry file is downloaded from GitHub only the first time the app is loaded. As the user updates the map with new data fetched from the server from the data table mentioned, eAtlas updates the map using the amazing DeckGL/Mapbox libraries.