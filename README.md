Use case:  User’s signature is very important to be captured as a business process. 

You may be thinking it will be a complex development. No, it’s not a complex development, if you are using SapphireIMS, I am going to show you how it can be integrated with 30min. Yes, you heard right less than 30min.

We are going to use open-source signature pad library in our integration – Please refer following URL for more detail “<https://github.com/szimek/signature_pad>”. You no need to download anything we are going to use CDN for the script. 

CDN URL is “<https://cdn.jsdelivr.net/npm/signature_pad@2.3.2/dist/signature_pad.min.js>”.

Let start integrating signature pad with SapphireIMS.

Navigate to SapphireIMS -> Service Desk -> Settings -> Select a Project -> fields & forms -> Customization Scripts

![](Aspose.Words.2cd4b8ba-272b-49b8-9230-7842ec7ac60e.001.png)

Click Add – To create a new custom script

Choose UI Type as Classic View Editor

Choose Field as All (onLoad)

Action as Load

Script Type as Text

That’s it, let us start some basic coding.

First we have to load the script from CDN, we will be using JQuery & java script in our scripting

Let us start writing the code.

|<p>// First we have to load the script from CDN, we will be using JQuery & java script in our scripting</p><p></p><p>jQuery.getScript( "https://cdn.jsdelivr.net/npm/signature\_pad@2.3.2/dist/signature\_pad.min.js", function( data, textStatus, jqxhr ) {</p><p>  </p><p>`  `// Append button two buttons with in SapphireIMS Edit Record page</p><p>`  `// 1. Signature Button to get the signature as image and uploading as attachment as part of the record</p><p>`  `// 2. Erase the signature if it is wrongly signed.</p><p>`  `// </p><p>`  `// We are going to use insertAfter - get the id after cancel button from the Edit Record Page, i.e, readOnly</p><p>`  `// </p><p></p><p>`  `jQuery("&nbsp;  &nbsp;<input id='signatureBTN' name='signatureBTN' type='button' value='Signature' class='addbuttons'>").insertAfter("#readOnly");</p><p>`  `jQuery("&nbsp;  &nbsp;<input id='erase' name='erase' type='button' value='Erase' class='addbuttons'>").insertAfter("#signatureBTN");</p><p></p><p></p><p>`  `// Add a canvas to the edit record after our last button erase which has been created. </p><p></p><p>`  `jQuery("<div><canvas id='signature-pad' class='signature-pad' width=900 height=200></canvas></div>").insertAfter("#erase");</p><p>  </p><p>`  `// Get the canvas element</p><p>`  `var canvas = document.getElementById('signature-pad');</p><p>  </p><p>`  `// Create signaturePad element and by using canvas</p><p>`  `var signaturePad = new SignaturePad(canvas, {</p><p>`    `backgroundColor: 'rgb(255, 255, 255)' // necessary for saving image as JPEG; can be removed is only saving as PNG or SVG</p><p>`  `});</p><p></p><p>`  `// Now we are ready with UI elements and it is rendering on our Edit Record page, time to write some actions</p><p></p><p>`  `// Register a click event to clear the signature pad</p><p></p><p>`  `document.getElementById('erase').addEventListener('click', function () {</p><p>`    `signaturePad.clear();</p><p>`  `});</p><p></p><p>`  `// Util method to get the csrf token from the current request for uploading </p><p>`  `function getMeta(metaName) {</p><p>`    `const metas = document.getElementsByTagName('meta');</p><p>`    `for (let i = 0; i < metas.length; i++) {</p><p>`        `if (metas[i].getAttribute('name') === metaName) {</p><p>`            `return metas[i].getAttribute('content');</p><p>`        `}</p><p>`    `}</p><p>`    `return '';</p><p>`  `}</p><p></p><p>`  `// Util method to convert the data uri from signature pad to Blob for upload as a binary file.</p><p>`  `function DataURIToBlob(dataURI) {</p><p>`    `const splitDataURI = dataURI.split(',');</p><p>`    `const byteString = splitDataURI[0].indexOf('base64') >= 0 ? atob(splitDataURI[1]) : decodeURI(splitDataURI[1]);</p><p>`    `const mimeString = splitDataURI[0].split(':')[1].split(';')[0];</p><p></p><p>`    `const ia = new Uint8Array(byteString.length);</p><p>`    `for (let i = 0; i < byteString.length; i++)</p><p>`        `ia[i] = byteString.charCodeAt(i);</p><p></p><p>`    `return new Blob([ia], { type: mimeString });</p><p>`  `}</p><p></p><p>`   `// Final event register for signature button to upload as part of the record</p><p></p><p>`  `document.getElementById('signatureBTN').addEventListener('click', function()</p><p>`  `{</p><p>`    `if (signaturePad.isEmpty()) {</p><p>`        `return alert("Please provide a signature first.");</p><p>`    `}</p><p>        </p><p>`    `var dataURL = signaturePad.toDataURL('image/jpeg');</p><p>`    `var fd = new FormData();</p><p>`    `fd.append('upload', DataURIToBlob(dataURL), 'Singature.jpg');</p><p>`    `fd.append('commentArea', "Signature uploaded");</p><p>         </p><p>`    `jQuery.ajax({</p><p>`                    `type: "POST",</p><p>`                    `data: fd,</p><p>`                    `processData: false,</p><p>`                    `contentType: false,</p><p>`                    `url: "Upload?\_csrf="+getMeta('\_csrf')+'&problemID='+document.getElementById("ticketid").value</p><p>`                `}).done(function(o) {</p><p>`                    `console.log('saved');</p><p>`                `});  </p><p>`    `});</p><p>});</p>|
| :- |

![](Aspose.Words.2cd4b8ba-272b-49b8-9230-7842ec7ac60e.002.png)

![](Aspose.Words.2cd4b8ba-272b-49b8-9230-7842ec7ac60e.003.png)


