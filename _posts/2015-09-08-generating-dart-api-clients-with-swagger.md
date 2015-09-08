---
layout: post
title: Generating Dart API Clients with Swagger Codegen
---

If you have an API documented in [Swagger](http://swagger.io/) format, there's good news: communicating with it in Dart just got a lot easier. The [swagger-codegen](https://github.com/swagger-api/swagger-codegen) project, which generates API client libraries for a variety of languages, now has Dart support.

### Generating the API client

Generating an API client is pretty straightforward, but you'll need to have Java 7 and [Apache Maven 3.0.3](https://maven.apache.org/) or higher available on your path.

The first step is to fetch the swagger-codegen project:

`git clone git@github.com:swagger-api/swagger-codegen.git`

Once that's done, you'll want a shell script that you can use to execute `swagger-codegen` with the correct parameters. In `bin` you'll find `dart-petstore.sh` which you can copy and use as a base for your own script.

On the second last line (the one that begins with `ags=`), you can change the `-i` parameter to point to the location of your Swagger file, and the `-o` parameter to point to the location where you want to generate the Dart client.

If you want to further customize the generated Dart files, you can add a `-c` argument that points to the location of a JSON file with the following structure:

```
{
	"browserClient": true
	"pubName": "MyApi",
	"pubVersion": "1.0.0",
	"pubDescription": "My API client"
}
```

If `browserClient` is true, Dart code that can run in a browser will be generated, otherwise `dart:io`-compatible code will be generated. If `browserClient` is not specified, it defaults to true.

Now you can execute your shell script:

`./bin/my-api.sh`

If all went well you should now have a Dart client library generated for your API. You can use it in another Dart library as a [path dependency](https://www.dartlang.org/tools/pub/dependencies.html#path-packages), Git dependency, or any other way you would depend on a Dart package.

Using Swagger codegen, you can now avoid the tedious work of building out manual API communication, and focus all your effort on building your app!  