# Formation

Application Next.js avec App Router et TypeScript.

Supabase est configuré avec des clients dédiés au navigateur et au serveur. Les
variables de connexion locales se trouvent dans `.env.local`; le modèle à copier
pour une autre machine est `.env.example`. La clé utilisée est une clé publiable :
elle peut être exposée au navigateur et ne remplace pas les règles d’accès. Les
tables doivent appliquer des politiques RLS adaptées.

## Utiliser les clients Supabase

Dans un composant serveur, importez `createClient` depuis
`@/lib/supabase/server`. Dans un composant client (`"use client"`), importez-le
depuis `@/lib/supabase/client`. Exemple de lecture côté serveur, en remplaçant
`ma_table` par une table existante et autorisée par RLS :

```tsx
import { createClient } from "@/lib/supabase/server";

export default async function Page() {
  const supabase = await createClient();
  const { data, error } = await supabase.from("ma_table").select();

  if (error) return <p>Erreur Supabase : {error.message}</p>;
  return <pre>{JSON.stringify(data, null, 2)}</pre>;
}
```

Le fichier racine `proxy.ts` renouvelle les sessions Supabase sur les requêtes
de pages correspondantes (les ressources statiques courantes sont exclues par
son matcher). Il ne protège pas à lui seul une route ni une donnée :
vérifiez l’identité dans le code serveur et appliquez les règles RLS dans la base.
Supabase indique que `@supabase/ssr` est encore en bêta et que son API peut évoluer.

## Démarrer en développement

```bash
npm install
npm run dev
```

En mode `next dev`, Next.js recharge les variables quand `.env.local` change.
Pour `next start` ou un déploiement, redémarrez le processus (et reconstruisez
l’application si les variables ont changé au moment du build).

Ouvrez ensuite [http://localhost:3000](http://localhost:3000).

## Commandes disponibles

- `npm run dev` : démarre le serveur de développement.
- `npm run build` : prépare la version de production.
- `npm run start` : démarre la version compilée.
- `npm run lint` : analyse le code avec ESLint.
