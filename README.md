<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>내 얼굴 뷰어</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            background-color: #f4f4f4;
            text-align: center;
            margin: 0;
            padding: 0;
        }
        #container, #gallery-container, #date-gallery-container {
            max-width: 800px;
            margin: 20px auto;
            padding: 20px;
            background-color: #fff;
            border-radius: 8px;
            box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
        }
        #video, #captured-photo {
            max-width: 100%;
            border-radius: 5px;
        }
        #capture-btn, .gallery-button, #download-link, .back-button {
            background-color: #007bff;
            color: #ffffff;
            border: none;
            padding: 10px 20px;
            margin-top: 10px;
            font-size: 16px;
            border-radius: 5px;
            text-decoration: none;
            display: inline-block;
            cursor: pointer;
        }
        #gallery, #date-gallery {
            display: flex;
            flex-wrap: wrap;
            justify-content: center;
            margin-top: 20px;
        }
        #gallery img, #date-gallery img {
            margin: 5px;
            width: 150px;
            height: auto;
            cursor: pointer;
        }
        .hidden {
            display: none;
        }
    </style>
</head>
<body>
    <div id="container">
        <h1>내 얼굴 뷰어</h1>
        <video id="video" autoplay playsinline></video>
        <button id="capture-btn">사진 찍기</button>
        <button class="gallery-button" onclick="showGallery()">나의 갤러리</button>
    </div>
    <div id="gallery-container" class="hidden">
        <button class="back-button" onclick="hideGallery()">뒤로 가기</button>
        <h2>대표사진</h2>
        <div id="gallery"></div>
    </div>
    <div id="date-gallery-container" class="hidden">
        <button class="back-button" onclick="showGallery()">뒤로 가기</button>
        <div id="date-gallery"></div>
    </div>

    <script>
        const photos = JSON.parse(localStorage.getItem('photos')) || {};

        function setupCamera() {
            if (navigator.mediaDevices && navigator.mediaDevices.getUserMedia) {
                navigator.mediaDevices.getUserMedia({ video: true }).then(stream => {
                    const video = document.getElementById('video');
                    video.srcObject = stream;
                }).catch(err => {
                    console.error("카메라 접근에 실패했습니다:", err);
                });
            }
        }

        document.getElementById('capture-btn').addEventListener('click', () => {
            const video = document.getElementById('video');
            const canvas = document.createElement('canvas');
            canvas.width = video.videoWidth;
            canvas.height = video.videoHeight;
            const ctx = canvas.getContext('2d');
            ctx.drawImage(video, 0, 0, canvas.width, canvas.height);
            const dataURL = canvas.toDataURL('image/png');

            savePhoto(dataURL);
        });

        function savePhoto(dataURL) {
            const today = new Date().toISOString().slice(0, 10);
            if (!photos[today]) {
                photos[today] = [];
            }
            photos[today].push(dataURL);
            localStorage.setItem('photos', JSON.stringify(photos));
        }

        function showGallery() {
            document.getElementById('container').classList.add('hidden');
            document.getElementById('gallery-container').classList.remove('hidden');
            document.getElementById('date-gallery-container').classList.add('hidden');

            const gallery = document.getElementById('gallery');
            gallery.innerHTML = ''; // Reset gallery

            Object.keys(photos).forEach(date => {
                const img = document.createElement('img');
                img.src = photos[date][photos[date].length - 1]; // Get the last photo of each day
                img.onclick = () => showDateGallery(date);
                gallery.appendChild(img);
            });
        }

        function showDateGallery(date) {
            document.getElementById('gallery-container').classList.add('hidden');
            document.getElementById('date-gallery-container').classList.remove('hidden');

            const dateGallery = document.getElementById('date-gallery');
            dateGallery.innerHTML = '<h2>' + date + '</h2>'; // Reset and set date title

            photos[date].forEach(photo => {
                const img = document.createElement('img');
                img.src = photo;
                dateGallery.appendChild(img);
            });
        }

        function hideGallery() {
            document.getElementById('container').classList.remove('hidden');
            document.getElementById('gallery-container').classList.add('hidden');
            document.getElementById('date-gallery-container').classList.add('hidden');
        }

        window.onload = setupCamera;
    </script>
</body>
</html>
