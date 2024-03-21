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
            flex-direction: column;
            align-items: center;
            font-family: Arial, sans-serif;
            background-color: #f4f4f4;
        }
        #video, #captured-photo {
            width: 480px;
            height: 360px;
            margin-top: 20px;
        }
        .button {
            margin-top: 10px;
            padding: 10px 20px;
            font-size: 16px;
            cursor: pointer;
        }
        .hidden {
            display: none;
        }
        #gallery-container, #date-gallery-container {
            display: none;
            margin-top: 20px;
        }
        #gallery img, #date-gallery img {
            width: 100px;
            margin: 5px;
            cursor: pointer;
            border: 1px solid #ccc;
        }
        #ad-container {
            margin-top: 20px;
        }
    </style>
</head>
<body>

<video id="video" autoplay playsinline></video>
<button id="capture-btn" class="button">사진 찍기</button>
<div id="photo-container" class="hidden">
    <img id="captured-photo" src="">
    <a id="download-link" download="captured_photo.png">사진 다운로드</a>
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
    <!-- 쿠팡 광고 문구 (광고 스크립트 적용 예시를 위한 텍스트로 대체) -->
    <p>쿠팡 파트너스 활동의 일환으로, 일정액의 수수료를 제공받을 수 있습니다.</p>
</div>

<script>
    let photos = JSON.parse(localStorage.getItem('photos')) || {};
    const videoElement = document.getElementById('video');
    const canvas = document.createElement('canvas');
    const photoContainer = document.getElementById('photo-container');
    const capturedPhotoElement = document.getElementById('captured-photo');
    const downloadLink = document.getElementById('download-link');

    navigator.mediaDevices.getUserMedia({ video: true })
        .then(stream => videoElement.srcObject = stream)
        .catch(err => console.error("카메라 접근 에러:", err));

    document.getElementById('capture-btn').addEventListener('click', () => {
        canvas.width = videoElement.videoWidth;
        canvas.height = videoElement.videoHeight;
        canvas.getContext('2d').drawImage(videoElement, 0, 0, canvas.width, canvas.height);
        const capturedPhoto = canvas.toDataURL('image/png');
        capturedPhotoElement.src = capturedPhoto;
        downloadLink.href = capturedPhoto;
        photoContainer.classList.remove('hidden');

        const today = new Date().toISOString().split('T')[0];
        if (!photos[today]) photos[today] = [];
        photos[today].push(capturedPhoto);
        localStorage.setItem('photos', JSON.stringify(photos));
    });

    document.getElementById('show-gallery-btn').addEventListener('click', () => {
        const galleryContainer = document.getElementById('gallery-container');
        const gallery = document.getElementById('gallery');
        gallery.innerHTML = '';
        Object.keys(photos).forEach(date => {
            const img = document.createElement('img');
            img.src = photos[date][photos[date].length - 1]; // Get the last photo of each day as representative
            img.addEventListener('click', () => {
                const dateGalleryContainer = document.getElementById('date-gallery-container');
                const dateGallery = document.getElementById('date-gallery');
                const dateGalleryTitle = document.getElementById('date-gallery-title');
                dateGalleryTitle.textContent = date;
                dateGallery.innerHTML = '';
                photos[date].forEach(photo => {
                    const dateImg = document.createElement('img');
                    dateImg.src = photo;
                    dateGallery.appendChild(dateImg);
                });
                galleryContainer.classList.add('hidden');
                dateGalleryContainer.classList.remove('hidden');
            });
            gallery.appendChild(img);
        });
        galleryContainer.classList.remove('hidden');
    });
</script>

</body>
</html>
