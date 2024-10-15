# github-actions-documentation

## Estructura y Elementos de Workflows en GitHub Actions
```yaml
⭐ GitHub Actions Workflow  
┗ 🌼 name (opcional)  
   ┗ 📄 Nombre del workflow para identificarlo  
┗ 🌼 on (requerido)  
   ┗ 🔄 Define los eventos que desencadenan el workflow  
┗ 🌼 env (opcional)  
   ┗ 🌍 Variables de entorno globales  
┗ 🌼 defaults (opcional)  
   ┗ 🎯 Valores por defecto aplicables a todos los steps
┗ 🔑 permissions (opcional)
  ┗ 🚥 contents (requerido) Define los permisos que el workflow tiene en el repositorio.  
┗ 🌼 concurrency (opcional)  
   ┗ ⏱️ Permite controlar la ejecución simultánea de workflows.  
┗ 🌼 jobs (requerido)  
   ┗ 💼 nombre del Job, Contiene todas las tareas que se ejecutarán
      ┗ 🌐 runs-on (requerido)
      ┗ 🎯 defaults (opcional)
      ┗ 🚀 strategy (opcional)
        ┗ 🔢 matrix (requerido)
      ┗ 🔑 permissions (opcional)
        ┗ 🚥 contents (requerido)
      ┗ 📄 needs (opcional)  
      ┗ 📜 steps (requerido)  
         ┗ 📄 name: Nombre del paso  
         ┗ 🪪 id: Identificador único  
         ┗ 🚀 uses: Acción externa  
         ┗ 💻 run: Comandos a ejecutar  
         ┗ 🔧 with: Parámetros para uses  
         ┗ 🌍 env: Variables de entorno  
         ┗ ⚡ if: Condición  
         ┗ ⏳ timeout-minutes: Tiempo límite  
         ┗ 🏁 continue-on-error: Continuar tras error  
         ┗ 📁 working-directory: Cambiar directorio  
┗ 🌼 container (opcional)  
   ┗ 🐳 Define un contenedor Docker personalizado  
┗ 🌼 services (opcional)  
   ┗ 🛠️ Servicios adicionales  
┗ 🌼 outputs (opcional)  
   ┗ 📊 Salidas del trabajo  
┗ 🌼 timeout-minutes (opcional)  
   ┗ ⏳ Límite de tiempo global para todo el job.  
┗ 🌼 continue-on-error (opcional)  
   ┗ 🏁 Permite que el workflow continúe aunque el job falle.  
┗ 🌼 if (opcional)  
   ┗ ⚙️ Condición para ejecutar el job completo.  
```
---


## Tipos de llaves y usos en el workflow

| Tipo de Llaves            | Uso                                               | Descripción                                                                                                                                                                          | Ejemplo                                                                                                       |
|---------------------------|--------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------|
| **Llaves Sencillas `{}`**  | Usadas en scripts de shell dentro de los comandos `run`. | Se utilizan dentro de scripts de shell (bash) para manejar variables locales del script y realizar sustituciones. Permiten acceder a valores calculados y exportar variables dentro del mismo entorno de ejecución del shell. Estas variables son locales al script y no se comparten entre pasos, a menos que se exporten explícitamente. | ```bash output=$(python src/script.py)  echo "Valor: ${output}"  # Acceder a una variable de shell ```          |
| **Llaves Dobles `{{}}`**   | Usadas en expresiones de GitHub Actions dentro del archivo YAML. | Se usan en GitHub Actions para evaluar expresiones y acceder a variables de contexto como `env`, `steps`, `secrets`, entre otros. Estas variables pueden ser compartidas entre pasos y jobs dentro del flujo de trabajo. Las expresiones con `{{}}` son procesadas por GitHub Actions antes de ejecutar los comandos, permitiendo el uso dinámico de datos en el YAML. | ```yaml run: echo "El tiempo actual es ${{ env.current_time }}"  # Acceder a una variable de entorno global ``` |

### Resumen:
- **Llaves sencillas `{}`**: Se usan dentro de scripts de shell (por ejemplo, en los pasos de `run` en GitHub Actions) para manejar variables locales del propio script. Estas variables pueden ser definidas y accedidas directamente dentro del entorno de ejecución del shell. Además, permiten exportar variables o valores calculados para usarlos en otros comandos dentro del mismo script. Por ejemplo, al ejecutar un script, puedes capturar la salida de un comando y almacenarla en una variable local usando llaves sencillas. Estas variables solo están disponibles dentro del script que se ejecuta en ese paso específico.
  
  **Ejemplo**:
  ```bash
   output=$(python script.py)  # Captura la salida de las funciones del codigo que son los prints del script Python en una variable. Si no hay prints, la variable estará vacía.
   output=$(python script.py | tail -n 1)  # Captura solo el último print de la salida del script
   echo "Resultado: ${output}"  # Imprime el valor de la variable 'output' en la consola para verificar el resultado
   echo "RESULT_VAR=${output}" >> $GITHUB_ENV  # Exporta la variable RESULT_VAR como variable de entorno
  ```

- **Llaves dobles `{{}}`**: Se usan dentro del archivo YAML de GitHub Actions para referirse a variables del contexto del flujo de trabajo. Estas expresiones permiten acceder a información específica del entorno de ejecución, como variables de entorno (`env`), salidas de pasos anteriores (`steps`), secretos (`secrets`), e incluso realizar operaciones o cálculos dentro del flujo. Las variables dentro de las llaves dobles son evaluadas por GitHub Actions y su valor es accesible a lo largo del flujo de trabajo. Esto es útil para compartir información entre pasos o jobs y configurar parámetros dinámicos en tiempo de ejecución.

  **Ejemplo**:
  ```yaml
  run: echo "La hora actual es ${{ env.RESULT_VAR }}"  # Accede a la variable de entorno RESULT_VAR
  
  run: echo "El token es: ${{ secrets.MY_SECRET_TOKEN }}"  # Acceso al secreto MY_SECRET_TOKEN

  steps:
   - name: Run a script
     id: my_step
     run: echo "output_value=42" >> $GITHUB_ENV  # Define una variable de entorno
   
   - name: Use output from previous step
     run: echo "El valor de output_value es: ${{ steps.my_step.outputs.output_value }}"

  ```
  Aquí, `${{ env.RESULT_VAR }}` es evaluado por GitHub Actions para obtener el valor de la variable de entorno `RESULT_VAR`, que fue definida en un paso o job anterior.

### Explicación de Variables de Contexto en GitHub Actions

GitHub Actions permite el uso de varias variables de contexto para facilitar la reutilización y el acceso a datos en los flujos de trabajo. Aquí se detallan algunas de las más importantes:

#### 1. **Secrets**

- **Definición**: Los *secrets* son variables de entorno que almacenan información sensible, como contraseñas, tokens de acceso y claves API, son guardados directamente en la configuración de tu repositorio en GitHub. Se utilizan para evitar la exposición de información confidencial en los registros de ejecución.
  
- **Acceso**: Se accede a un *secret* utilizando la sintaxis `${{ secrets.SECRET_NAME }}`. Esto permite acceder a un valor de secreto definido en la configuración del repositorio.

- **Ejemplo**:
  ```yaml
  run: echo "El token es: ${{ secrets.MY_SECRET_TOKEN }}"  # Acceso al secreto MY_SECRET_TOKEN
  ```

#### 2. **Steps**

- **Definición**: Los *steps* son las acciones individuales que se ejecutan dentro de un job en un flujo de trabajo. Cada *step* puede ejecutar un comando o usar una acción de GitHub.

- **Acceso**: Puedes acceder a los resultados de los pasos anteriores utilizando la sintaxis steps.<step_id>.outputs.<variable_name>
 `${{ steps.STEP_ID.outputs.VARIABLE_NAME }}`. Esto permite la reutilización de datos producidos por un *step* en otros pasos.

- **Ejemplo**:
  ```yaml
  steps:
    - name: Run a script
      id: my_step
      run: echo "output_value=42" >> $GITHUB_ENV  # Define una variable de entorno

    - name: Use output from previous step
      run: echo "El valor de output_value es: ${{ steps.my_step.outputs.output_value }}"
  ```

#### 3. **Env (Variables de Entorno)**

- **Definición**: Las variables de entorno son valores que se pueden definir y utilizar a lo largo del flujo de trabajo.

- **Acceso**: Se accede a una variable de entorno utilizando la sintaxis `${{ env.VARIABLE_NAME }}`.

- **Ejemplo**:
  ```yaml
  env:
    RESULT_VAR: "Valor de ejemplo"

  run: echo "La hora actual es: ${{ env.RESULT_VAR }}"  # Accede a la variable de entorno RESULT_VAR
  ```

### Ejemplo Completo

Aquí tienes un ejemplo de flujo de trabajo que utiliza **secrets**, **steps**, y **env**:

```yaml
name: Example Workflow

on: [push]

jobs:
  example_job:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout Repository
        uses: actions/checkout@v2

      - name: Set Environment Variable
        id: set_var
        run: |
          echo "RESULT_VAR=Valor de ejemplo" >> $GITHUB_ENV  # Exporta la variable como variable de entorno

      - name: Use Secret
        run: |
          echo "El token secreto es: ${{ secrets.MY_SECRET_TOKEN }}"  # Acceso al secreto

      - name: Use Environment Variable
        run: |
          echo "La hora actual es: ${{ env.RESULT_VAR }}"  # Accede a la variable de entorno RESULT_VAR

      - name: Output from Previous Step
        id: output_step
        run: |
          echo "output_value=42" >> $GITHUB_ENV  # Define un valor en el entorno

      - name: Access Output from Previous Step
        run: |
          echo "El valor de output_value es: ${{ steps.output_step.outputs.output_value }}"  # Acceso al output del paso anterior
```

### Resumen

En este flujo de trabajo, hemos utilizado:

- **Secrets** para acceder a un token sensible guardado directamente en la configuración de tu repositorio en GitHub, sin exponerlo en los registros de ejecución.
- **Steps** para definir y reutilizar valores generados en pasos anteriores.
- **Env** para acceder a variables de entorno definidas en el flujo de trabajo.

Esto permite una configuración más flexible y segura en GitHub Actions, facilitando la automatización de tareas mientras se protege la información sensible.


### Detalles adicionales:
- **Llaves sencillas `{}`**: Las variables declaradas con llaves sencillas en shell solo están disponibles dentro del ámbito del script que se está ejecutando en ese paso. No se comparten automáticamente entre diferentes pasos de GitHub Actions a menos que las exportes explícitamente a un archivo o variable de entorno.
  
- **Llaves dobles `{{}}`**: Las variables referenciadas con llaves dobles en el archivo YAML están disponibles para todos los pasos de un mismo job y pueden ser compartidas entre jobs si se configuran como artefactos o salidas. GitHub Actions procesa estas expresiones antes de ejecutar los comandos de shell, lo que permite que variables dinámicas sean fácilmente accesibles en todo el flujo de trabajo.



## Partes del workflow
| **Nombre**        | **Posición**           | **Descripción**                                                                                                                                                  | **Cuándo Usar**                                                                 | **Ejemplo**                                                                                      |
|-------------------|------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------|
| `name`            | Raíz                   | Nombre del workflow para identificarlo.                                                                                                                          | Opcional, útil para documentar el flujo de trabajo.                             | `name: Mi flujo de trabajo`                                                                      |
| `on`              | Raíz                   | Define los eventos que desencadenan el workflow.                                                                                                                 | Requerido para indicar cuándo debe ejecutarse el workflow.                      | `on: push: branches: - main`                                                                     |
| `env`             | Raíz o en `jobs`/`steps`| Variables de entorno globales o específicas de un job o step.                                                                                                    | Opcional, para compartir variables entre trabajos o pasos.                      | `env: NODE_ENV: production`                                                                      |
| `jobs`            | Raíz                   | Contiene los trabajos (jobs) a ejecutar en el workflow.                                                                                                          | Requerido, para definir las tareas del flujo de trabajo.                        | `jobs: build: runs-on: ubuntu-latest`                                                            |
| `runs-on`         | Dentro de `jobs`       | Define el sistema operativo o entorno en el que correrá el job.                                                                                                  | Requerido en cada trabajo.                                                      | `runs-on: ubuntu-latest`                                                                         |
| `needs`           | Dentro de `jobs`       | Define dependencias entre trabajos.                                                                                                                              | Opcional, si un job depende de la finalización de otro.                         | `needs: build`                                                                                   |
| `strategy`        | Dentro de `jobs`       | Define estrategias de ejecución paralela como matrices.                                                                                                          | Opcional, cuando necesitas ejecutar un job en varias configuraciones.           | `strategy: matrix: node-version: [12, 14, 16]`                                                   |
| `steps`           | Dentro de `jobs`       | Define los pasos a seguir dentro de un trabajo.                                                                                                                  | Requerido, para definir qué se ejecutará en un job.                             | `steps: - name: Checkout code uses: actions/checkout@v2`                                         |
| `name`            | Dentro de `steps`      | Nombre del step para identificarlo.                                                                                                                              | Opcional, útil para describir cada paso.                                        | `name: Checkout code`                                                                            |
| `id`              | Dentro de `steps`      | Identificador único del step que puede ser referenciado.                                                                                                         | Opcional, si necesitas referenciar el step más tarde.                           | `id: ejecutar_script`                                                                            |
| `uses`            | Dentro de `steps`      | Especifica una acción externa que se utilizará en el step.                                                                                                       | Requerido si utilizas una acción externa.                                       | `uses: actions/checkout@v2`                                                                      |
| `run`             | Dentro de `steps`      | Comando a ejecutar en el paso.                                                                                                                                   | Requerido si no usas `uses`.                                                    | `run: echo "Hello, world!"`                                                                      |
| `with`            | Dentro de `steps`      | Define parámetros para acciones cuando usas `uses`.                                                                                                              | Opcional, cuando necesitas pasar parámetros a una acción.                       | `with: ref: main`                                                                                |
| `env`             | Dentro de `steps`      | Variables de entorno específicas del step.                                                                                                                       | Opcional, cuando las variables solo son necesarias en un step.                  | `env: API_KEY: ${{ secrets.API_KEY }}`                                                           |
| `if`              | Dentro de `steps` o `jobs`| Condición para ejecutar el paso o job.                                                                                                                           | Opcional, si quieres que el paso o job se ejecute bajo condiciones específicas.  | `if: success()`                                                                                  |
| `timeout-minutes` | Dentro de `steps` o `jobs`| Tiempo máximo de espera para la ejecución del step o job.                                                                                                        | Opcional, si necesitas limitar la duración de un step o job.                    | `timeout-minutes: 5`                                                                             |
| `continue-on-error`| Dentro de `steps` o `jobs`| Permite que el workflow continúe aunque el step o job falle.                                                                                                     | Opcional, si quieres continuar a pesar de errores.                              | `continue-on-error: true`                                                                        |
| `working-directory`| Dentro de `steps`      | Cambia el directorio de trabajo para el step.                                                                                                                    | Opcional, si necesitas ejecutar el paso en un directorio específico.            | `working-directory: src/`                                                                        |
| `container`       | Dentro de `jobs`       | Define un contenedor Docker personalizado en el que se ejecutará el job.                                                                                         | Opcional, si el job necesita ejecutarse en un contenedor Docker.                | `container: image: node:14`                                                                      |
| `services`        | Dentro de `jobs`       | Define servicios adicionales que se ejecutarán junto al job (como bases de datos).                                                                               | Opcional, si tu job necesita servicios adicionales.                             | `services: postgres: image: postgres:latest`                                                     |
| `outputs`         | Dentro de `jobs`       | Define las salidas (outputs) de un job que pueden ser usadas por otros jobs.                                                                                     | Opcional, si necesitas pasar datos entre trabajos.                              | `outputs: resultado: ${{ steps.ejecutar_script.outputs.resultado }}`                             |
| `permissions`     | Raíz o dentro de `jobs`| Define los permisos que el workflow o job tiene en el repositorio.                                                                                               | Opcional, si necesitas permisos personalizados.                                 | `permissions: contents: read`                                                                    |
| `concurrency`     | Raíz o dentro de `jobs`| Controla la ejecución simultánea de workflows para evitar conflictos.                                                                                            | Opcional, si quieres evitar que múltiples ejecuciones entren en conflicto.      | `concurrency: group: ${{ github.ref }}`                                                          |
| `defaults`        | Raíz o dentro de `jobs`| Define valores por defecto para los steps (ej. shell, directorio de trabajo).                                                                                    | Opcional, para simplificar la configuración repetitiva de los steps.            | `defaults: run: shell: bash working-directory: /app`                                              |



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
