# claude-code-message-notifier

Notifications macOS pour Claude Code qui affichent **le contenu du dernier message** - pas juste "Task completed".

```
Claude Code
"Voici les fichiers modifiés : auth.ts, user.servic…"
```

Si le message se termine par une question, la notification est préfixée par "Attends ton input" - on sait au coup d'œil s'il faut revenir ou non.

## Pourquoi celui-là

Les notifieurs pour Claude Code sont nombreux. Ils signalent tous **qu'un événement a eu lieu** : tâche terminée, permission demandée, durée écoulée. Aucun de ceux que j'ai regardés ne lit le transcript pour dire **ce que Claude a répondu**.

C'est la seule chose que fait celui-ci : ouvrir le transcript de la session, extraire le dernier message de l'assistant, et l'afficher.

La différence à l'usage : on décide de revenir au terminal sans avoir à y revenir.

## Prérequis

- macOS (Apple Silicon)
- Claude Code - **CLI, extension VS Code ou n'importe quel terminal**, le hook `Stop` est le même partout
- Xcode Command Line Tools : `xcode-select --install`

## Installation

```bash
brew tap nicolasclaisse/tap
brew install claude-code-message-notifier
```

Puis ajouter le hook dans `~/.claude/settings.json` :

```json
{
  "hooks": {
    "Stop": [
      {
        "matcher": "",
        "hooks": [{ "type": "command", "command": "/opt/homebrew/opt/claude-code-message-notifier/bin/claude-notifier-hook" }]
      }
    ]
  }
}
```

## Comment ça marche

À chaque fin de réponse, le hook `Stop` reçoit le chemin du transcript sur son entrée standard, attend que le fichier soit écrit sur disque, puis en extrait le dernier message assistant.

Aucune dépendance externe - uniquement bash, sed et grep natifs macOS.

## Désinstallation

```bash
brew uninstall claude-code-message-notifier
```

Puis supprimer le bloc `Stop` dans `~/.claude/settings.json`.

## Renommage

Ce projet s'appelait `claude-code-vscode-notifier`. Le `vscode` venait du contexte d'origine et laissait croire à une dépendance à l'éditeur, alors que le hook fonctionne partout où tourne Claude Code.

Le tap déclare le renommage dans `formula_renames.json`. Pour migrer une installation existante :

```bash
brew update
brew migrate claude-code-vscode-notifier
```

Homebrew déplace le keg et relie le nouveau nom, en laissant l'ancien chemin `opt` en symlink - un hook qui pointe encore sur `/opt/homebrew/opt/claude-code-vscode-notifier/...` continue donc de fonctionner.

## Licence

MIT
