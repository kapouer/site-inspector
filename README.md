# sittin
A site indexer with pluggable backend and api


## Usage

```
var sittinBack = require('sittin-pg')({
	database: "postgres://sittin@localhost/sittindb"
});

app.get('/api/search/private', sittinBack);

var sittinFront = require('sittin')({
	backend: '/api/search/backend',
	domains: ['localhost'],
	interval: [1000, 60000]
});
app.get('/api/search/public', sittinFront);
```
