---
title: Implantar o contêiner de um pipeline de CI/CD com ações do GitHub-serviço de Azure App | Microsoft Docs
description: Saiba como usar as ações do GitHub para implantar seu contêiner no serviço de aplicativo
services: app-service
documentationcenter: ''
author: cephalin
manager: gwallace
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/25/2019
ms.author: jafreebe
ms.reviewer: ushan
ms.openlocfilehash: 7fbd7b571f5590ff35d52062cc621069a47b619c
ms.sourcegitcommit: 6c2c97445f5d44c5b5974a5beb51a8733b0c2be7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/05/2019
ms.locfileid: "73620220"
---
# <a name="deploy-a-custom-container-to-app-service-using-github-actions"></a>Implantar um contêiner personalizado no serviço de aplicativo usando as ações do GitHub

As [ações do GitHub](https://help.github.com/en/articles/about-github-actions) oferecem a flexibilidade para criar um fluxo de trabalho de desenvolvimento automatizado do software. Com a [ação de serviço Azure app para contêineres](https://github.com/Azure/webapps-container-deploy), você pode automatizar o fluxo de trabalho para implantar aplicativos como [contêineres personalizados no serviço de aplicativo](https://azure.microsoft.com/services/app-service/containers/) usando ações do github.

> [!IMPORTANT]
> No momento, as ações do GitHub estão em beta. Primeiro, você deve [se inscrever para ingressar na versão prévia](https://github.com/features/actions) usando sua conta do github.
> 

Um fluxo de trabalho é definido por um arquivo YAML (. yml) no caminho `/.github/workflows/` em seu repositório. Essa definição contém as várias etapas e parâmetros que compõem o fluxo de trabalho.

Para um fluxo de trabalho de contêiner de serviço Azure App, o arquivo tem três seções:

|Section  |Tarefas  |
|---------|---------|
|**Autenticação** | 1. definir uma entidade de serviço. <br /> 2. Crie um segredo do GitHub. |
|**Integrado** | 1. configurar o ambiente. <br /> 2. Crie a imagem de contêiner. |
|**Implementar** | 1. implante a imagem de contêiner. |

## <a name="create-a-service-principal"></a>Criar um principal de serviço

Você pode criar uma [entidade de serviço](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals#service-principal-object) usando o comando [AZ ad SP Create-for-RBAC](https://docs.microsoft.com/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) na [CLI do Azure](https://docs.microsoft.com/cli/azure/). Você pode executar esse comando usando [Azure cloud Shell](https://shell.azure.com/) na portal do Azure ou selecionando o botão **experimentar** .

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor \
                            --scopes /subscriptions/{subscription-id}/resourceGroups/{resource-group} \
                            --sdk-auth
                            
  # Replace {subscription-id}, {resource-group} with the subscription, resource group details of the WebApp
```

A saída é um objeto JSON com as credenciais de atribuição de função que fornecem acesso ao seu aplicativo do serviço de aplicativo semelhante ao mostrado abaixo. Copie este objeto JSON para autenticar do GitHub.

 ```azurecli 
  {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
  }
```

> [!IMPORTANT]
> É sempre uma boa prática conceder acesso mínimo. Você pode restringir o escopo no comando AZ CLI acima para o aplicativo do serviço de aplicativo específico e o registro de contêiner do Azure no qual as imagens de contêiner são enviadas por push.

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
      uses: azure/login@v1
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

## <a name="deploy-to-an-app-service-container"></a>Implantar em um contêiner do serviço de aplicativo

Para implantar a imagem em um contêiner personalizado no serviço de aplicativo, use a ação `azure/webapps-container-deploy@v1`. Esta ação tem cinco parâmetros:

| **Meter**  | **Explicação**  |
|---------|---------|
| **nome do aplicativo** | Necessária Nome do aplicativo do serviço de aplicativo | 
| **nome do slot** | Adicional Insira um slot existente que não seja o slot de produção |
| **imagens** | Necessária Especifique o nome da (s) imagem (ns) do contêiner totalmente qualificado. Por exemplo, ' myregistry.azurecr.io/nginx:latest ' ou ' Python: 3.7.2-Alpine/'. Para um aplicativo de vários contêineres, vários nomes de imagem de contêiner podem ser fornecidos (separados por várias linhas) |
| **arquivo de configuração** | Adicional Caminho do arquivo Docker-Compose. Deve ser um caminho totalmente qualificado ou relativo ao diretório de trabalho padrão. Necessário para aplicativos de vários contêineres. |
| **contêiner-comando** | Adicional Insira o comando de inicialização. Por exemplo, dotnet Run ou dotnet nome_do_arquivo. dll |

Veja abaixo o fluxo de trabalho de exemplo para criar e implantar um aplicativo node. js em um contêiner personalizado no serviço de aplicativo.

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
      uses: azure/login@v1
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

- [Logon do Azure](https://github.com/Azure/login)

- [WebApp do Azure](https://github.com/Azure/webapps-deploy)

- [Azure WebApp para contêineres](https://github.com/Azure/webapps-container-deploy)

- [Logon/logout do Docker](https://github.com/Azure/docker-login)

- [Eventos que disparam fluxos de trabalho](https://help.github.com/en/articles/events-that-trigger-workflows)

- [K8s implantar](https://github.com/Azure/k8s-deploy)

- [Fluxos de trabalho de CI inicial](https://github.com/actions/starter-workflows)

- [Fluxos de trabalho iniciais para implantar no Azure](https://github.com/Azure/actions-workflow-samples)
