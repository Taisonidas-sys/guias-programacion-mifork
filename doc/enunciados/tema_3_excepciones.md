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

En C, como no existe un mecanismo automático de excepciones, el error debe comunicarse de forma explícita.
Una posibilidad muy habitual consiste en devolver un valor especial que indique “ha ocurrido un error”, y
dejar que el código llamador lo compruebe. En este ejemplo podría devolverse -1, suponiendo que la raíz
cuadrada válida nunca será negativa. Después, desde fuera de raiz, se detecta ese valor y se informa al
usuario.
El problema de este diseño es que mezcla el resultado correcto con la señal de error. Funciona en ejemplos
simples, pero puede ser ambiguo en otros casos: a veces no existe un valor especial claro, o puede coincidir
con un resultado válido. Aun así, es una técnica muy usada en C por su simplicidad.
#include <stdio.h> #include <math.h>
float raiz(float x) { if (x < 0) { return -1.0f; // valor especial para indicar error } return sqrtf(x); }
int main() { float n = -9.0f; float r = raiz(n);
if (r == -1.0f) {
} else {
printf("Error: no se puede calcular la raiz de un numero negativo.\n");
printf("Resultado: %.2f\n", r);
}
return 0;
}
Otra opción consiste en separar el “resultado” del “estado de error”. Por ejemplo, la función puede devolver
un código de éxito o fracaso, y escribir el resultado real en una variable pasada por referencia mediante
puntero. Este diseño suele ser más limpio, porque ya no se confunde el valor calculado con la señal de error.
Esta segunda alternativa es muy frecuente en C profesional, porque obliga a distinguir claramente entre “la
operación salió bien o mal” y “cuál es el dato obtenido”. Además, permite añadir más códigos de error en el
futuro, por ejemplo uno para argumento inválido y otro para desbordamiento.
#include <stdio.h> #include <math.h>
int raiz(float x, float *resultado) { if (x < 0) { return 0; // 0 = error } *resultado = sqrtf(x); return 1; // 1 = éxito }
int main() { float n = -9.0f; float r;
if (!raiz(n, &r)) {
} else {
printf("Error: no se puede calcular la raiz de un numero negativo.\n");
printf("Resultado: %.2f\n", r);
}
return 0;
}

## 2. Brevemente ¿Qué es una **"excepción"**? ¿Con qué objetivo las usa un programador cuando implementa funciones o cuando las llama?

Una excepción es un mecanismo del lenguaje para representar que durante la ejecución ha ocurrido una
situación anómala o errónea que impide seguir con el flujo normal. En lugar de devolver manualmente un
código de error en cada función, el lenguaje permite “lanzar” un objeto especial que viaja hacia fuera hasta
que alguien lo controle. Es, por tanto, una forma estructurada de comunicar fallos.
Al implementar funciones, las excepciones se usan para señalar problemas que la propia función detecta y no
puede resolver localmente, como recibir un argumento inválido o no poder abrir un fichero. Al llamar a
funciones, se usan para separar el código normal del código de tratamiento de errores, de manera que el
programa principal quede más claro: por un lado se intenta hacer la operación, y por otro se decide qué hacer
si falla.

## 3. Reescribe el mismo ejemplo de raiz, pero en Java, metiendo ese método en una clase `Calculadora` y llama a dicho método desde el método `main`, mostrando cómo se puede controlar desde fuera.

En Java, el método puede indicar el error lanzando una excepción en lugar de devolver un valor especial. Eso
permite que el método raiz se concentre en su trabajo: calcular la raíz si el dato es válido, y avisar de forma
clara si no lo es. Después, desde main, se captura esa excepción con un bloque try-catch.
Esto mejora bastante la claridad respecto al estilo típico de C. El resultado correcto sigue siendo un double, y
el error ya no se mezcla con el valor de retorno. Además, quien llama decide cómo reaccionar: mostrar un
mensaje, pedir otro número o terminar el programa.
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

## 4. ¿Qué es **"lanzar"** una excepción? ¿Qué es **"controlar"** o **"capturar"** una excepción? ¿Qué es que se **"propague"** una excepción? ¿Qué le va ocurriendo a las funciones en la pila de llamadas por donde se va propagando la excepción? ¿Las funciones que no la controlan se reanudan después de alguna forma? Explica con el mismo ejemplo anterior en Java de la raíz cuadrada.

Lanzar una excepción significa indicar que ha ocurrido un error mediante la instrucción throw. En el ejemplo
de la raíz cuadrada, si x < 0, el método raiz no devuelve un resultado normal, sino que lanza una
IllegalArgumentException. A partir de ese momento, la ejecución normal de ese método se interrumpe
inmediatamente.
Capturar o controlar una excepción significa colocar un try-catch en algún punto del código para interceptarla
y decidir qué hacer con ella. Si main llama a raiz(-9), la excepción sube hasta el catch, donde puede mostrarse
el mensaje de error. Si entre raiz y main hubiese otros métodos intermedios y ninguno la capturase, la
excepción seguiría subiendo: eso es propagarse.
Mientras la excepción se propaga, las funciones por las que pasa van terminando de forma abrupta. No
continúan “por donde iban”; su ejecución queda cancelada y se van sacando de la pila de llamadas hasta
encontrar un manejador adecuado. Las funciones que no capturan la excepción no se reanudan después: se
abandonan por completo. Solo se continúa desde el catch que finalmente la maneja, o el programa termina si
nadie la captura.
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

## 5. ¿Qué ventajas tiene frente a C, la **"propagación natural"** de las excepciones a través de la pila (_stack_) de llamadas?

La principal ventaja es que no obliga a comprobar manualmente un código de error en cada llamada. En C, si
una función llama a otra, y esta a otra, cada nivel debe acordarse de revisar el valor devuelto y reenviar el
error hacia arriba. Eso genera mucho código repetitivo, fácil de olvidar y bastante propenso a fallos. Con
excepciones, el error sube automáticamente por la pila hasta que alguien decida manejarlo.
Otra ventaja importante es la separación entre el flujo normal y el flujo de error. El código queda más limpio:
las funciones pueden devolver resultados normales cuando todo va bien, y usar excepciones solo cuando algo
falla. Además, esa propagación conserva información del error mientras atraviesa varios métodos, sin
necesidad de diseñar manualmente convenios de retorno como en C.

## 6. En orientación a objetos, ¿las excepciones suelen ser objetos? ¿Qué ventajas tiene esto en términos de encapsulación? ¿Podemos entonces crear excepciones personalizadas?

Sí. En Java, una excepción es un objeto. Eso encaja muy bien con la orientación a objetos, porque permite
representar el error como una entidad con estado y comportamiento propios. No se trata solo de “un número
de error” o “una cadena”, sino de una instancia de una clase que puede transportar información relevante
sobre lo ocurrido.
Desde el punto de vista de la encapsulación, esto permite agrupar dentro del propio objeto excepción todo lo
necesario para describir el problema: mensaje, causa original y cualquier otro dato útil. Así, quien lanza la excepción no necesita exponer detalles internos de implementación mediante variables sueltas o códigos
mágicos. Además, sí: pueden crearse excepciones personalizadas heredando de Exception o de
RuntimeException, lo que permite modelar errores del dominio del programa con más claridad.
public class EdadInvalidaException extends Exception { public EdadInvalidaException(String mensaje) {
super(mensaje); } }

## 7. En relación con las ventajas de la encapsulación, comparando el ejemplo en C con Java. ¿Qué **información esencial** lleva cualquier **objeto excepción** que es muy útil tener cuando se llega a un manejador?

La información esencial que lleva cualquier objeto excepción es, como mínimo, el tipo concreto de error y un
mensaje descriptivo. El tipo permite distinguir qué clase de problema ha ocurrido, por ejemplo si se trata de
un argumento inválido, un problema de entrada/salida o un error aritmético. El mensaje permite entender
mejor el motivo exacto cuando se llega al manejador.
Además, en Java suele conservarse también la traza de pila, es decir, el recorrido de llamadas por donde pasó
la ejecución hasta el punto en que se lanzó la excepción. Esa información es extremadamente útil para
depurar, porque permite saber no solo que falló una raíz cuadrada, sino también en qué método y desde qué
cadena de llamadas se llegó allí. Frente a C, esto da un contexto mucho más rico que un simple -1 o un 0.

## 8. En Java, sobre el bloque **"try-catch"**, ¿se pueden tener más de un bloque `catch`? ¿cuántos bloques `catch` se ejecutan?

Sí, en Java se pueden tener varios bloques catch detrás de un mismo try. Esto se hace cuando dentro del
bloque protegido pueden producirse distintos tipos de excepción y se quiere reaccionar de forma diferente
según cuál haya ocurrido. Cada catch se asocia a un tipo concreto de excepción.
Sin embargo, solo se ejecuta un único catch: el primero que resulte compatible con la excepción lanzada. Una
vez que una excepción entra en un catch, no se siguen probando los demás. Por eso el orden importa:
primero deben ponerse los catch más específicos y después los más generales.

## 9. Si las excepciones producen rupturas en el código llamador, ¿cómo podemos garantizar que se ejecuta siempre finalmente un código necesario para cierre de ficheros, liberacion de recursos, antes de que continúe propagándose la excepción? Pon un ejemplo en Java con `finally`, tanto con `catch` como sin él.

Para garantizar que cierto código se ejecute siempre, haya o no excepción, se usa el bloque finally. Su función
típica es liberar recursos: cerrar ficheros, conexiones o cualquier elemento que deba dejarse en un estado
correcto. Esto es necesario porque una excepción puede interrumpir el flujo normal antes de que se alcance
manualmente el cierre.
El finally se ejecuta tanto si la excepción se captura en ese mismo sitio como si no se captura y sigue
propagándose. Por eso resulta tan útil: asegura la limpieza antes de continuar. Puede aparecer junto con
catch, o incluso sin catch, cuando se quiere dejar que el error suba pero sin olvidar la liberación del recurso.
Ejemplo con catch y finally:
import java.io.BufferedReader; import java.io.FileReader; import java.io.IOException;
public class Ejemplo1 { public static void main(String[] args) { BufferedReader br = null;
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
Ejemplo sin catch, pero con finally:
import java.io.BufferedReader; import java.io.FileReader; import java.io.IOException;
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
}try {
leerFichero();
} catch (IOException e) {
System.out.println("Excepción propagada hasta main");
}
}

## 10. En Java, el bloque `finally` puede ir sin `catch`? ¿Se ejecuta siempre tanto si ocurre como si no ocurre una excepción? ¿Y si hay un `return` en medio del `try`?

Sí, finally puede ir sin catch, siempre que vaya acompañado de try. En ese caso, el bloque try protege el
código y finally garantiza la ejecución del bloque de limpieza, mientras que cualquier excepción que se
produzca seguirá propagándose hacia arriba. Es una construcción válida y bastante útil.
En condiciones normales, finally se ejecuta siempre: tanto si no ocurre ninguna excepción, como si ocurre una
y se captura, o incluso si ocurre y se propaga. También se ejecuta aunque dentro del try haya un return. Lo
que sucede es que antes de abandonar definitivamente el método, Java ejecuta el finally. Solo situaciones
muy anómalas, como terminar brutalmente la máquina virtual, pueden impedirlo.
public class PruebaFinally { public static int ejemplo() { try { System.out.println("Dentro de try"); return 5; }
finally { System.out.println("Dentro de finally"); } }
public static void main(String[] args) {
int x = ejemplo();
System.out.println("Valor devuelto: " + x);
}
}

## 11. En Java, qué son las excepciones **"controladas"** y las **"no controladas"**? ¿Qué papel juega `RuntimeException`? Pon un ejemplo de excepciones típicas controladas y no controladas que incluso nosotros mismos podríamos usar. Haz dos listas con 3 o 4 ejemplos de situación donde se suele preferir una excepción controlada y donde se suele preferir una excepción no controlada.

Las excepciones controladas son aquellas que el compilador obliga a tratar, bien capturándolas con try-catch,
bien declarándolas con throws. Suelen representar errores previsibles del entorno, como problemas al leer un
fichero o al acceder a un recurso externo. Las no controladas, en cambio, no obligan al llamador a hacer nada
explícito; normalmente representan fallos de programación o usos incorrectos de una operación.

## 12. ¿Qué es y para qué se usa `throws`? ¿Por qué es alternativa a capturar una excepción controlada?

RuntimeException es la clase base más importante dentro de las excepciones no controladas. Si una
excepción hereda de RuntimeException, el compilador no exige capturarla ni declararla. Esto se usa mucho
para errores como argumentos inválidos, índices fuera de rango o estados incoherentes del programa.
También pueden crearse excepciones propias, tanto controladas como no controladas, según el diseño que
interese.
Ejemplos:
// Excepción controlada personalizada class FicheroConfiguracionException extends Exception { public
FicheroConfiguracionException(String mensaje) { super(mensaje); } }
// Excepción no controlada personalizada class DatoInvalidoException extends RuntimeException { public
DatoInvalidoException(String mensaje) { super(mensaje); } }
Situaciones donde suele preferirse una excepción controlada:
·Al abrir un fichero que puede no existir.
·Al leer datos de red o de base de datos.
·Al procesar una entrada externa cuyo fallo es esperable.
·Al obligar al llamador a decidir qué hacer ante un problema recuperable.
Situaciones donde suele preferirse una excepción no controlada:
·Cuando se pasa un argumento inválido a un método.
·Cuando se intenta acceder a una posición imposible en un array o lista.
·Cuando un objeto se usa en un estado incorrecto por error del programador.
·Cuando el fallo indica un bug y no una incidencia normal del entorno.

## 13. Pon un ejemplo en Java de firma de método que incluya `throws`, de una función que abre un fichero pero que declara que no le interesa menejar la excepción de si el fichero no existe, sino que se propague hacia arriba. Eso sí, acuérdate del `finally`.

En este caso, el método declara throws IOException porque no quiere decidir qué hacer si falla la apertura o la
lectura del fichero. Sin embargo, aunque no capture la excepción, sí debe preocuparse de cerrar el recurso
correctamente si llegó a abrirse. Para eso se usa finally.
Así se ve claramente la diferencia entre “manejar el error” y “limpiar recursos”. El método no resuelve el
problema del fichero inexistente: simplemente lo deja subir. Pero sí garantiza que, si el fichero llegó a abrirse,
se cerrará antes de salir del método.
import java.io.BufferedReader; import java.io.FileReader; import java.io.IOException;
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
System.out.println("Error propagado: " + e.getMessage());
}
}
}

## 14. ¿Podemos poner en `throws` excepciones no controladas, como `RuntimeException`? ¿Debería el método llamador entonces poner `try-catch` en ese caso? ¿Qué sentido tendría?

Sí, técnicamente se pueden poner excepciones no controladas en throws, incluyendo las que heredan de
RuntimeException. Java lo permite. Sin embargo, no es obligatorio hacerlo, porque el compilador no exige ni
declararlas ni capturarlas. Por eso, en la práctica, muchas veces no se ponen.
El método llamador tampoco está obligado a usar try-catch en ese caso. Solo tendría sentido hacerlo si
realmente puede reaccionar de forma útil ante ese error. Por ejemplo, si se quiere interceptar una
IllegalArgumentException para informar mejor al usuario final. Si no existe una recuperación razonable,
capturarla solo para silenciarla suele ser mala idea.
public static double raiz(double x) throws IllegalArgumentException { if (x < 0) { throw new
IllegalArgumentException("Numero negativo"); } return Math.sqrt(x); }

## 15. ¿Cuándo se recomienda usar excepciones controladas, como `IOException`, y cuándo no controladas como `IllegalArgumentException`? ¿Existen en todos los lenguajes ambas opciones? En los que sólo existe una opción, ¿cuál es la más habitual?

Se recomienda usar excepciones controladas cuando el error es esperable y razonablemente recuperable,
sobre todo si depende del entorno externo y el llamador puede decidir qué hacer. IOException es un ejemplo
típico: un fichero puede no existir, la red puede fallar o un recurso externo puede no responder. Son
problemas reales que no siempre implican un bug en el programa.
Se recomienda usar excepciones no controladas cuando el error indica normalmente un mal uso del método
o un fallo de programación, como pasar un argumento inválido. IllegalArgumentException encaja aquí: si se
llama a una raíz con un número negativo, el problema está en cómo se usó el método. En muchos otros
lenguajes no existe esta distinción tan marcada como en Java. Lo más habitual, cuando solo hay una opción,
es un modelo parecido al de las no controladas: las excepciones pueden lanzarse y capturarse, pero el
compilador no obliga a declararlas.

## 16. ¿Tiene sentido lanzar excepciones dentro del `catch`? ¿Se puede relanzar la misma excepción capturada? ¿Cuándo tendría sentido hacer esto último? Pon ejemplos de ambos casos.

Sí, tiene sentido lanzar excepciones dentro de un catch. A veces se captura una excepción para traducirla a
otra más adecuada al nivel de abstracción actual. Por ejemplo, un método de alto nivel quizá no quiera
exponer directamente una IOException, y prefiera lanzar una excepción propia que diga algo como “error al
cargar la configuración”.
También se puede relanzar la misma excepción capturada usando throw e;. Eso tiene sentido cuando se quiere
hacer alguna acción previa, como registrar un mensaje, liberar un recurso adicional o añadir contexto, pero sin
ocultar la excepción original. En ese caso, el error sigue propagándose.
Ejemplo lanzando otra excepción distinta dentro del catch:
class ConfiguracionException extends Exception { public ConfiguracionException(String mensaje) {
super(mensaje); } }
public static void cargarConfiguracion() throws ConfiguracionException { try { throw new
java.io.IOException("No se pudo leer el fichero"); } catch (java.io.IOException e) { throw new
ConfiguracionException("Fallo al cargar la configuracion"); } }
Ejemplo relanzando la misma excepción:
public static void leerDatos() throws java.io.IOException { try { throw new java.io.IOException("Error de
lectura"); } catch (java.io.IOException e) { System.out.println("Se registra el error y se relanza"); throw e; } }

## 17. ¿En qué consiste que una excepción sea la **"causa"** de otra excepción? Pon un ejemplo en Java, donde capturemos una excepción de bajo nivel y la encapsulemos en otra personalizada de alto nivel. Cuando una excepción sale por pantalla y tiene una causa, ¿se ve?

Que una excepción sea la causa de otra significa que una excepción nueva se lanza para expresar un error de
nivel más alto, pero conservando dentro la excepción original que realmente provocó el problema. Esto es
muy útil cuando se quiere cambiar el “vocabulario” del error según la capa del programa, sin perder la
información técnica de fondo.
Por ejemplo, una capa de acceso a ficheros puede producir una IOException, pero una capa de negocio puede
querer convertir eso en una ConfiguracionException. Así, el código superior entiende que ha fallado la
configuración, y al mismo tiempo sigue siendo posible consultar la causa real. Cuando esa excepción se
imprime por pantalla con la traza, sí suele verse la causa, normalmente indicada con un texto como Caused
by.
import java.io.BufferedReader; import java.io.FileReader; import java.io.IOException;
class ConfiguracionException extends Exception { public ConfiguracionException(String mensaje, Throwable
causa) { super(mensaje, causa); } }
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
Si config.txt no existe, al mostrar la excepción aparecerá primero la excepción de alto nivel y, debajo, la causa
original. Eso permite entender a la vez el significado funcional del error y su motivo técnico exacto.
