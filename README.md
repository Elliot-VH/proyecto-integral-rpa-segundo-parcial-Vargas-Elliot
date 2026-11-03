# Proyecto Integral de RPA - Cierre del Segundo Parcial

## Integrantes
* Elliot Vargas Herrera

## Herramientas y versiones
* Power Automate Desktop (PAD)
* Aplicación de escritorio "Solicitudes RPA" (Escenario de prueba: Base)
* Bloc de notas / Editor CSV (Codificación UTF-8)

## Rutas configurables
* **Archivo de entrada:** `C:\Ruta\Hacia\Tu\Carpeta\solicitudes_para_pad.csv` .
* **Bitácora de salida:** `C:\Ruta\Hacia\Tu\Carpeta\bitacora_resultados.txt` (Ajustar en PAD).

## Instrucciones de preparación y ejecución
1. Descargar los archivos del repositorio en una carpeta local.
2. Abrir Power Automate Desktop e importar o abrir el flujo `Proyecto_RegistroSolicitudes`.
3. Modificar las variables de ruta al inicio del flujo para que apunten al archivo CSV local y a la ruta deseada para la bitácora.
4. Abrir la aplicación "Solicitudes RPA" y asegurar que el **Escenario de prueba** esté en "Base".
5. Ejecutar el flujo. El robot leerá los datos, validará la interfaz y registrará los casos completos de forma autónoma.

## Limitaciones conocidas
* El registro C03 no se envía al formulario y se mantiene pendiente porque el documento original carece del campo "Tipo".
* Las opciones de la lista desplegable son estrictas; los datos del CSV deben coincidir usando formato de título (ej. "Administración", no "ADMINISTRACIÓN") y estar entre comillas dobles si contienen comas.

## Matriz de pruebas
| Prueba | Resultado que debe demostrar | Estado obtenido |
| :--- | :--- | :--- |
| Documento completo y aprobado | Llega al formulario y termina con folio real y bitácora. | Exitoso |
| Información ausente en C03 | Se conserva pendiente y no se envía al formulario. | Exitoso (Falta campo Tipo) |
| Datos inválidos en formulario | Reconoce el rechazo; no informa un registro exitoso. | Exitoso |
| Identificador ya registrado | Reconoce el duplicado; el contador no aumenta. | Exitoso |
| Respuesta lenta y espera | Verifica el registro sin repetir la confirmación. | Exitoso |
| Espera agotada tras confirmar | Informa pendiente; comprueba después sin reenviar. | Exitoso |
| Aplicación cerrada | Agota reintentos y registra fallo técnico. | Exitoso |

## Reflexión sobre robustez y validación
Para evitar registrar datos incorrectos, la validación crítica se realiza en la fase de extracción, asegurando que el CSV (solicitudes_para_pad.csv) únicamente contenga registros con los cinco campos requeridos completos. Casos incompletos, como el C03 que carece del campo "Tipo", son retenidos intencionalmente y no llegan al formulario. Durante la automatización, la exactitud se garantiza usando selectores de texto que respetan mayúsculas y minúsculas. 

La comprobación del guardado no se basa en cálculos predictivos, sino en la lectura directa del folio real que emite la interfaz y en la validación del incremento en el contador inferior de "Solicitudes guardadas". Finalmente, es fundamental que no todas las acciones se reintenten automáticamente. Si el flujo agota el tiempo de espera después de hacer clic en "Registrar solicitud", reintentar ese clic a ciegas es un riesgo grave que puede generar registros duplicados en la base de datos si el sistema simplemente estaba lento. Ante la incertidumbre, el flujo debe detener los clics, clasificar la solicitud como pendiente de verificación humana o técnica, y priorizar la integridad de la información sobre la velocidad.
