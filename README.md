# EventosU: modelo de amenazas

Modelo de amenazas de EventosU, el sitio (ficticio) donde la universidad publica congresos, talleres y conciertos, y donde los usuarios se inscriben, pagan y reciben una entrada con código QR. El sistema está en fase de diseño, así que todo el análisis se hizo sobre la descripción del caso.

## Autor

| Nombre | Carné | Curso |
|---|---|---|
| Pablo Andrés Pineda Polanco | 21010478 | Ciberseguridad |

## Alcance

Entra en el modelo: el sitio React servido desde el CDN (incluido el panel `/admin`), la API en Node.js, PostgreSQL, el bucket de objetos, los logs, la comunicación de la app de puerta con la API y las integraciones con Microsoft Entra ID, la pasarela de pagos y el servicio de correo.

Queda fuera: la seguridad interna de esos tres terceros, los datos de tarjeta (los procesa la pasarela), el sistema operativo de los teléfonos y computadoras, la seguridad física del evento y la red interna de la universidad.

Los supuestos que usé donde el caso no dice nada (formato del QR, dónde se guardan los logs, cómo se guarda el token, etc.) están en la sección 2.3 del informe.

## Contenido

```
eventosu-threat-model/
├── README.md               # este archivo
├── model/eventosu.json     # modelo de OWASP Threat Dragon (DFD nivel 0 y nivel 1 con las amenazas)
├── docs/informe.pdf        # informe final
└── docs/amenazas.csv       # tabla de amenazas (T01 a T26)
```

## Cómo abrir el modelo

1. Instalar OWASP Threat Dragon 2.x. Puede ser la versión de escritorio (desde los releases del repositorio `OWASP/threat-dragon` en GitHub) o la versión web.
2. En la versión de escritorio, elegir la opción para abrir un modelo existente y seleccionar `model/eventosu.json`. En la versión web, entrar con *Local Session*, ir a *Import* y cargar o pegar el contenido del archivo.
3. El modelo tiene dos diagramas:
   - **Nivel 0 - Contexto**: EventosU como un solo proceso con sus actores externos.
   - **Nivel 1 - Componentes**: todos los componentes, cinco límites de confianza y los flujos con su dato y protocolo.
4. Las 26 amenazas están asociadas a los elementos y flujos del nivel 1. Se ven seleccionando cada elemento o desde la opción de reporte. Los elementos en rojo son los que tienen amenazas abiertas.

El JSON se validó contra el esquema v2 de Threat Dragon 2.6 (`threat-dragon-v2.schema.json`), que es el mismo que usa la aplicación al importar un modelo.

## Resumen de resultados

- 26 amenazas STRIDE: 6 S, 6 T, 3 R, 4 I, 3 D y 4 E. Trece están sobre flujos que cruzan un límite de confianza.
- Riesgo: 13 altos, 12 medios y 1 bajo, con una matriz de impacto × probabilidad de 3 × 3.
- Las cinco críticas (riesgo 9) son T01 webhook falsificado, T02 QR fabricado, T07 monto manipulado, T17 exportación de CSV de otro evento y T23 funciones de admin sin validación en el servidor. Cada una está trazada a OWASP ASVS 5.0, a una historia de usuario y a un control del pipeline.
- T06 (QR compartido o revendido) es un riesgo aceptado, a cargo de la Coordinación de Eventos. T22 (DDoS volumétrico) se transfiere al proveedor de CDN y nube.
