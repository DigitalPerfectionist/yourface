<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>내 얼굴 뷰어</title>
    <style>
        /* 스타일은 문제 해결과 직접적인 관련이 없으므로 생략 가능 */
    </style>
</head>
<body>

<video id="video" autoplay playsinline></video>
<button id="capture-btn">사진 찍기</button>

<script>
    // 카메라 설정 및 사진 찍기 기능
    if (navigator.mediaDevices.getUserMedia) {
        navigator.mediaDevices.getUserMedia({ video: true })
            .then(function(stream) {
                const video = document.getElementById('video');
                video.srcObject = stream;
            })
            .catch(function(err) {
                console.error("카메라 접근 에러:", err);
                alert('카메라 접근 권한이 필요합니다.');
            });
    }

    document.getElementById('capture-btn').addEventListener('click', function() {
        const video = document.getElementById('video');
        const canvas = document.createElement('canvas');
        canvas.width = video.videoWidth;
        canvas.height = video.videoHeight;
        const context = canvas.getContext('2d');
        context.drawImage(video, 0, 0, canvas.width, canvas.height);

        const dataURL = canvas.toDataURL('image/png');
        // 실제 애플리케이션에서는 여기에 사진을 저장하거나 다운로드 링크를 생성하는 로직을 추가합니다.
        console.log('사진이 찍혔습니다:', dataURL); // 콘솔에 데이터 URL 로깅으로 기능 확인
    });
</script>

</body>
</html>
