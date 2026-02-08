<p align="center"><img src="./app/assets/images/SealCircle.png" width="150px" height="150px" alt="Zoo Launcher"></p>

<h1 align="center">Zoo Launcher</h1>

<p align="center">Rejoignez le serveur Zoo sans vous soucier d'installer Java, Forge, Fabric ou les mods. On s'occupe de tout.</p>

[<p align="center"><img src="https://img.shields.io/github/downloads/YoCsusu/Zoo/total.svg?style=for-the-badge" alt="downloads">](https://github.com/YoCsusu/Zoo/releases)</p>

## Fonctionnalites

* Full account management.
  * Add multiple accounts and easily switch between them.
  * Microsoft (OAuth 2.0) authentication fully supported.
* Efficient asset management.
  * Receive client updates as soon as we release them.
  * Files are validated before launch. Corrupt or incorrect files will be redownloaded.
* **Automatic Java validation.**
  * If you have an incompatible version of Java installed, we'll install the right one *for you*.
  * You do not need to have Java installed to run the launcher.
* Intuitive settings management, including a Java control panel.
* Automatic updates. The launcher updates itself.
* Detailed progress bar tracking every loading phase until the game is visible.

## Telechargement

Telechargez depuis [GitHub Releases](https://github.com/YoCsusu/Zoo/releases)

#### Derniere version

[![](https://img.shields.io/github/release/YoCsusu/Zoo.svg?style=flat-square)](https://github.com/YoCsusu/Zoo/releases/latest)

| Plateforme | Fichier |
| ---------- | ------- |
| Windows x64 | `Zoo-Launcher-setup-VERSION.exe` |

## Console

Pour ouvrir la console, utilisez le raccourci :

```
ctrl + shift + i
```

Ne collez rien dans la console sauf si vous savez exactement ce que cela fait.

## Developpement

**Pre-requis** : [Node.js][nodejs] v22

```bash
git clone https://github.com/YoCsusu/Zoo.git
cd Zoo
npm install
npm start
```

**Build**

| Plateforme | Commande |
| ---------- | -------- |
| Windows x64 | `npm run dist:win` |
| macOS | `npm run dist:mac` |
| Linux x64 | `npm run dist:linux` |

---

Base sur [HeliosLauncher](https://github.com/dscalzi/HeliosLauncher) par Daniel Scalzi.

[nodejs]: https://nodejs.org/en/ 'Node.js'
