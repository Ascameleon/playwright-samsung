# Playwright + Samsung Android

État du dépôt : **documentation de démarrage uniquement**. Aucun test Playwright, APK ou test matériel n'est encore fourni.

## Ce que Playwright sait faire sur Android

Playwright dispose d'une prise en charge **expérimentale** de l'automatisation Android, notamment Chrome pour Android et Android WebView. Le téléphone Samsung est l'appareil cible ; le script Playwright doit s'exécuter dans un environnement Node.js qui peut joindre un serveur ADB authentifié.

Prérequis officiels :

- Un appareil Android ou un émulateur AVD.
- Un démon ADB en cours d'exécution et autorisé pour l'appareil (`adb devices` doit l'afficher comme `device`, pas `unauthorized`).
- Chrome 105 ou plus récent sur l'appareil.
- L'option **Enable command line on non-rooted devices** activée dans `chrome://flags`.
- Le téléphone doit rester éveillé pour les captures d'écran.
- Playwright ne prend pas en charge l'accès USB brut : ADB est nécessaire.

Référence officielle : https://playwright.dev/docs/api/class-android

## Pourquoi rien ne se passe parfois

Ouvrir une page web, installer l'application Playwright ou créer un dépôt Git ne connecte pas automatiquement le téléphone. Si aucun serveur ADB n'est accessible, `android.devices()` ne détectera aucun Samsung. Si l'appareil affiche `unauthorized`, il faut accepter la demande de débogage USB sur le téléphone.

Avec le téléphone seul, sans environnement Node.js + ADB accessible, ce dépôt ne peut pas piloter physiquement Chrome Samsung. Cette configuration « téléphone uniquement » n'est pas encore validée ici ; aucune réussite matérielle n'est revendiquée.

## Diagnostic à exécuter sur l'hôte ADB

1. Activer les options développeur et le débogage USB sur le Samsung.
2. Connecter le téléphone à l'hôte ADB, puis accepter l'empreinte RSA affichée sur le téléphone.
3. Exécuter :

```bash
adb devices -l
```

L'appareil doit apparaître avec l'état `device`. Ensuite, depuis le projet Node.js Playwright :

```js
const { _android: android } = require('playwright');

const devices = await android.devices();
if (devices.length === 0) {
  throw new Error('Aucun appareil Android autorisé par ADB');
}

for (const device of devices) {
  console.log({
    model: device.model(),
    serial: device.serial()
  });
}
```

Ce diagnostic ne lance pas de test applicatif et ne modifie pas le téléphone. Il confirme seulement que Playwright voit l'appareil.

## Limites et sécurité

- Le support Android Playwright est expérimental ; toutes les fonctionnalités ne sont pas garanties.
- Ne publiez jamais un endpoint ADB ou WebSocket Playwright sur Internet.
- Ne partagez pas le numéro de série ADB ni les journaux contenant des identifiants.
- Une capture d'écran ou un test desktop/mobile émulé ne prouve pas un test sur Samsung physique.

## Prochaine validation réelle

Le dépôt ne sera considéré opérationnel qu'après un test exact sur le Samsung : détection ADB, identification du modèle, lancement de Chrome, navigation vers une page de test, capture d'écran et fermeture propre de la session. Aucun de ces tests matériels n'a encore été exécuté.
