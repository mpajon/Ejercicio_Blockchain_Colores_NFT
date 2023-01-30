# NFT sobre colores en hexadecimal

Aplicación para generar NFT sobre el código hexadecimal de los colores, usando el protocolo ERC-712 sobre Etherum

![](./screenshots/Prev.png)

## Stack tecnológico

- [Visual Studio Code](https://code.visualstudio.com/), como IDE para desarrollar la solución.
- [Extensión Visual Studio Code Solidity](https://marketplace.visualstudio.com/items?itemName=JuanBlanco.solidity), como extensión para desarrollar sobre el IDE anterior código en [Solidity](https://solidity-es.readthedocs.io/es/latest/).
- [Truffle Framework](https://trufflesuite.com/), como framework de desarrollo para _smart contracts_ (contratos inteligentes) .
- [Ganache](https://trufflesuite.com/ganache/), como red de pruebas Ethereum en local. 
- [Metamask](https://metamask.io/), como extensión del navegador que permite conexión directa con aplicaciones descentralizadas de la red Ethereum.
- [Angular](https://angular.io/), como entorno de servidor para desplegar el cliente que interactuará con la solución descentralizada.
- [ERC-721](https://eips.ethereum.org/EIPS/eip-721), Estándar de tokens no fungibles.

## Enunciado

Vamos a crear una aplicación para generar y registrar tokens NFT generados sobre el código hexadecimal de los colores. Para ello, vamos a apoyarnos en un lenguaje de programación llamado [Solidity](https://solidity-es.readthedocs.io/es/latest/) para desarrollar un contrato inteligente que nos permita registrar colores no existentes usando la blockchain de Ethereum.

Desde una ventana de comando, vamos a instalar [Truffle](https://trufflesuite.com/):

    $ npm install -g truffle

Una vez instalado, vamos a crear la carpeta que contendrá el contrato inteligente de la aplicación e iniciamos [Truffle](https://trufflesuite.com/):

    $ truffle init truffle

Nos situamos en la carpeta que se acaba de crear

    $ cd truffle

Y generamos nuestro primer contrato inteligente:

    $ truffle create contract Color

Esto nos creará un fichero _Color.sol_ que contendrá la lógica de nuestro contrato.

```js
// SPDX-License-Identifier: MIT
pragma solidity 0.8.17;

import "@openzeppelin/contracts/token/ERC721/extensions/ERC721Enumerable.sol";
import "@openzeppelin/contracts/token/ERC721/ERC721.sol";


contract Color is ERC721Enumerable  {
    string[] public mintedColors;
    mapping(string => bool) _colorExists;

    constructor() ERC721 ("Unique Colors", "UCO") {}

    function mint(string memory _color) public {
        require(!_colorExists[_color], 'Color already exists!');

        mintedColors.push(_color);
        uint _id = mintedColors.length - 1;
        _mint(msg.sender, _id);
        _colorExists[_color] = true;
    }

    function getMintedColors() public view returns (string[] memory) {
        return mintedColors;
    }
}
```

Una vez creado, nos situamos en la carpeta _migrations_ y crearemos un fichero con el nombre _`1_deploy_Color.js`_ con el siguiente contenido:

```js
const ColorToken = artifacts.require('Color');

module.exports = function(deployer) {
    deployer.deploy(ColorToken);
};
```

Como este contrato hace uso del estándar [ERC-721](https://eips.ethereum.org/EIPS/eip-721) necesitamos importarlo. Para ello, vamos a crear en la carpeta _truffle_ un fichero _package.json_
con el siguiente contenido:
```js
{
  "name": "truffle",
  "version": "1.0.0",
  "description": "",
  "main": "truffle-config.js",
  "directories": {
    "test": "test"
  },
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "",
  "license": "ISC",
  "dependencies": {
    "@openzeppelin/contracts": "^4.1.0"
  }
}
```
Una vez creado, instalamos los paquetes necesarios ejecutando desde la carpeta _truffle_ el siguiente comando:

    $ npm install

Para desplegar nuestro contrato en nuestra red local de pruebas de Ethereum vamos a usar [Ganache](https://trufflesuite.com/ganache/). Lo arrancamos y creamos una red:


![](./screenshots/ganache01.png)

Elegimos la opción _Quickstart Etherum_:

![](./screenshots/ganache02.png)

Con esto tenemos la red creada, con 10 cuentas que tienen un saldo de 100.00 ETH. Tenemos que fijarnos en el puerto donde se ha levantado al red:

![](./screenshots/ganache03.png)

En este caso, es el puerto **_7545_**. Este valor lo usaremos mas adelante (para configurar [Truffle](https://trufflesuite.com/) y [Metamask](https://metamask.io/))

Con la red creada, vamos a configurar [Truffle](https://trufflesuite.com/) para que use esta red. Para ello, en el fichero _truffle-config.js_, descomentamos las propiedades del apartado _network_ para que el puerto coincida con el que nos da [Ganache](https://trufflesuite.com/ganache/):

```js
development: {
     host: "127.0.0.1",     // Localhost (default: none)
     port: 7545,            // Standard Ethereum port (default: none)
     network_id: "*",       // Any network (default: none)
    },
};
```
Ya con todo listo, vamos a compilar, empaquetar y desplegar nuestro contrato inteligente:

    $ truffle deploy

Si todo ha ido bien, veremos el resultado de la operación (y su coste):

![](./screenshots/truffle_deploy.png)

Con el contrato inteligente en la red, vamos a configurar [Metamask](https://metamask.io/) para poder interactuar desde nuestro navegador con la red. Para ello, vamos a la URL y pulsamos _Download for_ 

![](./screenshots/metamask01.png)

Añadimos la extensión:

![](./screenshots/metamask02.png)

![](./screenshots/metamask03.png)

Al finalizar la instalación, se abre una nueva pestaña solicitandonos información. Seleccionamos _Crear una cartera nueva_:

![](./screenshots/metamask04.png)

Ayudamos a mejorar Metamask enviando de forma anónima los datos de uso:

![](./screenshots/metamask05.png)

Creamos una contraseña y aceptamos los terminos:

![](./screenshots/metamask06.png)

No es necesario proteger nuestra cuenta, seleccionamos _Recordarme más tarde (no recomendado)_:

![](./screenshots/metamask07.png)

![](./screenshots/metamask08.png)

Si todo ha ido bien, hemos creado la cuenta en [Metamask](https://metamask.io/):

![](./screenshots/metamask09.png)

Para facilitar su uso, anclamos la extensión al navegador:

![](./screenshots/metamask10.png)

[Metamask](https://metamask.io/) viene preparado para interactuar con la red principal de Etherum. Vamos a cambiarlo para que use la red que tenemos en local mediante [Ganache](https://trufflesuite.com/ganache/). Para ello, pulsamos _Agregar red_:

![](./screenshots/metamask11.png)

En el menú de configuración, seleccionamos _Redes_:

![](./screenshots/metamask12.png)

Y de ellas, seleccionamos la red _Localhost 8545_ y cambiamos el puerto al que nos da [Ganache](https://trufflesuite.com/ganache/) y que hemos obtenido antes (**_7545_**)

![](./screenshots/metamask13.png)

Una vez configurado [Metamask](https://metamask.io/) para que use la red local de [Ganache](https://trufflesuite.com/ganache/), nos queda importar una de las cuentas. Para ello, vamos a la opción _Mis Cuentas_ y seleccionamos la opción _Importar cuenta_:

![](./screenshots/metamask14.png)

Necesitamos una clave privada:

![](./screenshots/metamask15.png)

Para ello, vamos a [Ganache](https://trufflesuite.com/ganache/) y en el icono de la llave de cualquiera de los usuarios:

![](./screenshots/metamask16.png)

Obtenemos su clave privada

![](./screenshots/metamask17.png)

La copiamos y la ponemos en [Metamask](https://metamask.io/):

![](./screenshots/metamask18.png)

Ya tenemos una cuenta de nuestra red local de Etherum con saldo lista para usar:

![](./screenshots/metamask19.png)

Con [Metamask](https://metamask.io/) instalado y configurado, vamos a usar una aplicación desarrollada en Angular  nos sirva de interfaz para usar el contrato inteligente que tenemos en nuestra red Etherum de pruebas situada en local.

Descargaremos los paquetes necesarios para que funcione la aplicación:

    $ npm install

Una vez que este todo descargado, lanzaremos la aplicación:

    $ npm run start

![](./screenshots/npm_run_dev)

Y accediendo a la URL [http://localhost:400](http://localhost:4000) podremos ver la aplicación y comprobar si nuestro contrato inteligente funciona correctamente.