<!--
Posible prompt:
<prompt>
Tengo un cuestionario con preguntas sobre "Genericidad". Debes tener en cuenta que los conocimientos previos que tengo (y por tanto tus respuestas deben ser adaptadas), son:
- C/C++ sin orientación a objetos.
- Temas de Java previos: clases y objetos, encapsulación, excepciones, composición, herencia y polimorfismo.

Cada respuesta debe tener entre 2 - 4 párrafos de longitud (sin contar los trozos de código).

Por favor, escribe en impersonal las respuestas.

</prompt>
----
-->
# TEMA 6. Genericidad

## 1. Empleando `void*` en C o `Object` en Java, pon un ejemplo de una estructura de datos, que empleando un array primitivo, permita alojar cualquier tipo de dato.

### Respuesta

En lenguajes de tipado estático, la programación genérica rudimentaria se lograba abstrayendo los tipos concretos mediante punteros universales o referencias a la raíz de la jerarquía de tipos. En el lenguaje C, el tipo `void*` representa un puntero genérico a cualquier dirección de memoria, permitiendo que un array de punteros aloje referencias a estructuras de cualquier tipo.

En Java, dado que todas las clases heredan implícitamente de la clase universal `Object`, se puede crear una estructura de datos basada en un array de tipo `Object[]`. Al ser compatible a nivel de tipos, este array es capaz de almacenar de forma transparente cualquier objeto del sistema, incluyendo clases envoltorio para tipos primitivos gracias al mecanismo de autoboxing.

```c
// Ejemplo en C usando void*
#include <stdio.h>

struct ContenedorC {
    void* datos[10];
    int cantidad;
};

void insertarC(struct ContenedorC* c, void* dato) {
    if (c->cantidad < 10) {
        c->datos[c->cantidad] = dato;
        c->cantidad++;
    }
}
```

```java
// Ejemplo en Java usando Object
public class ContenedorJava {
    private final Object[] elementos;
    private int cantidad;

    public ContenedorJava(int capacidad) {
        this.elementos = new Object[capacidad];
        this.cantidad = 0;
    }

    public void insertar(Object elemento) {
        if (cantidad < elementos.length) {
            elementos[cantidad] = elemento;
            cantidad++;
        }
    }

    public Object obtener(int posicion) {
        if (posicion < 0 || posicion >= cantidad) {
            throw new IndexOutOfBoundsException("Posición fuera de rango.");
        }
        return elementos[posicion];
    }
}
```


## 2. Brevemente, ¿Qué significa la **programación genérica**? ¿Es el ejemplo anterior un ejemplo básico de programación genérica? 

### Respuesta

La programación genérica es un paradigma de diseño de software que se centra en escribir algoritmos y estructuras de datos de manera abstracta, independiente de los tipos de datos concretos con los que operarán. Permite definir la lógica de procesamiento una sola vez y reutilizarla de forma segura con múltiples tipos de datos diferentes, sin duplicar código ni perder eficiencia.

El ejemplo anterior, utilizando `void*` en C o `Object` en Java, representa en efecto una forma primitiva e histórica de programación genérica (conocida como genericidad por herencia o por conversión). Cumple con el objetivo de reutilizar la misma clase o estructura con diversos tipos de datos, abstrayendo el almacenamiento bajo un tipo universal común.

Sin embargo, carece de los mecanismos modernos de seguridad en tiempo de compilación. Es un diseño tosco que traslada toda la responsabilidad del control de tipos al desarrollador, obligándole a realizar conversiones manuales explícitas y exponiendo la aplicación a fallos catastróficos en tiempo de ejecución si se cometen errores de tipado.


## 3. Indica los problemas respecto al chequeo de tipos, de emplear `void*` o `Object` cuando se crean estructuras de datos genéricas. 

### Respuesta

El principal problema de emplear `void*` o `Object` para la genericidad radica en la pérdida absoluta del chequeo de tipos estático por parte del compilador (*type safety*). Al almacenar cualquier dato bajo una referencia universal, el compilador no puede verificar qué clase de objeto reside realmente dentro de la estructura, permitiendo insertar inadvertidamente elementos incompatibles (por ejemplo, meter un entero en un contenedor diseñado lógicamente para cadenas de texto).

Esto obliga a que al recuperar cualquier elemento de la estructura de datos, el programador deba realizar obligatoriamente un *downcasting* explícito (en Java) o una desreferenciación directa con cast (en C) al tipo real para poder interactuar con sus métodos específicos. Si el objeto recuperado no coincide lógicamente con el tipo al que se le aplica el cast, la aplicación fallará de forma abrupta en tiempo de ejecución lanzando una excepción `ClassCastException` o provocando un desbordamiento de memoria inaccesible en C.

Además, este enfoque debilita la legibilidad y auto-documentación del código. Las firmas de los métodos y la declaración de la estructura de datos solo revelan que se manipulan objetos de tipo `Object` o `void*`, no proporcionando ninguna pista semántica sobre el propósito real de la colección, lo que incrementa significativamente la probabilidad de introducir bugs lógicos durante el mantenimiento.


## 4. Vamos entonces con mecanismos de mejora de la programación genérica ¿Qué son los **parámetros de tipo**? 

### Respuesta

Los parámetros de tipo (representados convencionalmente con letras mayúsculas como `<T>`, `<E>` o `<K,V>`) son marcadores de posición temporales que se utilizan al declarar una clase, interfaz o método genérico. Actúan como variables de tipo que serán sustituidas por tipos de datos reales y concretos en el momento preciso en el que el programador declare e instancie la estructura en su código cliente.

Este mecanismo permite que la lógica interna de la clase (sus atributos, firmas de métodos y variables locales) se diseñe de forma abstracta basándose en la etiqueta de parámetro `<T>`, delegando la definición del tipo exacto a la decisión del desarrollador que va a utilizar la estructura de datos.

La introducción de los parámetros de tipo permite al compilador recuperar el control estático del tipado. Al instanciar la clase especificando un tipo concreto (ej. `List<String>`), el compilador asume contractualmente que todos los parámetros del tipo `<T>` dentro de esa instancia serán tratados rigurosamente como `String`, eliminando la necesidad de realizar casts manuales y rechazando en tiempo de compilación cualquier intento de insertar un tipo incompatible.


## 5. En Java existe "generics", en C++ existen "templates". Pon un ejemplo de uso de programación genérica en ambos, instanciando una lista o vector dinámico que solo admite `String`. Introduce valores, y luego haz un recorrido de ellos mostrando cómo cada elemento es del tipo concreto con seguridad.

### Respuesta

Para contrastar el uso práctico de la genericidad con tipado seguro en ambos lenguajes, se presenta la instanciación de un contenedor dinámico que restringe sus elementos exclusivamente a cadenas de texto (`String` o `std::string`).

En C++, se utiliza la biblioteca estándar `std::vector` parametrizada con `std::string`. En Java, se emplea la clase `ArrayList` parametrizada con `String`. En ambos casos, el compilador garantiza que no se puedan introducir elementos de otros tipos y permite recuperar los elementos directamente con su tipo específico sin requerir casting explícito.

```cpp
// Ejemplo en C++ usando Templates (std::vector)
#include <iostream>
#include <vector>
#include <string>

int main() {
    // Instanciacion con seguridad de tipos
    std::vector<std::string> lista;
    lista.push_back("Hola");
    lista.push_back("Mundo");

    // Recorrido seguro directamente como std::string
    for (const std::string& str : lista) {
        std::cout << str << std::endl; 
    }
    return 0;
}
```

```java
// Ejemplo en Java usando Generics (ArrayList)
import java.util.ArrayList;
import java.util.List;

public class PrincipalGenerics {
    public static void main(String[] args) {
        // Instanciacion con seguridad de tipos
        List<String> lista = new ArrayList<>();
        lista.add("Hola");
        lista.add("Mundo");

        // Recorrido seguro directamente como String, sin downcasting manual
        for (String str : lista) {
            System.out.println(str); 
        }
    }
}
```


## 6. Sobre el funcionamiento de la programación genérica. ¿Qué hace el compilador cuando se instancia una clase que tiene parámetros de tipo? ¿Hace lo mismo C++ y Java? ¿Qué es el "type erasure" de Java y la "instanciación de plantillas" de C++?

### Respuesta

Aunque a nivel de sintaxis el uso de generics en Java y templates en C++ parece muy similar, el compilador realiza procesos internos radicalmente diferentes en cada lenguaje para dar soporte al comportamiento genérico en tiempo de ejecución.

En C++, el compilador realiza un proceso denominado instanciación de plantillas (*template instantiation*). Cada vez que se utiliza una clase con un tipo de parámetro nuevo (ej. `std::vector<int>` y `std::vector<double>`), el compilador genera físicamente en el binario final una copia completa del código máquina de la clase adaptada para ese tipo específico. Esto proporciona una excelente eficiencia en ejecución y soporte para tipos primitivos directos, a costa de incrementar el tamaño físico del archivo compilado (fenómeno conocido como *code bloat*).

En Java, por el contrario, el compilador aplica una estrategia llamada borrado de tipos (*type erasure*). Tras realizar todas las validaciones estáticas de seguridad de tipos durante la compilación, el compilador elimina toda la información de los parámetros genéricos (los bloques `<T>`) antes de generar el *byte-code*. En su lugar, sustituye el parámetro `<T>` por su límite superior (típicamente la clase `Object`), e introduce de forma automática instrucciones de *downcasting* invisibles en los puntos de recuperación de datos. Esto permite mantener la compatibilidad con versiones antiguas de Java y evita el crecimiento del tamaño de los archivos `.class`, pero introduce la limitación de no poder trabajar directamente con tipos primitivos (obligando a usar sus envoltorios *wrappers*).


## 7. Vamos a crear una nueva clase con parámetros de tipo. Define en Java una clase `Par`, que permite alojar dos valores de tipos diferentes. Incluye un constructor y un getter para cada tipo. Pon un ejemplo de uso de ese `Par`, por ejemplo para especificar el tipo de retorno de una función que devuelve en un `Par` la media y desviación típica de un array de `double`. 

### Respuesta

Para diseñar un tipo de datos estructurado que actúe como una tupla genérica de dos elementos con tipos independientes en Java, se define la clase `Par` parametrizada con dos marcadores de tipo `<T, U>`. Esto proporciona la máxima flexibilidad para asociar cualesquiera combinaciones de objetos con seguridad.

```java
public class Par<T, U> {
    private final T primero;
    private final U segundo;

    public Par(T primero, U segundo) {
        this.primero = primero;
        this.segundo = segundo;
    }

    public T getPrimero() { return primero; }
    public U getSegundo() { return segundo; }
}
```

Este tipo es sumamente útil para superar la limitación de Java que prohíbe devolver múltiples valores desde un único método. Al calcular la media y la desviación típica, se puede encapsular el resultado de forma segura en un objeto `Par<Double, Double>` sin tener que crear una clase a medida ad-hoc para esta operación.

```java
public class CalculadoraEstadistica {
    public static Par<Double, Double> calcularEstadisticas(double[] valores) {
        if (valores == null || valores.length == 0) {
            throw new IllegalArgumentException("El array de valores no puede estar vacio.");
        }
        
        double suma = 0.0;
        for (double v : valores) {
            suma += v;
        }
        double media = suma / valores.length;

        double sumaCuadrados = 0.0;
        for (double v : valores) {
            sumaCuadrados += Math.pow(v - media, 2);
        }
        double desviacion = Math.sqrt(sumaCuadrados / valores.length);

        // Se retorna la tupla generica
        return new Par<>(media, desviacion);
    }
}
```


## 8. En Java, se pueden declarar parámetros de tipo también a nivel de método, no solo a nivel de clase. Pon un ejemplo con un método genérico `seleccionaUno`, que pasados dos objetos del mismo tipo, te devuelva aleatoriamente uno de ellos. Muestra la diferencia de definirlo con dos `Object`, a definirlo con dos parámetros de tipo, en terminos de (i) evitar downcasting y (ii) forzar que ambos objetos sean del mismo tipo. 

### Respuesta

Los métodos genéricos en Java declaran sus propios parámetros de tipo independientes, situando el bloque `<T>` justo antes del tipo de retorno en la cabecera de la función. Esto permite que el método sea genérico y aplique seguridad de tipos aunque resida dentro de una clase ordinaria no genérica.

Para ilustrar las ventajas críticas de un método genérico frente a una definición tradicional basada en `Object`, se exponen a continuación las dos alternativas en código Java.

```java
import java.util.Random;

public class Utilidades {
    private static final Random random = new Random();

    // Alternativa A: Usando Object
    public static Object seleccionaUnoObject(Object o1, Object o2) {
        return random.nextBoolean() ? o1 : o2;
    }

    // Alternativa B: Usando metodos genericos con parametros de tipo
    public static <T> T seleccionaUnoGenerico(T o1, T o2) {
        return random.nextBoolean() ? o1 : o2;
    }
}
```

La alternativa basada en parámetros de tipo aporta dos beneficios fundamentales de robustez:
1. **Evitar el downcasting:** Al invocar `seleccionaUnoGenerico("Hola", "Mundo")`, el compilador deduce automáticamente que `<T>` es de tipo `String`, por lo que el tipo de retorno devuelto por el método es exactamente `String`, pudiendo asignarse directamente a una variable `String` sin necesidad de realizar un cast manual molesto y peligroso. Con la alternativa basada en `Object`, el retorno es siempre un `Object` y obliga al cast explícito.
2. **Forzar coincidencia de tipos:** El parámetro `<T>` exige contractualmente que ambos argumentos pasados al método pertenezcan a clases compatibles dentro de la misma línea jerárquica. Si el programador intenta invocar `seleccionaUnoGenerico("Hola", 123)`, el compilador arrojará un error al no poder unificar los argumentos en un tipo concreto coherente. En la versión basada en `Object`, se permitiría la llamada de forma silenciosa mezclando tipos incompatibles sin alertar del riesgo de error posterior.


## 9. ¿Se pueden establecer restricciones en los parámetros de tipo? Por ejemplo, si quiero definir un tipo genérico `<T>`, ¿puedo decir que tenga que ser, al menos, un número para poder tratarlo como tal? Pon un ejemplo en Java de un `Punto` con dos coordenadas, metodos `getX`, `getY`, y una función `calcularDistanciaA` otro `Punto`. Permite que esas coordenadas sean cualquier tipo de número. Pon dos soluciones: una simplemente creando coordenadas de tipo `Number` y otra añadiendo generics para reforzar el chequeo de tipos y saber exactamente con qué tipo de número trabaja el `Punto`. En este caso y respecto al "type erasure", ¿cuál es el tipo final tras la compilación?

### Respuesta

Sí, en Java es posible establecer restricciones o límites sobre los parámetros de tipo genéricos utilizando la palabra clave `extends` seguida de una clase o interfaz límite (ej. `<T extends Number>`). Esto restringe la instanciación únicamente a tipos compatibles con la clase límite, permitiendo además invocar de forma segura a todos los métodos públicos expuestos por dicha clase base dentro del código genérico.

Para modelar un `Punto` con coordenadas numéricas flexibles (que puedan ser `Double`, `Integer`, `Float`, etc.), se presentan las dos alternativas de diseño. En la solución sin generics, se almacena el estado utilizando el tipo base polimórfico `Number` directamente. En la solución con generics parametrizada bajo la restricción `<T extends Number>`, se recupera el control estático de tipos.

```java
// ==================== SOLUCIÓN A: SIN GENERICS (Usando Number) ====================
public class PuntoNumber {
    private final Number x;
    private final Number y;

    public PuntoNumber(Number x, Number y) {
        this.x = x;
        this.y = y;
    }

    public Number getX() { return x; }
    public Number getY() { return y; }

    public double calcularDistanciaA(PuntoNumber otro) {
        double dx = this.x.doubleValue() - otro.x.doubleValue();
        double dy = this.y.doubleValue() - otro.y.doubleValue();
        return Math.sqrt(dx * dx + dy * dy);
    }
}

// ==================== SOLUCIÓN B: CON GENERICS (Acotado con extends) ====================
public class PuntoGenerico<T extends Number> {
    private final T x;
    private final T y;

    public PuntoGenerico(T x, T y) {
        this.x = x;
        this.y = y;
    }

    public T getX() { return x; }
    public T getY() { return y; }

    public double calcularDistanciaA(PuntoGenerico<T> otro) {
        // Al estar acotado a Number, es seguro llamar a doubleValue()
        double dx = this.x.doubleValue() - otro.x.doubleValue();
        double dy = this.y.doubleValue() - otro.y.doubleValue();
        return Math.sqrt(dx * dx + dy * dy);
    }
}
```

En la solución parametrizada, debido al proceso de borrado de tipos (*type erasure*), el compilador sustituye el parámetro acotado `<T extends Number>` por su clase límite superior en el byte-code final. Por lo tanto, tras la compilación del archivo `.class`, el tipo real que utiliza internamente la máquina virtual para las variables `x` e `y` y para los tipos de retorno de los getters es `Number`.


## 10. Sobre las soluciones anteriores. Si bien ambas permiten trabajar con distintos tipos de número sin duplicar la clase `Punto`, reflexiona sobre el refuerzo del chequeo de tipos con generics. ¿Permiten ambas crear un punto con una coordenada de tipo entero y la otra coordenada de tipo real? ¿Qué tipo devuelve el `getX` con la solucion sin generics y qué tipo devuelve el que tiene la solución con generics?

### Respuesta

La diferencia fundamental entre ambas soluciones radica en el nivel de precisión y control que el compilador ejerce sobre el tipado en tiempo de compilación.

En la solución sin generics (`PuntoNumber`), al utilizar el tipo base global `Number`, es perfectamente legal crear una instancia pasando una coordenada de tipo entero y otra de tipo real (ej. `new PuntoNumber(3, 4.5)`). En la solución genérica acotada (`PuntoGenerico<T>`), dado que ambas coordenadas se definen bajo el mismo parámetro `<T>`, el compilador forzará a que ambas pertenezcan exactamente al mismo tipo de número coincidente. Intentar instanciar `new PuntoGenerico<Integer>(3, 4.5)` fallará inmediatamente en la compilación por incoherencia de tipos.

Respecto a los métodos de acceso: en la solución sin generics, el getter `getX()` devuelve siempre una referencia del tipo polimórfico general `Number`. Si el código llamador requiere interactuar con el tipo específico (por ejemplo, obtener un entero para realizar una operación binaria específica), se verá obligado a realizar un *downcasting* manual. En la solución genérica, `getX()` devuelve exactamente el tipo concreto especificado en la instanciación (por ejemplo, si se tiene un `PuntoGenerico<Integer>`, `getX()` devolverá un tipo de dato `Integer` estático y seguro sin necesidad de casts intermedios).


## 11. Hagamos un ejemplo avanzado. El siguiente código, con interfaz `Punto`, que define un método `calcularDistanciaA(Punto p)`, junto con las implementaciones `Punto2D` y `Punto3D`. Añade generics para asegurarnos que la sobreescritura del método calcular distancia a otro `Punto` siempre es sobre un `Punto` del mismo tipo, evitando `instanceof` y el downcasting.

```java
public interface Punto { 
    public double distanciaA(Punto p); 
} 

public class Punto2D implements Punto { 
     private final double x, y; 
     public Punto2D(double x, double y) { 
        this.x = x; this.y = y; 
    } 

    @Override 
    public double distanciaA(Punto p) { 
        if (p instanceof Punto2D) { 
            Punto2D p2d = (Punto2D) p; 
            return Math.sqrt(Math.pow(x - p2d.x, 2) 
                    + Math.pow(y - p2d.y, 2)); 
        } else { 
            throw new RuntimeException("p debe ser Punto 2D"); 
        } 
    } 
} 
public class Punto3D implements Punto { 
    // Igual que Punto2D, pero con tres coordenadas
    ...
} 
```

### Respuesta

Para lograr un tipado seguro en las jerarquías de métodos que restringen sus parámetros al mismo tipo de la clase que los implementa, se debe parametrizar la interfaz genérica bajo una restricción autorreferencial.

Al declarar la interfaz `Punto<T>`, se asocia el parámetro `<T>` como el tipo del punto con el que es válido calcular la distancia. Cada clase concreta implementará la interfaz indicando su propio tipo como argumento genérico, obligando al compilador a forzar el tipo exacto en la sobreescritura y eliminando la necesidad de recurrir a operaciones de inspección de tipos en ejecución.

```java
// Interfaz parametrizada genéricamente
public interface Punto<T> { 
    public double distanciaA(T p); 
} 

public class Punto2D implements Punto<Punto2D> { 
     private final double x, y; 

     public Punto2D(double x, double y) { 
        this.x = x; 
        this.y = y; 
    } 

    @Override 
    public double distanciaA(Punto2D p2d) { 
        // Ya no es necesario usar instanceof ni realizar cast manual
        // El compilador garantiza que 'p2d' es obligatoriamente un Punto2D
        return Math.sqrt(Math.pow(x - p2d.x, 2) + Math.pow(y - p2d.y, 2)); 
    } 
} 

public class Punto3D implements Punto<Punto3D> { 
    private final double x, y, z;

    public Punto3D(double x, double y, double z) {
        this.x = x;
        this.y = y;
        this.z = z;
    }

    @Override
    public double distanciaA(Punto3D p3d) {
        // Sincronización de tipos garantizada estáticamente
        return Math.sqrt(Math.pow(x - p3d.x, 2) + Math.pow(y - p3d.y, 2) + Math.pow(z - p3d.z, 2));
    }
} 
```


## 12. Dado que `String` es subtipo de `Object`, ¿significa eso que `List<String>` es subtipo de `List<Object>`? ¿Y que `String[]` es subtipo de `Object[]`? Razona por qué la respuesta es diferente en cada caso y qué problema en tiempo de ejecución puede aparecer con los arrays. A partir de estos ejemplos, define qué significa que un tipo genérico sea **covariante**, **contravariante** o **invariante** respecto a su parámetro de tipo.

### Respuesta

No, que `String` sea subtipo de `Object` no implica en absoluto que `List<String>` sea subtipo de `List<Object>`. En Java, las clases parametrizadas con generics son estrictamente invariantes por defecto, lo que significa que no existe ninguna relación de herencia o asignación entre dos listas aunque sus tipos de parámetros estén relacionados jerárquicamente.

Sin embargo, en el caso de los arrays primitivos, la respuesta es diferente: en Java los arrays sí son covariantes (ej. `String[]` sí es considerado subtipo de `Object[]`). Esto se diseñó de este modo en las primeras versiones de Java para permitir la reutilización de métodos generales de ordenación y búsqueda antes de que existieran los generics, pero introduce un grave agujero de seguridad conocido como el "problema de la covarianza de arrays".

```java
// El problema con la covarianza de arrays en ejecucion
String[] palabras = new String[5];
Object[] objetos = palabras; // Compila sin problemas debido a la covarianza

// Lanza un fallo ArrayStoreException en tiempo de ejecucion
objetos[0] = Integer.valueOf(42); 
```

Debido a que el compilador permite la asignación anterior pero la máquina virtual conserva el tipo real del array en ejecución, intentar insertar un entero dentro del array de cadenas provoca un error catastrófico `ArrayStoreException` que interrumpe la aplicación. Los generics evitan este riesgo de raíz forzando la invarianza: intentar escribir `List<Object> l = new ArrayList<String>()` arrojará un error inmediato de compilación, impidiendo que el fallo ocurra en caliente.

A partir de estos comportamientos, se pueden definir formalmente las relaciones respecto a los parámetros de tipo:
* **Covariante:** Si un tipo `A` es subtipo de `B`, entonces `Contenedor<A>` es subtipo de `Contenedor<B>` (permite lectura segura).
* **Contravariante:** Si un tipo `A` es subtipo de `B`, entonces `Contenedor<B>` es subtipo de `Contenedor<A>` (permite escritura segura).
* **Invariante:** No se preserva ninguna relación entre los contenedores independientemente de la relación entre los tipos `A` y `B` (se exige una coincidencia estricta).


## 13. Java permite recuperar covarianza y contravarianza en tipos genéricos de forma controlada mediante **wildcards**. ¿Qué es un wildcard (`?`)? Muestra la diferencia entre `List<? extends T>` y `List<? super T>`, indicando en qué casos se usa cada uno. Pon dos ejemplos: (i) un método que reciba una lista de números y calcule su suma, usando `? extends`; (ii) un método que reciba una lista y le añada varios números enteros, usando `? super`.

### Respuesta

Un wildcard (comodín) en Java se representa mediante el símbolo de interrogación `?` y se utiliza para expresar un tipo desconocido o indeterminado en la parametrización de una estructura genérica. Permite flexibilizar las restricciones estrictas de la invarianza para dar soporte a operaciones polimórficas de forma controlada y segura.

La cláusula `? extends T` (covarianza de lectura) acota superiormente el comodín, indicando que el tipo de la colección es algún subtipo desconocido de la clase `T`. Es ideal para escenarios de solo lectura: al asegurar que todos los elementos heredan de `T`, es seguro recuperarlos tratándolos como `T`, pero no se permite insertar elementos (excepto `null`) porque no se puede saber el tipo exacto del subtipo real.

La cláusula `? super T` (contravarianza de escritura) acota inferiormente el comodín, indicando que el tipo de la colección es alguna superclase de la clase `T`. Se emplea en escenarios de solo escritura: al asegurar que el contenedor admite elementos compatibles con `T`, es seguro añadir instancias de `T` o de sus subclases, pero no es seguro realizar lecturas ya que no se puede determinar la naturaleza concreta del tipo de la lista más allá de ser `Object`.

```java
import java.util.List;

public class UtilidadesWildcards {
    // Ejemplo I: Uso de ? extends (Lectura segura de numeros para sumar)
    public static double sumarLista(List<? extends Number> lista) {
        double suma = 0.0;
        for (Number n : lista) {
            suma += n.doubleValue(); // Lectura permitida y segura
        }
        return suma;
    }

    // Ejemplo II: Uso de ? super (Escritura segura de enteros en una lista compatible)
    public static void agregarEnteros(List<? super Integer> lista) {
        // Escritura permitida porque la lista admite Integer o sus superclases
        lista.add(10);
        lista.add(20);
        lista.add(30);
    }
}
```
