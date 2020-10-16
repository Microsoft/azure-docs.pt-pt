---
title: Recipiente personalizado CI/CD das ações do GitHub
description: Saiba como utilizar as ações do GitHub para implantar o seu recipiente Linux personalizado para o Serviço de Aplicações a partir de um pipeline CI/CD.
ms.devlang: na
ms.topic: article
ms.date: 10/03/2020
ms.author: jafreebe
ms.reviewer: ushan
ms.custom: github-actions-azure
ms.openlocfilehash: 3a5e319115c124551c05f2ac5aa393ba19596d0d
ms.sourcegitcommit: b437bd3b9c9802ec6430d9f078c372c2a411f11f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91893361"
---
# <a name="deploy-a-custom-container-to-app-service-using-github-actions"></a>Implementar um recipiente personalizado para o Serviço de Aplicações utilizando ações do GitHub

[GitHub Actions](https://help.github.com/en/articles/about-github-actions) dá-lhe a flexibilidade para construir um fluxo de trabalho automatizado de desenvolvimento de software. Com a [ação Azure Web Deploy,](https://github.com/Azure/webapps-deploy)pode automatizar o seu fluxo de trabalho para implantar recipientes personalizados no [Serviço de Aplicações](overview.md) utilizando As ações do GitHub.

Um fluxo de trabalho é definido por um ficheiro YAML (.yml) no caminho do `/.github/workflows/` seu repositório. Esta definição contém os vários passos e parâmetros que estão no fluxo de trabalho.

Para um fluxo de trabalho do serviço de aplicações Azure, o ficheiro tem três secções:

|Section  |Tarefas  |
|---------|---------|
|**Autenticação** | 1. Recupere um perfil principal de serviço ou publique o perfil. <br /> 2. Criar um segredo do GitHub. |
|**Construir** | 1. Criar o ambiente. <br /> 2. Construa a imagem do recipiente. |
|**Implementar** | 1. Desdobre a imagem do recipiente. |

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Criar uma conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Uma conta do GitHub. Se não tiver um, inscreva-se [de graça.](https://github.com/join)  
- Um registo de contentores de trabalho e uma aplicação do Serviço de Aplicações Azure para contentores. Este exemplo utiliza o Registo do Contentor Azure. 
    - [Aprenda a criar uma aplicação de Node.js contentorizada usando o Docker, empurre a imagem do contentor para um registo e, em seguida, desloque a imagem para o Serviço de Aplicações Azure](https://docs.microsoft.com/azure/developer/javascript/tutorial-vscode-docker-node-01)

## <a name="generate-deployment-credentials"></a>Gerar credenciais de implantação

A forma recomendada de autenticar com os Serviços de Aplicações Azure para ações GitHub é com um perfil de publicação. Também pode autenticar com um diretor de serviço, mas o processo requer mais passos. 

Guarde a sua credencial de perfil de publicação ou principal de serviço como [um segredo do GitHub](https://docs.github.com/en/actions/reference/encrypted-secrets) para autenticar com a Azure. Vais aceder ao segredo dentro do teu fluxo de trabalho. 

# <a name="publish-profile"></a>[Publicar perfil](#tab/publish-profile)

Um perfil de publicação é uma credencial de nível de aplicação. Configura o teu perfil de publicação como um segredo do GitHub. 

1. Vá ao seu serviço de aplicações no portal Azure. 

1. Na página **'Vista Geral',** selecione **Obter Perfil De publicação**.

1. Guarde o ficheiro transferido. Vais usar o conteúdo do ficheiro para criar um segredo do GitHub.

# <a name="service-principal"></a>[Diretor de serviços](#tab/service-principal)

Pode criar um [principal de serviço](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) com o comando [ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac&preserve-view=true) no [Azure CLI](/cli/azure/). Executar este comando com [Azure Cloud Shell](https://shell.azure.com/) no portal Azure ou selecionando o botão **Try it.**

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor \
                            --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name>/providers/Microsoft.Web/sites/<app-name> \
                            --sdk-auth
```

No exemplo, substitua os espaços reservados pelo seu ID de subscrição, nome de grupo de recursos e nome de aplicação. A saída é um objeto JSON com as credenciais de atribuição de funções que fornecem acesso à sua aplicação De Serviço de Aplicações. Copie este objeto JSON para mais tarde.

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

---

## <a name="configure-the-github-secret"></a>Configure o segredo do GitHub

No [GitHub,](https://github.com/)navegue no seu repositório, selecione **Definições > Segredos > Adicione um novo segredo**.

Cole o conteúdo da saída JSON como o valor da variável secreta. Dê ao segredo o nome `AZURE_CREDENTIALS` como.

Quando configurar o ficheiro de fluxo de trabalho mais tarde, utilize o segredo para a entrada `creds` da ação Azure Login. Por exemplo:

```yaml
- uses: azure/login@v1
  with:
    creds: ${{ secrets.AZURE_CREDENTIALS }}
```

## <a name="configure-the-github-secret-for-authentication"></a>Configure o segredo do GitHub para a autenticação

# <a name="publish-profile"></a>[Publicar perfil](#tab/publish-profile)

No [GitHub,](https://github.com/)navegue no seu repositório, selecione **Definições > Segredos > Adicione um novo segredo**.

Para utilizar [credenciais de nível de aplicação,](#generate-deployment-credentials)cole o conteúdo do ficheiro de perfil de publicação descarregado no campo de valor do segredo. Diga o nome do `AZURE_WEBAPP_PUBLISH_PROFILE` segredo.

Ao configurar o seu fluxo de trabalho GitHub, utilize a ação `AZURE_WEBAPP_PUBLISH_PROFILE` da App Azure Web. Por exemplo:
    
```yaml
- uses: azure/webapps-deploy@v2
  with:
    publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
```

# <a name="service-principal"></a>[Diretor de serviços](#tab/service-principal)

No [GitHub,](https://github.com/)navegue no seu repositório, selecione **Definições > Segredos > Adicione um novo segredo**.

Para utilizar [credenciais ao nível do utilizador,](#generate-deployment-credentials)cole toda a saída JSON do comando Azure CLI para o campo de valor do segredo. Dê ao segredo o nome `AZURE_CREDENTIALS` como.

Quando configurar o ficheiro de fluxo de trabalho mais tarde, utilize o segredo para a entrada `creds` da ação Azure Login. Por exemplo:

```yaml
- uses: azure/login@v1
  with:
    creds: ${{ secrets.AZURE_CREDENTIALS }}
```

---

## <a name="configure-github-secrets-for-your-registry"></a>Configure os segredos do GitHub para o seu registo

Defina segredos para usar com a ação de Login do Docker. 

1. Vá ao seu recipiente no portal Azure ou Docker e copie o nome de utilizador e a palavra-passe. 

2. Defina um novo segredo para o nome de utilizador do registo denominado `REGISTRY_USERNAME` . 

3. Defina um novo segredo para a senha de registo chamada `REGISTRY_PASSWORD` . 

## <a name="build-the-container-image"></a>Construa a imagem do recipiente

O exemplo a seguir mostra parte do fluxo de trabalho que constrói uma imagem Node.JS Docker. Utilize [o Docker Login](https://github.com/azure/docker-login) para iniciar sessão num registo privado de contentores. Este exemplo utiliza o Registo do Contentor Azure, mas a mesma ação funciona para outros registos. 


```yaml
name: Linux Container Node Workflow

on: [push]

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v2
    - uses: azure/docker-login@v1
      with:
        login-server: mycontainer.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}
    - run: |
        docker build . -t mycontainer.azurecr.io/myapp:${{ github.sha }}
        docker push mycontainer.azurecr.io/myapp:${{ github.sha }}     
```

Também pode utilizar [o Docker Login](https://github.com/azure/docker-login) para iniciar sessão em vários registos de contentores ao mesmo tempo. Este exemplo inclui dois novos segredos do GitHub para autenticação com docker.io.

```yml
name: Linux Container Node Workflow

on: [push]

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v2
    - uses: azure/docker-login@v1
      with:
        login-server: mycontainer.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}
    - uses: azure/docker-login@v1
      with:
        login-server: index.docker.io
        username: ${{ secrets.DOCKERIO_USERNAME }}
        password: ${{ secrets.DOCKERIO_PASSWORD }}
    - run: |
        docker build . -t mycontainer.azurecr.io/myapp:${{ github.sha }}
        docker push mycontainer.azurecr.io/myapp:${{ github.sha }}     
```

## <a name="deploy-to-an-app-service-container"></a>Implementar para um recipiente de Serviço de Aplicações

Para implementar a sua imagem num recipiente personalizado no Serviço de Aplicações, utilize a `azure/webapps-deploy@v2` ação. Esta ação tem cinco parâmetros:

| **Parâmetro**  | **Explicação**  |
|---------|---------|
| **nome de aplicativo** | (Obrigatório) Nome da app App Service | 
| **perfil de publicação** | (Opcional) Publique conteúdos de ficheiros de perfil com segredos de implementação da Web |
| **imagens** | Nome de imagem de recipiente totalmente qualificado. Por exemplo, "myregistry.azurecr.io/nginx:latest" ou "python:3.7.2-alpine/". Para cenário de vários contentores podem ser fornecidos vários nomes de imagem de contentores (separados multi-linhas) |
| **nome slot** | (Opcional) Introduza uma ranhura existente que não seja a ranhura de produção |
| **ficheiro de configuração** | (Opcional) Caminho do arquivo Docker-Compose |

# <a name="publish-profile"></a>[Publicar perfil](#tab/publish-profile)

```yaml
name: Linux Container Node Workflow

on: [push]

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v2

    - uses: azure/docker-login@v1
      with:
        login-server: mycontainer.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}

    - run: |
        docker build . -t mycontainer.azurecr.io/myapp:${{ github.sha }}
        docker push mycontainer.azurecr.io/myapp:${{ github.sha }}     

    - uses: azure/webapps-deploy@v2
      with:
        app-name: 'myapp'
        publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
        images: 'mycontainer.azurecr.io/myapp:${{ github.sha }}'
```
# <a name="service-principal"></a>[Diretor de serviços](#tab/service-principal)

```yaml
on: [push]

name: Linux_Container_Node_Workflow

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    # checkout the repo
    - name: 'Checkout GitHub Action' 
      uses: actions/checkout@master
    
    - name: 'Login via Azure CLI'
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    
    - uses: azure/docker-login@v1
      with:
        login-server: mycontainer.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}
    - run: |
        docker build . -t mycontainer.azurecr.io/myapp:${{ github.sha }}
        docker push mycontainer.azurecr.io/myapp:${{ github.sha }}     
      
    - uses: azure/webapps-deploy@v2
      with:
        app-name: 'myapp'
        images: 'mycontainer.azurecr.io/myapp:${{ github.sha }}'
    
    - name: Azure logout
      run: |
        az logout
```

---

## <a name="next-steps"></a>Passos seguintes

Você pode encontrar o nosso conjunto de Ações agrupadas em diferentes repositórios no GitHub, cada um contendo documentação e exemplos para ajudá-lo a usar GitHub para CI/CD e implementar suas aplicações para Azure.

- [Fluxos de trabalho de ações para implantar para Azure](https://github.com/Azure/actions-workflow-samples)

- [Início de sessão no Azure](https://github.com/Azure/login)

- [Azure WebApp](https://github.com/Azure/webapps-deploy)

- [Login/logout do Docker](https://github.com/Azure/docker-login)

- [Eventos que acionam fluxos de trabalho](https://help.github.com/en/articles/events-that-trigger-workflows)

- [Implantação de K8s](https://github.com/Azure/k8s-deploy)

- [Fluxos de trabalho de arranque](https://github.com/actions/starter-workflows)