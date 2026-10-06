# Guide de mise en ligne — Vercel, domaine et Cal.com

Ce guide explique comment mettre ce site en production depuis le dépôt GitHub `maelveee/mv-conseil`, le relier au domaine acheté sur Vercel et activer la prise de rendez-vous Cal.com.

## Ce qu’il faut avoir avant de commencer

- L’accès au compte Vercel qui possède le domaine.
- L’accès au dépôt GitHub `maelveee/mv-conseil`. Si le dépôt appartient à ton ami, il faut que son compte GitHub puisse le voir et que Vercel soit autorisé à l’utiliser.
- Un compte Cal.com au nom de la personne qui recevra les rendez-vous.
- L’adresse exacte du domaine acheté, par exemple `exemple.fr`.

Le site est une application React construite avec Vite. Les réglages du projet Vercel sont :

| Réglage | Valeur |
| --- | --- |
| Framework Preset | Vite |
| Root Directory | `.` (racine du dépôt) |
| Build Command | `npm run build` |
| Output Directory | `dist` |
| Install Command | laisser la valeur détectée automatiquement (`npm install`) |

## 1. Préparer le compte Cal.com

Fais ces réglages dans le compte de la personne qui aura les rendez-vous. Son compte Cal.com doit rester sous son contrôle : c’est lui qui recevra les invitations et les notifications.

### Connecter son calendrier

1. Ouvre [Cal.com](https://cal.com) et crée le compte ou connecte-toi.
2. Dans les réglages de calendrier / apps, connecte le calendrier utilisé au quotidien (Google Calendar, Outlook ou autre fournisseur disponible).
3. Active la vérification des conflits sur le calendrier où se trouvent les événements privés et professionnels. Choisis le calendrier où Cal.com doit ajouter les rendez-vous réservés.
4. Vérifie le fuseau horaire du profil. Pour un conseiller basé en France métropolitaine, sélectionne `Europe/Paris`.

La connexion du calendrier est importante : les événements déjà présents peuvent alors masquer les créneaux occupés et aider à éviter les doubles réservations.

### Définir ses disponibilités

1. Ouvre **Availability / Disponibilités** dans Cal.com.
2. Modifie l’emploi du temps par défaut ou crée un nouvel emploi du temps, par exemple `Rendez-vous clients`.
3. Coche les jours ouverts à la réservation et indique les plages horaires réelles. Ajoute deux plages dans une journée si tu veux fermer pendant la pause déjeuner.
4. Vérifie le fuseau horaire associé à cet emploi du temps, puis enregistre.
5. Prévois aussi les absences et jours non travaillés, ou vérifie que les événements concernés figurent bien dans un calendrier configuré comme indisponible.

Les disponibilités sont les heures pendant lesquelles l’événement peut être réservé. Les événements déjà présents sur les calendriers vérifiés et les limites de réservation peuvent encore retirer des créneaux.

### Créer le type de rendez-vous

1. Va dans **Event types / Types d’événement** et crée un événement personnel (rendez-vous individuel).
2. Choisis un titre clair, par exemple `Premier échange – 30 minutes`.
3. Rédige une description courte qui explique le but du rendez-vous et ce que le visiteur doit préparer. Évite de demander des informations confidentielles ou des données sensibles dans le formulaire de réservation.
4. Choisis la durée, par exemple 30 minutes, puis lie l’événement à l’emploi du temps créé ci-dessus.
5. Choisis le lieu : appel téléphonique, rendez-vous au cabinet ou visioconférence. Pour Google Meet, Zoom ou un autre outil, connecte d’abord l’application correspondante dans Cal.com. Cal Video peut aussi être proposé selon le compte.
6. Dans les limites, règle notamment le préavis minimum, l’horizon maximal de réservation et une marge avant/après les rendez-vous si nécessaire.
7. Vérifie les champs demandés au visiteur : nom et adresse e-mail suffisent souvent pour un premier échange. Enregistre et publie l’événement.
8. Ouvre la page publique de cet événement et copie son URL. Elle aura une forme semblable à `https://cal.com/nom-du-compte/premier-echange`.

Fais un essai sur cette page avant de configurer Vercel : vérifie le fuseau affiché, les créneaux, les confirmations e-mail, l’ajout au calendrier et le lieu du rendez-vous.

## 2. Importer le dépôt GitHub dans Vercel

1. Connecte-toi à [Vercel](https://vercel.com/dashboard) avec le compte qui possède le domaine.
2. Clique sur **Add New… → Project**.
3. Si Vercel ne propose pas le dépôt, connecte GitHub ou ajuste l’accès de l’intégration GitHub Vercel pour qu’elle puisse lire `maelveee/mv-conseil`.
4. Choisis le dépôt `maelveee/mv-conseil`, puis **Import**.
5. Donne un nom au projet, par exemple `mv-conseil`.
6. Vérifie les paramètres Vite du tableau ci-dessus. Le dépôt doit être utilisé depuis sa racine.
7. Configure la variable Cal.com de la section suivante avant le premier déploiement, puis clique sur **Deploy**.

Vercel associe le dépôt au projet : les futurs pushs sur la branche de production déclencheront automatiquement une nouvelle mise en ligne. Vérifie dans **Settings → Git** que la branche de production est `main`.

## 3. Configurer la variable d’environnement Cal.com

Cette app ne demande qu’une variable d’environnement pour l’agenda :

| Nom | Valeur | Secret ? |
| --- | --- | --- |
| `VITE_CALCOM_URL` | URL publique du type d’événement Cal.com, par exemple `https://cal.com/nom-du-compte/premier-echange` | Non |

Dans Vercel :

1. Ouvre le projet, puis **Settings → Environment Variables**.
2. Ajoute la clé `VITE_CALCOM_URL` (respecte exactement les majuscules et les underscores).
3. Comme valeur, colle le lien public Cal.com de l’événement, sans guillemets ni espace. N’utilise pas seulement `https://cal.com` : il faut le lien complet vers le type d’événement.
4. Coche au minimum **Production**. Coche aussi **Preview** si tu veux que les déploiements de test affichent le calendrier.
5. Enregistre, puis ouvre **Deployments** et redéploie le dernier déploiement de production. Une variable Vite est intégrée lors de la compilation : l’ajouter ne modifie pas le site déjà déployé tant qu’un nouveau build n’a pas été fait.

Le préfixe `VITE_` rend cette valeur disponible dans le JavaScript envoyé au navigateur. Ici, c’est simplement l’URL publique du calendrier. **Ne mets jamais de mot de passe, clé API ou secret dans une variable `VITE_`** : elle ne serait pas secrète.

Pour travailler localement, crée un fichier `.env.local` à la racine contenant par exemple :

```dotenv
VITE_CALCOM_URL=https://cal.com/nom-du-compte/premier-echange
```

Ne committe pas `.env.local` dans GitHub. Après avoir changé ce fichier, arrête puis relance le serveur local (`npm run dev`).

## 4. Attacher le domaine acheté sur Vercel

1. Dans le projet, ouvre **Settings → Domains**.
2. Clique **Add Domain** et saisis le domaine acheté, par exemple `exemple.fr`.
3. Ajoute également la variante `www.exemple.fr` si Vercel la propose ou si tu veux que les deux adresses fonctionnent.
4. Si le domaine a été acheté directement dans le même compte Vercel, il peut être relié sans modifier de DNS manuellement. Vérifie tout de même que Vercel affiche le domaine comme configuré.
5. Si Vercel demande une configuration, suis les valeurs exactes affichées à l’écran. Pour un domaine géré par un fournisseur externe, ajoute chez ce fournisseur l’enregistrement A pour le domaine racine et/ou CNAME pour `www` demandés par Vercel. N’invente pas de valeurs et ne supprime pas les enregistrements MX utilisés pour les e-mails.
6. Choisis quelle adresse sera principale (avec ou sans `www`) et configure l’autre en redirection si Vercel le propose.
7. Attends que le statut soit valide et que le certificat HTTPS soit actif. La propagation DNS peut prendre du temps.

Si le domaine est déjà attaché à un autre compte ou projet Vercel, Vercel demandera une vérification ou un transfert. Il faut alors agir depuis le compte qui possède le domaine ; ne retire pas de DNS liés aux e-mails sans savoir à quoi ils servent.

## 5. Vérifier le site en production

Après le déploiement, ouvre l’URL `vercel.app`, puis le domaine personnalisé, sur ordinateur et téléphone.

- La page d’accueil et les différentes sections s’affichent sans erreur.
- Le bouton de prise de rendez-vous ouvre la fenêtre et affiche le calendrier Cal.com, au lieu du message indiquant que l’agenda n’est pas configuré.
- Les jours et heures proposés correspondent au fuseau `Europe/Paris` et aux disponibilités choisies.
- Fais une réservation test avec une adresse de test, puis vérifie les e-mails, le calendrier du conseiller, l’heure et le lieu. Annule ensuite le rendez-vous test.
- Vérifie qu’un créneau réservé disparaît ou n’est plus proposé.
- Dans Vercel, **Deployments** doit montrer le dernier déploiement comme **Ready** et le domaine doit pointer vers la production.

## 6. Publier les changements par la suite

Une fois le dépôt connecté, le cycle est simple :

1. Modifier le site.
2. Envoyer les changements sur GitHub, branche `main`.
3. Attendre que Vercel termine le build automatique.
4. Ouvrir le domaine et vérifier la modification.

Si un build échoue, ouvre **Deployments**, sélectionne le déploiement en échec et lis les **Build Logs**. Vérifie en premier les erreurs TypeScript, les paramètres du projet et la présence de `VITE_CALCOM_URL` dans l’environnement voulu.

## Avant l’ouverture au public

Le dépôt indique que le formulaire de contact actuel est une démonstration et n’envoie pas les données. Il faut donc le tester et mettre en place un vrai canal de contact avant de compter dessus. Vérifie aussi les coordonnées, mentions légales, statut, numéro ORIAS si applicable, rémunération, médiateur, confidentialité et formulations relatives à Predictis avec les informations exactes du conseiller.

## Liens officiels utiles

- [Importer un dépôt Git sur Vercel](https://vercel.com/docs/git)
- [Déployer un site Vite sur Vercel](https://vercel.com/docs/frameworks/frontend/vite)
- [Variables d’environnement Vercel](https://vercel.com/docs/environment-variables)
- [Ajouter un domaine personnalisé](https://vercel.com/docs/domains/working-with-domains/add-a-domain)
- [Dépannage des domaines Vercel](https://vercel.com/docs/domains/troubleshooting)
- [Intégrer Cal.com à un site](https://cal.com/features/embed)
- [Disponibilités Cal.com](https://cal.com/blog/using-custom-availability-scheduling)
- [Créer et paramétrer les types d’événement Cal.com](https://cal.com/blog/event-types-guide-calcom)
- [Choisir le lieu d’un type d’événement Cal.com](https://cal.com/help/event-types/how-to-add-location)
