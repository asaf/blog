---
tags:
- nodejs
- models
- nodejs-model
- validations
- schema
- couchdb
comments: true
date: 2013-08-06T00:00:00Z
title: nodejs-model is born
url: /2013/08/06/nodejs-model-is-born/
---

I've been struggling lately to find an appropriate way to handle models correctly,
In one hand, these noSQL DBs such _CouchDB_ have great flexibility and I didn't want to ruin that with a blowing up ORM framework,
in the other hand once the application grows a little bit more than a contact list app, then things become messy.

While writing a larger app with Node, I find myself struggling with finding solutions to problems such:
(there are few more but these are the main)

* **Validating a document (model) properties (application level) before storing it in the DB.**
* **A minimal schema where each attribute is defined per model to ensure no allowed attributes leaks into the model.**
* **Automatically convert a property value when a value is set.**
* **Filter critical, sensitive attributes when transfering the model on the wire to external clients.**
* **Updating an existing domain via an accessibility-aware method (you may want users to update their profile but not letting them directly change their password attribute)**

So I couldn't really find a good module to achieve all of that without sticking a giant into my lightweight app, so I built one, it's named
[nodejs-model](https://github.com/asaf/nodejs-model), available here: https://github.com/asaf/nodejs-model

For now, read the docs on the github repo for more info, i will post series of posts about _nodejs-model_ in the near future.