<! - {@a expresión-operadores} ->

# Operadores de expresión de plantilla

El lenguaje de expresión de plantilla angular emplea un subconjunto de sintaxis de JavaScript complementado con algunos operadores especiales
para escenarios específicos. Las siguientes secciones cubren tres de estos operadores:

* [tubería] (guía/plantilla-operadores-de-expresión#tubería)
* [operador de navegación segura] (guía/plantilla-expresión-operadores#operador-de-navegación-segura)
* [operador de aserción no nula] (guía/operadores-de-expresión-de-plantilla#operador-de-aserción-no-nula)

<div class="la alerta es útil">

Consulta el <live-example></live-example> para ver un ejemplo funcional que contiene los fragmentos de código de esta guía.

</div>

{@a pipe}

## El operador de tubería (`|`)

El resultado de una expresión puede requerir alguna transformación antes de que esté listo para usarlo en un enlace.
Por ejemplo, puedes mostrar un número como moneda, cambiar el texto a mayúsculas o filtrar una lista y ordenarla.

Las tuberías son funciones simples que aceptan un valor de entrada y devuelven un valor transformado.
Son fáciles de aplicar dentro de las expresiones de plantilla, utilizando el operador de tubería (`|`):

<code-example path="template-expression-operator/src/app/app.component.html" region="uppercase-pipe" header="src/app/app.component.html"> </code-example>

El operador de tubería pasa el resultado de una expresión de la izquierda a una función de tubería de la derecha.

Puedes encadenar expresiones a través de múltiples conductos:
<code-example ruta="plantilla-expresión-operadores/src/app/app.component.html" region="pipe-chain" header="src/app/app.component.html"> </code-example>

Y también puedes [aplicar parámetros] (guía/tuberías#parametrización-de-una-tubería) a una tubería:

<code-example path="template-expression-operators/src/app/app.component.html" region="uppercase-pipe" header="src/app/app.component.html"></code-example>

La tubería `json` es particularmente útil para depurar enlaces:

<code-example path="template-expression-operators/src/app/app.component.html" region="pipe-chain" header="src/app/app.component.html"></code-example>

La salida generada se vería así:

<code-example language="json">
  { "name": "Teléfono",
    "manufactureDate": "1980-02-25T05:00:00.000Z",
    "price": 98 }
</code-example>

<div class="la alerta es útil">

El operador de tubería tiene una precedencia mayor que el operador ternario (`?:`),
que significa ʻa? b: c | x` se analiza como ʻa? b: (c | x) `.
Sin embargo, por varias razones,
el operador de tubería no se puede utilizar sin paréntesis en el primer y segundo operandos de `?:`.
Una buena práctica es utilizar paréntesis también en el tercer operando.

</div>

<h/>

{@a safe-navigation-operator}

## El operador de navegación segura (`?`) Y rutas de propiedad nulas

El operador de navegación segura Angular, `?`, Protege contra `null` y `undefined`
valores en rutas de propiedad. Aquí, protege contra un error de renderizado de la vista si `item` es `null`.

<code-example path="template-expression-operators/src/app/app.component.html" region="json-pipe" header="src/app/app.component.html"></code-example>

Si `item` es `null`, la vista aún se procesa pero el valor mostrado está en blanco; solo verá "El nombre del elemento es:" sin nada después.

Considera el siguiente ejemplo, con un `nullItem`.

<code-example language="html">
 El nombre del elemento nulo es {{nullItem.name}}
</code-example>

Dado que no hay un operador de navegación seguro y `nullItem` es` null`, JavaScript y Angular arrojarían un error de referencia `null` y romperían el proceso de renderizado de Angular:

<code-example language="bash">
 TypeError: no se puede leer la propiedad 'nombre' de nulo.
</code-example>

A veces, sin embargo, los valores "nulos" en la propiedad
ruta puede estar bien en determinadas circunstancias,
especialmente cuando el valor comienza siendo nulo pero los datos llegan eventualmente.

Con el operador de navegación segura, `?`, Angular deja de evaluar la expresión cuando llega al primer valor `nulo` y muestra la vista sin errores.

Funciona perfectamente con rutas de propiedad largas como `a? .B? .C? .D`.

<h/>

{@a operador de aserción no nula}

## El operador de aserción no nula (`!`)

A partir de TypeScript 2.0, puede hacer cumplir [verificación nula estricta] (http://www.typescriptlang.org/docs/handbook/release-notes/typescript-2-0.html "Verificación nula estricta en TypeScript") con el `Bandera --strictNullChecks`. TypeScript luego asegura que ninguna variable sea involuntariamente "nula" o "indefinida".

En este modo, las variables escritas no permiten "nulo" y "no definido" por defecto. El verificador de tipos arroja un error si dejas una variable sin asignar o intentas asignar "nulo" o "indefinido" a una variable cuyo tipo no permite "nulo" y "indefinido".

El verificador de tipos también arroja un error si no puede determinar si una variable será "nula" o "indefinida" en tiempo de ejecución. Le dice al verificador de tipos que no arroje un error aplicando el sufijo
[operador de aserción no nula,!] (http://www.typescriptlang.org/docs/handbook/release-notes/typescript-2-0.html#non-null-assertion-operator "Operador de aserción no nula" ).

El operador de aserción no nula angular, `!`, Tiene el mismo propósito en
una plantilla angular. Por ejemplo, puede afirmar que las propiedades de `item` también están definidas.

<code-example path="template-expression-operators/src/app/app.component.html" region="non-null" header="src/app/app.component.html"></code-example>

Cuando el compilador Angular convierte tu plantilla en código TypeScript,
evita que TypeScript informe que `item.color` podría ser `null` o `undefined`.

A diferencia del [operador de navegación segura] (guia/plantilla-expresion-operadores#operador-de-navegación-segura "Operador de navegación segura (?)"),
el operador de aserción no nula no protege contra "nulo" o "indefinido".
Más bien, le dice al verificador de tipos de TypeScript que suspenda las comprobaciones estrictas "nulas" para una expresión de propiedad específica.

El operador de aserción no nula, `!`, Es opcional con la excepción de que debe usarlo cuando activa las verificaciones nulas estrictas.

{@a función de conversión de cualquier tipo}

## La función de conversión de tipo `$any()`

A veces, una expresión vinculante desencadena un error de tipo durante la [compilación AOT] (guía/compilador aot) y no es posible o difícil especificar completamente el tipo.
Para silenciar el error, puede usar la función de conversión `$any()` para transmitir
la expresión al [`cualquier` tipo] (http://www.typescriptlang.org/docs/handbook/basic-types.html#any) como en el siguiente ejemplo:

<code-example path="built-in-template-functions/src/app/app.component.html" region="any-type-cast-function-1" header="src/app/app.component.html"></code-example>

Cuando el compilador Angular convierte esta plantilla en código TypeScript,
evita que TypeScript informe que `bestByDate` no es miembro del `item`
objeto cuando ejecuta la verificación de tipos en la plantilla.

La función de conversión `$any()` también funciona con `this` para permitir el acceso a miembros no declarados de
el componente.

<code-example path="built-in-template-functions/src/app/app.component.html" region="any-type-cast-function-2" header="src/app/app.component.html"> </code-example>

La función de conversión `$any()` funciona en cualquier lugar de una expresión de enlace donde una llamada a un método es válida.

