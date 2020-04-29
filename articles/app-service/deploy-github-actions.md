---
title: Configure CI/CD com ações gitHub
description: Saiba como implementar o seu código para o Serviço de Aplicações Azure a partir de um pipeline CI/CD com GitHub Actions. Personalize as tarefas de construção e execute implementações complexas.
ms.devlang: na
ms.topic: article
ms.date: 10/25/2019
ms.author: jafreebe
ms.reviewer: ushan
ms.openlocfilehash: 57ca5b0880d4b027e33bc0d01fc6225eb886029b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82084996"
---
# <a name="deploy-to-app-service-using-github-actions"></a>Implemente para o Serviço de Aplicações usando ações GitHub

[GitHub Actions](https://help.github.com/en/articles/about-github-actions) dá-lhe a flexibilidade para construir um fluxo de trabalho de ciclo de vida de desenvolvimento de software automatizado. Com as ações de serviço de aplicação Azure para o GitHub, pode automatizar o seu fluxo de trabalho para implementar para [o Azure App Service](overview.md) utilizando as ações do GitHub.

> [!IMPORTANT]
> A GitHub Actions está atualmente em versão beta. Primeiro deve [inscrever-se para se juntar à pré-visualização](https://github.com/features/actions) utilizando a sua conta GitHub.
> 

Um fluxo de trabalho é definido por um ficheiro `/.github/workflows/` YAML (.yml) no caminho do seu repositório. Esta definição contém os vários passos e parâmetros que compõem o fluxo de trabalho.

Para um fluxo de trabalho do Serviço de Aplicações Azure, o ficheiro tem três secções:

|Section  |Tarefas  |
|---------|---------|
|**Autenticação** | 1. Definir um diretor de serviço <br /> 2. Criar um segredo GitHub |
|**Compilação** | 1. Criar o ambiente <br /> 2. Construir a aplicação web |
|**Implementar** | 1. Implementar a aplicação web |

## <a name="create-a-service-principal"></a>Criar um principal de serviço

Pode criar um diretor de [serviço](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) utilizando o comando [az ad sp create-for-rbac](https://docs.microsoft.com/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) no [Azure CLI](https://docs.microsoft.com/cli/azure/). Pode executar este comando utilizando a [Azure Cloud Shell](https://shell.azure.com/) no portal Azure ou selecionando o botão **Experimente..**

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name>/providers/Microsoft.Web/sites/<app-name> --sdk-auth
```

Neste exemplo, substitua os espaços reservados do recurso pelo seu ID de subscrição, nome do grupo de recursos e nome da aplicação. A saída são as credenciais de atribuição de funções que fornecem acesso à sua app App Service. Copie este objeto JSON, que pode usar para autenticar a partir do GitHub.

> [!NOTE]
> Não precisa de criar um diretor de serviço se decidir utilizar o perfil de publicação para autenticação.

> [!IMPORTANT]
> É sempre uma boa prática conceder o mínimo de acesso. É por isso que o âmbito no exemplo anterior se limita à aplicação específica do App Service e não a todo o grupo de recursos.

## <a name="configure-the-github-secret"></a>Configure o segredo GitHub

Também pode utilizar credenciais de nível de aplicações, ou seja, publicar o perfil para implementação. Siga os passos para configurar o segredo:

1. Descarregue o perfil de publicação da aplicação App Service a partir do portal utilizando a opção **de perfil Get Publish.**

2. No [GitHub,](https://github.com/)navegue no seu repositório, selecione **Definições > Segredos > Adicione um novo segredo**

    ![segredos](media/app-service-github-actions/secrets.png)

3. Colhe o conteúdo do ficheiro de perfil de publicação descarregado no campo de valor do segredo.

4. Agora, no ficheiro workflow `.github/workflows/workflow.yml` no seu ramo: `publish-profile` substitua o segredo para a entrada da ação da Implementação da Web App azure.
    
    ```yaml
        - uses: azure/webapps-deploy@v2
          with:
            creds: ${{ secrets.azureWebAppPublishProfile }}
    ```

5. Vê o segredo como mostrado abaixo uma vez definido.

    ![segredos](media/app-service-github-actions/app-service-secrets.png)

## <a name="set-up-the-environment"></a>Configurar o ambiente

A criação do ambiente pode ser feita utilizando uma das ações de configuração.

|**Idioma**  |**Ação de configuração**  |
|---------|---------|
|**.NET**     | `actions/setup-dotnet` |
|**Java**     | `actions/setup-java` |
|**JavaScript** | `actions/setup-node` |
|**Python**     | `actions/setup-python` |

Os seguintes exemplos mostram a parte do fluxo de trabalho que cria o ambiente para as várias línguas apoiadas:

**JavaScript**

```yaml
    - name: Setup Node 10.x
      uses: actions/setup-node@v1
      with:
        node-version: '10.x'
```
**Python**

```yaml
    - name: Setup Python 3.6
      uses: actions/setup-python@v1
      with:
        python-version: 3.6
```

**.NET**

```yaml
    - name: Setup Dotnet 2.2.300
      uses: actions/setup-dotnet@v1
      with:
        dotnet-version: '2.2.300'
```

**Java**

```yaml
    - name: Setup Java 1.8.x
      uses: actions/setup-java@v1
      with:
        # If your pom.xml <maven.compiler.source> version is not in 1.8.x
        # Please change the Java version to match the version in pom.xml <maven.compiler.source>
        java-version: '1.8.x'
```

## <a name="build-the-web-app"></a>Construir a aplicação web

Isto depende da linguagem e das línguas suportadas pelo Azure App Service, esta secção deve ser os passos padrão de construção de cada idioma.

Os exemplos seguintes mostram a parte do fluxo de trabalho que constrói a aplicação web, nos vários idiomas suportados.

**JavaScript**

```yaml
    - name: 'Run npm'
      shell: bash
      run: |
        # If your web app project is not located in your repository's root
        # Please change your directory for npm in pushd
        pushd .
        npm install
        npm run build --if-present
        npm run test --if-present
        popd
```

**Python**

```yaml
    - name: 'Run pip'
      shell: bash
      run: |
        # If your web app project is not located in your repository's root
        # Please change your directory for pip in pushd
        pushd .
        python -m pip install --upgrade pip
        pip install -r requirements.txt --target=".python_packages/lib/python3.6/site-packages"
        popd
```

**.NET**

```yaml
    - name: 'Run dotnet build'
      shell: bash
      run: |
        # If your web app project is not located in your repository's root
        # Please consider using pushd to change your path
        pushd .
        dotnet build --configuration Release --output ./output
        popd
```

**Java**

```yaml
    - uses: actions/checkout@v1
    - name: Set up JDK 1.8
      uses: actions/setup-java@v1
      with:
        java-version: 1.8
    - name: Build with Maven
      run: mvn -B package --file pom.xml
```
## <a name="deploy-to-app-service"></a>Implementar no Serviço de Aplicações

Para implementar o seu código numa `azure/webapps-deploy@v2` aplicação do App Service, utilize a ação. Esta ação tem quatro parâmetros:

| **Parâmetro**  | **Explicação**  |
|---------|---------|
| **nome de aplicativo** | (Obrigatório) Nome da app App Service | 
| **perfil editorial** | (Opcional) Publique conteúdo de ficheiro de perfil com segredos de Web Deploy |
| **pacote** | (Opcional) Caminho para pacote ou pasta. *.zip, *.war, *.jar ou uma pasta para implantar |
| **slot-name** | (Opcional) Introduza uma ranhura existente que não seja a ranhura de produção |

### <a name="deploy-using-publish-profile"></a>Implementar usando o Perfil de Publicação

Abaixo está o fluxo de trabalho da amostra para construir e implementar uma aplicação Node.js para o Azure usando o perfil de publicação.

```yaml
# File: .github/workflows/workflow.yml

on: push

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    # checkout the repo
    - name: 'Checkout GitHub Action' 
      uses: actions/checkout@master
    
    - name: Setup Node 10.x
      uses: actions/setup-node@v1
      with:
        node-version: '10.x'
    - name: 'npm install, build, and test'
      run: |
        npm install
        npm run build --if-present
        npm run test --if-present
       
    - name: 'Run Azure webapp deploy action using publish profile credentials'
          uses: azure/webapps-deploy@v2
          with: 
            app-name: node-rn
            publish-profile: ${{ secrets.azureWebAppPublishProfile }}
```

### <a name="deploy-using-azure-service-principal"></a>Implementar utilizando o principal de serviço Azure

Abaixo está o fluxo de trabalho da amostra para construir e implementar uma app Node.js para O Azure usando um diretor de serviço Azure.

```yaml
on: [push]

name: Node.js

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    # checkout the repo
    - name: 'Checkout GitHub Action' 
      uses: actions/checkout@master
   
    - uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
        
    - name: Setup Node 10.x
      uses: actions/setup-node@v1
      with:
        node-version: '10.x'
    
    - name: 'npm install, build, and test'
      run: |
        npm install
        npm run build --if-present
        npm run test --if-present
               
    # deploy web app using Azure credentials
    - uses: azure/webapps-deploy@v2
      with:
        app-name: 'node-rn'

    # Azure logout 
    - name: logout
      run: |
        az logout
```

## <a name="next-steps"></a>Passos seguintes

Pode encontrar o nosso conjunto de Ações agrupadas em diferentes repositórios no GitHub, cada um contendo documentação e exemplos para ajudá-lo a usar o GitHub para CI/CD e implementar as suas aplicações para o Azure.

- [Ações fluxo de trabalho para implantar para Azure](https://github.com/Azure/actions-workflow-samples)

- [Início de sessão no Azure](https://github.com/Azure/login)

- [Azure WebApp](https://github.com/Azure/webapps-deploy)

- [Azure WebApp para contentores](https://github.com/Azure/webapps-container-deploy)

- [Docker login/logout](https://github.com/Azure/docker-login)

- [Eventos que desencadeiam fluxos de trabalho](https://help.github.com/en/articles/events-that-trigger-workflows)

- [Implantação de K8s](https://github.com/Azure/k8s-deploy)

- [Fluxos de trabalho de arranque](https://github.com/actions/starter-workflows)
