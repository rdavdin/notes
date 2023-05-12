## Cors in Nodejs/Express [Detail here](https://expressjs.com/en/resources/middleware/cors.html)
#### Here some quick notes

### 1. Enable Cors for all requests
```
const cors = require('cors');
apps.use(cors());
```
### 2. Enable Cors for a single request
```
const cors = require('cors');
apps.get('/', cors(), (req, res)=>{
  //do sth
});
```
### 3. Enable Cors for all requests w/ limited origins
```
const cors = require('cors');
const originList = ['http://localhost:3000', 'http://localhost:3001'];
const corsOptionsDelegate = function(req, callback){
  let corsOptions = { origin: false };
  if(originList.indexOf(req.header('Origin') != -1)){
    corsOptions = { origin: true };
  }
  callback(null, corsOptions);
}
app.use(cors(corsOptionsDelegate));

apps.get('/', (req, res)=>{
  //do sth
});
```
### 4. Enable Cors for a single request w/ limited origins
```
const cors = require('cors');
const corsOptions = {
  origin: 'http://localhost:3000'
}
apps.get('/', cors(corsOptions), (req, res)=>{
  //do sth
});
```

**_Or_**
```
const cors = require('cors');
const originList = ['http://localhost:3000', 'http://localhost:3001'];
const corsOptionsDelegate = function(req, callback){
  let corsOptions = { origin: false };
  if(originList.indexOf(req.header('Origin') != -1)){
    corsOptions = { origin: true };
  }
  callback(null, corsOptions);
}
apps.get('/', cors(corsOptionsDelegate), (req, res)=>{
  //do sth
});
```