## Gateway for Laravel 4

Gateway is payment gateway adapters that support Paypal, Paysbuy, Bangkok Bank, Kasikorn Bank, True Money.

### Installation

- [Gateway on Packagist](https://packagist.org/packages/teepluss/gateway)
- [Gateway on GitHub](https://github.com/teepluss/laravel4-gateway)

To get the lastest version of Gateway simply require it in your `composer.json` file.

~~~
"teepluss/gateway": "dev-master"
~~~

You'll then need to run `composer install` to download it and have the autoloader updated.

Once Theme is installed you need to register the service provider with the application. Open up `app/config/app.php` and find the `providers` key.

~~~
'providers' => array(

    'Teepluss\Gateway\GatewayServiceProvider'

)
~~~

Gateway also ships with a facade which provides the static syntax for creating collections. You can register the facade in the `aliases` key of your `app/config/app.php` file.

~~~
'aliases' => array(

    'Gateway' => 'Teepluss\Gateway\Facades\Gateway'

)
~~~

## Usage

Generate payment form.
~~~php
$adapter = Gateway::driver('Paypal');

$adapter->setSandboxMode(true);

$adapter->setSuccessUrl('http://www.domain/foreground/success')
        ->setCancelUrl('http://www.domain/foreground/cancel')
        ->setBackendUrl('http://www.domain/background/invoice/00001');

$adapter->setMerchantAccount('demo@gmail.com');

$adapter->setLanguage('TH')
        ->setCurrency('THB');

$adapter->setInvoice(00001)
        ->setPurpose('Buy a beer.')
        ->setAmount(100);

$adapter->setRemark('Short note');

$generated = $adapter->render();

var_dump($generated);
~~~

You can use intialize also.
~~~php
$adapter = Gateway::driver('Paypal')->initialize(array(
    'sandboxMode'     => true,
    'successUrl'      => 'http://www.domain/foreground/success',
    'cancelUrl'       => 'http://www.domain/foreground/cancel',
    'backendUrl'      => 'http://www.domain/background/invoice/00001',
    'merchantAccount' => 'seller@domain.to',
    'language'        => 'TH',
    'currency'        => 'THB',
    'invoice'         => uniqid(),
    'purpose'         => 'Buy a beer.',
    'amount'          => 100,
    'remark'          => 'Short note'
));

$generated = $adapter->render();

var_dump($generated);
~~~

How to set TrueMoneyApi and PaysbuyApi
~~~php
// True Money
$gateway = Gateway::driver('TrueMoneyApi');
$gateway->setMerchantAccount('appId:shopCode:secret:bearer');

// Paysbuy
$gateway = Gateway::driver('PaysbuyApi');
$gateway->setMerchantAccount('merchantId:username:secureCode');

// Paysbuy having non-apu version.
$gateway = Gateway::driver('Paysbuy');
$gateway->setMerchantAccount('email');
~~~

Checking foregound process.
~~~php
$adapter = Gateway::driver('Paypal');

$adapter->setSandboxMode(true);

$adapter->setMerchantAccount('seller@domain.to');

$adapter->setInvoice(00001);

$result = $adapter->getFrontendResult();

var_dump($result);
~~~

Checking background process (IPN).
~~~php
$adapter = Gateway::driver('Paypal');

$adapter->setSandboxMode(true);

$adapter->setMerchantAccount('demo@gmail.com');

$adapter->setInvoice(00001);

$result = $adapter->getBackendResult();

var_dump($result);
~~~

Extending the core.
~~~php

use Teepluss\Gateway\Drivers\DriverAbstract;
use Teepluss\Gateway\Drivers\DriverInterface;

class Strip extends DriverAbstract imlements DriverInterface {
    //....
}

use Teepluss\Gateway\Repository;

Gateway::extend('Stripe', function()
{
    return new Repository(new Strip);
});
~~~

TrueMoneyApi adapter need more addition required data to make it works!

~~~php
$gateway = Gateway::driver('TrueMoneyApi');

$gateway->setMerchantAccount('appId:shopCode:secret:bearer');

$gateway->setSandboxMode(true);
$gateway->setSuccessUrl(URL::to('demo/thankyou'))
        ->setCancelUrl(URL::to('demo/thankyou'))
        ->setBackendUrl(URL::to('demo/background'));

$gateway->setLanguage('TH')
        ->setCurrency('THB');

$gateway->setInvoice(uniqid())
        ->setPurpose('sale');

$gateway->payer(array(
    'installment'       => null,
    'fundingInstrument' => null,
    'payerInfo' => array(
        'email'     => 'teepluss@gmail.com',
        'firstName' => 'Tee',
        'lastName'  => 'Pluss',
        'payerId'   => '11',
        'phone'     => '0867767779'
    ),
    'paymentMethod' => 'creditcard'
));

$gateway->address(array(
    'cityDistrict'  => 'Patumwan',
    'companyName'   => 'eCommerce Solution',
    'companyTaxId'  => '3334567',
    'country'       => 'Thailand',
    'email'         => 'me@email.com',
    'forename'      => 'Tee',
    'line1'         => 'Ratchadapisak Rd.',
    'line2'         => 'OX',
    'phone'         => '0888773390',
    'postalCode'    => '10310',
    'stateProvince' => 'Bangkok',
    'surname'       => 'Pluss',
));

$gateway->payment(array(
    'ref1' => 1,
    'ref2' => 2,
    'ref3' => 3
));

$gateway->product()->add(array(
    'shopCode'  => null,
    'itemId'    => 1,
    'service'   => 'bill',
    'productId' => 'p1',
    'detail'    => 'd1',
    'price'     => 5000,
    'ref1'      => '1',
    'ref2'      => '2',
    'ref3'      => '3',
));

$gateway->product()->add(array(
    'shopCode'  => null,
    'itemId'    => 2,
    'service'   => 'bill',
    'productId' => 'p1',
    'detail'    => 'd1',
    'price'     => 300,
    'ref1'      => '1',
    'ref2'      => '2',
    'ref3'      => '3',
));

echo $gateway->includeSubmitButton()->render();
~~~

## Support or Contact

If you have some problem, Contact teepluss@gmail.com


[![Support via PayPal](https://rawgithub.com/chris---/Donation-Badges/master/paypal.jpeg)](https://www.paypal.com/cgi-bin/webscr?cmd=_s-xclick&hosted_button_id=9GEC8J7FAG6JA)
