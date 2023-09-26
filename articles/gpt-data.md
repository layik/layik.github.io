---
permalink: /gptdata.html
---
[Home](https://layik.github.io) | About | Current
<hr/>

Semantic Processing using ChatGPT

Last edited: Sep 2023

My usage of ChatGPT has been wide and frequent from day one I can say without exaggerating. One use that I found accidentally was not a copilot case of asking the machines to "give me code to do data wrangling" but rather doing the data wrangling for me. And not doing this in a rigid workflow but rather using whatever the GPT model uses to structure knowledge. How did I go about this simple example? Read on to find out.

I saw something on Wikipedia which triggered the question: what are the widely translated articles on Wikipedia? There is actually a special page called [Wikipedia:Wikipedia articles written in the greatest number of languages](https://en.wikipedia.org/wiki/Wikipedia:Wikipedia_articles_written_in_the_greatest_number_of_languages).

I wrote (around March 2023) this line of JS on the console of that [page](https://en.wikipedia.org/wiki/Wikipedia:Wikipedia_articles_written_in_the_greatest_number_of_languages) to get those article names:

```js
[...document.getElementsByClassName("mw-parser-output")[0].querySelectorAll("p")].map(e => e.innerText.includes(":") ? e.innerText.replace(/:.* Wikipedias/i, "") : "").slice(1)
```

And then, I thought, well can ChatGPT group them for me to see what topics are most translated? Yes it can. Having copied the output of the console into the paste board, I gave it to ChatGPT a simple prompt: "Here is a json, group the contents" plus the JSON at the end of this article followed by "Just the groups and member counts" prompt. The result after a couple "regenerate response" was like:

Sure, here are the counts for each group:

1. Countries and regions: 63
2. Languages and language-related items: 2
3. Celestial objects: 2
4. Animals: 3
5. Religions and religious figures: 9
6. Sciences: 8
7. Historical figures: 9
8. Music and entertainment: 8
9. Concepts and ideas: 18
10. Miscellaneous: 12

As we can see the total grouped is 134 (63 + 2 + 2 + 3 + 9 + 8 + 9 + 8 + 18 + 12), while the list of titles provided to the model was 197. Indeed, as of 26th September 2023, when asked ChatGPT4+ model to sum the numbers in the aboe 10 item list, it did list the numbers correctly to (63 + 2 + 2 + 3 + 9 + 8 + 9 + 8 + 18 + 12) but came up with 135. Upon telling the machine it was failing its maths it apologised and confirmed the sum to be 134.

The key to all this is not how the OpenAI's ChatGPT does the processing but how the model does the semantic grouping of the topics. For example grouping the "relevant" topics under `Countries and regions` (63 of them) rather than "Geographic regions" or any other topic name. This is an area of research which I have been exposed to back in 2015 whilst working on a knowledge structuring project; specifically looking at replicating www.freebase.com which became Google Knowledge Graph.

How this is done in the GPT model by OpenAI is statistical or "random" rather than having a team of experts (as it was the case for www.freebase.com) to define an "ontology" and then structuring human kowledge around such an ontology. This statistical semantic processing is what I mean when I say "how". I am aware what the experts would come up with would be a [Delphi](https://en.wikipedia.org/wiki/Delphi_method) method of doing the semantic structuring of human knowledge. How the OpenAI model does it, I hope but doubt it, if ever defined would be worth studying and dissecting.

This short article is just a refresher for me but also an area that I would be following as NLP (Natural Language Processing and LLMs (Large Language Models) continue to develop. As a snapshot of publications, as of 26th Sep 2023 if you search on Google Scholar for [`"semantic processing" "chatgpt"`](https://scholar.google.com/scholar?hl=en&as_sdt=0%2C5&q=%22semantic+processing%22+%22chatgpt%22&btnG=) you would get about 67 results, if you add "ontology" to the keywords, there were around 12 results only.

Since drafting this article, I have done a little more with the model, but have asked the model to limit the scope of the semantic grouping and used free/unstructured text rather than the clean data at the end of this article.

The output from above JS line is below in case you need it. I cannot remember if I used the free GPT3.5 or the GPT4 model and would welcome your feedback.

```json
[
  "Russia",
  "Finland",
  "Japan",
  "Mexico",
  "United States",
  "Türkiye",
  "Italy",
  "China",
  "English language",
  "Africa",
  "Wikipedia",
  "Europe",
  "Asia",
  "Earth",
  "India",
  "Canada",
  "Ukraine",
  "Australia",
  "Brazil",
  "United Kingdom",
  "Austria",
  "Vietnam",
  "Azerbaijan",
  "Argentina",
  "Sun",
  "Egypt",
  "Bulgaria",
  "Iran",
  "Belgium",
  "Albania",
  "South Africa",
  "Belarus",
  "United Nations",
  "Czech Republic",
  "United Nations",
  "Israel",
  "Algeria",
  "Andorra",
  "Armenia",
  "Paris",
  "Moon",
  "Afghanistan",
  "Bosnia and Herzegovina",
  "Bangladesh",
  "Republic of Ireland",
  "New Zealand",
  "Jesus",
  "Dog",
  "Iraq",
  "Angola",
  "Bible",
  "Water",
  "Rome",
  "Cyprus",
  "Michael Jackson",
  "Ghana",
  "Bolivia",
  "North America",
  "Islam",
  "South America",
  "Botswana",
  "Christianity",
  "Bhutan",
  "New York City",
  "Geography",
  "Benin",
  "Animal",
  "Brunei",
  "Barack Obama",
  "Bahrain",
  "Mathematics",
  "Language",
  "Burkina Faso",
  "Arabic",
  "Physics",
  "Qatar",
  "Belize",
  "Donald Trump",
  "Science",
  "Leonardo da Vinci",
  "Philosophy",
  "Niger",
  "Jerusalem",
  "Continent",
  "Adolf Hitler",
  "Gold",
  "The Bahamas",
  "Music",
  "Association football (Soccer)",
  "World War II",
  "Planet",
  "Barbados",
  "Religion",
  "Isaac Newton",
  "Mediterranean Sea",
  "Star",
  "Antigua and Barbuda",
  "Albert Einstein",
  "Christmas",
  "California",
  "Nelson Mandela",
  "Corbin Bleu",
  "Buddhism",
  "Iron",
  "Human",
  "Internet",
  "World War I",
  "Human",
  "Hinduism",
  "Julius Caesar",
  "Ocean",
  "Milk",
  "George W. Bush",
  "Soviet Union",
  "The Beatles",
  "Judaism",
  "Elizabeth II",
  "Maize",
  "Muhammad",
  "Eye",
  "Periodic table",
  "Facebook",
  "Sports",
  "Buddha",
  "Silver",
  "Alexander the Great",
  "God",
  "Abraham Lincoln",
  "Heart",
  "Money",
  "George Washington",
  "Socrates",
  "Mahatma Gandhi",
  "Galileo Galilei",
  "Quran",
  "YouTube",
  "Mecca",
  "Medicine: 185 Wikipedas",
  "Insect",
  "Rice",
  "Economics",
  "Basketball",
  "Food",
  "Universe",
  "Time",
  "Brain",
  "Toronto",
  "Elvis Presley",
  "Alphabet",
  "Gibraltar",
  "Middle East",
  "Catholic Church",
  "Love",
  "Google",
  "Energy",
  "Lady Gaga",
  "Middle Ages",
  "Death",
  "Friedrich Nietzche",
  "Life",
  "A",
  "Steve Jobs",
  "Cristiano Ronaldo",
  "Lionel Messi",
  "Nature",
  "Abraham",
  "Logic",
  "Microsoft",
  "Eminem",
  "Human Rights",
  "Country",
  "New Testament",
  "Tennis",
  "Justin Bieber",
  "Madonna",
  "Ancient Greece",
  "Pope Benedict XVI",
  "Bone",
  "Moses",
  "Peace",
  "Organism",
  "Simón Bolívar",
  "Old Testament",
  "Freddie Mercury",
  "Ontario",
  "Natural Numbers",
  "Stephen Hawking",
  "Pope John Paul II",
  "Commonwealth of Nations",
  "Vertebrates",
  "North Pole",
  "Ireland",
  "Osama Bin Laden",
  "Jainism",
  "Soul",
  "Palestine",
  "province"
]
```

