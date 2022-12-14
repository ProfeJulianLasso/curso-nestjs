# Qué son los módulos

![Diagrama de módulos](https://profejulianlasso.github.io/curso-nestjs/assets/Modules_1.png "Diagrama de módulos")

Fuente: (<https://docs.nestjs.com/modules>)

Los módulos **son clases apoyadas en el decorador `@Module`**, el cual, proporciona metadatos a Nest, los cuales, son utilizados para organizar la estructura de la aplicación.

**Cada aplicación está compuesta por un módulo**, el módulo principal. Dicho módulo es el punto de partida que Nest utiliza para construir los cimientos del aplicativo, es decir, la estructura interna de los datos que Nest usa para resolver las relaciones y dependencias entre módulos y proveedores.

Normalmente las aplicaciones pequeñas suelen tener sólo el módulo principal, pero esto no es un caso común. **Los módulos son una forma eficaz de organizar los componentes del aplicativo desarrollado**. Por lo tanto, para la mayoría de las aplicaciones, la arquitectura resultante emplea múltiples módulos, cada uno encapsulando un conjunto de capacidades estrechamente relacionadas.

Para crear un módulo por medio del CLI de Nest, es necesario abrir la consola y digitar el siguiente comando `nest generate module [NOMBRE DEL MODULO]`, situado en la raíz, no del proyecto, sino, del código fuente del aplicativo.

![Generación de un módulo en consola](https://profejulianlasso.github.io/curso-nestjs/assets/generar-modulo-cli.png "Generación de un módulo en consola")

El decorador `@Module` toma un único objeto, el cual, sus propiedades describen el módulo.

```typescript
import { Module } from '@nestjs/common';

@Module({
    providers: [],
    controllers: [],
    imports: [],
    exports: [],
})
export class AppModule {}
```

**`providers`**, aquí se referencian los proveedores que serán instanciados por el inyector de dependencias de Nest, y que pueden ser compartidos por lo menos en este módulo.

**`controllers`**, se establece el conjunto de controladores que deben ser instanciados para el módulo en cuestión.

**`imports`**, se establece la lista de módulos requeridos a usar en el módulo que se está definiendo.

**`exports`**, es el listado de módulos, controladores y/o proveedores que el actual módulo puede llegar a proveer al módulo que instancia el módulo que se está definiendo, es decir, el módulo actual.

En Nest, **cuando se crea un módulo, este encapsula los proveedores de manera predeterminada**, lo anterior significa que es imposible inyectar un proveedor que no sea parte directamente del módulo actual, ni son exportados desde los módulos importados. Por lo tanto, se puede considerar los proveedores exportados de un módulo como la interfaz pública del módulo o API.

## Características de los módulos

Dando por cierto que, `UserController` y `UserService` pertenecen al mismo dominio de aplicación, tiene sentido moverlos a un módulo llamado `UserModule`. Lo anterior ayuda a gestionar la complejidad de la aplicación y a desarrollarla con los principios SOLID.

`user/user.module.ts`
```typescript
import { Module } from '@nestjs/common';
import { UserController } from './controllers/user.controller';
import { UserService } from './services/user.service';

@Module({
    controllers: [UserController],
    providers: [UserService],
})
export class UserModule {}
```

Dado lo anterior la forma de importar el `UserModule` en el módulo principal, sería la siguiente:

`app.module.ts`
```typescript
import { Module } from '@nestjs/common';
import { UserModule } from './user/user.module';

@Module({
    imports: [UserModule],
})
export class AppModule {}
```

Y así es como se podría ver la estructura de carpetas para con dicho módulo.

![Estructura de carpetas](https://profejulianlasso.github.io/curso-nestjs/assets/estructura_1.png "Estructura de carpetas")

## Módulos compartidos

En Nest, **los módulos utilizan el patrón singleton por defecto**, y por ende, se puede compartir la misma instancia de cualquier proveedor entre múltiples módulos.

![Módulos compartidos](https://profejulianlasso.github.io/curso-nestjs/assets/Shared_Module_1.png "Módulos compartidos")

Fuente: (<https://docs.nestjs.com/modules#shared-modules>)

Cada módulo en Nest cuando se crea, automáticamente es un módulo compartido, es decir, puede ser reutilizado en cualquier otro módulo. Imagine que desea compartir el servicio `UserService` que se dispone en el módulo `UserModule`, entonces, la forma correcta es importar dicho servicio en el área de providers y luego exportarlo en el área de exports dentro del módulo `UserModule`.

```typescript
import { Module } from '@nestjs/common';
import { UserController } from './controllers/user.controller';
import { UserService } from './services/user.service';

@Module({
    controllers: [UserController],
    providers: [UserService],
    exports: [UserService],
})
export class UserModule {}
```

Dado lo anterior, cualquier módulo que importe el módulo `UserModule`, podrá usar el servicio `UserService` y compartirá la misma instancia con todos los módulos que importen `UserModule`.

## Exportación de los módulos

**Así como un módulo puede exportar sus proveedores internos, también se pueden exportar los módulos que se importan.** En el siguiente ejemplo, se muestra cómo el módulo `CommonModule` es importado y exportado desde el `CoreModule`, de tal forma que, otros módulos puedan usar el módulo `CommonModule` con tan sólo importar el módulo `CoreModule`.

```typescript
import { Module } from '@nestjs/common';
import { CommonModule } from './common/common.module';

@Module({
    imports: [CommonModule]
    exports: [CommonModule],
})
export class CoreModule {}
```

## Módulos globales

A veces suele pasar que existe algún módulo o grupo de módulos que siempre estamos importando por todas partes y esto puede convertirse en algo tedioso así sea que agrupamos todo en un sólo módulo y este sea llamado por todas partes. A lo anterior le sumamos el tema de que Nest encapsula proveedores, módulos y demás dentro del ámbito del módulo, es decir, **no se puede usar los proveedores, módulos y demás en otro lugar sin antes importar el módulo que los encapsule.**

Cuando se quiera proporcionar un conjunto de proveedores, módulos y demás, como elementos disponibles de forma global en toda la aplicación, como por ejemplo, helpers, configuraciones de conexión a bases de datos, entre otros, entonces **se puede hacer que el módulo sea global por medio del decorador `@Global`.**

```typescript
import { Module, Global } from '@nestjs/common';
import { UserController } from './controllers/user.controller';
import { UserService } from './services/user.service';

@Global()
@Module({
    controllers: [UserController],
    providers: [UserService],
    exports: [UserService],
})
export class UserModule {}
```

El decorador `@Global` hace que el módulo señalado tenga un alcance global. Se debe saber que **los módulos globales deben ser importados sólo una vez**, por lo general son importados por el módulo principal de la aplicación desarrollada.

En el ejemplo del código anterior, el servicio `UserService` será global y los módulos que deseen inyectar el servicio no necesitarán importar el módulo `UserModule` en su área de imports.

**NOTA IMPORTANTE**, hacer que todo sea global no es una buena idea desde el punto de vista del diseño de arquitectura. Los módulos globales existen en Nest para reducir la cantidad de importaciones repetidas que se puedan generar en todo el sistema desarrollado. La mejor práctica es literalmente hacer uso del área de `imports` en los módulos que requiera la importación.
