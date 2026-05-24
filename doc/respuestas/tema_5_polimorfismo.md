<!--
Posible prompt:
<prompt>
Tengo un cuestionario con preguntas sobre "Polimorfismo". Debes tener en cuenta que los conocimientos previos que tengo (y por tanto tus respuestas deben ser adaptadas), son:
- C/C++ sin orientación a objetos.
- Temas de Java previos: Clases y Objetos, Encapsulación, Excepciones, Composición y Herencia.

Cada respuesta debe tener entre 2 - 4 párшивos de longitud (sin contar los trozos de código).

Por favor, escribe en impersonal las respuestas.

</prompt>
----
-->
# Tema 5. Polimorfismo

## 1. Brevemente, ¿qué es el **"polimorfismo"** y para qué sirve en programación orientada a objetos? ¿qué es la **"sobreescritura"** de métodos?

### Respuesta

El polimorfismo es la capacidad que poseen los objetos de responder de distintas maneras a una misma llamada o mensaje, dependiendo del tipo real del objeto que recibe la invocación en tiempo de ejecución. Sirve para construir sistemas altamente flexibles y desacoplados, permitiendo interactuar con colecciones de diversos objetos a través de una interfaz común sin necesidad de conocer su implementación interna específica.

La sobreescritura (o *overriding*) es el mecanismo concreto mediante el cual una subclase redefine un método heredado de su superclase, manteniendo exactamente el mismo nombre, tipo de retorno y parámetros (la misma firma). Esto permite que la subclase modifique o personalice el comportamiento original para adaptarlo a su propia naturaleza.

En combinación con la compatibilidad de tipos, la sobreescritura es lo que hace posible el polimorfismo. Cuando un cliente invoca un método sobre una referencia de la superclase, el entorno de ejecución determina dinámicamente cuál es la versión sobreescrita específica que debe llamarse en base a la clase concreta del objeto real.


## 2. ¿En qué consiste la **"ligadura dinámica"** o **"enlace tardío"**? ¿qué relación tiene con el polimorfismo? ¿hay que indicarlos explícitamente al programar o depende esto del lenguaje? Compara C++ y Java. Indicalo después también para Python.

### Respuesta

La ligadura dinámica (o enlace tardío) es la resolución que realiza la máquina virtual o el entorno de ejecución en tiempo de ejecución (y no en tiempo de compilación) para asociar la invocación de un método con el bloque de código máquina real que le corresponde en base al objeto concreto sobre el que se realiza la llamada. Es el mecanismo técnico subyacente que sustenta el comportamiento polimórfico del software.

A diferencia del enlace estático, donde el compilador determina de forma rígida la dirección de la función examinando únicamente el tipo de la referencia, en la ligadura dinámica el compilador genera instrucciones especiales que consultan una tabla de métodos virtuales (*vtable*) del objeto en memoria antes de saltar a la ejecución de la función.

La necesidad de indicar explícitamente este comportamiento varía según el lenguaje. En C++, la ligadura dinámica debe activarse explícitamente anteponiendo la palabra clave `virtual` en la declaración del método de la clase base. De lo contrario, C++ aplica por defecto ligadura estática por motivos de rendimiento. En Java, en cambio, todos los métodos no estáticos y no privados utilizan ligadura dinámica por defecto, sin necesidad de marcarlos explícitamente.

En Python, al ser un lenguaje de tipado dinámico basado en la filosofía de "duck typing", no existe una fase de compilación rígida con tipos estáticos. Todas las resoluciones de métodos se realizan dinámicamente en tiempo de ejecución buscando los atributos por nombre en el diccionario de la instancia, por lo que el polimorfismo y la ligadura dinámica son intrínsecos a la propia naturaleza del lenguaje.


## 3. Pon un ejemplo sencillo en Java, de un `Soldado`, con un método `saluda`, con dos subclases: `Zapador` y `Artillero`, donde `Zapador` sobreescribe el método `saludar`, sustituyendo por completo su comportamiento. Ilustra el funcionamiento del polimorfismo creando un array de `Soldados` de dos tipos y luego recorriéndolo empleando referencias de tipo `Soldado` y llamando a `saludar`.

### Respuesta

Para materializar el polimorfismo en Java de forma práctica, se define una clase base y sus correspondientes derivaciones. En este ejemplo, `Zapador` y `Artillero` extienden a la clase `Soldado`. `Zapador` redefine (sobreescribe) por completo el comportamiento del método `saludar` para que se adapte a su especialidad.

Al almacenar las diferentes instancias en un array de tipo `Soldado` y realizar el recorrido, la máquina virtual de Java aplica de forma automática la ligadura dinámica, invocando el comportamiento específico de cada objeto real a pesar de ser tratados todos bajo la referencia general del padre.

```java
public class Soldado {
    private final String nombre;

    public Soldado(String nombre) {
        this.nombre = nombre;
    }

    public String getNombre() { return nombre; }

    public void saludar() {
        System.out.println("Soldado " + nombre + " presentándose.");
    }
}

public class Artillero extends Soldado {
    public Artillero(String nombre) {
        super(nombre);
    }
    // Artillero hereda el saludo por defecto de Soldado
}

public class Zapador extends Soldado {
    public Zapador(String nombre) {
        super(nombre);
    }

    @Override
    public void saludar() {
        // Se sobreescribe por completo el comportamiento original
        System.out.println("¡A las órdenes! Aquí el zapador " + getNombre() + ", despejando el camino.");
    }
}
```

```java
public class PrincipalPolimorfismo {
    public static void main(String[] args) {
        Soldado[] ejercito = new Soldado[2];
        ejercito[0] = new Artillero("Ivan");
        ejercito[1] = new Zapador("Lucas");

        System.out.println("Recorrido polimórfico:");
        for (Soldado s : ejercito) {
            s.saludar(); // Ivan saluda de forma normal, Lucas usa su saludo de zapador
        }
    }
}
```


## 4. Si sobreescribo un método, ¿puedo invocar el método base para trabajar a partir de su resultado? Haz que zapador cambie ligeramente la forma de saludar, que salude de forma normal, tal cual hace el soldado base, pero que además añada un "ZAPADOR A SUS ORDENES" ¿qué palabra clave del lenguaje has usado para invocar al método de la clase base?

### Respuesta

Sí, al sobreescribir un método en una subclase es una práctica muy común y recomendada invocar al comportamiento de la clase base para reaprovechar su lógica y añadir o complementar funcionalidad a partir de su resultado. Esto evita tener que duplicar código base que ya es correcto.

Para invocar al método de la clase base desde el interior del método sobreescrito, se utiliza la palabra clave `super` seguida del punto y el nombre del método (ej. `super.saludar()`). Esto le indica explícitamente al compilador y al entorno de ejecución que ignore temporalmente la ligadura dinámica de la subclase y salte de forma directa a ejecutar el código definido en la superclase.

```java
public class ZapadorComplementario extends Soldado {
    public ZapadorComplementario(String nombre) {
        super(nombre);
    }

    @Override
    public void saludar() {
        // Se invoca el saludo base (reutilizando su codigo)
        super.saludar();
        // Se complementa con comportamiento especifico adicional
        System.out.println("  -> ZAPADOR A SUS ORDENES.");
    }
}
```


## 5. Al sobreescribir un método en Java, ¿qué restricciones existen sobre los tipos de los parámetros y el tipo de retorno? ¿Qué diferencia hay entre sobreescritura (*overriding*) y sobrecarga (*overloading*)? ¿Para qué sirve la anotación `@Override` y por qué es recomendable usarla siempre?

### Respuesta

Al sobreescribir un método en Java, existen restricciones estrictas para asegurar que el contrato de la superclase no se rompa. Los tipos de los parámetros de entrada deben ser exactamente idénticos en tipo, número y orden. El tipo de retorno debe ser el mismo, o bien un subtipo del tipo de retorno original (lo que se conoce como retorno covariante), y la visibilidad del método en la subclase no puede ser más restrictiva que la declarada en el padre (ej. un método público no puede sobreescribirse como privado).

La diferencia fundamental entre sobreescritura (*overriding*) y sobrecarga (*overloading*) radica en la firma del método y en el momento en que se resuelve la llamada. La sobreescritura ocurre en clases con relación de herencia, donde se redefine un método con la misma firma y la llamada se resuelve dinámicamente en tiempo de ejecución. La sobrecarga consiste en definir múltiples métodos con el mismo nombre pero con diferentes firmas (distintos parámetros) en una misma clase, y su resolución es realizada estáticamente por el compilador en tiempo de compilación.

La anotación `@Override` sirve para indicarle explícitamente al compilador la intención de sobreescribir un método de la superclase. Es altamente recomendable usarla siempre porque actúa como una salvaguarda: si se comete un error tipográfico en el nombre del método o en los tipos de sus parámetros, el compilador lo detectará inmediatamente como un error de compilación en lugar de tratarlo silenciosamente como una sobrecarga, lo que evita la introducción de bugs muy difíciles de rastrear.


## 6. Entonces, cuando se estudia Java, ¿se emplea el polimorfismo desde el principio? Por ejemplo, sobreescribiendo `toString` o sobreescribiendo `equals`, ¿ya estoy usando polimorfismo?

### Respuesta

Sí, en Java se emplea el polimorfismo prácticamente desde los primeros días de aprendizaje del lenguaje, a menudo de forma inconsciente. Debido a que todas las clases heredan implícitamente de la clase universal `Object`, cualquier acción de redefinición de sus métodos esenciales constituye un caso directo de sobreescritura y polimorfismo.

Cuando se sobreescribe el método `toString()` en una clase personalizada para mostrar un formato amigable de sus atributos, y posteriormente se pasa una instancia de esa clase a `System.out.println()`, la biblioteca interna de Java (que recibe una referencia genérica de tipo `Object`) invoca polimórficamente a nuestro método sobreescrito mediante ligadura dinámica.

Lo mismo sucede al sobreescribir `equals()`. Cuando colecciones de la API estándar de Java o algoritmos de comparación manipulan los datos genéricos para verificar si dos elementos son equivalentes, hacen uso del polimorfismo ejecutando las reglas de comparación específicas implementadas en nuestra subclase, demostrando que el diseño orientado a objetos de Java está cimentado en el polimorfismo desde su núcleo.


## 7. ¿Qué es una **"clase abstracta"**? ¿Qué es un **"método abstracto"**? ¿Puedo crear instancias de una clase abstracta? Pongamos un ejemplo en Java: Redefinamos `Soldado`, hagamos que, además del método `saluda` que ya tenía, tenga un método `atacar`, que sea abstracto y que cada tipo de soldado haga su acción cuando se le pida atacar. ¿Donde debemos poner `abstract`?

### Respuesta

Una clase abstracta es una clase diseñada específicamente para servir de plantilla común dentro de una jerarquía de herencia, agrupando la estructura básica y el comportamiento general de sus subclases, pero impidiendo su propia instanciación. Se utiliza cuando el concepto que representa es demasiado genérico para existir por sí mismo en el dominio (por ejemplo, existe el concepto genérico de "Soldado", pero en el campo de batalla siempre se es un tipo concreto de soldado como "Artillero" o "Zapador").

Un método abstracto es la declaración de un comportamiento en una clase base sin proporcionar su implementación real (sin cuerpo `{}`). Establece una obligación contractual para todas las subclases no abstractas: estas deben sobreescribir e implementar obligatoriamente dicho método de forma obligatoria para poder ser compiladas.

No es posible crear instancias directas de una clase abstracta mediante el operador `new`. Para indicar que una clase o un método son abstractos en Java, se debe anteponer la palabra clave `abstract` en sus respectivas declaraciones. El marcador `abstract` se sitúa en la cabecera de la clase y justo antes del tipo de retorno en la firma del método.

```java
// Declaracion de la clase como abstracta
public abstract class SoldadoAbstracto {
    private final String nombre;

    public SoldadoAbstracto(String nombre) {
        this.nombre = nombre;
    }

    public String getNombre() { return nombre; }

    public void saludar() {
        System.out.println("Hola, soy " + nombre);
    }

    // Metodo abstracto: define el que, delegando el como en las subclases
    public abstract void atacar();
}

public class ArtilleroConAtaque extends SoldadoAbstracto {
    public ArtilleroConAtaque(String nombre) {
        super(nombre);
    }

    @Override
    public void atacar() {
        System.out.println("El artillero " + getNombre() + " realiza un disparo de cohete.");
    }
}

public class ZapadorConAtaque extends SoldadoAbstracto {
    public ZapadorConAtaque(String nombre) {
        super(nombre);
    }

    @Override
    public void atacar() {
        System.out.println("El zapador " + getNombre() + " activa una mina de proximidad.");
    }
}
```


## 8. ¿Qué efecto tiene la palabra clave `final` sobre métodos y clases en Java? ¿Cómo se relaciona con el polimorfismo? ¿Conoces algún ejemplo de clase `final` en la propia API estándar de Java?

### Respuesta

La palabra clave `final` en Java actúa como un limitador de extensibilidad. Cuando se aplica a una clase completa, impide de forma absoluta que otras clases la extiendan (hereden de ella). Cuando se aplica a un método individual, permite que la clase sea heredada pero prohíbe que las subclases sobreescriban dicho método específico.

Se relaciona directamente con el polimorfismo de forma restrictiva. Al declarar un método o una clase como `final`, se desactiva la posibilidad de aplicar ligadura dinámica sobre ellos, ya que no puede existir una versión sobreescrita diferente. El compilador puede optimizar estas llamadas de forma estática en tiempo de compilación (*inlining*), mejorando sensiblemente el rendimiento al evitar consultas a la tabla virtual de métodos.

Un ejemplo clásico e importantísimo de clase `final` en la API estándar de Java es la clase `String`. Ha sido diseñada como final por motivos cruciales de seguridad y optimización de memoria (para garantizar que el comportamiento de las cadenas no pueda ser alterado maliciosamente por subclases de terceros y permitir el funcionamiento del pool de strings). Otros ejemplos destacados son las clases envoltura como `Integer` o `Double`, y la clase `Math`.


## 9. En Java, qué son las **"interfaces"**? ¿Son como clases abstractas? ¿Una clase puede implementar más de una interfaz?

### Respuesta

En Java, una interfaz es una especificación puramente abstracta de comportamiento que define un conjunto de firmas de métodos que las clases pueden optar por implementar. Se puede visualizar como un contrato riguroso: cualquier clase que implemente una interfaz se compromete formalmente ante el compilador a proporcionar el cuerpo y la lógica de todos los métodos declarados en ella.

Aunque comparten similitudes con las clases abstractas, las interfaces presentan diferencias estructurales determinantes. Una clase abstracta puede contener estado (atributos de instancia no constantes) y constructores, mientras que las interfaces tradicionales en Java no pueden almacenar variables de instancia (todos sus atributos son implícitamente `public static final` constantes) ni tienen constructores. Además, una clase solo puede heredar de una única clase abstracta debido a la regla de herencia simple de Java.

En contraste, la gran fortaleza de las interfaces es que una clase puede implementar múltiples interfaces simultáneamente (ej. `public class Zapador implements Atacante, Defensor, Conductor`). Esto permite modelar múltiples roles y comportamientos polimórficos de forma extremadamente flexible y limpia, superando la restricción de la herencia simple de clases sin incurrir en los peligros de ambigüedad de la herencia múltiple de estado.


## 10. Vamos a poner un ejemplo de un nuevo cuestionario con polimorfismo. Queremos implementar una clase `Punto`, con un método `calcularDistanciaA`, que permite calcular la distancia a la posición de otro `Punto`. Sin embargo, como queremos trabajar con puntos 2D y 3D, haz que ese método sea abstracto y haya dos implementaciones de ese cálculo de distancia. Emplea `instanceof` y *downcasting* para verificar que se recibe un punto compatible y poder calcular correctamente la distancia siempre entre puntos del mismo subtipo. Aprovecha este diseño para crear ahora una clase `Linea`, que acepta `Punto`, sin saber de qué tipo es, y es capaz de dar su longitud independientemente de las dimensiones de sus puntos (las cuales desconoce).

### Respuesta

Para lograr este diseño flexible combinando polimorfismo, clases abstractas y encapsulación, se define una clase base abstracta `Punto` que declara el comportamiento abstracto de cálculo de distancias. Las subclases `Punto2D` y `Punto3D` proporcionan la lógica matemática específica según sus coordenadas espaciales.

Para asegurar la coherencia espacial, cada subclase verifica mediante `instanceof` que el punto recibido sea de su misma naturaleza antes de realizar el *downcasting* y proceder al cálculo de la distancia euclídea, lanzando una excepción si se intenta mezclar dimensiones incompatibles (como medir la distancia entre un punto 2D y un punto 3D).

```java
public abstract class Punto {
    public abstract double distanciaA(Punto otro);
}

public class Punto2D extends Punto {
    private final double x;
    private final double y;

    public Punto2D(double x, double y) {
        this.x = x;
        this.y = y;
    }

    public double getX() { return x; }
    public double getY() { return y; }

    @Override
    public double distanciaA(Punto otro) {
        if (!(otro instanceof Punto2D)) {
            throw new IllegalArgumentException("No se puede calcular distancia entre un Punto2D y otro tipo de punto.");
        }
        Punto2D p2 = (Punto2D) otro;
        double dx = this.x - p2.x;
        double dy = this.y - p2.y;
        return Math.sqrt(dx * dx + dy * dy);
    }
}

public class Punto3D extends Punto {
    private final double x;
    private final double y;
    private final double z;

    public Punto3D(double x, double y, double z) {
        this.x = x;
        this.y = y;
        this.z = z;
    }

    public double getX() { return x; }
    public double getY() { return y; }
    public double getZ() { return z; }

    @Override
    public double distanciaA(Punto otro) {
        if (!(otro instanceof Punto3D)) {
            throw new IllegalArgumentException("No se puede calcular distancia entre un Punto3D y otro tipo de punto.");
        }
        Punto3D p3 = (Punto3D) otro;
        double dx = this.x - p3.x;
        double dy = this.y - p3.y;
        double dz = this.z - p3.z;
        return Math.sqrt(dx * dx + dy * dy + dz * dz);
    }
}
```

Gracias a este diseño polimórfico, la clase `Linea` puede acoplarse de forma genérica a la abstracción `Punto` sin necesidad de conocer los detalles de las dimensiones espaciales, delegando el cálculo de la longitud mediante enlace tardío.

```java
public class Linea {
    private final Punto origen;
    private final Punto destino;

    public Linea(Punto origen, Punto destino) {
        if (origen == null || destino == null) {
            throw new IllegalArgumentException("Los puntos de origen y destino son obligatorios.");
        }
        // Se valida que ambos pertenezcan a la misma dimension espacial
        if (origen.getClass() != destino.getClass()) {
            throw new IllegalArgumentException("Los puntos de la linea deben ser de la misma dimension espacial.");
        }
        this.origen = origen;
        this.destino = destino;
    }

    public double calcularLongitud() {
        // Ligadura dinamica en accion: calcula de forma transparente segun sea 2D o 3D
        return origen.distanciaA(destino);
    }
}
```


## 11. ¿Qué es la **"herencia de interfaces"** en Java? ¿Existe **"herencia múltiple de interfaces"**? Pon un ejemplo de una interfaz `Fichero` que tenga un método para leer su contenido en forma de `String` y luego dicha interfaz sea extendida por otra que sea `FicheroEscribible` que permita enviar contenido e incluso eliminar el fichero.

### Respuesta

La herencia de interfaces es el mecanismo mediante el cual una interfaz puede extender a otra interfaz existente, adquiriendo de forma automática todas sus firmas de métodos declaradas y pudiendo añadir nuevos requerimientos de comportamiento. En Java, este acoplamiento entre interfaces se realiza utilizando la misma palabra clave `extends`.

A diferencia de las clases, sí existe la herencia múltiple de interfaces en Java. Una interfaz puede extender simultáneamente a dos o más interfaces independientes (ej. `public interface DispositivoInteligente extends Conectable, Programable`). Dado que las interfaces no contienen estado interno ni implementaciones de métodos que causen colisiones de código máquina directo, este diseño es seguro y no produce los conflictos estructurales típicos del "Problema del Diamante".

```java
public interface Fichero {
    String leerContenido();
}

// Herencia de interfaces: FicheroEscribible extiende a Fichero
public interface FicheroEscribible extends Fichero {
    void escribirContenido(String datos);
    void eliminarFichero();
}
```

Cualquier clase concreta que decida implementar la interfaz `FicheroEscribible` estará obligada ante el compilador a proporcionar el cuerpo y la lógica para los tres métodos: `leerContenido()` (heredado de `Fichero`), `escribirContenido(...)` y `eliminarFichero()`.
