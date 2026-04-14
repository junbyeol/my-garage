이 프로젝트는 GCP(Google Cloud Platform) VM 인스턴스 위에서 여러 독립적인 서비스들을 운영한다.

## 📌 프로젝트 목적
* 인프라 비용 절감: 여러 개의 토이 프로젝트를 하나의 저사양 VM에 통합하여 운영 비용 절감.
* 운영지점 통일: Vercel, Firebase, AWS 등 제각각이었던 운영 방식을 하나로 통일.
* 확장성 및 이식성: 모든 서비스를 컨테이너화하여 추후 서버 이전 및 서비스 확장에 유연하게 대응. 새로운 프로젝트 배포 간소화.

---

## 🏗 아키텍처 개요
* **Gateway:** Main Nginx 컨테이너 (80, 443 포트 점유)
* **SSL:** Let's Encrypt (Certbot)를 통한 HTTPS 보안 적용
* **Services:**
  - 다양한 xxx.junbyeol.me 서비스 운영 중
* **Database:** MySQL (내부망 전용, 외부 노출 없음)

---

## 🛠 운영 가이드 (Troubleshooting & Ops)

### 1. 새로운 프로젝트(컨테이너) 추가 방법
[이력서 프로젝트](https://github.com/junbyeol/resume)의 셋팅을 참고할 것
1.  **애플리케이션 Docker 이미지 준비:** 해당 프로젝트 루트에 `Dockerfile`을 작성합니다.
2.  **docker-compose.yml 수정:** `services` 항목에 새로운 컨테이너 설정을 추가합니다.
    ```yaml
    new-project-api:
      build: ./new-project
      networks:
        - web-network
    ```
3.  **Main Nginx 설정 업데이트:** `nginx.conf`에 새로운 업스트림과 `server_name` 설정을 추가합니다.
4.  **재시작:** `docker-compose up -d --build` 실행.

### 2. 새로운 서브도메인 추가 방법
1.  **Nginx Conf 추가:** `server_name new.domain.me;` 블록을 작성하여 내부 포트와 연결합니다.
2.  **SSL 인증서 발급:** Certbot을 실행하여 새 도메인에 대한 인증서를 갱신/추가합니다.


# 새로운 서비스 추가 방법
### 1. 새로운 도메인의 인증서 등록

```bash
# /infra 경로로 이동

# 현재 구조로는 인증서 발급을 위해서 잠시 80번 포트를 certbot에게 열어주어야 하는 문제가 있음
# 이 때, 본 VM의 모든 서비스가 잠시 중단되므로 개선이 필요
docker compose stop nginx

# (선택) 80 포트 비워졌는지 확인
sudo lsof -i :80

# xxx.junbyeol.me의 인증서를 발급할 때
sudo certbot certonly --standalone --cert-name xxx.junbyeol.me -d xxx.junbyeol.me

docker compose start nginx

```

### 2. 새로운 서비스 컨테이너 실행
새로운 서비스 경로에서 docker compose up

### 3. nginx.conf에 블럭 추가 후
```bash
# nginx 컨테이너에서 conf 파일이 유효한지 테스트
docker compose exec nginx nginx -t

# conf 파일이 유효했다면 reload
docker compose exec nginx nginx -s reload
```