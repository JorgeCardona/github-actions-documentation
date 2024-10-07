# github-actions-documentation

## Eventos mas usados
| **Evento**                       | **Descripción**                                                                                          | **Ejemplo de uso**                                                                                       |
|----------------------------------|----------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------|
| `check_run`                     | Se activa cuando cambia el estado de una verificación (check run).                                      | ```yaml on: check_run: ```                                                                                 |
| `check_suite`                   | Se activa cuando se crea o actualiza un conjunto de verificaciones (check suite).                      | ```yaml on: check_suite: ```                                                                                |
| `commit`                        | Se activa cuando se envía un nuevo commit al repositorio.                                               | ```yaml on: commit: ```                                                                                     |
| `commit_comment`                | Se activa cuando se agrega un comentario a un commit.                                                   | ```yaml on: commit_comment: ```                                                                             |
| `create`                         | Se activa cuando se crea una rama o un tag.                                                             | ```yaml on: create: ```                                                                                     |
| `deployment`                     | Se activa cuando se crea un despliegue.                                                                 | ```yaml on: deployment: ```                                                                                 |
| `deployment_status`             | Se activa cuando cambia el estado de un despliegue.                                                     | ```yaml on: deployment_status: ```                                                                           |
| `delete`                         | Se activa cuando se elimina una rama o un tag en el repositorio.                                        | ```yaml on: delete: ```                                                                                     |
| `fork`                           | Se activa cuando se crea un fork de un repositorio.                                                    | ```yaml on: fork: ```                                                                                       |
| `issues`                         | Se activa cuando se crea o se modifica un problema (issue).                                            | ```yaml on: issues: types: [opened, edited] ```                                                            |
| `issue_comment`                 | Se activa cuando se agrega un comentario a un problema o pull request.                                   | ```yaml on: issue_comment: types: [created] ```                                                           |
| `label`                          | Se activa cuando se crea o elimina una etiqueta en un problema o pull request.                         | ```yaml on: label: ```                                                                                      |
| `labeled`                       | Se activa cuando se agrega o quita una etiqueta en un issue.                                            | ```yaml on: issues: types: [labeled] ```                                                                   |
| `milestone`                      | Se activa cuando se crea o actualiza un hito (milestone) en el repositorio.                            | ```yaml on: milestone: ```                                                                                  |
| `milestone`                      | Se activa cuando se cierra un hito (milestone).                                                        | ```yaml on: milestone: types: [closed] ```                                                                  |
| `package`                        | Se activa cuando se crea o se elimina un paquete.                                                       | ```yaml on: package: ```                                                                                   |
| `page_build`                    | Se activa cuando se completa la construcción de una página de GitHub (GitHub Pages).                    | ```yaml on: page_build: ```                                                                                 |
| `private`                        | Se activa cuando un repositorio se vuelve privado.                                                     | ```yaml on: private: ```                                                                                    |
| `public`                         | Se activa cuando un repositorio se vuelve público.                                                      | ```yaml on: public: ```                                                                                     |
| `pull_request`                  | Se activa cuando se crea o actualiza un pull request.                                                   | ```yaml on: pull_request: branches: [main] ```                                                              |
| `pull_request_review`           | Se activa cuando se deja una revisión en un pull request.                                               | ```yaml on: pull_request_review: types: [submitted] ```                                                   |
| `pull_request_review_comment`    | Se activa cuando se agrega un comentario de revisión en un pull request.                               | ```yaml on: pull_request_review_comment: types: [created] ```                                             |
| `pull_request_target`           | Se activa en el contexto de un pull request y permite acceder a los permisos del repositorio.          | ```yaml on: pull_request_target: ```                                                                         |
| `release`                        | Se activa cuando se publica una nueva versión (release) en el repositorio.                              | ```yaml on: release: types: [published] ```                                                                 |
| `release`                        | Se activa cuando se elimina un release.                                                                  | ```yaml on: release: types: [deleted] ```                                                                   |
| `repository`                     | Se activa para eventos relacionados con el repositorio.                                                 | ```yaml on: repository: ```                                                                                 |
| `repository_dispatch`           | Permite desencadenar un flujo de trabajo desde un webhook externo.                                       | ```yaml on: repository_dispatch: types: [custom_event] ```                                                |
| `schedule`                       | Se activa según una programación especificada utilizando la sintaxis de cron.                           | ```yaml on: schedule: - cron: '0 0 * * *' ```                                                              |
| `status`                         | Se activa cuando cambia el estado de una verificación (check) en un commit.                            | ```yaml on: status: ```                                                                                    |
| `synchronize`                   | Se activa cuando un pull request es sincronizado con su rama base.                                       | ```yaml on: pull_request: types: [synchronize] ```                                                         |
| `unlabeled`                     | Se activa cuando se quita una etiqueta de un issue.                                                     | ```yaml on: issues: types: [unlabeled] ```                                                                  |
| `watch`                          | Se activa cuando se agrega o se quita un seguimiento a un repositorio.                                  | ```yaml on: watch: ```                                                                                     |
| `workflow_dispatch`             | Permite desencadenar manualmente un flujo de trabajo desde la interfaz de GitHub.                        | ```yaml on: workflow_dispatch: ```                                                                          |
| `workflow_run`                  | Se activa cuando se completa otro flujo de trabajo.                                                     | ```yaml on: workflow_run: workflows: ["other-workflow"] types: [completed] ```                           |
