# Hechaspormi Frontend

Frontend e-commerce para Hechaspormi, construido con Next.js y conectado a un backend Spree/Rails.

## Tecnologias principales

- Next.js 16
- React 19
- TypeScript
- Tailwind CSS 4
- next-intl
- Spree SDK
- Stripe
- React Email
- Vitest
- Biome

## Requisitos

- Node.js compatible con Next.js 16
- npm
- Backend Spree/Rails disponible, por defecto en `http://localhost:3000`
- Una `SPREE_PUBLISHABLE_KEY` valida generada por el backend Spree

Este proyecto usa `package-lock.json`, por lo que el gestor recomendado es npm.

## Instalacion

```bash
npm install
```

## Variables de entorno

Crear el archivo local de entorno:

```bash
cp .env.example .env.local
```

Para desarrollo local, revisa al menos estas variables en `.env.local`:

```bash
SPREE_API_URL=http://localhost:3000
SPREE_PUBLISHABLE_KEY=tu_publishable_key_real_de_spree

NEXT_PUBLIC_DEFAULT_COUNTRY=us
NEXT_PUBLIC_DEFAULT_LOCALE=en
NEXT_PUBLIC_SITE_URL=http://localhost:3001
NEXT_PUBLIC_STORE_NAME=Hechaspormi
NEXT_PUBLIC_STORE_DESCRIPTION=E-commerce Hechaspormi
```

`SPREE_PUBLISHABLE_KEY` no puede quedar con el valor de ejemplo. Si se deja como placeholder, el backend respondera con:

```text
SpreeError: Valid API key required
code: invalid_token
status: 401
```

Stripe, Sentry, Google Tag Manager, Resend y webhooks de Spree son opcionales para levantar el proyecto en desarrollo. Configuralos solo si vas a probar pagos, monitoreo, analitica o emails transaccionales.

## Levantar en desarrollo

Primero asegúrate de que el backend Spree/Rails este corriendo. Por defecto el frontend espera encontrarlo en:

```text
http://localhost:3000
```

Luego levanta el frontend:

```bash
npm run dev
```

La aplicacion queda disponible en:

```text
http://localhost:3001
```

La ruta storefront por defecto usa pais y locale:

```text
http://localhost:3001/us/en
```

Si modificas `.env.local`, reinicia `npm run dev` para que Next.js vuelva a cargar las variables.

## Scripts disponibles

```bash
npm run dev            # Servidor de desarrollo en puerto 3001 con Turbopack
npm run build          # Build de produccion
npm run start          # Servidor de produccion en puerto 3001
npm run lint           # Lint con Biome
npm run format         # Formatea el proyecto con Biome
npm run check          # Checks de Biome
npm run test           # Tests con Vitest
npm run test:watch     # Tests en modo watch
npm run check:locales  # Verifica paridad de traducciones
npm run email:dev      # Preview de emails en puerto 4000
```

## Estructura del proyecto

```text
src/
├── app/          # App Router de Next.js
├── components/   # Componentes reutilizables
├── i18n/         # Configuracion de internacionalizacion
├── lib/          # Integraciones, datos, helpers y SDK Spree
└── middleware.ts # Middleware de pais/locale

messages/         # Archivos de traduccion
emails/           # Templates de React Email
public/           # Assets publicos
scripts/          # Scripts internos
```

## Backend y API

El frontend consume la API de Spree mediante `@spree/sdk`. La URL se define con:

```bash
SPREE_API_URL=http://localhost:3000
```

Las llamadas al catalogo, carrito, checkout, customer, ordenes y mercados requieren una publishable key valida:

```bash
SPREE_PUBLISHABLE_KEY=...
```

## Emails en desarrollo

Para previsualizar templates de email:

```bash
npm run email:dev
```

Esto levanta React Email en:

```text
http://localhost:4000
```

Para probar webhooks de Spree contra el frontend local, puedes exponer el puerto `3001` con `cloudflared` y configurar el webhook en el Admin de Spree apuntando a:

```text
https://<tu-tunnel>.trycloudflare.com/api/webhooks/spree
```

## Solucion de problemas

### `Spree client is not configured`

Falta `.env.local` o no estan definidas estas variables:

```bash
SPREE_API_URL
SPREE_PUBLISHABLE_KEY
```

Crea el archivo con:

```bash
cp .env.example .env.local
```

### `Valid API key required`

El frontend esta llegando al backend, pero Spree rechazo la key. Reemplaza `SPREE_PUBLISHABLE_KEY` por una publishable key real y reinicia el servidor de Next.js.

### Cambie `.env.local` y no se refleja

Deten el servidor y vuelve a ejecutar:

```bash
npm run dev
```

## Notas de desarrollo

- No subir `.env.local` ni secretos al repositorio.
- Mantener `package-lock.json` sincronizado usando npm.
- Usar `npm run check` y `npm run test` antes de abrir un PR cuando el cambio toque logica compartida.
