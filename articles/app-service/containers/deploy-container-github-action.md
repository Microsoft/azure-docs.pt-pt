---
title: Implantar o contêiner de um pipeline de CI/CD com ações do GitHub-serviço de Azure App | Microsoft Docs
description: Saiba como usar as ações do GitHub para implantar seu contêiner no serviço de aplicativo
services: app-service
documentationcenter: ''
author: jasonfreeberg
writer: ''
manager: ''
editor: ''
ms.assetid: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/09/2019
ms.author: jafreebe
ms.openlocfilehash: 2341eba2c24c06d654c9d2eeda96788d168fe27c
ms.sourcegitcommit: ec2b75b1fc667c4e893686dbd8e119e7c757333a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72809815"
---
# <a name="github-actions-for-deploying-to-web-app-for-containers"></a>Ações do GitHub para implantação no Aplicativo Web para Contêineres

As [ações do GitHub](https://help.github.com/en/articles/about-github-actions) oferecem a flexibilidade para criar um fluxo de trabalho de desenvolvimento automatizado do software. Com as ações de serviço Azure App para o GitHub, você pode automatizar o fluxo de trabalho para implantar [aplicativos Web do Azure para contêineres](https://azure.microsoft.com/services/app-service/containers/) usando ações do github.

> [!IMPORTANT]
> No momento, as ações do GitHub estão em beta. Primeiro, você deve [se inscrever para ingressar na versão prévia](https://github.com/features/actions) usando sua conta do github.
> 

Um fluxo de trabalho é definido por um arquivo YAML (. yml) no caminho `/.github/workflows/` em seu repositório. Essa definição contém as várias etapas e parâmetros que compõem o fluxo de trabalho.

Para um fluxo de trabalho de contêiner do aplicativo Web do Azure, o arquivo tem três seções:

|Section  |Tarefas  |
|---------|---------|
|**Autenticação** | 1. definir uma entidade de serviço <br /> 2. criar um segredo do GitHub |
|**Integrado** | 1. configurar o ambiente <br /> 2. criar a imagem de contêiner |
|**Implementar** | 1. implantar a imagem de contêiner |

## <a name="create-a-service-principal"></a>Criar um principal de serviço

Você pode criar uma [entidade de serviço](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals#service-principal-object) usando o comando [AZ ad SP Create-for-RBAC](https://docs.microsoft.com/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) na [CLI do Azure](https://docs.microsoft.com/cli/azure/). Você pode executar esse comando usando [Azure cloud Shell](https://shell.azure.com/) na portal do Azure ou selecionando o botão **experimentar** .

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP>/providers/Microsoft.Web/sites/<APP_NAME> --sdk-auth
```

Neste exemplo, substitua os espaços reservados no recurso por sua ID de assinatura, grupo de recursos e nome do aplicativo Web. A saída são as credenciais de atribuição de função que fornecem acesso ao seu aplicativo Web. Copie esse objeto JSON, que você pode usar para autenticar do GitHub.

> [!NOTE]
> Você não precisará criar uma entidade de serviço se decidir usar o perfil de publicação para autenticação.

> [!IMPORTANT]
> É sempre uma boa prática conceder acesso mínimo. É por isso que o escopo no exemplo anterior é limitado ao aplicativo Web específico e não ao grupo de recursos inteiro.

## <a name="configure-the-github-secret"></a>Configurar o segredo do GitHub

O exemplo abaixo usa credenciais de nível de usuário, ou seja, entidade de serviço do Azure para implantação. Siga as etapas para configurar o segredo:

1. No [GitHub](https://github.com/), procure seu repositório, selecione **configurações > segredos > Adicionar um novo segredo**

2. Cole o conteúdo do comando a seguir `az cli` como o valor da variável secreta. Por exemplo, `AZURE_CREDENTIALS`.

    
    ```azurecli
    az ad sp create-for-rbac --name "myApp" --role contributor \
                                --scopes /subscriptions/{subscription-id}/resourceGroups/{resource-group} \
                                --sdk-auth
                                
    # Replace {subscription-id}, {resource-group} with the subscription, resource group details
    ```

3. Agora, no arquivo de fluxo de trabalho em seu Branch: `.github/workflows/workflow.yml` substituir o segredo na ação de logon do Azure pelo seu segredo.

4. Da mesma forma, defina os seguintes segredos adicionais para as credenciais de registro de contêiner e defina-as na ação de logon do Docker. 

    - REGISTRY_USERNAME
    - REGISTRY_PASSWORD

5. Você verá os segredos conforme mostrado abaixo, uma vez definidos.

    ![segredos do contêiner](../media/app-service-github-actions/app-service-secrets-container.png)

## <a name="build-the-container-image"></a>Criar a imagem de contêiner

O exemplo a seguir mostra parte do fluxo de trabalho que cria a imagem do Docker.

```yaml
on: [push]

name: Linux_Container_Node_Workflow

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    # checkout the repo
    - name: 'Checkout Github Action' 
      uses: actions/checkout@master
    
    - name: 'Login via Azure CLI'
      uses: azure/actions/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    
    - uses: azure/docker-login@v1
      with:
        login-server: contoso.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}
    
    - run: |
        docker build . -t contoso.azurecr.io/nodejssampleapp:${{ github.sha }}
        docker push contoso.azurecr.io/nodejssampleapp:${{ github.sha }}
```

## <a name="deploy-to-web-app-container"></a>Implantar no contêiner do aplicativo Web

Para implantar a imagem em um contêiner de aplicativo Web, você precisará usar a ação `Azure/appservice-actions/webapp@master`. Esta ação tem 5 parâmetros:

| **Meter**  | **Explicação**  |
|---------|---------|
| **nome do aplicativo** | Necessária Nome do aplicativo Web do Azure | 
| **nome do slot** | Adicional Insira um slot existente que não seja o slot de produção |
| **imagens** | Necessária Especifique o nome da (s) imagem (ns) do contêiner totalmente qualificado. Por exemplo, ' myregistry.azurecr.io/nginx:latest ' ou ' Python: 3.7.2-Alpine/'. Para cenários de vários contêineres, vários nomes de imagem de contêiner podem ser fornecidos (separados por várias linhas) |
| **arquivo de configuração** | Adicional Caminho do arquivo Docker-Compose. Deve ser um caminho totalmente qualificado ou relativo ao diretório de trabalho padrão. Necessário para o cenário de vários contêineres |
| **contêiner-comando** | Adicional Insira o comando de inicialização. Por exemplo, dotnet Run ou dotnet nome_do_arquivo. dll |

Veja abaixo o fluxo de trabalho de exemplo para criar e implantar um aplicativo Web node. js no contêiner do aplicativo Web do Azure.

```yaml
on: [push]

name: Linux_Container_Node_Workflow

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    # checkout the repo
    - name: 'Checkout Github Action' 
      uses: actions/checkout@master
    
    - name: 'Login via Azure CLI'
      uses: azure/actions/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    
    - uses: azure/docker-login@v1
      with:
        login-server: contoso.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}
    
    - run: |
        docker build . -t contoso.azurecr.io/nodejssampleapp:${{ github.sha }}
        docker push contoso.azurecr.io/nodejssampleapp:${{ github.sha }} 
      
    - uses: azure/webapps-container-deploy@v1
      with:
        app-name: 'node-rnc'
        images: 'contoso.azurecr.io/nodejssampleapp:${{ github.sha }}'
    
    - name: Azure logout
      run: |
        az logout
```

## <a name="next-steps"></a>Passos seguintes

Você pode encontrar nosso conjunto de ações agrupadas em repositórios diferentes no GitHub, cada uma contendo documentação e exemplos para ajudá-lo a usar o GitHub para CI/CD e implantar seus aplicativos no Azure.

- [Logon do Azure](https://github.com/Azure/actions)

- [WebApp do Azure](https://github.com/Azure/webapps-deploy)

- [Azure WebApp para contêineres](https://github.com/Azure/webapps-container-deploy)

- [Logon/logout do Docker](https://github.com/Azure/docker-login)

- [Eventos que disparam fluxos de trabalho](https://help.github.com/en/articles/events-that-trigger-workflows)

- [K8s implantar](https://github.com/Azure/k8s-deploy)

- [Fluxos de trabalho iniciais](https://github.com/actions/starter-workflows)
