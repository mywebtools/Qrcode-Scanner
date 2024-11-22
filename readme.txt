The core logic of Qrcode Scanner utilizes the jsQR.js library for decoding and reading QR codes, ensuring efficient and accurate extraction of QR code content.

Below is the core code logic for scanning via file upload and webcam.

live demo: [QRcode Scanner](https://qrcode-scanner.online)

1. Decoding QR Code from a Local Image
This implementation allows users to upload an image file and decode the QR code content from it.

```javascript
document.getElementById('fileInput').addEventListener('change', function (event) {
    const file = event.target.files[0];
    if (!file) return;

    const reader = new FileReader();
    reader.onload = function () {
        const img = new Image();
        img.onload = function () {
            const canvas = document.createElement('canvas');
            const ctx = canvas.getContext('2d');
            canvas.width = img.width;
            canvas.height = img.height;
            ctx.drawImage(img, 0, 0, img.width, img.height);

            const imageData = ctx.getImageData(0, 0, canvas.width, canvas.height);
            const qrCode = jsQR(imageData.data, imageData.width, imageData.height);

            if (qrCode) {
                console.log('QR Code Content:', qrCode.data);
            } else {
                console.log('No QR code detected.');
            }
        };
        img.src = reader.result;
    };
    reader.readAsDataURL(file);
});


2. Decoding QR Code from Webcam Feed
This implementation continuously reads frames from the webcam and decodes any QR code detected.

```javascript
const video = document.getElementById('video');
const canvas = document.getElementById('canvas');
const ctx = canvas.getContext('2d');

async function startWebcam() {
    try {
        const stream = await navigator.mediaDevices.getUserMedia({ video: { facingMode: 'environment' } });
        video.srcObject = stream;
        video.play();
        scanQRCode();
    } catch (error) {
        console.error('Error accessing webcam:', error);
    }
}

function scanQRCode() {
    canvas.width = video.videoWidth;
    canvas.height = video.videoHeight;

    const scan = () => {
        ctx.drawImage(video, 0, 0, canvas.width, canvas.height);
        const imageData = ctx.getImageData(0, 0, canvas.width, canvas.height);
        const qrCode = jsQR(imageData.data, imageData.width, imageData.height);

        if (qrCode) {
            console.log('QR Code Content:', qrCode.data);
        } else {
            console.log('Scanning...');
        }

        requestAnimationFrame(scan);
    };

    scan();
}

startWebcam();
