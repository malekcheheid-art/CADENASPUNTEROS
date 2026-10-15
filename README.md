# CADENASPUNTEROS — Manipulación de Cadenas y Punteros en C/C++

**Actividad 4** · Laboratorio de Programación (LPR) · 5° Año 3° División "A"
E.E.S.T. N.° 1 "Eduardo Ader" — Vicente López
**Profesor:** Yamil Ganduglia
**Estudiante:** Malek Cheheid

---

## ¿Qué es este proyecto?

Un sistema básico de **control de acceso por edad** escrito en C++, construido para
comprender **qué ocurre realmente dentro de la memoria RAM** cuando se ejecuta un
programa. En lugar de tratar las variables como cajas negras, el código las maneja
como **casilleros físicos con dirección**, usando punteros y desreferenciación.

---

## La idea central: la RAM como casilleros numerados

La memoria RAM se organiza como **casilleros numerados**, y cada casillero tiene una
**dirección física única**. Para el sistema operativo, una dirección no es un número
decimal cualquiera: es un número **hexadecimal** (base 16) antecedido por `0x`.

Esto es una decisión de ingeniería, no estética:

| Ventaja | Detalle |
|---|---|
| **Compactidad** | Un byte se escribe con **2 dígitos** en hexadecimal (`0xFF`), en lugar de hasta 3 en decimal. |
| **Alineación a potencia de 2** | Las direcciones de memoria están alineadas a 2, 4, 8 o 16 bytes, es decir, a **potencias de dos**. El sistema binario las representa de forma directa. |
| **Conversión trivial a binario** | Cada dígito hexadecimal equivale **exactamente** a 4 bits. `0x61fe1c` → `0110 0001 1111 1110 0001 1100`. |

Por eso el enunciado del ejemplo menciona direcciones como `0x61fe1c`: **6 dígitos
hexadecimales = 24 bits**, suficiente para direccionar 16 MB de memoria.

### Los dos operadores que hacen todo el trabajo

```cpp
int edad = 0;        // 1. La variable ocupa un casillero físico de la RAM
int* p = &edad;      // 2. '&' OBTIENE LA DIRECCIÓN de ese casillero y la copia en p
                     //    -> p NO contiene la edad, contiene la UBICACIÓN de la edad
cin >> *p;           // 3. '*' DESREFERENCIA: entra al casillero y escribe ahí
cout << *p;          //    Lee el valor alojado (21)
cout << p;           //    Imprime la dirección (0x61feff...)
```

- **`&` (operador de dirección)** → *¿dónde está?* Devuelve la ubicación física.
- **`*` (operador de desreferenciación)** → *¿qué hay ahí?* Lee o escribe el contenido.

**El error clásico:** escribir `cin >> p;` en lugar de `cin >> *p;`.
Como `p` es un `int*` y no un `int`, el compilador **rechaza la instrucción**:

```text
error: no match for 'operator>>' (operand types are 'std::istream' and 'int*')
```

`cin` solo sabe escribir sobre un `int&` (una referencia a entero). Al pasarle `p`,
que es un `int*`, no encuentra ningún candidato válido. Precisamente por eso el
asterisco es **obligatorio**: `*p` es una expresión de tipo `int` —una *referencia
al casillero apuntado*— y es exactamente eso lo que `cin` sabe escribir.
Con `cin >> *p;` el compilador entiende: *"entrá al casillero apuntado y escribí ahí"*.

> **Nota técnica:** este proyecto lo verificó empíricamente. El compilador
> `g++ 10.3.0 (tdm64)` rechaza `cin >> p;` en tiempo de compilación con el error
> `no match for 'operator>>'`. La protección no es una convención de estilo: el
> sistema de tipos impide que una dirección de memoria se sobrescriba con un dato.

---

## Estructura de carpetas

```
CADENASPUNTEROS/
├── .gitignore                      # Ignora la configuración de editores (.vscode)
├── LICENSE                         # Licencia MIT del proyecto (Uso escolar)
├── README.md                       # Este documento explicativo
├── docs/
│   └── InformeProyectoCADENASPUNTEROS.pdf   # ENTREGABLE: Informe en Normas APA v7
├── src/
│   └── main.cpp                    # Código fuente del programa en C++
└── capturas/
    ├── ejecucion_punteros.png      # Terminal mostrando las direcciones hexadecimales
    └── esquema_ram.png             # Diagrama de memoria de la RAM
```

---

## Compilación y ejecución (Windows 10/11)

Abrí la **terminal integrada de VS Code** (`Ctrl` + `Ñ`) en la raíz del proyecto y
seguí estos comandos:

```powershell
g++ src/main.cpp -o src/cadenaspunteros.exe
```

Luego ejecutá el programa generado:

```powershell
.\src\cadenaspunteros.exe
```

### Alternativa: OnlineGDB

Si la PC del laboratorio no permite compilar, la consigna obliga a usar
**OnlineGDB** para correr el código y tomar las capturas correspondientes.

---

## Ejemplo de salida real

```text
=====================================================
   CONTROL DE ACCESO RAM - ESTUDIANTE: Malek Cheheid
=====================================================
=> Ingrese su edad: 21

--- ANALIZANDO ACCESO SEGURO EN MEMORIA ---
[ACCESO APROBADO] El usuario es mayor de edad.
Edad registrada: 21 anos.
Direccion fisica en RAM Hexadecimal: 0xe11efffde4
=====================================================
```

> La dirección hexadecimal cambia en **cada ejecución**: depende del estado de la
> pila (*stack*) al momento de invocar `main()`. Lo que se demuestra no es un valor
> fijo, sino que **`p` contiene una dirección real y válida de la RAM**.

---

## Glosario

| Término | Definición |
|---|---|
| **Memoria RAM** | Espacio físico de almacenamiento temporal donde residen las variables durante la ejecución del programa. |
| **Dirección hexadecimal** | Representación numérica en base 16 (que inicia con `0x`) para identificar un casillero exacto de la RAM. |
| **Puntero (Pointer)** | Variable que guarda la dirección de memoria de otra variable del sistema. |
| **Operador de dirección (`&`)** | Operador que devuelve la dirección física en la RAM donde está guardada una variable. |
| **Desreferenciar (`*`)** | Operación para leer o modificar el valor contenido en la dirección de memoria apuntada. |

---

## Recursos oficiales

- [Referencia oficial C++ — Punteros y Memoria (cplusplus.com)](https://www.cplusplus.com/doc/tutorial/pointers/)
- [Microsoft — Operador de dirección `&`](https://learn.microsoft.com/es-es/cpp/cpp/address-of-operator-amp?view=msvc-170)
- [Microsoft — Operador de direccionamiento indirecto `*`](https://learn.microsoft.com/es-es/cpp/cpp/indirection-operator-star?view=msvc-170)
- [GeeksforGeeks — C++ Pointers](https://www.geeksforgeeks.org/cpp/cpp-pointers/)

---

## Licencia

Proyecto bajo licencia **MIT** — ver [`LICENSE`](LICENSE). Uso escolar.
