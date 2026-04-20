

# Plan — Finalisation MIPROJET (Modules restants + Compléments + Responsive)

## Vue d'ensemble

Implémentation complète de tout ce qui reste : Module 5 (sources de leads), Module 12 (journal maintenance), page `/journey` avec export PDF, corrections responsive mobile, et **toutes les corrections du document `Complément.docx`** (page d'accueil, footer, services, investisseurs, ressources, alignement entonnoir Projet → Évaluation → Structuration → Financement).

---

## 1. Page d'accueil & Footer (Complément §1)

**Navigation.tsx**
- Supprimer le texte « MIPROJET » à côté du logo (logo seul, cliquable, redirige vers `/`)
- Logo agrandi légèrement pour rester lisible sans le texte

**Footer.tsx**
- Email officiel : `infos@ivoireprojet.com` (vérifier) + ajouter numéro WhatsApp correct
- Réseaux sociaux corrigés :
  - Facebook → `https://www.facebook.com/mivprojet`
  - LinkedIn → `https://www.linkedin.com/in/marcelkonan/`
  - TikTok (nouvelle icône) → lien fourni
- Retirer Twitter/Instagram non utilisés

---

## 2. Module Projets (Complément §2)

**SubmitProject.tsx** — après soumission réussie :
- Toast + écran de succès :
  > « Votre projet a été enregistré avec succès. Passez maintenant à l'étape suivante : évaluer votre projet avec MIPROJET+ pour connaître son niveau de financement. »
- Bouton **« Évaluer mon projet (MIPROJET+) »** → redirige vers `/project-evaluation?projectId=...`

---

## 3. Comment ça marche (Complément §3)

**HowItWorks.tsx** + traductions — refonte 5 étapes alignées MIPROJET+ :
1. Soumettre son projet
2. Évaluation (score MIPROJET+)
3. Analyse & recommandations
4. Structuration
5. Mise en relation investisseurs

---

## 4. Module Services (Complément §4)

**Services.tsx**
- **Supprimer tous les prix affichés** (À partir de X FCFA)
- Remplacer par deux CTA : `Demander un devis` et `Être contacté`
- Après soumission d'un service → redirection vers évaluation MIPROJET+ avec message d'invitation

---

## 5. Page Investisseurs (Complément §5)

**Investors.tsx** — déplacer le bloc « Rejoindre notre réseau d'investisseurs » + bouton **S'inscrire** en haut de page (juste après le hero).

---

## 6. Module Ressources (Complément §6)

**Documents.tsx / Forum.tsx / SuccessStories.tsx**
- Vérifier que `AdminDocumentsManager` permet l'upload (déjà implémenté)
- Si Forum vide → masquer temporairement avec bandeau « Bientôt disponible »
- Page Témoignages → seed quelques témoignages par défaut depuis la BDD

---

## 7. Module 5 — Sources de leads (auto-tracking)

**Tracker automatique de la source dans `leads.lead_source`** :
| Action utilisateur            | `lead_source` enregistré |
|-------------------------------|--------------------------|
| Inscription (Auth.tsx)        | `signup`                 |
| Demande de service            | `service_request`        |
| Parrainage (lien `?ref=`)     | `referral`               |
| Inscription événement/webinar | `event`                  |
| Téléchargement ebook          | `ebook` (déjà OK)        |
| Formulaire de contact         | `contact`                |

**AdminLeadsManager.tsx** — nouveau panneau **« Répartition par source »** :
- Graphique camembert (recharts) avec compte par source
- Cartes KPI : total leads, % par canal, top source du mois

---

## 8. Module 12 — Journal de maintenance

**Nouveau composant `AdminMaintenanceManager.tsx`** dans Admin → Système :
- Switch ON/OFF maintenance (manuel uniquement)
- Modal de confirmation avec **raison obligatoire** avant activation/désactivation
- Tableau des entrées de `maintenance_log` (date, action ON/OFF, déclencheur, raison, source)
- Bandeau global rouge sur le site quand maintenance ON (lecture de `platform_settings.maintenance_mode`)

---

## 9. Module 10 — Page `/journey` dédiée

**Nouvelle route `/journey`** :
- Sidebar utilisateur : entrée « Mon parcours » (icône `Compass`)
- Layout 2 colonnes (desktop) / empilé (mobile) — parcours **Activités existantes** + **Startup** côte à côte
- Réutilise le composant `UserJourney`
- Bouton **« Exporter en PDF »** utilisant `jspdf` (génère un récap des étapes complétées + recommandations)

---

## 10. Responsive mobile (captures fournies)

Problèmes visibles sur 360px :
- Hero : titre déborde horizontalement (« Plateforme Panafricaine » coupé)
- Bouton « Soumettre » coupé à droite
- News ticker : texte tronqué hors viewport
- Bulle chat virtuelle masque la stat « 65+ »

**Corrections** :
- `Hero.tsx` : `text-3xl sm:text-4xl lg:text-6xl` au lieu de `text-5xl` direct, `break-words`, padding réduit en mobile, `overflow-x-hidden` sur le container
- News ticker : `truncate` strict, max 1 ligne en mobile
- Boutons CTA : `w-full sm:w-auto` + `whitespace-normal text-center` pour éviter coupure
- `StatsSection` : repositionner avec `grid-cols-2 gap-3` mobile, centrage texte
- `VirtualAssistant` : décaler la bulle (`bottom-20 right-4`) pour ne pas masquer le contenu, taille réduite mobile (`h-12 w-12`)
- Audit global : ajouter `overflow-x-hidden` au `body` dans `index.css`, vérifier toutes les sections marketing avec `min-w-0` sur les enfants flex

---

## 11. Build & qualité

- Vérifier qu'aucune référence aux fichiers/composants supprimés ne casse le build
- Nettoyer les imports inutilisés
- Vérifier les types TypeScript après mise à jour des props

---

## Détails techniques

**Migrations DB** : aucune nouvelle migration nécessaire (toutes les tables existent : `leads.lead_source`, `maintenance_log`, `user_journeys`, `platform_settings`).

**Nouvelles dépendances** : aucune (jspdf déjà présent, recharts déjà présent).

**Fichiers créés** :
- `src/pages/Journey.tsx`
- `src/components/admin/AdminMaintenanceManager.tsx`
- `src/components/MaintenanceBanner.tsx`
- `src/components/admin/LeadsSourceChart.tsx`
- `src/lib/leadTracking.ts` (helper centralisé `trackLead(source, data)`)

**Fichiers modifiés** :
- `src/components/Navigation.tsx` (logo seul)
- `src/components/Footer.tsx` (réseaux sociaux + contacts)
- `src/components/Hero.tsx` (responsive)
- `src/components/HowItWorks.tsx` + `src/i18n/translations.ts` (5 nouvelles étapes)
- `src/pages/Services.tsx` (supprimer prix, CTA devis)
- `src/pages/Investors.tsx` (CTA en haut)
- `src/pages/SubmitProject.tsx` (écran post-soumission)
- `src/pages/Auth.tsx` (tracker `signup` source)
- `src/components/services/EnterpriseForm.tsx` + `StructuringForm.tsx` (tracker `service_request` + redirection MP+)
- `src/components/admin/AdminLeadsManager.tsx` (graphique sources)
- `src/components/dashboard/DashboardLayout.tsx` (entrée sidebar Mon parcours)
- `src/components/admin/AdminSidebar.tsx` (entrée Maintenance dans Système)
- `src/pages/admin/AdminDashboard.tsx` (case `maintenance`)
- `src/App.tsx` (route `/journey` + `MaintenanceBanner` global)
- `src/components/VirtualAssistant.tsx` (position bulle mobile)
- `src/index.css` (overflow-x-hidden global)

**Test end-to-end** (à valider après implémentation) :
1. Inscription avec code parrainage → lead créé avec `lead_source=signup`
2. Onglet Parcours → étapes Startup cochables
3. Admin → Paiements → journal avec filtres
4. Admin → Utilisateurs → Suspendre/Activer
5. Admin → Demandes → boutons email/WhatsApp
6. Mobile 360px → aucun débordement horizontal
7. Page d'accueil → logo seul cliquable, footer corrigé, étapes alignées
8. Services → aucun prix visible, CTA devis
9. Maintenance ON → bandeau rouge global

