## 🏗️ Arquitectura
                 ┌───────────────────────┐
                 │       GitHub          │
                 │  Actions Workflow     │
                 └───────────┬───────────┘
                             │
                             │ Build & Push
                             ▼
                 ┌───────────────────────┐
                 │        AWS ECR        │
                 │   Imágenes Docker     │
                 └───────────┬───────────┘
                             │
                             │ Upload .env + schema.sql
                             ▼
                 ┌───────────────────────┐
                 │         AWS S3        │
                 │ Configuración & Env   │
                 └───────────┬───────────┘
                             │
                             │ SSM Commands
                             ▼
                 ┌───────────────────────┐
                 │         AWS SSM       │
                 │  RunShellScript en EC2│
                 └───────────┬───────────┘
                             │
                             ▼
        ┌───────────────────────────────────────────────┐
        │                   AWS EC2                     │
        │                                               │
        │   ┌───────────────┐   ┌───────────────┐       │
        │   │ admin-dashboard│   │  public-app   │      │
        │   │   (port 3000)  │   │   (port 8000) │      │
        │   └───────────────┘   └───────────────┘       │
        │           │                   │               │
        │           │                   │               │
        │   ┌───────────────┐           │               │
        │   │    service    │───────────┘               │
        │   │   (port 3001) │                           │
        │   └───────────────┘                           │
        │                                               │
        │   ┌───────────────┐                           │
        │   │    MySQL      │                           │
        │   │   (port 3306) │                           │
        │   └───────────────┘                           │
        │                                               │
        │   Todos conectados en red Docker `cloudnet`   │
        └───────────────────────────────────────────────┘


## 🏗️ Componentes

    GitHub Actions: dispara el pipeline al hacer commit/push.

    ECR: almacena las imágenes Docker de las apps.

    S3: se suben los composes  .env y schema.sql para sincronización en EC2.

    SSM: ejecuta comandos remotos en EC2 (sin necesidad de SSH).

    EC2: host donde se levantan los contenedores con Docker Compose.

    Docker network cloudnet: conecta admin-dashboard, public-app, service y MySQL internamente.


📦 Flujo

    Dev hace commit en GitHub.

    Workflow se construyen imágenes y las sube a ECR.

    Workflow se suben compose .env y schema.sql a S3.

    Workflow ejecuta SSM → EC2 descarga archivos de S3.

    EC2 levanta contenedores con Docker Compose.


🔒 Seguridad 

    .env gestionados en S3, no en el repo.

    IAM roles para acceso controlado a S3/ECR.

    Puertos expuestos solo los necesarios.


# 🚀 Escalabilidad

    Infraestructura lista para migrar a ECS/EKS.

    Separación de apps en contenedores independientes.

    Uso de red interna cloudnet para comunicación segura.


# 📦 Endpoints
    Admin Dashboard:
        GET /api/count → devuelve contador.
        POST /api/increment → incrementa contador.

    Public App:
        POST /api/calculate → guarda cálculo y notifica al admin.

    Service:
        Endpoints de lógica de negocio.

# 🚀 Parametros

![Parameters](/wiki/parameters-deploy-apps.png)

Public-app

![Public App Operativo](/wiki/public-app.png)

Admin-dashboard

![Admin Dashboard Operativo](/wiki/admin-dashboard.png)
