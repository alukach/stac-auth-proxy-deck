stac-auth-proxy <!-- .element: class="r-fit-text" -->

---

anthony lukach

cloud engineer@developmentseed

---

## why?

auth story is still unsolved for stac ecosystem

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

NOTE: cookies vs JWTs vs basic auth

--

<small>why...</small>

varied auth needs

1. route level auth  <!-- .element: class="fragment" -->
1. record-level auth  <!-- .element: class="fragment" -->
1. asset access  <!-- .element: class="fragment" -->

NOTE:

## route level auth
- entirely private catalogs
- public catalogs, rectricted edits (useful for data editors who need to make one-off edits to a record)

## record-level auth
- private items/collections (e.g. collections in preview state)
- personal collections (multi-tenant system)

## asset access
- typically, STAC is merely for the discovery of assets
- it is the assets themselves that matter
- how does we marry asset access policies with our STAC access policies?

---

## what?

targeting the most common needs

- auth via oidc  <!-- .element: class="fragment" -->
- route-level auth  <!-- .element: class="fragment" -->
- row-level auth  <!-- .element: class="fragment" -->
- asset access via signed-urls <!-- .element: class="fragment" -->

---

## how?

embrace standards

- auth: [oidc](https://openid.net/specs/openid-connect-core-1_0-final.html)
- row-level filtering: [filter extension](https://github.com/stac-api-extensions/filter)
- documentation: [authentication extension](https://github.com/stac-extensions/authentication)
- documentation: [openapi specification](https://swagger.io/specification)

--

<small>how: applying auth...</small>

```dotenv[|1|2-3|4-5]
UPSTREAM_URL=http://stac:8001
OIDC_DISCOVERY_URL=http://localhost:8888/.well-known/openid-configuration
OIDC_DISCOVERY_INTERNAL_URL=http://oidc:8888/.well-known/openid-configuration
DEFAULT_PUBLIC=false
PUBLIC_ENDPOINTS={ "^/api.html$": ["GET"], "^/api$": ["GET"], "^/docs/oauth2-redirect": ["GET"], "^/healthz": ["GET"] }
PRIVATE_ENDPOINTS='{ "^/collections$": ["POST"], "^/collections/([^/]+)$": ["PUT", "PATCH", "DELETE"], "^/collections/([^/]+)/items$": ["POST"], "^/collections/([^/]+)/items/([^/]+)$": ["PUT", "PATCH", "DELETE"], "^/collections/([^/]+)/bulk_items$": ["POST"] }
```

--

## how?

augment requests & response

--

<small>how...</small>

embracing existing stac extensions

--

<small>how...</small>

## [authentication extension](https://github.com/stac-extensions/authentication)

```json[|3-5|15-35]
{
  "stac_version": "1.0.0",
  "stac_extensions": [
    "https://stac-extensions.github.io/authentication/v1.1.0/schema.json"
  ],
  "type": "Feature",
  "id": "item",
  "bbox": [172.9, 1.3, 173, 1.4],
  "geometry": {
    "type": "Polygon",
    "coordinates": [[[172.9, 1.3], [173, 1.3], [173, 1.4], [172.9, 1.4], [172.9, 1.3]]]
  },
  "properties": {
    "datetime": "2020-12-11T22:38:32Z",
    "auth:schemes": {
      "oauth": {
        "type": "oauth2",
        "description": "requires a login and user token",
        "flows": {
          "authorizationCode": {
            "authorizationUrl": "https://example.com/oauth/authorize",
            "tokenUrl": "https://example.com/oauth/token",
            "scopes": {
              "read:example": "Read the example data",
              "write:example": "Write the example data",
              "admin:example": "Read/write/delete the example data"
            }
          }
        }
      },
      "none": {
        "type": "http",
        "scheme": "basic",
        "description": "Free access without restrictions"
      }
    }
  },
  "links": [
    {
      "href": "https://example.com/examples/item.json",
      "rel": "self"
    }
  ],
  "assets": {
    "data": {
      "href": "https://example.com/examples/file.xyz",
      "title": "Secure Asset Example",
      "type": "application/vnd.example",
      "roles": [
        "data"
      ],
      "auth:refs": [
        "oauth"
      ]
    }
  }
}
```

_value_: self-describing API 📖

---

https://github.com/developmentseed/stac-auth-proxy/