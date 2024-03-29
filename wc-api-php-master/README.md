# WooCommerce API - PHP Client

Um wrapper PHP para a API REST do WooCommerce. Interaja facilmente com a API REST do WooCommerce com segurança usando esta biblioteca. Se você estiver usando uma conexão HTTPS, esta biblioteca usará o BasicAuth, caso contrário, usará o Oauth para fornecer uma conexão segura ao WooCommerce.

[![build status](https://secure.travis-ci.org/woocommerce/wc-api-php.svg)](http://travis-ci.org/woocommerce/wc-api-php)
[![Scrutinizer Code Quality](https://scrutinizer-ci.com/g/woocommerce/wc-api-php/badges/quality-score.png?b=master)](https://scrutinizer-ci.com/g/woocommerce/wc-api-php/?branch=master)
[![PHP version](https://badge.fury.io/ph/automattic%2Fwoocommerce.svg)](https://packagist.org/packages/automattic/woocommerce)

## Instalação

```
composer require automattic/woocommerce
```

## Getting started

Gere credenciais de API (chave do consumidor e segredo do consumidor)seguindo estas instruções<http://docs.woocommerce.com/document/woocommerce-rest-api/>
.

Check out the WooCommerce API endpoints and data that can be manipulated in <https://woocommerce.github.io/woocommerce-rest-api-docs/>.

## Setup

Configuração para a nova integração da API WP REST (WooCommerce 2.6 ou posterior):

```php
require __DIR__ . '/vendor/autoload.php';

use Automattic\WooCommerce\Client;

$woocommerce = new Client(
    'http://example.com', 
    'ck_XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX', 
    'cs_XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX',
    [
        'version' => 'wc/v3',
    ]
);
```

## Client class

```php
$woocommerce = new Client($url, $consumer_key, $consumer_secret, $options)
```

### Options

| Option            | Type     | Required | Description                                |
|-------------------|----------|----------|--------------------------------------------|
| `url`             | `string` | yes      | Your Store URL, example: http://woo.dev/   |
| `consumer_key`    | `string` | yes      | Your API consumer key                      |
| `consumer_secret` | `string` | yes      | Your API consumer secret                   |
| `options`         | `array`  | no       | Extra arguments (see client options table) |

#### Client options

| Option              | Type     | Required | Description                                                                                                            |
|---------------------|----------|----------|------------------------------------------------------------------------------------------------------------------------|
| `version`           | `string` | no       | API version, default is `wc/v3`                                                                                        |
| `timeout`           | `int`    | no       | Request timeout, default is `15`                                                                                       |
| `verify_ssl`        | `bool`   | no       | Verify SSL when connect, use this option as `false` when need to test with self-signed certificates, default is `true` |
| `follow_redirects`  | `bool`   | no       | Allow the API call to follow redirects                                                                                 |
| `query_string_auth` | `bool`   | no       | Force Basic Authentication as query string when `true` and using under HTTPS, default is `false`                       |
| `oauth_timestamp`   | `string` | no       | Custom oAuth timestamp, default is `time()`                                                                            |
| `user_agent`        | `string` | no       | Custom user-agent, default is `WooCommerce API Client-PHP`                                                             |
| `wp_api_prefix`     | `string` | no       | Custom WP REST API URL prefix, used to support custom prefixes created with the `rest_url_prefix` filter               |
| `wp_api`            | `bool`   | no       | Set to `false` in order to use the legacy WooCommerce REST API (deprecated and not recommended)                        |

## Client methods

### GET

```php
$woocommerce->get($endpoint, $parameters = [])
```

### POST

```php
$woocommerce->post($endpoint, $data)
```

### PUT

```php
$woocommerce->put($endpoint, $data)
```

### DELETE

```php
$woocommerce->delete($endpoint, $parameters = [])
```

### OPTIONS

```php
$woocommerce->options($endpoint)
```

#### Arguments

| Params       | Type     | Description                                                  |
|--------------|----------|--------------------------------------------------------------|
| `endpoint`   | `string` | WooCommerce API endpoint, example: `customers` or `order/12` |
| `data`       | `array`  | Only for POST and PUT, data that will be converted to JSON   |
| `parameters` | `array`  | Only for GET and DELETE, request query string                |

#### Response

All methods will return arrays on success or throwing `HttpClientException` errors on failure.

```php
use Automattic\WooCommerce\HttpClient\HttpClientException;

try {
    // Array of response results.
    $results = $woocommerce->get('customers');
    // Example: ['customers' => [[ 'id' => 8, 'created_at' => '2015-05-06T17:43:51Z', 'email' => ...
    echo '<pre><code>' . print_r( $results, true ) . '</code><pre>'; // JSON output.

    // Last request data.
    $lastRequest = $woocommerce->http->getRequest();
    echo '<pre><code>' . print_r( $lastRequest->getUrl(), true ) . '</code><pre>'; // Requested URL (string).
    echo '<pre><code>' . print_r( $lastRequest->getMethod(), true ) . '</code><pre>'; // Request method (string).
    echo '<pre><code>' . print_r( $lastRequest->getParameters(), true ) . '</code><pre>'; // Request parameters (array).
    echo '<pre><code>' . print_r( $lastRequest->getHeaders(), true ) . '</code><pre>'; // Request headers (array).
    echo '<pre><code>' . print_r( $lastRequest->getBody(), true ) . '</code><pre>'; // Request body (JSON).

    // Last response data.
    $lastResponse = $woocommerce->http->getResponse();
    echo '<pre><code>' . print_r( $lastResponse->getCode(), true ) . '</code><pre>'; // Response code (int).
    echo '<pre><code>' . print_r( $lastResponse->getHeaders(), true ) . '</code><pre>'; // Response headers (array).
    echo '<pre><code>' . print_r( $lastResponse->getBody(), true ) . '</code><pre>'; // Response body (JSON).

} catch (HttpClientException $e) {
    echo '<pre><code>' . print_r( $e->getMessage(), true ) . '</code><pre>'; // Error message.
    echo '<pre><code>' . print_r( $e->getRequest(), true ) . '</code><pre>'; // Last request data.
    echo '<pre><code>' . print_r( $e->getResponse(), true ) . '</code><pre>'; // Last response data.
}
```
