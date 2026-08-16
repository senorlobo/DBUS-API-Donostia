ARG BUILD_FROM
FROM $BUILD_FROM

# Node.js, npm, Chromium y las librerías que necesita para ejecutarse
# en Alpine (sin ellas Puppeteer falla al lanzar el navegador).
RUN apk add --no-cache \
        nodejs \
        npm \
        git \
        chromium \
        nss \
        freetype \
        freetype-dev \
        harfbuzz \
        ca-certificates \
        ttf-freefont

# Puppeteer no distribuye un Chromium precompilado para Alpine/musl,
# así que usamos el que acabamos de instalar por apk en vez de
# intentar descargarlo.
ENV PUPPETEER_SKIP_CHROMIUM_DOWNLOAD=true \
    PUPPETEER_EXECUTABLE_PATH=/usr/bin/chromium-browser \
    NODE_ENV=production \
    PORT=8080

WORKDIR /usr/src/app

# Clona la última versión de dbus-api en tiempo de build. Fija aquí un
# commit/tag concreto (en vez de la rama main) si en algún momento
# quieres una versión estable en vez de la última.
RUN git clone --depth 1 https://github.com/GlutenFreeSoftware/dbus-api.git . \
    && npm ci --omit=dev \
    && rm -rf .git

EXPOSE 8080

CMD ["npm", "start"]
