---
title: Recipiente personalizado CI/CD das ações do GitHub
description: Saiba como utilizar as ações do GitHub para implantar o seu recipiente Linux personalizado para o Serviço de Aplicações a partir de um pipeline CI/CD.
ms.devlang: na
ms.topic: article
ms.date: 10/25/2019
ms.author: jafreebe
ms.reviewer: ushan
ms.openlocfilehash: d5f175d887cec1d5b5e567d3f716e6492f4516dd
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "78246977"
---
# <a name="deploy-a-custom-container-to-app-service-using-github-actions"></a>Implementar um recipiente personalizado para o Serviço de Aplicações utilizando ações do GitHub

[GitHub Actions](https://help.github.com/en/articles/about-github-actions) dá-lhe a flexibilidade para construir um fluxo de trabalho de ciclo de vida de desenvolvimento de software automatizado. Com a [Azure App Service Action for Containers,](https://github.com/Azure/webapps-container-deploy)pode automatizar o seu fluxo de trabalho para implementar aplicações como [recipientes personalizados para o Serviço de Aplicações](https://azure.microsoft.com/services/app-service/containers/) utilizando As ações do GitHub.

> [!IMPORTANT]
> GitHub Actions está atualmente em versão beta. Primeiro tem [de se inscrever para se juntar à pré-visualização](https://github.com/features/actions) utilizando a sua conta GitHub.
> 

Um fluxo de trabalho é definido por um ficheiro YAML (.yml) no caminho do `/.github/workflows/` seu repositório. Esta definição contém os vários passos e parâmetros que compõem o fluxo de trabalho.

Para um fluxo de trabalho do serviço de aplicações Azure, o ficheiro tem três secções:

|Section  |Tarefas  |
|---------|---------|
|**Autenticação** | 1. Defina um diretor de serviço. <br /> 2. Criar um segredo do GitHub. |
|**Compilação** | 1. Criar o ambiente. <br /> 2. Construa a imagem do recipiente. |
|**Implementar** | 1. Desdobre a imagem do recipiente. |

## <a name="create-a-service-principal"></a>Criar um principal de serviço

Pode criar um [principal de serviço](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals#service-principal-object) utilizando o comando [ad sp create-for-rbac](https://docs.microsoft.com/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) no [Azure CLI](https://docs.microsoft.com/cli/azure/). Pode executar este comando utilizando [a Azure Cloud Shell](https://shell.azure.com/) no portal Azure ou selecionando o botão **Tentar.**

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor \
                            --scopes /subscriptions/{subscription-id}/resourceGroups/{resource-group} \
                            --sdk-auth
                            
# Replace {subscription-id}, {resource-group} with the subscription, resource group details of the WebApp
```

A saída é um objeto JSON com as credenciais de atribuição de funções que fornecem acesso à sua aplicação de Serviço de Aplicações semelhante abaixo. Copie este objeto JSON para autenticar do GitHub.

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
> É sempre uma boa prática conceder o mínimo acesso. Pode restringir o âmbito no comando Az CLI acima referido à aplicação específica do Serviço de Aplicações e ao Registo de Contentores Azure, onde as imagens do contentor são empurradas para.

## <a name="configure-the-github-secret"></a>Configure o segredo do GitHub

O exemplo abaixo utiliza credenciais de nível de utilizador, ou seja, o diretor de serviço azure para implantação. Siga os passos para configurar o segredo:

1. No [GitHub,](https://github.com/)navegue no seu repositório, selecione **Definições > Segredos > Adicionar um novo segredo**

2. Cole o conteúdo do comando abaixo `az cli` como o valor da variável secreta. Por exemplo, `AZURE_CREDENTIALS`.

    
    ```azurecli
    az ad sp create-for-rbac --name "myApp" --role contributor \
                                --scopes /subscriptions/{subscription-id}/resourceGroups/{resource-group} \
                                --sdk-auth
                                
    # Replace {subscription-id}, {resource-group} with the subscription, resource group details
    ```

3. Agora, no ficheiro de fluxo de trabalho na sua sucursal: `.github/workflows/workflow.yml` substitua o segredo na ação de login do Azure pelo seu segredo.

4. Da mesma forma, defina os seguintes segredos adicionais para as credenciais de registo do contentor e coloque-os em ação de login do Docker. 

    - REGISTRY_USERNAME
    - REGISTRY_PASSWORD

5. Você vê os segredos como mostrado abaixo uma vez definidos.

    ![segredos de contentor](../media/app-service-github-actions/app-service-secrets-container.png)

## <a name="build-the-container-image"></a>Construa a imagem do recipiente

O exemplo a seguir mostra parte do fluxo de trabalho que constrói a imagem do estivador.

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
        login-server: contoso.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}
    
    - run: |
        docker build . -t contoso.azurecr.io/nodejssampleapp:${{ github.sha }}
        docker push contoso.azurecr.io/nodejssampleapp:${{ github.sha }}
```

## <a name="deploy-to-an-app-service-container"></a>Implementar para um recipiente de Serviço de Aplicações

Para implementar a sua imagem num recipiente personalizado no Serviço de Aplicações, utilize a `azure/webapps-container-deploy@v1` ação. Esta ação tem cinco parâmetros:

| **Parâmetro**  | **Explicação**  |
|---------|---------|
| **nome de aplicativo** | (Obrigatório) Nome da app App Service | 
| **nome slot** | (Opcional) Introduza uma ranhura existente que não seja a ranhura de produção |
| **imagens** | (Obrigatório) Especificar o(s) nome de imagem de recipiente totalmente qualificado. Por exemplo, "myregistry.azurecr.io/nginx:latest" ou "python:3.7.2-alpine/". Para uma aplicação multi-contentor, podem ser fornecidos vários nomes de imagem de contentor (separados multi-linhas) |
| **ficheiro de configuração** | (Opcional) Caminho do ficheiro Docker-Compose. Deve ser um caminho totalmente qualificado ou em relação ao diretório de trabalho predefinido. Necessário para aplicações multi-contentores. |
| **comando de contentores** | (Opcional) Insira o comando de arranque. Para ex. dotnet run ou dotnet filename.dll |

Abaixo está o fluxo de trabalho da amostra para construir e implementar uma aplicação Node.js para um recipiente personalizado no Serviço de Aplicações.

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

## <a name="next-steps"></a>Próximos passos

Você pode encontrar o nosso conjunto de Ações agrupadas em diferentes repositórios no GitHub, cada um contendo documentação e exemplos para ajudá-lo a usar GitHub para CI/CD e implementar suas aplicações para Azure.

- [Início de sessão no Azure](https://github.com/Azure/login)

- [Azure WebApp](https://github.com/Azure/webapps-deploy)

- [Azure WebApp para contentores](https://github.com/Azure/webapps-container-deploy)

- [Login/logout do Docker](https://github.com/Azure/docker-login)

- [Eventos que desencadeiam fluxos de trabalho](https://help.github.com/en/articles/events-that-trigger-workflows)

- [Implantação de K8s](https://github.com/Azure/k8s-deploy)

- [Fluxos de trabalho ci iniciantes](https://github.com/actions/starter-workflows)

- [Fluxos de trabalho de arranque para implantar para Azure](https://github.com/Azure/actions-workflow-samples)
