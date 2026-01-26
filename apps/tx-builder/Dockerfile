# 1) Build stage
FROM node:20-alpine AS build
WORKDIR /repo

# node-gyp(네이티브 모듈) 빌드에 필요한 툴체인
RUN apk add --no-cache python3 make g++

# Yarn workspace 빌드를 위해 루트 워크스페이스 파일들이 필요합니다.
COPY package.json yarn.lock nx.json tsconfig.json ./
COPY apps/tx-builder ./apps/tx-builder

# 의존성 설치 (워크스페이스 전체 관점)
RUN yarn install --frozen-lockfile

# tx-builder 빌드 (apps/tx-builder/build 생성)
WORKDIR /repo
RUN yarn workspace tx-builder build


# 2) Runtime stage (nginx로 정적 서빙)
FROM nginx:1.26-alpine AS runtime

# /tx-builder 서브패스 서빙 + SPA 라우팅 대응 nginx 설정
COPY apps/tx-builder/nginx.conf /etc/nginx/conf.d/default.conf

# 빌드 산출물 복사
COPY --from=build /repo/apps/tx-builder/build /usr/share/nginx/html/tx-builder

EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
