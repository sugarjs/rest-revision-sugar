# rest-revision-sugar - Makes it easy to write yummy REST APIs with revisioning

This library complements [rest-sugar](https://github.com/bebraw/rest-sugar) by
providing revisioning. While it adds some overhead it makes it possible to
create wikis and such with a nice REST backend.

As there is no standard way to implement revisioning on top of a REST scheme,
I ended up making my own interpretation of the subject.

## Features

The following urls contain a prefix given at init.

* GET /? (ie. /api/v1) -&gt; API metadata (ie. schema and help)
* GET /&lt;api&gt; -&gt; Get all
* GET /&lt;api&gt;?name=foobar -&gt; Get all with the matching name
* GET /&lt;api&gt;?limit=25&offset=50 -&gt; Get with pagination
* GET /&lt;api&gt;/count -&gt; Get count
* GET /&lt;api&gt;?fields=name,color -&gt; Get name and color fields only
* POST /&lt;api&gt;?name=foobar -&gt; Create new item with the given name
* PUT /&lt;api&gt; -&gt; Disallowed, gives 403 error
* DELETE /&lt;api&gt; -&gt; Disallowed, gives 403 error

Note that it is possible to mix and match various GETs above. The API above is
what you get by using rest-sugar. There are some differences in the way you
deal with CRUD. While the operations are the same, there are some extra
considerations to keep in mind. The following urls operate on a specific
resource (ie. /&lt;api&gt;/&lt;id&gt;).

* GET /&lt;api&gt;/&lt;id&gt; -&gt; Get resource matching to the id. Returns
  the latest "active" revision.
* POST /&lt;api&gt;/&lt;id&gt; -&gt; Disallowed, gives 403 error
* PUT /&lt;api&gt;/&lt;id&gt;?name=joe -&gt; Updates the content of the given
  resource with the given field data. Creates a new revision.
* DELETE /&lt;api&gt;/&lt;id&gt; -&gt; Deletes the given resource by setting
  "active" state of all its revisions to zero. Returns an empty structure if
  successful.

### Revisioning

In addition there is a revision related API as described below:

* GET /&lt;api&gt;/&lt;id&gt;/revisions Returns all revisions related to a
  certain resource regardless of their state. If you want just "active" ones,
  use a filter (ie. active=1).
* PUT /&lt;api&gt;/&lt;id&gt;/revisions/&lt;id&gt;?active=0 Makes the revision
  and all following it inactive.
* PUT /&lt;api&gt;/&lt;id&gt;/revisions/&lt;id&gt;?active=1 Makes the revision
  and all before it active.

DELETE and POST have been disallowed for revisions and give 403 error
accordingly.

### Other Notes

Note that it is possible to emulate POST, PUT and DELETE via GET by using a
query (ie. method=post).

In case you want to use some authentication method (preferable!), you may
provide a custom auth handler for the init. It wraps each call in the API
except for the metadata one. It's signature is auth(fn) meaning you are
supposed to call fn in case auth has been made properly. The default
implementation just passes everything through.

## License

rest-revision-sugar is available under MIT. See LICENSE for more details.

