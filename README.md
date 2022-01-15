# Tural/Epoint library/SDK

The library provides convenient access to the [Epoint API](https://epoint.az) from applications written in the PHP language. It includes a pre-defined set of static class functions for API resources that initialize themselves dynamically from Tural/Epoint class
which makes it easier without writing repetitive configuration.

## Requirements

PHP 7 and later

## Installation

```bash
composer require tural/epoint
```

To include use Composer's [autoload](https://getcomposer.org/doc/01-basic-usage.md#autoloading):

```php
require_once('vendor/autoload.php');
```

## Dependencies

[Guzzle](https://github.com/php-http/guzzle6-adapter)

## Getting started

### Payment by typing card number

```php
$response = Epoint::typeCard(PRIVATE_KEY, PUBLIC_KEY, "your_order_id", '0.01', 'Test payment');

if ($response->status == 'success') {

    // if successful it redirects to bank's payment page,
    // when user pays successfully, epoint will call the success webhook you provided
    $this->flashSuccess()->redirect($response->redirect_url);
    
} else {

    // handle failure
    
}
```

### Cancel payment

```php
$response = Epoint::cancel("write epoint transaction id");

if ($response->status == 'success') {

    // handle success
    
} else {

    // handle failure
    
}
```

### Check payment

```php
// if false, it will check by order_id, if true,
// it will check by epoint transaction id
$epoint_transaction = false;

$response = Epoint::checkPayment("order or transaction id", $epoint_transaction);

// handle response
dd($response);
```

### Save card for payment

```php
$response = Epoint::saveCardForPayment("Kartı yadda saxla");

if ($response->status == 'success') {

    // save it to db
    $card_uid=$response->card_id;

    $this->flashSuccess()->redirect($response->redirect_url);
    
} else {

    // handle failure
    
}
```

### Save card for refund

```php
$response = Epoint::saveCardForRefund("Kartı yadda saxla");

if ($response->status == 'success') {

    // save it to db
    $card_uid = $response->card_id;
    $this->flashSuccess()->redirect($response->redirect_url);
    
} else {

    // handle failure
    
}
```

### Pay with saved card

```php
// if successful it returns success
$response = Epoint::payWithSaved("card_uid", 'order_id', 'amount for ex. 0.01', 'write description');

if ($response->status == 'success') {

    $epoint_transaction = $response->transaction;
    $bank_transaction = $response->bank_transaction;
    
} else {

    // handle failure
    
}
```

### Refund action

```php
$response = Epoint::refund("card_uid", 'order_id', 'amount 0.05', 'write description');

if ($response->status == 'success') {

    // handle success
    $this->redirect($response->redirect_url, true);
    
} else {

    // handle failure
    
}
```

### Webhook called by Epoint on success or failure

```php

$data = $this->req('data');

if (!empty($data)) {

    $epoint = new Epoint([
        "data" => $_POST["data"], 
        "signature" => $_POST["signature"]
    ]);

    // verify the response is really coming from epoint
    if ($epoint->isSignatureValid()) {

        $json_string = $epoint->getDataAsJson();
        $json = $epoint->getDataAsObject();

        // handle them for your needs
        $json->order_id ?? null,
        $json->status ?? null, 
        $json->code ?? null, 
        $json->transaction ?? null, 
        $json->bank_transaction ?? null, 
        $json->card_id ?? null, 
        $json->card_name ?? null, 
        $json->card_mask ?? null, 
        $json->operation_code ?? null, 
        
        // json as string
        $json_string ?? null;

        // if payment is successful
        if ($json->status == 'success') {

            // if card_id is set it means it is save_card response
            if (!empty($json->card_id)) {

                // handle

            }
        }

    }
}
```

All the parameters of the class are inline-documented inside class.

You can have a look at Api documentation in Azerbaijani [here](https://github.com/tural-esger/epoint/blob/master/Epoint%20API%20Documentation%20az.pdf), or [download](https://github.com/tural-esger/epoint/raw/master/Epoint%20API%20Documentation%20az.pdf).
