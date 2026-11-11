# 1. 기본 이미지 선택: nginx
FROM nginx:latest
# 2. 적용한 커스텀 포인트와 목적: 직접 만든 커스텀 html 페이지를 기본 웹 root에 복사(정적 파일 교체)
COPY src/index.html /usr/share/nginx/html/index.html