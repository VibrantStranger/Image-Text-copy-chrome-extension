// content.js

// Load Tesseract.js
const Tesseract = window.Tesseract;

chrome.runtime.onMessage.addListener((message, sender, sendResponse) => {
  if (message.action === "extractText") {
    const imageUrl = window.getSelection().toString();
    const image = new Image();
    image.src = imageUrl;

    image.onload = () => {
      const canvas = document.createElement("canvas");
      canvas.width = image.width;
      canvas.height = image.height;
      const ctx = canvas.getContext("2d");
      ctx.drawImage(image, 0, 0, image.width, image.height);

      const imageData = ctx.getImageData(0, 0, image.width, image.height);
      
      // Use Tesseract.js to perform OCR on the image data
      Tesseract.recognize(imageData, 'eng', {
        logger: info => console.log(info)
      }).then(({ data: { text } }) => {
        const extractedText = text.trim();

        const textArea = document.createElement("textarea");
        textArea.value = extractedText;
        document.body.appendChild(textArea);
        textArea.select();
        document.execCommand("copy");
        document.body.removeChild(textArea);
      });
    };
  }
});
