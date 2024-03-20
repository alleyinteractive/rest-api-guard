# REST API Guard

Stable tag: 1.3.2

Requires at least: 6.0

Tested up to: 6.0

Requires PHP: 8.0

License: GPL v2 or later

Tags: alleyinteractive, rest-api-guard

Contributors: sean212

[![Coding Standards](https://github.com/alleyinteractive/wp-rest-api-guard/actions/workflows/coding-standards.yml/badge.svg)](https://github.com/alleyinteractive/wp-rest-api-guard/actions/workflows/coding-standards.yml)
[![Testing Suite](https://github.com/alleyinteractive/wp-rest-api-guard/actions/workflows/unit-test.yml/badge.svg)](https://github.com/alleyinteractive/wp-rest-api-guard/actions/workflows/unit-test.yml)

Restrict and control access to the REST API.

## Installation

You can install the package via composer:

```bash
composer require alleyinteractive/wp-rest-api-guard
```

## Usage

The WordPress REST API is generally very public and can share a good deal of
information with the internet anonymously. This plugin aims to make it easier to
restrict access to the REST API for your WordPress site.

Out of the box the plugin can:

- Disable anonymous access to the REST API.
- Restrict and control anonymous access to the REST API by namespace, path, etc.

### Settings Page

The plugin can be configured via the Settings page (`Settings -> REST API
Guard`) or via the relevant filter.

![Screenshot of plugin settings screen](https://user-images.githubusercontent.com/346399/194411352-aa05e939-3fd1-4e37-a3d5-276c1c5c288f.png)

### Preventing Access to User Information (`wp/v2/users`)

By default, the plugin will restrict anonymous access to the users endpoint.
This can be prevented in the plugin's settings or via code:

```php
add_filter( 'rest_api_guard_allow_user_access', fn () => true );
```

### Preventing Access to Index (`/`) or Namespace Endpoints (`wp/v2`)

To prevent anonymous users from browsing your site and discovering what plugins/post types are set up, the plugin restricts access to the index (`/`) and namespace (`wp/v2`) endpoints. This can be prevented in the plugin's settings or via code:

```php
// Allow index access.
add_filter( 'rest_api_guard_allow_index_access', fn () => true );

// Allow namespace access.
add_filter( 'rest_api_guard_allow_namespace_access', fn ( string $namespace ) => true );
```

### Restrict Anonymous Access to the REST API

The plugin can restrict anonymous access for any request to the REST API in the plugin's settings or via code:

```php
add_filter( 'rest_api_guard_prevent_anonymous_access', fn () => true );
```

### Limit Anonymous Access to Specific Namespaces/Routes (Allowlist)

Anonymous users can be granted access only to specific namespaces/routes.
Requests outside of these paths will be denied. This can be configured in the
plugin's settings or via code:

```php
add_filter(
	'rest_api_guard_anonymous_requests_allowlist',
	function ( array $paths, WP_REST_Request $request ): array {
		// Allow other paths not included here will be denied.
		$paths[] = 'wp/v2/post';
		$paths[] = 'custom-namespace/v1/public/*';

		return $paths;
	},
	10,
	2
);
```

### Restrict Anonymous Access to Specific Namespaces/Routes (Denylist)

Anonymous users can be restricted from specific namespaces/routes. This acts as
a denylist for specific paths that an anonymous user cannot access. The paths
support regular expressions for matching. The use of the
[Allowlist](#limit-anonymous-access-to-specific-namespacesroutes-allowlist)
takes priority over this denylist. This can be configured in the plugin's
settings or via code:

```php
add_filter(
	'rest_api_guard_anonymous_requests_denylist',
	function ( array $paths, WP_REST_Request $request ): array {
		$paths[] = 'wp/v2/user';
		$paths[] = 'custom-namespace/v1/private/*';

		return $paths;
	},
	10,
	2
);
```

### Require JSON Web Token (JWT) Authentication for Anonymous Users

Anonymous users can be required to authenticate via a JSON Web Token (JWT) to
access the REST API. Users should pass an `Authorization: Bearer <token>` header
with their request. This can be configured in the plugin's settings or via code:

```php
add_filter( 'rest_api_guard_authentication_jwt', fn () => true );
```

Out of the box, the plugin will look for a JWT in the `Authorization: Bearer
<token>` header. The JWT will be expected to have an audience of
'wordpress-rest-api' and issuer of the site's URL. This can be configured in the
plugin's settings or via code:

```php
add_filter( 'rest_api_guard_jwt_audience', fn ( string $audience ) => 'custom-audience' );

add_filter( 'rest_api_guard_jwt_issuer', fn ( string $issuer ) => 'https://example.com' );
```

The JWT's secret will be autogenerated and stored in the
`rest_api_guard_jwt_secret` option. The secret can also be filtered via code:

```php
add_filter( 'rest_api_guard_jwt_secret', fn ( string $secret ) => 'my-custom-secret' );
```

### Allow JWT Authentication for Authenticated Users

Authenticated users can be authenticated with the REST API via a JSON Web Token.
Similar to the anonymous JWT authentication, users should pass an
`Authorization: Bearer <token>` header with their request. This can be
configured in the plugin's settings or via code:

```php
add_filter( 'rest_api_guard_user_authentication_jwt', fn () => true );
```

### Generating JWTs for Anonymous and Authenticated Users

JWTs can be generated by calling the `wp rest-api-guard generate-jwt [--user=<user_id>]`
command or using the `Alley\WP\REST_API_Guard\generate_jwt()` method:

```php
$jwt = \Alley\WP\REST_API_Guard\generate_jwt(
	expiration: 3600, // Optional. The expiration time in seconds from now.
	user: 1, // Optional. The user ID to generate the JWT for. Supports `WP_User` or user ID.
);
```

## Testing

Run `composer test` to run tests against PHPUnit and the PHP code in the plugin.

## Changelog

Please see [CHANGELOG](CHANGELOG.md) for more information on what has changed recently.

## Credits

This project is actively maintained by [Alley
Interactive](https://github.com/alleyinteractive). Like what you see? [Come work
with us](https://alley.co/careers/).

![Alley logo](https://avatars.githubusercontent.com/u/1733454?s=200&v=4)

- [Sean Fisher](https://github.com/srtfisher)
- [All Contributors](../../contributors)

## License

The GNU General Public License (GPL) license. Please see [License File](LICENSE) for more information.
