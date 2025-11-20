# 🔔 Configuration des Push Notifications avec EAS

## ⚠️ État actuel

Les notifications push sont **temporairement désactivées** car:
- Les notifications ne fonctionnent plus dans **Expo Go** depuis SDK 53
- Il faut un **development build** pour tester les notifications
- Il faut un **projectId EAS valide** (UUID)

## 📋 Prérequis

1. **Compte Expo** (gratuit): https://expo.dev/signup
2. **EAS CLI** installé: `npm install -g eas-cli`
3. **Compte Firebase** avec projet créé: `kmerservice-d178f`
4. **Fichiers Firebase téléchargés**:
   - `google-services.json` (Android)
   - `GoogleService-Info.plist` (iOS)

## 🚀 Étapes pour activer les notifications

### 1. Se connecter à Expo

```bash
cd mobile
eas login
```

Entrez vos identifiants Expo (email + mot de passe).

### 2. Initialiser le projet EAS

```bash
eas init
```

Cette commande va:
- Créer un nouveau projet EAS
- Générer un UUID valide pour le projectId
- Mettre à jour `app.json` et `app.config.js` automatiquement

### 3. Vérifier le projectId généré

Après `eas init`, vérifiez que le projectId a été ajouté:

```bash
cat app.config.js | grep projectId
```

Vous devriez voir quelque chose comme:
```js
projectId: 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'
```

### 4. Réactiver le code des notifications

Dans `src/hooks/useNotifications.ts`, **décommenter** le code désactivé:

```typescript
// Lignes 35-66: Retirer les /* */ pour décommenter
```

### 5. Créer un development build

**Pour Android:**
```bash
eas build --profile development --platform android
```

**Pour iOS:**
```bash
eas build --profile development --platform ios
```

Cette étape prend **15-30 minutes**. EAS va:
- Compiler l'application native
- Intégrer Firebase Cloud Messaging
- Créer un fichier `.apk` (Android) ou `.ipa` (iOS)

### 6. Installer le development build

Une fois le build terminé, EAS vous donnera un lien de téléchargement.

**Sur Android:**
1. Téléchargez le fichier `.apk` sur votre téléphone
2. Installez-le (autorisez les sources inconnues si nécessaire)

**Sur iOS:**
1. Téléchargez via TestFlight
2. Ou installez directement via le lien EAS

### 7. Configurer Supabase

Suivez le guide `PUSH_NOTIFICATIONS_SETUP.md` pour:
1. Créer la migration SQL (colonne `fcm_token`)
2. Créer les triggers PostgreSQL
3. Déployer l'Edge Function `send-push-notification`

## 🧪 Tester les notifications

Une fois tout configuré:

1. **Lancez le development build** sur votre téléphone
2. **Connectez-vous** à votre compte
3. **Vérifiez les logs**:
   ```
   ✅ Permission de notification accordée
   🔍 ProjectId détecté: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
   📱 Expo Push Token: ExponentPushToken[...]
   ✅ Token FCM enregistré avec succès
   ```

4. **Créez une commande** pour déclencher une notification
5. **Vérifiez** que la notification s'affiche

## 📱 Test sans build (pour développement)

Si vous voulez juste tester le reste de l'app (bookings, reviews) **sans notifications**:

```bash
npx expo start
```

Les notifications resteront désactivées mais l'app fonctionnera normalement.

## 🔧 Alternative: Development client local

Au lieu de EAS Build (cloud), vous pouvez créer un development build localement:

**Android (nécessite Android Studio):**
```bash
npx expo run:android
```

**iOS (nécessite Xcode + Mac):**
```bash
npx expo run:ios
```

Cette méthode est plus rapide mais nécessite l'environnement de développement natif.

## 📚 Ressources

- [Expo Push Notifications](https://docs.expo.dev/push-notifications/overview/)
- [EAS Build](https://docs.expo.dev/build/introduction/)
- [Development Builds](https://docs.expo.dev/develop/development-builds/introduction/)
- [Firebase Cloud Messaging](https://firebase.google.com/docs/cloud-messaging)

## ⚡ Résumé rapide

```bash
# 1. Connexion
eas login

# 2. Init projet
eas init

# 3. Décommenter le code dans useNotifications.ts

# 4. Build
eas build --profile development --platform android

# 5. Installer l'APK sur votre téléphone

# 6. Configurer Supabase (SQL + Edge Function)

# 7. Tester!
```

## 💡 Note importante

**Les notifications ne fonctionneront JAMAIS dans Expo Go** (limitation depuis SDK 53).
Vous DEVEZ utiliser un development build pour tester les notifications.
