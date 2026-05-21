# Réponses aux questions ouvertes — DevOpsGPT

---

## Exercice 1 : Conception Logicielle

### 1. Diagramme de Contexte

```
┌───────────────────────────────────────────────────────────┐
│                    Système DevOpsGPT                      │
│                                                           │
│  ┌──────────┐   question   ┌──────────────┐              │
│  │          │ ──────────→  │              │              │
│  │Utilisateur│              │   Backend    │              │
│  │          │ ←──────────  │  (Express)   │              │
│  └──────────┘   réponse    │              │              │
│                            └──────┬───────┘              │
│                                   │  sauvegarde          │
│                                   ↓                      │
│                          ┌────────────────┐              │
│                          │  Base de       │              │
│                          │  données       │              │
│                          └────────────────┘              │
│                                   │  envoi message       │
│                                   ↓                      │
│                          ┌────────────────┐              │
│                          │   API GPT-4    │              │
│                          │  (OpenAI)      │              │
│                          └────────────────┘              │
└───────────────────────────────────────────────────────────┘
```

**Acteurs externes :**
- **Utilisateur** : envoie des questions DevOps via l'interface web, reçoit les réponses générées par l'IA.
- **API GPT-4 (OpenAI)** : reçoit le message de l'utilisateur et retourne une réponse générée.
- **Base de données** : stocke les historiques de conversation pour consultation future.

---

### 2. Organigramme / Flowchart

```
          ┌───────────────────────────┐
          │  Réception du message     │
          │     de l'utilisateur      │
          └─────────────┬─────────────┘
                        │
                        ▼
          ┌─────────────────────────────┐
          │  Le message contient-il     │
          │       des insultes ?        │
          └──────┬──────────────┬───────┘
                 │ OUI          │ NON
                 ▼              ▼
         ┌──────────────┐  ┌──────────────────────┐
         │  Rejeter le  │  │  Envoi du message     │
         │  message +   │  │     à l'API GPT-4     │
         │  message     │  └──────────┬────────────┘
         │  d'erreur    │             │
         └──────────────┘             ▼
                             ┌──────────────────────┐
                             │  Réception de la     │
                             │  réponse de l'API    │
                             └──────────┬───────────┘
                                        │
                                        ▼
                             ┌──────────────────────┐
                             │  Sauvegarde de la    │
                             │  réponse en base     │
                             │  de données          │
                             └──────────┬───────────┘
                                        │
                                        ▼
                             ┌──────────────────────┐
                             │  Affichage de la     │
                             │  réponse à           │
                             │  l'utilisateur       │
                             └──────────────────────┘
```

---

### 3. Dictionnaire de données — entité `Message`

| Nom de la donnée   | Type           | Description                                           |
|--------------------|----------------|-------------------------------------------------------|
| `id`               | UUID / Integer | Identifiant unique du message                         |
| `contenu`          | String (TEXT)  | Texte du message envoyé ou reçu                       |
| `role`             | Enum           | Rôle de l'auteur : `user` ou `assistant`              |
| `date_creation`    | Timestamp      | Date et heure d'envoi du message                      |
| `session_id`       | UUID / Integer | Identifiant de la session/conversation associée       |

---

## Exercice 2 : Git et Docker

### 1. Méthodologie et Git

#### Question A — User Story

> **En tant que** utilisateur premium,  
> **je veux** accéder à des fonctionnalités avancées (historique illimité, réponses prioritaires, etc.),  
> **afin de** bénéficier d'une expérience plus complète et sans limitations.

---

#### Question B — Commandes Git

**1. Créer la branche feature et faire un commit :**

```bash
git checkout -b feature-premium-subscription
# ... modifications du code ...
git add .
git commit -m "feat: add premium subscription system"
```

**2. Fusionner sur `main` et créer le tag `v1.0.0` :**

```bash
git checkout main
git merge feature-premium-subscription
git tag v1.0.0
```

**3. Pousser le tag sur GitHub :**

```bash
git push origin v1.0.0
```

---

## Exercice 3 : CI/CD — Sécurité et Secrets

### Question A — Où enregistrer le secret sur GitHub

1. Aller sur la page du dépôt GitHub.
2. Cliquer sur **Settings** (onglet en haut à droite).
3. Dans le menu gauche, cliquer sur **Secrets and variables** → **Actions**.
4. Cliquer sur **New repository secret**.
5. Renseigner :
   - **Name** : `OPENAI_API_KEY`
   - **Secret** : la valeur réelle de la clé API
6. Cliquer sur **Add secret**.

---

### Question B — Syntaxe pour injecter le secret dans le workflow

Dans le fichier `.github/workflows/main.yml`, injecter le secret comme variable d'environnement dans l'étape de déploiement :

```yaml
- name: Déploiement (uniquement sur tag)
  if: startsWith(github.ref, 'refs/tags/v')
  env:
    OPENAI_API_KEY: ${{ secrets.OPENAI_API_KEY }}
  run: echo "Déploiement en cours..."
```

La syntaxe `${{ secrets.NOM_DU_SECRET }}` permet d'injecter n'importe quel secret GitHub Actions dans une étape. La valeur n'est jamais visible dans les logs (elle est masquée automatiquement par GitHub).
