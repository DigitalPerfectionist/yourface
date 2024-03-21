<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>My Face Viewer / 내 얼굴 뷰어</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            background-color: #f4f4f4;
            text-align: center;
            margin: 0;
            padding: 0;
        }
        #container {
            max-width: 600px;
            margin: 50px auto;
            padding: 20px;
            background-color: #fff;
            border-radius: 8px;
            box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
        }
        h1 {
            color: #333;
        }
        #video {
            width: 100%;
            height: auto;
            border-radius: 8px;
            margin-bottom: 20px;
        }
        #capture-btn {
            background-color: #007bff;
            color: #fff;
            border: none;
            padding: 10px 20px;
            font-size: 16px;
            border-radius: 4px;
            cursor: pointer;
        }
        #gallery {
            display: flex;
            flex-wrap: wrap;
            justify-content: center;
        }
        #gallery img {
            margin: 5px;
            width: 100px;
            height: auto;
        }
        .gallery-button {
            background-color: #007bff;
            color: #fff;
            border: none;
            padding: 10px 20px;
            font-size: 16px;
            border-radius: 4px;
            cursor: pointer;
            margin-top: 20px;
        }
    </style>
</head>
<body>
    <div id="container">
        <h1>My Face Viewer / 내 얼굴 뷰어</h1>
        <video id="video" autoplay playsinline></video>
        <button id="capture-btn">사진 찍기</button>
        <button class="gallery-button" onclick="openGallery()">나의 갤러리</button>
        <div id="gallery"></div>
    </div>

    <script>
        // 카메라 접근 권한 요청 및 비디오 스트림 설정
        function setupCamera() {
            if (navigator.mediaDevices && navigator.mediaDevices.getUserMedia) {
                navigator.mediaDevices.getUserMedia({ video: true }).then(function(stream) {
                    var video = document.getElementById('video');
                    video.srcObject = stream;
                    video.play();
                });
            }
        }

        // 사진 찍기 및 로컬 스토리지에 저장
        document.getElementById('capture-btn').addEventListener('click', function() {
            var canvas = document.createElement('canvas');
            var context = canvas.getContext('2d');
            var video = document.getElementById('video');
            canvas.width = video.videoWidth;
            canvas.height = video.videoHeight;
            context.drawImage(video, 0, 0, canvas.width, canvas.height);

            var dataURL = canvas.toDataURL('image/png');
            savePicture(dataURL); // 로컬 스토리지에 사진 저장
        });

        // 사진 로컬 스토리지에 저장
        function savePicture(dataURL) {
            var today = new Date().toISOString().slice(0, 10); // YYYY-MM-DD 형식
            if (!localStorage.getItem(today)) {
                localStorage.setItem(today, JSON.stringify([])); // 날짜별로 배열 생성
            }
            var todayPhotos = JSON.parse(localStorage.getItem(today));
            todayPhotos.push(dataURL);
            localStorage.setItem(today, JSON.stringify(todayPhotos));
            alert('사진이 저장되었습니다.');
        }

        // '나의 갤러리'를 눌렀을 때 갤러리 표시
        function openGallery() {
            var gallery = document.getElementById('gallery');
            gallery.innerHTML = ''; // 갤러리 초기화

            // 저장된 사진 불러오기
            for (var key in localStorage) {
                var photos = JSON.parse(localStorage.getItem(key));
                if (photos) {
                    photos.forEach(function(photo) {
                        var img = document.createElement('img');
                        img.src = photo;
                        gallery.appendChild(img);
                    });
                }
            }
        }

        // 페이지가 로드될 때 카메라 설정
        window.onload = function() {
            setupCamera();
        };
    </script>
</body>
</html>
# yourface
