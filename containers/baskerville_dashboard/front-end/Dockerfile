ARG BRANCH_NAME
FROM node:14.8.0-alpine AS build
RUN apk update && \
    apk upgrade && \
    apk add git && \
    git clone --branch $BRANCH_NAME https://github.com/equalitie/baskerville_dashboard.git && \
    cd baskerville_dashboard/front-end && ls -a
WORKDIR baskerville_dashboard/front-end/
RUN npm install && ls -a
RUN npm run build --prod


FROM nginx:1.17.1-alpine
COPY nginx.conf /etc/nginx/nginx.conf
RUN ls -a
COPY --from=build baskerville_dashboard/front-end/dist/baskerville_dashboard_frontend /usr/share/nginx/html
