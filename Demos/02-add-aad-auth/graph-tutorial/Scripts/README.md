<!-- IGNORE THE HTML BLOCK BELOW, THE INTERESTING PART IS AFTER IT -->

<h1 align="center">Popper. js</h1>

<p align="center">
    <strong>Una biblioteca que se usa para posicionar a los sacadores en las aplicaciones Web.</strong>
</p>

<p align="center">
    <a href="https://travis-ci.org/FezVrasta/popper.js/branches" target="_blank"><img src="https://travis-ci.org/FezVrasta/popper.js.svg?branch=master" alt="Build Status"/></a>
    <img src="http://img.badgesize.io/https://unpkg.com/popper.js/dist/popper.min.js?compression=gzip" alt="Stable Release Size"/>
    <a href="https://www.bithound.io/github/FezVrasta/popper.js"><img src="https://www.bithound.io/github/FezVrasta/popper.js/badges/score.svg" alt="bitHound Overall Score"></a>
    <a href="https://codeclimate.com/github/FezVrasta/popper.js/coverage"><img src="https://codeclimate.com/github/FezVrasta/popper.js/badges/coverage.svg" alt="Istanbul Code Coverage"/></a>
    <a href="https://gitter.im/FezVrasta/popper.js" target="_blank"><img src="https://img.shields.io/gitter/room/nwjs/nw.js.svg" alt="Get support or discuss"/></a>
    <br />
    <a href="https://saucelabs.com/u/popperjs" target="_blank"><img src="https://badges.herokuapp.com/browsers?labels=none&googlechrome=latest&firefox=latest&microsoftedge=latest&iexplore=11,10&safari=latest&iphone=latest" alt="SauceLabs Reports"/></a>
</p>

<img src="https://raw.githubusercontent.com/FezVrasta/popper.js/master/popperjs.png" align="right" width=250 />

<!-- 🚨 HEY! HERE BEGINS THE INTERESTING STUFF 🚨 -->

## <a name="wut-poppers"></a>Wut? ¿Los sacadores?

Popper es un elemento de la pantalla que "extrae" del flujo natural de la aplicación.  
Los ejemplos comunes de las sacantes son información sobre herramientas, popovers y listas desplegables.


## <a name="so-yet-another-tooltip-library"></a>Entonces, ¿aún hay otra biblioteca de información sobre herramientas?

Bueno, básicamente, **no**.  
Popper. js es un **motor de posicionamiento**, su propósito es calcular la posición de un elemento para que sea posible colocarlo cerca de un elemento de referencia determinado.  

El motor es completamente modular y la mayoría de sus características se implementan como **Modificadores** (similares a los middleware o los complementos).  
La base de código completa se escribe en ES2015 y sus características se prueban automáticamente en exploradores reales gracias a [SauceLabs](https://saucelabs.com/) y [TravisCI](https://travis-ci.org/).

Popper. js tiene cero dependencias. Sin jQuery, no LoDash, Nothing.  
Se usa en compañías grandes como [Twitter en bootstrap V4](https://getbootstrap.com/), [Microsoft en webclipper](https://github.com/OneNoteDev/WebClipper) y [Atlassian en AtlasKit](https://aui-cdn.atlassian.com/atlaskit/registry/).

### <a name="popperjs"></a>Popper. js

Este es el motor, la biblioteca que calcula y, de manera opcional, aplica los estilos a los sacadores.

Algunos de los puntos clave son:

- Colocar los elementos manteniéndolos en su contexto DOM original (no se está de acuerdo con el DOM);
- Permite exportar los datos calculados para integrarlos con reAct y otras bibliotecas de vistas;
- Admite elementos de sombra de DOM;
- Totalmente personalizable gracias a la estructura basada en los modificadores;

Visite nuestra [Página de proyecto](https://fezvrasta.github.io/popper.js) para ver una gran cantidad de ejemplos de lo que puede hacer con Popper. js.

Busque [aquí la documentación](/docs/_includes/popper-documentation.md).


### <a name="tooltipjs"></a>ToolTip. js

Dado que muchos usuarios solo necesitan una forma sencilla de integrar información sobre herramientas eficaz en sus proyectos, creamos **ToolTip. js**.  
Se trata de una biblioteca pequeña que facilita la creación automática de información sobre herramientas mediante el uso del motor Popper. js.  
Su API es prácticamente idéntica al famoso sistema de información sobre herramientas de bootstrap, de esta forma será fácil integrarla en sus proyectos.  
La información sobre herramientas generada por ToolTip. js es accesible gracias `aria` a las etiquetas.

Busque [aquí la documentación](/docs/_includes/tooltip-documentation.md).


## <a name="installation"></a>Instalación
Popper. js está disponible en los siguientes administradores de paquetes y CDN:

| Origen |                                                                                  |
|:-------|:---------------------------------------------------------------------------------|
| npm    | `npm install popper.js --save`                                                   |
| yarn   | `yarn add popper.js`                                                             |
| NuGet  | `PM> Install-Package popper.js`                                                  |
| Bower  | `bower install popper.js --save`                     |
| unpkg  | [`https://unpkg.com/popper.js`](https://unpkg.com/popper.js)                     |
| cdnjs  | [`https://cdnjs.com/libraries/popper.js`](https://cdnjs.com/libraries/popper.js) |

También la información sobre herramientas. js:

| Origen |                                                                                  |
|:-------|:---------------------------------------------------------------------------------|
| npm    | `npm install tooltip.js --save`                                                  |
| yarn   | `yarn add tooltip.js`                                                            |
| Bower* | `bower install tooltip.js=https://unpkg.com/tooltip.js --save`                   |
| unpkg  | [`https://unpkg.com/tooltip.js`](https://unpkg.com/tooltip.js)                   |
| cdnjs  | [`https://cdnjs.com/libraries/popper.js`](https://cdnjs.com/libraries/popper.js) |

\*: Bower no es compatible oficialmente, se puede usar para instalar ToolTip. js solo la red CDN unpkg.com. Este método tiene la limitación de no poder definir una versión específica de la biblioteca. Bower y Popper. js sugieren usar NPM o hilados para los proyectos.  
Para obtener más información, [Lea el problema relacionado](https://github.com/FezVrasta/popper.js/issues/390).

### <a name="dist-targets"></a>Destinos de distribución

Popper. js se distribuye actualmente con tres destinos en mente: UMD, ESM y ESNext.

- UMD-definición de módulo universal: AMD, RequireJS y Globals;
- Módulos de ESM-ES: para WebPack/Rollup o browser compatibles con las especificaciones;
- ESNext: disponible en `dist/`, puede usarse con WebPack y `babel-preset-env`;

Asegúrese de usar el adecuado para sus necesidades. Si quiere importarla con una `<script>` etiqueta, use UMD.

## <a name="usage"></a>Uso

Dado un nodo Popper DOM existente, pida a Popper. js que lo sitúe cerca de su botón.

```js
var reference = document.querySelector('.my-button');
var popper = document.querySelector('.my-popper');
var anotherPopper = new Popper(
    reference,
    popper,
    {
        // popper options here
    }
);
```

### <a name="callbacks"></a>Devoluciones

Popper. js admite dos tipos de devoluciones de `onCreate` llamada, la devolución de llamada se llama después de que se haya inicializado Popper. Se `onUpdate` llama a la misma en cualquier actualización posterior.

```js
const reference = document.querySelector('.my-button');
const popper = document.querySelector('.my-popper');
new Popper(reference, popper, {
    onCreate: (data) => {
        // data is an object containing all the informations computed
        // by Popper.js and used to style the popper and its arrow
        // The complete description is available in Popper.js documentation
    },
    onUpdate: (data) => {
        // same as `onCreate` but called on subsequent updates
    }
});
```

### <a name="writing-your-own-modifiers"></a>Escribir sus propios modificadores

Popper. js se basa en una arquitectura de tipo "complemento", la mayoría de sus características son "modificadores" totalmente encapsulados.  
Un modificador es una función a la que se llama cada vez que Popper. js necesita calcular la posición de la Popper. Por este motivo, los modificadores deben ser muy eficaz para evitar los cuellos de botella.  

Para obtener información sobre cómo crear un modificador, [Lea la documentación de los modificadores](docs/_includes/popper-documentation.md#modifiers--object)


### <a name="react-vuejs-angular-angularjs-emberjs-etc-integration"></a>La integración de reAct, Vue. js, angular, AngularJS, Ember. js (etc.)

La integración de bibliotecas de terceros en React o en otras bibliotecas puede ser un problema porque normalmente alteran el DOM y driven las bibliotecas loco.  
Popper. js limita todas sus modificaciones de DOM dentro `applyStyle` del modificador, sólo tiene que deshabilitarla y aplicar manualmente las coordenadas Popper con la biblioteca que elija.  

Para obtener una lista completa de las bibliotecas que le permiten usar Popper. js en los marcos existentes, [](/MENTIONS.md) visite la página menciones.

Como alternativa, puede incluso reemplazar su propio por `applyStyles` el suyo personalizado e integrar Popper. js por su cuenta.

```js
function applyReactStyle(data) {
    // export data in your framework and use its content to apply the style to your popper
};

const reference = document.querySelector('.my-button');
const popper = document.querySelector('.my-popper');
new Popper(reference, popper, {
    modifiers: {
        applyStyle: { enabled: false },
        applyReactStyle: {
            enabled: true,
            fn: applyReactStyle,
            order: 800,
        },
    },
});

```

### <a name="migration-from-popperjs-v0"></a>Migración desde Popper. js v0

Debido a que la API cambió, preparamos algunas instrucciones de migración para facilitar la actualización a Popper. js v1.  

https://github.com/FezVrasta/popper.js/issues/62

Si tiene alguna pregunta, no dude en comentar el problema.

### <a name="performances"></a>Excelentes

Popper. js es muy eficaz. Por lo general, suele tomar 0,5 ms para calcular la posición de un Popper (en un iMac con 3,5 G de i5 de núcleo de Intel).  
Esto significa que no provocará ningún [Jank](https://www.chromium.org/developers/how-tos/trace-event-profiling-tool/anatomy-of-jank), lo que llevará a una experiencia de usuario sin problemas.

## <a name="notes"></a>Notas

### <a name="libraries-using-popperjs"></a>Bibliotecas que usan Popper. js

El objetivo de Popper. js es proporcionar un motor de posicionamiento estable y eficaz listo para su uso en bibliotecas de terceros.  

Visite la [](/MENTIONS.md) página menciones para obtener una lista actualizada de proyectos.


### <a name="credits"></a>Créditos
Quiero agradecer a algunos amigos y proyectos por el trabajo que hicimos:

- [@AndreaScn](https://github.com/AndreaScn) para su trabajo en la página de Github y las pruebas manuales que realizó durante el desarrollo;
- [@vampolo](https://github.com/vampolo) de la idea original y del nombre de la biblioteca;
- [Sysdig](https://github.com/Draios) para todas las cosas impresionantes que aprendí durante estos años que me hicieron posible escribir esta biblioteca;
- [Tethering. js](http://github.hubspot.com/tether/) por haber sido inspirado al escribir una biblioteca de posicionamiento lista para el mundo real;
- [Los colaboradores](https://github.com/FezVrasta/popper.js/graphs/contributors) de sus solicitudes de extracción y informes de errores muy apreciados;
- **usted** para la estrella que va a dar a este proyecto y a ser tan maravilla para dar a este proyecto un intento🙂

### <a name="copyright-and-license"></a>Copyright y licencia
Código y documentación Copyright 2016 **Federico Zivolo**. Código lanzado en la [licencia MIT](LICENSE.md). Documentos publicados en Creative Commons.
