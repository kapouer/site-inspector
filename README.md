# site-inspector
A site indexer with pluggable backend and api


## Usage

```
var backend = require('site-inspector-backend-pg')({
	database: "postgres://sittin@localhost/sittindb"
});

app.get('/api/search/private', backend);

var frontend = require('site-inspector')({
	backend: '/api/search/backend',
	domains: ['localhost'],
	interval: [1000, 60000]
});
app.get('/api/search/public', frontend);
```
