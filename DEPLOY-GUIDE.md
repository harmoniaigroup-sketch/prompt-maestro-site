# GitHub Pages + DNS — Guía de Deploy

## Paso 1: Crear el repo en GitHub

1. Ve a https://github.com/new
2. Nombre del repo: `prompt-maestro-site` (o `prompt-maestro.com`, el que prefieras)
3. **Public** (GitHub Pages gratuito requiere repo público)
4. NO marques "Add README" ni nada — déjalo vacío
5. Click **Create repository**

## Paso 2: Subir los archivos

Desde tu terminal local:

```bash
cd ~/Desktop   # o donde tengas los archivos
git clone https://github.com/TU_USUARIO/prompt-maestro-site.git
cd prompt-maestro-site

# Copia estos 3 archivos al repo:
# - index.html (la landing)
# - CNAME
# - .nojekyll

git add .
git commit -m "Initial deploy: landing page v5"
git push origin main
```

**Alternativa rápida:** En GitHub, click "uploading an existing file" y arrastra los 3 archivos.

## Paso 3: Activar GitHub Pages

1. En el repo → **Settings** → **Pages** (menú lateral izquierdo)
2. Source: **Deploy from a branch**
3. Branch: **main** / **(root)**
4. Click **Save**
5. En "Custom domain": escribe `prompt-maestro.com` → Click **Save**
6. Espera ~1 min, luego marca **Enforce HTTPS** ✅

## Paso 4: Configurar DNS en Namecheap

Ve a Namecheap → Domain List → `prompt-maestro.com` → **Advanced DNS**

**IMPORTANTE:** Ya tienes records de Resend (DKIM, SPF, MX, DMARC). **NO los borres.** Solo agrega estos nuevos:

### Records a AGREGAR:

| Type  | Host | Value               | TTL       |
|-------|------|---------------------|-----------|
| A     | @    | 185.199.108.153     | Automatic |
| A     | @    | 185.199.109.153     | Automatic |
| A     | @    | 185.199.110.153     | Automatic |
| A     | @    | 185.199.111.153     | Automatic |
| CNAME | www  | TU_USUARIO.github.io| Automatic |

### Records que ya tienes (NO TOCAR):

| Type  | Host              | Value                                          |
|-------|-------------------|-------------------------------------------------|
| TXT   | resend._domainkey | (DKIM key de Resend)                            |
| TXT   | send              | (SPF para Resend)                               |
| MX    | send              | feedback-smtp.us-east-1.amazonses.com           |
| TXT   | _dmarc            | (DMARC policy)                                  |

### Records a ELIMINAR (si existen):

- Cualquier "parking page" record de Namecheap (URL redirect, etc.)
- Cualquier A record que apunte a una IP de Namecheap parking

## Paso 5: Verificar

- Espera 10-30 minutos para propagación DNS
- Prueba: https://prompt-maestro.com → debería cargar la landing
- Prueba: https://www.prompt-maestro.com → debería redirigir al anterior
- Verifica HTTPS (candado verde en el navegador)

### Verificar DNS con terminal:
```bash
dig prompt-maestro.com +short
# Debe mostrar las 4 IPs de GitHub: 185.199.108-111.153

dig www.prompt-maestro.com +short  
# Debe mostrar TU_USUARIO.github.io
```

## Troubleshooting

- **"DNS check unsuccessful"** en GitHub Pages → espera 30 min, los DNS tardan en propagar
- **No HTTPS** → necesita que el DNS esté propagado primero, luego marca "Enforce HTTPS"
- **404** → verifica que el archivo se llame `index.html` (no `Index.html`)
- **Landing no carga estilos** → verifica que `.nojekyll` esté en el repo
