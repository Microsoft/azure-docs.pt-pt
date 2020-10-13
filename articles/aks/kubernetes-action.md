---
title: Construir, testar e implantar contentores para o Serviço Azure Kubernetes usando ações do GitHub
description: Saiba como usar as ações do GitHub para implantar o seu contentor em Kubernetes
services: container-service
author: azooinmyluggage
ms.topic: article
ms.date: 11/04/2019
ms.author: atulmal
ms.openlocfilehash: 7743a3a8d6e77affd6229b648ab79b5b2f07a0af
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90564105"
---
# <a name="github-actions-for-deploying-to-kubernetes-service"></a>GitHub Actions para implantação no serviço Kubernetes

[GitHub Actions](https://help.github.com/en/articles/about-github-actions) dá-lhe a flexibilidade para construir um fluxo de trabalho de ciclo de vida de desenvolvimento de software automatizado. A ação Kubernetes [azure/aks-set-context@v1](https://github.com/Azure/aks-set-context) facilita as implementações para os clusters de serviçoS Azure Kubernetes. A ação define o contexto de cluster AKS alvo, que poderia ser usado por outras ações como [azure/k8s-deploy,](https://github.com/Azure/k8s-deploy/tree/master) [azure/k8s-create-secret](https://github.com/Azure/k8s-create-secret/tree/master) etc. ou executar quaisquer comandos kubectl.

Um fluxo de trabalho é definido por um ficheiro YAML (.yml) no caminho do `/.github/workflows/` seu repositório. Esta definição contém os vários passos e parâmetros que compõem o fluxo de trabalho.

Para um fluxo de trabalho direcionado para a AKS, o ficheiro tem três secções:

|Section  |Tarefas  |
|---------|---------|
|**Autenticação** | Iniciar sessão num registo privado de contentores (ACR) |
|**Construir** | Construa & empurre a imagem do recipiente  |
|**Implementar** | 1. Definir o cluster AKS alvo |
| |2. Criar um segredo genérico/estivador-registo no cluster Kubernetes  |
||3. Implantar no cluster Kubernetes|

## <a name="create-a-service-principal"></a>Criar um principal de serviço

Pode criar um [principal de serviço](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) utilizando o comando [ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) no [Azure CLI](/cli/azure/). Pode executar este comando utilizando [a Azure Cloud Shell](https://shell.azure.com/) no portal Azure ou selecionando o botão **Tentar.**

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP> --sdk-auth
```

No comando acima, substitua os espaços reservados pelo seu ID de subscrição e grupo de recursos. A saída são as credenciais de atribuição de funções que fornecem acesso ao seu recurso. O comando deve descoduar um objeto JSON semelhante a este.

```json
  {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
  }
```
Copie este objeto JSON, que pode utilizar para autenticar a partir do GitHub.

## <a name="configure-the-github-secrets"></a>Configure os segredos do GitHub

Siga os passos para configurar os segredos:

1. No [GitHub,](https://github.com/)navegue no seu repositório, selecione **Definições > Segredos > Adicione um novo segredo**.

    ![A screenshot mostra o Add uma nova ligação secreta para um repositório.](media/kubernetes-action/secrets.png)

2. Cole o conteúdo do comando acima `az cli` como o valor da variável secreta. Por exemplo, `AZURE_CREDENTIALS`.

3. Da mesma forma, defina os seguintes segredos adicionais para as credenciais de registo do contentor e coloque-os em ação de login do Docker. 

    - REGISTRY_USERNAME
    - REGISTRY_PASSWORD

4. Verá os segredos como mostrado abaixo uma vez definidos.

    ![A screenshot mostra os segredos existentes para um repositório.](media/kubernetes-action/kubernetes-secrets.png)

##  <a name="build-a-container-image-and-deploy-to-azure-kubernetes-service-cluster"></a>Construa uma imagem de contentor e implemente para o cluster de serviçoS Azure Kubernetes

A construção e o impulso das imagens do recipiente são feitos usando `Azure/docker-login@v1` a ação. Para colocar uma imagem de recipiente na AKS, terá de utilizar a `Azure/k8s-deploy@v1` ação. Esta ação tem cinco parâmetros:

| **Parâmetro**  | **Explicação**  |
|---------|---------|
| **espaço de nome** | (Opcional) Escolha o espaço de nomes target Kubernetes. Se o espaço de nome não for fornecido, os comandos serão executados no espaço de nome predefinido | 
| **manifestos** |  (Obrigatório) Caminho para os ficheiros manifestos, que serão usados para implantação |
| **imagens** | (Opcional) URL de recursos totalmente qualificados das imagens a utilizar para substituições nos ficheiros manifestos |
| **imagenspullsecrets** | (Opcional) Nome de um segredo de registo de estivador que já foi criado dentro do aglomerado. Cada um destes nomes secretos é adicionado no campo ImagePullSecrets para as cargas de trabalho encontradas nos ficheiros manifestos de entrada |
| **kubectl-versão** | (Opcional) Instala uma versão específica do binário kubectl |

### <a name="deploy-to-azure-kubernetes-service-cluster"></a>Implementar para o cluster de serviços Azure Kubernetes

Fluxo de trabalho final para construir imagens de contentores e implantar para um cluster de serviço Azure Kubernetes.

```yaml
on: [push]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@master
    
    - uses: Azure/docker-login@v1
      with:
        login-server: contoso.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}
    
    - run: |
        docker build . -t contoso.azurecr.io/k8sdemo:${{ github.sha }}
        docker push contoso.azurecr.io/k8sdemo:${{ github.sha }}
      
    # Set the target AKS cluster.
    - uses: Azure/aks-set-context@v1
      with:
        creds: '${{ secrets.AZURE_CREDENTIALS }}'
        cluster-name: contoso
        resource-group: contoso-rg
        
    - uses: Azure/k8s-create-secret@v1
      with:
        container-registry-url: contoso.azurecr.io
        container-registry-username: ${{ secrets.REGISTRY_USERNAME }}
        container-registry-password: ${{ secrets.REGISTRY_PASSWORD }}
        secret-name: demo-k8s-secret

    - uses: Azure/k8s-deploy@v1
      with:
        manifests: |
          manifests/deployment.yml
          manifests/service.yml
        images: |
          demo.azurecr.io/k8sdemo:${{ github.sha }}
        imagepullsecrets: |
          demo-k8s-secret
```

## <a name="next-steps"></a>Passos seguintes

Você pode encontrar o nosso conjunto de ações em diferentes repositórios no GitHub, cada um contendo documentação e exemplos para ajudá-lo a usar GitHub para CI/CD e implementar suas aplicações para Azure.

- [setup-kubectl](https://github.com/Azure/setup-kubectl)

- [k8s-set-context](https://github.com/Azure/k8s-set-context)

- [aks-set-context](https://github.com/Azure/aks-set-context)

- [k8s-criar-segredo](https://github.com/Azure/k8s-create-secret)

- [k8s-implantação](https://github.com/Azure/k8s-deploy)

- [webapps-contentor-implantação](https://github.com/Azure/webapps-container-deploy)

- [acções-amostras de fluxo de trabalho](https://github.com/Azure/actions-workflow-samples)
