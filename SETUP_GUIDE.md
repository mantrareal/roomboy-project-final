# 🚀 Guía de Configuración - México Destination Club

## 📋 Requisitos Previos

- Cuenta de Supabase (gratuita)
- Navegador web moderno
- Acceso a GitHub (opcional para deployment)

## 🗄️ Configuración de Supabase

### 1. Crear Proyecto en Supabase

1. Ve a [supabase.com](https://supabase.com)
2. Crea una cuenta o inicia sesión
3. Crea un nuevo proyecto
4. Anota la **URL del proyecto** y **Anon Key**

### 2. Configurar Base de Datos

Ejecuta estos comandos SQL en el editor de Supabase:

```sql
-- Crear tabla de perfiles de usuario
CREATE TABLE user_profiles (
    user_id UUID REFERENCES auth.users(id) PRIMARY KEY,
    email TEXT UNIQUE NOT NULL,
    full_name TEXT NOT NULL,
    role TEXT NOT NULL CHECK (role IN ('admin', 'roomboy')),
    active BOOLEAN DEFAULT true,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);

-- Crear tabla de fotos de tareas
CREATE TABLE task_photos (
    id UUID DEFAULT gen_random_uuid() PRIMARY KEY,
    user_id UUID REFERENCES user_profiles(user_id) NOT NULL,
    table_number INTEGER NOT NULL,
    description TEXT,
    photo_url TEXT NOT NULL,
    status TEXT DEFAULT 'pending' CHECK (status IN ('pending', 'approved', 'rejected')),
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    reviewed_at TIMESTAMP WITH TIME ZONE,
    reviewed_by UUID REFERENCES user_profiles(user_id),
    metadata JSONB
);

-- Crear índices para mejor rendimiento
CREATE INDEX idx_task_photos_user_id ON task_photos(user_id);
CREATE INDEX idx_task_photos_created_at ON task_photos(created_at);
CREATE INDEX idx_task_photos_status ON task_photos(status);

-- Habilitar Row Level Security (RLS)
ALTER TABLE user_profiles ENABLE ROW LEVEL SECURITY;
ALTER TABLE task_photos ENABLE ROW LEVEL SECURITY;

-- Políticas de seguridad para user_profiles
CREATE POLICY "Users can view their own profile" ON user_profiles
    FOR SELECT USING (auth.uid() = user_id);

CREATE POLICY "Admins can view all profiles" ON user_profiles
    FOR SELECT USING (
        EXISTS (
            SELECT 1 FROM user_profiles 
            WHERE user_id = auth.uid() AND role = 'admin'
        )
    );

-- Políticas de seguridad para task_photos
CREATE POLICY "Users can view their own photos" ON task_photos
    FOR SELECT USING (auth.uid() = user_id);

CREATE POLICY "Users can insert their own photos" ON task_photos
    FOR INSERT WITH CHECK (auth.uid() = user_id);

CREATE POLICY "Admins can view all photos" ON task_photos
    FOR SELECT USING (
        EXISTS (
            SELECT 1 FROM user_profiles 
            WHERE user_id = auth.uid() AND role = 'admin'
        )
    );

CREATE POLICY "Admins can update photo status" ON task_photos
    FOR UPDATE USING (
        EXISTS (
            SELECT 1 FROM user_profiles 
            WHERE user_id = auth.uid() AND role = 'admin'
        )
    );
```

### 3. Configurar Storage

1. Ve a **Storage** en el panel de Supabase
2. Crea un nuevo bucket llamado `task-photos`
3. Configúralo como **público**
4. Establece las siguientes políticas:

```sql
-- Política para subir fotos (solo usuarios autenticados)
CREATE POLICY "Users can upload photos" ON storage.objects
    FOR INSERT WITH CHECK (
        bucket_id = 'task-photos' AND 
        auth.role() = 'authenticated'
    );

-- Política para ver fotos (público)
CREATE POLICY "Photos are publicly viewable" ON storage.objects
    FOR SELECT USING (bucket_id = 'task-photos');

-- Política para eliminar fotos (solo admins)
CREATE POLICY "Admins can delete photos" ON storage.objects
    FOR DELETE USING (
        bucket_id = 'task-photos' AND
        EXISTS (
            SELECT 1 FROM user_profiles 
            WHERE user_id = auth.uid() AND role = 'admin'
        )
    );
```

### 4. Configurar Autenticación

1. Ve a **Authentication** > **Settings**
2. Configura **Site URL**: tu dominio de producción
3. En **Auth Providers**, asegúrate que **Email** esté habilitado
4. Opcional: Deshabilita **Enable email confirmations** para testing

## 👥 Crear Usuarios de Prueba

### 1. Crear Usuarios en Auth

1. Ve a **Authentication** > **Users**
2. Crea usuarios manualmente o usa el siguiente SQL:

```sql
-- Nota: Los usuarios deben crearse primero en la interfaz de Supabase Auth
-- Luego ejecutar estos INSERT para crear los perfiles

-- Administrador de prueba
INSERT INTO user_profiles (user_id, email, full_name, role, active)
VALUES (
    'REEMPLAZAR_CON_UUID_DEL_ADMIN',
    'admin@mexicodestination.com',
    'Administrador Principal',
    'admin',
    true
);

-- Room Boy de prueba
INSERT INTO user_profiles (user_id, email, full_name, role, active)
VALUES (
    'REEMPLAZAR_CON_UUID_DEL_ROOMBOY',
    'roomboy1@mexicodestination.com',
    'Juan Pérez',
    'roomboy',
    true
);
```

### 2. Obtener UUIDs de Usuarios

Para obtener los UUIDs después de crear usuarios:

```sql
-- Ver todos los usuarios creados
SELECT id, email FROM auth.users;
```

## 🔧 Configurar la Aplicación

### 1. Actualizar Credenciales

En cada archivo HTML (`index.html`, `admin.html`, `roomboy-app.html`), actualiza:

```javascript
const SUPABASE_URL = 'TU_URL_DE_SUPABASE';
const SUPABASE_ANON_KEY = 'TU_ANON_KEY_DE_SUPABASE';
```

### 2. Probar Localmente

```bash
# Clonar el repositorio
git clone https://github.com/mantrareal/roomboy-project-final.git
cd roomboy-project-final

# Iniciar servidor local
python3 -m http.server 8000

# Abrir en navegador
# http://localhost:8000
```

## 🌐 Deployment

### Opción 1: GitHub Pages

1. Sube el código a GitHub
2. Ve a **Settings** > **Pages**
3. Selecciona **Deploy from a branch**
4. Elige **main** branch
5. Tu app estará en `https://tuusuario.github.io/roomboy-project-final`

### Opción 2: Netlify

1. Conecta tu repositorio de GitHub
2. Deploy automático en cada push
3. Dominio personalizado disponible

### Opción 3: Vercel

1. Importa desde GitHub
2. Deploy automático
3. Excelente rendimiento

## ✅ Lista de Verificación

### Base de Datos
- [ ] Tablas creadas correctamente
- [ ] Políticas RLS configuradas
- [ ] Índices creados
- [ ] Storage bucket configurado

### Usuarios
- [ ] Usuario admin creado
- [ ] Usuario roomboy creado
- [ ] Perfiles insertados en user_profiles
- [ ] Credenciales de prueba funcionando

### Aplicación
- [ ] Credenciales de Supabase actualizadas
- [ ] Login funciona correctamente
- [ ] Redirección por roles funciona
- [ ] Subida de fotos funciona
- [ ] Dashboard admin funciona

### Testing
- [ ] Login como admin
- [ ] Login como roomboy
- [ ] Subir foto como roomboy
- [ ] Aprobar/rechazar como admin
- [ ] Filtros en galería funcionan

## 🐛 Solución de Problemas

### Error: "Invalid API key"
- Verifica que el SUPABASE_ANON_KEY sea correcto
- Asegúrate de no usar el service_role key

### Error: "Row Level Security"
- Verifica que las políticas RLS estén configuradas
- Comprueba que los usuarios tengan perfiles en user_profiles

### Fotos no se suben
- Verifica que el bucket 'task-photos' exista
- Comprueba que sea público
- Revisa las políticas de storage

### Gráficas no aparecen
- Verifica que Chart.js se cargue correctamente
- Comprueba que haya datos en task_photos
- Revisa la consola del navegador por errores

## 📞 Soporte

Si encuentras problemas:

1. Revisa la consola del navegador (F12)
2. Verifica los logs de Supabase
3. Comprueba que todas las tablas y políticas estén creadas
4. Crea un issue en GitHub con detalles del error

---

**¡Listo! Tu aplicación México Destination Club está configurada y lista para usar.**

