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
$this->checkMethod($parameter_list);
```
Esta funciona comprueba que el método recibido sea uno de los escritos como parámetro de la función.

El parámetro de la función es:

 1. ***parameter_list***: String de los métodos en mayúscula, separados por comas y evitando los espacios. Ejemplo, 'GET,POST,PUT'.

## PÁRAMETROS DE URL/PATH

### *params*

```php
$this->params;
```
Esta propiedad es la que almacena los parámetros de la URL. Se entiende por este parámetro los diferentes slugs o segmentos de la URL más allá del fichero actual. 
Por ejemplo, accediendo a *api.gcf.com/test/ejemplo1/ejemplo2*, el array contedría *['ejemplo1','ejemplo2']*.

### checkMandatoryParam
```php
$this->checkMandatoryParam($parameter_position, $error_m_essage=null, $array_of_words_allowed=null);
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
$this->checkMandatoryFormParam($form_param,$error_msg=null);
```
Comprueba si existe el valor indicado en el array de formParams. Básicamente es como hacer un *in_array($formParam, $this->formParams)*. Por lo tanto devolvería un booleano de verdad si existe y falso si no existe.

Los parámetro de la función son:

 1. ***form_param***: Parámetro a comprobar si existe.
 2. ***error_msg***: Mensaje de error en caso de que no lo encuentre.

 ### checkMandatoryFormParams
```php
$this->checkMandatoryFormParams($array);
```
Básicamente comprueba cada uno de los valores de un array del mismo modo que checkMandatoryFormParam.

El parámetro de la función es:

 1. ***array***: Array de parámetro para validar si se han recibido o no valores para ellos.

## DATASTORE
...

## CFOS
Los CFOs o CloudFramework Objects son objetos creados para gestionar la llamada a base de datos. Al ser una clase del framework, puede ser instanciada desde el core con LoadClass usando una KEY.
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

### *fetchOneBykey*


```php
if(!$this->initCFOs()) return; //Necesitamos iniciar el objeto

$this->cfos->ds('Users')->limit = 2; //Establecemos el límite de registros
$data = $this->cfos->ds('Users')->fetchOneByKey(4511020568543232); //Buscamos por una key específica
if($this->cfos->ds('Users')->error) return($this->setErrorFromCodelib('system-error',$this->cfos->ds('Users')->errorMsg));
$this->addReturnData([$data]);
```

## Errores
CloudFramework, cómo otros frameworks, ofrece un sistema para gestionar rápidamente los errores y añadirlos en la respuesta del programa.

### *setErrorFromCodelib*
```php
$this->setError($error, $return_status = 400, $return_code=null, $message='');
```
Esta función permite añadir un error a la respuesta además de decidir el código HTTP de la respuesta con tal de que con una sola función puedas declarar el error. Esta función NO finaliza con el flujo del código. Por lo tanto, puede comprobarse al mismo tiempo otros errores antes de concluir. No obstante, debes evitar que una vez detectado el error prosiga todo el hilo de ejecución para evitar problemas.

Los parámetros de la función son:
1. ***error***: String con el mensaje de error que será añadido en el cuerpo de la respuesta.
2. ***return_status**[opcional]*: Es un integer que contiene el código de respuesta de la API al cliente. Por predeterminado será 400 que es un código de error.
3. ***return_code**[opcional]*: También código de error que se devuelve por respuesta.
4. ***message**[opciona]*: Mensaje adicional.

### *setErrorFromCodelib*
```php
$this->setErrorFromCodelib($code,$extramsg='');
```
Esta funcion es una mejorar de setError. Es una simplificación en la que se envía tipos de error basados en la librería de error de CloudFramework y un mensaje de error. Internamente, el código ya se encarga de añadir el contenido en el cuerpo de error. Importante, esta función no rompe con el hilo de ejecución.

Los parámetros de la función son:

 1. ***code***: Este valor debe coincidir con el de un string de error. Algunos de los códigos de error son:
    - **'system-error'**
    - **'params-error'**
    - **'security-error'**
    - **'not-allowed'**
    - **'server-error'**
