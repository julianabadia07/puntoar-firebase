# PuntoAr · Guía de configuración Firebase

## Estructura del proyecto

```
puntoar-plataforma/
├── firebase-config.js      ← Tu configuración Firebase va acá
├── admin/
│   └── index.html          ← Panel de administración (protegido)
└── public/
    └── index.html          ← Web pública para visitantes
```

---

## Paso 1 — Crear proyecto en Firebase

1. Ir a https://console.firebase.google.com
2. Clic en **"Agregar proyecto"**
3. Nombre: `puntoar` (o el que prefieras)
4. Desactivar Google Analytics (opcional)
5. Clic en **"Crear proyecto"**

---

## Paso 2 — Obtener las credenciales

1. En la consola del proyecto, clic en el ícono **`</>`** (Web app)
2. Nombre de la app: `PuntoAr Web`
3. **NO** activar Firebase Hosting por ahora
4. Copiar el objeto `firebaseConfig` que aparece

Debería verse así:
```javascript
const firebaseConfig = {
  apiKey: "AIzaSy...",
  authDomain: "puntoar-xxxxx.firebaseapp.com",
  projectId: "puntoar-xxxxx",
  storageBucket: "puntoar-xxxxx.appspot.com",
  messagingSenderId: "123456789",
  appId: "1:123456789:web:abc123"
};
```

5. Abrir el archivo `firebase-config.js` y reemplazar los valores de `FIREBASE_CONFIG`

---

## Paso 3 — Configurar Firebase Authentication

1. En la consola: **Authentication** → **Sign-in method**
2. Habilitar **"Email/contraseña"**
3. Ir a **Authentication** → **Users** → **Agregar usuario**
4. Email: `admin@puntoar.com.ar` (o el que prefieras)
5. Contraseña: elegí una segura

> ⚠️ Este email y contraseña son los que usás para entrar al panel admin.

---

## Paso 4 — Configurar Firestore Database

1. En la consola: **Firestore Database** → **Crear base de datos**
2. Modo: **"Iniciar en modo de producción"**
3. Ubicación: `nam5 (us-central)` o la más cercana a Argentina

### Reglas de seguridad Firestore

Ir a **Firestore** → **Reglas** y pegar:

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    // Web pública: solo lectura
    match /propiedades/{doc} {
      allow read: if true;
      allow write: if request.auth != null;
    }
  }
}
```

Clic en **"Publicar"**

---

## Paso 5 — Configurar Firebase Storage (para las fotos)

1. En la consola: **Storage** → **Comenzar**
2. Modo: **"Iniciar en modo de producción"**

### Reglas de Storage

Ir a **Storage** → **Reglas** y pegar:

```
rules_version = '2';
service firebase.storage {
  match /b/{bucket}/o {
    // Fotos de propiedades: lectura pública, escritura solo admin
    match /propiedades/{allPaths=**} {
      allow read: if true;
      allow write: if request.auth != null;
    }
  }
}
```

Clic en **"Publicar"**

---

## Paso 6 — Configurar el archivo firebase-config.js

Abrir `firebase-config.js` y actualizar:

```javascript
const FIREBASE_CONFIG = {
  apiKey:            "TU_API_KEY_REAL",
  authDomain:        "tu-proyecto.firebaseapp.com",
  projectId:         "tu-proyecto",
  storageBucket:     "tu-proyecto.appspot.com",
  messagingSenderId: "TU_SENDER_ID",
  appId:             "TU_APP_ID"
};

// Número de WhatsApp (sin + ni espacios)
const WHATSAPP_NUMBER = "5493512345678";

// Nombre empresa
const EMPRESA_NOMBRE = "PuntoAr Obras Civiles";
```

---

## Paso 7 — Correr localmente

```bash
# En la carpeta raíz del proyecto:
npm start
# → http://localhost:8080

# Web pública:    http://localhost:8080/public/
# Panel admin:    http://localhost:8080/admin/
```

Para verlo desde otro dispositivo en la misma red:
```
http://TU_IP_LOCAL:8080/public/
http://TU_IP_LOCAL:8080/admin/
```

Tu IP local: ejecutar `ipconfig` (Windows) o `ip a` (Linux/Mac)

---

## Uso del panel admin

1. Ir a `http://localhost:8080/admin/`
2. Ingresar con el email y contraseña que creaste en Firebase Auth
3. Crear propiedades con el botón **"+ Nueva propiedad"**
4. Las fotos se suben automáticamente a Firebase Storage
5. Los cambios aparecen **en tiempo real** en la web pública

---

## Para publicar en internet (opcional)

### Opción A — Firebase Hosting (gratis)
```bash
npm install -g firebase-tools
firebase login
firebase init hosting
firebase deploy
```

### Opción B — Netlify (gratis, muy simple)
1. Arrastrar la carpeta del proyecto a https://netlify.com/drop
2. Listo, te da una URL pública

### Opción C — Cualquier hosting estático
Subir todos los archivos a tu hosting habitual.

---

## Resumen de archivos importantes

| Archivo | Qué hace |
|---|---|
| `firebase-config.js` | Configuración Firebase (editá este) |
| `admin/index.html` | Panel admin completo |
| `public/index.html` | Web pública para visitantes |
| `package.json` | Para usar `npm start` |
