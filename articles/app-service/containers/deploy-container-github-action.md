---
title: Ci/CD de recipiente personalizado das ações do GitHub
description: Aprenda a utilizar as Ações GitHub para implantar o seu recipiente Linux personalizado para o Serviço de Aplicações a partir de um pipeline CI/CD.
ms.devlang: na
ms.topic: article
ms.date: 10/25/2019
ms.author: jafreebe
ms.reviewer: ushan
ms.openlocfilehash: f32ea2ae0be66259ff153c24bfd10e179fddbbe5
ms.sourcegitcommit: dd3db8d8d31d0ebd3e34c34b4636af2e7540bd20
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/22/2020
ms.locfileid: "77559033"
---
# <a name="deploy-a-custom-container-to-app-service-using-github-actions"></a>Implemente um recipiente personalizado para o Serviço de Aplicações utilizando ações gitHub

[GitHub Actions](https://help.github.com/en/articles/about-github-actions) dá-lhe a flexibilidade para construir um fluxo de trabalho de ciclo de vida de desenvolvimento de software automatizado. Com a Ação de Serviço de [Aplicações Azure para Contentores,](https://github.com/Azure/webapps-container-deploy)pode automatizar o seu fluxo de trabalho para implementar aplicações como [recipientes personalizados para](https://azure.microsoft.com/services/app-service/containers/) o App Service utilizando as Ações GitHub.

> [!IMPORTANT]
> A GitHub Actions está atualmente em versão beta. Primeiro deve [inscrever-se para se juntar à pré-visualização](https://github.com/features/actions) utilizando a sua conta GitHub.
> 

Um fluxo de trabalho é definido por um ficheiro YAML (.yml) no caminho `/.github/workflows/` no seu repositório. Esta definição contém os vários passos e parâmetros que compõem o fluxo de trabalho.

Para um fluxo de trabalho de contentor estoque do Serviço de Aplicações Azure, o ficheiro tem três secções:

|Section  |Tarefas  |
|---------|---------|
|**Autenticação** | 1. Defina um diretor de serviço. <br /> 2. Criar um segredo GitHub. |
|**Construir** | 1. Instale o ambiente. <br /> 2. Construa a imagem do recipiente. |
|**Implementar** | 1. Implante a imagem do recipiente. |

## <a name="create-a-service-principal"></a>Criar um principal de serviço

Pode criar um diretor de [serviço](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals#service-principal-object) utilizando o comando [az ad sp create-for-rbac](https://docs.microsoft.com/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) no [Azure CLI](https://docs.microsoft.com/cli/azure/). Pode executar este comando utilizando a [Azure Cloud Shell](https://shell.azure.com/) no portal Azure ou selecionando o botão **Experimente..**

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor \
                            --scopes /subscriptions/{subscription-id}/resourceGroups/{resource-group} \
                            --sdk-auth
                            
  # Replace {subscription-id}, {resource-group} with the subscription, resource group details of the WebApp
```

A saída é um objeto JSON com as credenciais de atribuição de funções que fornecem acesso à sua app App Service semelhante a abaixo. Copie este objeto JSON para autenticar a partir do GitHub.

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
> É sempre uma boa prática conceder o mínimo de acesso. Pode restringir o âmbito no comando Az CLI acima da aplicação app Service e no Registo de Contentores Azure para onde as imagens do contentor são empurradas.

## <a name="configure-the-github-secret"></a>Configure o segredo GitHub

O exemplo abaixo utiliza credenciais de nível de utilizador, ou assim, o Diretor de Serviço Azure para implantação. Siga os passos para configurar o segredo:

1. No [GitHub,](https://github.com/)navegue no seu repositório, selecione **Definições > Secrets > Adicione um novo segredo**

2. Colar o conteúdo do comando abaixo `az cli` como o valor da variável secreta. Por exemplo, `AZURE_CREDENTIALS`.

    
    ```azurecli
    az ad sp create-for-rbac --name "myApp" --role contributor \
                                --scopes /subscriptions/{subscription-id}/resourceGroups/{resource-group} \
                                --sdk-auth
                                
    # Replace {subscription-id}, {resource-group} with the subscription, resource group details
    ```

3. Agora, no ficheiro workflow da sua filial: `.github/workflows/workflow.yml` substituir o segredo em ação de login azure pelo seu segredo.

4. Da mesma forma, defina os seguintes segredos adicionais para as credenciais de registo de contentores e coloque-os em ação de login do Docker. 

    - REGISTRY_USERNAME
    - REGISTRY_PASSWORD

5. Vê os segredos como mostrados abaixo uma vez definidos.

    ![segredos de contentores](../media/app-service-github-actions/app-service-secrets-container.png)

## <a name="build-the-container-image"></a>Construa a imagem do recipiente

O exemplo seguinte mostra parte do fluxo de trabalho que constrói a imagem do estivador.

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

## <a name="deploy-to-an-app-service-container"></a>Desloque para um recipiente de serviço de aplicações

Para implementar a sua imagem num recipiente personalizado no Serviço de Aplicações, utilize a ação `azure/webapps-container-deploy@v1`. Esta ação tem cinco parâmetros:

| **Parâmetro**  | **Explicação**  |
|---------|---------|
| **nome de aplicativo** | (Obrigatório) Nome da app App Service | 
| **slot-name** | (Opcional) Introduza uma ranhura existente que não seja a ranhura de produção |
| **imagens** | (Obrigatório) Especifique o nome(s) de imagem do recipiente totalmente qualificado. Por exemplo, 'myregistry.azurecr.io/nginx:latest' ou 'python:3.7.2-alpine/'. Para uma aplicação multi-contentor, podem ser fornecidos vários nomes de imagens de contentores (separados em várias linhas) |
| **arquivo de configuração** | (Opcional) Caminho do ficheiro Docker-Compose. Deve ser um caminho totalmente qualificado ou relativo ao diretório de trabalho padrão. Necessário para aplicações multi-contentores. |
| **comando de contentores** | (Opcional) Insira o comando de arranque. Para ex. dotnet executar ou dotnet filename.dll |

Abaixo está o fluxo de trabalho da amostra para construir e implementar uma app Node.js para um recipiente personalizado no App Service.

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

## <a name="next-steps"></a>Passos seguintes

Pode encontrar o nosso conjunto de Ações agrupadas em diferentes repositórios no GitHub, cada um contendo documentação e exemplos para ajudá-lo a usar o GitHub para CI/CD e implementar as suas aplicações para o Azure.

- [Login azure](https://github.com/Azure/login)

- [Azure WebApp](https://github.com/Azure/webapps-deploy)

- [Azure WebApp para contentores](https://github.com/Azure/webapps-container-deploy)

- [Docker login/logout](https://github.com/Azure/docker-login)

- [Eventos que desencadeiam fluxos de trabalho](https://help.github.com/en/articles/events-that-trigger-workflows)

- [Implantação de K8s](https://github.com/Azure/k8s-deploy)

- [Fluxos de trabalho de CI de arranque](https://github.com/actions/starter-workflows)

- [Fluxos de trabalho de arranque para implantar para Azure](https://github.com/Azure/actions-workflow-samples)
