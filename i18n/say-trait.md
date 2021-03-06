# TranslatorTrait
You can get access to translation services without requesting Translator dependency but using
`TranslatorTrait` which can work with any of your Components (Controllers, Services, Commands).

## Translator trait and domain routing
Such trait defines method `say` which works as proxy to `trasn` method of translator.
```php
class HomeController extends Controller
{
    use TranslatorTrait;

    public function indexAction()
    {
        echo $this->say('Hello world');
    }
}
```

> Say method can be indexed by 'i18n:index' command.

### Domain routing
Every message generated by such trait will be associated with bundle name based on your class and 
namespace.

For example class `Controlers\HomeController` will get bundle name "controllers-home-controller".
Use translator config to associate this bundle with messages domain:

```php
'domains'          => [
    'controller-messages' => [
        'controllers-home-controller',
        
        //We can also use star syntax
        'controllers-*'
    ],
    
    'spiral'     => [
        'spiral-*',
        'view-spiral-*',
        /*{{domain.spiral}}*/
    ],
    'profiler'   => [
        'view-profiler-*',
        /*{{domain.profiler}}*/
    ],
    'views'      => [
        'view-*',
        /*{{domain.views}}*/
    ],

    //Fallback domain
    'messages'   => ['*'],
],
```

## Class messages
In cases where message is defined by logic and can not be indexed use constants and/or properties
to declare class messages, every string wrapped with `[[]]` will be automatically indexed.

```php
class HomeController extends Controller
{
    use TranslatorTrait;

    const MESSAGES = [
        'error'   => '[[An error]]',
        'success' => '[[Success]]'
    ];

    public function indexAction()
    {
        echo $this->say(self::MESSAGES['error']);
    }
}
```

## Error Messages
Http RequestFilters and other `ValidatesEntity` models support ability to define custom validation
messages with automatic translation:

```php
class SampleRequest extends RequestFilter
{
    const SCHEMA = [
        'name'    => 'data:name',
        'status'  => 'data:status'
    ];

    const SETTERS = [
        'name'    => 'trim',
        'status'  => 'trim'
    ];

    const VALIDATES= [
        'name'    => [
            ['notEmpty', 'error' => '[[Name is required]]'],
        ],
        'status'  => [
            'notEmpty',
            ['in_array', ['active', 'disabled'], 'error' => '[[Invalid status value]]']
        ]
    ];
}
```

> Errors messages will be translated into proper user locale inside `getErrors` method.
