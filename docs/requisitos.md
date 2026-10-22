# Requisitos

**Proyecto:** Ridgeline
**Versión:** 0.1 (borrador)
**Última actualización:** 2026-10-22
**Autor:** Adrián Martínez Granados

> Documento vivo. Recoge la visión del proyecto, el vocabulario del dominio, los actores, los requisitos y las historias de usuario.

## Índice

1. [Visión y alcance](#1-visión-y-alcance)
2. [Glosario](#2-glosario)
3. [Actores](#3-actores)
4. [Requisitos funcionales](#4-requisitos-funcionales)
5. [Requisitos no funcionales](#5-requisitos-no-funcionales)
6. [Historias de usuario](#6-historias-de-usuario)

---

# 1. Visión y alcance

## 1.1 Contexto

Cualquier aplicación de grabación de rutas permite exportar un recorrido en formato GPX: un XML con una secuencia de puntos, cada uno con latitud, longitud, altitud y marca de tiempo.

Esas plataformas calculan métricas sobre la ruta, pero el método que emplean no es público y los resultados difieren de forma apreciable entre ellas: la misma ruta puede arrojar 870 m de desnivel acumulado en una y más de 1.000 m en otra. Las diferencias proceden del tratamiento del ruido de la señal, del origen del dato de altitud —barométrico o satelital— y del umbral a partir del cual se contabiliza una subida.

Ridgeline aborda el problema desde el lado contrario: un analizador cuyo método de cálculo es explícito, está documentado y se valida contra rutas de referencia.

## 1.2 Visión

> Ridgeline convierte un archivo GPX en información fiable y comprensible sobre una ruta, explicando cómo se ha obtenido cada cifra.

La evolución prevista a largo plazo pasa por profundizar en el análisis del terreno (corrección de altitud mediante modelos digitales del terreno, exposición solar, meteorología sincronizada con el avance) y no por competir en funcionalidad social con las plataformas existentes.

## 1.3 Objetivos

**De producto.** Entregar un análisis correcto y reproducible de una ruta, presentarlo de forma visual, conservar el histórico de cada usuario y permitir su exportación.

**De aprendizaje.** Ridgeline es además un proyecto de portfolio, y sus objetivos formativos condicionan decisiones de diseño que de otro modo serían más simples:

- Diseñar y operar una arquitectura dirigida por eventos sobre servicios gestionados.
- Definir la infraestructura como código.
- Sostener una cadena de calidad automatizada: pruebas, análisis estático y despliegue continuo.
- Documentar y justificar cada decisión de arquitectura.

Esta doble naturaleza explica, por ejemplo, que el procesamiento sea asíncrono pese a que un análisis síncrono bastaría para el volumen previsto.

## 1.4 Alcance de la primera versión

**Incluye** la gestión de cuentas de usuario, la carga y el análisis de archivos GPX, la presentación visual de los resultados, el histórico de rutas de cada usuario y la exportación del análisis. Las secciones 4 y 6 lo detallan.

**Queda explícitamente fuera:**

- Funcionalidad social de cualquier tipo: rutas públicas, compartir, comentarios, seguidores o valoraciones.
- Aplicación móvil, grabación de rutas y navegación sobre el terreno.
- Funcionamiento sin conexión.
- Edición o corrección manual del trazado.
- Comparación entre rutas y detección de tramos comunes.
- Búsqueda geográfica de rutas cercanas.
- Importación directa desde las API de otras plataformas.
- Formatos distintos de GPX (TCX, FIT, KML).
- Internacionalización: la interfaz se entrega únicamente en español.

Las funcionalidades avanzadas de análisis del terreno mencionadas en la visión se consideran evolución posterior y no forman parte de esta versión

# 4. Requisitos funcionales

| RF-01 | Registro de usuario |
|-------------------|-----------------------------------------------|
| **Versión:** | 1.0 |
| **Descripción:** | El sistema debe permitir que una persona cree una cuenta aportando un correo electrónico y una contraseña, rechazando los correos ya registrados. |
| **Importancia:** | Vital |
| **Urgencia:** | Inmediata |
 
| RF-02 | Inicio de sesión |
|-------------------|-----------------------------------------------|
| **Versión:** | 1.0 |
| **Descripción:** | El sistema debe autenticar al usuario mediante sus credenciales y emitir una credencial de sesión con la que acceder al resto de funcionalidades. |
| **Importancia:** | Vital |
| **Urgencia:** | Inmediata |
 
| RF-03 | Cierre de sesión |
|-------------------|-----------------------------------------------|
| **Versión:** | 1.0 |
| **Descripción:** | El sistema debe permitir al usuario cerrar su sesión e invalidar la credencial activa. |
| **Importancia:** | Importante |
| **Urgencia:** | Hay presión |
 
| RF-04 | Aislamiento de datos entre usuarios |
|-------------------|-----------------------------------------------|
| **Versión:** | 1.0 |
| **Descripción:** | El sistema debe garantizar que cada usuario accede exclusivamente a sus propias rutas y análisis, denegando cualquier intento de acceso a recursos ajenos. |
| **Importancia:** | Vital |
| **Urgencia:** | Inmediata |
  
| RF-05 | Solicitud de carga de un archivo |
|-------------------|-----------------------------------------------|
| **Versión:** | 1.0 |
| **Descripción:** | El sistema debe proporcionar al usuario autenticado un medio para subir un archivo GPX al almacenamiento sin que el archivo transite por el servidor de la API. |
| **Importancia:** | Vital |
| **Urgencia:** | Inmediata |
 
| RF-06 | Validación del archivo recibido |
|-------------------|-----------------------------------------------|
| **Versión:** | 1.0 |
| **Descripción:** | El sistema debe rechazar los archivos que no sean GPX válidos, que superen el tamaño máximo admitido, que excedan el número máximo de puntos o que no contengan ninguna traza, informando al usuario del motivo. |
| **Importancia:** | Vital |
| **Urgencia:** | Inmediata |
 
| RF-07 | Registro de la ruta y encolado del análisis |
|-------------------|-----------------------------------------------|
| **Versión:** | 1.0 |
| **Descripción:** | Al completarse la carga, el sistema debe registrar la ruta asociada al usuario en estado pendiente y encolar un trabajo de análisis. |
| **Importancia:** | Vital |
| **Urgencia:** | Inmediata |
 
| RF-08 | Consulta del estado del análisis |
|-------------------|-----------------------------------------------|
| **Versión:** | 1.0 |
| **Descripción:** | El sistema debe permitir al usuario consultar en qué estado se encuentra el análisis de una ruta: pendiente, en proceso, completado o fallido. |
| **Importancia:** | Vital |
| **Urgencia:** | Inmediata |
 
| RF-09 | Asignación de nombre a la ruta |
|-------------------|-----------------------------------------------|
| **Versión:** | 1.0 |
| **Descripción:** | El sistema debe tomar el nombre contenido en el archivo GPX como nombre de la ruta y, en su defecto, generar uno a partir de la fecha de la actividad. El usuario debe poder modificarlo posteriormente. |
| **Importancia:** | Deseable |
| **Urgencia:** | Puede esperar |
  
| RF-10 | Lectura de la traza GPX |
|-------------------|-----------------------------------------------|
| **Versión:** | 1.0 |
| **Descripción:** | El sistema debe extraer del archivo la secuencia de puntos con su latitud, longitud, altitud y marca de tiempo, procesando el documento en flujo para no cargarlo íntegramente en memoria. |
| **Importancia:** | Vital |
| **Urgencia:** | Inmediata |
 
| RF-11 | Cálculo de la distancia recorrida |
|-------------------|-----------------------------------------------|
| **Versión:** | 1.0 |
| **Descripción:** | El sistema debe calcular la distancia total de la ruta sumando la distancia entre puntos consecutivos sobre la superficie terrestre. |
| **Importancia:** | Vital |
| **Urgencia:** | Inmediata |
 
| RF-12 | Cálculo del desnivel acumulado |
|-------------------|-----------------------------------------------|
| **Versión:** | 1.0 |
| **Descripción:** | El sistema debe calcular el desnivel positivo y negativo acumulado aplicando un filtrado del ruido de la señal de altitud, mediante un método documentado y con sus parámetros configurables. |
| **Importancia:** | Vital |
| **Urgencia:** | Inmediata |
 
| RF-13 | Cálculo de las altitudes extremas |
|-------------------|-----------------------------------------------|
| **Versión:** | 1.0 |
| **Descripción:** | El sistema debe determinar la altitud máxima y mínima alcanzadas en la ruta. |
| **Importancia:** | Importante |
| **Urgencia:** | Inmediata |
 
| RF-14 | Cálculo de las pendientes |
|-------------------|-----------------------------------------------|
| **Versión:** | 1.0 |
| **Descripción:** | El sistema debe calcular la pendiente media de la ruta y la pendiente máxima sostenida, descartando los valores aberrantes derivados del ruido de la señal. |
| **Importancia:** | Importante |
| **Urgencia:** | Hay presión |
 
| RF-15 | Cálculo de la duración y del tiempo en movimiento |
|-------------------|-----------------------------------------------|
| **Versión:** | 1.0 |
| **Descripción:** | El sistema debe calcular la duración total de la actividad a partir de las marcas de tiempo y el tiempo efectivo en movimiento, descontando las paradas. |
| **Importancia:** | Importante |
| **Urgencia:** | Hay presión |
 
| RF-16 | Detección de paradas |
|-------------------|-----------------------------------------------|
| **Versión:** | 1.0 |
| **Descripción:** | El sistema debe identificar los intervalos en los que el desplazamiento es inferior a un umbral durante un tiempo mínimo, y registrar su número y duración acumulada. |
| **Importancia:** | Deseable |
| **Urgencia:** | Puede esperar |
 
| RF-17 | Estimación del tiempo a pie |
|-------------------|-----------------------------------------------|
| **Versión:** | 1.0 |
| **Descripción:** | El sistema debe estimar el tiempo necesario para completar la ruta a pie en función de la distancia y del desnivel, mediante un método reconocido y citado en la documentación. |
| **Importancia:** | Importante |
| **Urgencia:** | Hay presión |
 
| RF-18 | Clasificación de la forma de la ruta |
|-------------------|-----------------------------------------------|
| **Versión:** | 1.0 |
| **Descripción:** | El sistema debe determinar si la ruta es circular o lineal comparando la proximidad entre el punto inicial y el final. |
| **Importancia:** | Deseable |
| **Urgencia:** | Puede esperar |
 
| RF-19 | Tratamiento de trazas sin datos de altitud |
|-------------------|-----------------------------------------------|
| **Versión:** | 1.0 |
| **Descripción:** | Cuando la traza carezca de datos de altitud, el sistema debe completar el análisis con las métricas que no dependan de ella e indicar expresamente que las restantes no están disponibles, sin estimarlas. |
| **Importancia:** | Importante |
| **Urgencia:** | Hay presión |
 
| RF-20 | Idempotencia del procesamiento |
|-------------------|-----------------------------------------------|
| **Versión:** | 1.0 |
| **Descripción:** | El sistema debe garantizar que el procesamiento repetido de un mismo trabajo de análisis no genere rutas ni resultados duplicados. |
| **Importancia:** | Vital |
| **Urgencia:** | Hay presión |
 
| RF-21 | Reintento y registro de análisis fallidos |
|-------------------|-----------------------------------------------|
| **Versión:** | 1.0 |
| **Descripción:** | Ante un fallo durante el análisis, el sistema debe reintentar el trabajo un número limitado de veces y, agotados los reintentos, marcar la ruta como fallida, apartar el trabajo para su inspección y conservar el motivo del fallo. |
| **Importancia:** | Vital |
| **Urgencia:** | Hay presión |
  
| RF-22 | Listado de las rutas del usuario |
|-------------------|-----------------------------------------------|
| **Versión:** | 1.0 |
| **Descripción:** | El sistema debe mostrar al usuario sus rutas ordenadas por fecha de la actividad, con su nombre, estado y métricas principales, y paginar el resultado. |
| **Importancia:** | Vital |
| **Urgencia:** | Inmediata |
 
| RF-23 | Consulta del detalle de un análisis |
|-------------------|-----------------------------------------------|
| **Versión:** | 1.0 |
| **Descripción:** | El sistema debe mostrar el conjunto completo de métricas calculadas para una ruta concreta. |
| **Importancia:** | Vital |
| **Urgencia:** | Inmediata |
 
| RF-24 | Visualización del trazado sobre un mapa |
|-------------------|-----------------------------------------------|
| **Versión:** | 1.0 |
| **Descripción:** | El sistema debe representar el trazado de la ruta sobre un mapa, reduciendo el número de puntos cuando sea necesario para no penalizar el rendimiento del navegador. |
| **Importancia:** | Importante |
| **Urgencia:** | Hay presión |
 
| RF-25 | Visualización del perfil de elevación |
|-------------------|-----------------------------------------------|
| **Versión:** | 1.0 |
| **Descripción:** | El sistema debe representar gráficamente la altitud de la ruta frente a la distancia recorrida. |
| **Importancia:** | Importante |
| **Urgencia:** | Hay presión |
 
| RF-26 | Borrado de una ruta |
|-------------------|-----------------------------------------------|
| **Versión:** | 1.0 |
| **Descripción:** | El sistema debe permitir al usuario eliminar una de sus rutas, lo que conlleva la eliminación de su análisis y del archivo almacenado, previa confirmación. |
| **Importancia:** | Importante |
| **Urgencia:** | Hay presión |
 
| RF-27 | Descarga del archivo original |
|-------------------|-----------------------------------------------|
| **Versión:** | 1.0 |
| **Descripción:** | El sistema debe permitir al usuario recuperar el archivo GPX que subió. |
| **Importancia:** | Deseable |
| **Urgencia:** | Puede esperar |
 
| RF-28 | Exportación del análisis en PDF |
|-------------------|-----------------------------------------------|
| **Versión:** | 1.0 |
| **Descripción:** | El sistema debe generar un documento PDF descargable con las métricas de la ruta, el mapa del trazado y el perfil de elevación. |
| **Importancia:** | Importante |
| **Urgencia:** | Puede esperar |
 