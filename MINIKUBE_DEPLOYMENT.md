# Guía de Despliegue en Minikube

## Prerequisitos
```bash
# Instalar Minikube si no lo tienes
# https://minikube.sigs.k8s.io/docs/start/

# Instalar kubectl
# https://kubernetes.io/es/docs/tasks/tools/

# Iniciar Minikube
minikube start

# Verificar estado
minikube status
```

## Paso 1: Preparar las imágenes Docker

Tienes dos opciones:

### Opción A: Usar imágenes del registro (si ya existen)
Las imágenes se descargarán automáticamente de Docker Hub:
- `eritzsm/airbnb-backend:latest`
- `eritzsm/airbnb-frontend:latest`

### Opción B: Construir localmente en Minikube
```bash
# Apuntar el Docker daemon a Minikube
minikube docker-env | Invoke-Expression  # En PowerShell

# O para cmd.exe:
# @FOR /f "tokens=*" %i IN ('minikube docker-env') DO @%i

# Construir el backend
docker build -f Dockerfile.backend -t airbnb-backend:latest .

# Construir el frontend
docker build -f frontend/Dockerfile -t airbnb-frontend:latest .

# Actualizar imagePullPolicy a "Never" en deployment.yaml si usas esta opción
```

## Paso 2: Aplicar los manifiestos Kubernetes

```bash
# Cambiar al directorio del proyecto
cd AIRBNB_GESTION

# 1. Crear ConfigMap
kubectl apply -f configmap.yaml

# 2. Crear Secrets
kubectl apply -f secret.yaml

# 3. Crear Services
kubectl apply -f service.yaml

# 4. Crear Deployments y StatefulSet
kubectl apply -f deployment.yaml
```

## Paso 3: Verificar el despliegue

```bash
# Ver todos los recursos
kubectl get all

# Ver los pods en ejecución
kubectl get pods -w

# Ver los servicios
kubectl get svc

# Ver los PersistentVolumeClaims
kubectl get pvc

# Ver los StatefulSets
kubectl get statefulset
```

## Paso 4: Acceder a la aplicación

```bash
# Obtener la URL del frontend (NodePort)
minikube service frontend-service --url

# En PowerShell:
# minikube service frontend-service

# El frontend estará disponible en http://<minikube-ip>:30080
# Obtener IP de Minikube:
minikube ip
```

## Paso 5: Ver logs de los pods

```bash
# Ver logs del backend
kubectl logs -f deployment/backend-deployment

# Ver logs del frontend
kubectl logs -f deployment/frontend-deployment

# Ver logs de PostgreSQL
kubectl logs -f statefulset/db-statefulset
```

## Solución de problemas

### El pod no inicia
```bash
# Ver detalles del pod
kubectl describe pod <pod-name>

# Ver eventos del cluster
kubectl get events --sort-by='.lastTimestamp'
```

### PostgreSQL no se conecta
```bash
# Verificar la conectividad entre pods
kubectl exec -it <backend-pod-name> -- bash
# Dentro del pod:
pg_isready -h db-service -p 5432 -U user_fastapi
```

### Cambiar imágenes o configuración
```bash
# Para reimplementar con cambios:
kubectl rollout restart deployment/backend-deployment
kubectl rollout restart deployment/frontend-deployment
kubectl rollout restart statefulset/db-statefulset

# Para eliminar todo y empezar de nuevo:
kubectl delete -f deployment.yaml
kubectl delete -f service.yaml
kubectl delete secret airbnb-secret
kubectl delete configmap airbnb-config
```

## Actualizar Secrets de Google OAuth

Si necesitas actualizar los secretos de Google:

```bash
# Codificar nuevos valores
# PowerShell:
[Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes("TU_CLIENT_ID"))
[Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes("TU_CLIENT_SECRET"))

# O en Linux/WSL:
# echo -n 'TU_CLIENT_ID' | base64
# echo -n 'TU_CLIENT_SECRET' | base64

# Actualizar el secret.yaml con los nuevos valores

# Aplicar los cambios
kubectl apply -f secret.yaml

# Reiniciar los pods para que usen los nuevos secretos
kubectl rollout restart deployment/backend-deployment
```

## Comandos útiles

```bash
# Dashboard de Minikube
minikube dashboard

# Abrir el servicio frontend directamente
minikube service frontend-service

# Puerto-forward para acceso local
kubectl port-forward svc/frontend-service 8080:80
kubectl port-forward svc/backend-service 8001:8000

# Ejecutar comando en un pod
kubectl exec -it <pod-name> -- bash

# Copiar archivos del/al pod
kubectl cp <pod-name>:/path/in/pod /local/path
```

## Limpiar recursos

```bash
# Eliminar todo el despliegue
kubectl delete -f deployment.yaml
kubectl delete -f service.yaml
kubectl delete -f secret.yaml
kubectl delete -f configmap.yaml

# Limpiar Minikube completamente
minikube delete
```

## Notas importantes

1. **imagePullPolicy**: Está configurado como `IfNotPresent` para usar imágenes locales si existen
2. **Storage**: PostgreSQL usa PersistentVolumeClaim (PVC) para persistencia de datos
3. **Health Checks**: Se incluyen readiness y liveness probes para mejor estabilidad
4. **Resource Limits**: Se han configurado límites de CPU y memoria para cada contenedor
5. **Headless Service**: La base de datos usa un servicio headless para mejor estabilidad del StatefulSet
