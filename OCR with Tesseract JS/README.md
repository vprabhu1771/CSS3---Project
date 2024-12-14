```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>OCR with Tesseract.js</title>
  <script src="https://cdn.jsdelivr.net/npm/tesseract.js@4.0.3/dist/tesseract.min.js"></script>
  <style>
    body {
      font-family: Arial, sans-serif;
      padding: 20px;
      line-height: 1.6;
    }
    .container {
      max-width: 600px;
      margin: 0 auto;
      text-align: center;
    }
    #imagePreview {
      max-width: 100%;
      margin-top: 20px;
    }
    #result {
      margin-top: 20px;
      background-color: #f4f4f4;
      padding: 10px;
      border-radius: 5px;
      word-wrap: break-word;
    }
  </style>
</head>
<body>
  <div class="container">
    <h1>Image to Text (OCR)</h1>
    <input type="file" id="imageInput" accept="image/*">
    <img id="imagePreview" alt="Image Preview" />
    <div id="status"></div>
    <div id="result"></div>
  </div>

  <script>
    document.getElementById('imageInput').addEventListener('change', async (event) => {
      const file = event.target.files[0];
      const imagePreview = document.getElementById('imagePreview');
      const resultDiv = document.getElementById('result');
      const statusDiv = document.getElementById('status');

      if (file) {
        // Preview the image
        const reader = new FileReader();
        reader.onload = () => {
          imagePreview.src = reader.result;
        };
        reader.readAsDataURL(file);

        // Show status
        statusDiv.textContent = 'Processing... Please wait.';

        // Perform OCR using Tesseract.js
        try {
          const { data: { text } } = await Tesseract.recognize(file, 'eng', {
            logger: (info) => {
              console.log(info);
              statusDiv.textContent = info.status + ' (' + Math.round(info.progress * 100) + '%)';
            },
          });
          statusDiv.textContent = 'Processing completed!';
          resultDiv.textContent = text;
        } catch (error) {
          console.error(error);
          statusDiv.textContent = 'An error occurred during processing.';
          resultDiv.textContent = '';
        }
      }
    });
  </script>
</body>
</html>
```