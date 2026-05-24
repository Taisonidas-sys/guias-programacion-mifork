<!--
Posible prompt:
<prompt>
Tengo un cuestionario con preguntas sobre "Aspectos funcionales". Debes tener en cuenta que los conocimientos previos que tengo (y por tanto tus respuestas deben ser adaptadas), son:
- C/C++ sin orientación a objetos.
- Temas de Java previos: clases y objetos, encapsulación, excepciones, composición, herencia, polimorfismo y genericidad.

Cada respuesta debe tener entre 2 - 4 párrafos de longitud (sin contar los trozos de código).

Por favor, escribe en impersonal las respuestas.

</prompt>
----
-->

# TEMA 7. Aspectos funcionales

## 1. ¿Qué es un puntero a una función? Pon un ejemplo de código en C, donde se define una función y que reciba una cadena de caracteres como parámetro y devuelva la cadena en mayúsculas. Crea un puntero en una variable local a dicha función llamado `aMayusculas` e invócala con el puntero.

### Respuesta

En el lenguaje C, un puntero a una función es una variable que almacena la dirección de memoria física donde se encuentra el código compilado de una función determinada. Este mecanismo permite tratar a las funciones como datos, facilitando su paso como argumentos a otras funciones para implementar comportamientos dinámicos o *callbacks*.

Para declarar un puntero a función en C, se debe especificar obligatoriamente el tipo de retorno y las firmas de los parámetros de entrada entre paréntesis, asociando la variable de forma estricta. La invocación de la función a través del puntero se realiza de forma idéntica a una llamada convencional utilizando el nombre de la variable puntero.

```c
#include <stdio.h>
#include <ctype.h>

char* convertirAMayusculas(char* cadena) {
    char* aux = cadena;
    while (*aux) {
        *aux = toupper((unsigned char)*aux);
        aux++;
    }
    return cadena;
}

int main() {
    char texto[] = "hola mundo";
    // Declaracion del puntero a funcion 'aMayusculas'
    char* (*aMayusculas)(char*) = convertirAMayusculas;

    // Invocacion empleando el puntero
    aMayusculas(texto);
    printf("Resultado: %s\n", texto);
    return 0;
}
```


## 2. ¿Qué es una **función lambda** en un lenguaje de programación? Pon un ejemplo similar al anterior en Javascript y otro en Java con funciones lambda. Usa una variable local `aMayusculas` para apuntar a la función lambda. Por simplicidad, en Java, emplea `Function<String, String>` para el tipo de la referencia a la función lambda.

### Respuesta

Una función lambda (o función anónima) es una definición condensada y sin nombre de una función que puede ser tratada como un valor literal dentro del código del programa. Permite declarar comportamientos sobre la marcha de forma muy compacta, típicamente para ser pasados como argumentos de entrada a otros métodos o almacenados en variables locales.

A diferencia de los punteros a funciones en C, las funciones lambda representan abstracciones de mayor nivel que evitan la necesidad de declarar firmas complejas a nivel de sistema. Se construyen empleando operadores específicos como `=>` en JavaScript o `->` en Java.

```javascript
// Ejemplo en JavaScript
const aMayusculas = (cadena) => cadena.toUpperCase();
console.log(aMayusculas("hola mundo"));
```

En Java, dado que es un lenguaje de tipado estático, la referencia a una función lambda debe estar respaldada por una interfaz compatible. En este caso se utiliza la interfaz funcional genérica `Function<T, R>` de la API estándar (`java.util.function`), parametrizada con `String` tanto para el argumento de entrada como para el tipo de retorno.

```java
// Ejemplo en Java
import java.util.function.Function;

public class PrincipalLambda {
    public static void main(String[] args) {
        // Expresión lambda en Java
        Function<String, String> aMayusculas = (cadena) -> cadena.toUpperCase();

        // Invocación a través del método apply
        String resultado = aMayusculas.apply("hola mundo");
        System.out.println(resultado);
    }
}
```


## 3. ¿Qué es el **paradigma funcional**? ¿Por qué a algunos lenguajes orientados a objetos como Java 8, se les llama multi-paradigma? ¿Qué quiere decir que las funciones son "ciudadanos de primera clase"?

### Respuesta

El paradigma funcional es un estilo de desarrollo de software cimentado en la evaluación de funciones matemáticas puras, evitando el uso de estados mutables y datos compartidos. Se caracteriza por tratar los datos como inmutables, donde los métodos no producen efectos secundarios en las variables globales y el flujo del programa se resuelve mediante la composición y el encadenamiento de funciones en lugar de bucles iterativos tradicionales.

A lenguajes orientados a objetos como Java 8 se les denomina multi-paradigma porque permiten convivir y combinar armónicamente la orientación a objetos con los aspectos esenciales de la programación funcional dentro de una misma aplicación. Esto dota a los desarrolladores de herramientas para elegir el enfoque más eficiente, claro y mantenible para resolver cada problema específico de diseño.

La expresión de que las funciones son "ciudadanos de primera clase" (*first-class citizens*) significa que el lenguaje de programación trata a las funciones exactamente con los mismos derechos y capacidades que a cualquier otro tipo de dato ordinario (como enteros o cadenas). Esto implica que las funciones pueden ser asignadas libremente a variables locales, almacenadas en estructuras de datos, pasadas como argumentos a otros métodos o devueltas como tipo de retorno desde una función.


## 4. Explica la sintaxis básica de una función lambda en Java.

### Respuesta

La sintaxis básica de una expresión lambda en Java se compone de tres partes diferenciadas separadas por el operador de flecha `->`: los parámetros de entrada, el operador y el cuerpo de la función. La estructura típica se escribe como `(parámetros) -> { cuerpo }`.

Los parámetros de entrada se declaran entre paréntesis. Si la lambda recibe un único parámetro, se pueden omitir los paréntesis (ej. `cadena -> ...`). Además, gracias al motor de inferencia de tipos de Java, no es necesario declarar el tipo de datos de los parámetros, deduciéndolo el compilador del contexto de forma automática.

El cuerpo de la lambda contiene las instrucciones de ejecución. Si el cuerpo consta de una única instrucción o expresión, se pueden omitir las llaves `{}` y la palabra clave `return`, asumiendo el compilador que el resultado de evaluar esa línea es el valor de retorno de la lambda (ej. `x -> x * x`). Si se requieren múltiples líneas de código, se deben incluir obligatoriamente las llaves y declarar explícitamente el retorno mediante `return`.


## 5. Ahora recibamos una función como parámetro a un método y la llamaremos desde dentro. Amplia los ejemplos anteriores de Java y JavaScript con un método llamado `transformar`, que reciba un `String` como parámetro y luego una función transformadora como lo es `aMayúsculas` y la invoque desde dentro.

### Respuesta

Recibir funciones como argumentos de entrada en otros métodos es la base de las denominadas "funciones de orden superior". Este patrón permite parametrizar el comportamiento de un método general delegando la lógica de procesamiento específica en la función externa recibida como parámetro.

En JavaScript, al carecer de tipado estático, la función transformadora se recibe directamente como un parámetro genérico y se invoca utilizando paréntesis comunes. En Java, se declara que el método `transformar` acepta un argumento de tipo `Function<String, String>` y se invoca su comportamiento haciendo uso del método estándar `apply(...)`.

```javascript
// Ampliación en JavaScript
function transformar(cadena, funcionTransformadora) {
    return funcionTransformadora(cadena);
}

const aMayusculas = (c) => c.toUpperCase();
console.log(transformar("hola mundo", aMayusculas));
```

```java
// Ampliación en Java
import java.util.function.Function;

public class PrincipalTransformar {
    public static String transformar(String cadena, Function<String, String> funcion) {
        if (cadena == null || funcion == null) {
            throw new IllegalArgumentException("Parámetros nulos no permitidos.");
        }
        // Invocación interna de la función
        return funcion.apply(cadena);
    }

    public static void main(String[] args) {
        Function<String, String> aMayusculas = (c) -> c.toUpperCase();
        String resultado = transformar("hola mundo", aMayusculas);
        System.out.println(resultado);
    }
}
```


## 6. Now, invoca `transformar`, con una nueva función lambda directamente en la llamada a `transformar`, por ejemplo, una función lambda que invierta la cadena. Define la función de inversión justo cuando la estás pasando como parámetro.

### Respuesta

Una de las grandes ventajas de las funciones lambda es la capacidad de declararlas directamente en el punto de llamada de un método, eliminando la necesidad de almacenarlas previamente en variables temporales locales. Esto resulta en un código sumamente compacto y enfocado.

Para invertir una cadena de texto en Java, se puede hacer uso de la clase mutable de utilidad `StringBuilder`, la cual cuenta con un método integrado `reverse()`. La expresión lambda se escribe directamente en la llamada pasando como parámetro una función que toma la cadena, la introduce en el constructor de `StringBuilder`, la invierte y la retorna convertida a `String`.

```javascript
// Invocación en JavaScript con lambda anónima directa
console.log(transformar("hola mundo", (c) => c.split('').reverse().join('')));
```

```java
// Invocación en Java con lambda anónima directa
public class PrincipalInversion {
    public static String transformar(String cadena, java.util.function.Function<String, String> funcion) {
        return funcion.apply(cadena);
    }

    public static void main(String[] args) {
        // Se define la lambda de inversión justo en el argumento de la función
        String invertida = transformar("hola mundo", (c) -> new StringBuilder(c).reverse().toString());
        System.out.println(invertida); // Imprime: odnum aloh
    }
}
```


## 7. ¿Qué se entiende por cierre o "closure" en el contexto de las funciones lambda? Pon un ejemplo en Java de cómo una función lambda es capaz de acceder a una variable local en el contexto donde fue definida. Modifica el ejemplo anterior, creando otra función lambda para transformar una cadena, pero que lo que haga es concatenar a la cadena de entrada otra cadena que está en una variable local definida fuera de la función lambda.

### Respuesta

Un cierre (o *closure*) es la capacidad que posee una función lambda de "recordar" y acceder al ámbito léxico o entorno de variables que existían en el contexto preciso en el que fue creada, incluso si la lambda se ejecuta en un momento posterior o en un ámbito diferente de la aplicación. La lambda captura o "cierra" esas variables externas dentro de su estructura.

En Java, existe una restricción técnica de diseño para garantizar la estabilidad de la pila de memoria: las variables locales del contexto externo que son capturadas por la expresión lambda deben ser obligatoriamente inmutables, es decir, deben estar marcadas como `final` o ser "efectivamente finales" (no modificadas en el código posterior). De lo contrario, el compilador reportará un error para evitar problemas de sincronización de datos.

```java
import java.util.function.Function;

public class PrincipalClosure {
    public static String transformar(String cadena, Function<String, String> funcion) {
        return funcion.apply(cadena);
    }

    public static void main(String[] args) {
        // Variable local en el ámbito externo de la lambda
        final String sufijo = "!!!"; // O efectivamente final

        // La lambda captura e integra la variable 'sufijo' en su comportamiento (Closure)
        Function<String, String> concatenador = (c) -> c + sufijo;

        String resultado = transformar("hola mundo", concatenador);
        System.out.println(resultado); // Imprime: hola mundo!!!
    }
}
```


## 8. Reflexiona: ¿en qué se diferencia entonces una función lambda de los punteros a funciones que hay en C?

### Respuesta

Aunque ambos conceptos persiguen el objetivo común de parametrizar comportamiento tratando al código como datos, una función lambda de Java y un puntero a función de C operan bajo niveles de abstracción y arquitecturas de memoria completamente diferentes.

El puntero a función en C es una referencia de bajo nivel directa a una dirección física de memoria donde reside el código de la función. No posee ningún estado interno ni almacena contexto: es puramente una dirección de salto ejecutable. Esto impide la creación de cierres (*closures*); un puntero a función en C no puede capturar variables locales del entorno donde se declaró sin recurrir a estructuras globales complejas o punteros manuales inseguros pasados como parámetros `void*` adicionales.

En contraste, una función lambda en Java es en realidad un objeto de alto nivel gestionado de forma segura en el heap. Además de contener la lógica ejecutable, encapsula y retiene las referencias de las variables locales externas que capturó en su construcción (su estado de clausura). Asimismo, la plataforma Java garantiza la seguridad de tipos estática en compilación y la liberación automática de la memoria del cierre mediante el recolector de basura, evitando por completo los punteros nulos o desbordamientos típicos de C.


## 9. Devolvamos ahora funciones. Creemos ahora una función que sea capaz de crear funciones "descuento". Una función "descuento", decrementa un porcentaje pasado como parámetro. Por simplicidad, usa `Function<Double, Double>` para su tipo. La función `crearDescuento(porcentaje)`, recibe solo el porcentaje de descuento a aplicar y devuelve la función de descuento. Prueba a crear dos descuentos distintos y aplicarlos a una cantidad. Explica la closure en la función descuento.

### Respuesta

La capacidad de que una función devuelva otra función como resultado es otra de las características esenciales de los "ciudadanos de primera clase". Permite parametrizar la generación de comportamientos personalizados que serán invocados en el futuro.

En este ejemplo, el método `crearDescuento(double porcentaje)` actúa como una fábrica de funciones de descuento. Devuelve una instancia de `Function<Double, Double>` que, al recibir un importe original, le aplica el descuento preestablecido.

```java
import java.util.function.Function;

public class FabricaDescuentos {
    public static Function<Double, Double> crearDescuento(double porcentaje) {
        if (porcentaje < 0.0 || porcentaje > 100.0) {
            throw new IllegalArgumentException("El porcentaje debe estar entre 0 y 100.");
        }
        // Retorna una función lambda que captura la variable local 'porcentaje'
        return (precioOriginal) -> precioOriginal * (1.0 - (porcentaje / 100.0));
    }

    public static void main(String[] args) {
        // Creación de dos funciones de descuento distintas
        Function<Double, Double> descuentoDiez = crearDescuento(10.0);
        Function<Double, Double> descuentoMitad = crearDescuento(50.0);

        double precioOriginal = 200.0;

        // Aplicación de las funciones generadas
        System.out.println("Precio con 10% de descuento: " + descuentoDiez.apply(precioOriginal)); // 180.0
        System.out.println("Precio a mitad de precio: " + descuentoMitad.apply(precioOriginal)); // 100.0
    }
}
```

La *closure* juega un papel determinante en el funcionamiento del descuento devuelto. Cuando finaliza la ejecución del método `crearDescuento(10.0)`, la variable local `porcentaje` (que valía 10.0) debería ser eliminada de la pila de llamadas del sistema. Sin embargo, la función lambda retornada conserva internamente una referencia capturada a ese valor exacto. Al invocar posteriormente `descuentoDiez.apply(200.0)` en `main`, la lambda recuerda de forma transparente su *porcentaje* original de 10.0, permitiendo que cada función generada se comporte de manera independiente.


## 10. En Java, que es un lenguaje con comprobación estática de tipos, donde los tipos se declaran, toda función lambda tiene un tipo, que se conoce como **interfaz funcional**. ¿Qué es una **interfaz funcional**? ¿Qué requisito tiene?

### Respuesta

En Java, una interfaz funcional es una interfaz estándar que declara única y exclusivamente un único método abstracto (denominado SAM, o *Single Abstract Method*). Sirve como el tipo estático de datos que respalda y define la firma de las expresiones lambda en tiempo de compilación.

El requisito ineludible de una interfaz funcional es tener exactamente un único método abstracto. Si la interfaz declara más de un método abstracto, el compilador rechazará tratarla como tipo para una expresión lambda. Sin embargo, la interfaz sí puede contener cualquier número de métodos estáticos y métodos por defecto (`default`) ya implementados, ya que estos no rompen la regla del único método abstracto pendiente.

Para documentar y forzar esta regla de diseño de forma explícita, se recomienda anteponer la anotación `@FunctionalInterface` en la cabecera de la declaración de la interfaz. Esto le ordena al compilador verificar de forma proactiva que se cumpla el requisito del método abstracto único, arrojando un error inmediato de compilación si se intenta agregar un segundo método abstracto por error.


## 11. Creemos una interfaz funcional a mano. Por ejemplo, define la interfaz funcional del ejemplo que transforma la cadena en otra. Llámale `Transformador`, que define una función que convierte una cadena de texto (`String`) en otra (`String`).

### Respuesta

Para diseñar nuestra interfaz funcional personalizada a mano en Java, se declara la interfaz `Transformador` decorada con la anotación `@FunctionalInterface` y se define su único método abstracto `transformar` que recibe y retorna objetos de tipo `String`.

```java
@FunctionalInterface
public interface Transformador {
    // Unico metodo abstracto
    String transformar(String texto);
}
```

Una vez definida, esta interfaz puede utilizarse directamente como tipo estático para declarar e instanciar expresiones lambda de forma transparente en el código de la aplicación.

```java
public class PrincipalTransformadorPersonalizado {
    public static void main(String[] args) {
        // La lambda se adapta perfectamente a la firma de Transformador
        Transformador exclamar = (t) -> t + "!!!";

        String resultado = exclamar.transformar("hola");
        System.out.println(resultado); // Imprime: hola!!!
    }
}
```


## 12. Ahora hagamos la interfaz funcional algo más genérica y empleando generics, para que permita definir un `Transformador` de un tipo en otro. Pon un ejemplo de un transformador que redondea un `Double` en un `Integer`.

### Respuesta

Para expandir el diseño y dotarlo de máxima reutilización y tipado seguro, se transforma la interfaz en una estructura genérica parametrizada con dos marcadores de tipo `<T, R>`. El parámetro `<T>` representa el tipo del elemento de entrada y el parámetro `<R>` representa el tipo del resultado transformado devuelto.

```java
@FunctionalInterface
public interface TransformadorGenerico<T, R> {
    R transformar(T entrada);
}
```

Este diseño genérico permite declarar transformadores entre cualesquiera combinaciones de objetos del sistema. Para redondear un valor de tipo real `Double` a un entero `Integer`, se instancia la interfaz especificando dichos tipos y implementando la conversión matemática mediante `Math.round`.

```java
public class PrincipalTransformadorGenerico {
    public static void main(String[] args) {
        // Transformador de Double a Integer
        TransformadorGenerico<Double, Integer> redondeador = (d) -> (int) Math.round(d);

        Integer resultado = redondeador.transformar(5.7);
        System.out.println(resultado); // Imprime: 6
    }
}
```


## 13. `Transformador`, en su versión genérica, parece muy útil y reutilizable, hasta el punto de que es igual a una interfaz funcional que ya hay, que es `Function<T, R>`. Muestra las interfaces funcionales predefinidas que hay en Java.

### Respuesta

Para evitar que los desarrolladores tengan que redefinir constantemente interfaces funcionales comunes, Java 8 incorporó en el paquete estándar `java.util.function` un amplísimo conjunto de interfaces funcionales genéricas predefinidas y listas para su uso en la mayoría de los escenarios habituales.

Las cuatro categorías esenciales de interfaces funcionales predefinidas en Java son las siguientes:
1. **`Function<T, R>`:** Representa una función que recibe un argumento de tipo `T` y devuelve un resultado de tipo `R`. Su método abstracto es `R apply(T t)`.
2. **`Consumer<T>`:** Representa una operación que acepta un único argumento de entrada de tipo `T` y realiza alguna acción sin devolver ningún resultado (`void`). Su método abstracto es `void accept(T t)`.
3. **`Supplier<T>`:** Representa un proveedor de datos que no recibe ningún parámetro de entrada y retorna una instancia de tipo `T`. Su método abstracto es `T get()`.
4. **`Predicate<T>`:** Representa un predicado lógico o filtro que recibe un argumento de entrada de tipo `T` y devuelve un valor booleano (`boolean`). Su método abstracto es `boolean test(T t)`.

Además de estas cuatro categorías fundamentales, la API estándar incluye variantes específicas muy útiles para trabajar con dos argumentos (ej. `BiFunction<T, U, R>`, `BiConsumer<T, U>`), especializaciones optimizadas para tipos primitivos que evitan el coste de autoboxing (ej. `IntConsumer`, `DoublePredicate`), e interfaces de identidad donde los operandos y resultados son del mismo tipo (ej. `UnaryOperator<T>`, `BinaryOperator<T>`).


## 14. Vamos a ver ejemplos expresivos de funcional en Java. Estudiemos el `List.forEach`, como versión funcional del bucle `for`. Emplea el `forEach` para recorrer una lista de `Integer` y que muestre un mensaje si el entero es positivo.

### Respuesta

El método `forEach` incorporado en la interfaz `Iterable` de las colecciones de Java representa una alternativa declarativa muy limpia frente a los bucles imperativos tradicionales (`for` o `while`). Permite recorrer los elementos delegando la acción a ejecutar en cada paso a una función que implementa la interfaz funcional `Consumer`.

Para recorrer una lista de números enteros y filtrar únicamente los positivos utilizando un enfoque funcional, se invoca a `forEach` pasándole una expresión lambda que evalúa la condición de valor y realiza la salida estándar por consola de forma directa.

```java
import java.util.Arrays;
import java.util.List;

public class PrincipalForEach {
    public static void main(String[] args) {
        List<Integer> numeros = Arrays.asList(-3, 5, -1, 10, 0, 8);

        System.out.println("Números positivos en la lista:");
        // Uso de forEach con una expresión lambda
        numeros.forEach((n) -> {
            if (n > 0) {
                System.out.println("Positivo detectado: " + n);
            }
        });
    }
}
```


## 15. Repasando el tema de genericidad, fíjate en la firma de `forEach`, ¿por qué se usa `Consumer<? super T>` y no `Consumer<T>`? Explica qué significa **PECS**, y explícalo para el caso de mejorar el ejemplo del método `transformar` la hora de definir el tipo de la función transformadora.

### Respuesta

La firma del método `forEach` está declarada como `void forEach(Consumer<? super T> action)`. El uso del comodín contravariante `? super T` en lugar de la coincidencia estricta `Consumer<T>` se realiza para maximizar la flexibilidad del polimorfismo de lectura y escritura con seguridad de tipos genéricos.

Esto permite que un consumidor general diseñado para procesar una superclase sea reutilizado para recorrer una lista de sus subtipos. Por ejemplo, si se tiene una lista de `Integer`, es perfectamente válido y seguro pasar un consumidor que opere sobre la clase base general `Number` (ej. `Consumer<Number>`), ya que cualquier entero "es-un" número compatible con los requisitos del consumidor. Si la firma estuviera rígida a `Consumer<T>`, Java rechazaría la llamada forzando la invarianza de forma ineficiente.

Este comportamiento se rige por el principio nemotécnico de diseño de software genérico conocido como **PECS** (*Producer Extends, Consumer Super*):
* **Producer Extends:** Si la estructura genérica actúa principalmente como un proveedor o productor de datos que van a ser leídos, se debe utilizar `? extends T` para poder recuperar con seguridad los elementos tratándolos como subtipos de `T`.
* **Consumer Super:** Si la estructura actúa principalmente como un consumidor o receptor de datos donde se van a escribir elementos, se debe utilizar `? super T` para poder añadir con seguridad elementos compatibles con `T`.

Para el caso del método `transformar(String, Function<String, String>)` del Tema 7, la firma original es restrictiva. Siguiendo el principio PECS, la función de transformación actúa como un consumidor para su argumento de entrada (recibe datos) y como un productor para su valor de salida (devuelve datos). Por tanto, la firma ideal mejorada del método debe reescribirse utilizando comodines para dotarlo de máxima flexibilidad y admitir transformadores generales:

```java
// Firma flexible aplicando el principio PECS
public static <T, R> R transformar(T entrada, java.util.function.Function<? super T, ? extends R> funcion) {
    return funcion.apply(entrada);
}
```


## 16. Referencias a métodos. Podemos obtener una referencia a métodos de objetos o clases. Pon un ejemplo en JavaScript y en Java, de una clase `Persona` con un método `saludar`. En el código principal, crea una `Persona` con un nombre, y obtén una referencia a su método `saludar` en una variable local. Invoca `saludar` con esa referencia a su método `saludar`.

### Respuesta

Las referencias a métodos son una simplificación sintáctica muy elegante para expresiones lambda que se limitan a invocar de forma directa a un método ya existente de una clase o de un objeto. Permiten tratar a los métodos como funciones puras asignándolos a variables locales con una notación sumamente limpia.

En JavaScript, dado que las funciones son objetos ordinarios desde el diseño, se puede obtener la referencia al método extrayéndolo directamente del objeto de la instancia, aunque se debe vincular de forma explícita el contexto del objeto utilizando el método integrado `bind` para evitar la pérdida del puntero `this`. En Java, se utiliza el operador de doble dos puntos `::` para declarar la referencia del método asociado a una instancia concreta de forma automática y segura.

```javascript
// Ejemplo en JavaScript
class Persona {
    constructor(nombre) {
        this.nombre = nombre;
    }
    saludar() {
        return `Hola, soy ${this.nombre}`;
    }
}

const persona = new Persona("Tomas");
// Obtencion de la referencia vinculando el contexto
const saludarReferencia = persona.saludar.bind(persona);

console.log(saludarReferencia()); // Invocación
```

```java
// Ejemplo en Java
import java.util.function.Supplier;

public class Persona {
    private final String nombre;

    public Persona(String nombre) {
        this.nombre = nombre;
    }

    public String saludar() {
        return "Hola, soy " + nombre;
    }
}
```

```java
public class PrincipalReferencia {
    public static void main(String[] args) {
        Persona persona = new Persona("Tomas");

        // Referencia a método de una instancia concreta en una variable local
        Supplier<String> saludarReferencia = persona::saludar;

        // Invocación a través del método get de la interfaz funcional Supplier
        System.out.println(saludarReferencia.get()); 
    }
}
```


## 17. ¿Qué tipos de referencias a método se pueden hacer en Java? Pon un ejemplo de referencia a método estático, a constructor, a método de instancia de una instancia concreta y a método de instancia sobre cualquier instancia.

### Respuesta

En Java, el compilador admite cuatro clasificaciones o variantes distintas de referencias a métodos utilizando el operador de doble dos puntos `::`, adaptándose de forma muy precisa a la firma y contexto de la interfaz funcional requerida.

Los cuatro tipos de referencias a métodos en Java se exponen a continuación con sus respectivos ejemplos ilustrativos:

1. **Referencia a método estático:** Asocia un método de clase estático (ej. `Clase::metodoEstatico`).
   ```java
   // Lambda equivalente: (x) -> Math.abs(x)
   java.util.function.Function<Double, Double> absRef = Math::abs;
   ```
2. **Referencia a un constructor:** Permite crear fábricas de instancias asociándolas al operador `new` (ej. `Clase::new`).
   ```java
   // Lambda equivalente: (s) -> new StringBuilder(s)
   java.util.function.Function<String, StringBuilder> builderRef = StringBuilder::new;
   ```
3. **Referencia a método de instancia de un objeto concreto:** Vincula la llamada a una instancia específica creada previamente (ej. `objeto::metodoInstancia`).
   ```java
   String sufijo = "!!!";
   // Lambda equivalente: (s) -> sufijo.concat(s)
   java.util.function.Function<String, String> concRef = sufijo::concat;
   ```
4. **Referencia a método de instancia de un objeto arbitrario de un tipo particular:** Permite invocar un método ordinario de instancia donde la propia instancia de ejecución es el primer parámetro que recibirá la lambda en su procesamiento (ej. `Clase::metodoInstancia`).
   ```java
   // Lambda equivalente: (s) -> s.isEmpty()
   java.util.function.Predicate<String> emptyRef = String::isEmpty;
   ```


## 18. Otro ejemplo expresivo. Ordena una lista de `Persona`, cada persona tiene un nombre y una edad (de tipo entero). Ordena la lista de `Persona` con `Collections.sort`, pasándole como comparador una expresión lambda que compare la edad de ambas personas y si tienen la misma edad, se ordene por orden alfabético del nombre. Crea dos versiones: Una con la función de comparación hecha manualmente, y otra empleando `Comparator`.

### Respuesta

La ordenación de colecciones es uno de los escenarios donde la programación funcional en Java brilla de forma más expresiva, permitiendo inyectar criterios de comparación directamente mediante lambdas claras y descriptivas.

Se presentan a continuación las dos versiones del código para ordenar una lista de personas combinando múltiples criterios (edad y orden alfabético del nombre).

```java
public class Persona {
    private final String nombre;
    private final int edad;

    public Persona(String nombre, int edad) {
        this.nombre = nombre;
        this.edad = edad;
    }

    public String getNombre() { return nombre; }
    public int getEdad() { return edad; }

    @Override
    public String toString() {
        return nombre + " (" + edad + ")";
    }
}
```

```java
import java.util.Arrays;
import java.util.Collections;
import java.util.Comparator;
import java.util.List;

public class PrincipalOrdenacion {
    public static void main(String[] args) {
        List<Persona> personas = Arrays.asList(
            new Persona("Tomas", 25),
            new Persona("Ana", 20),
            new Persona("Tomas", 20),
            new Persona("Maria", 25)
        );

        // ==================== VERSION 1: COMPARACION MANUAL ====================
        Collections.sort(personas, (p1, p2) -> {
            // Criterio 1: Comparar por edad
            int compEdad = Integer.compare(p1.getEdad(), p2.getEdad());
            if (compEdad != 0) {
                return compEdad;
            }
            // Criterio 2: En caso de empate, comparar por nombre alfabeticamente
            return p1.getNombre().compareTo(p2.getNombre());
        });
        System.out.println("Ordenado manual: " + personas);

        // Rebarajamos para probar la segunda version
        Collections.shuffle(personas);

        // ==================== VERSION 2: EMPLEANDO COMPARATOR (Funcional / Fluida) ====================
        // Hacemos uso del patron builder fluido de la API java.util.Comparator
        personas.sort(
            Comparator.comparingInt(Persona::getEdad)
                      .thenComparing(Persona::getNombre)
        );
        System.out.println("Ordenado fluido: " + personas);
    }
}
```

La segunda alternativa demuestra el poder de la expresividad funcional en Java. Mediante referencias a métodos y métodos fluidos encadenados (`comparingInt` y `thenComparing`), el código resultante se auto-documenta, asemejándose a una declaración del lenguaje natural que define los criterios con máxima limpieza matemática.
