<!--
Posible prompt:
<prompt>
Tengo un cuestionario con preguntas sobre "Composición". Debes tener en cuenta que los conocimientos previos que tengo (y por tanto tus respuestas deben ser adaptadas), son:
- C/C++ sin orientación a objetos.
- Temas de Java previos: Clases y Objetos, Encapsulación y Excepciones.

Cada respuesta debe tener entre 2 - 4 párrafos de longitud (sin contar los trozos de código).

Por favor, escribe en impersonal las respuestas.

</prompt>
----
-->
# Tema 4.1. Composición 


## 1. En C, podemos crear estructuras mayores **componiendo** unas con otras, que suelen describirse como "A tiene-un/tiene-varios B". Pon un ejemplo, empleando `struct`, de una línea de puntos, donde puntos tienen dos coordenadas (`x` e `y`), y la línea esta hecha de dos puntos. Incluye una función para calcular la distancia entre puntos y otra para hallar la longitud de una línea.

### Respuesta

En el lenguaje C, la composición se realiza de forma directa anidando unas estructuras de datos (`struct`) dentro de otras. Este enfoque permite modelar relaciones de tipo "tiene-un" o "tiene-varios" agrupando variables de tipos ya definidos bajo una nueva entidad común. Al carecer de orientación a objetos, el comportamiento (las funciones que operan sobre estos datos) debe definirse por separado.

Para modelar una línea a partir de dos puntos, se define primero la estructura `Punto` con sus coordenadas cartesianas. Posteriormente, la estructura `Linea` se compone declarando dos campos de tipo `Punto` en su interior. Dado que en C los datos no tienen métodos integrados, las funciones que calculan distancias o longitudes deben recibir explícitamente las estructuras o sus direcciones de memoria como argumentos de entrada.

```c
#include <stdio.h>
#include <math.h>

struct Punto {
    double x;
    double y;
};

struct Linea {
    struct Punto origen;
    struct Punto destino;
};

double calcularDistancia(struct Punto p1, struct Punto p2) {
    double dx = p2.x - p1.x;
    double dy = p2.y - p1.y;
    return sqrt(dx * dx + dy * dy);
}

double calcularLongitud(struct Linea l) {
    return calcularDistancia(l.origen, l.destino);
}

int main() {
    struct Punto p1 = {0.0, 0.0};
    struct Punto p2 = {3.0, 4.0};
    struct Linea l = {p1, p2};
    printf("Longitud de la linea: %f\n", calcularLongitud(l));
    return 0;
}
```


## 2. Ahora transforma ese ejemplo a orientación a objetos con Java, para tener un primer ejemplo de **composición** en orientación a objetos. Crea una clase `Punto`, y una clase `Linea`. La clase `Punto` debe tener un método para calcular distancia a otro `Punto` y `Linea` debe tener un método para calcular su longitud. Gracias a la ocultación de información, supera a C, garantizando que los puntos sean inmutables, al igual que la línea, que una vez creada, no queremos que se modifique de qué a qué puntos va dicha línea.  

### Respuesta

En la programación orientada a objetos con Java, la composición no consiste únicamente en anidar datos, sino en acoplar de forma cohesiva tanto el estado como el comportamiento bajo clases bien definidas. A diferencia del diseño en C, los atributos de las clases se ocultan del exterior mediante visibilidad privada, y solo se exponen aquellos comportamientos necesarios a través de métodos públicos.

Para garantizar la inmutabilidad de los puntos y las líneas, se diseñan clases cuyos atributos se marcan con la palabra clave `final`. Esto asegura que los valores de las coordenadas del `Punto` y las referencias a los puntos que componen la `Linea` se establezcan únicamente en el momento de la construcción y no puedan ser alterados posteriormente desde el exterior. De esta manera, se protegen las invariantes del modelo frente a modificaciones accidentales.

```java
public class Punto {
    private final double x;
    private final double y;

    public Punto(double x, double y) {
        this.x = x;
        this.y = y;
    }

    public double getX() { return x; }
    public double getY() { return y; }

    public double distanciaA(Punto otro) {
        double dx = this.x - otro.x;
        double dy = this.y - otro.y;
        return Math.sqrt(dx * dx + dy * dy);
    }
}

public class Linea {
    private final Punto origen;
    private final Punto destino;

    public Linea(Punto origen, Punto destino) {
        if (origen == null || destino == null) {
            throw new IllegalArgumentException("Los puntos de origen y destino no pueden ser nulos.");
        }
        this.origen = origen;
        this.destino = destino;
    }

    public Punto getOrigen() { return origen; }
    public Punto getDestino() { return destino; }

    public double calcularLongitud() {
        return origen.distanciaA(destino);
    }
}
```


## 3. ¿Qué significa la **multiplicidad** en la composición? En el ejemplo anterior, ¿cuál es la multiplicidad entre `Linea` y `Punto`? Indícalo expresando la multiplicidad en ambas direcciones, de `Linea` a `Punto` y de `Punto` a `Linea`.

### Respuesta

La multiplicidad es un concepto clave en el modelado de software que define el número de instancias de una clase que pueden estar asociadas con una única instancia de otra clase en una relación dada. Este límite numérico (que puede ser un valor fijo, un rango o un número indeterminado) establece las restricciones estructurales que deben ser forzadas y garantizadas por el código de la aplicación.

En el ejemplo de `Linea` y `Punto`, la relación en la dirección de `Linea` a `Punto` tiene una multiplicidad exacta de 2. Esto significa que cada instancia de `Linea` está compuesta obligatoriamente por exactamente dos objetos `Punto` (el origen y el destino), no pudiendo existir una línea válida con menos o más puntos en este diseño.

En la dirección inversa, de `Punto` a `Linea`, la multiplicidad es de `0..*`. Un punto determinado puede ser compartido o no formar parte de ninguna línea, o por el contrario, ser el extremo de una, dos o infinitas líneas diferentes en el plano. Esto refleja que el ciclo de vida del punto puede ser independiente al de la línea en ciertos diseños del dominio.


## 4. ¿Qué significa composición **fuerte** y composición **débil**? ¿Qué consecuencia implica en relación al ciclo de vida de los objetos? Indica a cuál solemos referirnos como **"asociación o agregación"** y a cuál como **"composición"** propiamente.

### Respuesta

La distinción entre composición fuerte y composición débil radica en el grado de acoplamiento y dependencia que existe entre el objeto contenedor y los objetos contenidos. Esta diferencia conceptual tiene un impacto directo sobre la propiedad y la gestión del ciclo de vida de las instancias involucradas en la relación.

En la composición fuerte (o simplemente "composición" propiamente dicha), el ciclo de vida de los objetos contenidos está estrechamente ligado al del objeto contenedor. Esto significa que los componentes no tienen sentido de la existencia por sí mismos fuera del contenedor y son creados y destruidos junto a él. Si el contenedor deja de existir, todos sus elementos internos son destruidos de forma lógica o automática.

En la composición débil (a la cual solemos referirnos como "agregación" o "asociación"), los objetos contenidos tienen un ciclo de vida independiente del contenedor. Los objetos son creados fuera de la clase contenedora y se le pasan como referencia. Si el contenedor se destruye, los objetos agregados siguen existiendo en el sistema de manera autónoma, pudiendo ser vinculados a otros contenedores si es necesario.


## 5. Cuando una clase usa a otra al recibirla o devolverla como parámetro en algún método, al hacer `new` dentro de un método, o al usarlas como variables locales, ¿hablamos de composición o de **"dependencia"**?

### Respuesta

Cuando la interacción entre dos clases se limita a un uso puntual dentro del comportamiento de un método, sin que exista un atributo persistente que las vincule de forma estructural, se habla de una relación de "dependencia" (también llamada relación de uso). En este escenario, una clase requiere de la otra únicamente para llevar a cabo una acción o cálculo específico en un momento determinado.

A diferencia de la composición, donde el objeto compuesto mantiene una referencia permanente a sus componentes como parte de su estado interno, en la dependencia la relación es transitoria. La clase dependiente no es propietaria de la otra clase, ni la almacena a largo plazo; simplemente interactúa con ella de forma temporal como parámetro, variable local o tipo de retorno.

En términos de acoplamiento, la dependencia representa la forma de conexión más débil entre clases en la programación orientada a objetos. Sin embargo, sigue siendo relevante vigilarla, ya que cualquier cambio en la interfaz pública de la clase de la que se depende puede obligar a modificar el código del método que la utiliza, afectando a la mantenibilidad del sistema.


## 6. En el ejemplo anterior de línea y punto, programa la relación entre `Linea` y `Punto` de dos formas. Una **como composición fuerte**, donde el ciclo de vida de los puntos está ligado al de Linea y otra **como composición débil**, donde no.

### Respuesta

Para ilustrar la diferencia práctica en código Java, en la composición fuerte la clase contenedora se encarga de crear sus propias instancias de los componentes internos. En este caso, el constructor de `LineaFuerte` no recibe objetos `Punto` ya creados, sino los valores primitivos (coordenadas) necesarios para fabricar sus propios puntos internamente. De este modo, nadie fuera de `LineaFuerte` tiene acceso ni referencia directa a sus puntos de origen y destino.

En la composición débil (agregación), el constructor recibe referencias a objetos `Punto` que ya han sido creados externamente. La clase contenedora se limita a almacenar esas referencias en sus variables miembro, permitiendo que dichos puntos existan antes de la creación de la línea y sigan existiendo intactos después de que la línea deje de ser utilizada.

```java
// Ejemplo 1: Composición Fuerte (Composición propiamente dicha)
public class LineaFuerte {
    private final Punto origen;
    private final Punto destino;

    public LineaFuerte(double x1, double y1, double x2, double y2) {
        // La propia línea crea e inicializa sus puntos internos
        this.origen = new Punto(x1, y1);
        this.destino = new Punto(x2, y2);
    }

    public double calcularLongitud() {
        return origen.distanciaA(destino);
    }
}

// Ejemplo 2: Composición Débil (Agregación)
public class LineaDebil {
    private final Punto origen;
    private final Punto destino;

    public LineaDebil(Punto origen, Punto destino) {
        if (origen == null || destino == null) {
            throw new IllegalArgumentException("Los puntos no pueden ser nulos.");
        }
        // Se copian las referencias de objetos ya creados fuera
        this.origen = origen;
        this.destino = destino;
    }

    public double calcularLongitud() {
        return origen.distanciaA(destino);
    }
}
```


## 7. En Java, en la composición fuerte, ¿cuando el contenedor destruye los objetos? No se observa que `Linea` destruya los `Punto` explícitamente, ¿Por qué?

### Respuesta

En Java, a diferencia de lenguajes como C++, no existe un operador de destrucción explícita como `delete` ni destructores manuales en las clases. El programador no es responsable de liberar la memoria ocupada por las instancias creadas con `new`, ya que esta tarea es delegada por completo a la máquina virtual (JVM) a través de su proceso de recolección de basura (Garbage Collector).

En la composición fuerte, cuando el objeto contenedor (`LineaFuerte`) deja de ser referenciado y se vuelve elegible para ser recolectado, las únicas referencias que apuntaban a sus objetos internos (`Punto`) también desaparecen con él, siempre y cuando no se hayan expuesto al exterior. Al quedar esos puntos con cero referencias activas en el sistema, el recolector de basura los identifica como inaccesibles y libera su memoria de forma automática en sus siguientes ciclos de ejecución.

Esto representa una gran ventaja para la seguridad del código frente a C, donde olvidar liberar la memoria asignada dinámicamente (`free`) provoca fugas de memoria, o liberarla antes de tiempo causa fallos catastróficos por punteros colgantes. Java mitiga estos errores garantizando que la destrucción de los componentes internos ocurra de manera segura tan pronto como el contenedor sea desechado.


## 8. Pon un ejemplo de composicion débil entre un departamento que tiene varios profesores. Implementa dos composiciones a la vez: entre el departamento y todos sus profesores y entre el departamento y su director, que es un profesor del departamento. Siempre debe haber un director en el departamento desde el inicio. Lanza excepciones si se viola la invariante. Emplea arrays primitivos de Java, estilo `Profesor[]`, con máximo 50, pero no rompas la encapsulación, no desveles que estás empleando un array, permite añadir un `Profesor` al final de la lista, y eliminar un profesor dada su posición. Da acceso a los profesores con un método para saber cuántos hay y otro para obtener un profesor por posición. El director se puede cambiar por otro profesor del departamento. Sin embargo, ten en cuenta esta invariante de clase: el director debe formar siempre parte de la lista de profesores, es decir, ten cuidado al cambiar el director o al eliminar un profesor.

### Respuesta

Para implementar este diseño de agregación compleja en Java, se debe asegurar que el estado interno se mantenga en equilibrio coherente controlando rigurosamente los cambios. La principal regla de integridad (invariante de clase) exige que el director del departamento sea obligatoriamente uno de los profesores que forman parte del equipo de ese mismo departamento.

Para respetar la encapsulación, se mantiene el array de profesores como privado y no se expone al exterior en ningún método. El tamaño y el contenido del array se manipulan a través de métodos seguros de inserción y borrado que verifican los límites físicos y las invariantes de negocio, lanzando excepciones si un cambio deja al departamento en un estado inconsistente (por ejemplo, intentar eliminar al profesor que actualmente ejerce de director).

```java
public class Profesor {
    private final String nombre;

    public Profesor(String nombre) {
        if (nombre == null || nombre.trim().isEmpty()) {
            throw new IllegalArgumentException("El nombre del profesor no puede estar vacio.");
        }
        this.nombre = nombre;
    }

    public String getNombre() { return nombre; }
}

public class Departamento {
    private final String nombre;
    private final Profesor[] profesores;
    private int numProfesores;
    private Profesor director;

    public Departamento(String nombre, Profesor directorInicial) {
        if (nombre == null || nombre.trim().isEmpty()) {
            throw new IllegalArgumentException("El nombre del departamento es obligatorio.");
        }
        if (directorInicial == null) {
            throw new IllegalArgumentException("El director inicial es obligatorio.");
        }
        this.nombre = nombre;
        this.profesores = new Profesor[50];
        // Invariante inicial: El director debe estar en la lista de profesores
        this.profesores[0] = directorInicial;
        this.numProfesores = 1;
        this.director = directorInicial;
    }

    public Profesor getDirector() {
        return director;
    }

    public void setDirector(Profesor nuevoDirector) {
        if (nuevoDirector == null) {
            throw new IllegalArgumentException("El director no puede ser nulo.");
        }
        // Invariante: El nuevo director debe formar parte del departamento
        if (!contieneProfesor(nuevoDirector)) {
            throw new IllegalStateException("El director propuesto debe pertenecer a la lista de profesores del departamento.");
        }
        this.director = nuevoDirector;
    }

    public void añadirProfesor(Profesor prof) {
        if (prof == null) {
            throw new IllegalArgumentException("No se puede añadir un profesor nulo.");
        }
        if (numProfesores >= 50) {
            throw new IllegalStateException("El departamento esta lleno (maximo 50 profesores).");
        }
        if (contieneProfesor(prof)) {
            return; // Evitamos duplicados
        }
        profesores[numProfesores] = prof;
        numProfesores++;
    }

    public void eliminarProfesor(int posicion) {
        if (posicion < 0 || posicion >= numProfesores) {
            throw new IndexOutOfBoundsException("Posicion de profesor no valida.");
        }
        Profesor candidatoEliminar = profesores[posicion];
        // Invariante: No podemos eliminar al profesor si es el director actual
        if (candidatoEliminar.equals(director)) {
            throw new IllegalStateException("No se puede eliminar al profesor porque es el director actual. Cambie el director antes.");
        }

        // Desplazamos elementos para cubrir el hueco
        for (int i = posicion; i < numProfesores - 1; i++) {
            profesores[i] = profesores[i + 1];
        }
        profesores[numProfesores - 1] = null;
        numProfesores--;
    }

    public int getNumProfesores() {
        return numProfesores;
    }

    public Profesor getProfesor(int posicion) {
        if (posicion < 0 || posicion >= numProfesores) {
            throw new IndexOutOfBoundsException("Posicion de profesor no valida.");
        }
        return profesores[posicion];
    }

    private boolean contieneProfesor(Profesor prof) {
        for (int i = 0; i < numProfesores; i++) {
            if (profesores[i].equals(prof)) {
                return true;
            }
        }
        return false;
    }
}
```


## 9. En Java, existen también `List`, cambia y muestra cómo sería el código anterior empleando `List` en vez de arrays primitivos. ¿Qué parte del código original te has ahorrado? Además, fíjate en el método `getProfesor(int pos)`: si en su lugar existiera un método que devolviera todos los profesores a la vez, ¿qué problema tendría devolver directamente la lista interna? ¿Cómo lo resolverías?

### Respuesta

Al sustituir el array primitivo de tamaño fijo por una lista dinámica (`List<Profesor>`), implementada típicamente con `ArrayList`, el diseño del código se simplifica de forma notable. Se ahorra todo el código relacionado con la gestión manual de la memoria secuencial: el control del número de elementos ocupados (`numProfesores`), el redimensionamiento o límites físicos del array, y los bucles manuales necesarios para desplazar elementos al eliminar una posición intermedia de la lista.

Sin embargo, si se decide añadir un método que retorne la colección completa de profesores, devolver directamente la referencia a la lista interna (`return profesores;`) presenta un grave problema de diseño. Al hacerlo, se rompería la encapsulación de la clase, permitiendo que cualquier código externo modifique, añada o elimine elementos de la lista directamente sin pasar por las validaciones e invariantes de clase del `Departamento` (por ejemplo, podrían vaciar la lista o eliminar al director desde fuera).

Para resolver esta vulnerabilidad estructural, se debe evitar devolver la lista original. La solución idónea consiste en devolver una vista no modificable de la lista utilizando el método `Collections.unmodifiableList(profesores)`. Esto garantiza que cualquier intento de alteración desde el exterior lance una excepción de forma automática, protegiendo las reglas del departamento.

```java
import java.util.ArrayList;
import java.util.Collections;
import java.util.List;

public class DepartamentoConLista {
    private final String nombre;
    private final List<Profesor> profesores;
    private Profesor director;

    public DepartamentoConLista(String nombre, Profesor directorInicial) {
        if (nombre == null || nombre.trim().isEmpty()) {
            throw new IllegalArgumentException("El nombre del departamento es obligatorio.");
        }
        if (directorInicial == null) {
            throw new IllegalArgumentException("El director inicial es obligatorio.");
        }
        this.nombre = nombre;
        this.profesores = new ArrayList<>();
        this.profesores.add(directorInicial);
        this.director = directorInicial;
    }

    public Profesor getDirector() { return director; }

    public void setDirector(Profesor nuevoDirector) {
        if (nuevoDirector == null) {
            throw new IllegalArgumentException("El director no puede ser nulo.");
        }
        if (!profesores.contains(nuevoDirector)) {
            throw new IllegalStateException("El director debe pertenecer al departamento.");
        }
        this.director = nuevoDirector;
    }

    public void añadirProfesor(Profesor prof) {
        if (prof == null) {
            throw new IllegalArgumentException("No se puede añadir un profesor nulo.");
        }
        if (!profesores.contains(prof)) {
            profesores.add(prof);
        }
    }

    public void eliminarProfesor(int posicion) {
        if (posicion < 0 || posicion >= profesores.size()) {
            throw new IndexOutOfBoundsException("Posicion no valida.");
        }
        Profesor candidato = profesores.get(posicion);
        if (candidato.equals(director)) {
            throw new IllegalStateException("No se puede eliminar al director del departamento.");
        }
        profesores.remove(posicion);
    }

    public int getNumProfesores() {
        return profesores.size();
    }

    public Profesor getProfesor(int posicion) {
        if (posicion < 0 || posicion >= profesores.size()) {
            throw new IndexOutOfBoundsException("Posicion no valida.");
        }
        return profesores.get(posicion);
    }

    // Resolviendo el problema de la encapsulacion al devolver todos los profesores
    public List<Profesor> getProfesores() {
        return Collections.unmodifiableList(profesores);
    }
}
```


## 10. Al igual que ocurre con las excepciones en Java, que pueden encerrar causas (que son excepciones), de forma recursiva, suponen un tipo especial de composiciones, denominadas composiciones recursivas. Pon un ejemplo en Java de una `Persona`, que sea inmutable, y que tiene una madre, que es otra `Persona`. Haz un main con un ejemplo de uso con una familia de personas, desde el nieto hasta la abuela. Enumera algún otro ejemplo clásico de composiciones recursivas.

### Respuesta

Una composición recursiva es aquella relación de parte-todo donde un objeto se compone de instancias que pertenecen a su misma clase. Este patrón permite estructurar jerarquías o secuencias de profundidad indefinida de forma muy elegante y compacta. Al igual que las excepciones encadenan su causa original, un objeto mantiene una referencia a otro objeto de su mismo tipo para representar una cadena de datos.

En el modelado de la clase `Persona`, la inmutabilidad se logra declarando sus atributos privados y de tipo `final`, estableciendo su estado únicamente a través del constructor. Al tener una madre (que es a su vez otra `Persona`), se puede navegar hacia atrás en la línea de ascendencia familiar de forma recursiva, deteniendo la cadena cuando la referencia al objeto superior es `null`.

```java
public class Persona {
    private final String nombre;
    private final Persona madre; // Composición recursiva

    public Persona(String nombre, Persona madre) {
        if (nombre == null || nombre.trim().isEmpty()) {
            throw new IllegalArgumentException("El nombre es obligatorio.");
        }
        this.nombre = nombre;
        this.madre = madre; // Puede ser null en caso de no conocerse
    }

    public String getNombre() { return nombre; }
    public Persona getMadre() { return madre; }

    @Override
    public String toString() {
        if (madre == null) {
            return nombre + " (Madre: Desconocida)";
        }
        return nombre + " -> " + madre.toString();
    }
}
```

```java
public class PrincipalFamilia {
    public static void main(String[] args) {
        // Creación de la genealogía desde la abuela
        Persona abuela = new Persona("Carmen", null);
        Persona madre = new Persona("Ana", abuela);
        Persona nieto = new Persona("Carlos", madre);

        System.out.println("Linea de ascendencia materna:");
        System.out.println(nieto);
    }
}
```

Existen otros ejemplos clásicos muy populares de composiciones recursivas en el desarrollo de software. En el diseño de estructuras de datos, las listas enlazadas (donde un nodo apunta a otro nodo del mismo tipo) y los árboles de decisión son composiciones recursivas directas. También destaca el patrón de diseño *Composite*, empleado habitualmente para modelar sistemas de archivos del disco duro (donde un directorio puede contener archivos y otros subdirectorios que son carpetas del mismo tipo).


## 11. ¿Qué son las relaciones de composición "bidireccionales"? ¿Qué habría que hacer para implementar este tipo de relación en el ejemplo de `Profesor` y `Departamento`?

### Respuesta

Las relaciones de composición bidireccionales son aquellas conexiones de asociación donde ambos objetos participantes guardan referencias mutuas permanentes el uno del otro. Esto permite navegar por la relación y consultar los datos en ambos sentidos sin necesidad de realizar búsquedas exhaustivas o costosas: desde el objeto contenedor se puede acceder a sus componentes, y desde cualquiera de los componentes se puede identificar de forma inmediata a su contenedor.

Para implementar esta bidireccionalidad en el ejemplo de `Profesor` y `Departamento`, se debe modificar la clase `Profesor` para que incluya un atributo privado de tipo `Departamento`. De este modo, un departamento conoce su colección de profesores, y cada profesor conoce a qué departamento pertenece.

Sin embargo, este tipo de relación introduce la complejidad de mantener sincronizadas ambas referencias en todo momento. Para evitar incoherencias (por ejemplo, que el departamento A crea que el profesor pertenece a su plantilla, pero el profesor crea que trabaja para el departamento B), se deben diseñar métodos de control centralizados (típicamente en el contenedor) que actualicen la referencia en ambos lados a la vez de forma atómica y segura cuando un profesor es contratado o trasladado.
