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

# Se ha comprobado que esta app NO respeta variables de entorno como
# PUPPETEER_EXECUTABLE_PATH, PORT o NODE_ENV (sigue arrancando con sus
# valores por defecto pase lo que pase). Así que, en vez de depender
# de que las lea, colocamos el Chromium de Alpine exactamente en la
# ruta de caché por defecto donde Puppeteer lo busca. Esa ruta incluye
# la versión concreta de Chromium que esa versión de Puppeteer espera
# (se ve en el mensaje de error si Puppeteer no lo encuentra, algo
# como ".../chrome/linux-133.0.6943.98/chrome-linux64/chrome"), así
# que puede romperse si el proyecto sube de versión de Puppeteer en el
# futuro; si vuelve a fallar, hay que actualizar PUPPETEER_CACHE_VERSION
# abajo con la versión nueva que indique el mensaje de error.
ENV PUPPETEER_SKIP_CHROMIUM_DOWNLOAD=true \
    PUPPETEER_EXECUTABLE_PATH=/usr/bin/chromium-browser \
    PUPPETEER_CACHE_VERSION=linux-133.0.6943.98 \
    NODE_ENV=production \
    PORT=8080

RUN CHROME_BIN="$(command -v chromium-browser || command -v chromium)" \
    && CACHE_DIR="/root/.cache/puppeteer/chrome/${PUPPETEER_CACHE_VERSION}/chrome-linux64" \
    && mkdir -p "$CACHE_DIR" \
    && ln -sf "$CHROME_BIN" "$CACHE_DIR/chrome"

WORKDIR /usr/src/app

# Clona la última versión de dbus-api en tiempo de build. No hay tags
# ni releases estables en el repo (solo la rama main), así que no se
# puede fijar a una versión concreta.
RUN git clone --depth 1 https://github.com/GlutenFreeSoftware/dbus-api.git . \
    && npm ci --omit=dev \
    && rm -rf .git

EXPOSE 8080

CMD ["npm", "start"]
