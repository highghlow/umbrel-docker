# ======= ui build ========

FROM node:18 as ui-build

RUN npm install -g pnpm@8.9.2

WORKDIR /app
COPY ./app-ui .

RUN pnpm install
RUN pnpm run build

# ===== umbreld build =====

FROM node:18 as umbreld-build

WORKDIR /app
COPY ./app-umbreld .

COPY ./patches/* /patches/
RUN patch ./source/modules/apps/apps.ts /patches/no-clean-docker-state.patch
RUN patch ./source/index.ts /patches/no-clean-docker-state2.patch

RUN mkdir -p ./ui

COPY --from=ui-build /app/dist/* ./ui/

RUN npm install tsconfig
RUN npm install --omit dev --global

# ======= fake bins =======
COPY ./proxies/* /bin/

# ======== docker =========
RUN apt-get update
RUN apt-get install --yes curl
RUN curl -fsSL https://get.docker.com | bash

CMD ["umbreld", "--data-directory=/umbrel", "--log-level", "verbose"]
