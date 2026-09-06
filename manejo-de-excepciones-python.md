# Manejo de excepciones en Python: cómo leer un traceback y responder a él

> Complemento de `tipos-de-errores-python.md`. Ese documento explica **qué tipos de error existen**; este explica **qué hacer cuando aparecen**.

---

## 1. Cómo leer un traceback

Cuando una excepción no se captura, Python imprime un **traceback**: el recorrido exacto de llamadas que llevó al error. Se lee de arriba hacia abajo, pero **la información más útil está al final**.

```python
def dividir(a, b):
    return a / b

def procesar():
    return dividir(10, 0)

procesar()
```

```
Traceback (most recent call last):
  File "main.py", line 6, in <module>
    procesar()
  File "main.py", line 4, in procesar
    return dividir(10, 0)
  File "main.py", line 2, in dividir
    return a / b
ZeroDivisionError: division by zero
```

**Cómo interpretarlo:**
- La **última línea** (`ZeroDivisionError: division by zero`) dice **qué pasó** — el tipo de excepción y el mensaje.
- Las líneas anteriores muestran **la ruta de llamadas**, de la más externa (`procesar()`) a la más interna (`dividir`), donde ocurrió el fallo real.
- El número de línea y el archivo indicados junto a cada `File "..."` te llevan directo al punto exacto del código.

**Regla práctica:** empieza siempre por la última línea para saber el tipo de error, y solo después sube por el traceback para entender la cadena de llamadas que lo provocó.

---

## 2. Manejo con `try` / `except`

```python
try:
    resultado = 10 / 0
except ZeroDivisionError:
    print("No se puede dividir entre cero")
```

- El bloque `try` contiene el código que puede fallar.
- El bloque `except` se ejecuta **solo si** ocurre la excepción indicada.

**Capturar varios tipos de error:**

```python
try:
    valor = int(input("Ingresa un número: "))
except ValueError:
    print("Eso no es un número válido")
except KeyboardInterrupt:
    print("Operación cancelada por el usuario")
```

**Mala práctica a evitar:**

```python
try:
    hacer_algo()
except:          # captura TODO, incluso errores que no esperabas
    pass          # y los oculta en silencio
```

Un `except:` desnudo (sin especificar el tipo) atrapa cualquier excepción — incluida una que no tenga nada que ver con lo que intentabas manejar — y `pass` la hace desaparecer sin dejar rastro. Esto convierte un bug fácil de encontrar en uno invisible.

---

## 3. Las cuatro cláusulas: `try`, `except`, `else`, `finally`

```python
try:
    archivo = open("datos.txt", "r")
    contenido = archivo.read()
except FileNotFoundError:
    print("El archivo no existe")
else:
    print("Lectura exitosa")
    print(contenido)
finally:
    print("Este bloque se ejecuta siempre, haya error o no")
```

| Cláusula | ¿Cuándo se ejecuta? |
|---|---|
| `try` | Siempre — contiene el código que puede fallar |
| `except` | Solo si ocurrió la excepción indicada |
| `else` | Solo si **no** ocurrió ninguna excepción en el `try` |
| `finally` | Siempre, haya habido excepción o no (ideal para cerrar archivos, liberar recursos) |

---

## 4. Lanzar tus propias excepciones (`raise`)

A veces el error no lo detecta Python automáticamente — lo detecta la lógica de tu programa. Para esos casos, se usa `raise`:

```python
def procesar_pago(valor):
    if valor < 0:
        raise ValueError("El valor de la compra no puede ser negativo")
    return valor * 1.05
```

**Excepciones personalizadas:** cuando ningún error incorporado describe bien el problema, se puede crear una excepción propia heredando de `Exception`:

```python
class MetodoPagoNoSoportadoError(Exception):
    """Se lanza cuando se intenta usar un método de pago no reconocido."""
    pass

def procesar(metodo):
    if metodo not in ("tarjeta", "transferencia", "billetera", "cripto"):
        raise MetodoPagoNoSoportadoError(f"Método no soportado: {metodo}")
```

Esto permite capturarla igual que cualquier excepción nativa:

```python
try:
    procesar("bitcoin_directo")
except MetodoPagoNoSoportadoError as e:
    print(f"Error: {e}")
```

---

## 5. Checklist rápido

- [ ] ¿Leíste la última línea del traceback antes que nada?
- [ ] ¿Capturas el tipo de excepción específico, no un `except:` desnudo?
- [ ] ¿Usas `finally` para cerrar archivos o liberar recursos, en vez de duplicar ese código en cada rama?
- [ ] ¿Cuando lanzas un error propio, el mensaje explica *qué* pasó y *por qué*, no solo que "hubo un error"?

---

## Fuentes

- Documentación oficial de Python — [Tutorial: Errors and Exceptions](https://docs.python.org/3/tutorial/errors.html)
- Documentación oficial de Python — [The `try` statement (referencia del lenguaje)](https://docs.python.org/3/reference/compound_stmts.html#the-try-statement)
