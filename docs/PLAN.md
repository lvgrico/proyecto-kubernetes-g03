# Plan de trabajo

## 0. Repositorio
- [ ] Repo **público** creado y todos los integrantes agregados como colaboradores
- [ ] Primer commit con esta estructura (`main`)
- [ ] Tabla de integrantes completa en el README

## 1. Entorno
- [ ] `minikube start`
- [ ] `kubectl apply -f .\k8s\00-namespace.yaml`
- [ ] `.\scripts\preparar-imagenes.ps1` (verificar que salen las 3 imágenes)
- [ ] `pip install -r .\entrega\requirements.txt`

## 2. Gestor (`entrega/gestor_jobs.py`)
- [ ] Opción 1: crear Job (probar con `python/hola/baja`)
- [ ] Opción 2: estado — contrastar con `kubectl get jobs,pods -n estudiantes-202630`
- [ ] Opción 3: logs — la última línea debe ser el JSON de la tarea
- [ ] Opción 4: limpieza — que no borre Jobs en ejecución
- [ ] Probar sin `propagation_policy` para ver el Pod huérfano (y volver a ponerla)
- [ ] Provocar un `OOMKilled` bajando `mem_limit` y verlo reflejado en el estado

## 3. Tarea adicional
- [ ] Elegir lenguaje y tarea
- [ ] Implementarla en el programa del contenedor
- [ ] Reconstruir con `preparar-imagenes.ps1`
- [ ] Agregarla a `catalogo/tareas.json` y a `TAMANOS`

## 4. Entrega (antes de la clase de la semana 12)
- [ ] README explicando la implementación
- [ ] Video de demo en YouTube y link en el README
- [ ] Link del repo entregado en la actividad del catálogo
- [ ] Todos los integrantes pueden explicar el código y el recorrido completo
