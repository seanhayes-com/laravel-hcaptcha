# hCAPTCHA PHP client library

hCAPTCHA is a free CAPTCHA service that protects websites from spam and abuse.
This is a PHP library that wraps up the server-side verification step required
to process responses from the hCAPTCHA service. 

- hCAPTCHA: https://www.hcaptcha.com/
- This repo: https://github.com/seanhayes-com/laravel-hcaptcha
- Version: 1.2.4
- License: BSD, see [LICENSE](LICENSE)

## Installation

### Composer (recommended)

Run the following command from your project directory to add the dependency:

``` bash
composer require seanhayes-com/laravel-hcaptcha "^1.2"
```

Alternatively, add the dependency directly to your `composer.json` file:

```json
"require": {
    "seanhayes-com/laravel-hcaptcha": "^1.2"
}
```

### Direct download

Download the [ZIP file](https://github.com/seanhayes-com/laravel-hcaptcha/archive/master.zip)
and extract into your project. An autoloader script is provided in
`src/autoload.php` which you can require into your script. For example:

```php
require_once '/path/to/hcaptcha/src/autoload.php';
$hcaptcha = new \HCaptcha\HCaptcha($secret);
```

The classes in the project are structured according to the
[PSR-4](http://www.php-fig.org/psr/psr-4/) standard, so you can also use your
own autoloader or require the needed files directly in your code.

## Usage

First obtain the appropriate keys for the type of hCAPTCHA you wish to
integrate at https://www.hcaptcha.com/

Then follow the [integration guide on the developer
site](https://docs.hcaptcha.com/) to add the hCAPTCHA
functionality into your frontend.

This library comes in when you need to verify the user's response. On the PHP
side you need the response from the hCAPTCHA service and secret key from your
credentials. Instantiate the `HCaptcha` class with your secret key, specify any
additional validation rules, and then call `verify()` with the hCAPTCHA
response and user's IP address. For example:

```php
<?php
$hcaptcha = new \HCaptcha\HCaptcha($secret);
$resp = $hcaptcha->setExpectedHostname('your-domain.com.here')
                  ->verify($HcaptchaResponse, $remoteIp);
if ($resp->isSuccess()) {
    // Verified!
} else {
    $errors = $resp->getErrorCodes();
}
```

The following methods are available:

- `setExpectedHostname($hostname)`: ensures the hostname matches. You must do
  this if you have disabled "Domain/Package Name Validation" for your
  credentials.
- `setExpectedApkPackageName($apkPackageName)`: if you're verifying a response
  from an Android app. Again, you must do this if you have disabled
  "Domain/Package Name Validation" for your credentials.
- `setExpectedAction($action)`: ensures the action matches for the v3 API.
- `setScoreThreshold($threshold)`: set a score threshold for responses from the
  v3 API
- `setChallengeTimeout($timeoutSeconds)`: set a timeout between the user passing
  the hCAPTCHA and your server processing it.

Each of the `set`\*`()` methods return the `HCaptcha` instance so you can chain
them together. For example:

```php
<?php
$hcaptcha = new \HCaptcha\HCaptcha($secret);
$resp = $hcaptcha->setExpectedHostname('your-domain.com.here')
                  ->setExpectedAction('homepage')
                  ->setScoreThreshold(0.5)
                  ->verify($HcaptchaResponse, $remoteIp);

if ($resp->isSuccess()) {
    // Verified!
} else {
    $errors = $resp->getErrorCodes();
}
```

You can find the constants for the libraries error codes in the `HCaptcha`
class constants, e.g. `HCaptcha::E_HOSTNAME_MISMATCH`
