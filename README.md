# Don't Sleep Shortener

## Descripción
Don't Sleep Shortener es una aplicación web diseñada para acortar URLs largas en URLs más cortas y fáciles de compartir. Esta aplicación también rastrea estadísticas de clics, proporcionando información sobre el uso de los enlaces acortados. La arquitectura incluye una base de datos relacional (MySQL) y un sistema de caché (Redis) para un rendimiento y escalabilidad óptimos.

## Características
- Crear y gestionar URLs cortas
- Autenticación y autorización de usuarios
- Estadísticas detalladas de clics (diarias, semanales, mensuales)
- Interfaz de usuario moderna
- Almacenamiento en caché para mejorar el rendimiento
- Manejo de sesiones eficiente

## Tecnologías Utilizadas
- **Frontend:** React.js, Tailwind CSS
- **Backend:** Node.js, Express.js
- **Base de Datos Primaria:** MySQL
- **Almacenamiento en Caché:** Redis
- **Contenerización:** Docker
- **CI/CD:** GitHub Actions
- **Seguridad:** Let's Encrypt

## Estructura del Proyecto (TBD)
```
url-shortener/
├── backend/
│   ├── src/
│   │   ├── controllers/
│   │   ├── models/
│   │   ├── routes/
│   │   └── utils/
│   ├── tests/
│   ├── .env
│   └── server.js
├── frontend/
│   ├── public/
│   ├── src/
│   │   ├── components/
│   │   ├── pages/
│   │   └── utils/
│   ├── .env
│   └── package.json
├── docker-compose.yml
└── README.md
```

## Configuración y Despliegue

### Prerrequisitos
- Node.js >= 14.x
- Docker >= 20.x
- MySQL >= 8.x
- Redis >= 6.x

### Variables de Entorno
Crea un archivo `.env` en los directorios `backend` y `frontend` con las siguientes variables:

#### Backend
```
PORT=5000
DB_HOST=your_mysql_host
DB_USER=your_mysql_user
DB_PASSWORD=your_mysql_password
DB_NAME=url_shortener
REDIS_HOST=your_redis_host
REDIS_PORT=6379
JWT_SECRET=your_jwt_secret
```

#### Frontend
```
REACT_APP_API_URL=http://localhost:5000
```

### Instalación (TBD)

### Ejecución en Desarrollo

#### Backend
```bash
cd backend
npm run dev
```

#### Frontend
```bash
cd frontend
npm start
```

### Despliegue con Docker (TBD)
```bash
docker-compose up --build
```

## Estructura de la Base de Datos

### MySQL

#### Tabla: `users`
```sql
CREATE TABLE users (
    id UUID PRIMARY KEY,
    username VARCHAR(255) UNIQUE NOT NULL,
    email VARCHAR(255) UNIQUE NOT NULL,
    password_hash VARCHAR(255) NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP
);
```

#### Tabla: `urls`
```sql
CREATE TABLE urls (
    id UUID PRIMARY KEY,
    user_id UUID NOT NULL,
    original_url TEXT NOT NULL,
    short_url VARCHAR(255) UNIQUE NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    FOREIGN KEY (user_id) REFERENCES users(id)
);
```

#### Tabla: `clicks`
```sql
CREATE TABLE clicks (
    id UUID PRIMARY KEY,
    url_id UUID NOT NULL,
    clicked_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    ip_address VARCHAR(45) NOT NULL,
    user_agent TEXT NOT NULL,
    referrer TEXT,
    FOREIGN KEY (url_id) REFERENCES urls(id)
);
```

#### Tabla: `statistics`
```sql
CREATE TABLE statistics (
    id UUID PRIMARY KEY,
    url_id UUID NOT NULL,
    daily_clicks INT NOT NULL DEFAULT 0,
    weekly_clicks INT NOT NULL DEFAULT 0,
    monthly_clicks INT NOT NULL DEFAULT 0,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    FOREIGN KEY (url_id) REFERENCES urls(id)
);
```

### Redis

#### Claves y Valores
- **short_url:{short_url}** -> `{original_url}`
- **session:{session_id}** -> `{user_id, session_data}`
- **url_clicks:{url_id}:{date}** -> `{click_count}`

## API Endpoints

### Autenticación
- **POST /api/auth/register:** Registrar un nuevo usuario
- **POST /api/auth/login:** Iniciar sesión de usuario

### URLs
- **POST /api/urls:** Crear una nueva URL corta
- **GET /api/urls/:shortUrl:** Redirigir a la URL original
- **GET /api/urls:** Obtener todas las URLs cortas del usuario autenticado

### Clics
- **GET /api/clicks/:urlId:** Obtener estadísticas de clics de una URL

## Contribuciones
¡Las contribuciones son bienvenidas! Por favor, sigue los pasos a continuación para contribuir:

1. Haz un fork del repositorio.
2. Crea una rama para tu feature (`git checkout -b feature/nueva-feature`).
3. Realiza tus cambios y commitea (`git commit -am 'Agrega nueva feature'`).
4. Haz push a la rama (`git push origin feature/nueva-feature`).
5. Abre un Pull Request.

## Licencia
Este proyecto está bajo la licencia MIT. Consulta el archivo [LICENSE](LICENSE) para más detalles.
