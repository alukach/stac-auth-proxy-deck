stac-auth-proxy <!-- .element: class="r-fit-text" -->

---

anthony lukach

cloud engineer@developmentseed

---

# why?

auth story is still unsolved for the STAC ecosystem

--

<small>why...</small>

many stac backends

- stac-fastapi-pgstac
- stac-fastapi-elasticsearch
- franklin
- staccato
- stac-server

--

<small>why...</small>

many auth strategies

- 🍪
- jwts
- basic auth
- api tokens

--

<small>why...</small>

varied auth needs

1. route-level auth <!-- .element: class="fragment" -->
1. record-level auth <!-- .element: class="fragment" -->
1. asset access <!-- .element: class="fragment" -->

NOTE:

## route level auth

- entirely private catalogs
- public catalogs, restricted edits (useful for data editors who need to make one-off edits to a record)

## record-level auth

- private items/collections (e.g. collections in preview state)
- personal collections (multi-tenant system)

## asset access

- typically, STAC is merely for the discovery of assets
- it is the assets themselves that matter
- how do we marry asset access policies with our STAC access policies?

---

# what?

target the most common needs

- JWTs <!-- .element: class="fragment" -->
- route-level auth <!-- .element: class="fragment" -->
- record-level auth <!-- .element: class="fragment" -->
- asset access via signed-urls <!-- .element: class="fragment" -->

---

# how?

embrace standards to maximize compatibility

- auth: [oidc](https://openid.net/specs/openid-connect-core-1_0-final.html)
- record-level filtering: [filter extension](https://github.com/stac-api-extensions/filter)
- self-describing api: [authentication extension](https://github.com/stac-extensions/authentication)
- self-describing api: [openapi specification](https://swagger.io/specification)

---

# route-level auth

--

<small>how: route-level auth...</small>

proxy setup

```dotenv[|1|2-3|4-5|6]
UPSTREAM_URL=http://stac:8001
OIDC_DISCOVERY_URL=http://localhost:8888/.well-known/openid-configuration
OIDC_DISCOVERY_INTERNAL_URL=http://oidc:8888/.well-known/openid-configuration
DEFAULT_PUBLIC=false
PUBLIC_ENDPOINTS={ "^/api.html$": ["GET"], "^/api$": ["GET"], "^/docs/oauth2-redirect": ["GET"], "^/healthz": ["GET"] }
PRIVATE_ENDPOINTS={ "^/collections$": [["POST", "editor"]] }
```

--

simple auth demo 🧑‍💻

---

<small>(while we're at it...)</small>

# self-description

augment responses with:

1. [authentication extension](https://github.com/stac-extensions/authentication)
2. [openapi specification](https://swagger.io/specification)

--

<small>how: self-description...</small>

## [authentication extension](https://github.com/stac-extensions/authentication)

```json[|37-39|40-106|59-67|63|64-66|120-125]
{
  "type": "Catalog",
  "id": "stac-fastapi",
  "title": "stac-fastapi",
  "description": "stac-fastapi",
  "stac_version": "1.0.0",
  "conformsTo": [
    "http://www.opengis.net/spec/cql2/1.0/conf/basic-cql2",
    "http://www.opengis.net/spec/cql2/1.0/conf/cql2-json",
    "http://www.opengis.net/spec/cql2/1.0/conf/cql2-text",
    "http://www.opengis.net/spec/ogcapi-common-2/1.0/conf/simple-query",
    "http://www.opengis.net/spec/ogcapi-features-1/1.0/conf/core",
    "http://www.opengis.net/spec/ogcapi-features-1/1.0/conf/geojson",
    "http://www.opengis.net/spec/ogcapi-features-1/1.0/conf/oas30",
    "http://www.opengis.net/spec/ogcapi-features-3/1.0/conf/features-filter",
    "http://www.opengis.net/spec/ogcapi-features-3/1.0/conf/filter",
    "https://api.stacspec.org/v1.0.0-rc.1/collection-search",
    "https://api.stacspec.org/v1.0.0-rc.1/collection-search#fields",
    "https://api.stacspec.org/v1.0.0-rc.1/collection-search#filter",
    "https://api.stacspec.org/v1.0.0-rc.1/collection-search#free-text",
    "https://api.stacspec.org/v1.0.0-rc.1/collection-search#query",
    "https://api.stacspec.org/v1.0.0-rc.1/collection-search#sort",
    "https://api.stacspec.org/v1.0.0-rc.2/item-search#filter",
    "https://api.stacspec.org/v1.0.0/collections",
    "https://api.stacspec.org/v1.0.0/collections/extensions/transaction",
    "https://api.stacspec.org/v1.0.0/core",
    "https://api.stacspec.org/v1.0.0/item-search",
    "https://api.stacspec.org/v1.0.0/item-search#fields",
    "https://api.stacspec.org/v1.0.0/item-search#query",
    "https://api.stacspec.org/v1.0.0/item-search#sort",
    "https://api.stacspec.org/v1.0.0/ogcapi-features",
    "https://api.stacspec.org/v1.0.0/ogcapi-features#fields",
    "https://api.stacspec.org/v1.0.0/ogcapi-features#query",
    "https://api.stacspec.org/v1.0.0/ogcapi-features#sort",
    "https://api.stacspec.org/v1.0.0/ogcapi-features/extensions/transaction"
  ],
  "stac_extensions": [
    "https://stac-extensions.github.io/authentication/v1.1.0/schema.json"
  ],
  "links": [
    {
      "rel": "self",
      "type": "application/json",
      "title": "This document",
      "href": "http://localhost:8000/",
      "auth:refs": [
        "oidc"
      ]
    },
    {
      "rel": "root",
      "type": "application/json",
      "title": "Root",
      "href": "http://localhost:8000/",
      "auth:refs": [
        "oidc"
      ]
    },
    {
      "rel": "data",
      "type": "application/json",
      "title": "Collections available for this Catalog",
      "href": "http://localhost:8000/collections",
      "auth:refs": [
        "oidc"
      ]
    },
    {
      "rel": "conformance",
      "type": "application/json",
      "title": "STAC/OGC conformance classes implemented by this server",
      "href": "http://localhost:8000/conformance",
      "auth:refs": [
        "oidc"
      ]
    },
    {
      "rel": "search",
      "type": "application/geo+json",
      "title": "STAC search [GET]",
      "href": "http://localhost:8000/search",
      "method": "GET",
      "auth:refs": [
        "oidc"
      ]
    },
    {
      "rel": "search",
      "type": "application/geo+json",
      "title": "STAC search [POST]",
      "href": "http://localhost:8000/search",
      "method": "POST",
      "auth:refs": [
        "oidc"
      ]
    },
    {
      "rel": "http://www.opengis.net/def/rel/ogc/1.0/queryables",
      "type": "application/schema+json",
      "title": "Queryables available for this Catalog",
      "href": "http://localhost:8000/queryables",
      "method": "GET",
      "auth:refs": [
        "oidc"
      ]
    },
    {
      "rel": "service-desc",
      "type": "application/vnd.oai.openapi+json;version=3.0",
      "title": "OpenAPI service description",
      "href": "http://localhost:8000/api"
    },
    {
      "rel": "service-doc",
      "type": "text/html",
      "title": "OpenAPI service documentation",
      "href": "http://localhost:8000/api.html"
    }
  ],
  "auth:schemes": {
    "oidc": {
      "type": "openIdConnect",
      "openIdConnectUrl": "http://localhost:8888/.well-known/openid-configuration"
    }
  }
}
```

--

<small>how: self-description...</small>

## [openapi specification](https://swagger.io/specification)

```json[|9-10|43|49-52]
{
  "openapi": "3.1.0",
  "info": {
    "title": "stac-fastapi",
    "description": "stac-fastapi",
    "version": "0.1"
  },
  "paths": {
    "/collections": {
      "post": {
        "tags": ["Transaction Extension"],
        "summary": "Create Collection",
        "description": "Endpoint.",
        "operationId": "Create_Collection_collections_post",
        "requestBody": {
          "required": true,
          "content": {
            "application/json": {
              "schema": { "$ref": "#/components/schemas/Collection-Input" }
            }
          }
        },
        "responses": {
          "201": {
            "description": "Successful Response",
            "content": {
              "application/json": {
                "schema": {
                  "$ref": "#/components/schemas/stac_pydantic__collection__Collection-Output"
                }
              }
            }
          },
          "422": {
            "description": "Validation Error",
            "content": {
              "application/json": {
                "schema": { "$ref": "#/components/schemas/HTTPValidationError" }
              }
            }
          }
        },
        "security": [{ "oidcAuth": ["editor"] }]
      }
    }
  },
  "components": {
    "securitySchemes": {
      "oidcAuth": {
        "type": "openIdConnect",
        "openIdConnectUrl": "http://localhost:8888/.well-known/openid-configuration"
      }
    }
  }
}
```

--

swagger ui demo 🧑‍💻

http://localhost:8000/api.html

```env
OPENAPI_SPEC_ENDPOINT=/api
DEFAULT_PUBLIC=true
PRIVATE_ENDPOINTS={ "^/collections$": [["POST", "editor"]] }
```

---

# record-level auth

[filter extension](https://github.com/stac-api-extensions/filter)

--

<small>how: record-level auth...</small>

## CQL2

_(stac lingua franca)_

simple or complex filter logic <!-- .element: class="fragment" -->

--

<small>how: record-level auth...</small>

## filter builders

1. jinja2 template strings <!-- .element: class="fragment" -->
2. open policy agent <!-- .element: class="fragment" -->
3. bring your own <!-- .element: class="fragment" -->

--

<small>how: record-level auth...</small>

## diy filter builders

```py[|7|8|10|4|]
from dataclasses import dataclass
from typing import Any, Alias

type Expr = dict[str, Any] | str

@dataclass
class ExampleFilter:
    api_url: str  # initialization args go here...

    async def __call__(self, context: dict[str, Any]) -> Expr:
        ...
```

```env
ITEM_FILTER_CLS=my_module:ExampleFilter
ITEM_FILTER_KWARGS={"api_url": "https://microsoft.com"}
```

<!-- .element: class="fragment" -->

--

<small>how: record-level auth...</small>

## calling a filter builder

```python[|2-8|9|11|12]
ctx = {
    "req": {
        "path": request.url.path,
        "method": request.method,
        "query_params": dict(request.query_params),
        "path_params": requests.extract_variables(request.url.path),
        "headers": dict(request.headers),
    },
    "payload": decoded_jwt_payload,
}
cql2_filter = await filter_builder(ctx)  # str or dict
expr = cql2.Expr(cql2_filter)
```

--

<!-- .slide: data-background-iframe="http://developmentseed.org/cql2-rs/latest/" data-background-interactive style="color:black" -->

--

<small>how: record-level auth...</small>

applying filter on list-endpoints

<div class="mermaid">
sequenceDiagram
    Client->>Proxy: GET /collections
    Note over Proxy: EnforceAuth checks credentials
    Note over Proxy: BuildCql2Filter creates filter
    Note over Proxy: ApplyCql2Filter applies filter to request
    Proxy->>STAC API: GET /collection?filter=(collection=landsat)
    STAC API->>Client: Response
</div>

--

<small>how: applying record-level auth...</small>

applying filter on detail-endpoints

<div class="mermaid">
sequenceDiagram
    Client->>Proxy: GET /collections/abc123
    Note over Proxy: EnforceAuth checks credentials
    Note over Proxy: BuildCql2Filter creates filter
    Proxy->>STAC API: GET /collection/abc123
    Note over Proxy: ApplyCql2Filter validates the response
    STAC API->>Client: Response
</div>

<small>(filter extension doesn't apply)</small>

--

record-level filter demo 🧑‍💻

```env
DEFAULT_PUBLIC=true
COLLECTIONS_FILTER_CLS=stac_auth_proxy.filters:Template
COLLECTIONS_FILTER_ARGS=["{{ \"1=1\" if payload else \"(preview IS NULL) OR (preview = false)\" }}"]
```

---

# asset access

--

<small>how: asset access...</small>

🚧 under construction 🚧

--
<small>how: asset access...</small>

asset server 🤝 auth server

--

<small>how: asset access...</small>

signed URLs

```json[|2|3|6-7|8-36|38]
"auth:schemes": {
  "azure_signed_url_auth": {
    "type": "signedUrl",
    "description": "Requires an authentication API",
    "flows": {
      "authorizationApi": "https://example.com/signed_url/authorize",
      "method": "POST",
      "parameters": {
        "collection_id": {
          "in": "body",
          "required": true,
          "description": "collection id",
          "schema": {
            "type": "string",
            "examples": "landsat"
          }
        },
        "item_id": {
          "in": "body",
          "required": true,
          "description": "item id",
          "schema": {
            "type": "string",
            "examples": "landsat"
          }
        },
        "asset_key": {
          "in": "body",
          "required": true,
          "description": "asset key",
          "schema": {
            "type": "string",
            "examples": "analytic"
          }
        }
      },
      "auth:refs": ["oauth"],
      "responseField": "signed_url"
    },
    "oauth": {
      "type": "oauth2",
      "description": "requires a login and user token",
      "flows": {
        "authorizationUrl": "https://example.com/oauth/authorize",
        "tokenUrl": "https://example.com/oauth/token",
        "scopes": {}
      }
    }
  }
}
```

--

<small>how: asset access...</small>

configuration

```env[|2|3|4|5-6|7-35]
SIGNED_URL_SCHEMES={
  "azure_signed_url_auth": {
    "href_expr": "^https://[a-z0-9\-]+\.blob\.core\.windows\.net\/.+",
    "endpoint": "/_sign/azure",
    "cls": "custom:SignAzure"
    "args": [],
    "parameters": {
      "collection_id": {
        "in": "body",
        "required": true,
        "description": "collection id",
        "schema": {
          "type": "string",
          "examples": "landsat"
        }
      },
      "item_id": {
        "in": "body",
        "required": true,
        "description": "item id",
        "schema": {
          "type": "string",
          "examples": "landsat"
        }
      },
      "asset_key": {
        "in": "body",
        "required": true,
        "description": "asset key",
        "schema": {
          "type": "string",
          "examples": "analytic"
        }
      }
    }
  }
}
```

---

## questions?

[github.com/developmentseed/stac-auth-proxy](https://github.com/developmentseed/stac-auth-proxy/)
