# Orchestrateur ou integration continue ? (TP06)

| Critere | Prefect (orchestrateur) | GitHub Actions (CI) |
|---|---|---|
| Ce qui declenche l'execution | Une planification temporelle (cron : chaque lundi a 6h) ou un declenchement manuel via l'interface (Quick run) / webhook / evenement de donnees | Un evenement Git (`git push`, pull request) ou un declenchement manuel via `workflow_dispatch` |
| Ou s'execute le traitement | Sur l'infrastructure locale ou des serveurs/workers dedies de l'organisation (Worker local, VM, cluster Kubernetes) | Sur les serveurs distants heberges par GitHub (runners cloud `ubuntu-latest`) |
| Qui surveille, et ou | L'equipe MLOps et metier via l'interface web de Prefect Dashboard (`http://127.0.0.1:4200` ou Prefect Cloud) avec statuts, graphe de taches et logs | Les developpeurs et l'equipe technique directement dans l'onglet Actions du depot GitHub et les statuts des commits |
| Ce qui se passe en cas d'echec | Prefect retente automatiquement les taches tolerantes aux pannes (`retries=2`), interrompt le flow sur la tache bloquante (`valider`), marque l'execution en rouge/Failed et n'execute pas `notifier` | Le job s'arrete immediatement sur l'etape en echec (code de sortie 1), le commit est marque d'une croix rouge, l'artefact des rapports est conserve via `if: always()` et l'equipe est avertie par email |
| Type de tache adapte | Traitements de donnees longs, reentrainement periodique lourd, pipelines de production ML, taches necessitant acces aux donnees privees et stockage interne | Validation rapide du code, tests unitaires, linters, verification de non-regression, construction d'images Docker et deploiement continu |
| Ce qui est versionne dans Git | Le code du workflow en Python (`flow_mlops.py`), les scripts de deploiement (`planifier.py`) et la configuration du deploiement (`prefect.yaml`) | Le fichier declaratif YAML du workflow (`.github/workflows/ci.yml`), le script de validation (`verifier_qualite.py`) et la liste des dependances (`requirements-ci.txt`) |

## Conclusion (5 lignes)
La chaine d'integration continue (GitHub Actions) valide le code et assure la non-regression a chaque modification avant toute mise en production.
L'orchestrateur (Prefect) execute, surveille et planifie des traitements recurrents ou lourds directement sur l'infrastructure de donnees.
Tandis que la CI protege la qualite du code source, l'orchestrateur assure la continuite operationnelle des modeles en production.
Ces deux technologies repondent a des finalites differentes mais indispensables et parfaitement complementaires dans un cycle MLOps mature.
L'un ne remplace pas l'autre : la CI valide ce qui doit etre deploye, et l'orchestrateur pilote ce qui tourne en production.
