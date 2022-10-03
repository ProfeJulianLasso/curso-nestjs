# Qué son los controladores

Los controladores en Nest, **son las clases que se encargan de gestionar las solicitudes entrantes y devolver las respuestas al cliente que lo solicita**.

![Controladores en Nest](https://profejulianlasso.github.io/curso-nestjs/assets/Controllers_1 "Controladores en Nest")

Fuente: (<https://docs.nestjs.com/controllers>)

El propósito de un controlador es recibir solicitudes específicas para la aplicación. El mecanismo de enrutamiento controla ¿qué controlador? recibe ¿qué solicitudes?. Con frecuencia, cada controlador tiene más de una ruta, y diferentes rutas pueden realizar diferentes acciones.

Para crear un controlador básico, se usa una clase y el decorador `@Controller`. Los decoradores asocian las clases con los metadatos necesarios y permiten a Nest crear un mapa de enrutamiento, es decir, enrutar las solicitudes a los controladores correspondientes.

Para crear un controlador por medio del CLI, simplemente se debe de ejecutar el siguiente comando ubicado en la raíz de la carpeta src del proyecto o dónde se desee ubicar dentro del sistema que se está desarrollando.

```bash
nest generate controller my-controller
```

La siguiente imagen muestra el comando necesario para generar un controlador por medio del CLI de Nest.

