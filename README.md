# 💜 Matripuntos - Sistema Completo de Gamificación para Parejas

## 📋 Descripción General

Matripuntos es una aplicación web completa de gamificación para parejas que permite llevar un registro divertido y equilibrado de acciones, puntos y recompensas en la relación. El sistema incluye tanto la aplicación principal para usuarios como un panel de administración completo.

---

## 🎯 Características Principales

### Aplicación Principal (matripuntos-app.html)

#### ✨ Funcionalidades Core
- **Sistema de Usuarios Dual**: Dos usuarios (pareja) con perfiles independientes
- **Sistema de Puntos**: Gana puntos por acciones positivas
- **Catálogo de Acciones**: 13 acciones predefinidas en 4 categorías
- **Sistema de Recompensas**: 7 recompensas canjeables
- **Historial de Actividad**: Registro completo de todas las acciones
- **Sistema de Logros**: Achievements desbloqueables
- **Racha (Streak)**: Contador de días consecutivos activos
- **Balance Visual**: Indicadores de equilibrio en la relación

#### 🎮 Gamificación
- Animaciones de confeti al completar acciones
- Feedback visual inmediato
- Categorización por colores
- Iconos emoji expresivos
- Barras de progreso animadas
- Celebraciones visuales

#### 📱 Secciones de la App
1. **Inicio**: Dashboard con métricas, balance y acciones rápidas
2. **Acciones**: Catálogo completo con filtros por categoría
3. **Recompensas**: Lista de premios canjeables con progreso
4. **Perfil**: Estadísticas personales, logros y configuración

#### 💾 Persistencia de Datos
- Almacenamiento local (localStorage)
- Los datos persisten entre sesiones
- Función de reseteo de datos

---

### Panel de Administración (matripuntos-admin.html)

#### 📊 Dashboard General
- **KPIs en Tiempo Real**:
  - Usuarios activos
  - Parejas registradas
  - Acciones del día
  - Recompensas canjeadas
  - Ingresos
  - Racha promedio
- **Sistema de Alertas**: Críticas, advertencias e información
- **Gráficos de Crecimiento**: Visualización de usuarios en el tiempo

#### 👥 Gestión de Usuarios
- Tabla completa de usuarios
- Filtros por plan, estado y pareja
- Búsqueda por email/ID
- Vista detallada de cada usuario
- Métricas individuales
- Acciones administrativas:
  - Otorgar premium
  - Enviar emails
  - Ajustar puntos
  - Suspender/eliminar cuentas

#### 💑 Gestión de Parejas
- Vista de todas las parejas vinculadas
- Estado de vinculación
- Análisis de balance entre miembros
- Historial de parejas

#### 💰 Análisis de Ingresos
- MRR (Monthly Recurring Revenue)
- ARPU (Average Revenue Per User)
- LTV (Customer Lifetime Value)
- Desglose de ingresos:
  - Suscripciones (91%)
  - Compras in-app (9%)

#### 🎫 Sistema de Soporte
- Dashboard de tickets
- Priorización por criticidad
- Contador de tickets abiertos/resueltos
- Asignación de casos

#### ⚙️ Configuración
- Versiones de la app (iOS/Android)
- Gestión de precios
- Feature flags con A/B testing
- Control de funcionalidades

---

## 🛠️ Tecnologías Utilizadas

### Frontend
- **React 18**: Framework principal
- **Tailwind CSS**: Estilos y diseño responsive
- **Babel Standalone**: Compilación JSX en el navegador
- **Fonts**:
  - Urbanist (App principal)
  - Space Mono (Números monoespaciados)
  - Inter (Panel admin)
  - JetBrains Mono (Admin)

### Características Técnicas
- **Sin dependencias de servidor**: Todo funciona en el navegador
- **Almacenamiento local**: localStorage para persistencia
- **Responsive Design**: Adaptado a móviles y escritorio
- **Animaciones CSS**: Transiciones suaves y efectos visuales
- **Componentes modulares**: Código organizado y reutilizable

---

## 🚀 Instalación y Uso

### Requisitos Previos
- Navegador web moderno (Chrome, Firefox, Safari, Edge)
- No requiere instalación de dependencias
- No requiere servidor

### Pasos de Instalación

1. **Descargar los archivos**:
   - `matripuntos-app.html` (Aplicación principal)
   - `matripuntos-admin.html` (Panel de administración)

2. **Abrir la aplicación**:
   - Doble clic en `matripuntos-app.html`
   - Se abrirá directamente en el navegador

3. **Acceder al panel admin**:
   - Doble clic en `matripuntos-admin.html`
   - Panel completo de administración

### Primer Uso

#### En la Aplicación Principal:
1. **Seleccionar usuario**: Click en Juan o María
2. **Registrar acción**: Click en cualquier acción del catálogo
3. **Ver puntos**: Los puntos se actualizan instantáneamente
4. **Canjear recompensa**: En la sección "Premios"
5. **Ver perfil**: Estadísticas completas en la sección "Perfil"

#### En el Panel Admin:
1. Navega por las secciones usando el sidebar izquierdo
2. Explora métricas en el Dashboard
3. Gestiona usuarios y parejas
4. Analiza ingresos y tickets de soporte

---

## 📊 Estructura de Datos

### Usuario
```javascript
{
  id: number,
  name: string,
  email: string,
  avatar: emoji,
  points: number,
  streak: number,
  achievements: []
}
```

### Acción
```javascript
{
  id: number,
  name: string,
  points: number,
  category: string,
  icon: emoji,
  color: string (Tailwind class)
}
```

### Recompensa
```javascript
{
  id: number,
  name: string,
  cost: number,
  category: string,
  icon: emoji,
  color: string
}
```

### Entrada de Historial
```javascript
{
  id: timestamp,
  userId: number,
  userName: string,
  type: 'action' | 'reward',
  action: string,
  points: number,
  icon: emoji,
  timestamp: ISO string
}
```

---

## 🎨 Catálogo por Defecto

### Acciones (13 totales)

#### 🏠 Hogar (5 acciones)
- Lavar platos: +50 pts
- Cocinar cena: +100 pts
- Limpiar baño: +150 pts
- Lavar ropa: +60 pts
- Pasar aspiradora: +80 pts

#### 💑 Relación (3 acciones)
- Planear cita: +200 pts
- Dar masaje: +150 pts
- Escuchar activamente: +50 pts

#### 🎁 Detalles (3 acciones)
- Traer café: +30 pts
- Regalo sorpresa: +500 pts
- Flores: +300 pts

#### 🎭 Concesiones (2 acciones)
- Ver su serie: +100 pts
- Ir a su evento: +300 pts

### Recompensas (7 totales)

- 🎬 Elegir película: 300 pts
- 🎮 Noche de videojuegos: 500 pts
- 🍺 Salida con amigos: 800 pts
- 🛋️ Día libre de tareas: 1000 pts
- 🍕 Pizza night: 400 pts
- 💆 Masaje de pareja: 600 pts
- 🍽️ Cena fuera: 700 pts

---

## 🔧 Personalización

### Modificar Usuarios Iniciales
En `matripuntos-app.html`, líneas 130-152:
```javascript
const initialUser1 = {
    id: 1,
    name: 'TuNombre',
    email: 'tu@email.com',
    avatar: '👨', // Cambiar emoji
    points: 0, // Puntos iniciales
    streak: 0,
    achievements: []
};
```

### Agregar Nuevas Acciones
En `matripuntos-app.html`, array `defaultActions`:
```javascript
{
    id: 14, // Siguiente ID
    name: 'Nueva acción',
    points: 100,
    category: 'Hogar',
    icon: '🆕',
    color: 'bg-blue-500'
}
```

### Agregar Nuevas Recompensas
En `matripuntos-app.html`, array `defaultRewards`:
```javascript
{
    id: 8, // Siguiente ID
    name: 'Nueva recompensa',
    cost: 500,
    category: 'Entretenimiento',
    icon: '🎉',
    color: 'bg-red-500'
}
```

---

## 🎯 Sistema de Logros

### Logros Implementados
1. **Primera Acción** ⭐: Completa tu primera acción
2. **10 Acciones** 🏆: Completa 10 acciones

### Agregar Nuevos Logros
Modifica la función `checkAchievements()` en línea ~330:
```javascript
if (condition && !achievements.some(a => a.id === 'achievement_id')) {
    const newAchievement = {
        id: 'achievement_id',
        name: 'Nombre',
        icon: '🏆',
        description: 'Descripción',
        timestamp: new Date().toISOString()
    };
    setAchievements(prev => [...prev, newAchievement]);
}
```

---

## 📈 Métricas y Balance

### Indicadores de Balance
- **🟢 Equilibrado**: Diferencia ≤300 pts
- **🟡 Ligero desequilibrio**: 301-700 pts
- **🟠 Desequilibrio moderado**: 701-1200 pts
- **🔴 Desequilibrio severo**: >1200 pts

### Cálculo de Balance
```javascript
const balance = user1.points - user2.points;
const balancePercentage = (user1.points / (user1.points + user2.points)) * 100;
```

---

## 🎨 Diseño y UX

### Paleta de Colores

#### App Principal
- **Gradiente Principal**: `#667eea` → `#764ba2` (púrpura)
- **Categorías**:
  - Hogar: Azul (`bg-blue-500`)
  - Relación: Rosa (`bg-pink-500`)
  - Detalles: Ámbar (`bg-amber-500`)
  - Concesiones: Púrpura (`bg-purple-500`)
- **Recompensas**: Colores variados por categoría

#### Panel Admin
- **Fondo**: `#f8fafc` (gris claro)
- **Gradiente**: Mismo que app principal
- **Estados**:
  - Éxito: Verde
  - Advertencia: Amarillo
  - Error: Rojo
  - Info: Azul

### Animaciones
- **Float**: Elementos flotantes
- **Pulse**: Pulsación suave
- **Slide Up**: Entrada desde abajo
- **Confetti**: Celebración con partículas

---

## 🔐 Seguridad y Privacidad

### Almacenamiento Local
- Los datos se guardan solo en el navegador del usuario
- No hay transmisión de datos a servidores externos
- Cada navegador/dispositivo tiene sus propios datos

### Reseteo de Datos
- Función de reseteo disponible en Perfil
- Requiere confirmación del usuario
- Borra todo el historial y vuelve a valores iniciales

---

## 🚧 Limitaciones Actuales

1. **Sin Backend Real**: 
   - Los datos son locales por dispositivo
   - No hay sincronización entre dispositivos
   - El panel admin usa datos mock

2. **Sin Autenticación**:
   - No hay login/logout
   - Sistema de usuarios simplificado

3. **Sin Notificaciones Push**:
   - Las notificaciones son solo visuales dentro de la app

4. **Sin Pagos Reales**:
   - El sistema de monetización es simulado

---

## 🔮 Roadmap y Próximos Pasos

### Fase 1: MVP Funcional ✅ (Completado)
- [x] Interfaz principal
- [x] Sistema de puntos
- [x] Catálogo de acciones/recompensas
- [x] Panel de administración
- [x] Persistencia local

### Fase 2: Backend Real (Próximo)
- [ ] API REST con Node.js/Express
- [ ] Base de datos (PostgreSQL)
- [ ] Autenticación JWT
- [ ] Sincronización en tiempo real
- [ ] Sistema de notificaciones

### Fase 3: Features Avanzadas
- [ ] Chat de pareja integrado
- [ ] Calendario inteligente
- [ ] Estadísticas avanzadas
- [ ] Banco de ideas de la comunidad
- [ ] Rankings anónimos

### Fase 4: Monetización
- [ ] Integración con Stripe
- [ ] Sistema de suscripciones
- [ ] Compras in-app
- [ ] Programa de referidos

### Fase 5: Plataformas Nativas
- [ ] App iOS (React Native)
- [ ] App Android (React Native)
- [ ] Widgets móviles
- [ ] Integración con wearables

---

## 💡 Casos de Uso

### Escenario 1: Pareja Recién Conviviendo
**Problema**: Disputas sobre quién hace más tareas del hogar

**Solución con Matripuntos**:
1. Ambos registran sus acciones diariamente
2. El sistema muestra el balance objetivo
3. Se incentiva la colaboración con puntos
4. Se canjean recompensas juntos

### Escenario 2: Relación de Larga Distancia
**Problema**: Difícil mantener el engagement

**Solución con Matripuntos**:
1. Registrar detalles y gestos románticos
2. Acumular puntos para "Cita especial"
3. Sistema de logros motiva interacción
4. Chat integrado (futura feature)

### Escenario 3: Equilibrio Work-Life
**Problema**: Un miembro trabaja más horas

**Solución con Matripuntos**:
1. Ajustar valores de puntos según esfuerzo
2. Acciones de calidad de tiempo valen más
3. Balance muestra equidad real
4. Recompensas de descanso priorizadas

---

## 🛟 Soporte y Ayuda

### Problemas Comunes

#### Los datos desaparecieron
**Causa**: Se borró el localStorage del navegador
**Solución**: Los datos en localStorage no son permanentes. Para producción, se necesita backend.

#### La página no carga
**Causa**: JavaScript bloqueado o navegador antiguo
**Solución**: 
- Habilitar JavaScript
- Actualizar navegador
- Probar en Chrome/Firefox

#### Las animaciones van lentas
**Causa**: Dispositivo con recursos limitados
**Solución**: Las animaciones CSS son ligeras, pero dispositivos antiguos pueden tener lag.

---

## 👨‍💻 Desarrollo

### Estructura del Código

#### matripuntos-app.html
```
Lines 1-100:    HTML head y estilos
Lines 101-200:  Utilidades y datos iniciales
Lines 201-300:  Componentes React (Confetti, etc)
Lines 301-500:  Lógica principal de la app
Lines 501-700:  Renderizado de secciones
Lines 701-800:  Componentes UI (BottomNav, Modal)
Lines 801-850:  ReactDOM render
```

#### matripuntos-admin.html
```
Lines 1-100:    HTML head y estilos admin
Lines 101-200:  Datos mock del sistema
Lines 201-300:  Estado y configuración
Lines 301-600:  Secciones del admin (Dashboard, Users, etc)
Lines 601-700:  Componentes de navegación
Lines 701-750:  Renderizado principal
```

### Modificar Estilos

Los estilos están en `<style>` tags al inicio de cada archivo:
```css
/* Personaliza colores, fuentes, animaciones */
.gradient-bg {
    background: linear-gradient(135deg, #TuColor1, #TuColor2);
}
```

---

## 📄 Licencia

Este proyecto es un demo/prototipo creado con fines educativos y de demostración.

---

## 🙏 Créditos

- **Diseño y Desarrollo**: Claude (Anthropic)
- **Concepto**: Basado en el sistema informal de "matripuntos/matrimillas"
- **Inspiración**: Programas de puntos de aerolíneas aplicados a relaciones

---

## 📞 Contacto

Para preguntas, sugerencias o reportar bugs:
- GitHub: [tu-repo]
- Email: [tu-email]
- Twitter: [@tu-twitter]

---

## 🎉 ¡Gracias por usar Matripuntos!

Esperamos que esta herramienta ayude a tu relación a ser más equilibrada, divertida y colaborativa. 

**Recuerda**: El objetivo no es competir, sino **colaborar** y mantener un balance justo. ¡Diviértanse! 💜

---

**Versión**: 1.0.0  
**Última actualización**: Febrero 2026  
**Estado**: MVP Funcional ✅
