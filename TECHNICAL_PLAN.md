# 🚀 Plan Técnico de Implementación - Matripuntos

## Documento de Arquitectura y Desarrollo para Producción

---

## 📑 Tabla de Contenidos

1. [Arquitectura del Sistema](#arquitectura-del-sistema)
2. [Stack Tecnológico](#stack-tecnológico)
3. [Esquema de Base de Datos](#esquema-de-base-de-datos)
4. [API Endpoints](#api-endpoints)
5. [Autenticación y Seguridad](#autenticación-y-seguridad)
6. [Integración de Pagos](#integración-de-pagos)
7. [Notificaciones Push](#notificaciones-push)
8. [Plan de Desarrollo por Fases](#plan-de-desarrollo-por-fases)
9. [Infraestructura y Hosting](#infraestructura-y-hosting)
10. [Testing y QA](#testing-y-qa)
11. [Monitoreo y Analytics](#monitoreo-y-analytics)
12. [Costos Estimados](#costos-estimados)

---

## 1. Arquitectura del Sistema

### 1.1 Visión General

```
┌─────────────────────────────────────────────────────┐
│                   FRONTEND                          │
├─────────────────────────────────────────────────────┤
│  React Native App     │    Web Dashboard            │
│  (iOS + Android)      │    (Admin Panel)            │
└──────────┬────────────┴─────────────┬───────────────┘
           │                          │
           └──────────┬───────────────┘
                      │
           ┌──────────▼───────────┐
           │      API Gateway     │
           │    (Load Balancer)   │
           └──────────┬───────────┘
                      │
         ┌────────────┴────────────┐
         │                         │
    ┌────▼─────┐           ┌──────▼──────┐
    │   API    │           │  WebSocket  │
    │  Server  │◄──────────┤   Server    │
    │ (REST)   │           │ (Real-time) │
    └────┬─────┘           └──────┬──────┘
         │                        │
         └────────┬───────────────┘
                  │
    ┌─────────────▼──────────────┐
    │      PostgreSQL            │
    │    (Primary Database)      │
    └────────────────────────────┘
                  │
         ┌────────┴────────┐
         │                 │
    ┌────▼─────┐    ┌─────▼────┐
    │  Redis   │    │   S3     │
    │ (Cache)  │    │ (Files)  │
    └──────────┘    └──────────┘
```

### 1.2 Componentes Principales

#### Frontend
- **React Native Mobile App**: iOS + Android
- **React Admin Dashboard**: Panel de administración web
- **Landing Page**: Next.js para SEO

#### Backend
- **API Server**: Node.js + Express
- **WebSocket Server**: Socket.io para real-time
- **Background Jobs**: Bull + Redis para tareas asíncronas

#### Base de Datos
- **PostgreSQL**: Datos principales
- **Redis**: Cache + sessions + queues
- **S3/CloudStorage**: Imágenes y archivos

#### Servicios Externos
- **Stripe**: Pagos y suscripciones
- **Firebase**: Push notifications
- **SendGrid**: Emails transaccionales
- **Sentry**: Error tracking
- **Mixpanel**: Analytics

---

## 2. Stack Tecnológico

### 2.1 Frontend Mobile (React Native)

```json
{
  "dependencies": {
    "react-native": "^0.73.0",
    "react-navigation": "^6.0.0",
    "react-native-reanimated": "^3.0.0",
    "react-native-gesture-handler": "^2.14.0",
    "axios": "^1.6.0",
    "react-query": "^5.0.0",
    "zustand": "^4.4.0",
    "react-native-push-notification": "^8.1.0",
    "react-native-firebase": "^19.0.0",
    "react-native-linear-gradient": "^2.8.0",
    "lottie-react-native": "^6.4.0"
  }
}
```

**Librerías Clave**:
- **Navegación**: React Navigation
- **Estado Global**: Zustand (más ligero que Redux)
- **Networking**: Axios + React Query (caching automático)
- **Animaciones**: Reanimated + Lottie
- **Push Notifications**: Firebase Cloud Messaging

### 2.2 Frontend Admin (React Web)

```json
{
  "dependencies": {
    "react": "^18.2.0",
    "react-router-dom": "^6.20.0",
    "tailwindcss": "^3.3.0",
    "recharts": "^2.10.0",
    "react-table": "^8.0.0",
    "date-fns": "^3.0.0",
    "react-hook-form": "^7.48.0",
    "zod": "^3.22.0"
  }
}
```

### 2.3 Backend (Node.js)

```json
{
  "dependencies": {
    "express": "^4.18.0",
    "socket.io": "^4.6.0",
    "pg": "^8.11.0",
    "prisma": "^5.7.0",
    "jsonwebtoken": "^9.0.0",
    "bcrypt": "^5.1.0",
    "joi": "^17.11.0",
    "stripe": "^14.8.0",
    "bull": "^4.12.0",
    "ioredis": "^5.3.0",
    "aws-sdk": "^2.1500.0",
    "@sendgrid/mail": "^8.1.0",
    "helmet": "^7.1.0",
    "cors": "^2.8.5",
    "rate-limiter-flexible": "^4.0.0"
  }
}
```

---

## 3. Esquema de Base de Datos

### 3.1 Diagrama ER

```sql
-- USERS TABLE
CREATE TABLE users (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    email VARCHAR(255) UNIQUE NOT NULL,
    password_hash VARCHAR(255) NOT NULL,
    name VARCHAR(100) NOT NULL,
    avatar_url VARCHAR(500),
    phone VARCHAR(20),
    country_code VARCHAR(2),
    language VARCHAR(2) DEFAULT 'es',
    plan_type VARCHAR(20) DEFAULT 'free', -- free, premium
    plan_expires_at TIMESTAMP,
    stripe_customer_id VARCHAR(100),
    points INTEGER DEFAULT 0,
    streak_current INTEGER DEFAULT 0,
    streak_longest INTEGER DEFAULT 0,
    last_activity_date DATE,
    email_verified BOOLEAN DEFAULT false,
    is_active BOOLEAN DEFAULT true,
    created_at TIMESTAMP DEFAULT NOW(),
    updated_at TIMESTAMP DEFAULT NOW()
);

-- COUPLES TABLE
CREATE TABLE couples (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user1_id UUID REFERENCES users(id) ON DELETE CASCADE,
    user2_id UUID REFERENCES users(id) ON DELETE CASCADE,
    couple_name VARCHAR(100),
    invitation_code VARCHAR(10) UNIQUE,
    status VARCHAR(20) DEFAULT 'pending', -- pending, active, inactive
    linked_at TIMESTAMP,
    anniversary_date DATE,
    created_at TIMESTAMP DEFAULT NOW(),
    updated_at TIMESTAMP DEFAULT NOW(),
    UNIQUE(user1_id, user2_id)
);

-- ACTIONS TABLE (Catalog)
CREATE TABLE actions (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    name VARCHAR(100) NOT NULL,
    description TEXT,
    points INTEGER NOT NULL,
    category VARCHAR(50) NOT NULL,
    icon VARCHAR(10),
    color VARCHAR(50),
    is_default BOOLEAN DEFAULT false,
    is_community BOOLEAN DEFAULT false,
    usage_count INTEGER DEFAULT 0,
    created_by UUID REFERENCES users(id),
    created_at TIMESTAMP DEFAULT NOW(),
    updated_at TIMESTAMP DEFAULT NOW()
);

-- USER_ACTIONS TABLE (Custom per couple)
CREATE TABLE user_actions (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    couple_id UUID REFERENCES couples(id) ON DELETE CASCADE,
    action_id UUID REFERENCES actions(id),
    custom_name VARCHAR(100),
    custom_points INTEGER,
    is_active BOOLEAN DEFAULT true,
    created_at TIMESTAMP DEFAULT NOW()
);

-- REWARDS TABLE (Catalog)
CREATE TABLE rewards (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    name VARCHAR(100) NOT NULL,
    description TEXT,
    cost INTEGER NOT NULL,
    category VARCHAR(50) NOT NULL,
    icon VARCHAR(10),
    color VARCHAR(50),
    is_default BOOLEAN DEFAULT false,
    is_community BOOLEAN DEFAULT false,
    usage_count INTEGER DEFAULT 0,
    created_by UUID REFERENCES users(id),
    created_at TIMESTAMP DEFAULT NOW(),
    updated_at TIMESTAMP DEFAULT NOW()
);

-- USER_REWARDS TABLE (Custom per couple)
CREATE TABLE user_rewards (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    couple_id UUID REFERENCES couples(id) ON DELETE CASCADE,
    reward_id UUID REFERENCES rewards(id),
    custom_name VARCHAR(100),
    custom_cost INTEGER,
    is_active BOOLEAN DEFAULT true,
    created_at TIMESTAMP DEFAULT NOW()
);

-- HISTORY TABLE (All transactions)
CREATE TABLE history (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID REFERENCES users(id) ON DELETE CASCADE,
    couple_id UUID REFERENCES couples(id) ON DELETE CASCADE,
    type VARCHAR(20) NOT NULL, -- action, reward, adjustment
    action_id UUID REFERENCES actions(id),
    reward_id UUID REFERENCES rewards(id),
    points_change INTEGER NOT NULL,
    description TEXT,
    metadata JSONB,
    created_at TIMESTAMP DEFAULT NOW()
);

-- ACHIEVEMENTS TABLE (Catalog)
CREATE TABLE achievements (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    name VARCHAR(100) NOT NULL,
    description TEXT,
    icon VARCHAR(10),
    condition_type VARCHAR(50), -- action_count, streak_days, balance, etc
    condition_value INTEGER,
    reward_points INTEGER DEFAULT 0,
    created_at TIMESTAMP DEFAULT NOW()
);

-- USER_ACHIEVEMENTS TABLE (Unlocked achievements)
CREATE TABLE user_achievements (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID REFERENCES users(id) ON DELETE CASCADE,
    achievement_id UUID REFERENCES achievements(id),
    unlocked_at TIMESTAMP DEFAULT NOW(),
    UNIQUE(user_id, achievement_id)
);

-- NOTIFICATIONS TABLE
CREATE TABLE notifications (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID REFERENCES users(id) ON DELETE CASCADE,
    type VARCHAR(50) NOT NULL,
    title VARCHAR(200) NOT NULL,
    body TEXT NOT NULL,
    data JSONB,
    is_read BOOLEAN DEFAULT false,
    sent_at TIMESTAMP,
    read_at TIMESTAMP,
    created_at TIMESTAMP DEFAULT NOW()
);

-- MESSAGES TABLE (Couple chat)
CREATE TABLE messages (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    couple_id UUID REFERENCES couples(id) ON DELETE CASCADE,
    sender_id UUID REFERENCES users(id) ON DELETE CASCADE,
    content TEXT NOT NULL,
    message_type VARCHAR(20) DEFAULT 'text', -- text, image, action, reward
    metadata JSONB,
    is_read BOOLEAN DEFAULT false,
    created_at TIMESTAMP DEFAULT NOW()
);

-- DATES TABLE (Important dates)
CREATE TABLE important_dates (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    couple_id UUID REFERENCES couples(id) ON DELETE CASCADE,
    name VARCHAR(100) NOT NULL,
    date DATE NOT NULL,
    type VARCHAR(50), -- anniversary, birthday, custom
    reminder_days INTEGER DEFAULT 7,
    created_at TIMESTAMP DEFAULT NOW()
);

-- SUBSCRIPTIONS TABLE
CREATE TABLE subscriptions (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID REFERENCES users(id) ON DELETE CASCADE,
    stripe_subscription_id VARCHAR(100) UNIQUE,
    plan_type VARCHAR(20) NOT NULL,
    status VARCHAR(20) NOT NULL, -- active, canceled, past_due
    current_period_start TIMESTAMP,
    current_period_end TIMESTAMP,
    cancel_at TIMESTAMP,
    canceled_at TIMESTAMP,
    created_at TIMESTAMP DEFAULT NOW(),
    updated_at TIMESTAMP DEFAULT NOW()
);

-- TICKETS TABLE (Support)
CREATE TABLE support_tickets (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID REFERENCES users(id) ON DELETE SET NULL,
    subject VARCHAR(200) NOT NULL,
    description TEXT NOT NULL,
    status VARCHAR(20) DEFAULT 'open', -- open, in_progress, resolved, closed
    priority VARCHAR(20) DEFAULT 'normal', -- low, normal, high, critical
    assigned_to VARCHAR(100),
    resolved_at TIMESTAMP,
    created_at TIMESTAMP DEFAULT NOW(),
    updated_at TIMESTAMP DEFAULT NOW()
);

-- INDEXES
CREATE INDEX idx_users_email ON users(email);
CREATE INDEX idx_couples_user1 ON couples(user1_id);
CREATE INDEX idx_couples_user2 ON couples(user2_id);
CREATE INDEX idx_history_user ON history(user_id);
CREATE INDEX idx_history_couple ON history(couple_id);
CREATE INDEX idx_history_created ON history(created_at DESC);
CREATE INDEX idx_messages_couple ON messages(couple_id);
CREATE INDEX idx_notifications_user ON notifications(user_id);
CREATE INDEX idx_subscriptions_user ON subscriptions(user_id);
```

---

## 4. API Endpoints

### 4.1 Authentication

```
POST   /api/auth/register          - Registro de usuario
POST   /api/auth/login             - Login
POST   /api/auth/logout            - Logout
POST   /api/auth/refresh           - Refresh token
POST   /api/auth/forgot-password   - Solicitar reset
POST   /api/auth/reset-password    - Reset password
POST   /api/auth/verify-email      - Verificar email
```

### 4.2 Users

```
GET    /api/users/me               - Perfil del usuario
PUT    /api/users/me               - Actualizar perfil
GET    /api/users/:id              - Perfil de otro usuario (pareja)
PUT    /api/users/me/avatar        - Subir avatar
DELETE /api/users/me               - Eliminar cuenta
```

### 4.3 Couples

```
POST   /api/couples/invite         - Crear invitación
POST   /api/couples/join           - Unirse con código
GET    /api/couples/me             - Info de la pareja
PUT    /api/couples/me             - Actualizar info
DELETE /api/couples/me             - Desvincular
GET    /api/couples/balance        - Balance de puntos
```

### 4.4 Actions

```
GET    /api/actions                - Catálogo de acciones
GET    /api/actions/:id            - Detalle de acción
POST   /api/actions/custom         - Crear acción custom
PUT    /api/actions/custom/:id     - Editar acción custom
DELETE /api/actions/custom/:id     - Eliminar acción custom
POST   /api/actions/:id/execute    - Ejecutar acción (sumar puntos)
```

### 4.5 Rewards

```
GET    /api/rewards                - Catálogo de recompensas
GET    /api/rewards/:id            - Detalle de recompensa
POST   /api/rewards/custom         - Crear recompensa custom
PUT    /api/rewards/custom/:id     - Editar recompensa custom
DELETE /api/rewards/custom/:id     - Eliminar recompensa custom
POST   /api/rewards/:id/redeem     - Canjear recompensa
```

### 4.6 History

```
GET    /api/history                - Historial (paginado)
GET    /api/history/stats          - Estadísticas
DELETE /api/history/:id            - Eliminar entrada
```

### 4.7 Achievements

```
GET    /api/achievements           - Logros disponibles
GET    /api/achievements/me        - Mis logros
```

### 4.8 Notifications

```
GET    /api/notifications          - Mis notificaciones
PUT    /api/notifications/:id/read - Marcar como leída
POST   /api/notifications/register-device - Registrar device token
```

### 4.9 Messages (Chat)

```
GET    /api/messages               - Mensajes de pareja
POST   /api/messages               - Enviar mensaje
PUT    /api/messages/:id/read      - Marcar como leído
```

### 4.10 Subscriptions

```
GET    /api/subscriptions/plans    - Planes disponibles
POST   /api/subscriptions/create   - Crear suscripción
POST   /api/subscriptions/cancel   - Cancelar suscripción
GET    /api/subscriptions/me       - Mi suscripción
POST   /api/subscriptions/webhook  - Stripe webhook
```

### 4.11 Admin

```
GET    /api/admin/stats            - Estadísticas generales
GET    /api/admin/users            - Lista de usuarios
GET    /api/admin/couples          - Lista de parejas
GET    /api/admin/revenue          - Métricas de ingresos
GET    /api/admin/tickets          - Tickets de soporte
PUT    /api/admin/users/:id        - Actualizar usuario
POST   /api/admin/users/:id/grant-premium - Otorgar premium
```

---

## 5. Autenticación y Seguridad

### 5.1 JSON Web Tokens (JWT)

```javascript
// Token Structure
{
  "access_token": "eyJhbGciOiJIUzI1NiIs...", // Expires in 15 min
  "refresh_token": "eyJhbGciOiJIUzI1NiIs...", // Expires in 30 days
}

// Access Token Payload
{
  "userId": "uuid",
  "email": "user@example.com",
  "plan": "premium",
  "iat": 1234567890,
  "exp": 1234568790
}
```

### 5.2 Middleware de Autenticación

```javascript
// Express middleware
const authenticateJWT = async (req, res, next) => {
  const token = req.headers.authorization?.split(' ')[1];
  
  if (!token) {
    return res.status(401).json({ error: 'No token provided' });
  }
  
  try {
    const decoded = jwt.verify(token, process.env.JWT_SECRET);
    req.user = await User.findById(decoded.userId);
    next();
  } catch (error) {
    return res.status(403).json({ error: 'Invalid token' });
  }
};
```

### 5.3 Seguridad

**Headers de Seguridad (Helmet)**:
```javascript
app.use(helmet({
  contentSecurityPolicy: true,
  crossOriginEmbedderPolicy: true,
  crossOriginOpenerPolicy: true,
  crossOriginResourcePolicy: true,
}));
```

**Rate Limiting**:
```javascript
const rateLimiter = new RateLimiterRedis({
  storeClient: redisClient,
  points: 100, // Number of points
  duration: 60, // Per 60 seconds
});
```

**Validación de Input (Joi)**:
```javascript
const registerSchema = Joi.object({
  email: Joi.string().email().required(),
  password: Joi.string().min(8).required(),
  name: Joi.string().min(2).max(100).required(),
});
```

---

## 6. Integración de Pagos

### 6.1 Stripe Setup

```javascript
const stripe = require('stripe')(process.env.STRIPE_SECRET_KEY);

// Crear cliente Stripe
const customer = await stripe.customers.create({
  email: user.email,
  metadata: { userId: user.id }
});

// Crear suscripción
const subscription = await stripe.subscriptions.create({
  customer: customer.id,
  items: [{ price: 'price_premium_monthly' }],
  payment_behavior: 'default_incomplete',
  expand: ['latest_invoice.payment_intent'],
});
```

### 6.2 Planes de Suscripción

```javascript
const PLANS = {
  premium_monthly: {
    id: 'price_xxx',
    name: 'Premium Monthly',
    price: 4.99,
    interval: 'month',
    features: ['unlimited_actions', 'unlimited_rewards', 'no_ads', 'advanced_stats']
  },
  premium_yearly: {
    id: 'price_yyy',
    name: 'Premium Yearly',
    price: 39.99,
    interval: 'year',
    features: ['unlimited_actions', 'unlimited_rewards', 'no_ads', 'advanced_stats', 'priority_support']
  }
};
```

### 6.3 Webhooks

```javascript
app.post('/api/subscriptions/webhook', async (req, res) => {
  const sig = req.headers['stripe-signature'];
  
  let event;
  try {
    event = stripe.webhooks.constructEvent(
      req.body,
      sig,
      process.env.STRIPE_WEBHOOK_SECRET
    );
  } catch (err) {
    return res.status(400).send(`Webhook Error: ${err.message}`);
  }
  
  switch (event.type) {
    case 'customer.subscription.created':
      await handleSubscriptionCreated(event.data.object);
      break;
    case 'customer.subscription.updated':
      await handleSubscriptionUpdated(event.data.object);
      break;
    case 'customer.subscription.deleted':
      await handleSubscriptionCanceled(event.data.object);
      break;
    case 'invoice.payment_failed':
      await handlePaymentFailed(event.data.object);
      break;
  }
  
  res.json({ received: true });
});
```

---

## 7. Notificaciones Push

### 7.1 Firebase Cloud Messaging

```javascript
const admin = require('firebase-admin');

admin.initializeApp({
  credential: admin.credential.cert(serviceAccount)
});

// Enviar notificación
const message = {
  notification: {
    title: '¡Nueva actividad!',
    body: 'Tu pareja acaba de ganar 100 puntos',
  },
  data: {
    type: 'action_completed',
    userId: 'uuid',
    points: '100'
  },
  token: deviceToken
};

await admin.messaging().send(message);
```

### 7.2 Tipos de Notificaciones

```javascript
const NOTIFICATION_TYPES = {
  ACTION_COMPLETED: {
    title: (userName, action) => `${userName} completó: ${action}`,
    body: (points) => `+${points} puntos ganados`,
  },
  REWARD_REDEEMED: {
    title: (userName, reward) => `${userName} canjeó: ${reward}`,
    body: (cost) => `-${cost} puntos`,
  },
  STREAK_AT_RISK: {
    title: '¡Tu racha está en riesgo!',
    body: 'Registra una acción hoy para mantenerla',
  },
  BALANCE_ALERT: {
    title: 'Balance desnivelado',
    body: 'Hay una diferencia significativa de puntos',
  },
  ACHIEVEMENT_UNLOCKED: {
    title: '¡Nuevo logro desbloqueado!',
    body: (achievement) => `${achievement}`,
  }
};
```

---

## 8. Plan de Desarrollo por Fases

### FASE 1: Backend Core (4-6 semanas)

**Semana 1-2: Setup y Base de Datos**
- [ ] Configurar proyecto Node.js
- [ ] Configurar PostgreSQL con Prisma
- [ ] Crear esquema completo
- [ ] Seed inicial de datos

**Semana 3-4: API Core**
- [ ] Sistema de autenticación
- [ ] CRUD de usuarios
- [ ] CRUD de parejas
- [ ] Sistema de puntos base

**Semana 5-6: Features Principales**
- [ ] Acciones y recompensas
- [ ] Historial
- [ ] Logros
- [ ] Testing unitario

### FASE 2: Mobile App (6-8 semanas)

**Semana 1-2: Setup y Navegación**
- [ ] Setup React Native
- [ ] Navegación
- [ ] Autenticación UI
- [ ] Onboarding

**Semana 3-4: Pantallas Core**
- [ ] Dashboard
- [ ] Acciones
- [ ] Recompensas
- [ ] Perfil

**Semana 5-6: Features Avanzadas**
- [ ] Notificaciones push
- [ ] Animaciones
- [ ] Chat básico
- [ ] Compartir

**Semana 7-8: Polish y Testing**
- [ ] Testing QA
- [ ] Beta testing
- [ ] Optimizaciones
- [ ] App Store prep

### FASE 3: Admin Panel (3-4 semanas)

**Semana 1-2: Dashboard y Usuarios**
- [ ] Dashboard principal
- [ ] Gestión de usuarios
- [ ] Gestión de parejas
- [ ] Métricas básicas

**Semana 3-4: Features Avanzadas**
- [ ] Sistema de soporte
- [ ] Analytics completo
- [ ] Gestión de contenido
- [ ] Configuración

### FASE 4: Monetización (2-3 semanas)

**Semana 1-2: Stripe Integration**
- [ ] Integración Stripe
- [ ] Planes y precios
- [ ] Webhooks
- [ ] UI de suscripciones

**Semana 3: Testing**
- [ ] Testing de pagos (Sandbox)
- [ ] Pruebas end-to-end
- [ ] Documentación

### FASE 5: Launch (2-3 semanas)

**Semana 1: Pre-launch**
- [ ] Testing final
- [ ] Landing page
- [ ] Material de marketing
- [ ] Beta testers

**Semana 2: Soft Launch**
- [ ] Publicar en stores
- [ ] Monitoreo intensivo
- [ ] Primeros usuarios
- [ ] Feedback collection

**Semana 3: Growth**
- [ ] Marketing activo
- [ ] Iteración rápida
- [ ] Soporte usuarios
- [ ] Optimizaciones

---

## 9. Infraestructura y Hosting

### 9.1 Opción 1: AWS (Escalable)

```
┌─────────────────────────────────────┐
│      Route 53 (DNS)                 │
└─────────┬───────────────────────────┘
          │
┌─────────▼───────────────────────────┐
│  CloudFront (CDN)                   │
└─────────┬───────────────────────────┘
          │
┌─────────▼───────────────────────────┐
│  ALB (Application Load Balancer)    │
└──────┬──────────────────┬───────────┘
       │                  │
┌──────▼───────┐   ┌──────▼───────┐
│  ECS Task 1  │   │  ECS Task 2  │
│  (API Node)  │   │  (API Node)  │
└──────┬───────┘   └──────┬───────┘
       │                  │
       └────────┬─────────┘
                │
    ┌───────────▼──────────┐
    │  RDS PostgreSQL      │
    │  (Multi-AZ)          │
    └──────────────────────┘
    
    ┌──────────────────────┐
    │  ElastiCache Redis   │
    └──────────────────────┘
    
    ┌──────────────────────┐
    │  S3 (File Storage)   │
    └──────────────────────┘
```

**Costos AWS (estimado mensual)**:
- ECS Fargate (2 tareas): ~$50
- RDS PostgreSQL (t3.micro): ~$20
- ElastiCache Redis: ~$15
- S3: ~$5
- Route 53: ~$1
- CloudFront: ~$10
- **Total**: ~$100-150/mes

### 9.2 Opción 2: Digital Ocean (Más Económica)

```
┌─────────────────────────────────────┐
│      Cloudflare (CDN + DNS)         │
└─────────┬───────────────────────────┘
          │
┌─────────▼───────────────────────────┐
│  Load Balancer                      │
└──────┬──────────────────┬───────────┘
       │                  │
┌──────▼───────┐   ┌──────▼───────┐
│  Droplet 1   │   │  Droplet 2   │
│  (4GB RAM)   │   │  (4GB RAM)   │
└──────────────┘   └──────────────┘
       
┌──────────────────────────────────────┐
│  Managed PostgreSQL                  │
└──────────────────────────────────────┘

┌──────────────────────────────────────┐
│  Managed Redis                       │
└──────────────────────────────────────┘

┌──────────────────────────────────────┐
│  Spaces (S3-compatible)              │
└──────────────────────────────────────┘
```

**Costos Digital Ocean**:
- 2x Droplets (4GB): $48
- Managed PostgreSQL: $15
- Managed Redis: $15
- Spaces: $5
- Load Balancer: $12
- **Total**: ~$95/mes

### 9.3 Opción 3: Firebase (Rápido para MVP)

```
┌────────────────────────────────┐
│  Firebase Hosting              │
│  (Frontend)                    │
└────────────────────────────────┘

┌────────────────────────────────┐
│  Cloud Functions               │
│  (Backend Serverless)          │
└────────────────────────────────┘

┌────────────────────────────────┐
│  Firestore                     │
│  (Database NoSQL)              │
└────────────────────────────────┘

┌────────────────────────────────┐
│  Cloud Storage                 │
│  (Files)                       │
└────────────────────────────────┘

┌────────────────────────────────┐
│  Firebase Auth                 │
│  (Authentication)              │
└────────────────────────────────┘
```

**Costos Firebase (Blaze plan)**:
- Cloud Functions: ~$20-40
- Firestore: ~$10-20
- Storage: ~$5
- Hosting: ~$0-5
- **Total**: ~$35-70/mes (muy escalable, pago por uso)

---

## 10. Testing y QA

### 10.1 Testing Backend

```javascript
// Unit Tests (Jest)
describe('Actions Service', () => {
  test('should add points when action is executed', async () => {
    const result = await executeAction(userId, actionId);
    expect(result.newPoints).toBe(150);
  });
  
  test('should create history entry', async () => {
    await executeAction(userId, actionId);
    const history = await getHistory(userId);
    expect(history).toHaveLength(1);
  });
});

// Integration Tests
describe('API /api/actions/:id/execute', () => {
  test('should return 200 and update points', async () => {
    const response = await request(app)
      .post('/api/actions/123/execute')
      .set('Authorization', `Bearer ${token}`)
      .expect(200);
    
    expect(response.body.points).toBe(150);
  });
});
```

### 10.2 Testing Frontend

```javascript
// Component Tests (React Testing Library)
test('renders action button correctly', () => {
  render(<ActionButton action={mockAction} onPress={mockFn} />);
  expect(screen.getByText('Lavar platos')).toBeInTheDocument();
  expect(screen.getByText('+50')).toBeInTheDocument();
});

// E2E Tests (Detox)
describe('Complete action flow', () => {
  it('should add points when action is completed', async () => {
    await element(by.text('Lavar platos')).tap();
    await expect(element(by.text('150'))).toBeVisible();
  });
});
```

### 10.3 QA Checklist

**Funcional**:
- [ ] Registro y login
- [ ] Vinculación de pareja
- [ ] Ejecutar acciones
- [ ] Canjear recompensas
- [ ] Ver historial
- [ ] Notificaciones push
- [ ] Chat funciona
- [ ] Pagos Stripe

**Performance**:
- [ ] Carga inicial < 3s
- [ ] API response < 300ms
- [ ] Animaciones 60fps
- [ ] Imágenes optimizadas

**Seguridad**:
- [ ] Autenticación segura
- [ ] Tokens expiran
- [ ] Rate limiting activo
- [ ] Input validation
- [ ] SQL injection prevented

**UX**:
- [ ] Onboarding claro
- [ ] Feedback visual
- [ ] Estados de carga
- [ ] Mensajes de error
- [ ] Accesibilidad básica

---

## 11. Monitoreo y Analytics

### 11.1 Error Tracking (Sentry)

```javascript
import * as Sentry from '@sentry/node';

Sentry.init({
  dsn: process.env.SENTRY_DSN,
  environment: process.env.NODE_ENV,
  tracesSampleRate: 0.1,
});

// Capturar errores
try {
  await executeAction();
} catch (error) {
  Sentry.captureException(error, {
    tags: { action: 'execute_action' },
    user: { id: userId }
  });
}
```

### 11.2 Analytics (Mixpanel)

```javascript
import mixpanel from 'mixpanel';

const mp = mixpanel.init(process.env.MIXPANEL_TOKEN);

// Track events
mp.track('Action Completed', {
  distinct_id: userId,
  action_id: actionId,
  action_name: action.name,
  points: action.points,
  category: action.category
});

// User properties
mp.people.set(userId, {
  $email: user.email,
  $name: user.name,
  plan: user.plan,
  points: user.points,
  streak: user.streak
});
```

### 11.3 Métricas Clave (KPIs)

**Producto**:
- DAU / MAU (Daily/Monthly Active Users)
- Retention (D1, D7, D30)
- Session length
- Actions per user per day
- Rewards redemption rate

**Negocio**:
- MRR (Monthly Recurring Revenue)
- Churn rate
- LTV (Lifetime Value)
- CAC (Customer Acquisition Cost)
- Conversion rate (free → premium)

**Técnicas**:
- API response time
- Error rate
- Uptime
- Database query time
- Cache hit rate

---

## 12. Costos Estimados

### 12.1 Desarrollo Inicial

| Rol | Costo | Duración | Total |
|-----|-------|----------|-------|
| Backend Developer | $80/hora | 240 horas | $19,200 |
| Mobile Developer | $90/hora | 320 horas | $28,800 |
| UI/UX Designer | $70/hora | 80 horas | $5,600 |
| QA Tester | $50/hora | 80 horas | $4,000 |
| Project Manager | $75/hora | 100 horas | $7,500 |
| **TOTAL DESARROLLO** | | | **$65,100** |

### 12.2 Infraestructura Mensual

| Servicio | Costo |
|----------|-------|
| Hosting (Digital Ocean) | $95 |
| Firebase (Push) | $20 |
| Stripe (fee) | Variable (2.9% + $0.30) |
| SendGrid (emails) | $15 |
| Sentry (errors) | $26 |
| Mixpanel (analytics) | $25 |
| Domain + SSL | $2 |
| **TOTAL MENSUAL** | **~$183** |

### 12.3 Marketing Inicial

| Item | Costo |
|------|-------|
| Landing page | $2,000 |
| App Store assets | $800 |
| Ads iniciales (3 meses) | $9,000 |
| Influencers micro | $2,000 |
| PR y contenido | $3,000 |
| **TOTAL MARKETING** | **$16,800** |

### 12.4 Total Inversión Inicial

```
Desarrollo:        $65,100
Marketing:         $16,800
Infra (3 meses):      $549
Legal (LLC, etc):   $2,000
Reserva:            $5,551
─────────────────────────
TOTAL:             $90,000
```

### 12.5 Proyección de Ingresos (Año 1)

**Supuestos**:
- 10,000 usuarios en mes 12
- 5% conversion rate a premium
- $4.99/mes premium

**Mes 12**:
- 500 usuarios premium
- MRR: $2,495
- Ingresos anuales: ~$15,000

**Break-even**: Mes 18-24 (estimado)

---

## 📌 Conclusión

Este plan técnico proporciona una hoja de ruta completa para llevar Matripuntos de prototipo a producto de producción escalable. 

**Próximos pasos inmediatos**:
1. Validar MVP actual con usuarios
2. Definir presupuesto disponible
3. Contratar equipo o buscar co-founder técnico
4. Comenzar con Fase 1 (Backend)

**Recomendación**: Comenzar con Firebase para un MVP rápido, luego migrar a arquitectura más robusta cuando se alcancen 10K usuarios activos.

---

**Documento preparado por**: Claude  
**Fecha**: Febrero 2026  
**Versión**: 1.0
