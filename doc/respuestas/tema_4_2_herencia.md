<!--
Posible prompt:
<prompt>
Tengo un cuestionario con preguntas sobre "Herencia". Debes tener en cuenta que los conocimientos previos que tengo (y por tanto tus respuestas deben ser adaptadas), son:
- C/C++ sin orientación a objetos.
- Temas de Java previos: Clases y Objetos, Encapsulación, Excepciones y Composición.

Cada respuesta debe tener entre 2 - 4 párrafos de longitud (sin contar los trozos de código).

Por favor, escribe en impersonal las respuestas.

</prompt>
----
-->
# Tema 4.2. Herencia

## 1. En orientación a objetos, ¿qué es la **herencia** y su relación con "A es-un B"?. Explica las dos implicaciones principales: (1) **compatibilidad de tipos** y (2) **herencia de estado y comportamiento**. Pon un ejemplo en Java muy sencillo, donde un `Soldado` tiene un `nombre` (privado) y un método `saludar()` que muestra su nombre. Hay dos subtipos: un `Artillero`, que es capaz de disparar cohetes y un `Zapador` que pone minas, ambos heredan el atributo nombre y la capacidad de saludar. Además, y de forma específica, el artillero tiene un número de cohetes y el zapador un número de minas, accesibles mediante "getters" específicos. Respecto a la compatibilidad de tipos, aprovechémosla: crea un array de `Soldado`, mete varios de distinto tipo (son todos compatibles con `Soldado`). Recórrela y que todos te saluden.

### Respuesta

La herencia es un mecanismo fundamental de la programación orientada a objetos que permite crear una clase nueva (subclase o clase hija) basándose en una clase ya existente (superclase o clase madre). Esta relación se conceptualiza bajo la semántica "A es-un B". Significa que cualquier objeto de la subclase comparte la naturaleza de la superclase, pero añadiendo especializaciones en forma de atributos y métodos propios.

La primera implicación principal es la herencia de estado y comportamiento. La subclase adquiere automáticamente todos los campos y métodos definidos en la superclase. Esto fomenta la reutilización del código y evita redundancias al agrupar los datos comunes en un único lugar.

La segunda implicación es la compatibilidad de tipos. Dado que "A es-un B", una variable con una referencia de tipo de la superclase puede almacenar de forma transparente objetos reales de cualquiera de sus subclases. Esto permite tratar colecciones heterogéneas de objetos especializados de forma unificada en el código principal, mejorando la extensibilidad.

```java
public class Soldado {
    private final String nombre;

    public Soldado(String nombre) {
        if (nombre == null || nombre.trim().isEmpty()) {
            throw new IllegalArgumentException("El nombre del soldado no puede estar vacio.");
        }
        this.nombre = nombre;
    }

    public String getNombre() {
        return nombre;
    }

    public void saludar() {
        System.out.println("Hola, soy el soldado " + nombre);
    }
}

public class Artillero extends Soldado {
    private final int cohetes;

    public Artillero(String nombre, int cohetes) {
        super(nombre);
        this.cohetes = cohetes;
    }

    public int getCohetes() {
        return cohetes;
    }

    public void dispararCohete() {
        System.out.println(getNombre() + " disparando cohete.");
    }
}

public class Zapador extends Soldado {
    private final int minas;

    public Zapador(String nombre, int minas) {
        super(nombre);
        this.minas = minas;
    }

    public int getMinas() {
        return minas;
    }

    public void ponerMina() {
        System.out.println(getNombre() + " colocando mina.");
    }
}
```

```java
public class PrincipalSoldados {
    public static void main(String[] args) {
        // Aprovechando la compatibilidad de tipos
        Soldado[] peloton = new Soldado[3];
        peloton[0] = new Artillero("Ivan", 5);
        peloton[1] = new Zapador("Lucas", 10);
        peloton[2] = new Soldado("Tomas");

        System.out.println("El peloton saludando:");
        for (Soldado s : peloton) {
            s.saludar(); // Todos son tratados como Soldado
        }
    }
}
```


## 2. Al crear los soldados concretos, ¿cuántos constructores se ejecutan y en qué orden? ¿Qué significa `super` dentro de un constructor? Si la clase base no tiene visible el constructor sin parámetros, ¿debo llamar a `super` siempre? 

### Respuesta

Cuando se instancia un objeto de una subclase, se ejecutan de forma encadenada los constructores de toda su línea de ascendencia jerárquica, comenzando desde la clase base más alta (`Object`) y bajando en orden sucesivo hasta la subclase concreta. En el ejemplo de `Artillero`, al hacer `new Artillero(...)` se ejecutan dos constructores de forma secuencial: primero el de `Soldado` y después el de `Artillero`.

La palabra clave `super` dentro de un constructor se utiliza para invocar de forma explícita al constructor de la superclase inmediata. Debe ser la primera instrucción ejecutable del bloque del constructor de la subclase. Esto asegura que la parte del estado del objeto que depende de la superclase se inicialice de forma correcta antes de que se ejecute la inicialización de la subclase.

Si la clase base no expone un constructor sin parámetros (ya sea porque se ha definido uno con parámetros y el compilador ha retirado el constructor por defecto, o porque es privado), la subclase está obligada a realizar una llamada explícita mediante `super(...)` en su constructor, pasándole los argumentos necesarios. De no hacerse, el compilador reportará un error en tiempo de compilación al no poder insertar automáticamente la llamada implícita a `super()`.


## 3. Respecto a los objetos de subclases en memoria, los atributos privados de la superclase, ¿forman parte de una instancia de la subclase en memoria? En caso afirmativo ¿implica que se puedan usar desde el código de la subclase? Explícalo con el ejemplo de `Soldado` y alguna de sus subclases.

### Respuesta

Sí, los atributos privados de la superclase forman parte física del objeto en memoria cuando se instancia una subclase. En el heap de la JVM, el objeto de tipo `Artillero` reserva espacio para almacenar el campo `nombre` (declarado como privado en `Soldado`), además del campo `cohetes` (declarado en la propia clase `Artillero`). Estructuralmente, la subclase engloba por completo a la superclase.

Sin embargo, esto no implica que se puedan acceder o utilizar directamente desde el código de la subclase. Debido a los principios de encapsulación y ocultación de información, la palabra clave `private` restringe la visibilidad de los atributos única y exclusivamente a la clase en la que fueron definidos.

Por tanto, dentro de los métodos de la clase `Artillero`, no es legal escribir expresiones que accedan directamente a `this.nombre`. Para interactuar con dicho estado, la subclase debe hacer uso de los métodos públicos o protegidos expuestos por la superclase, como por ejemplo invocar al método `getNombre()`. Esto garantiza que la superclase mantenga el control total sobre las invariantes y reglas de sus propios datos.


## 4. ¿Qué implica en términos de **extensibilidad** de código el hecho de que sean compatibles a nivel de tipos? Ilustra esto añadiendo un nuevo tipo de `Soldado` y demostrando que el código para pedir el saludo a todos los soldados no se modifica.

### Respuesta

La compatibilidad a nivel de tipos (también conocida como polimorfismo de inclusión) proporciona una de las mayores ventajas de la POO en términos de extensibilidad y mantenimiento de software. Permite diseñar un sistema basado en interfaces o clases base estables de modo que, si en el futuro se añaden nuevos subtipos especializados, no es necesario modificar ni recompilar el código ya existente que interactúa con el tipo general.

En C, añadir un nuevo tipo de estructura requeriría modificar las funciones y los bucles manuales mediante condicionales (`switch` o `if`) para verificar el tipo exacto del dato antes de procesarlo. En Java, gracias a que cualquier nuevo tipo "es-un" `Soldado`, el código del programa cliente permanece completamente intacto al introducir extensiones en la jerarquía.

```java
// Se añade un nuevo subtipo de Soldado sin tocar el resto del codigo del sistema
public class Medico extends Soldado {
    private final int botiquines;

    public Medico(String nombre, int botiquines) {
        super(nombre);
        this.botiquines = botiquines;
    }

    public int getBotiquines() {
        return botiquines;
    }

    public void curar() {
        System.out.println(getNombre() + " curando a un compañero.");
    }
}
```

Al integrarlo en la simulación anterior, el bucle que solicita el saludo no experimenta ningún cambio:

```java
public class PrincipalExtensible {
    public static void main(String[] args) {
        Soldado[] peloton = new Soldado[3];
        peloton[0] = new Artillero("Ivan", 5);
        peloton[1] = new Zapador("Lucas", 10);
        peloton[2] = new Medico("Elena", 3); // Medico es 100% compatible con Soldado

        // El codigo del recorrido NO cambia en absoluto
        System.out.println("El peloton saludando con la extension:");
        for (Soldado s : peloton) {
            s.saludar(); 
        }
    }
}
```


## 5. En Java, cuando trabajo con referencias y herencia. ¿Puedo tener una referencia del supertipo que apunte a objetos reales de un subtipo? ¿Puedo invocar con la referencia del supertipo a métodos públicos del subtipo? ¿En qué consiste el **"upcasting"** y el **"downcasting"**? ¿Qué es el `instanceof`? Pon un ejemplo de recorrido de un array de `Soldado`, comprobando que, si el objeto real es un `Artillero`, solicite el número de cohetes que tiene y los imprima.

### Respuesta

Sí, en Java es perfectamente legal y habitual que una referencia del supertipo apunte a un objeto real de un subtipo. No obstante, a través de esa referencia de la superclase, el compilador solo permite invocar a aquellos métodos públicos declarados en la propia superclase. No es posible invocar directamente a métodos específicos de la subclase, ya que el compilador comprueba la firma del tipo de la referencia, no del objeto real en memoria.

El *upcasting* consiste en asignar una referencia de un subtipo a una variable del supertipo (ej. `Soldado s = new Artillero(...)`). Es un proceso automático y seguro que el compilador realiza de forma implícita. Por el contrario, el *downcasting* es la conversión explícita de una referencia del supertipo a un subtipo (ej. `Artillero a = (Artillero) s`). Requiere un "cast" explícito y conlleva riesgos de error en tiempo de ejecución si el objeto real no coincide con el tipo esperado.

Para evitar errores catastróficos (`ClassCastException`) al realizar un *downcasting*, se utiliza el operador `instanceof`. Este operador evalúa de forma dinámica si el objeto real referenciado es compatible con un tipo concreto, devolviendo un booleano. A partir de Java 16, existe además el "Pattern Matching for instanceof", que realiza el cast automáticamente en caso de éxito.

```java
public class PrincipalChequeo {
    public static void main(String[] args) {
        Soldado[] peloton = {
            new Artillero("Ivan", 5),
            new Zapador("Lucas", 10),
            new Soldado("Tomas")
        };

        for (Soldado s : peloton) {
            s.saludar();
            // Comprobacion con instanceof y downcasting tradicional
            if (s instanceof Artillero) {
                Artillero a = (Artillero) s;
                System.out.println("  -> Es un artillero con " + a.getCohetes() + " cohetes.");
            }
        }
    }
}
```


## 6. Respecto a la ocultación de información y herencia, ¿qué significa acceso **"protegido"** de métodos y/o atributos? ¿Cómo se implementa en Java? Pon un ejemplo de uso de en la clase `Soldado` para que su nombre sea protegido y pueda usarse en el método de poner bombas del `Zapador`.

### Respuesta

El acceso protegido es un nivel de visibilidad intermedio diseñado en la programación orientada a objetos que flexibiliza las restricciones estrictas del acceso privado. Permite que los miembros (atributos o métodos) de una clase sean accesibles tanto para la propia clase como para cualquiera de sus subclases en la jerarquía, así como para otras clases que residan dentro del mismo paquete de la aplicación.

En Java, se implementa anteponiendo la palabra clave `protected` en la declaración del atributo o método. Esto resulta útil para evitar la redundancia de tener que recurrir a getters y setters públicos en relaciones de herencia estrechas, permitiendo un acceso directo a la estructura interna para optimizar código o simplificar la lógica de las subclases.

Sin embargo, el uso de atributos protegidos debe tratarse con extrema cautela. Declarar campos como `protected` debilita el encapsulamiento de la clase base, ya que cualquier subclase creada en el paquete podría alterar arbitrariamente el valor del atributo y romper las invariantes de clase que la superclase intentaba defender.

```java
public class SoldadoConNombreProtegido {
    protected String nombre; // Acceso protegido

    public SoldadoConNombreProtegido(String nombre) {
        this.nombre = nombre;
    }

    public void saludar() {
        System.out.println("Hola, soy " + nombre);
    }
}

public class ZapadorConNombreProtegido extends SoldadoConNombreProtegido {
    private final int minas;

    public ZapadorConNombreProtegido(String nombre, int minas) {
        super(nombre);
        this.minas = minas;
    }

    public void ponerMina() {
        // Acceso directo a 'nombre' porque esta declarado como protected en la clase padre
        System.out.println("El zapador " + nombre + " coloca una mina.");
    }
}
```


## 7. En los lenguajes orientados a objetos ¿hay una **clase base** para todos los objetos? ¿Ocurre en todos los lenguajes? ¿Qué ocurre en Java?

### Respuesta

En muchos lenguajes de programación orientados a objetos, existe una clase raíz universal de la cual heredan implícitamente todas las demás clases si no se especifica una herencia distinta de forma manual. Este diseño unifica la jerarquía de tipos de la aplicación y proporciona un conjunto mínimo de comportamientos comunes y necesarios para cualquier objeto del sistema.

Sin embargo, esto no ocurre de forma idéntica en todos los lenguajes. En C++, por ejemplo, no existe una clase base común única; se pueden definir múltiples jerarquías inconexas independientes. En cambio, lenguajes como Java, C#, Smalltalk o Python implementan una raíz única.

En Java, la clase base universal se denomina `Object` (ubicada en el paquete `java.lang`). Todas las clases que se definan heredan automáticamente de `Object` de manera implícita. Esto dota a cualquier objeto de métodos estandarizados muy importantes para la plataforma, como `equals()`, `hashCode()`, `toString()`, `clone()` y `getClass()`.


## 8. ¿Qué es la **"herencia múltiple"**? ¿Existe en Java herencia múltiple?

### Respuesta

La herencia múltiple es la capacidad que ofrece un lenguaje de programación orientada a objetos para que una clase nueva pueda derivarse de dos o más superclases simultáneamente (ej. una clase `Pato` que herede de `Ave` y `Nadador`). Esto permite combinar atributos y comportamientos de múltiples fuentes independientes dentro de un único subtipo.

Aunque conceptualmente potente, la herencia múltiple introduce graves problemas de ambigüedad en el diseño de software. El ejemplo más célebre es el "Problema del Diamante": si una subclase hereda de dos clases que a su vez derivan de una clase base común, y ambas superclases intermedias sobreescriben un mismo método, el compilador no puede determinar de forma automática cuál versión del método debe ejecutar en el objeto final.

Para evitar estas inconsistencias y mantener el modelo simple y robusto, Java prohíbe de forma estricta la herencia múltiple de clases. En Java, una clase solo puede extender (`extends`) a una única superclase. Sin embargo, para recuperar la flexibilidad de modelar múltiples comportamientos, Java permite a una clase implementar múltiples interfaces simultáneamente, lo cual proporciona una alternativa limpia y segura sin conflictos estructurales de estado.


## 9. Las excepciones en los lenguajes orientados a objetos son objetos. Por tanto, se pueden crear excepciones personalizadas. Pon un ejemplo en Java de una excepción personalizada (`UsuarioNoEncontradoException`), que sea *no controlada* y que además este compuesto con un `Usuario`, para saber qué `Usuario` dio el problema. Permite además que se pueda incluir la causa, es decir, sobrecarga el constructor para tener una versión que permita añadir la causa subyacente. 

### Respuesta

Dado que las excepciones en Java son objetos ordinarios que forman parte de una jerarquía de clases encabezada por `Throwable`, es muy sencillo crear excepciones personalizadas que se adapten a las necesidades funcionales del dominio de la aplicación. Para modelar una excepción que sea no controlada, la nueva clase debe heredar obligatoriamente de `RuntimeException`.

Al tratarse de un objeto, se puede enriquecer la excepción agregando atributos adicionales protegidos por encapsulación, como en este caso asociar la instancia del `Usuario` que provocó el error para facilitar la depuración técnica. Además, se sobrecargan los constructores para soportar el patrón de encadenamiento de causas, permitiendo pasar un objeto `Throwable` como causa de origen.

```java
public class Usuario {
    private final String login;

    public Usuario(String login) {
        this.login = login;
    }

    public String getLogin() { return login; }
}

public class UsuarioNoEncontradoException extends RuntimeException {
    private final Usuario usuarioAsociado;

    // Constructor sin causa
    public UsuarioNoEncontradoException(String mensaje, Usuario usuarioAsociado) {
        super(mensaje);
        this.usuarioAsociado = usuarioAsociado;
    }

    // Constructor sobrecargado con soporte para causa (encadenamiento)
    public UsuarioNoEncontradoException(String mensaje, Usuario usuarioAsociado, Throwable causa) {
        super(mensaje, causa);
        this.usuarioAsociado = usuarioAsociado;
    }

    public Usuario getUsuarioAsociado() {
        return usuarioAsociado;
    }
}
```


## 10. Herencia vs. Composición. Se dice que no se debe emplear herencia simplemente por reutilizar código, es decir, que si quiero reutilizar código simplemente, no debo pensar en herencia como primera opción ¿por qué?

### Respuesta

La herencia es un mecanismo potente pero intrusivo que establece un acoplamiento extremadamente fuerte entre la superclase y la subclase. Utilizar herencia únicamente para ahorrarse escribir unas líneas de código reutilizando funciones preexistentes suele conducir a diseños de software rígidos, frágiles y semánticamente incorrectos.

Si se utiliza la herencia para una relación que no cumple con el concepto estricto de "es-un", la subclase quedará atada al contrato de la superclase y se verá obligada a exponer a través de su interfaz pública métodos heredados que carecen por completo de sentido para su verdadera naturaleza. Por ejemplo, si se heredara una clase `Pila` de una clase `Lista`, la pila expondría métodos para insertar en posiciones intermedias, violando sus propias invariantes.

Además, cualquier modificación futura en la implementación o la estructura interna de la superclase puede romper el funcionamiento de las subclases de manera inesperada. Para reutilizar comportamiento de forma segura, la primera opción debe ser siempre agrupar e interactuar con los objetos a través de relaciones de uso o composición, manteniendo las clases pequeñas, independientes y con responsabilidades nítidamente acotadas.


## 11. Herencia vs. Composición. Se dice que se debe *"favorecer la composición frente a la herencia"*, ¿por qué?

### Respuesta

Favorecer la composición frente a la herencia es una de las reglas de oro de la arquitectura de software. La composición ofrece un acoplamiento mucho más débil y una flexibilidad infinitamente mayor debido a que la relación se define a través de referencias a objetos ("tiene-un") en lugar de uniones a nivel de tipos en tiempo de compilación.

Una de las ventajas cruciales es la flexibilidad dinámica. En la herencia, el comportamiento del objeto se fija de forma rígida en tiempo de compilación y no puede ser alterado dinámicamente en ejecución. Con la composición, al contener referencias a interfaces o clases componentes, se puede sustituir fácilmente el objeto componente interno por otro diferente en tiempo de ejecución, alterando instantáneamente la estrategia de comportamiento del objeto contenedor.

Asimismo, la composición promueve clases con mayor cohesión y responsabilidades únicas. Dado que el objeto contenedor delega el trabajo especializado en sus componentes individuales, se reduce la complejidad de la lógica interna y se facilita el testeo unitario, al poder aislar y mockear las partes componentes de forma muy sencilla, algo considerablemente más complejo en jerarquías de herencia rígidas.


## 12. Herencia vs. Composición. Se dice que la *"herencia rompe la encapsulación"*, ¿a qué se refiere esto?

### Respuesta

La afirmación de que la herencia rompe la encapsulación se refiere a que la subclase depende de manera íntima de los detalles de implementación interna de su superclase para funcionar correctamente. A diferencia de las relaciones entre clases ordinarias, donde solo se interactúa a través de la interfaz pública, en la herencia el límite de privacidad entre padre e hijo se vuelve difuso.

Si la superclase sufre cambios internos (como modificar el orden en el que sus propios métodos públicos se invocan entre sí), la subclase puede experimentar fallos de lógica de forma inesperada sin haber alterado una sola línea de su propio código. Por ejemplo, si una subclase de un conjunto cuenta los elementos que se añaden sobreescribiendo el método `add` y `addAll`, y en una nueva versión la superclase modifica `addAll` para que llame internamente a `add`, la subclase contará los elementos de forma duplicada.

Este acoplamiento estrecho obliga al diseñador de la superclase a documentar minuciosamente cómo interactúan sus métodos internos o a sellar la clase para impedir la herencia. Al romper el aislamiento natural de los datos, la herencia expone el estado interno del padre a los caprichos del diseño del hijo, dificultando la evolución independiente de ambas piezas de código.


## 13. Pongamos un ejemplo de dos alternativas para lo mismo. Tenemos un `Estudiante` y un `Trabajador`, ambos tienen datos en común: el DNI y el nombre. Modelemos esto de dos formas: uno por herencia, con una superclase `Persona`, y otro con composición, con una clase `DatosPersonales`. Se debe recibir una instancia de `DatosPersonales` en el constructor de la clase `Estudiante` y `Trabajador`.

### Respuesta

Para plasmar la diferencia estructural de forma empírica en Java, se presentan las dos alternativas de diseño. En el enfoque basado en herencia, `Estudiante` y `Trabajador` extienden a la superclase común `Persona`, estableciendo una relación semántica estática y uniendo sus destinos a nivel de tipo en compilación.

En el enfoque basado en composición, `Estudiante` y `Trabajador` son clases independientes y autónomas. Cada una de ellas aloja una variable miembro privada de tipo `DatosPersonales` y recibe una instancia de la misma en sus respectivos constructores. Esto permite delegar la lectura del DNI y el nombre de forma limpia sin arrastrar dependencias jerárquicas rígidas.

```java
// ==================== ALTERNATIVA A: HERENCIA ====================

public class Persona {
    private final String dni;
    private final String nombre;

    public Persona(String dni, String nombre) {
        this.dni = dni;
        this.nombre = nombre;
    }

    public String getDni() { return dni; }
    public String getNombre() { return nombre; }
}

public class EstudianteHerencia extends Persona {
    private final String expediente;

    public EstudianteHerencia(String dni, String nombre, String expediente) {
        super(dni, nombre);
        this.expediente = expediente;
    }

    public String getExpediente() { return expediente; }
}

public class TrabajadorHerencia extends Persona {
    private final double salario;

    public TrabajadorHerencia(String dni, String nombre, double salario) {
        super(dni, nombre);
        this.salario = salario;
    }

    public double getSalario() { return salario; }
}

// ==================== ALTERNATIVA B: COMPOSICIÓN ====================

public class DatosPersonales {
    private final String dni;
    private final String nombre;

    public DatosPersonales(String dni, String nombre) {
        this.dni = dni;
        this.nombre = nombre;
    }

    public String getDni() { return dni; }
    public String getNombre() { return nombre; }
}

public class EstudianteComposicion {
    private final DatosPersonales datos; // Composición
    private final String expediente;

    public EstudianteComposicion(DatosPersonales datos, String expediente) {
        if (datos == null) {
            throw new IllegalArgumentException("Los datos personales son obligatorios.");
        }
        this.datos = datos;
        this.expediente = expediente;
    }

    public DatosPersonales getDatos() { return datos; }
    public String getExpediente() { return expediente; }
}

public class TrabajadorComposicion {
    private final DatosPersonales datos; // Composición
    private final double salario;

    public TrabajadorComposicion(DatosPersonales datos, double salario) {
        if (datos == null) {
            throw new IllegalArgumentException("Los datos personales son obligatorios.");
        }
        this.datos = datos;
        this.salario = salario;
    }

    public DatosPersonales getDatos() { return datos; }
    public double getSalario() { return salario; }
}
```
