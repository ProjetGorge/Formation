---
title: 'Intégration de Supabase'
type: 'feature'
created: '2026-11-11'
status: 'done'
route: 'oneshot'
review_loop_iteration: 0
context: []
---

<frozen-after-approval reason="human-owned intent — do not modify unless human renegotiates">

## Intent

**Problem:** Le projet Next.js n’a pas de client Supabase configuré, alors que le projet Supabase et sa clé publique sont fournis.

**Approach:** Installer les bibliothèques officielles Supabase pour Next.js, configurer les clients navigateur et serveur avec les variables locales fournies, et documenter la configuration sans ajouter de flux d’authentification ni dépendre d’un schéma de base de données non spécifié.

</frozen-after-approval>

## Implementation Notes

Utiliser `@supabase/ssr` et `@supabase/supabase-js` avec les variables `NEXT_PUBLIC_SUPABASE_URL` et `NEXT_PUBLIC_SUPABASE_PUBLISHABLE_KEY`. Ne jamais introduire une clé `service_role`. Protéger `.env.local` par `.gitignore` et fournir un `.env.example` sans identifiant actif. Garder les clients compatibles avec l’App Router et les API async de Next.js 16. Aucune requête de lecture ou écriture ne peut être prédéfinie sans nom de table ni politique RLS.

Paquets ajoutés : `@supabase/ssr` et `@supabase/supabase-js`. Fichiers créés : `.gitignore`, `.env.local`, `.env.example`, `lib/supabase/config.ts`, `lib/supabase/client.ts`, `lib/supabase/server.ts`, `lib/supabase/proxy.ts` et `proxy.ts`. Le proxy rafraîchit les sessions Supabase selon le nom de fichier requis par Next.js 16; il ne fournit pas de contrôle d’accès par route. L’URL est validée avant la création du client. Le README donne un exemple de lecture, précise les règles RLS, la bêta de `@supabase/ssr` et le rechargement des variables selon le mode d’exécution.

La revue a conduit à exclure davantage de ressources statiques du proxy, valider le schéma HTTP(S) de l’URL et compléter la documentation sur les clients, RLS, la portée du proxy et la stabilité de `@supabase/ssr`. Aucun écran d’authentification, accès à une table réelle ou test réseau n’a été ajouté, faute de demande et de schéma fournis.

## Review Triage Log

- low — Le proxy s’exécute sur les routes de page et appelle `getClaims`; conservé pour le renouvellement SSR conforme à Supabase, avec sa portée et ses limites documentées.
- medium — Les ressources statiques courantes n’étaient pas toutes exclues du matcher; exclusions ajoutées pour robots, sitemap, manifeste, polices et extensions d’images.
- false — Le `catch` serveur ignore les erreurs de modification des cookies; cette capture est requise dans les Server Components en lecture seule et suit le modèle documenté par Supabase.
- medium — Le README demandait un redémarrage à chaque modification de `.env.local`; il distingue maintenant le rechargement en développement du redémarrage/rebuild hors dev.
- medium — Le README ne montrait pas comment instancier les clients; un exemple serveur avec une table à remplacer a été ajouté.
- medium — Le README ne précisait pas les limites de la clé publique; il explique maintenant que les accès dépendent des politiques RLS.
- low — `@supabase/ssr` est encore en bêta selon Supabase; cette stabilité est indiquée dans le README.
- low — L’URL n’était vérifiée que par présence; une validation de format et de protocole HTTP(S) est maintenant faite.
- false — Aucune requête réseau n’a validé les identifiants; ce travail configure le client sans table ni opération fournie et ne prétend pas valider l’accès à la base.
- medium — Le proxy rafraîchissait les sessions sans préciser qu’il ne protège pas les routes ni les données; cette limite et la nécessité de contrôles d’identité/RLS sont maintenant documentées.
