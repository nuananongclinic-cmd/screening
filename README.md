/**
 * @OnlyCurrentDoc
 * This script creates a web app to record patient data into a Google Sheet.
 */

// --- CONFIGURATION ---
const SHEET_ID = "1mtRnqVdBl4MGcBZDU3ghx1K7UBjQmmGvHf0EgAdJdus";
const SHEET_NAME = "screening";

/**
 * Serves the HTML file for the web app's user interface.
 * This is the entry point when a user visits the web app URL.
 */
function doGet(e) {
  return HtmlService.createHtmlOutputFromFile('index')
    .setTitle('ระบบบันทึกข้อมูลผู้ป่วยคลินิกหมอนวลอนงค์')
    .setXFrameOptionsMode(HtmlService.XFrameOptionsMode.ALLOWALL);
}

/**
 * Saves the form data submitted from the web app to the designated Google Sheet.
 * This function is called from the client-side JavaScript using google.script.run.
 *
 * @param {object} formData - The data object from the HTML form.
 * @returns {string} A JSON string indicating success or failure.
 */
function saveDataToSheet(formData) {
  try {
    const spreadsheet = SpreadsheetApp.openById(SHEET_ID);
    let sheet = spreadsheet.getSheetByName(SHEET_NAME);

    // If the sheet doesn't exist, create it with headers.
    if (!sheet) {
      sheet = spreadsheet.insertSheet(SHEET_NAME);
      const headers = [
        "Timestamp", 
        "บัตรประชาชน", 
        "ชื่อ-สกุล", 
        "อายุ", 
        "วันเดือนปีเกิด", 
        "เบอร์โทร",
        "ความต้องการตรวจ"
      ];
      sheet.appendRow(headers);
    }
    
    // The order of data here MUST match the column order in the sheet.
    const newRow = [
      new Date(), // Timestamp for the record
      formData.idCard,
      formData.fullName,
      formData.age,
      formData.dob,
      formData.phone,
      formData.serviceRequest
    ];

    sheet.appendRow(newRow);
    
    return JSON.stringify({ status: 'success', message: 'บันทึกข้อมูลสำเร็จ' });

  } catch (error) {
    console.error("Error in saveDataToSheet: " + error.toString());
    // Return a structured error to the client-side
    throw new Error('เกิดข้อผิดพลาดในการบันทึกข้อมูล: ' + error.message);
  }
}
