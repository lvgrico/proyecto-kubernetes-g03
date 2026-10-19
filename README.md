# Proyecto Kubernetes 202630 — Gestor de Jobs

Herramienta de línea de comandos en Python que crea **Jobs de Kubernetes** a partir de un
menú (lenguaje, tarea y complejidad), revisa su estado, muestra sus logs y limpia los
Jobs terminados. Corre sobre Minikube y usa el cliente oficial `kubernetes` (sin `kubectl`).

```text
gestor_jobs.py -> Job de Kubernetes -> Pod -> contenedor con argumentos -> logs
```

> El enunciado original del profesor está en [`docs/ENUNCIADO.md`](docs/ENUNCIADO.md).

## Integrantes

| Nombre | Código |
|---|---|
| Paulina Valencia| 200167925 |
| Laura Gonzalez | 200195919 |
| Carlos Morales | 200190657 |

## Demo

📹 Video de YouTube: _Completar con el link_

## Estructura del repositorio

```text
catalogo/tareas.json         catálogo de lenguajes, imágenes y tareas
docs/                        enunciado y plan de trabajo
entrega/gestor_jobs.py       gestor con menú (lo que implementa el grupo)
entrega/requirements.txt     dependencias de Python
imagenes/{python,java,c}/    imágenes con las tareas (hola, ordenar, fib, matriz)
k8s/00-namespace.yaml        namespace estudiantes-202630
scripts/preparar-imagenes.ps1 construye las imágenes dentro de Minikube
```

## Requisitos

Docker, Minikube, `kubectl` (solo para verificar manualmente) y Python 3.10+.

## Puesta en marcha

Desde la raíz del repositorio (PowerShell):

```powershell
minikube start
kubectl apply -f .\k8s\00-namespace.yaml
.\scripts\preparar-imagenes.ps1
pip install -r .\entrega\requirements.txt
python .\entrega\gestor_jobs.py
```

Si se modifica el programa de un contenedor hay que volver a ejecutar
`preparar-imagenes.ps1` (construye contra el Docker de Minikube; `minikube image load`
no reemplaza un tag existente).

## Uso

El programa se ejecuta sin argumentos y muestra un menú:

```text
=== Gestor de Jobs - namespace estudiantes-202630 ===
  1. Ejecutar una tarea nueva
  2. Revisar el estado de las tareas
  3. Ver los logs de una tarea
  4. Limpiar las tareas terminadas
  5. Salir
```

- **Opción 1:** pide lenguaje, tarea y complejidad. El menú de complejidad muestra el N y la
  memoria de cada nivel. Al terminar imprime el nombre del Job y sus recursos.
- **Opción 2:** lista los Jobs del namespace con su estado.
- **Opción 3:** permite elegir un Job y muestra la salida de su contenedor.
- **Opción 4:** borra los Jobs terminados (completados o fallidos) tras pedir confirmación;
  no toca los que siguen corriendo.
  
## Implementación

### Flujo del programa

1. **Selección** (`seleccionar`): lenguaje y tarea salen de `catalogo/tareas.json`; la
   complejidad fija a la vez los recursos (`NIVELES`) y el valor de N (`TAMANOS`).
2. **Creación** (`crear_job`): se arma un `V1Job` equivalente a un manifiesto `batch/v1`
   y se envía con `create_namespaced_job`.
3. **Estado** (`describir_estado`, `listar_jobs`): se interpretan los contadores
   `active`, `succeeded` y `failed` del `status` del Job.
4. **Logs** (`consultar_logs`): los logs son del Pod, que se encuentra con la etiqueta
   `job-name=<nombre>`.
5. **Limpieza** (`jobs_terminados`, `eliminar_job`): solo se borran Jobs sin Pods activos
   y con al menos un Pod correcto o fallido, con `propagation_policy="Background"`.

### Tarea adicional

Cuenta cuántos números primos existen hasta N. Se implementó en los tres lenguajes
(Python, Java y C) con el mismo contrato que las demás tareas: recibe `contar_primos <N>`,
escribe logs en stdout y cierra con un JSON de una línea que incluye `cantidad_primos`.
Por ejemplo, para N=100000 el resultado es 9592, que es el valor matemáticamente correcto.

Cambios realizados:

- **Contenedores:** se agregó la tarea en el programa de `imagenes/python/`,
  `imagenes/java/` y `imagenes/c/`, y se reconstruyeron las imágenes con
  `preparar-imagenes.ps1`.
- **`catalogo/tareas.json`:** se añadió `contar_primos` a la lista de tareas de cada lenguaje.
- **`TAMANOS` en `gestor_jobs.py`:** se añadió la fila
  `"contar_primos": {"baja": 10000, "media": 100000, "alta": 1000000}`.

