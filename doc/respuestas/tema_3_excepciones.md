<!--
Posible prompt:
<prompt>
Tengo un cuestionario con preguntas sobre "Excepciones". Debes tener en cuenta que los conocimientos previos que tengo (y por tanto tus respuestas deben ser adaptadas), son:
- C/C++ sin orientación a objetos.
- Temas de Java previos: Clases y Objetos, Encapsulación.

Cada respuesta debe tener entre 2 - 4 párrafos de longitud (sin contar los trozos de código).

Por favor, escribe en impersonal las respuestas.

</prompt>
----
-->

# TEMA 3. Excepciones

## 1. Empecemos un tema sobre control de errores en lenguajes de programación, con algo básico. En C, donde no existen las excepciones, pongamos un ejemplo de una raíz que toma número flotante positivo. Queremos controlar el error si la función recibe un número negativo. El usuario debe ser informado pero desde fuera de la función `raiz` ¿Cómo indicamos ese error?. Enumera dos opciones diferentes de diseñar, poniendo un ejemplo de código de cada una.

### Respuesta

En C, como no existe un mecanismo automático de excepciones, el error debe comunicarse de forma explícita. Una posibilidad muy habitual consiste en devolver un valor especial que indique “ha ocurrido un error”, y dejar que el código llamador lo compruebe. En este ejemplo podría devolverse `-1.0f`, suponiendo que la raíz cuadrada válida de un número real nunca será negativa en este dominio. Después, desde fuera de `raiz`, se detecta ese valor y se informa al usuario.

El problema de este diseño es que mezcla el resultado correcto con la señal de error. Funciona en ejemplos simples, pero puede ser ambiguo en otros casos: a veces no existe un valor especial claro, o puede coincidir con un resultado válido. Aun así, es una técnica muy usada en C por su simplicidad.

```c
#include <stdio.h> 
#include <math.h>

float raiz(float x) { 
    if (x < 0) { 
        return -1.0f; // valor especial para indicar error 
    } 
    return sqrtf(x); 
}

int main() { 
    float n = -9.0f; 
    float r = raiz(n);
    if (r == -1.0f) {
        printf("Error: no se puede calcular la raiz de un numero negativo.\n");
    } else {
        printf("Resultado: %.2f\n", r);
    }
    return 0;
}
```

Otra opción consiste en separar el “resultado” del “estado de error”. Por ejemplo, la función puede devolver un código de éxito o fracaso (como un entero booleano), y escribir el resultado real en una variable pasada por referencia mediante puntero. Este diseño suele ser más limpio, porque ya no se confunde el valor calculado con la señal de error.

Esta segunda alternativa es muy frecuente en C profesional, porque obliga a distinguir claramente entre “la operación salió bien o mal” y “cuál es el dato obtenido”. Además, permite añadir más códigos de error en el futuro, por ejemplo uno para argumento inválido y otro para desbordamiento.

```c
#include <stdio.h> 
#include <math.h>

int raiz(float x, float *resultado) { 
    if (x < 0) { 
        return 0; // 0 = error 
    } 
    *resultado = sqrtf(x); 
    return 1; // 1 = éxito 
}

int main() { 
    float n = -9.0f; 
    float r;
    if (!raiz(n, &r)) {
        printf("Error: no se puede calcular la raiz de un numero negativo.\n");
    } else {
        printf("Resultado: %.2f\n", r);
    }
    return 0;
}
```


## 2. Brevemente ¿Qué es una **"excepción"**? ¿Con qué objetivo las usa un programador cuando implementa funciones o cuando las llama?

### Respuesta

Una excepción es un mecanismo del lenguaje para representar que durante la ejecución ha ocurrido una situación anómala o errónea que impide seguir con el flujo normal. En lugar de devolver manualmente un código de error en cada función, el lenguaje permite “lanzar” un objeto especial que viaja hacia fuera hasta que alguien lo controle. Es, por tanto, una forma estructurada de comunicar fallos.

Al implementar funciones, las excepciones se usan para señalar problemas que la propia función detecta y no puede resolver localmente, como recibir un argumento inválido o no poder abrir un fichero. Al llamar a funciones, se usan para separar el código normal del código de tratamiento de errores, de manera que el programa principal quede más claro: por un lado se intenta hacer la operación, y por otro se decide qué hacer si falla.


## 3. Reescribe el mismo ejemplo de raiz, pero en Java, metiendo ese método en una clase `Calculadora` y llama a dicho método desde el método `main`, mostrando cómo se puede controlar desde fuera.

### Respuesta

En Java, el método puede indicar el error lanzando una excepción en lugar de devolver un valor especial. Eso permite que el método `raiz` se concentre en su trabajo: calcular la raíz si el dato es válido, y avisar de forma clara si no lo es. Después, desde `main`, se captura esa excepción con un bloque `try-catch`.

Esto mejora bastante la claridad respecto al estilo típico de C. El resultado correcto sigue siendo un `double`, y el error ya no se mezcla con el valor de retorno. Además, quien llama decide cómo reaccionar: mostrar un mensaje, pedir otro número o terminar el programa.

```java
public class Calculadora {
    public static double raiz(double x) {
        if (x < 0) {
            throw new IllegalArgumentException(
                "No se puede calcular la raiz de un numero negativo"
            );
        }
        return Math.sqrt(x);
    }

    public static void main(String[] args) {
        double n = -9.0;
        try {
            double r = raiz(n);
            System.out.println("Resultado: " + r);
        } catch (IllegalArgumentException e) {
            System.out.println("Error: " + e.getMessage());
        }
    }
}
```


## 4. ¿Qué es **"lanzar"** una excepción? ¿Qué es **"controlar"** o **"capturar"** una excepción? ¿Qué es que se **"propague"** una excepción? ¿Qué le va ocurriendo a las funciones en la pila de llamadas por donde se va propagando la excepción? ¿Las funciones que no la controlan se reanudan después de alguna forma? Explica con el mismo ejemplo anterior en Java de la raíz cuadrada.

### Respuesta

Lanzar una excepción significa indicar que ha ocurrido un error mediante la instrucción `throw`. En el ejemplo de la raíz cuadrada, si `x < 0`, el método `raiz` no devuelve un resultado normal, sino que lanza una `IllegalArgumentException`. A partir de ese momento, la ejecución normal de ese método se interrumpe inmediatamente.

Capturar o controlar una excepción significa colocar un `try-catch` en algún punto del código para interceptarla y decidir qué hacer con ella. Si `main` llama a `raiz(-9)`, la excepción sube hasta el `catch`, donde puede mostrarse el mensaje de error. Si entre `raiz` y `main` hubiese otros métodos intermedios y ninguno la capturase, la excepción seguiría subiendo: eso es propagarse.

Mientras la excepción se propaga, las funciones por las que pasa van terminando de forma abrupta. No continúan “por donde iban”; su ejecución queda cancelada y se van sacando de la pila de llamadas hasta encontrar un manejador adecuado. Las funciones que no capturan la excepción no se reanudan después: se abandonan por completo. Solo se continúa desde el `catch` que finalmente la maneja, o el programa termina si nadie la captura.

```java
public class Calculadora {
    public static double raiz(double x) {
        if (x < 0) {
            throw new IllegalArgumentException("Numero negativo");
        }
        return Math.sqrt(x);
    }

    public static void calcularYMostrar(double n) {
        double r = raiz(n); // si hay excepción, este método termina aquí
        System.out.println("Resultado: " + r);
    }

    public static void main(String[] args) {
        try {
            calcularYMostrar(-9);
            System.out.println("Este mensaje no se ejecuta");
        } catch (IllegalArgumentException e) {
            System.out.println("Error controlado en main: " + e.getMessage());
        }
    }
}
```


## 5. ¿Qué ventajas tiene frente a C, la **"propagación natural"** de las excepciones a través de la pila (_stack_) de llamadas?

### Respuesta

La principal ventaja es que no obliga a comprobar manualmente un código de error en cada llamada. En C, si una función llama a otra, y esta a otra, cada nivel debe acordarse de revisar el valor devuelto y reenviar el error hacia arriba. Eso genera mucho código repetitivo, fácil de olvidar y bastante propenso a fallos. Con excepciones, el error sube automáticamente por la pila hasta que alguien decida manejarlo.

Otra ventaja importante es la separación entre el flujo normal y el flujo de error. El código queda más limpio: las funciones pueden devolver resultados normales cuando todo va bien, y usar excepciones solo cuando algo falla. Además, esa propagación conserva información del error mientras atraviesa varios métodos, sin necesidad de diseñar manualmente convenios de retorno como en C.


## 6. En orientación a objetos, ¿las excepciones suelen ser objetos? ¿Qué ventajas tiene esto en términos de encapsulación? ¿Podemos entonces crear excepciones personalizadas?

### Respuesta

Sí. En Java, una excepción es un objeto. Eso encaja muy bien con la orientación a objetos, porque permite representar el error como una entidad con estado y comportamiento propios. No se trata solo de “un número de error” o “una cadena”, sino de una instancia de una clase que puede transportar información relevante sobre lo ocurrido.

Desde el punto de vista de la encapsulación, esto permite agrupar dentro del propio objeto excepción todo lo necesario para describir el problema: mensaje, causa original y cualquier otro dato útil. Así, quien lanza la excepción no necesita exponer detalles internos de implementación mediante variables sueltas o códigos mágicos. Además, sí: pueden crearse excepciones personalizadas heredando de `Exception` o de `RuntimeException`, lo que permite modelar errores del dominio del programa con más claridad.

```java
public class EdadInvalidaException extends Exception { 
    public EdadInvalidaException(String mensaje) {
        super(mensaje); 
    } 
}
```


## 7. En relación con las ventajas de la encapsulación, comparando el ejemplo en C con Java. ¿Qué **información esencial** lleva cualquier **objeto excepción** que es muy útil tener cuando se llega a un manejador?

### Respuesta

La información esencial que lleva cualquier objeto excepción es, como mínimo, el tipo concreto de error y un mensaje descriptivo. El tipo permite distinguir qué clase de problema ha ocurrido, por ejemplo si se trata de un argumento inválido, un problema de entrada/salida o un error aritmético. El mensaje permite entender mejor el motivo exacto cuando se llega al manejador.

Además, en Java suele conservarse también la traza de pila, es decir, el recorrido de llamadas por donde pasó la ejecución hasta el punto en que se lanzó la excepción. Esa información es extremadamente útil para depurar, porque permite saber no solo que falló una raíz cuadrada, sino también en qué método y desde qué cadena de llamadas se llegó allí. Frente a C, esto da un contexto mucho más rico que un simple -1 o un 0.


## 8. En Java, sobre el bloque **"try-catch"**, ¿se pueden tener más de un bloque `catch`? ¿cuántos bloques `catch` se ejecutan?

### Respuesta

Sí, en Java se pueden tener varios bloques `catch` detrás de un mismo `try`. Esto se hace cuando dentro del bloque protegido pueden producirse distintos tipos de excepción y se quiere reaccionar de forma diferente según cuál haya ocurrido. Cada `catch` se asocia a un tipo concreto de excepción.

Sin embargo, solo se ejecuta un único `catch`: el primero que resulte compatible con la excepción lanzada. Una vez que una excepción entra en un `catch`, no se siguen probando los demás. Por eso el orden importa: primero deben ponerse los `catch` más específicos y después los más generales.


## 9. Si las excepciones producen rupturas en el código llamador, ¿cómo podemos garantizar que se ejecuta siempre finalmente un código necesario para cierre de ficheros, liberacion de recursos, antes de que continúe propagándose la excepción? Pon un ejemplo en Java con `finally`, tanto con `catch` como sin él.

### Respuesta

Para garantizar que cierto código se ejecute siempre, haya o no excepción, se usa el bloque `finally`. Su función típica es liberar recursos: cerrar ficheros, conexiones o cualquier elemento que deba dejarse en un estado correcto. Esto es necesario porque una excepción puede interrumpir el flujo normal antes de que se alcance manualmente el cierre.

El `finally` se ejecuta tanto si la excepción se captura en ese mismo sitio como si no se captura y sigue propagándose. Por eso resulta tan útil: asegura la limpieza antes de continuar. Puede aparecer junto con `catch`, o incluso sin `catch`, cuando se quiere dejar que el error suba pero sin olvidar la liberación del recurso.

Ejemplo con `catch` y `finally`:

```java
import java.io.BufferedReader; 
import java.io.FileReader; 
import java.io.IOException;

public class Ejemplo1 { 
    public static void main(String[] args) { 
        BufferedReader br = null;
        try {
            br = new BufferedReader(new FileReader("datos.txt"));
            System.out.println(br.readLine());
        } catch (IOException e) {
            System.out.println("Error al leer el fichero: " + e.getMessage());
        } finally {
            System.out.println("Se ejecuta finally");
            if (br != null) {
                try {
                    br.close();
                } catch (IOException e) {
                    System.out.println("Error al cerrar el fichero");
                }
            }
        }
    }
}
```

Ejemplo sin `catch`, pero con `finally`:

```java
import java.io.BufferedReader; 
import java.io.FileReader; 
import java.io.IOException;

public class Ejemplo2 {
    public static void leerFichero() throws IOException {
        BufferedReader br = null;
        try {
            br = new BufferedReader(new FileReader("datos.txt"));
            System.out.println(br.readLine());
        } finally {
            System.out.println("Se ejecuta finally");
            if (br != null) {
                br.close();
            }
        }
    }
    
    public static void main(String[] args) {
        try {
            leerFichero();
        } catch (IOException e) {
            System.out.println("Excepción propagada hasta main");
        }
    }
}
```


## 10. En Java, el bloque `finally` puede ir sin `catch`? ¿Se ejecuta siempre tanto si ocurre como si no ocurre una excepción? ¿Y si hay un `return` en medio del `try`?

### Respuesta

Sí, `finally` puede ir sin `catch`, siempre que vaya acompañado de `try`. En ese caso, el bloque `try` protege el código y `finally` garantiza la ejecución del bloque de limpieza, mientras que cualquier excepción que se produzca seguirá propagándose hacia arriba. Es una construcción válida y bastante útil.

En condiciones normales, `finally` se ejecuta siempre: tanto si no ocurre ninguna excepción, como si ocurre una y se captura, o incluso si ocurre y se propaga. También se ejecuta aunque dentro del `try` haya un `return`. Lo que sucede es que antes de abandonar definitivamente el método, Java ejecuta el `finally`. Solo situaciones muy anómalas, como terminar brutalmente la máquina virtual con `System.exit(0)`, pueden impedirlo.

```java
public class PruebaFinally { 
    public static int ejemplo() { 
        try { 
            System.out.println("Dentro de try"); 
            return 5; 
        } finally { 
            System.out.println("Dentro de finally"); 
        } 
    }
    
    public static void main(String[] args) {
        int x = ejemplo();
        System.out.println("Valor devuelto: " + x);
    }
}
```


## 11. En Java, qué son las excepciones **"controladas"** y las **"no controladas"**? ¿Qué papel juega `RuntimeException`? Pon un ejemplo de excepciones típicas controladas y no controladas que incluso nosotros mismos podríamos usar. Haz dos listas con 3 o 4 ejemplos de situación donde se suele preferir una excepción controlada y donde se suele preferir una excepción no controlada.

### Respuesta

Las excepciones controladas (checked exceptions) son aquellas que el compilador obliga a tratar, bien capturándolas con `try-catch`, bien declarándolas en la firma del método mediante `throws`. Suelen representar errores previsibles del entorno que están fuera del control directo del programador, como problemas al leer un fichero o al acceder a un recurso de red.

Las excepciones no controladas (unchecked exceptions) no obligan al compilador a realizar ningún chequeo explícito; normalmente representan fallos de programación o bugs (como intentar dividir por cero o desreferenciar un puntero nulo). La clase base `RuntimeException` juega un papel clave aquí: todas las excepciones que heredan de ella o de `Error` son consideradas excepciones no controladas.

```java
// Excepción controlada personalizada 
class FicheroConfiguracionException extends Exception { 
    public FicheroConfiguracionException(String mensaje) { 
        super(mensaje); 
    } 
}

// Excepción no controlada personalizada 
class DatoInvalidoException extends RuntimeException { 
    public DatoInvalidoException(String mensaje) { 
        super(mensaje); 
    } 
}
```

Situaciones donde se suele preferir una excepción controlada:
* Al abrir o leer un archivo en el disco que puede no existir o no tener permisos de lectura (`IOException`).
* Al conectarse a una base de datos o realizar una consulta a través de la red que pueda fallar por problemas de conexión (`SQLException`).
* Al analizar o parsear un formato de archivo externo (como JSON o XML) cuyo contenido pueda estar mal formado.
* Al obligar contractualmente al llamador a decidir cómo reaccionar ante un error recuperable y previsto de la lógica de negocio.

Situaciones donde se suele preferir una excepción no controlada:
* Al recibir parámetros inválidos en un método o constructor, indicando un mal uso de la interfaz (`IllegalArgumentException`).
* Al intentar acceder a una posición fuera de los límites de un array o de una lista (`IndexOutOfBoundsException`).
* Al invocar un método sobre un objeto que no está en el estado adecuado para realizar esa acción (`IllegalStateException`).
* Al intentar usar una referencia nula (`NullPointerException`), lo cual representa inequívocamente un bug en el código.


## 12. ¿Qué es y para qué se usa `throws`? ¿Por qué es alternativa a capturar una excepción controlada?

### Respuesta

La palabra clave `throws` se utiliza en la declaración o firma de un método para indicar que dicho método puede lanzar o propagar una o varias excepciones específicas. Avisa tanto al compilador como a otros desarrolladores de que el método no va a manejar localmente ese tipo de error, delegando esa responsabilidad a quien decida llamarlo.

Es una alternativa a capturar la excepción controlada porque, en lugar de resolver el problema mediante un bloque `try-catch` dentro del propio método, se permite que la excepción suba por la pila de llamadas. De este modo, el programador puede posponer la decisión de manejo del error hasta una capa superior del programa que tenga más contexto sobre cómo reaccionar (por ejemplo, mostrando un diálogo en la interfaz de usuario en vez de escribir un log silencioso en segundo plano).


## 13. Pon un ejemplo en Java de firma de método que incluya `throws`, de una función que abre un fichero pero que declara que no le interesa menejar la excepción de si el fichero no existe, sino que se propague hacia arriba. Eso sí, acuérdate del `finally`.

### Respuesta

En este escenario, el método declara `throws IOException` en su firma porque no desea tomar una decisión local sobre qué hacer si el archivo no existe o no se puede leer. Sin embargo, dado que se hace uso de un recurso (`BufferedReader`), se debe garantizar su cierre correcto empleando el bloque `finally` para evitar fugas de recursos en el sistema.

Así se separa nítidamente la responsabilidad de liberar el recurso físico (que pertenece al método que lo abrió) de la responsabilidad de manejar el error de negocio (que pertenece al método llamador superior).

```java
import java.io.BufferedReader; 
import java.io.FileReader; 
import java.io.IOException;

public class LectorFicheros {
    public static String leerPrimeraLinea(String nombreFichero) throws IOException {
        BufferedReader br = null;
        try {
            br = new BufferedReader(new FileReader(nombreFichero));
            return br.readLine();
        } finally {
            if (br != null) {
                br.close();
            }
        }
    }

    public static void main(String[] args) {
        try {
            String linea = leerPrimeraLinea("datos.txt");
            System.out.println(linea);
        } catch (IOException e) {
            System.out.println("Error propagado capturado en main: " + e.getMessage());
        }
    }
}
```


## 14. ¿Podemos poner en `throws` excepciones no controladas, como `RuntimeException`? ¿Debería el método llamador entonces poner `try-catch` en ese caso? ¿Qué sentido tendría?

### Respuesta

Sí, técnicamente Java permite incluir excepciones no controladas (que heredan de `RuntimeException`) en la cláusula `throws` de la firma de un método. No hay ninguna restricción del compilador que lo impida, aunque no es obligatorio declararlas de este modo.

El método llamador tampoco está obligado por el compilador a utilizar un bloque `try-catch` cuando invoca a un método que declara lanzar una `RuntimeException`. La única utilidad real de incluir estas excepciones en el `throws` es puramente documental e informativa, sirviendo como advertencia explícita en la API o documentación del método para avisar de condiciones extremas que causarán fallos del programa.


## 15. ¿Cuándo se recomienda usar excepciones controladas, como `IOException`, y cuándo no controladas como `IllegalArgumentException`? ¿Existen en todos los lenguajes ambas opciones? En los que sólo existe una opción, ¿cuál es la más habitual?

### Respuesta

Se recomienda usar excepciones controladas cuando el error se considera un evento externo razonablemente previsible y del cual la aplicación se puede recuperar de forma autónoma. Por ejemplo, que falte un archivo de configuración al inicio del programa es recuperable si se puede solicitar al usuario que indique otra ruta, por lo que una excepción controlada obliga al programador a prever esta alternativa.

Se recomienda usar excepciones no controladas cuando el error es la consecuencia de un bug o un fallo lógico en el código que no debería ocurrir si el software estuviera correctamente diseñado. Por ejemplo, pasar una edad negativa a un constructor indica un error del desarrollador al no validar los datos en la interfaz de usuario antes de instanciar la clase de negocio.

La distinción estricta entre excepciones controladas y no controladas en tiempo de compilación es una característica casi exclusiva de Java. La gran mayoría de lenguajes orientados a objetos modernos (como C++, C#, Python o Kotlin) operan bajo un modelo único equivalente a las excepciones no controladas: todas las excepciones pueden lanzarse en cualquier punto sin necesidad de declararse con `throws` ni capturarse obligatoriamente, delegando la responsabilidad de su gestión directamente en la disciplina del programador.


## 16. ¿Tiene sentido lanzar excepciones dentro del `catch`? ¿Se puede relanzar la misma excepción capturada? ¿Cuándo tendría sentido hacer esto último? Pon ejemplos de ambos casos.

### Respuesta

Sí, lanzar excepciones dentro de un bloque `catch` tiene mucho sentido en el desarrollo de software multicapa. Esta técnica, conocida como "traducción de excepciones", permite capturar un error de bajo nivel técnico y transformarlo en una excepción con un nivel de abstracción más apropiado para las capas superiores de la aplicación.

Por otro lado, relanzar exactamente la misma excepción capturada (utilizando la instrucción `throw e;`) es de gran utilidad cuando se desea realizar una acción intermedia de manera local (como registrar el suceso en un archivo de trazas o liberar algún estado temporal) sin interferir en la propagación natural del error hacia el código superior.

Ejemplo traduciendo una excepción a otra de mayor abstracción:

```java
class ConfiguracionException extends Exception { 
    public ConfiguracionException(String mensaje, Throwable causa) { 
        super(mensaje, causa); 
    } 
}

public static void cargarConfiguracion() throws ConfiguracionException { 
    try { 
        // Simulación de error de E/S
        throw new java.io.IOException("Fichero corrupto"); 
    } catch (java.io.IOException e) { 
        throw new ConfiguracionException("Error al cargar la configuracion del sistema", e); 
    } 
}
```

Ejemplo relanzando la misma excepción tras realizar una acción intermedia:

```java
public static void leerDatos() throws java.io.IOException { 
    try { 
        throw new java.io.IOException("Error de lectura fisica"); 
    } catch (java.io.IOException e) { 
        System.out.println("LOG: Incidencia registrada de forma local."); 
        throw e; // Relanza la misma excepción
    } 
}
```


## 17. ¿En qué consiste que una excepción sea la **"causa"** de otra excepción? Pon un ejemplo en Java, donde capturemos una excepción de bajo nivel y la encapsulemos en otra personalizada de alto nivel. Cuando una excepción sale por pantalla y tiene una causa, ¿se ve?

### Respuesta

Que una excepción sea la "causa" de otra significa que un error original de bajo nivel técnico es encapsulado dentro de una nueva instancia de excepción de mayor abstracción. Esto se conoce como "encadenamiento de excepciones" y permite mantener una trazabilidad técnica completa sobre el origen real del fallo sin violar los niveles de encapsulamiento del diseño.

En Java, esto se logra pasando el objeto excepción original como argumento al constructor de la nueva excepción personalizada (el cual invoca a `super(mensaje, causa)`). Cuando el programa se interrumpe y la traza de error se imprime por pantalla, el motor de ejecución de Java muestra la descripción del error de alto nivel y, seguidamente, añade la sección `Caused by:` con el detalle de la excepción original, permitiendo que la depuración sea sumamente ágil.

```java
import java.io.BufferedReader; 
import java.io.FileReader; 
import java.io.IOException;

class ConfiguracionException extends Exception { 
    public ConfiguracionException(String mensaje, Throwable causa) { 
        super(mensaje, causa); 
    } 
}

public class App {
    public static void cargarConfiguracion() throws ConfiguracionException {
        try {
            BufferedReader br = new BufferedReader(new FileReader("config.txt"));
            br.readLine();
            br.close();
        } catch (IOException e) {
            throw new ConfiguracionException(
                "No se pudo cargar la configuracion de la aplicacion", e
            );
        }
    }

    public static void main(String[] args) {
        try {
            cargarConfiguracion();
        } catch (ConfiguracionException e) {
            e.printStackTrace();
        }
    }
}
```
