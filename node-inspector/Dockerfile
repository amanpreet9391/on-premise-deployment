FROM node:7

RUN mkdir /app
WORKDIR /app
COPY package.json /app
RUN npm install
COPY . /app
RUN npm install -g node-inspector

CMD ["node-inspector","start"]
CMD ["node","--inspect-brk=0.0.0.0"]
#CMD ["node","--debug"]
