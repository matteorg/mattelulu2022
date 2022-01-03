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
9. Start and run the website __completely free__. No hosting, backend server, or database required as you can use
   [GitHub Pages](https://pages.github.com/) to host and Google sheets (with the help of Google scripts) to store response
   data.
10. Add __custom domain__ and increase the overall cost of your website to around 12$ per year.

# Getting Started
1. `$ cd wedding-website` - go inside the project directory
2. `$ npm install` - install dependencies _(optional)_
3. `$ gulp` - compile sass to css, minify js, etc. _(optional)_
4. That's it, open `index.html` on your browser by just double-clicking on the file.

# Send email and upload response to Google Sheet

If you want the responses to be stored in an Excel-like file, you can simply use __Google Sheets__.

First, start a new tab and open [Google Sheets](https://docs.google.com/spreadsheets/u/0/?tgif=d). The start a new blank sheet. Maybe you want to give a name to the sheet, otherwise it will remain __Untitled spreadsheet__.

When you're on the new sheet, you can go to __Extensions__--->__App Scripts__

![Screen Shot 2022-01-03 at 10 24 22](https://user-images.githubusercontent.com/41672045/147915709-1009fcce-887c-4495-8cbd-8ea15866b8cf.png)

Once the new page is open, the __Code.gs__ window should be automatically open. There, you can replace the existing code with the following, where you have to type at the beginning your email address, so that you receive an email alert when someone fills the form. You should also edit the sheet name if you rename yours from __Sheet 1__ to whatever you like. Here you can also define the subject of the email (in whatever language suits you best).

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
    var sheet   = doc.getSheetByName('Sheet1'); // select the responses sheet, change name if you change sheet name
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

After copying and pasting this code, and editing whatever is necessary for you, you should save it by clicking on the usual __save__ icon just above the code window. The next step is to set up the __trigger__. So just go the column menu to the left, click on __triggers__, then select __Add Trigger__ from the bottom right corner of the page.

![Screen Shot 2022-01-03 at 10 44 07](https://user-images.githubusercontent.com/41672045/147917678-a367525f-5b5e-49b4-a305-18eff5470b59.png)

Once the new modal tab is open, you can leave everything as it is, except __Select event type__. From the scroll down menu, select __On form submit__ and then __Save__.

![Screen Shot 2022-01-03 at 10 54 06](https://user-images.githubusercontent.com/41672045/147917955-99cca4b7-2c9c-42f8-8899-959c381e9881.png)

Once you have completed these steps, you are ready to __deploy__. Let's go back to the __editor__ and click __Deploy__ ---> __New Deployment__.

![Screen Shot 2022-01-03 at 10 57 14](https://user-images.githubusercontent.com/41672045/147918190-344ceb70-3084-456a-8201-405c36a44b03.png)

In the new pop-up window you need to specify the type of deployment. Click on the icon and select __Web App__.

![Screen Shot 2022-01-03 at 11 00 22](https://user-images.githubusercontent.com/41672045/147918353-fdc915a7-d915-470c-a6d2-f308198bebb4.png)

When asked to add a description, just write whatever you like. If you are connected to your Google Account, the __execute as__ should already display your email address. Finally you can choose the level of access according to your taste.

![Screen Shot 2022-01-03 at 11 02 52](https://user-images.githubusercontent.com/41672045/147918554-d8a64c29-c845-406c-aa93-4fea0cdfcc03.png)

You can finally click on __Deploy__. You may be asked to authorize access, so just click on __authorize access__ and sign in with your account, then deployment will proceed. You finally obtained the __web app link__ that you'll need to obtain a custumized email and fill a google sheet.

Your next step is to copy this link and go to __js__ scripts folder on your project and in your __script.js__ file paste the link in the following block:

```JavaScript
/********************** RSVP **********************/
 $('#rsvp-form').on('submit', function (e) {
     e.preventDefault();
     var data = $(this).serialize();

     $('#alert-wrapper').html(alert_markup('info', '<strong>Un momento!</strong> Stiamo salvando i tuoi dettagli.'));

     if "No"===$("#menu-rsvp").val() {
         $.post('https://script.google.com/macros/s/your_link_part/exec', data)
             .done(function (data) {
                 console.log(data);
                 if (data.result === "error") {
                     $('#alert-wrapper').html(alert_markup('danger', data.message));
                 } else {
                     $('#alert-wrapper').html('');
                     $('#rsvp-nocome').modal('show');
                 }
             })
     } else {
         $.post('https://script.google.com/macros/s/your_link_part/exec', data)
             .done(function (data) {
                 console.log(data);
                 if (data.result === "error") {
                     $('#alert-wrapper').html(alert_markup('danger', data.message));
                 } else {
                     $('#alert-wrapper').html('');
                     $('#rsvp-modal').modal('show');
                 }
             })
             .fail(function (data) {
                 console.log(data);
                 $('#alert-wrapper').html(alert_markup('danger', "<strong>Mi spiace!</strong> C'è un problema col server. "));
             });
     }
 });
```

Now, whenever someone submits the form, the Google Sheet will be filled with the info you want, and you'll get a nice, customized email :)

## Important remark on column names

The set up above is 95% of what you need. What you need to be careful with is also column names setup. When writing the form (here is the code from the file __ita.html__) make sure that the __name__ tag has exactly the same name of the relative column in the google spreadsheet.
In my case, the column names are __timestamp, Ci sarà?, e-mail, nome, partecipanti, opzione__, and as you can see, they exaclty match the names in the code below.

```JavaScript
<form id="rsvp-form" class="rsvp-form"
       action=""
       method="POST">
     <div class="row">
         <div class="col-md-12 col-sm-12">
             <div class="form-input-group">
                 <i class="fa fa-angellist"></i><select id="menu-rsvp" name="Ci sarà?" onChange="checkOption(this)" required>
                     <option disabled selected value> Ci sarai? </option>
                     <option value="Si">Certo che vengo!</option>
                     <option value="No"> &#128546; Purtroppo non riesco</option>
                 </select>
             </div>
         </div>
     </div>
     <div class="row">
         <div class="col-md-6 col-sm-6">
             <div class="form-input-group">
                 <i class="fa fa-envelope"></i><input type="email" id="emailpart" name="e-mail" class=""
                                                      placeholder="La tua email"
                                                      required disabled>
             </div>
         </div>
         <div class="col-md-6 col-sm-6">
             <div class="form-input-group">
                 <i class="fa fa-user"></i><input name="nome" class=""
                                                  placeholder="Il tuo nome"
                                                  required>
             </div>
         </div>
     </div>
     <div class="row">
         <div class="col-md-6 col-sm-6">
             <div class="form-input-group">
                 <i class="fa fa-users"></i><input type="number" id="quantepers" name="partecipanti" class="" min="0"
                                                   max="4"
                                                   placeholder="Quanti siete?"
                                                   required disabled>
             </div>
         </div>
         <div class="col-md-6 col-sm-6">
             <div class="form-input-group">
                 <i class="fa fa-laptop"></i><select id="qualeopz" name="opzione" size="0" disabled>
                     <option disabled selected value> Qual è il tuo piano? </option>
                     <option value="self-planning"> Mi organizzo da solo </option>
                     <option value="proposta accettata"> &#128077; 2 notti in Hotel a Santa Cesarea</option>
                 </select>
             </div>
         </div>
     </div>
     <div class="row">
         <div class="col-md-12" id="alert-wrapper"></div>
     </div>
     <button class="btn-fill rsvp-btn">
         Rispondi
     </button>
 </form>
```

# Add to calendar

There are only few changes that you have to implement for the __add to calendar__ feature. First, in your json script, just adjust the following block with the information specific to __your__ wedding:

```JavaScript
/********************** Add to Calendar **********************/
 var myCalendar = createCalendar({
     options: {
         class: '',
         // You can pass an ID. If you don't, one will be generated for you
         id: ''
     },
     data: {
         // Event title
         title: "Matrimonio Matte e Lulu",

         // Event start date
         start: new Date('Lug 3, 2022 17:00'),

         // Event duration (IN MINUTES)
         // duration: 120,

         // You can also choose to set an end time
         // If an end time is set, this will take precedence over duration
         end: new Date('Lug 4, 2022 03:00'),

         // Event Address
         address: 'Cala dei Balcani, Santa Cesarea Terme',

         // Event Description
         description: "Non vediamo l'ora di avervi al nostro grande giorno!. Se avete domande, non esitate a contattarci!"
     }
 });

 $('#add-to-cal').html(myCalendar);
```

Next you should just custumize the following piece of code in the calendar json file in the __json__--->__vendor__ folder:

```JavaScript
var generateMarkup = function(calendars, clazz, calendarId) {
     var result = document.createElement('div');

     result.innerHTML = '<label id="add-to-calendar-label" for="checkbox-for-' +
         calendarId + '" class="btn btn-fill btn-small"><i class="fa fa-calendar"></i>&nbsp;&nbsp; Aggiungi al calendario</label>';
     result.innerHTML += '<input name="add-to-calendar-checkbox" class="add-to-calendar-checkbox" id="checkbox-for-' + calendarId + '" type="checkbox">';

     Object.keys(calendars).forEach(function(services) {
         result.innerHTML += calendars[services];
     });

     result.className = 'add-to-calendar';
     if (clazz !== undefined) {
         result.className += (' ' + clazz);
     }

     addCSS();

     result.id = calendarId;
     return result;
 };
```

The rest of the code was developed by the amazing [rampatra](https://github.com/rampatra), so you can always refer to his github page for more details.

# Google Maps

In order to display a nice map from Google Maps you need an API. Start by going to __Google Cloud__ by clicking [here](https://cloud.google.com/gcp), then click on __go to console__.

Once you are redirected to the __Google Cloud Platform__ you'll first be asked to agree to the terms. Just do it and you'll be in the home page.

Apparently, once upon a time creating Google Maps APIs was free. Now you should activate a billing account. __NO WORRIES__: you'll get your API for free. I just neet to tell you the whole story.

Once you activate a Google Cloud billing account, you get a credit of 300$ for 90 days. After this trial period, you get charged for the usage that exceeds the monthly limits which you can find [here](https://cloud.google.com/free/docs/gcp-free-tier?authuser=1#free-tier-usage-limits). __Trust me__, if you use the API for something as small as a project like this, you'll __never__ exceed the usage limit. __Furthermore__, Google Maps Platform (which is what we're using) features a recurring 200$ monthly credit. You can read by yourself the [Pricing for Maps, Routes, and Places](https://mapsplatform.google.com/pricing/?authuser=1).

So, if you are now convinced, you can click on the top-right corner of the page on __activate__.

![Screen Shot 2022-01-03 at 11 45 46](https://user-images.githubusercontent.com/41672045/147922084-943b2702-6d77-4044-8ece-761ee11d8d22.png)

Follow the required steps (including giving credit card details) and finally click on __start free trial__.

If everything went successfully, the administration will display a message with the title __Enable Google Maps Platform__ to activate the API key. Click the __Next__ button. Generating process — you will find your new API key in the grey box. Click __Done__ to finish the process.

The key is generated, but you now need to specify some settings before you can start using Maps on your website.

### Settings

You need to set some security settings to begin with. So go to the left column menu, select __APIs and services__ and then click on __credentials__.

![Screen Shot 2022-01-03 at 11 56 14](https://user-images.githubusercontent.com/41672045/147923059-67e2f174-539d-4651-9b6a-ca43c9ad154d.png)

Once you are there, select the only API which should appear there, and select the pencil icon to edit the security settings of the API.

![Screen Shot 2022-01-03 at 11 55 03](https://user-images.githubusercontent.com/41672045/147923287-81ebb06a-5b72-495c-9e07-60a7b8634d3c.png)

When you are finally there, scroll down to the __Application restrictions__ section and select __HTTP referrers__. Just below, in the __Website restrictions__ just add all the items that you wish. In my case I decided to restrict access only to the wedding web pages in Italian and German. You can learn more on how to restrict websites by reading the column to the right (it's also in the picture below).

![Screen Shot 2022-01-03 at 11 59 09](https://user-images.githubusercontent.com/41672045/147923523-5631d19d-bc79-46d3-bcfa-fc7c6a33cb55.png)

Once these settings have been specified, just click on __save__. In the __credential__ page you can copy your API key (it's just next to the pencil icon you had to click before, once you edited the security settings) and store it, as you'll need in the next steps.

Once a month you'll get an invoice from Google amounting to plausibly 0$. You can easily check your API usage and your billing account from the home page of Google Cloud Platform. Now we can move back to the wedding website.

### Displaying maps on website

In the json scripts, you can just edit the following block with the coordinates of the place you'd like to be displayed. The coordinates are simple to recover. Once you select the place you want on Google Maps, the navigation bar will automatically include the coordinates. For example <https://www.google.com/maps/place/Cala+dei+Balcani/@40.0365908,18.4557733,17z/data=!3m1!4b1!4m5!3m4!1s0x13446b68dc0aae1f:0x712bcadc0421810a!8m2!3d40.0366714!4d18.4578939> this is the link to the location of our wedding, and as you can see, after the name theres __40.0365908,18.4557733__. These are exactly the latitude and longitude that you need :)

```JavaScript
function initMap() {
    var location = {lat: 40.0365867, lng: 18.457962};
    var map = new google.maps.Map(document.getElementById('map-canvas'), {
        zoom: 15,
        center: location,
        scrollwheel: false
    });

    var marker = new google.maps.Marker({
        position: location,
        map: map
    });
}
```

Finally, just go the html file where you want your map to be displayed, and at the end of the page, where you have the scripts, just add the following and replace __yourAPIkey__ with the key that your generated and stored in the steps above:

```HTML
<script async defer
        src="https://maps.googleapis.com/maps/api/js?key=yourAPIkey&callback=initMap"></script>
```

There you go, your map will appear on your website :)

# Password protected page

This is an easy step, in case you are interesting in password protecting your homepage. You can just go to [this page](https://robinmoisson.github.io/staticrypt/) and paste the whole html code of your website in the html box, and set the password that you prefer. Finally generated your encrypted and password protected html and reload it to github with the correct name. The webpage will look like this:

![Screen Shot 2022-01-03 at 12 19 44](https://user-images.githubusercontent.com/41672045/147924899-e8d929cb-48aa-4cf4-b031-332abc220075.png)

You can customize the html file to display the message that you prefer (like I did with the double option Italian/German).

# Custom domain

To publish your website you can simply use [GitHub Pages](https://pages.github.com/). Once everything you want to publish is ready, you can go to __settings__ and in the left column menu __pages__. There you can either decide to publish the website __for free__, with the github domain.

If you are instead willing to spend around 1 dollar/euro/chf per month to have a nicer domain name, you can just buy one. Buying a domain name is incredibly easy and quite quick (it may take up to 48 hours; in my case I had to wait aroun 15 hours after the purchase). Just google __buy domain__ and you'll find a ton of possible websites where you can purchase custom domains for very cheap fees. Google itself gives the possibility to buy custom domains (unfortunately not in Switzerland) and that would probably be the easiest option.

Once you get confirmation that your domain is active, there is one thing you still need to do. Whatever your host is (e.g. Google, Hoststar, etc.) in your personal account there must be somewhere the possibility to edit the DNS properties. You should go to that page and in the Type A you should add any (or all) of the following GitHub pages addresses: __185.199.108.153__, __185.199.109.153__, __185.199.110.153__, __185.199.111.153__. Once you have done this, you can go back to your __settings/pages__ page on GitHub, add your custom domain and finally click __save__. Your website will be finally published under your customized domain name :)

# Credits

Of course huge credits to [rampatra](https://github.com/rampatra) for the big work which served as base for my website.

This [article](https://medium.com/superkoders/ultimate-guide-to-google-maps-d86ad945636a) was very helpful to understand how to set up the Maps API.

Feel free to contact me if you have any questions!


