---
title: Configure CI/CD com ações do GitHub
description: Saiba como implementar o seu código no Azure App Service a partir de um pipeline CI/CD com ações GitHub. Personalize as tarefas de construção e execute implementações complexas.
ms.devlang: na
ms.topic: article
ms.date: 10/25/2019
ms.author: jafreebe
ms.reviewer: ushan
ms.custom: devx-track-python
ms.openlocfilehash: 713f4228bc2ba968fc96668d4d5c568f33b7e786
ms.sourcegitcommit: 2ffa5bae1545c660d6f3b62f31c4efa69c1e957f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/11/2020
ms.locfileid: "88080288"
---
# <a name="deploy-to-app-service-using-github-actions"></a>Implementar para o Serviço de Aplicações usando ações do GitHub

[GitHub Actions](https://help.github.com/en/articles/about-github-actions) dá-lhe a flexibilidade para construir um fluxo de trabalho de ciclo de vida de desenvolvimento de software automatizado. Com as Ações de Serviço de Aplicações Azure para GitHub, pode automatizar o seu fluxo de trabalho para implantar no [Azure App Service](overview.md) utilizando ações do GitHub.

> [!IMPORTANT]
> GitHub Actions está atualmente em versão beta. Primeiro tem [de se inscrever para se juntar à pré-visualização](https://github.com/features/actions) utilizando a sua conta GitHub.
> 

Um fluxo de trabalho é definido por um ficheiro YAML (.yml) no caminho do `/.github/workflows/` seu repositório. Esta definição contém os vários passos e parâmetros que compõem o fluxo de trabalho.

Para um fluxo de trabalho do Azure App Service, o ficheiro tem três secções:

|Section  |Tarefas  |
|---------|---------|
|**Autenticação** | 1. Defina um diretor de serviço. <br /> 2. Criar um segredo do GitHub. |
|**Compilar** | 1. Criar o ambiente. <br /> 2. Construa a aplicação web. |
|**Implementar** | 1. Implementar a aplicação web. |

## <a name="generate-deployment-credentials"></a>Gerar credenciais de implantação

# <a name="user-level-credentials"></a>[Credenciais ao nível do utilizador](#tab/userlevel)

Pode criar um [principal de serviço](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) utilizando o comando [ad sp create-for-rbac](https://docs.microsoft.com/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) no [Azure CLI](https://docs.microsoft.com/cli/azure/). Pode executar este comando utilizando [a Azure Cloud Shell](https://shell.azure.com/) no portal Azure ou selecionando o botão **Tentar.**

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor \
                            --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name>/providers/Microsoft.Web/sites/<app-name> \
                            --sdk-auth
```

No exemplo acima, substitua os espaços reservados pelo seu ID de subscrição, nome de grupo de recursos e nome de aplicação. A saída é um objeto JSON com as credenciais de atribuição de funções que fornecem acesso à sua aplicação de Serviço de Aplicações semelhante abaixo. Copie este objeto JSON para mais tarde.

```output 
  {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
  }
```

> [!IMPORTANT]
> É sempre uma boa prática conceder o mínimo acesso. O âmbito no exemplo anterior está limitado à aplicação específica do Serviço de Aplicações e não a todo o grupo de recursos.

# <a name="app-level-credentials"></a>[Credenciais de nível de aplicativo](#tab/applevel)

Pode utilizar credenciais de nível de aplicação utilizando o perfil de publicação da sua aplicação. Vá à página de gestão da sua aplicação no portal. Na página **'Vista Geral',** clique em Obter Publicar a opção **de perfil.**

Mais tarde, necessitará do conteúdo do ficheiro.

---

## <a name="configure-the-github-secret"></a>Configure o segredo do GitHub

# <a name="user-level-credentials"></a>[Credenciais ao nível do utilizador](#tab/userlevel)

No [GitHub,](https://github.com/)navegue no seu repositório, selecione **Definições > Segredos > Adicione um novo segredo**.

Para utilizar [credenciais ao nível do utilizador,](#generate-deployment-credentials)cole toda a saída JSON do comando Azure CLI para o campo de valor do segredo. Dê ao segredo o nome `AZURE_CREDENTIALS` como.

Quando configurar o ficheiro de fluxo de trabalho mais tarde, utilize o segredo para a entrada `creds` da ação Azure Login. Por exemplo:

```yaml
- uses: azure/login@v1
  with:
    creds: ${{ secrets.AZURE_CREDENTIALS }}
```

# <a name="app-level-credentials"></a>[Credenciais de nível de aplicativo](#tab/applevel)

No [GitHub,](https://github.com/)navegue no seu repositório, selecione **Definições > Segredos > Adicione um novo segredo**.

Para utilizar [credenciais de nível de aplicação,](#generate-deployment-credentials)cole o conteúdo do ficheiro de perfil de publicação descarregado no campo de valor do segredo. Dê ao segredo o nome `azureWebAppPublishProfile` como.

Quando configurar o ficheiro de fluxo de trabalho mais tarde, utiliza o segredo para a entrada `publish-profile` da ação da App Web Azure. Por exemplo:
    
```yaml
- uses: azure/webapps-deploy@v2
  with:
    publish-profile: ${{ secrets.azureWebAppPublishProfile }}
```

---

## <a name="set-up-the-environment"></a>Configure o ambiente

A configuração do ambiente pode ser feita utilizando uma das ações de configuração.

|**Linguagem**  |**Ação de configuração**  |
|---------|---------|
|**.NET**     | `actions/setup-dotnet` |
|**Java**     | `actions/setup-java` |
|**JavaScript** | `actions/setup-node` |
|**Python**     | `actions/setup-python` |

Os exemplos a seguir mostram a parte do fluxo de trabalho que configura o ambiente para as várias línguas apoiadas:

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

## <a name="build-the-web-app"></a>Construa a aplicação web

Isto depende do idioma e para as línguas suportadas pelo Azure App Service, esta secção deve ser os passos padrão de construção de cada idioma.

Os exemplos a seguir mostram a parte do fluxo de trabalho que constrói a aplicação web, nos vários idiomas suportados.

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

Para implementar o seu código numa aplicação do Serviço de Aplicações, utilize a `azure/webapps-deploy@v2` ação. Esta ação tem quatro parâmetros:

| **Parâmetro**  | **Explicação**  |
|---------|---------|
| **nome de aplicativo** | (Obrigatório) Nome da app App Service | 
| **perfil de publicação** | (Opcional) Publique conteúdos de ficheiros de perfil com segredos de implementação da Web |
| **pacote** | (Opcional) Caminho para embalagem ou pasta. *.zip, *.war, *.jar ou uma pasta para implantar |
| **nome slot** | (Opcional) Introduza uma ranhura existente que não seja a ranhura de produção |

# <a name="user-level-credentials"></a>[Credenciais ao nível do utilizador](#tab/userlevel)

Abaixo está o fluxo de trabalho da amostra para construir e implementar uma aplicação Node.js para a Azure usando um diretor de serviço Azure. Note como a `creds` entrada refere o segredo que criou `AZURE_CREDENTIALS` anteriormente.

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

# <a name="app-level-credentials"></a>[Credenciais de nível de aplicativo](#tab/applevel)

Abaixo está o fluxo de trabalho da amostra para construir e implementar uma aplicação Node.js para a Azure usando o perfil de publicação da aplicação. Note como a `publish-profile` entrada refere o segredo que criou `azureWebAppPublishProfile` anteriormente.

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

---

## <a name="next-steps"></a>Passos seguintes

Você pode encontrar o nosso conjunto de Ações agrupadas em diferentes repositórios no GitHub, cada um contendo documentação e exemplos para ajudá-lo a usar GitHub para CI/CD e implementar suas aplicações para Azure.

- [Fluxos de trabalho de ações para implantar para Azure](https://github.com/Azure/actions-workflow-samples)

- [Início de sessão no Azure](https://github.com/Azure/login)

- [Azure WebApp](https://github.com/Azure/webapps-deploy)

- [Azure WebApp para contentores](https://github.com/Azure/webapps-container-deploy)

- [Login/logout do Docker](https://github.com/Azure/docker-login)

- [Eventos que acionam fluxos de trabalho](https://help.github.com/en/articles/events-that-trigger-workflows)

- [Implantação de K8s](https://github.com/Azure/k8s-deploy)

- [Fluxos de trabalho de arranque](https://github.com/actions/starter-workflows)
