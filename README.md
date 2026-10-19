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
| _Completar_ | _Completar_ |

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

### Decisiones sobre el Job

| Campo | Valor | Por qué |
|---|---|---|
| `backoff_limit` | `0` | _Completar con la explicación del grupo_ |
| `restart_policy` | `Never` | _Completar_ |
| `image_pull_policy` | `Never` | _Completar_ |
| `resources.requests` / `limits` | según `NIVELES` | _Completar_ |

### Tarea adicional

_Completar: nombre de la tarea, lenguaje elegido, cambios en el contenedor, en
`catalogo/tareas.json` y en `TAMANOS`._

### Pruebas realizadas

_Completar: capturas o salidas de `kubectl get jobs,pods -n estudiantes-202630`, la
comparación de `hola` entre lenguajes y la prueba de `OOMKilled`._
