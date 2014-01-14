#Backbeard

Backbeard is yet another DSLish minimum oriented framework for PHP.

![backbeard](http://gyazo.com/44a5c43a817927032d6f5ff0ed8cda74.png)


```php
<?php
use Backbeard\Dispatcher;
use Backbeard\ValidationError;
use Zend\Http\PhpEnvironment\Request;

$routing = call_user_func(function () {
    yield '/hello/:foo' => function ($foo) {
        return "Hello $foo";
    };

    $error = (yield ['method' => 'POST', 'route' => '/entry/:id'] => function ($id) {
        if ($this->get('request')->getPost('NAME') == 'wtf') {
            return ['var1' => 'baz']; // will be render entry.mustache
        } else {
            return new ValidationError(['error']);
        }
    });

    yield '/entry/:id' => function ($id) use ($error) {
        $message = $error ? current($error->getMessages()) :'';
        return "Hello $id ".$message.
        '<form method="POST" action="/entry/'.$id.'">'.
            'NAME<input type="text" name="NAME">'.
        '</form>';
    };

    yield function(Request $request) {
        return $request->getRequestUri() === '/';
    } => function () {
        $response = $this->get('response');
        $response->setContent("Hello");
        return $response;
    };
});

(new Dispatcher($routing))->dispatch(new Request)->send();
```

##Install
install application skeleton via composer

 - `php composer.phar create-project -s dev sasezaki/backbeard-skeleton path/to/install`

https://github.com/sasezaki/BackbeardSkeleton/

##Run
with php built-in web server 
 - `php -S localhost:8080 -t public/ public/index.php`

##UnitTest
`phpunit -c tests/phpunit.xml tests`
