# pgFaas - PostGresql Functions As A Service

Proof-of-concept of a FaaS on PostgreSQL.  

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
not use POstgreSQL):
```javascript
module.exports = {
  echo: (sqlexec, req, callback) => {
    return callback(null, req.body);
  },
  plus: (sqlexec, req, callback) => {
    return callback(null, {c: req.body.a + req.body.b});
  },
  headers: (sqlexec, req, callback) => {
    return callback(null, req.headers);
  }
};
```

There are three `verbs` in this function (methods, if you like) that can be invoked independently.
For instance:
```bash
curl -XPOST "http://pgfaas/api/simple/dazzling_dijkstra "\
  --header "Content-Type:application/json"\
  --data '{"verb":"plus", "a":2, "b":4}
``` 
Would execute the `plus` verb on arguments 2 and 4, resulting in:
`{"c":6}`


## Sandbox

### Where to go from here

An online sandbox is available at TODO (treat it kindly). It is re-created every 24 hours, to ensure its viability even after abuse.   


### Installation and use

Please clone the [Infra](https://github.com/AURIN/pgFaas-infra) module, and follow the instructions in the README to install pgFaas on your own infrastructure.


## Contributing

TODO   

