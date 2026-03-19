# first-garage-machine

[https://gemini.google.com/share/25677ccddf05](https://gemini.google.com/share/25677ccddf05)
- Gemini와의 대화를 통해 작성중인 내 프로젝트용 VM의 config

# 새로운 도메인을 포함한 인증서 등록 방법
(infra 경로에서) docker compose stop nginx
sudo certbot certonly --standalone \
  --cert-name junbyeol.me \
  --expand \
  -d junbyeol.me \
  -d www.junbyeol.me \
  -d resume.junbyeol.me \
  -d github.junbyeol.me \
  -d linkedin.junbyeol.me
docker compose start nginx