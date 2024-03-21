<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>내 얼굴 뷰어</title>
    <style>
        body, html {
            margin: 0;
            padding: 0;
            display: flex;
            justify-content: center;
            align-items: center;
            flex-direction: column;
            font-family: Arial, sans-serif;
            background-color: #f4f4f4;
        }
        #video {
            width: 480px;
            height: 360px;
            background-color: #000;
        }
        .button {
            margin-top: 10px;
            padding: 10px 20px;
            font-size: 16px;
            cursor: pointer;
        }
        #captured-photo {
            display: none;
            margin-top: 10px;
        }
        .hidden {
            display: none;
        }
        .photo-container, #gallery-container, #date-gallery-container {
            display: none;
            flex-direction: column;
            align-items: center;
            width: 100%;
            max-width: 640px;
            margin-top: 20px;
        }
        .photo-container img, #gallery img, #date-gallery img {
            width: 30%;
            max-width: 180px;
            margin: 5px;
            cursor: pointer;
            border: 1px solid #ccc;
            box-shadow: 0 0 5px #ccc;
        }
        #ad-container {
            margin-top: 20px;
        }
    </style>
</head>
<body>

<video id="video" autoplay playsinline></video>
<button id="capture-btn" class="button">사진 찍기</button>
<div class="photo-container hidden">
    <img id="captured-photo" src="">
    <a id="download-link" class="button" download="captured_photo.png">사진 다운로드</a>
</div>
<button id="show-gallery-btn" class="button">나의 갤러리</button>

<div id="gallery-container" class="hidden">
    <h2>대표사진</h2>
    <div id="gallery"></div>
</div>

<div id="date-gallery-container" class="hidden">
    <h2 id="date-gallery-title"></h2>
    <div id="date-gallery"></div>
</div>

<div id="ad-container">
    <!-- 쿠팡 광고 베너 및 문구 -->
    <p>이 포스팅은 쿠팡 파트너스 활동의 일환으로, 이에 따른 일정액의 수수료를 제공받습니다.</p>
</div>

<script>
    let photos = JSON.parse(localStorage.getItem('photos')) || {};

    document.addEventListener('DOMContentLoaded', function() {
        const video = document.getElementById('video');
        const canvas = document.createElement('canvas');
        const photoContainer = document.querySelector('.photo-container');
        const photo = document.getElementById('captured-photo');
        const downloadLink = document.getElementById('download-link');

        navigator.mediaDevices.getUserMedia({ video: true })
            .then(stream => video.srcObject = stream)
            .catch(err => alert("카메라 접근 권한이 필요합니다."));

        document.getElementById('capture-btn').addEventListener('click', function() {
            canvas.width = video.videoWidth;
            canvas.height = video.videoHeight;
            canvas.getContext('2d').drawImage(video, 0, 0, canvas.width, canvas.height);
            let imageSrc = canvas.toDataURL('image/png');
            photo.src = imageSrc;
            downloadLink.href = imageSrc;
            photoContainer.classList.remove('hidden');

            const today = new Date().toISOString().split('T')[0];
            if (!photos[today]) photos[today] = [];
            photos[today].push(imageSrc);
            localStorage.setItem('photos', JSON.stringify(photos));
        });

        document.getElementById('show-gallery-btn').addEventListener('click', showGallery);
    });

    function showGallery() {
        const galleryContainer = document.getElementById('gallery-container');
        const gallery = document.getElementById('gallery');
        gallery.innerHTML = ''; // Clear previous content
        Object.keys(photos).forEach(date => {
            const imgSrc = photos[date][photos[date].length - 1]; // Last photo of the day
            const img = document.createElement('img');
            img.src = imgSrc;
            img.addEventListener('click', () => showDateGallery(date));
            gallery.appendChild(img);
        });
        galleryContainer.classList.remove('hidden');
    }

    function showDateGallery(date) {
        const dateGalleryContainer = document.getElementById('date-gallery-container');
        const dateGallery = document.getElementById('date-gallery');
        const dateGalleryTitle = document.getElementById('date-gallery-title');
        dateGallery.innerHTML = ''; // Clear previous content
        dateGalleryTitle.textContent = `Gallery for ${date}`;
        photos[date].forEach(imgSrc => {
            const img = document.createElement('img');
            img.src = imgSrc;
            dateGallery.appendChild(img);
        });
        dateGalleryContainer.classList.remove('hidden');
    }
</script>

</body>
</html>
