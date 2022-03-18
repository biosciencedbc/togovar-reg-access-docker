# Dockerfile for https://github.com/dbcls/sparqlist

FROM node:14

ENV PORT 3000
ENV ROOT_PATH=/sparqlist_reg_access/

RUN npm -g install npm
RUN useradd --create-home --home-dir /app app

USER app
WORKDIR /app
RUN git init && git remote add origin https://github.com/dbcls/sparqlist.git && git fetch --all --prune && git pull origin main 

RUN npm ci --production && npm run build
CMD npm start
