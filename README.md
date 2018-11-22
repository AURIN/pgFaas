# pgFaas - PostGresql Functions As A Service

Proof-of-concept of a FaaS on PostgreSQLPostGIS.  

The project is composed of four different modules   

* [Infra](https://github.com/AURIN/pgFaas-infra) A collection of recipes for the deployment of pgFaas under different scenarios.
* [Functions](https://github.com/AURIN/pgFaas-functions) Base Docker images pgFaas run functions on. 
* [API](https://github.com/AURIN/pgFaas-api) A service that allows the creation, update, deletion and invocation of functions.
* [UI](https://github.com/AURIN/pgFaas-ui) A web front-end to the API. 


## Architecture

pgFaas functions run on OpenFaas through the pgFaas API, and connect to a PostgreSQL instance.

In OpenFaas every function is a Docker container, which is based on a Docker image and some environment variables
that are passed when the container is created.

In pgFaas all functions are based the same (Node.js) image, which exposes a library to interact with a PostgreSQL
database, but, crucially, every function can be passed a Node.js script upon creation, thus allowing
containers with different behaviour to be created.

For instance, a pgFaas function may be based on this script (which, for the sake of simlicity, does
not use PostgreSQL):
```javascript
module.exports = {
  add: (sqlexec, req, callback) => {
    return callback(null, {c: req.body.a + req.body.b});
  },
  minus: (sqlexec, req, callback) => {
    return callback(null, {c: req.body.a - req.body.b});
  },
  times: (sqlexec, req, callback) => {
    return callback(null, {c: req.body.a * req.body.b});
  },
  div: (sqlexec, req, callback) => {
    return callback(null, {c: req.body.a / req.body.b});
  },
  mod: (sqlexec, req, callback) => {
    return callback(null, {c: req.body.a % req.body.b});
  }
};
```

There are four `verbs` in this function (methods, if you like) that can be invoked independently.
For instance:
```bash
curl -XPOST "http://sandbox.pgfaas.aurin.org.au/api/function/namespaces/sample/math"\
  --header "Content-Type:application/json"\
  --data '{"verb":"add", "a":2, "b":4}'
``` 

Would execute the `add` verb on arguments 2 and 4, resulting in:
`{"c":6}`


Another function (`osm` under the same `sample` namespace) contains geoprocessing functions, such as finding the K Nearest Neighbours bus stops to a location:
```bash
curl -XPOST "http://sandbox.pgfaas.aurin.org.au/api/function/namespaces/sample/osm"\
  --header "Content-Type:application/json"\
  --data '{"verb":"knnbusstops","k":20,"y":-22.28,"x":166.6}'
``` 
(this function returns GeoJSON.)


## Where to go from here

### Sandbox

An [online sandbox is available](http://sandbox.pgfaas.aurin.org.au/ui/) (treat it kindly). It gives access to an OpenStreetMap (New Caledonia) database.   


### Installation and use

Please clone the [Infra](https://github.com/AURIN/pgFaas-infra) module, and follow the instructions in the README to install pgFaas on your own infrastructure.


### Contributing

Add issues to GitHub, so that we can start a conversation.   

