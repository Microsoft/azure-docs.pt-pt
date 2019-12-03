---
title: Configurar CI/CD com ações do GitHub
description: Saiba como implantar seu código para Azure App serviço de um pipeline de CI/CD com ações do GitHub. Personalize as tarefas de compilação e execute implantações complexas.
ms.devlang: na
ms.topic: article
ms.date: 10/25/2019
ms.author: jafreebe
ms.reviewer: ushan
ms.openlocfilehash: e3d6e730846388c4b74cfa0b6361629e836b0517
ms.sourcegitcommit: 265f1d6f3f4703daa8d0fc8a85cbd8acf0a17d30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2019
ms.locfileid: "74670199"
---
# <a name="deploy-to-app-service-using-github-actions"></a>Implantar no serviço de aplicativo usando ações do GitHub

As [ações do GitHub](https://help.github.com/en/articles/about-github-actions) oferecem a flexibilidade para criar um fluxo de trabalho de desenvolvimento automatizado do software. Com as ações de serviço Azure App para o GitHub, você pode automatizar o fluxo de trabalho para implantar o [serviço Azure app](overview.md) usando as ações do github.

> [!IMPORTANT]
> No momento, as ações do GitHub estão em beta. Primeiro, você deve [se inscrever para ingressar na versão prévia](https://github.com/features/actions) usando sua conta do github.
> 

Um fluxo de trabalho é definido por um arquivo YAML (. yml) no caminho `/.github/workflows/` em seu repositório. Essa definição contém as várias etapas e parâmetros que compõem o fluxo de trabalho.

Para um fluxo de trabalho de serviço Azure App, o arquivo tem três seções:

|Section  |Tarefas  |
|---------|---------|
|**Autenticação** | 1. definir uma entidade de serviço <br /> 2. criar um segredo do GitHub |
|**Integrado** | 1. configurar o ambiente <br /> 2. compilar o aplicativo Web |
|**Implementar** | 1. implantar o aplicativo Web |

## <a name="create-a-service-principal"></a>Criar um principal de serviço

Você pode criar uma [entidade de serviço](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) usando o comando [AZ ad SP Create-for-RBAC](https://docs.microsoft.com/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) na [CLI do Azure](https://docs.microsoft.com/cli/azure/). Você pode executar esse comando usando [Azure cloud Shell](https://shell.azure.com/) na portal do Azure ou selecionando o botão **experimentar** .

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name>/providers/Microsoft.Web/sites/<app-name> --sdk-auth
```

Neste exemplo, substitua os espaços reservados no recurso por sua ID de assinatura, nome do grupo de recursos e nome do aplicativo. A saída são as credenciais de atribuição de função que fornecem acesso ao aplicativo do serviço de aplicativo. Copie esse objeto JSON, que você pode usar para autenticar do GitHub.

> [!NOTE]
> Você não precisará criar uma entidade de serviço se decidir usar o perfil de publicação para autenticação.

> [!IMPORTANT]
> É sempre uma boa prática conceder acesso mínimo. É por isso que o escopo no exemplo anterior é limitado ao aplicativo do serviço de aplicativo específico e não ao grupo de recursos inteiro.

## <a name="configure-the-github-secret"></a>Configurar o segredo do GitHub

Você também pode usar credenciais de nível de aplicativo, ou seja, publicar perfil para implantação. Siga as etapas para configurar o segredo:

1. Baixe o perfil de publicação para o aplicativo do serviço de aplicativo no portal usando a opção **obter perfil de publicação** .

2. No [GitHub](https://github.com/), procure seu repositório, selecione **configurações > segredos > Adicionar um novo segredo**

    ![confidenciais](media/app-service-github-actions/secrets.png)

3. Cole o conteúdo do arquivo de perfil de publicação baixado no campo valor do segredo.

4. Agora, no arquivo de fluxo de trabalho em seu Branch: `.github/workflows/workflow.yml` substituir o segredo para o `publish-profile` de entrada da ação implantar aplicativo Web do Azure.
    
    ```yaml
        - uses: azure/webapps-deploy@v1
          with:
            creds: ${{ secrets.azureWebAppPublishProfile }}
    ```

5. Você verá o segredo, conforme mostrado abaixo, uma vez definido.

    ![confidenciais](media/app-service-github-actions/app-service-secrets.png)

## <a name="set-up-the-environment"></a>Configurar o ambiente

A configuração do ambiente pode ser feita usando uma das ações de instalação.

|**Language** (Idioma)  |**Ação de instalação**  |
|---------|---------|
|**.NET**     | `actions/setup-dotnet` |
|**Java**     | `actions/setup-java` |
|**JavaScript** | `actions/setup-node` |
|**Python**     | `actions/setup-python` |

Os exemplos a seguir mostram a parte do fluxo de trabalho que configura o ambiente para os vários idiomas com suporte:

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

## <a name="build-the-web-app"></a>Compilar o aplicativo Web

Isso depende do idioma e dos idiomas com suporte pelo serviço Azure App, esta seção deve ser as etapas de compilação padrão de cada idioma.

Os exemplos a seguir mostram a parte do fluxo de trabalho que cria o aplicativo Web, em vários idiomas com suporte.

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

Para implantar seu código em um aplicativo do serviço de aplicativo, use a ação `azure/webapps-deploy@v1 `. Esta ação tem quatro parâmetros:

| **Meter**  | **Explicação**  |
|---------|---------|
| **nome do aplicativo** | Necessária Nome do aplicativo do serviço de aplicativo | 
| **publicar perfil** | Adicional Publicar o conteúdo do arquivo de perfil com segredos de Implantação da Web |
| **agrupa** | Adicional Caminho para o pacote ou a pasta. *. zip, *. War, *. jar ou uma pasta a ser implantada |
| **nome do slot** | Adicional Insira um slot existente que não seja o slot de produção |

### <a name="deploy-using-publish-profile"></a>Implantar usando o perfil de publicação

Veja abaixo o fluxo de trabalho de exemplo para criar e implantar um aplicativo node. js no Azure usando o perfil de publicação.

```yaml
# File: .github/workflows/workflow.yml

on: push

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    # checkout the repo
    - name: 'Checkout Github Action' 
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
          uses: azure/webapps-deploy@v1
          with: 
            app-name: node-rn
            publish-profile: ${{ secrets.azureWebAppPublishProfile }}
```

### <a name="deploy-using-azure-service-principal"></a>Implantar usando a entidade de serviço do Azure

Veja abaixo o fluxo de trabalho de exemplo para criar e implantar um aplicativo node. js no Azure usando uma entidade de serviço do Azure.

```yaml
on: [push]

name: Node.js

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    # checkout the repo
    - name: 'Checkout Github Action' 
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
    - uses: azure/webapps-deploy@v1
      with:
        app-name: 'node-rn'

    # Azure logout 
    - name: logout
      run: |
        az logout
```

## <a name="next-steps"></a>Passos seguintes

Você pode encontrar nosso conjunto de ações agrupadas em repositórios diferentes no GitHub, cada uma contendo documentação e exemplos para ajudá-lo a usar o GitHub para CI/CD e implantar seus aplicativos no Azure.

- [Fluxo de trabalho de ações para implantar no Azure](https://github.com/Azure/actions-workflow-samples)

- [Logon do Azure](https://github.com/Azure/login)

- [WebApp do Azure](https://github.com/Azure/webapps-deploy)

- [Azure WebApp para contêineres](https://github.com/Azure/webapps-container-deploy)

- [Logon/logout do Docker](https://github.com/Azure/docker-login)

- [Eventos que disparam fluxos de trabalho](https://help.github.com/en/articles/events-that-trigger-workflows)

- [K8s implantar](https://github.com/Azure/k8s-deploy)

- [Fluxos de trabalho iniciais](https://github.com/actions/starter-workflows)
