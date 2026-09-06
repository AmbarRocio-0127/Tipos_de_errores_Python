# Tipos de errores en Python

> Basado en la documentación oficial de Python (`docs.python.org/3/library/exceptions.html` y `docs.python.org/3/tutorial/errors.html`).

En Python, los errores se agrupan en tres grandes categorías: **errores de sintaxis**, **errores lógicos** y **excepciones en tiempo de ejecución**. Solo las dos primeras y la tercera tienen un tratamiento distinto por parte del intérprete — es clave diferenciarlas para saber cómo depurarlas.

---

## 1. Errores de sintaxis (`SyntaxError`)

Ocurren cuando el código no respeta la gramática del lenguaje. El intérprete los detecta **antes de ejecutar cualquier línea** — ni siquiera llega a correr el programa.

```python
if True
    print("falta el dos puntos")
```

```
SyntaxError: expected ':'
```

**Variantes comunes:**
- `IndentationError`: subclase de `SyntaxError`, ocurre por indentación incorrecta o inconsistente (mezclar tabs y espacios, por ejemplo).
- `TabError`: cuando la indentación mezcla tabuladores y espacios de forma ambigua.

**Cómo se detectan:** el propio intérprete señala la línea exacta (o muy cercana) donde rompe la gramática, antes de ejecutar nada.

---

## 2. Errores lógicos

Son los más difíciles de detectar porque **el programa corre sin lanzar ninguna excepción**, pero produce un resultado incorrecto. No existen como una clase de error en Python — no hay un `LogicError` — porque desde la perspectiva del intérprete, el código es perfectamente válido.

**Ejemplo real de esta conversación:**

```python
# Se esperaba un descuento del 2%
def procesar_pago(self, valor):
    descuento = valor * 0.2   # 20% en vez de 2%
    return valor - descuento
```

El programa ejecuta sin errores y entrega un número — solo que es el número equivocado. La única forma de detectarlos es **verificar el resultado contra un caso de prueba conocido** (por eso, en el ejercicio de pagos, comparar contra el ejemplo numérico del enunciado fue la única manera de encontrar el bug).

---

## 3. Excepciones en tiempo de ejecución (`Exception`)

Ocurren durante la ejecución del programa, cuando una operación válida sintácticamente no puede completarse (dividir por cero, acceder a una clave que no existe, abrir un archivo inexistente, etc.). En Python, **todas las excepciones heredan de `BaseException`**, y la inmensa mayoría de las que se manejan en el código de una aplicación heredan de su subclase `Exception`.

### Jerarquía simplificada (excepciones más comunes)

```
BaseException
 ├── SystemExit
 ├── KeyboardInterrupt
 ├── GeneratorExit
 └── Exception
      ├── ArithmeticError
      │    ├── ZeroDivisionError
      │    ├── OverflowError
      │    └── FloatingPointError
      ├── AttributeError
      ├── ImportError
      │    └── ModuleNotFoundError
      ├── LookupError
      │    ├── IndexError
      │    └── KeyError
      ├── NameError
      │    └── UnboundLocalError
      ├── OSError
      │    ├── FileNotFoundError
      │    ├── FileExistsError
      │    └── PermissionError
      ├── TypeError
      ├── ValueError
      └── AssertionError
```

### Las más frecuentes en la práctica

| Excepción | Cuándo ocurre | Ejemplo |
|---|---|---|
| `AttributeError` | Se accede a un atributo o método que el objeto no tiene | `self.valor` cuando el `__init__` guardó `self.value` |
| `NameError` | Se usa una variable que no fue definida | Usar `total` sin haberla asignado antes |
| `TypeError` | Se aplica una operación a un tipo de dato incompatible | `"5" + 5` (sumar texto y número) |
| `ValueError` | El tipo es correcto, pero el valor no es válido para la operación | `int("hola")` |
| `IndexError` | Se accede a una posición de una lista que no existe | `lista[10]` en una lista de 3 elementos |
| `KeyError` | Se accede a una clave de un diccionario que no existe | `diccionario["clave_inexistente"]` |
| `ZeroDivisionError` | Se divide entre cero | `10 / 0` |
| `ImportError` / `ModuleNotFoundError` | No se encuentra el módulo o el nombre importado | `from Modulo import Clase` cuando el archivo no existe o el nombre está mal escrito |
| `FileNotFoundError` | Se intenta abrir un archivo que no existe en la ruta indicada | `open("archivo.txt")` |

### Manejo con `try` / `except`

A diferencia de los errores de sintaxis, las excepciones en tiempo de ejecución pueden **capturarse y manejarse** sin detener el programa:

```python
try:
    resultado = 10 / 0
except ZeroDivisionError:
    print("No se puede dividir entre cero")
```

**Buena práctica:** capturar siempre la excepción más específica posible (`ZeroDivisionError`, no `Exception` genérico), para no ocultar errores distintos a los que realmente se está manejando.

---

## Resumen: ¿cómo diferenciarlos?

| Tipo de error | ¿Cuándo se detecta? | ¿El programa llega a ejecutarse? | ¿Se puede capturar con `try/except`? |
|---|---|---|---|
| Sintaxis (`SyntaxError`) | Antes de ejecutar | No | No |
| Lógico | Nunca lo detecta el intérprete | Sí, corre completo | No aplica (no lanza excepción) |
| Excepción en tiempo de ejecución | Durante la ejecución, en la línea que falla | Sí, hasta ese punto | Sí |

---

## Fuentes

- Documentación oficial de Python — [Built-in Exceptions](https://docs.python.org/3/library/exceptions.html)
- Documentación oficial de Python — [Tutorial: Errors and Exceptions](https://docs.python.org/3/tutorial/errors.html)
