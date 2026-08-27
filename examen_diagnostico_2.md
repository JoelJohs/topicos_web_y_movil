# Examen Diagnostico 2
## Hernández Saucedo Joel Josafat 20121000

1. Metodologías: ¿Cuál es la diferencia principal entre una metodología de desarrollo tradicional (como Cascada) y una metodología ágil (como Scrum) frente a los cambios en los requisitos?
	- Cascada sigue un patron de desarrollo y estandares desde un inicio, mientras que las ágil suelen adaptarse a cambios

2. Requerimientos: Explica la diferencia entre requerimientos funcionales y no funcionales, dando un ejemplo de cada uno aplicable a una plataforma web.
	- Funcionales: acciones que debe realizar el sistema. Ej. Inicio de Sesión
	- No funcionales: Calidad o rendimiento del sistema. Ej. El tiempo de respuesta en una petición

3. Arquitectura: Describe el modelo Cliente-Servidor y explica  brevemente cómo se comunican el frontend y el backend en una aplicación  web moderna. 
	- Es la division de entre las acciones realizadas por el usuario en el frontend y las acciones del servidor mediante el backend

4. Bases de Datos: ¿En qué escenarios recomendarías utilizar una base de datos relacional (SQL) frente a una no relacional (NoSQL) para el almacenamiento de datos en una aplicación?
	- Cuando la estructura esta definida es mas recomendable una SQL y si son mas flexibles una NoSQL

5. APIs: ¿Qué es una API REST y qué papel fundamental juega en la integración entre una aplicación móvil y los servidores (backend)?
	- Son la forma en que una aplicacion se comunica mediante HTTP, usando operaciones GET, POST, PUT o PATCH y DELETE, el el puente entre Back y Front

6. Control de Versiones: Explica la importancia de utilizar Git en un equipo de desarrollo de software y describe brevemente qué es un "merge conflict" (conflicto de fusión).
	- Usar Git en un desarrollo de software es escencial para evitar conflicto entre versiones de los diferentes colaboradores de un repositorio, tambien para que todos trabajen con una misma version base de este pero desde sus propias ramas.
	- Un Merge conflict es cuando la rama que se va a mergear a, por ejemplo, la rama main presenta cambios significativos o no compatibles en partes del codigo que un merge anterior y git no puede fucionarlos, por lo que se debe resolver manualmente

7. Pruebas: ¿Qué son las pruebas unitarias (unit testing) y por qué son cruciales para asegurar la calidad del software antes de su paso a producción? 
	- Son pruebas a segmentos del codigo o funciones individuales, permiten detectar errores tempranos

8. POO: Define los conceptos de encapsulamiento y polimorfismo de la Programación Orientada a Objetos, y menciona cómo ayudan a crear un código más mantenible. 
	- Encapsulamiento: agrupar datos y comportamientos en un objeto
	- Polimorfismo: permite que los objetos puedan responder distinta a una misma funcion
	
9. Patrones de Diseño: ¿Qué es el patrón de arquitectura Modelo-Vista-Controlador (MVC) y cómo ayuda a organizar el código en el desarrollo de software?
	- Es un patron que divide la apicacion en:
		* Modelo: Administra datos y logica
		* Vista: Presenta la información al usuario
		* Controlador: Coordina al Modelo y la vista gestionando la información

10. Seguridad: Explica la diferencia técnica entre "autenticación" y "autorización" en el contexto de seguridad de una aplicación.
	- Autenticación: Confirma la identidad del usuario
	- Autorización: Determina los permisos del usuario
