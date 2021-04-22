# Calendly Online Provider for OAuth 2.0 Client

This package provides Calendly OAuth 2.0 support for the PHP League's [OAuth 2.0 Client](https://github.com/thephpleague/oauth2-client).

## Installation

To install, use composer:

```
composer require sumaerjolly/oauth2-calendly
```

### Usage

Usage is the same as The League's OAuth client, using `\Sumaerjolly\OAuth2\Client\Provider\Calendly` as the provider.

### Authorization Code Flow

```php
$provider = new Sumaerjolly\OAuth2\Client\Provider\Calendly([
    'clientId'          => '{calendly-app-id}',
    'clientSecret'      => '{calendly-app-secret}',
    'redirectUri'       => 'https://example.com/callback-url',
]);

if (!isset($_GET['code'])) {
    $options = [
        'scope' => ['default'] // array or string
    ];

    // If we don't have an authorization code then get one
    $authUrl = $provider->getAuthorizationUrl($options);
    $_SESSION['oauth2state'] = $provider->getState();
    header('Location: '.$authUrl);
    exit;

// Check given state against previously stored one to mitigate CSRF attack
} elseif (empty($_GET['state']) || ($_GET['state'] !== $_SESSION['oauth2state'])) {
    unset($_SESSION['oauth2state']);
    exit('Invalid state');
} else {

    // Try to get an access token (using the authorization code grant)
    $token = $provider->getAccessToken('authorization_code', [
        'code' => $_GET['code']
    ]);

    // Optional: Now you have a token you can look up a users profile data
    try {

        // We got an access token, let's now get the user's details
        $user = $provider->getResourceOwner($token);

        // Use these details to create a new profile
        printf('Hello %s!', $user->getName());

    } catch (Exception $e) {

        // Failed to get user details
        exit('Oh dear...');
    }

    // Use this to interact with an API on the users behalf
    echo $token->getToken();
}
```

### Managing Scopes

When creating your Calendly authorization URL, you can specify the state and scopes your application may authorize.

```php
$options = [
    'state' => 'OPTIONAL_CUSTOM_CONFIGURED_STATE',
    'scope' => ['default'] // array or string
];

$authorizationUrl = $provider->getAuthorizationUrl($options);
```

If neither are defined, the provider will utilize internal defaults.

At the time of authoring this documentation, the [following scopes are available]().

## Testing

```bash
$ ./vendor/bin/phpunit
```

## Credits

- [Nile Suan](https://github.com/nilesuan)
- [Chad Hutchins](https://github.com/chadhutchins)

## License

The MIT License (MIT). Please see [License File](https://github.com/multidimension-al/oauth2-shopify/blob/master/LICENSE) for more information.
