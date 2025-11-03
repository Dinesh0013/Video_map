Script to add lng and lat in the sheet:

/**
 * Automatically geocodes new Google Form responses.
 * Adds latitude and longitude for each address submitted.
 */

function onFormSubmit(e) {
  const sheet = e.range.getSheet();
  const row = e.range.getRow();

  // Make sure we're working on the right sheet
  if (sheet.getName() !== "Form Responses 1") return;

  const headers = sheet.getDataRange().getValues()[0];

  const addressIndex = headers.indexOf("Address");
  const latIndex = headers.indexOf("lat");
  const lngIndex = headers.indexOf("lng");
  const statusIndex = headers.indexOf("geocode_status");

  if (addressIndex === -1 || latIndex === -1 || lngIndex === -1) {
    Logger.log("Required columns missing: Address, lat, lng, geocode_status");
    return;
  }

  const address = sheet.getRange(row, addressIndex + 1).getValue();
  if (!address) return;

  const apiKey = "AIzaSyBaKGAaZgEGYbQcIJidMnc9IJzLfM-TySY";
  const url = `https://maps.googleapis.com/maps/api/geocode/json?address=${encodeURIComponent(address)}&key=${apiKey}`;

  try {
    const response = UrlFetchApp.fetch(url);
    const data = JSON.parse(response.getContentText());

    if (data.status === "OK") {
      const location = data.results[0].geometry.location;
      sheet.getRange(row, latIndex + 1).setValue(location.lat);
      sheet.getRange(row, lngIndex + 1).setValue(location.lng);
      sheet.getRange(row, statusIndex + 1).setValue("OK");
      Logger.log(`Geocoded: ${address} → (${location.lat}, ${location.lng})`);
    } else {
      sheet.getRange(row, statusIndex + 1).setValue(data.status);
      Logger.log(`Failed: ${address} → ${data.status}`);
    }
  } catch (err) {
    Logger.log("Error during geocoding: " + err);
    sheet.getRange(row, statusIndex + 1).setValue("ERROR");
  }
}






turning a Google Sheet into a mini API that outputs your sheet as JSON

function doGet() {
  const sheet = SpreadsheetApp.getActiveSpreadsheet().getSheetByName("Form Responses 1");
  const data = sheet.getDataRange().getValues();
  const headers = data[0];
  const json = [];

  for (let i = 1; i < data.length; i++) {
    const row = {};
    for (let j = 0; j < headers.length; j++) {
      row[headers[j]] = data[i][j];
    }
    json.push(row);
  }

  return ContentService
    .createTextOutput(JSON.stringify(json))
    .setMimeType(ContentService.MimeType.JSON);
}





