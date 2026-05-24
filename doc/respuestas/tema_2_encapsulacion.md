<!--
Posible prompt:
<prompt>
Tengo un cuestionario con preguntas sobre "Encapsulación". Debes tener en cuenta que los conocimientos previos que tengo (y por tanto tus respuestas deben ser adaptadas), son:
- C/C++ sin orientación a objetos.
- Temas de Java previos: Clases y Objetos.

Cada respuesta debe tener entre 2 - 4 párrafos de longitud (sin contar los trozos de código).

Por favor, escribe en impersonal las respuestas.

</prompt>
----
-->
# TEMA 2. Encapsulación 

## 1. En Programación Orientada a Objetos (POO), ¿Qué buscan la **encapsulación** y **la ocultación** de información? Enumera brevemente algunas ventajas de la ocultación de información.

### Respuesta

La encapsulación busca agrupar en una única unidad (la clase) tanto los datos (atributos) como las operaciones que los manipulan (métodos). Es el equivalente evolucionado de las `struct` de C, pero añadiendo comportamiento para que el dato no esté "huérfano".

La ocultación de información persigue proteger el estado interno del objeto, impidiendo que el código externo acceda directamente a las variables. Entre sus ventajas destacan el facilitar el mantenimiento (se puede cambiar la estructura interna sin romper otros módulos), aumentar la robustez (evita estados inválidos) y reducir la complejidad, ya que el usuario de la clase solo ve lo necesario para trabajar.


## 2. ¿Qué se entiende por la **interfaz pública** de un objeto o clase en POO? Describe brevemente cómo se relaciona con la ocultación de información.

### Respuesta

La interfaz pública de una clase es el conjunto de métodos y constantes que son visibles desde el exterior. Se puede visualizar como el "manual de instrucciones" o el contrato que el objeto ofrece a otros programadores para que puedan interactuar con él sin conocer sus detalles internos.

Se relaciona con la ocultación de información al actuar como un filtro. Mientras que la ocultación mantiene los detalles técnicos en privado (el "cómo"), la interfaz pública define qué acciones están permitidas (el "qué"). En C, esto sería comparable a lo que se expone en un archivo `.h`, ocultando la implementación lógica en el `.c`.


## 3. Brevemente: ¿Por qué hay que ser conscientes y diseñar con cuidado la **interfaz pública** de una clase? ¿Es fácil cambiarla?

### Respuesta

Se debe diseñar con cautela porque, una vez que otros módulos o programadores empiezan a usar una clase, la interfaz pública se convierte en un compromiso difícil de cambiar. Alterar un método público (cambiar su nombre o sus parámetros) obliga a modificar todo el código externo que dependía de él, lo que genera un "efecto dominó" de errores de compilación.

Cambiar la interfaz no es fácil; requiere procesos de depreciación de código (`@Deprecated` en Java) y soporte de versiones anteriores. En cambio, si la interfaz se mantiene estable, la implementación interna puede rediseñarse completamente (por ejemplo, para optimizar rendimiento) sin que el resto del programa lo note.


## 4. ¿Qué son las **invariantes de clase** y por qué la ocultación de información nos ayuda?

### Respuesta

Las invariantes de clase son condiciones o reglas que deben cumplirse siempre para que un objeto se considere en un estado válido. Por ejemplo, en una clase `Fecha`, una invariante sería que el mes siempre esté en el rango [1, 12].

La ocultación de información es fundamental para proteger estas invariantes. Si los atributos fueran públicos, cualquier parte del programa podría asignar un valor erróneo (como mes 13). Al hacerlos privados, se obliga a pasar por métodos que validan los datos antes de modificar el estado interno.


## 5. Pon un ejemplo de una clase `Punto` en `Java`, con dos coordenadas, `x` e `y`, de tipo `double`, con un método `calcularDistanciaAOrigen`, y que haga uso de la ocultación de información. ¿Cuál es la interfaz pública de la clase `Punto`? ¿Qué significa `public` y `private`?

### Respuesta

La interfaz pública en este ejemplo es el método `calcularDistanciaAOrigen()`. Los modificadores significan lo siguiente: `private` restringe el acceso a los miembros solo a la propia clase (el exterior no puede ver ni tocar `x` o `y`), mientras que `public` permite que el método sea invocado desde cualquier otra parte del programa.

```java
public class Punto { 
    private double x; 
    private double y;

    public double calcularDistanciaAOrigen() {
        return Math.sqrt(x * x + y * y);
    }
}
```


## 6. En Java, ¿A quiénes se pueden aplicar los modificadores `public` o `private`?

### Respuesta

En Java, estos modificadores se pueden aplicar a los miembros de la clase (atributos y métodos) y a la propia clase (aunque con matices para clases externas). También pueden aplicarse a los constructores para controlar quién puede instanciar el objeto.

No se pueden aplicar a las variables locales dentro de un método. Al igual que en C, una variable declarada dentro de una función solo existe en ese ámbito y no tiene sentido hablar de visibilidad pública o privada para ella.


## 7. En POO, la visibilidad puede ser pública o privada, pero ¿existen más tipos de visibilidad? ¿Qué ocurre en Java? ¿Y en otros lenguajes?

### Respuesta

Sí, en Java existen dos niveles adicionales: el nivel por defecto (package-private), que permite el acceso a cualquier clase dentro del mismo paquete, y `protected`, que permite el acceso a clases del mismo paquete y a subclases (herencia).

Otros lenguajes como C++ incluyen el concepto de `friend`, que permite a funciones o clases específicas acceder a los miembros privados de otra. En lenguajes como Python, la ocultación es meramente convencional (usando guiones bajos), ya que no existen restricciones técnicas estrictas de visibilidad.


## 8. Responde: Los miembros de instancia privados de un objeto están ocultos para (a) otras clases o (b) otras instancias, aunque sean de la misma clase. Pon un ejemplo añadiendo un método `calcularDistanciaAPunto(Punto otro)` y explica la respuesta.

### Respuesta

Los miembros privados están ocultos para (a) otras clases. En Java, una instancia de una clase puede acceder a los miembros privados de otra instancia de su misma clase.

```java
public double calcularDistanciaAPunto(Punto otro) { 
    // Es posible acceder a 'otro.x' aunque sea privado porque estamos dentro de la clase Punto
    double dx = this.x - otro.x; 
    double dy = this.y - otro.y; 
    return Math.sqrt(dx * dx + dy * dy); 
}
```


## 9. ¿Qué son los métodos "getter" y "setter" en los lenguajes orientados a objetos?

### Respuesta

Son métodos públicos diseñados específicamente para leer ("getter") o modificar ("setter") el valor de un atributo privado. En Java, siguen una convención de nombres: `getAtributo()` y `setAtributo(valor)`.

Su propósito no es solo dar acceso, sino actuar como intermediarios. Un "setter" permite validar que el nuevo valor sea correcto antes de asignarlo, y un "getter" podría devolver una copia del dato o un valor calculado, manteniendo el control total sobre el atributo real.


## 10. Cuando nos referimos a que la ocultación de información mejora la "seguridad" del programa, ¿nos referimos a que no pueda ser "hackeado"?

### Respuesta

No se refiere a seguridad informática en el sentido de ataques externos o cifrado. Se refiere a la seguridad del código o robustez lógica. Busca evitar que el programador cometa errores accidentales al manipular datos que no debería tocar directamente.

Es una medida de "seguridad industrial": poner protecciones en una máquina para que el operario no meta la mano donde hay engranajes peligrosos. Evita que el sistema colapse por un uso indebido de sus componentes internos.


## 11. ¿Qué diferencia hay entre **miembro de instancia** y **miembro de clase**? ¿Los miembros de clase también se pueden ocultar?

### Respuesta

Un miembro de instancia pertenece a cada objeto individual; cada `Punto` tiene sus propias coordenadas `x` e `y`. Un miembro de clase (marcado con `static`) es compartido por todos los objetos de esa clase; existe una sola copia para todos.

Los miembros de clase también se pueden (y suelen) ocultar. Un atributo `static private` puede servir para llevar una cuenta interna que la clase necesita, pero que no debe ser alterada desde fuera.


## 12. Brevemente: ¿Tiene sentido que los constructores sean privados?

### Respuesta

Tiene mucho sentido en varios escenarios. Se utiliza cuando se quiere impedir que cualquiera cree objetos de esa clase de forma indiscriminada, obligando a usar métodos específicos para obtener una instancia (como en el patrón Singleton o en métodos factoría).

También se usa en clases que solo contienen métodos de utilidad estáticos (como la clase `Math` de Java), donde no tiene sentido crear un "objeto Math" porque todas sus funciones operan de forma independiente a cualquier estado de instancia.


## 13. ¿Cómo se indican los **miembros de clase** en Java? Pon un ejemplo, en la clase `Punto` definida anteriormente, para que incluya miembros de clase que permitan saber cuáles son los valores `x` e `y` máximos que se han establecido en todos los puntos que se hayan creado hasta el momento.

### Respuesta

Para indicar que un miembro es de clase se utiliza la palabra reservada `static`. En el ejemplo del punto, se usarían para registrar los máximos históricos.

```java
public class Punto { 
    private double x, y; 
    private static double maxX = 0; 
    private static double maxY = 0;

    public Punto(double x, double y) {
        this.x = x;
        this.y = y;
        if (x > maxX) maxX = x;
        if (y > maxY) maxY = y;
    }
}
```


## 14. Como sería un método factoría dentro de la clase `Punto` para construir un `Punto` a partir de dos coordenadas, pero que las redondee al entero más cercano. Escribe sólo el código del método, no toda la clase ¿Has usado `static`? 

### Respuesta

Un método factoría es un método estático que devuelve una instancia de la clase.

```java
public static Punto crearPuntoRedondeado(double x, double y) { 
    return new Punto(Math.round(x), Math.round(y)); 
}
```

Se ha utilizado `static` porque el método debe poder llamarse sin que exista todavía un objeto `Punto` creado; su función es, precisamente, fabricar uno.


## 15. Cambia la implementación de `Punto`. En vez de dos `double`, emplea un array interno de dos posiciones, intentando no modificar la interfaz pública de la clase.

### Respuesta

Se modifica la estructura interna, pero se mantienen los métodos públicos para que el código externo no tenga que cambiar.

```java
public class Punto { 
    private double[] coords = new double[2]; // x es coords[0], y es coords[1]

    public double getX() { return coords[0]; }
    public double getY() { return coords[1]; }

    public double calcularDistanciaAOrigen() {
        return Math.sqrt(coords[0] * coords[0] + coords[1] * coords[1]);
    }
}
```


## 16. Si un atributo va a tener un método "getter" y "setter" públicos, ¿no es mejor declararlo público? ¿Cuál es la convención más habitual sobre los atributos, que sean públicos o privados? ¿Tiene esto algo que ver con las "invariantes de clase"?

### Respuesta

Aunque se incluyan ambos métodos, es mejor mantener el atributo privado. Declararlo público rompe la encapsulación; si en el futuro se desea que el atributo sea de solo lectura, o que su cambio dispare una actualización en la interfaz gráfica, no se podrá hacer sin cambiar todo el programa.

La convención habitual es que todos los atributos sean privados. Esto se relaciona con las invariantes de clase porque el "setter" es el único punto donde se puede centralizar la lógica de validación, garantizando que el objeto nunca entre en un estado ilegal.


## 17. ¿Qué significa que una clase sea **inmutable**? ¿qué es un método modificador? ¿Un método modificador es siempre un "setter"? ¿Tiene ventajas que una clase sea inmutable?

### Respuesta

Una clase es inmutable si su estado no puede cambiar una vez creada; el ejemplo más claro en Java es `String`. Un método modificador es aquel que altera el estado del objeto. No siempre es un "setter" tradicional (ej. `acelerar()` en un coche).

Las ventajas de la inmutabilidad son la seguridad en entornos con varios hilos (no hay miedo de que alguien cambie el dato mientras otro lo lee) y la simplicidad, ya que se pueden compartir referencias al objeto sin riesgo de que sean alteradas.


## 18. ¿Es recomendable incluir métodos "setter" siempre y como convención?

### Respuesta

No es recomendable incluirlos por defecto. Solo deben crearse si es estrictamente necesario que el objeto sea modificable desde fuera. La programación moderna tiende hacia la inmutabilidad; si un objeto no necesita cambiar, es mejor no proporcionar "setters" para evitar efectos secundarios indeseados.


## 19. ¿La clase `String` en Java es mutable o inmutable? ¿Qué ocurre al concatenar dos cadenas? ¿Qué debemos hacer si vamos a hacer una operación que implique concatenar muchas veces para construir paso a paso una cadena muy larga?

### Respuesta

La clase `String` es inmutable. Al concatenar dos cadenas (`s1 + s2`), no se modifica ninguna de las originales, sino que se crea un objeto `String` totalmente nuevo en memoria con el resultado.

Si se van a realizar miles de concatenaciones, el uso de `String` es ineficiente por la gran cantidad de objetos temporales creados. En esos casos, se debe usar la clase `StringBuilder`, que es una versión mutable diseñada específicamente para construir cadenas paso a paso de forma eficiente.


## 20. En POO ¿Cómo se comparan objetos de una misma clase? ¿Por su contenido o por su identidad? ¿Qué es el método equals en Java? ¿Qué hace por defecto? ¿Cómo se deben comparar dos cadenas en Java? 

### Respuesta

Los objetos se pueden comparar por identidad (¿son el mismo objeto en memoria?) usando `==`, o por contenido (¿tienen los mismos datos?) usando el método `equals()`.

Por defecto, `equals()` en Java se comporta igual que `==`, comparando direcciones de memoria. Por ello, es necesario sobrescribirlo en nuestras clases para definir qué significa que dos objetos sean "iguales" lógicamente. Las cadenas en Java siempre deben compararse con `s1.equals(s2)`.


## 21. ¿Qué son las clases "wrapper" en un lenguaje de programación orientado a objetos? ¿Cómo se hace? ¿Es un proceso automático? ¿Qué ventajas tienen? ¿Todos los lenguajes orientados a objetos tienen tipos primitivos y necesitan wrappers? 

### Respuesta

Las clases "wrapper" (envoltorios) son clases que representan tipos primitivos como objetos (ej. `Integer` para `int`, `Double` para `double`). Se usan porque muchas utilidades de Java solo funcionan con objetos, no con tipos simples de C.

En Java, el proceso es automático mediante el autoboxing y unboxing. No todos los lenguajes tienen tipos primitivos; en lenguajes como Smalltalk o Ruby, todo es un objeto desde el principio y no necesitan wrappers.


## 22. ¿En POO qué es un **tipo de dato enumerado**? ¿En Java, un tipo de dato enumerado es una clase? ¿Qué ventajas tienen en términos de encapsulación los enumerados en Java?

### Respuesta

Un tipo enumerado define un conjunto fijo de constantes con nombre, haciendo el código más legible y seguro que usando simples `int`. En Java, un `enum` es una clase especial.

En términos de encapsulación, los `enum` de Java son muy potentes porque pueden tener atributos privados, constructores y métodos. Esto permite que cada constante de la enumeración porte información asociada de forma protegida y coherente.


## 23. Crea un tipo enumerado en Java que se llame `Mes`, con doce posibles instancias y que además proporcione métodos para obtener cuántos días tiene ese mes, el ordinal de ese mes en el año (1-12), empleando atributos privados y constructores del tipo enumerado.

## 24. Añade a la clase `Mes` del ejercicio anterior cuatro métodos para devolver si ese mes tiene algunos días de invierno, primavera, verano u otoño, indicando con un booleano el hemisferio (norte o sur, parámetro `enHemisferioNorte`). Es decir: `esDePrimavera(boolean esHemisferioNorte)`, `esDeVerano(boolean esHemisferioNorte)`, `esDeOtoño(boolean esHemisferioNorte)`, `esDeInvierno(boolean esHemisferioNorte)`

### Respuesta

```java
public enum Mes { 
    ENERO(31, 1), FEBRERO(28, 2), MARZO(31, 3), ABRIL(30, 4), MAYO(31, 5), JUNIO(30, 6),
    JULIO(31, 7), AGOSTO(31, 8), SEPTIEMBRE(30, 9), OCTUBRE(31, 10), NOVIEMBRE(30, 11), DICIEMBRE(31, 12);

    private final int dias;
    private final int ordinal;

    private Mes(int dias, int ordinal) {
        this.dias = dias;
        this.ordinal = ordinal;
    }

    public int getDias() { return dias; }
    public int getOrdinal() { return ordinal; }

    public boolean esDeVerano(boolean norte) {
        if (norte) {
            return this == JUNIO || this == JULIO || this == AGOSTO;
        } else {
            return this == DICIEMBRE || this == ENERO || this == FEBRERO;
        }
    }
    
    public boolean esDeInvierno(boolean norte) {
        if (norte) {
            return this == DICIEMBRE || this == ENERO || this == FEBRERO;
        } else {
            return this == JUNIO || this == JULIO || this == AGOSTO;
        }
    }
    
    public boolean esDePrimavera(boolean norte) {
        if (norte) {
            return this == MARZO || this == ABRIL || this == MAYO;
        } else {
            return this == SEPTIEMBRE || this == OCTUBRE || this == NOVIEMBRE;
        }
    }
    
    public boolean esDeOtoño(boolean norte) {
        if (norte) {
            return this == SEPTIEMBRE || this == OCTUBRE || this == NOVIEMBRE;
        } else {
            return this == MARZO || this == ABRIL || this == MAYO;
        }
    }
}
```
