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
        #container {
            max-width: 800px;
            margin: auto;
            padding: 20px;
            background-color: #fff;
            border-radius: 8px;
            box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
        }
        #video, #captured-photo {
            max-width: 100%;
            border-radius: 5px;
        }
        #capture-btn, .gallery-button, #download-link {
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
        #gallery {
            display: flex;
            flex-wrap: wrap;
            justify-content: center;
            margin-top: 20px;
        }
        #gallery img {
            margin: 5px;
            width: 150px;
            height: auto;
            cursor: pointer;
            border-radius: 5px;
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
        <img id="captured-photo" class="hidden">
        <a id="download-link" class="hidden">사진 다운로드</a>
        <button class="gallery-button" onclick="openGallery()">나의 갤러리</button>
        <div id="gallery" class="hidden"></div>
    </div>

    <script>
        let photoDataUrl; // 촬영된 사진의 데이터 URL을 저장합니다.

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
            photoDataUrl = canvas.toDataURL('image/png');

            const capturedPhoto = document.getElementById('captured-photo');
            capturedPhoto.src = photoDataUrl;
            capturedPhoto.classList.remove('hidden');

            const downloadLink = document.getElementById('download-link');
            downloadLink.href = photoDataUrl;
            downloadLink.download = 'captured_photo.png';
            downloadLink.textContent = "사진 다운로드";
            downloadLink.classList.remove('hidden');

            // 사진을 로컬 스토리지에 저장합니다.
            savePhotoToLocal(photoDataUrl);
        });

        function savePhotoToLocal(dataUrl) {
            const now = new Date();
            const photoKey = `photo-${now.getTime()}`;
            localStorage.setItem(photoKey, dataUrl);

            // 나중에 찾기 쉽도록 날짜별 인덱스에 사진 키를 추가합니다.
            const dateKey = now.toISOString().split('T')[0];
            let datePhotos = JSON.parse(localStorage.getItem(dateKey)) || [];
            datePhotos.push(photoKey);
            localStorage.setItem(dateKey, JSON.stringify(datePhotos));
        }

        function openGallery() {
            const gallery = document.getElementById('gallery');
            gallery.innerHTML = ''; // 갤러리 초기화
            gallery.classList.remove('hidden');

            Object.keys(localStorage).forEach(key => {
                if (key.startsWith('photo-')) {
                    const imgSrc = localStorage.getItem(key);
                    const img = document.createElement('img');
                    img.src = imgSrc;
                    img.onclick = () => {
                        window.open(img.src, '_blank');
                    };
                    gallery.appendChild(img);
                }
            });
        }

        window.onload = setupCamera;
    </script>
</body>
</html>
