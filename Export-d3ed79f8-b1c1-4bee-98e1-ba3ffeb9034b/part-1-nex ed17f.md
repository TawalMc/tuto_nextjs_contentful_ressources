# part-1-nextjs-chakraui-contentful-blog

*Dans ce tutorial nous allons développer un blog de publication d’articles du début jusqu’à la fin en utilisant Next.js .*

## Encore un autre tutoriel sur Next.js et Contentful ?

Oui et non. Dans ce tutoriel en effet nous allons développer un blog de plublication d’articles. Mais contrairement à ce qui se fait courrament dans la création de blog en utilisant Next.js et Contenful qui est le rendu des pages en utilisant du `SSG (*Static Site Generation*)`, nous allons plutôt utiliser du `SSR (*Server Side Rendering*)`. Pourquoi ? Parce que:

- Avec du SSG, les pages sont générées lors du **`build`** donc chaque fois que vous créez un nouvel article sur votre dashboard contentful, vous devez **re-build** toute l’application et déployer ensuite. **C’est bon si c’est un blog de développeur.**
- Et si le blog darticles, c’était un médécin, un avocat ou un écrivain qui vous l’avait commandé ? Est ce que vous allez tout le temps refaire le processus de build et déployer chaque fois qu’il publie un article ? **Vous n’avez rien d’autre à faire?**
- OK, vous pouvez utiliser du CI/CD via GitHub Actions et connecter le compte contentful à ce dernier pour déclencher un build lors d’une nouvelle publication et ensuite un nouveau déploiement: **convenez avec moi, c’est long.**

Alors qu’avec du SSR, vous écrivez votre code **une fois** et chaque fois qu’un article est publié depuis contentful, il est listé directement (expliquer ultérieurement): pas de re-build, re-déploiement. Et donc vous pouvez livrer ce blog à n’importe qui voulant publier des post ou des articles. 

## Le travail à faire

### `Objectif`

Avec ce blog, l’utilisateur pourra se connecter à son compte contentful, créer un article complet (titre, description, corps de l’article, images), le publier sur son blog (automatiquement) et revenir l’éditer sans se soucier du code derrière (contentful).

### `Différentes parties`

Il sera constitué principalement de trois pages:

- une pour lister les articles disponibles
- une autre pour afficher le contenu d’un article
- et la dernière pour afficher les articles par tag ou mots clés

### `Prérequis et outils`

Nous supposons que le lecteur a une base en **JavaScript** et **React** et qu’il a les outils suivants installés :

- [Node.js](https://nodejs.org/en/) : 16.14.2 LTS
- [git](https://git-scm.com/)
- Un éditeur de code: VSCode ou autre

Nous allons utiliser les versions récentes de:

- [TypeScript](https://www.typescriptlang.org/) **:** Qui permet d’avoir de typer notre code JavaScript
- [Next.js](https://nextjs.org/) : Framework React très puissant permettant de faire du web fullstack
- [Chakra UI](https://chakra-ui.com/) : Une belle bibliothèque UI basée sur React avec des composants près à l’emploi
- [Contentful](https://www.contentful.com/) : Un CMS qui vous permet de gérer du contenu digital et fournit une API pour les devs

Dans cette première partie nous allons faire les installations et configurer notre projet, ensuite créer configurer notre dashboard contentful.

---

## On commence

### `Installations et configurations`

1. **Créez un projet Next.js**

Dans un dossier de travail, lancer la ligne de commande (git) et entrez la commande suivante :

```bash
npx create-next-app@latest --ts
```

Et suivez les instructions. J’ai dans mon cas choisi comme  nom de projet: `amazing-blog`

**NB :** le `--ts` est mis pour typescript donc nous allons écrire du code typé en javascript

Vous aurez un dossier avec la dernière version de Next.js installée avec du TypeScript par défaut, estlint intégré: C’est vraiment pratique.

**Un petit rappel sur la structure d’un projet Next.js**

> Je vous conseille de créer un dossier `src` dans l’arborescense directe de votre dossier `amazing-blog` et de déplacer tous les dossiers `styles` et `pages` à l’intérieur du dossier `src`. C’est une question d’organisation.
> 

![nextjs-folder-structure_with_src.PNG](part-1-nex%20ed17f/nextjs-folder-structure_with_src.png)

Ce qui nous interesse c’est le dossier src à l’intérieur duquel on a le dossier:

- **pages** :   chaque fichier `tsx`, `ts`, `js`, ou `jsx` que vous créez ici, est considéré par par Next.js comme une route, une page de votre application à l’exception des fichiers `_app` et `_document`

Le fichier `index.tsx` représente la page d’accueil ([voir mon article sur Next.js](https://tawaldevuniverse.vercel.app/blog/web/pourquoi-next.js-plut%C3%B4t-que-create-react-app))

- **api :** le code se trouvant dans les fichiers de ce sous-dossier sont exécutés côté serveur. On pourra y faire des requêtes d’API, traiter avant de les avoir côté client, vous pouvez aussi créer votre API directement là ([voir mon article sur Next.js](https://tawaldevuniverse.vercel.app/blog/web/pourquoi-next.js-plut%C3%B4t-que-create-react-app))

Pour voir si l’initialisation du projet est correcte tapez dans la ligne de commande:

```bash
cd amazing-blog 
npm run dev
```

Dans le navigateur, allez à l’adresse indiquée dans le terminal. Dans mon cas comme indiqué sur l’image suivante c’est `[http://localhost:3001](http://localhost:3001)` car le port 3000 est en cours d’utilisation

[localhost_3001.PNG](https://drive.google.com/file/d/1pvF6nw1pegEka2u9SYyQ1dBrPgp3ORRx/view?usp=drivesdk)

*So can you smell what TawalMc is cooking?*

Si tout est bon, vous verrez afficher la page d’accueil où ils parlent de Next.js (c’est évident).

[home_page_nextjs_project_creation.PNG](https://drive.google.com/file/d/1emMS065dayUHf_5sYJpNLB9NnAHzZkjR/view?usp=drivesdk)

Super

1. **Configurez Chakra UI**

[Chakra UI](https://chakra-ui.com/) est une bibliothèque UI puissante basée sur React et créée par [Segun Adebayo](https://twitter.com/thesegunadebayo), un dev nigérian. C’est une bibliothèques avec des composants prêts à l’emploi, qu’on peut facilement customiser aussi. 

Déjà:

- supprimez le fichier `Home.module.css` se trouvant dans le dossier `styles`: nous n’en avons pas besoin
- installez le package Chakra UI et ses dépendances comme suit

 

```bash
npm i @chakra-ui/react @emotion/react@^11 @emotion/styled@^11 framer-motion@^6
```

- Mettez à jour le fichier `pages/_app.tsx` en configurant le Provider fourni par Chakra UI comme suit:

```tsx
import { ChakraProvider } from "@chakra-ui/react" // Chakra UI Provider import
import type { AppProps } from "next/app"

import "../styles/globals.css"

function MyApp({ Component, pageProps }: AppProps) {
  return (
    <ChakraProvider> // Add here
      <Component {...pageProps} />
    </ChakraProvider> // add here
  )
}

export default MyApp
```

- Pour être sûr que Chakra UI a été bien configuré, remplacez le contenu du fichier `pages/index.tsx` par

```tsx
import { Text, VStack } from "@chakra-ui/react"
import type { NextPage } from "next"

const Home: NextPage = () => {
  return (
    <VStack
      w={"100vw"}
      h={"100vh"}
      justify={"center"}
      bgColor={"black"}
      color={"white"}
    >
      <Text>Amazing Blog</Text>
    </VStack>
  )
}

export default Home
```

Si vous avez un font noir avec le texte `Amazing Blog` centré en blanc alors chakra ui est bien configurée.

**NB** : Avant de passer à la configuration de contentful, nous allons configurer prettier rapidement pour un formatage joli du code source

- Installez les dépendances suivantes qui permettent d’ordonner les imports

```bash
npm install --save-dev @trivago/prettier-plugin-sort-imports
```

- Créez un fichier `.prettierrc.json` votre dossier de projet et collez le code suivant:

```json
{
  "printWidth": 80,
  "trailingComma": "none",
  "tabWidth": 2,
  "semi": false,
  "singleQuote": false,
  "importOrder": ["^@core/(.*)$", "^@server/(.*)$", "^@ui/(.*)$", "^[./]"],
  "importOrderSeparation": true,
  "importOrderSortSpecifiers": true
}
```

Si vous sauvegardez à nouveau vos fichiers, vous verrez que le formatage sera exécuté (si vous êtes sous VSCode configurez la `le formatage lors de la sauvegarde` dans vos settings).

1. **Configurez Contentful**