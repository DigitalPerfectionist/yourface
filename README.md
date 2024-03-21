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
        #video, #captured-photo {
            width: 480px;
            height: 360px;
            background-color: #000;
        }
        .button {
            margin: 10px;
            padding: 10px 20px;
            font-size: 16px;
        }
        #gallery-container, #date-gallery-container, #ad-container {
            display: none;
            flex-direction: column;
            align-items: center;
            width: 100%;
            max-width: 640px;
            margin-top: 20px;
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
<canvas id="canvas" style="display:none;"></canvas> <!-- 숨겨진 캔버스 추가 -->

<div id="ad-container">
    <!-- 쿠팡 광고 베너 및 문구 유지 -->
    <p>이 포스팅은 쿠팡 파트너스 활동의 일환으로, 이에 따른 일정액의 수수료를 제공받습니다.</p>
</div>

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
    const canvas = document.getElementById('canvas');
    const context = canvas.getContext('2d');
    let photos = JSON.parse(localStorage.getItem('photos')) || {};

    navigator.mediaDevices.getUserMedia({ video: true })
        .then(stream => video.srcObject = stream)
        .catch(err => console.error("Error accessing camera:", err));

    document.getElementById('capture-btn').addEventListener('click', () => {
        canvas.width = video.videoWidth;
        canvas.height = video.videoHeight;
        context.drawImage(video, 0, 0, canvas.width, canvas.height);

        const dataURL = canvas.toDataURL('image/png');
        const today = new Date().toISOString().slice(0, 10);

        if (!photos[today]) photos[today] = [];
        photos[today].push(dataURL);
        localStorage.setItem('photos', JSON.stringify(photos));

        // 찍힌 사진 다운로드 버튼 생성
        const downloadLink = document.createElement('a');
        downloadLink.href = dataURL;
        downloadLink.download = 'captured_photo.png';
        downloadLink.textContent = '사진 다운로드';
        downloadLink.className = 'button';
        document.body.appendChild(downloadLink);
    });

    // 갤러리 보기 및 다른 기능들은 이전 코드와 동일하게 유지됩니다.

</script>

</body>
</html>
