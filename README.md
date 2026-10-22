# Ridgeline

Analizador de rutas de senderismo a partir de archivos GPX. Convierte una traza GPS en métricas fiables sobre la ruta (distancia, desnivel acumulado, pendientes, tiempo estimado) filtrando el ruido del GPS y documentando el método de cálculo empleado.

![Estado](https://img.shields.io/badge/estado-en%20desarrollo-yellow?style=flat-square)

---

## El problema

Las plataformas de senderismo calculan métricas sobre las rutas, pero no publican su método y los resultados difieren notablemente entre ellas: la misma ruta puede arrojar 870 m de desnivel acumulado en una y más de 1.000 m en otra. La causa está en el tratamiento del ruido de altitud, en el origen del dato (barométrico o satelital) y en el umbral a partir del cual se contabiliza una subida.

Ridgeline hace lo contrario: el método es explícito, está documentado y se valida contra rutas de referencia.

## Qué hace

- Analiza un archivo GPX y calcula distancia, desnivel positivo y negativo, altitudes extremas, pendientes, duración, tiempo en movimiento y tiempo estimado a pie.
- Detecta paradas y determina si la ruta es circular o lineal.
- Muestra el trazado sobre un mapa y el perfil de elevación.
- Conserva el histórico de rutas de cada usuario.
- Exporta el análisis completo en PDF.

El procesamiento es asíncrono: el archivo se almacena, se encola un trabajo y un worker independiente ejecuta el análisis.

## Puesta en marcha

> Pendiente. Se documentará cuando exista el primer despliegue local funcional.

```bash
git clone https://github.com/amgranados/ridgeline.git
cd ridgeline
docker compose up
```

## Documentación

| Archivo | Contenido |
|---|---|
| [`Requisitos`](docs/requisitos.md) | Visión y alcance, glosario, actores, requisitos, historias de usuario |
| [`Análisis`](docs/analisis.md) | Casos de uso, modelo de dominio, máquina de estados |
| [`Diseño`](docs/diseno.md) | Arquitectura, modelo de datos, diagramas de secuencia, contrato de la API |
| [`Calidad`](docs/calidad.md) | Plan de pruebas, trazabilidad, quality gate, riesgos |
| [`ADRs`](docs/adr/) | Registro de decisiones de arquitectura |
| [`UI`](docs/ui/) | Esbozos de interfaz y guía de estilos |

Los diagramas están escritos en PlantUML y Mermaid para que se versionen y se revisen como el resto del código.

## Alcance

Ridgeline es un analizador, no una red social de senderismo. Quedan fuera de esta versión: rutas públicas, comentarios y cualquier funcionalidad social, aplicación móvil, grabación y navegación sobre el terreno, edición del trazado, comparación entre rutas y formatos distintos de GPX.

El detalle completo está en [Visión y alcance](docs/requisitos.md).

## Sobre el proyecto

Proyecto personal de Adrián Martínez Granados, desarrollado como ejercicio de arquitectura backend y cloud: diseño dirigido por eventos, infraestructura como código y una cadena de calidad automatizada. El proceso de análisis y diseño está documentado por completo en `docs/`.