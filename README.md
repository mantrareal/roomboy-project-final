# México Destination Club - Sistema de Gestión Room Boy

## 📋 Descripción del Proyecto

Sistema web completo para la gestión y supervisión de tareas de Room Boys en México Destination Club. Permite a los empleados subir fotos como evidencia de trabajo completado y a los administradores supervisar el cumplimiento y aprobar/rechazar las evidencias.

## 🚀 Características Principales

### 🔐 Sistema de Autenticación
- Login seguro con Supabase Auth
- Redirección automática según rol de usuario
- Validación de formularios en tiempo real
- Manejo de errores amigable en español
- Verificación de sesiones existentes

### 👥 Roles de Usuario

#### 🏢 Administrador (Gerencia)
- **Dashboard completo** con estadísticas en tiempo real
- **Gráficas interactivas** de cumplimiento diario, semanal y mensual
- **Galería de fotos** con filtros avanzados
- **Gestión de empleados** con porcentajes de cumplimiento
- **Aprobación/Rechazo** de fotos desde el panel
- **Filtros por empleado, fecha y estado**

#### 🧹 Room Boy
- **Subida de fotos** (máximo 2 por día)
- **Progreso visual** del cumplimiento diario
- **Galería personal** de fotos subidas
- **Estadísticas semanales** de rendimiento
- **Validación de duplicados** por mesa/área
- **Vista previa** antes de subir

### 📱 Funcionalidades Técnicas

#### 🔒 Seguridad
- Autenticación con Supabase
- Validación de roles y permisos
- Verificación de metadatos de fotos
- Límites de tamaño de archivo (5MB)
- Protección contra duplicados

#### 📊 Gestión de Datos
- **Almacenamiento automático** de datos de cumplimiento
- **Eliminación automática** de fotos después de 32 días
- **Metadatos de fotos** para verificación
- **Historial completo** de actividades

#### 📱 Diseño Responsive
- **Optimizado para móviles** (Room Boys)
- **Dashboard profesional** para escritorio (Admins)
- **Interfaz intuitiva** y fácil de usar
- **Tema consistente** con colores corporativos

## 🛠️ Tecnologías Utilizadas

- **Frontend**: HTML5, CSS3, JavaScript (Vanilla)
- **Backend**: Supabase (PostgreSQL + Auth + Storage)
- **Gráficas**: Chart.js
- **Estilos**: CSS Variables, Flexbox, Grid
- **Hosting**: GitHub Pages compatible

## 📁 Estructura del Proyecto

```
roomboy-project-final/
├── index.html          # Página de login
├── admin.html          # Panel de administrador
├── roomboy-app.html    # Aplicación Room Boy
└── README.md           # Documentación
```

## 🗄️ Estructura de Base de Datos

### Tabla: `user_profiles`
```sql
- user_id (UUID, PK)
- email (TEXT)
- full_name (TEXT)
- role (TEXT) -- 'admin' o 'roomboy'
- active (BOOLEAN)
- created_at (TIMESTAMP)
```

### Tabla: `task_photos`
```sql
- id (UUID, PK)
- user_id (UUID, FK)
- table_number (INTEGER)
- description (TEXT, opcional)
- photo_url (TEXT)
- status (TEXT) -- 'pending', 'approved', 'rejected'
- created_at (TIMESTAMP)
- reviewed_at (TIMESTAMP, opcional)
- reviewed_by (UUID, opcional)
- metadata (JSONB)
```

## ⚙️ Configuración

### 1. Supabase
- **URL**: `https://depkkpxkbpgbvzylcdcn.supabase.co`
- **Anon Key**: Configurada en cada archivo HTML
- **Storage Bucket**: `task-photos` (público)

### 2. Usuarios de Ejemplo
Crear usuarios en Supabase Auth y perfiles correspondientes:

```sql
-- Administrador
INSERT INTO user_profiles (user_id, email, full_name, role, active)
VALUES ('uuid-admin', 'admin@mexicodestination.com', 'Administrador Principal', 'admin', true);

-- Room Boy
INSERT INTO user_profiles (user_id, email, full_name, role, active)
VALUES ('uuid-roomboy', 'roomboy1@mexicodestination.com', 'Juan Pérez', 'roomboy', true);
```

## 🚀 Instalación y Uso

### 1. Clonar el Repositorio
```bash
git clone https://github.com/mantrareal/roomboy-project-final.git
cd roomboy-project-final
```

### 2. Configurar Supabase
1. Crear proyecto en Supabase
2. Configurar las tablas según el esquema
3. Actualizar las credenciales en los archivos HTML
4. Configurar Storage bucket `task-photos`

### 3. Desplegar
- **Opción 1**: Servidor web local
  ```bash
  python3 -m http.server 8000
  ```
- **Opción 2**: GitHub Pages
- **Opción 3**: Netlify/Vercel

## 📱 Flujo de Usuario

### Room Boy
1. **Login** con credenciales
2. **Ver progreso** del día (0/2 fotos)
3. **Subir foto** con número de mesa
4. **Verificar** que no sea duplicada
5. **Confirmar** subida exitosa
6. **Revisar** fotos del día y estadísticas

### Administrador
1. **Login** con credenciales de admin
2. **Dashboard** con estadísticas generales
3. **Revisar fotos** en la galería
4. **Filtrar** por empleado, fecha o estado
5. **Aprobar/Rechazar** fotos pendientes
6. **Monitorear** cumplimiento por empleado

## 🔧 Funcionalidades Avanzadas

### Validaciones
- ✅ Máximo 2 fotos por día por empleado
- ✅ No duplicados por mesa en el mismo día
- ✅ Validación de tamaño de archivo (5MB)
- ✅ Verificación de tipo de archivo (imágenes)
- ✅ Campos obligatorios y opcionales

### Automatizaciones
- 🔄 Eliminación automática de fotos (32 días)
- 📊 Cálculo automático de porcentajes
- 📈 Actualización en tiempo real de estadísticas
- 🔔 Mensajes de estado y confirmación

### Optimizaciones
- 📱 Diseño mobile-first para Room Boys
- 💻 Dashboard profesional para Admins
- ⚡ Carga rápida con CSS/JS optimizado
- 🎨 Interfaz moderna y atractiva

## 🐛 Solución de Problemas

### Problemas Comunes

1. **Error de autenticación**
   - Verificar credenciales de Supabase
   - Comprobar configuración de Auth

2. **Fotos no se suben**
   - Verificar configuración de Storage
   - Comprobar permisos del bucket

3. **Gráficas no aparecen**
   - Verificar carga de Chart.js
   - Comprobar datos en la base

## 📞 Soporte

Para soporte técnico o consultas:
- **Repositorio**: [GitHub](https://github.com/mantrareal/roomboy-project-final)
- **Issues**: Crear issue en GitHub para reportar bugs

## 📄 Licencia

Este proyecto es propiedad de México Destination Club. Todos los derechos reservados.

---

**Desarrollado con ❤️ para México Destination Club**

