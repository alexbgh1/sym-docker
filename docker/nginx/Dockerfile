FROM nginx:1.19

# Argumento desde el exterior para el usuario
ARG UID

EXPOSE $UID

# Con el usuario manejaremos los permisos
RUN adduser -u ${UID} --disabled-password --gecos "" appuser

COPY default.conf /etc/nginx/conf.d/