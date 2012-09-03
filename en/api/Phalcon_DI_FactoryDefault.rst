Class **Phalcon\\DI\\FactoryDefault**
=====================================

*extends* :doc:`Phalcon\\DI <Phalcon_DI>`

This is a variant of the standard Phalcon\\DI. By default it automatically registers all the services provided by the framework. Thanks to this, the developer does not need to register each service individually.


Methods
---------

public **__construct** ()

Phalcon\\DI\\FactoryDefault constructor



public **set** (*unknown* $alias, *unknown* $config)

public **remove** (*unknown* $alias)

public **attempt** (*unknown* $alias, *unknown* $config)

public **_factory** (*unknown* $service, *unknown* $parameters)

public **get** (*unknown* $alias, *unknown* $parameters)

public **getShared** (*unknown* $alias, *unknown* $parameters)

public **has** (*unknown* $alias)

public **wasFreshInstance** ()

public **__call** (*unknown* $method, *unknown* $arguments)

public static **setDefault** (*unknown* $dependencyInjector)

public static **getDefault** ()

public static **reset** ()
