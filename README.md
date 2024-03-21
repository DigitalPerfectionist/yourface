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
            margin: 10px;
            padding: 10px 20px;
            font-size: 16px;
        }
        #gallery-container, #date-gallery-container {
            display: none;
            flex-direction: column;
            align-items: center;
            width: 100%;
            max-width: 640px;
        }
        #gallery, #date-gallery {
            display: flex;
            flex-wrap: wrap;
            justify-content: center;
            margin-top: 20px;
        }
        #gallery img, #date-gallery img {
            width: 30%;
            max-width: 180px;
            margin: 5px;
            cursor: pointer;
            border: 1px solid #ccc;
            box-shadow: 0 0 5px #ccc;
        }
    </style>
</head>
<body>

<video id="video" autoplay playsinline></video>
<button id="capture-btn" class="button">사진 찍기</button>
<button id="show-gallery-btn" class="button">나의 갤러리</button>

<div id="gallery-container">
    <h2>대표사진</h2>
    <div id="gallery"></div>
    <button id="back-to-camera" class="button">뒤로가기</button>
</div>

<div id="date-gallery-container">
    <h2 id="date-gallery-title"></h2>
    <div id="date-gallery"></div>
    <button id="back-to-representative" class="button">뒤로가기</button>
</div>

<script>
    const video = document.getElementById('video');
    let photos = JSON.parse(localStorage.getItem('photos')) || {};

    // 비디오 스트림 설정
    navigator.mediaDevices.getUserMedia({ video: true })
        .then(stream => video.srcObject = stream)
        .catch(err => console.error("Error accessing camera:", err));

    // 사진 찍기
    document.getElementById('capture-btn').addEventListener('click', () => {
        const canvas = document.createElement('canvas');
        canvas.width = video.videoWidth;
        canvas.height = video.videoHeight;
        const context = canvas.getContext('2d');
        context.drawImage(video, 0, 0, canvas.width, canvas.height);

        const dataURL = canvas.toDataURL('image/png');
        const today = new Date().toISOString().slice(0, 10);

        if (!photos[today]) {
            photos[today] = [];
        }
        photos[today].push(dataURL);
        localStorage.setItem('photos', JSON.stringify(photos));
    });

    // 대표사진 보기
    document.getElementById('show-gallery-btn').addEventListener('click', () => {
        displayGallery();
    });

    // 대표사진에서 날짜별 갤러리로 이동
    function displayGallery() {
        document.getElementById('gallery-container').style.display = 'flex';
        document.getElementById('video').style.display = 'none';
        document.getElementById('capture-btn').style.display = 'none';
        document.getElementById('show-gallery-btn').style.display = 'none';

        const gallery = document.getElementById('gallery');
        gallery.innerHTML = '';

        Object.keys(photos).forEach(date => {
            const img = document.createElement('img');
            img.src = photos[date][photos[date].length - 1]; // 가장 마지막 사진
            img.addEventListener('click', () => displayDateGallery(date));
            gallery.appendChild(img);
        });
    }

    // 날짜별 갤러리 보기
    function displayDateGallery(date) {
        document.getElementById('gallery-container').style.display = 'none';
        document.getElementById('date-gallery-container').style.display = 'flex';

        const title = document.getElementById('date-gallery-title');
        title.textContent = `날짜별 갤러리: ${date}`;

        const dateGallery = document.getElementById('date-gallery');
        dateGallery.innerHTML = '';

        photos[date].forEach(photo => {
            const img = document.createElement('img');
            img.src = photo;
            dateGallery.appendChild(img);
        });
    }

    // 뒤로가기 버튼
    document.getElementById('back-to-camera').addEventListener('click', () => {
        document.getElementById('gallery-container').style.display = 'none';
        document.getElementById('video').style.display = 'block';
        document.getElementById('capture-btn').style.display = 'inline-block';
        document.getElementById('show-gallery-btn').style.display = 'inline-block';
    });

    document.getElementById('back-to-representative').addEventListener('click', () => {
        document.getElementById('date-gallery-container').style.display = 'none';
        document.getElementById('gallery-container').style.display = 'flex';
    });
</script>

</body>
</html>
