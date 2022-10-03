# Qué son los controladores

Los controladores en Nest, **son las clases que se encargan de gestionar las solicitudes entrantes y devolver las respuestas al cliente que lo solicita**.

![Controladores en Nest](https://profejulianlasso.github.io/curso-nestjs/assets/Controllers_1.png "Controladores en Nest")

Fuente: (<https://docs.nestjs.com/controllers>)

El propósito de un controlador es recibir solicitudes específicas para la aplicación. El mecanismo de enrutamiento controla ¿qué controlador? recibe ¿qué solicitudes?. Con frecuencia, cada controlador tiene más de una ruta, y diferentes rutas pueden realizar diferentes acciones.

Para crear un controlador básico, se usa una clase y el decorador `@Controller`. Los decoradores asocian las clases con los metadatos necesarios y permiten a Nest crear un mapa de enrutamiento, es decir, enrutar las solicitudes a los controladores correspondientes.

Para crear un controlador por medio del CLI, simplemente se debe de ejecutar el siguiente comando ubicado en la raíz de la carpeta src del proyecto o dónde se desee ubicar dentro del sistema que se está desarrollando.

```bash
nest generate controller [NOMBRE DEL CONTROLADOR]
```

La siguiente imagen muestra el comando necesario para generar un controlador por medio del CLI de Nest.

![Generando un controlador por CLI](https://profejulianlasso.github.io/curso-nestjs/assets/generate-controller.png "Generando un controlador por CLI")

Nest y su poderoso CLI, posee la forma de generar un CRUD con el comando que se muestra en la siguiente imagen.

```bash
nest generate resource usuario
```

![Generando un CRUD por CLI](https://profejulianlasso.github.io/curso-nestjs/assets/resource-usuario.png "Generando un CRUD por CLI")

## El sistema de rutas y métodos HTTP

Cuando se acostumbra a trabajar con **Express** o **Fastify**, notará que en dichos frameworks es normal crear un mapa de rutas donde se relacionan las funciones o métodos de una clase que responden a dichas rutas, es decir, los controladores. Pero Nest ofrece un sistema muy parecido a **Spring Boot** en Java, para quienes hayan trabajado con dicha tecnología, y es que las rutas se definen tanto en las clases que actúan como controladores y también en los métodos a usar. En el siguiente código de ejemplo, el controlador `UserController` responderá a la ruta `/user`.

```typescript
import { Controller, Get, Req } from '@nestjs/common';
import { Request } from 'express';

@Controller('user')
export class UserController {
    @Get()
    findAll(@Req() request: Request): string {
        return 'This action return all users';
    }
}
```

Como se observa en el código anterior, en la línea 4 se declara la dirección a la cual dicho controlador estará atendiendo la solicitud del cliente, que para este caso será `http://localhost:3000/user` bajo el método **GET**; nótese que en la línea 6 se usa el decorador `@Get`, el cual, le indica al método que responderá a la dirección solicitada por el cliente bajo el método GET.

Sí la dirección solicitada por el cliente fuese la siguiente: `http://localhost:3000/user/get-all`, entonces el código del controlador se vería como el siguiente código.

```typescript
import { Controller, Get, Req } from '@nestjs/common';
import { Request } from 'express';

@Controller('user')
export class UserController {
    @Get('get-all')
    findAll(@Req() request: Request): string {
        return 'This action return all users';
    }
}
```

Es importante tener presente que Nest maneja los siguientes decoradores para los métodos HTTP.

| | |
|---|---|
| `Get()` | El método **GET** solicita un recurso en específico, es decir, las peticiones GET sólo deben recuperar datos. |
| `@Post()` | El método **POST** se usa para enviar datos al servidor y que estos tengan un efecto secundario en la aplicación, ejemplo: registrar un usuario. |
| `@Put()` | El método **PUT** se usa para actualizar un recurso completo en el servidor, por ejemplo: actualizar todos los campos del usuario registrado. |
| `@Patch()` | El método **PATCH** se usa para actualizar una parte del recurso que se quiere afectar en el servidor, por ejemplo: la actualización sólo del nombre del usuario registrado. |
| `@Delete()` | El método **DELETE** borra un recurso en específico del servidor. |
| `@Options()` | El método **OPTIONS** sirve para averiguar qué opciones de comunicación permite el recurso de destino, es decir, qué métodos soporta la URL consultada. |
| `@Head()` | El método **HEAD**, tiene el mismo comportamiento que el método GET, con la diferencia que no existe una respuesta en el HTTP response, normalmente este método es usado para comprobar sí un enlace se encuentra roto o no ya que es más rápido usar este método que el GET para su comprobación. |
| `@All()` | Este último decorador, **no es un método HTTP como tal**, sólo es un decorador que le indica al método que podrá responder ante cualquier método HTTP por donde se pregunte, es decir, responde a los métodos GET, POST, PUT, DELETE, PATCH, HEAD y OPTIONS. |

Es importante tener presente la forma de responder del controlador, ya que existen dos formas de hacer lo "mismo" pero cada una de ellas **tiene pros y contras**. La forma estándar que recomienda Nest es la de, literalmente, hacer uso de la palabra reservada return y por ende **especificar el tipo de dato que devuelve el método** o hacer uso de la forma en que la librería que se usa de fondo, ejemplo Express, utiliza habitualmente para dar respuesta al cliente.

Para el siguiente ejemplo se responderá con un **string** usando las dos formas válidas en Nest, la primera de la forma estándar y la segunda, haciendo uso de la forma específica que usaría la librería de forma, para este ejemplo sería con Express aunque también funciona para Fastify.

Modo recomendado por Nest.

```typescript
import { Controller, Get, Req } from '@nestjs/common';
import { Request } from 'express';

@Controller('user')
export class UserController {
    @Get('get-all')
    findAll(@Req() request: Request): string {
        return 'This action return all users';
    }
}
```

Modo recomendado específico de la librería que se usa de fondo, ejemplo Express.

```typescript
import { Controller, Get, Res, Req } from '@nestjs/common';
import { Response, Request } from 'express';

@Controller('user')
export class UserController {
    @Get('get-all')
    findAll(@Res() response: Response, @Req() request: Request): void {
        response.send('This action return all users');
    }
}
```

Como se puede observar en los códigos anteriores, el primero especifica en la línea 7 el tipo de dato que se devolverá, `string` para este ejemplo, en contraste con la segunda imagen, en la misma línea 7, especifica `void` ya que no se devolverá nada como tal, pero en la primera imagen, línea 8, se observa un `return` directo de la respuesta requerida, pero en la segunda imagen, línea 8, se hace uso del objeto `Response`, propiedad de Express para este ejemplo.

| Respuesta estándar recomendada por Nest | Respuesta con objeto de librería de fondo |
|---|---|
| Utilizando este método incorporado, cuando un gestor de solicitudes devuelve un objeto o matriz de JavaScript, se serializará automáticamente a JSON. Sin embargo, cuando devuelve un tipo primitivo de JavaScript, por ejemplo una cadena, un número o un booleano, Nest enviará el valor sin intentar serializarlo. Esto hace que la gestión de la respuesta sea sencilla: sólo hay que devolver el valor y Nest se encarga del resto. Además, el código de estado de la respuesta es siempre 200 por defecto, excepto para las peticiones POST que utilizan 201. Se puede cambiar fácilmente este comportamiento añadiendo el decorador `@HttpCode` a nivel del método que se usa para responder la petición. | Se puede utilizar el objeto de respuesta específico de la biblioteca, Express para este ejemplo, que puede inyectarse utilizando el decorador @Res en la solicitud de atributos del método. Con este enfoque, tienes la posibilidad de utilizar los métodos nativos de manejo de respuesta expuestos por ese objeto. Por ejemplo, con Express, puedes construir respuestas utilizando código como `response.status(200).send()` |