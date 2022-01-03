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

# Send email and upload response to Google Sheet

If you want the responses to be stored in an Excel-like file, you can simply use __Google Sheets__.

First, start a new tab and open [Google Sheets](https://docs.google.com/spreadsheets/u/0/?tgif=d). The start a new blank sheet.

When you're on the new sheet, you can go to __Extensions__--->__App Scripts__

![Screen Shot 2022-01-03 at 10 24 22](https://user-images.githubusercontent.com/41672045/147915709-1009fcce-887c-4495-8cbd-8ea15866b8cf.png)

Once the new page is open, the __Code.gs__ window should be automatically open. There, you can replace the existing code with the following.

```JavaScript
var TO_ADDRESS = "youremailaddress"; // email to send the form data to

/**
 ** This method is the entry point.
 */
function doPost(e) {

  try {
    Logger.log(e); // the Google Script version of console.log see: Class Logger
    
    var mailData = e.parameters; // just create a slightly nicer variable name for the data
    
    record_data(e);
    
    MailApp.sendEmail({
      to: TO_ADDRESS,
      subject: "Un ospite ha risposto al tuo invito!", // a guest replied to your invitation
      replyTo: String(mailData.email), // This is optional and reliant on your form actually collecting a field named `email`
      htmlBody: formatMailBody(mailData)
    });

    return ContentService    // return json success results
          .createTextOutput(JSON.stringify({"result":"success","data": JSON.stringify(e.parameters) }))
          .setMimeType(ContentService.MimeType.JSON);
  } catch(error) { // if error return this
    Logger.log(error);
    return ContentService
          .createTextOutput(JSON.stringify({"result":"error", "message": "Sorry, there is an issue with the server."}))
          .setMimeType(ContentService.MimeType.JSON);
  }
}


/**
 * This method inserts the data received from the html form submission
 * into the sheet. e is the data received from the POST
 */
function record_data(e) {
  Logger.log(JSON.stringify(e)); // log the POST data in case we need to debug it
  try {
    var doc     = SpreadsheetApp.getActiveSpreadsheet();
    var sheet   = doc.getSheetByName('responses'); // select the responses sheet
    var headers = sheet.getRange(1, 1, 1, sheet.getLastColumn()).getValues()[0];
    var nextRow = sheet.getLastRow()+1; // get next row
    var row     = [ new Date().toUTCString() ]; // first element in the row should always be a timestamp
    // loop through the header columns
    for (var i = 1; i < headers.length; i++) { // start at 1 to avoid Timestamp column
      if(headers[i].length > 0) {
        row.push(e.parameter[headers[i]]); // add data to row
      }
    }
    // more efficient to set values as [][] array than individually
    sheet.getRange(nextRow, 1, 1, row.length).setValues([row]);
  }
  catch(error) {
    Logger.log(error);
    Logger.log(e);
    throw error;
  }
  finally {
    return;
  }
}


/**
 * This method is just to prettify the email.
 */
function formatMailBody(obj) { // function to spit out all the keys/values from the form in HTML
  var result = "";
  for (var key in obj) { // loop over the object passed to the function
    result += "<h4 style='text-transform: capitalize; margin-bottom: 0'>" + key + "</h4><div>" + obj[key] + "</div>";
    // for every key, concatenate an `<h4 />`/`<div />` pairing of the key name and its value, 
    // and append it to the `result` string created at the start.
  }
  return result; // once the looping is done, `result` will be one long string to put in the email body
}
```

