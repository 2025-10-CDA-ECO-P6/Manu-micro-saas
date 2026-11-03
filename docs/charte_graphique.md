# MotoConnect — Charte graphique
**Licence Concepteur Développeur d'Application — 2025/2026**

---

## Principe général

MotoConnect supporte deux modes complets — dark et light — comme iOS natif. L'utilisateur choisit dans les réglages de l'app, et le système respecte automatiquement le mode système de l'iPhone via `useColorScheme()` de React Native.

Le rouge (#D02D1E) est l'unique accent de l'app — identique dans les deux modes. C'est l'ancre visuelle de MotoConnect.

---

## Palette de couleurs

### Tokens (dark / light)

| Token | Dark mode | Light mode | Usage |
|---|---|---|---|
| `bg-primary` | `#0F0A08` | `#F8F5F2` | Fond principal de tous les écrans |
| `bg-surface` | `#1A1210` | `#FFFFFF` | Cards, surfaces, modales |
| `bg-elevated` | `#241A16` | `#F0EBE7` | Inputs, overlays, menus |
| `accent` | `#D02D1E` | `#D02D1E` | CTA, état actif, badge LIVE |
| `accent-soft` | `rgba(208,45,30,.12)` | `rgba(208,45,30,.08)` | Fond des badges accent |
| `text-primary` | `#F0E0D0` | `#1A0E08` | Titres, corps principal |
| `text-secondary` | `#8A6A58` | `#7A5A48` | Méta, labels secondaires |
| `text-muted` | `#4A2E20` | `#C0A898` | Placeholders, états inactifs |
| `border` | `rgba(255,255,255,.06)` | `rgba(0,0,0,.08)` | Séparateurs, bordures cards |
| `success` | `#4CAF72` | `#2E9E5A` | Confirmations uniquement |

### Couleurs fixes (identiques dans les deux modes)

| Nom | Hex | Usage |
|---|---|---|
| Rouge accent | `#D02D1E` | CTA principal, actif, LIVE |
| Rouge doux | `rgba(208,45,30,.6)` | Badge AUJOURD'HUI |
| Vert succès dark | `#4CAF72` | Confirmé, validé (dark) |
| Vert succès light | `#2E9E5A` | Confirmé, validé (light) |

---

## Typographies

| Usage | Police | Poids | Taille |
|---|---|---|---|
| Grands titres (splash, hero) | Playfair Display | Bold 700 | 32–40pt |
| Titres d'écrans | SF Pro Display | Semibold 600 | 24–28pt |
| Titres de cards | SF Pro Display | Medium 500 | 16–18pt |
| Corps, descriptions | SF Pro Text | Regular 400 | 14–15pt |
| Méta, badges, labels | SF Pro Text | Medium 500 | 11–12pt |

**Playfair Display** — typo serif premium pour les grands titres (splash screen, titre hero). Donne le caractère distinctif de l'app.

**SF Pro** — typo système iOS pour tout le reste. Lisibilité parfaite, cohérence avec l'OS, zéro latence de rendu.

---

## Glassmorphism — règles d'usage

Le glassmorphism est utilisé **uniquement sur 3 éléments** pour rester épuré :

| Élément | Traitement |
|---|---|
| Badges sur photos (LIVE, style, distance) | `backdrop-filter: blur(10px)` + fond `rgba(15,10,8,.5)` |
| Bottom sheet de la carte | `backdrop-filter: blur(20px)` + fond `rgba(15,10,8,.75)` |
| Bouton retour sur détail sortie | Même traitement que les badges |

Partout ailleurs : couleurs solides. Pas de glassmorphism sur les cards du fil, les inputs, la tab bar.

---

## Composants

### Badge horizon
- **MAINTENANT / LIVE** — fond `accent-soft`, texte `accent`, préfixe `●`
- **AUJOURD'HUI** — fond `rgba(208,45,30,.08)`, texte `rgba(208,45,30,.6)`
- **PLANIFIÉ** — fond `rgba(255,255,255,.05)`, texte `text-secondary`

### CTA principal
- Fond : `accent` (#D02D1E)
- Texte : `text-primary` light (#F0E0D0 en dark, #FFFFFF en light)
- Border-radius : 14px
- Hauteur : 52pt
- Font : SF Pro Display Semibold 16pt

### CTA secondaire
- Fond : `bg-elevated`
- Texte : `text-secondary`
- Bordure : `border`
- Border-radius : 14px
- Hauteur : 52pt

### Card sortie (hero — pleine largeur)
- Photo pleine largeur avec overlay gradient `rgba(15,10,8,0)` → `rgba(15,10,8,.92)`
- Badges glassmorphism en haut de la photo
- Titre + organisateur en bas de la photo (sur l'overlay)
- Footer solid : fond `bg-surface`, tags + bouton rejoindre

### Card sortie (compact — liste)
- Photo à gauche (70×90px)
- Gradient de droite sur la photo pour la fondre dans `bg-surface`
- Infos à droite : nom, méta, tag + bouton voir

### Tab bar
- Fond : `bg-primary`
- Icône active : `accent` + dot indicator rouge
- Icône inactive : `text-muted`
- Bordure top : `border`
- Home indicator iOS natif

### Input
- Fond : `bg-elevated`
- Texte : `text-primary`
- Placeholder : `text-muted`
- Bordure : `border`
- Border-radius : 12px
- Hauteur : 47pt

### Avatar
- Border-radius : 10px (carré arrondi, pas circulaire)
- Fond fallback : `bg-elevated`
- Bordure subtile : `border`

---

## Implémentation NativeWind

### tailwind.config.js

```javascript
module.exports = {
  content: ['./app/**/*.{js,jsx}', './components/**/*.{js,jsx}'],
  presets: [require('nativewind/preset')],
  theme: {
    extend: {
      colors: {
        // Accent — identique dark/light
        accent: '#D02D1E',
        'accent-soft': 'rgba(208,45,30,0.12)',
        success: '#4CAF72',

        // Dark mode
        dark: {
          'bg-primary': '#0F0A08',
          'bg-surface': '#1A1210',
          'bg-elevated': '#241A16',
          'text-primary': '#F0E0D0',
          'text-secondary': '#8A6A58',
          'text-muted': '#4A2E20',
        },

        // Light mode
        light: {
          'bg-primary': '#F8F5F2',
          'bg-surface': '#FFFFFF',
          'bg-elevated': '#F0EBE7',
          'text-primary': '#1A0E08',
          'text-secondary': '#7A5A48',
          'text-muted': '#C0A898',
          success: '#2E9E5A',
        },
      },
    },
  },
}
```

### Détection du mode dans les composants

```javascript
import { useColorScheme } from 'react-native'

const theme = useColorScheme() === 'dark' ? 'dark' : 'light'

// Utilisation avec NativeWind
<View className="bg-white dark:bg-dark-bg-primary">
  <Text className="text-light-text-primary dark:text-dark-text-primary">
    Autour de toi
  </Text>
</View>
```

---

## Wireframes de référence

La charte est appliquée sur les wireframes Figma existants :
- Version dark : `onboarding-splash`, `home-feed`, `ride-detail`, `create-ride`, `rider-profile`, `messages-list`, `conversation-detail`, `notifications`
- Version light : mêmes écrans, palette inversée
- Les deux versions sont dans le fichier Figma MotoConnect POC — Wireframes

---