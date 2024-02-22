# Minecraft-auth
![npm bundle size](https://img.shields.io/bundlephobia/min/minecraft-auth?label=npm%20size)
![GitHub top language](https://img.shields.io/github/languages/top/dommilosz/minecraft-auth)

Minecraft-auth es un paquete para autenticar y obtener tokens de acceso a Minecraft.

Tipos de autenticación:
* Autenticación de Mojang: autenticación de Mojang estándar mediante nombre de usuario y contraseña
* Autenticación de Microsoft: nueva autenticación oauth de Microsoft para iniciar sesión en nuevas cuentas/migración a Microsoft.
* Autenticación descifrada: autenticación en modo fuera de línea no premium. Sólo requiere nombre de usuario.

API de Mojang:

El paquete contiene la clase MojangApi que se puede usar para buscar máscaras, uuids de otros usuarios, verificar el estado del servidor y más.

### Migración 2.0
La versión 2.0 cambia el funcionamiento de la autenticación de Microsoft.
* La aplicación de Azure debe registrarse con el tipo "Aplicaciones móviles y de escritorio".
* Los parámetros en las funciones Setup y listeningForCode cambiaron

### Manejo de errores:
Todos los errores de autenticación se generan mediante el uso de las clases AuthenticationError o OwnershipError y todas extienden la clase Error.
AuthenticationError también contiene `additionalInfo: string`

importar:
```javascript
import * as minecraftAuth from "./src/index";
//or
const minecraftAuth = require("./src/index.ts");
```

### Ejemplos de autenticación:

 * Autenticación de Microsoft [válido por 24 horas](https://learn.microsoft.com/en-us/azure/active-directory/develop/v2-oauth2-auth-code-flow#refresh-the-access-token)): 
```javascript
const MicrosoftAuth = minecraftAuth.MicrosoftAuth;

let account = new minecraftAuth.MicrosoftAccount();
MicrosoftAuth.setup({appID:"747bf062-ab9c-4690-842d-a77d18d4cf82"});
let code = await MicrosoftAuth.listenForCode();

if(code !== undefined){
    await account.authFlow(code);
}
 ```

* Autenticación de Microsoft ([no tiene una duración especificada](https://learn.microsoft.com/en-us/azure/active-directory/develop/v2-oauth2-auth-code-flow#refresh-the-access-token)):
 ```javascript
const MicrosoftAuth = minecraftAuth.MicrosoftAuth;

let account = new minecraftAuth.MicrosoftAccount();
MicrosoftAuth.setup({appID:"YOUR APP ID", appSecret:"YOUR APP SECRET"}); 
let code = await MicrosoftAuth.listenForCode();

if(code !== undefined){
    await account.authFlow(code);
}
 ```

* Autenticación Mojang (obsoleta debido a la migración):
```javascript
let account = new minecraftAuth.MojangAccount();
await account.Login("email","password");
```


* Cracked Autenticación:
```javascript
let account = new minecraftAuth.CrackedAccount("username");
```

### Ejemplo de uso
```javascript
//any type of authentication eg. from above examples
        
console.log(account.accessToken);
await account.getProfile();
console.log(account.username);            //Nombre de usuario de la cuenta
console.log(account.uuid);                //UUID de la cuenta (sin guiones)
console.log(account.ownership);           //Does account even have minecraft
console.log(account.profile)              //Perfil del usuario - skins, capas, uuid, username
console.log(account.profile.skins[0].url) //URL de la primera máscara.
```

### Cuentas Almacenamiento:
AccountsStorage es el almacenamiento para sus cuentas.
###### Agregar cuentas:
Puedes agregar una nueva cuenta con `AccountsStorage::addAccount(account)`
###### Eliminando cuentas:
Puedes eliminar cuenta con `AccountsStorage::removeAccount(account)`

###### Obteniendo Cuentas:
Puedes obtener cuentas con:
* `getAccount(index)`
* `getAccountByName(name)`
* `getAccountByUUID(uuid)`
###### Guardando/Leyendo Cuentas
* `serialize` convierte el almacenamiento a una cadena JSON para guardarlo en un archivo
* `deserialize` convierte una cadena en un objeto AccountStorage
