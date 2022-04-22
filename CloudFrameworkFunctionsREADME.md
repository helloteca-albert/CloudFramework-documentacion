# Cloud Framework Functions
En este documento se estudian y detallan las funciones de Cloud Framework que podrás usar en tu desarrollo para ahorrar tiempo y esfuerzo.



## CORS

### *sendCorsHeaders*

``` php
$this->sendCorsHeaders($methods = 'GET,POST,PUT', $origin = '', $allow_extra_headers='');
```

Esta función se encarga de enviar cabeceras en los headers para que no de error de CORS de lado cliente. Esto es debido normalmente a enlaces que se intentan llamar desde servidores distintos. Con esta función deberías tener todo lo que necesitas para evitar el problema.

Los parámetros de la función son:

 1. ***methods**[opcional]*: Indica los métodos que serán permitidos en las llamadas de tipo cors.
 2. ***origin**[opcional]*: Indica los origenes permitidos (Access-Control-Allow-Origin), por defecto permitirá todos los origenes.
 3. ***allow_extra_headers**[opcional]*: Se pueden establecer headers extra que serán permitidos por parte del origen (además de los estrictamente necesarios de Cloud Framework).


## MÉTODOS

### *method*

``` php
$this->method;
```

Esta propiedad devuelve el método actualmente utilizado para llamar a la API (GET, POST, PUT, DELETE, ...).

### *checkMethod*

```php
$this->checkMethod('GET,PUT');
```
Esta funciona comprueba que el método recibido sea uno de los escritos como parámetro de la función.
Importante, intentar siempre escribir separado con comas y sin espacios.

## PÁRAMETROS DE URL/PATH

### *params*

```php
$this->params;
```
Esta propiedad es la que almacena los parámetros de la URL. Se entiende por este parámetro los diferentes slugs o segmentos de la URL más allá del fichero actual. 
Por ejemplo, accediendo a *api.gcf.com/test/ejemplo1/ejemplo2*, el array contedría *['ejemplo1','ejemplo2']*.

### checkMandatoryParam
```php
$this->checkMandatoryParam($parameter_position, ["optional"=>$error_m_essage], ["optional"=>$array_of_words_allowed]);
```

Los parámetros de la función son:

 1. ***parameter_position***: Indica que posición debe existir, es decir si se ha envíado o no el parámetro en la URL. Básicamente es muy útil cuándo se quiere comprobar si esa ruta existe e incluso para determinar una u otra acción según el slug de la URL.
 2. ***error_message**[opcional]*: Indica que mensaje de error quieras que se genere en caso de que no se encuentre el contenido de la url.
 3. ***array_of_words_allowed**[opcional]*: Puede ser tanto un array como un string. en caso de mandar un array será una lista de términos permitidos. En cuánto al string parece ser algo que tiene que ver con los módelos de GCF. Pero no lo acabo de entender. Para mí, debería ser una regex. @CloudFramework, crees que sería buena idea?

## PÁRAMETROS DE FORMULARIO
En este apartado se entiende como parámetros todo el contenido mandando tanto por GET, POST o en el BODY.

### *formParams*

```php
$this->formParams
```
Esta propiedad es la que almacena los parámetros de la petición recibido tanto en el GET como parámetros de la función cómo en el POST sea como parámetros de formulario o en un body de tipo JSON. Lo único relevante a tener en cuenta es que priorizará siempre los valores del BODY en caso de existir que los del GET en la URL. Es muy importante, por lo tanto, evitar duplicar valores en las peticiones y poner diferentes llaves siempre a los parámetros de la URL que a los del BODY. 

### checkMandatoryFormParam
```php
$this->checkMandatoryFormParam
```

## DATASTORE
...

## CFOS
Los CFOs o CloudFramework Objects son objetos creados para gestionar la llamada a base de datos. Al ser una clase del framework, puede ser instanciada desde el core con LoadClass usando una KEU.
``` php
$this->sendCorsHeaders($methods = 'GET,POST,PUT', $origin = '', $allow_extra_headers='');
```

### *initCFOs*
Esta es una implementación para si extiendes de la clase RESTful de GCF para tus propios objetivos.

``` php
class API extends RESTful {
    public function initCFOs($key='') {
        if(!$key)
            $key = $this->core->config->getEnvVar('CLOUDFRAMEWORK_API_KEY');
        //region SET  $this->cfos
        $this->cfos = $this->core->loadClass('CFOs',$key);
        if($this->cfos->error)
            return($this->setErrorFromCodelib('system-error',$this->cfos->errorMsg));
        if(isset($this->formParams['_reload_cache']))
            $this->cfos->resetCache();
        $this->cfos->setNameSpace($this->spacename);
        //endregion
        return true;
    }
}
```

Esta función se encarga de enviar cabeceras en los headers para que no de error de CORS de lado cliente. Esto es debido normalmente a enlaces que se intentan llamar desde servidores distintos. Con esta función deberías tener todo lo que necesitas para evitar el problema.

Los parámetros de la función son:

 1. ***methods**[opcional]*: Indica los métodos que serán permitidos en las llamadas de tipo cors.
 2. ***origin**[opcional]*: Indica los origenes permitidos (Access-Control-Allow-Origin), por defecto permitirá todos los origenes.
 3. ***allow_extra_headers**[opcional]*: Se pueden establecer headers extra que serán permitidos por parte del origen (además de los estrictamente necesarios de Cloud Framework).