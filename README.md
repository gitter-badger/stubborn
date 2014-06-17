## Stubborn

###### Configurable call handler that is persistent against failures.

Frustrated by unreliable API's that seem to fail randomly?
Use Stubborn to ensure you aren't left unprotected in the most vulnerable and unknown spots in your code.

Stubborn provides all the necessary tooling you need to handle a variety of external API calls:

-Result Handling
-Retry Handling(immediate/delayed)
-Exception Catching
-Backoff Handling

Basic example:

```php

$result = Stubborn::build()
    // Use the Stubborn Result Handler to drive your call retries
    ->resultHandler(
        function ($stubborn, $result) {
            if ($result == 'Success') {
                $stubborn->accept();
            } elseif ($result == 'Backoff_Needed') {
                $stubborn->backoff();
            } elseif ($result == 'Not_Yet_Persisted') {
                $stubborn->delayRetry();
            } elseif ($result == 'Hard_Failure') {
                $stubborn->fail();
            }
            $stubborn->retry();
        }
    // Backoff With Our Predefined Helper, or your own implementation
    // Performs a static 5 second backoff, leave constructor blank for an
    // attempt based exponential backoff
    )->backoffHandler(new DefaultBackoffHandler(5))
    // Retry if defined exceptions are thrown by your function
    ->catchExceptions(array('\Awesome_API\UnexpectedError'))
    ->retries(4)
    ->run(function() use ($id) {
        return Awesome_API::add_subscriber($id); 
    });
```
