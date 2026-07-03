# TheMarketplace - Architecture avec Budget Flexible

## 📋 Vue d'ensemble

**TheMarketplace** est une application de marketplace complète inspirée de Leboncoin et Vinted. Cette documentation décrit l'architecture avec un **budget flexible** (50-100€/mois) pour une qualité premium et une scalabilité maximale.

---

## 🏗️ Architecture Globale Premium

```
┌─────────────────────────────────────────────────────────────────┐
│                         UTILISATEUR                              │
└─────────────────────────────────────────────────────────────────┘
                              ↓
        ┌──────────────────────��──────────────────┐
        │    FRONTEND (React + TypeScript)         │
        │    Hébergé sur Vercel Pro (20€/mois)   │
        │    CDN Global, Builds illimités         │
        └─────────────────────────────────────────┘
                              ↓
        ┌─────────────────────────────────────────┐
        │   API BACKEND (Node.js + Express)       │
        │  Hébergé sur AWS (15-25€/mois)         │
        │  Auto-scaling, Haute disponibilité      │
        └─────────────────────────────────────────┘
                              ↓
        ┌─────────────────────────────────────────┐
        │  BASE DE DONNÉES (PostgreSQL Premium)   │
        │  AWS RDS (20-30€/mois)                 │
        │  Backups automatiques, Haute disponibilité │
        └─────────────────────────────────────────┘
                              ↓
        ┌─────────────────────────────────────────┐
        │  STOCKAGE D'IMAGES (AWS S3)             │
        │  5-10€/mois (CDN CloudFront inclus)    │
        │  Illimité, très rapide                  │
        └─────────────────────────────────────────┘
                              ↓
        ┌─────────────────────────────────────────┐
        │  PAIEMENTS (Stripe Premium)             │
        │  2.9% + 0.30€ + support prioritaire     │
        │  Webhooks fiables, Monitoring           │
        └─────────────────────────────────────────┘
                              ↓
        ┌─────────────────────────────────────────┐
        │  EMAIL TRANSACTIONNEL (SendGrid Pro)    │
        │  10€/mois pour 40K emails/mois         │
        │  Délivrabilité garantie, Analytics      │
        └───���─────────────────────────────────────┘
                              ↓
        ┌─────────────────────────────────────────┐
        │  MONITORING & ANALYTICS                 │
        │  Sentry (Error tracking) : 10€/mois    │
        │  DataDog (Monitoring) : 10€/mois       │
        └─────────────────────────────────────────┘
```

---

## 🛠️ Stack Technologique Premium

### Frontend
- **Framework** : Next.js 14 (React 18 + TypeScript)
- **Build Tool** : Webpack (Next.js intégré)
- **Styling** : Tailwind CSS + Framer Motion (animations)
- **État Global** : Redux Toolkit + Redux Persist
- **HTTP Client** : TanStack Query (caching, sync avancé)
- **Forms** : React Hook Form + Zod
- **UI Components** : Shadcn/ui + Radix UI
- **Real-time** : Socket.io client (pour chat instantané)
- **Optimisations** : Image Optimization, Code Splitting auto
- **Hébergement** : Vercel Pro (20€/mois)

### Backend
- **Runtime** : Node.js LTS
- **Framework** : Express.js + Socket.io
- **Langage** : TypeScript stricte
- **Base de données** : PostgreSQL (AWS RDS)
- **ORM** : Prisma (migrations, type-safe)
- **Cache** : Redis (pour sessions, rate limiting)
- **Queue Jobs** : Bull (pour tâches asynchrones)
- **Authentification** : JWT + OAuth2 (Google, Facebook)
- **Validation** : Zod + Joi
- **Logging** : Winston + Morgan
- **API Documentation** : Swagger/OpenAPI
- **Tests** : Jest + Supertest
- **Hébergement** : AWS ECS (15-25€/mois)

### Stockage & Services Premium
- **Images & Assets** : AWS S3 + CloudFront CDN (5-10€/mois)
- **Paiements** : Stripe (avec tous les features)
- **Email** : SendGrid Pro (10€/mois)
- **Base de données** : AWS RDS PostgreSQL (20-30€/mois)
- **Cache/Sessions** : AWS ElastiCache Redis (5€/mois)
- **Error Tracking** : Sentry (10€/mois)
- **Monitoring** : DataDog (10€/mois)
- **Video (optionnel)** : AWS S3 + CloudFront pour uploads vidéo

---

## 💾 Structure de la Base de Données Premium

### Tables Principales

```sql
-- Utilisateurs
Users
├── id (UUID primary key)
├── email (unique, indexed)
├── password (bcrypt, 12 rounds)
├── firstName
├── lastName
├── profilePicture (S3 URL)
├── bio
├── phone (chiffré)
├── address (chiffré pour sécurité)
├── city
├── zipCode
├── country
├── rating (decimal 1-5)
├── totalReviews (int)
├── responseTime (moyenne en heures)
├── isVerified (email verified)
├── isBanned (soft delete)
├── accountType (basic/premium)
├── premiumUntil (nullable timestamp)
├── createdAt (indexed)
├── updatedAt
├── lastLoginAt
├── indexes: email, createdAt, city

-- Articles à vendre
Articles
├── id (UUID primary key)
├── userId (FK - Utilisateurs, indexed)
├── title (indexed)
├── description
├── category (enum, indexed)
├── subcategory (indexed)
├── price (decimal, indexed)
├── images[] (JSON array S3 URLs)
├── condition (enum: neuf/très_bon/bon/acceptable)
├── location
├── lat/lng (indexed pour géosearch)
├── status (enum: actif/vendu/archivé/suspendu)
├── views (int)
├── favorites_count (int)
├── seller_response_time (decimal)
├── tags[] (JSON array)
├── metadata (JSON: dimensions, poids, etc)
├── createdAt (indexed)
├── updatedAt
├── expiresAt (nullable - articles expirables)
├── indexes: userId, category, price, location, createdAt, status

-- Favoris
Favorites
├── id (UUID primary key)
├── userId (FK, indexed)
├── articleId (FK, indexed)
├── createdAt (indexed)
├── unique constraint: userId + articleId

-- Conversations (Chat)
Conversations
├── id (UUID primary key)
├── participant1Id (FK - Utilisateurs)
├── participant2Id (FK - Utilisateurs)
├── articleId (FK - Articles, nullable)
├── lastMessageAt (indexed)
├── createdAt
├── updatedAt

-- Messages/Chat
Messages
├── id (UUID primary key)
├── conversationId (FK, indexed)
├── senderId (FK - Utilisateurs, indexed)
├── content
├── isRead
├── readAt (nullable timestamp)
├── attachments[] (JSON array S3 URLs)
├── createdAt (indexed)

-- Commandes/Transactions
Orders
├── id (UUID primary key)
├── buyerId (FK - Utilisateurs, indexed)
├── sellerId (FK - Utilisateurs, indexed)
├── articleId (FK - Articles)
├── amount (decimal)
├── currency (enum: EUR)
├── status (enum: pending/paid/shipped/delivered/cancelled/refunded)
├── stripePaymentIntentId (unique, indexed)
├── shippingAddress (JSON)
├── trackingNumber (nullable)
├── estimatedDelivery (timestamp)
├── completedAt (nullable)
├── cancelledAt (nullable)
├── cancelReason (nullable)
├── createdAt (indexed)
├── updatedAt
├── indexes: buyerId, sellerId, status, createdAt

-- Avis/Évaluations
Reviews
├── id (UUID primary key)
├── orderId (FK - Commandes, unique)
├── fromUserId (FK - Utilisateurs, indexed)
├── toUserId (FK - Utilisateurs, indexed)
├── rating (int 1-5, indexed)
├── comment (text)
├── photos[] (JSON array S3 URLs)
├── tags[] (JSON: positive/negative traits)
├── helpful_count (int)
├── createdAt (indexed)
├── updatedAt

-- Catégories
Categories
├── id (UUID primary key)
├── name (unique)
├── slug (unique)
├── icon (S3 URL)
├── description
├── parent_id (FK nullable, for nested categories)
├── order (int)

-- Transactions/Wallet (Premium)
Transactions
├── id (UUID primary key)
├── userId (FK - Utilisateurs, indexed)
├── orderId (FK - Orders, indexed)
├── amount (decimal)
├── type (enum: sale/refund/fee/withdrawal)
├── status (enum: pending/completed/failed)
├── createdAt (indexed)

-- Plaintes/Reports (Modération)
Reports
├── id (UUID primary key)
├── reporterId (FK - Utilisateurs)
├── articleId (FK - Articles, nullable)
├── userId (FK - Utilisateurs, nullable)
├── reason (enum: fraud/illegal/inappropriate/etc)
├── description
├── evidence[] (JSON array S3 URLs)
├── status (enum: open/investigating/resolved/dismissed)
├── resolutionNotes (nullable)
├── createdAt
├── resolvedAt (nullable)

-- Analytics
PageViews
├── id (UUID primary key)
├── userId (FK nullable - visitor anonyme)
├── articleId (FK)
├── sessionId
├── referrer
├── timestamp (indexed)

-- Notifications
Notifications
├── id (UUID primary key)
├── userId (FK - Utilisateurs, indexed)
├── type (enum: message/purchase/review/etc)
├── relatedId (FK UUID)
├── content
├── isRead
├── readAt (nullable)
├── createdAt (indexed)
```

---

## 🔐 Fonctionnalités Clés Premium

### Pour les Utilisateurs

#### 1. **Authentification Avancée**
- Inscription/Connexion avec email
- OAuth2 (Google, Facebook)
- Authentification 2FA (TOTP)
- Vérification email
- Réinitialisation mot de passe sécurisée
- Sessions multiples avec révocation

#### 2. **Profil Premium**
- Profil utilisateur complet
- Photos de profil HD
- Badges de vérification
- Historique des transactions
- Statistiques de vente
- Temps de réponse moyen
- Système de compte premium

#### 3. **Vendre des Articles Pro**
- Créer annonce avec 10+ images
- Descriptions enrichies (HTML)
- Videos de produit
- Catégorisations multiples
- Tags et mots-clés
- Géolocalisation précise
- Promotion/mise en avant payante
- Annonces planifiées
- Édition/Duplication rapide

#### 4. **Acheter & Parcourir**
- Recherche textuelle avancée
- Filtres multiples et sauvegardables
- Recherche géographique (rayon)
- Notifications de prix
- Alertes d'articles similaires
- Historique de recherche
- Suggestions personnalisées (ML optionnel)

#### 5. **Paiement Sécurisé Premium**
- Paiement Stripe complet
- Plusieurs cartes de crédit
- Apple Pay / Google Pay
- Portefeuille/Wallet
- Factures détaillées
- Historique complet

#### 6. **Messagerie Temps Réel**
- Chat instantané (Socket.io)
- Notifications en temps réel
- Photos dans messages
- Indicateurs de frappe
- Historique complet
- Bloc utilisateurs
- Automatisation des réponses

#### 7. **Système d'Avis Avancé**
- Avis avec photos
- Tags (rapidité, qualité, communication)
- Avis certifiés achat
- Réponses aux avis
- Statistiques de vendeur
- Badge de vendeur de confiance

#### 8. **Livraison & Expédition**
- Intégration avec services de livraison
- Génération d'étiquettes
- Suivi du colis
- Assurance expédition
- Collecte à domicile

#### 9. **Modération & Sécurité**
- Système de signalements
- Modération manuelle
- Blocage d'utilisateurs
- Historique de transactions pour dispute
- Protection de l'acheteur
- Protection du vendeur

#### 10. **Analytics & Tableaux de Bord**
- Dashboard vendeur avec stats
- Graphiques de ventes
- ROI des annonces
- Taux de conversion
- Données de consulting

---

## 🌐 Points d'Accès API Complets

### Authentification & OAuth
```
POST   /api/v1/auth/register
POST   /api/v1/auth/login
POST   /api/v1/auth/logout
POST   /api/v1/auth/refresh-token
POST   /api/v1/auth/forgot-password
POST   /api/v1/auth/reset-password
POST   /api/v1/auth/enable-2fa
POST   /api/v1/auth/verify-2fa
GET    /api/v1/auth/oauth/google
GET    /api/v1/auth/oauth/facebook
POST   /api/v1/auth/oauth/callback/:provider
```

### Utilisateurs
```
GET    /api/v1/users/:id
PUT    /api/v1/users/:id
DELETE /api/v1/users/:id
GET    /api/v1/users/:id/profile
GET    /api/v1/users/:id/stats
GET    /api/v1/users/:id/reviews
GET    /api/v1/users/:id/articles
POST   /api/v1/users/:id/verify-email
POST   /api/v1/users/:id/upgrade-premium
```

### Articles
```
GET    /api/v1/articles (avec pagination, filtres, tri)
POST   /api/v1/articles
GET    /api/v1/articles/:id
PUT    /api/v1/articles/:id
DELETE /api/v1/articles/:id
POST   /api/v1/articles/:id/promote
GET    /api/v1/articles/search (recherche avancée)
GET    /api/v1/articles/trending
POST   /api/v1/articles/:id/views (tracking)
```

### Favoris
```
POST   /api/v1/favorites
DELETE /api/v1/favorites/:articleId
GET    /api/v1/favorites
POST   /api/v1/favorites/alerts (notifications)
```

### Messagerie Real-time
```
GET    /api/v1/conversations
GET    /api/v1/conversations/:id
POST   /api/v1/conversations/:id/messages
GET    /api/v1/conversations/:id/messages
PUT    /api/v1/messages/:id/mark-read
DELETE /api/v1/messages/:id
WS     /socket.io (Socket.io events)
```

### Commandes
```
POST   /api/v1/orders
GET    /api/v1/orders
GET    /api/v1/orders/:id
PUT    /api/v1/orders/:id/status
POST   /api/v1/orders/:id/cancel
POST   /api/v1/orders/:id/refund
GET    /api/v1/orders/:id/tracking
```

### Paiements Stripe
```
POST   /api/v1/payments/create-payment-intent
POST   /api/v1/payments/confirm-payment
POST   /api/v1/payments/webhook (Stripe webhook)
POST   /api/v1/payments/refund
GET    /api/v1/payments/history
```

### Avis
```
POST   /api/v1/reviews
GET    /api/v1/reviews/:userId
PUT    /api/v1/reviews/:id/respond
DELETE /api/v1/reviews/:id (admins only)
```

### Admin & Modération
```
GET    /api/v1/admin/reports
PUT    /api/v1/admin/reports/:id
POST   /api/v1/admin/users/:id/ban
GET    /api/v1/admin/articles
DELETE /api/v1/admin/articles/:id (suspension)
GET    /api/v1/admin/analytics
```

---

## 📊 Coûts Mensuels Détaillés

| Service | Coût | Notes |
|---------|------|-------|
| **Vercel Pro** (Frontend) | **20€** | Deployments illimités, CDN global |
| **AWS ECS** (Backend) | **15-25€** | Containers, auto-scaling |
| **AWS RDS PostgreSQL** | **20-30€** | Haute disponibilité, backups |
| **AWS S3 + CloudFront** | **5-10€** | Stockage illimité, CDN |
| **AWS ElastiCache Redis** | **5€** | Cache, sessions |
| **SendGrid Pro** | **10€** | 40K emails/mois, délivrabilité |
| **Stripe** | **Gratuit** | 2.9% + 0.30€ par transaction |
| **Sentry** | **10€** | Error tracking, alertes |
| **DataDog** | **10€** | Monitoring, analytics |
| **SSL Certificate** | **Gratuit** | AWS Certificate Manager |
| **Domain** | **5-10€** | Registrar (Namecheap, OVH) |
| **TOTAL** | **95-125€/mois** | |

---

## 🚀 Phases de Déploiement Premium

### Phase 1: Foundation (Semaines 1-3)
- Architecture backend robuste
- Base de données optimisée
- Authentification JWT + OAuth2
- Frontend Next.js setup
- CI/CD setup avec GitHub Actions

### Phase 2: Core Features (Semaines 4-8)
- Annonces (créer, lister, filtrer)
- Profil utilisateur complet
- Système de paiement Stripe
- Messagerie en temps réel (Socket.io)
- Notifications

### Phase 3: Features Secondaires (Semaines 9-12)
- Système d'avis complet
- Modération et rapports
- Dashboard vendeur
- Optimisations SEO
- Performance (caching, CDN)

### Phase 4: Polish & Scale (Semaines 13+)
- Tests automatisés complets
- Load testing
- Optimisations DB
- Features Premium
- Mobile app optionnelle

---

## 🔒 Sécurité Enterprise

✅ **Authentication**
- JWT avec rotation des tokens
- Oauth2 (Google, Facebook)
- 2FA (TOTP, SMS)
- Session management

✅ **Data Protection**
- Encryption au repos (AWS KMS)
- Encryption en transit (TLS 1.3)
- Chiffrement des données sensibles (phone, address)
- Database backups journaliers

✅ **API Security**
- Rate limiting (Redux Saga + Middleware)
- CORS configuration stricte
- CSRF protection
- Input validation stricte (Zod)
- SQL injection prevention (ORM)

✅ **Monitoring**
- Error tracking (Sentry)
- Log aggregation (DataDog)
- Security alerts
- Audit trail complet

✅ **Compliance**
- RGPD compliant
- Droit à l'oubli
- Export de données
- Privacy policy automatisée

---

## 📱 Progressive Web App (PWA)

- Installable sur mobile
- Offline mode de base
- Push notifications
- Native app-like experience
- Service Workers

---

## 🎯 Avantages du Budget Flexible

✅ Infrastructure enterprise-grade
✅ Scalabilité illimitée
✅ Performance optimale (CDN, caching)
✅ Haute disponibilité et fiabilité
✅ Real-time features complètes
✅ Excellent support des services
✅ Analytics détaillés
✅ Monitoring et alertes
✅ Compliance et sécurité maximales

---

## ⚡ Optimisations Incluses

⚡ Database indexing et query optimization
⚡ Frontend code splitting et lazy loading
⚡ Image optimization (WebP, multiple sizes)
⚡ Redis caching pour endpoints fréquents
⚡ CDN global pour assets
⚡ Database replicas pour scaling
⚡ Queue jobs pour tâches lourdes
⚡ Connection pooling

---

## Prochaines Étapes

1. ✅ Documentation Budget Moyen ✓
2. ✅ Documentation Budget Flexible ✓
3. ➡️ Je crée la **structure complète du projet**
4. ➡️ Je crée le **repository GitHub**
5. ➡️ Je pousse tout le code sur GitHub

Quel budget préférez-vous ? 🚀
