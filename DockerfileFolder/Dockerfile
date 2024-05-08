FROM node:14

WORKDIR /usr/src/app

COPY package.json .
RUN npm install 
COPY . .

# expose the app on web
EXPOSE 3000

CMD ["node", "index.js"]
