# 🚀 ACCESO A LA APLICACIÓN - MINIKUBE CON WINDOWS (DOCKER DRIVER)

## ⚠️ IMPORTANTE - Limitaciones en Windows con Docker Driver

Cuando usas Minikube con Docker driver en Windows, **NO puedes acceder directamente a través de la IP de Minikube** (192.168.49.2:30080). En su lugar, debes usar **port-forward** o **localhost**.

## ✅ SOLUCIÓN: Usar Port-Forward (RECOMENDADO)

### Opción 1: Port-Forward en PowerShell (Manteniendo la terminal abierta)

```powershell
kubectl port-forward svc/frontend-service 8080:80
```

**Luego accede a:**
```
http://localhost:8080
```

**La terminal debe mantenerse abierta** mientras usas la aplicación.

### Opción 2: Port-Forward en Background (Mejor)

En PowerShell, ejecuta en una terminal separada:

```powershell
# Terminal 1 - Port Forward del Frontend
kubectl port-forward svc/frontend-service 8080:80

# Terminal 2 - Port Forward del Backend (Opcional, para debugging)
kubectl port-forward svc/backend-service 8001:8000
```

**Accede a:**
- **Frontend (Nginx):** `http://localhost:8080`
- **Backend (FastAPI):** `http://localhost:8001`
- **Backend Docs (Swagger):** `http://localhost:8001/docs`

### Opción 3: Cambiar a Minikube Hyperv o VirtualBox

Si necesitas acceso directo sin port-forward, cambia el driver:

```powershell
# Cambiar a Hyper-V (requiere Hyper-V habilitado)
minikube stop
minikube delete
minikube start --driver=hyperv

# O cambiar a VirtualBox
minikube start --driver=virtualbox
```

Luego podrías acceder con:
```
http://192.168.49.2:30080
```

## 🧪 Verificar que Funciona

### Opción A: Curl desde PowerShell

```powershell
curl http://localhost:8080/
```

### Opción B: Desde el navegador

1. Abre tu navegador
2. Vaya a `http://localhost:8080`

## 📋 Resumen de URLs

| Componente | URL | Descripción |
|-----------|-----|-----------|
| **Frontend** | `http://localhost:8080` | Aplicación web principal |
| **Backend** | `http://localhost:8001` | API FastAPI |
| **Backend Docs** | `http://localhost:8001/docs` | Swagger UI para probar endpoints |
| **Backend ReDoc** | `http://localhost:8001/redoc` | Documentación alternativa |

## 🔧 Mantener Port-Forward Permanente

Para no tener que abrir una terminal cada vez, crea un archivo batch:

### Archivo: `start-minikube.bat`
```batch
@echo off
echo Iniciando Minikube Port Forwarding...
kubectl port-forward svc/frontend-service 8080:80
```

Guarda este archivo en tu escritorio y ejecútalo cada vez que quieras usar la app.

## 📝 Comandos Útiles

```powershell
# Ver qué puerto está usando el port-forward
kubectl port-forward svc/frontend-service 8080:80 --address 0.0.0.0

# Port forward en específico de pod (en lugar de servicio)
kubectl port-forward pod/frontend-85f8545fdd-h5b2w 8080:80

# Ver pods y servicios
kubectl get pods
kubectl get svc

# Ver logs en tiempo real
kubectl logs -f deployment/frontend
kubectl logs -f deployment/backend
```

## ✅ Verificación: Puertos Activos

Para verificar qué puertos están escuchando en tu máquina:

```powershell
# Ver puertos en uso
netstat -ano | findstr ":8080"
netstat -ano | findstr ":8001"
```

---

**¡Ahora deberías poder acceder a la aplicación en `http://localhost:8080`!** 🎉
