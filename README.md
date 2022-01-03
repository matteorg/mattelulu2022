# Premise
Il seguente readme file è scritto in inglese. Se preferisci leggere in italiano, per una traduzione accurata puoi usare __deepl.com__.
Das folgende Readme-File ist in Englisch verfasst. Wenn du lieber auf Deutsch lesen möchtest, könntest du __deepl.com__ für eine genaue Übersetzung nutzen.

# Wedding Website
A beautiful, feature rich, device friendly wedding website.  
_See [mattelulu.ch](http://www.mattelulu.ch/) for a demo. Use invite code `to-be-posted-soon` to Enter (if you are a guest, you'll know it)._

# Highlights
1. Slick and fully __responsive__ design.
2. __RSVP feature__ which directly uploads data to a Google sheet.
3. __Receive email alerts__ when someone RSVPs.
4. __Add to Calendar__ feature which supports four different calendars.
6. A nice __Youtube video__ showing the town of the weedding location.
7. __Google Map__ showing your venue's location.
8. __Password Protected__ homepage, so that you can give your personal access password to your guests
9. Start and run the website (almost) __completely free__. No hosting, backend server, or database required as you can use
   [GitHub Pages](https://pages.github.com/) to host and Google sheets (with the help of Google scripts) to store response
   data.

# Getting Started
1. `$ cd wedding-website` - go inside the project directory
2. `$ npm install` - install dependencies _(optional)_
3. `$ gulp` - compile sass to css, minify js, etc. _(optional)_
4. That's it, open `index.html` on your browser by just double-clicking on the file.

# Uploading to Google Sheet

If you want the responses to be stored in an Excel-like file, you can simply use __Google Sheets__.

First, start a new tab and open [Google Sheets](https://docs.google.com/spreadsheets/u/0/?tgif=d). The start a new blank sheet.

When you're on the new sheet, you can go to __Extensions__--->__App Scripts__

![Screen Shot 2022-01-03 at 10 24 22](https://user-images.githubusercontent.com/41672045/147915709-1009fcce-887c-4495-8cbd-8ea15866b8cf.png)

