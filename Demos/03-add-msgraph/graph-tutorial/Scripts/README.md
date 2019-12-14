<!-- IGNORE THE HTML BLOCK BELOW, THE INTERESTING PART IS AFTER IT -->

<h1 align="center"><span data-ttu-id="5b2d4-101">Popper. js</span><span class="sxs-lookup"><span data-stu-id="5b2d4-101">Popper.js</span></span></h1>

<p align="center"><span data-ttu-id="5b2d4-102">
    <strong>Una biblioteca que se usa para posicionar a los sacadores en las aplicaciones Web.</strong>
</span><span class="sxs-lookup"><span data-stu-id="5b2d4-102">
    <strong>A library used to position poppers in web applications.</strong>
</span></span></p>

<p align="center">
    <img src="http://badge-size.now.sh/https://unpkg.com/popper.js/dist/popper.min.js?compression=brotli" alt="Stable Release Size"/>
  <img src="http://badge-size.now.sh/https://unpkg.com/popper.js/dist/popper.min.js?compression=gzip" alt="Stable Release Size"/>
    <a href="https://codeclimate.com/github/FezVrasta/popper.js/coverage"><img src="https://codeclimate.com/github/FezVrasta/popper.js/badges/coverage.svg" alt="Istanbul Code Coverage"/></a>
    <a href="https://www.npmjs.com/browse/depended/popper.js"><img src="https://badgen.net/npm/dependents/popper.js" alt="Dependents packages" /></a>
    <a href="https://spectrum.chat/popper-js" target="_blank"><img src="https://img.shields.io/badge/chat-on_spectrum-6833F9.svg?logo=data%3Aimage%2Fsvg%2Bxml%3Bbase64%2CPHN2ZyBpZD0iTGl2ZWxsb18xIiBkYXRhLW5hbWU9IkxpdmVsbG8gMSIgeG1sbnM9Imh0dHA6Ly93d3cudzMub3JnLzIwMDAvc3ZnIiB2aWV3Qm94PSIwIDAgMTAgOCI%2BPGRlZnM%2BPHN0eWxlPi5jbHMtMXtmaWxsOiNmZmY7fTwvc3R5bGU%2BPC9kZWZzPjx0aXRsZT5zcGVjdHJ1bTwvdGl0bGU%2BPHBhdGggY2xhc3M9ImNscy0xIiBkPSJNNSwwQy40MiwwLDAsLjYzLDAsMy4zNGMwLDEuODQuMTksMi43MiwxLjc0LDMuMWgwVjcuNThhLjQ0LjQ0LDAsMCwwLC42OC4zNUw0LjM1LDYuNjlINWM0LjU4LDAsNS0uNjMsNS0zLjM1UzkuNTgsMCw1LDBaTTIuODMsNC4xOGEuNjMuNjMsMCwxLDEsLjY1LS42M0EuNjQuNjQsMCwwLDEsMi44Myw0LjE4Wk01LDQuMThhLjYzLjYzLDAsMSwxLC42NS0uNjNBLjY0LjY0LDAsMCwxLDUsNC4xOFptMi4xNywwYS42My42MywwLDEsMSwuNjUtLjYzQS42NC42NCwwLDAsMSw3LjE3LDQuMThaIi8%2BPC9zdmc%2B" alt="Get support or discuss"/></a>
    <br />
    <a href="https://travis-ci.org/FezVrasta/popper.js/branches" target="_blank"><img src="https://travis-ci.org/FezVrasta/popper.js.svg?branch=master" alt="Build Status"/></a>
    <a href="https://saucelabs.com/u/popperjs" target="_blank"><img src="https://badges.herokuapp.com/browsers?labels=none&googlechrome=latest&firefox=latest&microsoftedge=latest&iexplore=11,10&safari=latest" alt="SauceLabs Reports"/></a>
</p>

<img src="https://raw.githubusercontent.com/FezVrasta/popper.js/master/popperjs.png" align="right" width=250 />

<!-- 🚨 HEY! HERE BEGINS THE INTERESTING STUFF 🚨 -->

## <a name="wut-poppers"></a><span data-ttu-id="5b2d4-103">Wut?</span><span class="sxs-lookup"><span data-stu-id="5b2d4-103">Wut?</span></span> <span data-ttu-id="5b2d4-104">¿Los sacadores?</span><span class="sxs-lookup"><span data-stu-id="5b2d4-104">Poppers?</span></span>

<span data-ttu-id="5b2d4-105">Popper es un elemento de la pantalla que "extrae" del flujo natural de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="5b2d4-105">A popper is an element on the screen which "pops out" from the natural flow of your application.</span></span>  
<span data-ttu-id="5b2d4-106">Los ejemplos comunes de las sacantes son información sobre herramientas, popovers y listas desplegables.</span><span class="sxs-lookup"><span data-stu-id="5b2d4-106">Common examples of poppers are tooltips, popovers and drop-downs.</span></span>


## <a name="so-yet-another-tooltip-library"></a><span data-ttu-id="5b2d4-107">Entonces, ¿aún hay otra biblioteca de información sobre herramientas?</span><span class="sxs-lookup"><span data-stu-id="5b2d4-107">So, yet another tooltip library?</span></span>

<span data-ttu-id="5b2d4-108">Bueno, básicamente, **no**.</span><span class="sxs-lookup"><span data-stu-id="5b2d4-108">Well, basically, **no**.</span></span>  
<span data-ttu-id="5b2d4-109">Popper. js es un **motor de posicionamiento**, su propósito es calcular la posición de un elemento para que sea posible colocarlo cerca de un elemento de referencia determinado.</span><span class="sxs-lookup"><span data-stu-id="5b2d4-109">Popper.js is a **positioning engine**, its purpose is to calculate the position of an element to make it possible to position it near a given reference element.</span></span>  

<span data-ttu-id="5b2d4-110">El motor es completamente modular y la mayoría de sus características se implementan como **Modificadores** (similares a los middleware o los complementos).</span><span class="sxs-lookup"><span data-stu-id="5b2d4-110">The engine is completely modular and most of its features are implemented as **modifiers** (similar to middlewares or plugins).</span></span>  
<span data-ttu-id="5b2d4-111">La base de código completa se escribe en ES2015 y sus características se prueban automáticamente en exploradores reales gracias a [SauceLabs](https://saucelabs.com/) y [TravisCI](https://travis-ci.org/).</span><span class="sxs-lookup"><span data-stu-id="5b2d4-111">The whole code base is written in ES2015 and its features are automatically tested on real browsers thanks to [SauceLabs](https://saucelabs.com/) and [TravisCI](https://travis-ci.org/).</span></span>

<span data-ttu-id="5b2d4-112">Popper. js tiene cero dependencias.</span><span class="sxs-lookup"><span data-stu-id="5b2d4-112">Popper.js has zero dependencies.</span></span> <span data-ttu-id="5b2d4-113">Sin jQuery, no LoDash, Nothing.</span><span class="sxs-lookup"><span data-stu-id="5b2d4-113">No jQuery, no LoDash, nothing.</span></span>  
<span data-ttu-id="5b2d4-114">Se usa en compañías grandes como [Twitter en bootstrap V4](https://getbootstrap.com/), [Microsoft en webclipper](https://github.com/OneNoteDev/WebClipper) y [Atlassian en AtlasKit](https://aui-cdn.atlassian.com/atlaskit/registry/).</span><span class="sxs-lookup"><span data-stu-id="5b2d4-114">It's used by big companies like [Twitter in Bootstrap v4](https://getbootstrap.com/), [Microsoft in WebClipper](https://github.com/OneNoteDev/WebClipper) and [Atlassian in AtlasKit](https://aui-cdn.atlassian.com/atlaskit/registry/).</span></span>

### <a name="popperjs"></a><span data-ttu-id="5b2d4-115">Popper. js</span><span class="sxs-lookup"><span data-stu-id="5b2d4-115">Popper.js</span></span>

<span data-ttu-id="5b2d4-116">Este es el motor, la biblioteca que calcula y, de manera opcional, aplica los estilos a los sacadores.</span><span class="sxs-lookup"><span data-stu-id="5b2d4-116">This is the engine, the library that computes and, optionally, applies the styles to the poppers.</span></span>

<span data-ttu-id="5b2d4-117">Algunos de los puntos clave son:</span><span class="sxs-lookup"><span data-stu-id="5b2d4-117">Some of the key points are:</span></span>

- <span data-ttu-id="5b2d4-118">Colocar los elementos manteniéndolos en su contexto DOM original (no se está de acuerdo con el DOM);</span><span class="sxs-lookup"><span data-stu-id="5b2d4-118">Position elements keeping them in their original DOM context (doesn't mess with your DOM!);</span></span>
- <span data-ttu-id="5b2d4-119">Permite exportar los datos calculados para integrarlos con reAct y otras bibliotecas de vistas;</span><span class="sxs-lookup"><span data-stu-id="5b2d4-119">Allows to export the computed informations to integrate with React and other view libraries;</span></span>
- <span data-ttu-id="5b2d4-120">Admite elementos de sombra de DOM;</span><span class="sxs-lookup"><span data-stu-id="5b2d4-120">Supports Shadow DOM elements;</span></span>
- <span data-ttu-id="5b2d4-121">Totalmente personalizable gracias a la estructura basada en los modificadores;</span><span class="sxs-lookup"><span data-stu-id="5b2d4-121">Completely customizable thanks to the modifiers based structure;</span></span>

<span data-ttu-id="5b2d4-122">Visite nuestra [Página de proyecto](https://fezvrasta.github.io/popper.js) para ver una gran cantidad de ejemplos de lo que puede hacer con Popper. js.</span><span class="sxs-lookup"><span data-stu-id="5b2d4-122">Visit our [project page](https://fezvrasta.github.io/popper.js) to see a lot of examples of what you can do with Popper.js!</span></span>

<span data-ttu-id="5b2d4-123">Busque [aquí la documentación](/docs/_includes/popper-documentation.md).</span><span class="sxs-lookup"><span data-stu-id="5b2d4-123">Find [the documentation here](/docs/_includes/popper-documentation.md).</span></span>


### <a name="tooltipjs"></a><span data-ttu-id="5b2d4-124">ToolTip. js</span><span class="sxs-lookup"><span data-stu-id="5b2d4-124">Tooltip.js</span></span>

<span data-ttu-id="5b2d4-125">Dado que muchos usuarios solo necesitan una forma sencilla de integrar información sobre herramientas eficaz en sus proyectos, creamos **ToolTip. js**.</span><span class="sxs-lookup"><span data-stu-id="5b2d4-125">Since lots of users just need a simple way to integrate powerful tooltips in their projects, we created **Tooltip.js**.</span></span>  
<span data-ttu-id="5b2d4-126">Se trata de una biblioteca pequeña que facilita la creación automática de información sobre herramientas mediante el uso del motor Popper. js.</span><span class="sxs-lookup"><span data-stu-id="5b2d4-126">It's a small library that makes it easy to automatically create tooltips using as engine Popper.js.</span></span>  
<span data-ttu-id="5b2d4-127">Su API es prácticamente idéntica al famoso sistema de información sobre herramientas de bootstrap, de esta forma será fácil integrarla en sus proyectos.</span><span class="sxs-lookup"><span data-stu-id="5b2d4-127">Its API is almost identical to the famous tooltip system of Bootstrap, in this way it will be easy to integrate it in your projects.</span></span>  
<span data-ttu-id="5b2d4-128">La información sobre herramientas generada por ToolTip. js es accesible gracias `aria` a las etiquetas.</span><span class="sxs-lookup"><span data-stu-id="5b2d4-128">The tooltips generated by Tooltip.js are accessible thanks to the `aria` tags.</span></span>

<span data-ttu-id="5b2d4-129">Busque [aquí la documentación](/docs/_includes/tooltip-documentation.md).</span><span class="sxs-lookup"><span data-stu-id="5b2d4-129">Find [the documentation here](/docs/_includes/tooltip-documentation.md).</span></span>


## <a name="installation"></a><span data-ttu-id="5b2d4-130">Instalación</span><span class="sxs-lookup"><span data-stu-id="5b2d4-130">Installation</span></span>
<span data-ttu-id="5b2d4-131">Popper. js está disponible en los siguientes administradores de paquetes y CDN:</span><span class="sxs-lookup"><span data-stu-id="5b2d4-131">Popper.js is available on the following package managers and CDNs:</span></span>

| <span data-ttu-id="5b2d4-132">Origen</span><span class="sxs-lookup"><span data-stu-id="5b2d4-132">Source</span></span> |                                                                                  |
|:-------|:---------------------------------------------------------------------------------|
| <span data-ttu-id="5b2d4-133">npm</span><span class="sxs-lookup"><span data-stu-id="5b2d4-133">npm</span></span>    | `npm install popper.js --save`                                                   |
| <span data-ttu-id="5b2d4-134">yarn</span><span class="sxs-lookup"><span data-stu-id="5b2d4-134">yarn</span></span>   | `yarn add popper.js`                                                             |
| <span data-ttu-id="5b2d4-135">NuGet</span><span class="sxs-lookup"><span data-stu-id="5b2d4-135">NuGet</span></span>  | `PM> Install-Package popper.js`                                                  |
| <span data-ttu-id="5b2d4-136">Bower</span><span class="sxs-lookup"><span data-stu-id="5b2d4-136">Bower</span></span>  | `bower install popper.js --save`                     |
| <span data-ttu-id="5b2d4-137">unpkg</span><span class="sxs-lookup"><span data-stu-id="5b2d4-137">unpkg</span></span>  | [`https://unpkg.com/popper.js`](https://unpkg.com/popper.js)                     |
| <span data-ttu-id="5b2d4-138">cdnjs</span><span class="sxs-lookup"><span data-stu-id="5b2d4-138">cdnjs</span></span>  | [`https://cdnjs.com/libraries/popper.js`](https://cdnjs.com/libraries/popper.js) |

<span data-ttu-id="5b2d4-139">También la información sobre herramientas. js:</span><span class="sxs-lookup"><span data-stu-id="5b2d4-139">Tooltip.js as well:</span></span>

| <span data-ttu-id="5b2d4-140">Origen</span><span class="sxs-lookup"><span data-stu-id="5b2d4-140">Source</span></span> |                                                                                  |
|:-------|:---------------------------------------------------------------------------------|
| <span data-ttu-id="5b2d4-141">npm</span><span class="sxs-lookup"><span data-stu-id="5b2d4-141">npm</span></span>    | `npm install tooltip.js --save`                                                  |
| <span data-ttu-id="5b2d4-142">yarn</span><span class="sxs-lookup"><span data-stu-id="5b2d4-142">yarn</span></span>   | `yarn add tooltip.js`                                                            |
| <span data-ttu-id="5b2d4-143">Bower\*</span><span class="sxs-lookup"><span data-stu-id="5b2d4-143">Bower\*</span></span> | `bower install tooltip.js=https://unpkg.com/tooltip.js --save`                   |
| <span data-ttu-id="5b2d4-144">unpkg</span><span class="sxs-lookup"><span data-stu-id="5b2d4-144">unpkg</span></span>  | [`https://unpkg.com/tooltip.js`](https://unpkg.com/tooltip.js)                   |
| <span data-ttu-id="5b2d4-145">cdnjs</span><span class="sxs-lookup"><span data-stu-id="5b2d4-145">cdnjs</span></span>  | [`https://cdnjs.com/libraries/popper.js`](https://cdnjs.com/libraries/popper.js) |

<span data-ttu-id="5b2d4-146">\*: Bower no es compatible oficialmente, se puede usar para instalar ToolTip. js solo la red CDN unpkg.com.</span><span class="sxs-lookup"><span data-stu-id="5b2d4-146">\*: Bower isn't officially supported, it can be used to install Tooltip.js only trough the unpkg.com CDN.</span></span> <span data-ttu-id="5b2d4-147">Este método tiene la limitación de no poder definir una versión específica de la biblioteca.</span><span class="sxs-lookup"><span data-stu-id="5b2d4-147">This method has the limitation of not being able to define a specific version of the library.</span></span> <span data-ttu-id="5b2d4-148">Bower y Popper. js sugieren usar NPM o hilados para los proyectos.</span><span class="sxs-lookup"><span data-stu-id="5b2d4-148">Bower and Popper.js suggests to use npm or Yarn for your projects.</span></span>  
<span data-ttu-id="5b2d4-149">Para obtener más información, [Lea el problema relacionado](https://github.com/FezVrasta/popper.js/issues/390).</span><span class="sxs-lookup"><span data-stu-id="5b2d4-149">For more info, [read the related issue](https://github.com/FezVrasta/popper.js/issues/390).</span></span>

### <a name="dist-targets"></a><span data-ttu-id="5b2d4-150">Destinos de distribución</span><span class="sxs-lookup"><span data-stu-id="5b2d4-150">Dist targets</span></span>

<span data-ttu-id="5b2d4-151">Popper. js se distribuye actualmente con tres destinos en mente: UMD, ESM y ESNext.</span><span class="sxs-lookup"><span data-stu-id="5b2d4-151">Popper.js is currently shipped with 3 targets in mind: UMD, ESM and ESNext.</span></span>

- <span data-ttu-id="5b2d4-152">UMD-definición de módulo universal: AMD, RequireJS y Globals;</span><span class="sxs-lookup"><span data-stu-id="5b2d4-152">UMD - Universal Module Definition: AMD, RequireJS and globals;</span></span>
- <span data-ttu-id="5b2d4-153">Módulos de ESM-ES: para WebPack/Rollup o browser compatibles con las especificaciones;</span><span class="sxs-lookup"><span data-stu-id="5b2d4-153">ESM - ES Modules: For webpack/Rollup or browser supporting the spec;</span></span>
- <span data-ttu-id="5b2d4-154">ESNext: disponible en `dist/`, puede usarse con WebPack y `babel-preset-env`;</span><span class="sxs-lookup"><span data-stu-id="5b2d4-154">ESNext: Available in `dist/`, can be used with webpack and `babel-preset-env`;</span></span>

<span data-ttu-id="5b2d4-155">Asegúrese de usar el adecuado para sus necesidades.</span><span class="sxs-lookup"><span data-stu-id="5b2d4-155">Make sure to use the right one for your needs.</span></span> <span data-ttu-id="5b2d4-156">Si quiere importarla con una `<script>` etiqueta, use UMD.</span><span class="sxs-lookup"><span data-stu-id="5b2d4-156">If you want to import it with a `<script>` tag, use UMD.</span></span>

## <a name="usage"></a><span data-ttu-id="5b2d4-157">Uso</span><span class="sxs-lookup"><span data-stu-id="5b2d4-157">Usage</span></span>

<span data-ttu-id="5b2d4-158">Dado un nodo Popper DOM existente, pida a Popper. js que lo sitúe cerca de su botón.</span><span class="sxs-lookup"><span data-stu-id="5b2d4-158">Given an existing popper DOM node, ask Popper.js to position it near its button</span></span>

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

### <a name="callbacks"></a><span data-ttu-id="5b2d4-159">Devoluciones</span><span class="sxs-lookup"><span data-stu-id="5b2d4-159">Callbacks</span></span>

<span data-ttu-id="5b2d4-160">Popper. js admite dos tipos de devoluciones de `onCreate` llamada, se llama a la devolución de llamada después de que se haya inicializado Popper.</span><span class="sxs-lookup"><span data-stu-id="5b2d4-160">Popper.js supports two kinds of callbacks, the `onCreate` callback is called after the popper has been initialized.</span></span> <span data-ttu-id="5b2d4-161">Se `onUpdate` llama a la misma en cualquier actualización posterior.</span><span class="sxs-lookup"><span data-stu-id="5b2d4-161">The `onUpdate` one is called on any subsequent update.</span></span>

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

### <a name="writing-your-own-modifiers"></a><span data-ttu-id="5b2d4-162">Escribir sus propios modificadores</span><span class="sxs-lookup"><span data-stu-id="5b2d4-162">Writing your own modifiers</span></span>

<span data-ttu-id="5b2d4-163">Popper. js se basa en una arquitectura de tipo "complemento", la mayoría de sus características son "modificadores" totalmente encapsulados.</span><span class="sxs-lookup"><span data-stu-id="5b2d4-163">Popper.js is based on a "plugin-like" architecture, most of its features are fully encapsulated "modifiers".</span></span>  
<span data-ttu-id="5b2d4-164">Un modificador es una función a la que se llama cada vez que Popper. js necesita calcular la posición de la Popper.</span><span class="sxs-lookup"><span data-stu-id="5b2d4-164">A modifier is a function that is called each time Popper.js needs to compute the position of the popper.</span></span> <span data-ttu-id="5b2d4-165">Por este motivo, los modificadores deben ser muy eficaz para evitar los cuellos de botella.</span><span class="sxs-lookup"><span data-stu-id="5b2d4-165">For this reason, modifiers should be very performant to avoid bottlenecks.</span></span>  

<span data-ttu-id="5b2d4-166">Para obtener información sobre cómo crear un modificador, [Lea la documentación de los modificadores](docs/_includes/popper-documentation.md#modifiers--object)</span><span class="sxs-lookup"><span data-stu-id="5b2d4-166">To learn how to create a modifier, [read the modifiers documentation](docs/_includes/popper-documentation.md#modifiers--object)</span></span>


### <a name="react-vuejs-angular-angularjs-emberjs-etc-integration"></a><span data-ttu-id="5b2d4-167">La integración de reAct, Vue. js, angular, AngularJS, Ember. js (etc.)</span><span class="sxs-lookup"><span data-stu-id="5b2d4-167">React, Vue.js, Angular, AngularJS, Ember.js (etc...) integration</span></span>

<span data-ttu-id="5b2d4-168">La integración de bibliotecas de terceros en React o en otras bibliotecas puede ser un problema porque normalmente alteran el DOM y driven las bibliotecas loco.</span><span class="sxs-lookup"><span data-stu-id="5b2d4-168">Integrating 3rd party libraries in React or other libraries can be a pain because they usually alter the DOM and drive the libraries crazy.</span></span>  
<span data-ttu-id="5b2d4-169">Popper. js limita todas sus modificaciones de DOM dentro `applyStyle` del modificador, sólo tiene que deshabilitarla y aplicar manualmente las coordenadas Popper con la biblioteca que elija.</span><span class="sxs-lookup"><span data-stu-id="5b2d4-169">Popper.js limits all its DOM modifications inside the `applyStyle` modifier, you can simply disable it and manually apply the popper coordinates using your library of choice.</span></span>  

<span data-ttu-id="5b2d4-170">Para obtener una lista completa de las bibliotecas que le permiten usar Popper. js en los marcos existentes, visite la página [menciones](/MENTIONS.md) .</span><span class="sxs-lookup"><span data-stu-id="5b2d4-170">For a comprehensive list of libraries that let you use Popper.js into existing frameworks, visit the [MENTIONS](/MENTIONS.md) page.</span></span>

<span data-ttu-id="5b2d4-171">Como alternativa, puede incluso reemplazar su propio por `applyStyles` el suyo personalizado e integrar Popper. js por su cuenta.</span><span class="sxs-lookup"><span data-stu-id="5b2d4-171">Alternatively, you may even override your own `applyStyles` with your custom one and integrate Popper.js by yourself!</span></span>

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

### <a name="migration-from-popperjs-v0"></a><span data-ttu-id="5b2d4-172">Migración desde Popper. js v0</span><span class="sxs-lookup"><span data-stu-id="5b2d4-172">Migration from Popper.js v0</span></span>

<span data-ttu-id="5b2d4-173">Debido a que la API cambió, preparamos algunas instrucciones de migración para facilitar la actualización a Popper. js v1.</span><span class="sxs-lookup"><span data-stu-id="5b2d4-173">Since the API changed, we prepared some migration instructions to make it easy to upgrade to Popper.js v1.</span></span>  

https://github.com/FezVrasta/popper.js/issues/62

<span data-ttu-id="5b2d4-174">Si tiene alguna pregunta, no dude en comentar el problema.</span><span class="sxs-lookup"><span data-stu-id="5b2d4-174">Feel free to comment inside the issue if you have any questions.</span></span>

### <a name="performances"></a><span data-ttu-id="5b2d4-175">Excelentes</span><span class="sxs-lookup"><span data-stu-id="5b2d4-175">Performances</span></span>

<span data-ttu-id="5b2d4-176">Popper. js es muy eficaz.</span><span class="sxs-lookup"><span data-stu-id="5b2d4-176">Popper.js is very performant.</span></span> <span data-ttu-id="5b2d4-177">Por lo general, suele tomar 0,5 ms para calcular la posición de un Popper (en un iMac con 3,5 G de i5 de núcleo de Intel).</span><span class="sxs-lookup"><span data-stu-id="5b2d4-177">It usually takes 0.5ms to compute a popper's position (on an iMac with 3.5G GHz Intel Core i5).</span></span>  
<span data-ttu-id="5b2d4-178">Esto significa que no provocará ningún [Jank](https://www.chromium.org/developers/how-tos/trace-event-profiling-tool/anatomy-of-jank), lo que llevará a una experiencia de usuario sin problemas.</span><span class="sxs-lookup"><span data-stu-id="5b2d4-178">This means that it will not cause any [jank](https://www.chromium.org/developers/how-tos/trace-event-profiling-tool/anatomy-of-jank), leading to a smooth user experience.</span></span>

## <a name="notes"></a><span data-ttu-id="5b2d4-179">Notas</span><span class="sxs-lookup"><span data-stu-id="5b2d4-179">Notes</span></span>

### <a name="libraries-using-popperjs"></a><span data-ttu-id="5b2d4-180">Bibliotecas que usan Popper. js</span><span class="sxs-lookup"><span data-stu-id="5b2d4-180">Libraries using Popper.js</span></span>

<span data-ttu-id="5b2d4-181">El objetivo de Popper. js es proporcionar un motor de posicionamiento estable y eficaz listo para su uso en bibliotecas de terceros.</span><span class="sxs-lookup"><span data-stu-id="5b2d4-181">The aim of Popper.js is to provide a stable and powerful positioning engine ready to be used in 3rd party libraries.</span></span>  

<span data-ttu-id="5b2d4-182">Visite la página [menciones](/MENTIONS.md) para obtener una lista actualizada de proyectos.</span><span class="sxs-lookup"><span data-stu-id="5b2d4-182">Visit the [MENTIONS](/MENTIONS.md) page for an updated list of projects.</span></span>


### <a name="credits"></a><span data-ttu-id="5b2d4-183">Créditos</span><span class="sxs-lookup"><span data-stu-id="5b2d4-183">Credits</span></span>
<span data-ttu-id="5b2d4-184">Quiero agradecer a algunos amigos y proyectos por el trabajo que hicimos:</span><span class="sxs-lookup"><span data-stu-id="5b2d4-184">I want to thank some friends and projects for the work they did:</span></span>

- <span data-ttu-id="5b2d4-185">[@AndreaScn](https://github.com/AndreaScn) para su trabajo en la página de Github y las pruebas manuales que realizó durante el desarrollo;</span><span class="sxs-lookup"><span data-stu-id="5b2d4-185">[@AndreaScn](https://github.com/AndreaScn) for his work on the GitHub Page and the manual testing he did during the development;</span></span>
- <span data-ttu-id="5b2d4-186">[@vampolo](https://github.com/vampolo) de la idea original y del nombre de la biblioteca;</span><span class="sxs-lookup"><span data-stu-id="5b2d4-186">[@vampolo](https://github.com/vampolo) for the original idea and for the name of the library;</span></span>
- <span data-ttu-id="5b2d4-187">[Sysdig](https://github.com/Draios) para todas las cosas impresionantes que aprendí durante estos años que me hicieron posible escribir esta biblioteca;</span><span class="sxs-lookup"><span data-stu-id="5b2d4-187">[Sysdig](https://github.com/Draios) for all the awesome things I learned during these years that made it possible for me to write this library;</span></span>
- <span data-ttu-id="5b2d4-188">[Tethering. js](http://github.hubspot.com/tether/) por haber sido inspirado al escribir una biblioteca de posicionamiento lista para el mundo real;</span><span class="sxs-lookup"><span data-stu-id="5b2d4-188">[Tether.js](http://github.hubspot.com/tether/) for having inspired me in writing a positioning library ready for the real world;</span></span>
- <span data-ttu-id="5b2d4-189">[Los colaboradores](https://github.com/FezVrasta/popper.js/graphs/contributors) de sus solicitudes de extracción y informes de errores muy apreciados;</span><span class="sxs-lookup"><span data-stu-id="5b2d4-189">[The Contributors](https://github.com/FezVrasta/popper.js/graphs/contributors) for their much appreciated Pull Requests and bug reports;</span></span>
- <span data-ttu-id="5b2d4-190">**usted** para la estrella que va a dar a este proyecto y a ser tan maravilla para dar a este proyecto un intento🙂</span><span class="sxs-lookup"><span data-stu-id="5b2d4-190">**you** for the star you'll give to this project and for being so awesome to give this project a try 🙂</span></span>

### <a name="copyright-and-license"></a><span data-ttu-id="5b2d4-191">Copyright y licencia</span><span class="sxs-lookup"><span data-stu-id="5b2d4-191">Copyright and license</span></span>
<span data-ttu-id="5b2d4-192">Código y documentación Copyright 2016 **Federico Zivolo**.</span><span class="sxs-lookup"><span data-stu-id="5b2d4-192">Code and documentation copyright 2016 **Federico Zivolo**.</span></span> <span data-ttu-id="5b2d4-193">Código lanzado en la [licencia MIT](LICENSE.md).</span><span class="sxs-lookup"><span data-stu-id="5b2d4-193">Code released under the [MIT license](LICENSE.md).</span></span> <span data-ttu-id="5b2d4-194">Documentos publicados en Creative Commons.</span><span class="sxs-lookup"><span data-stu-id="5b2d4-194">Docs released under Creative Commons.</span></span>
