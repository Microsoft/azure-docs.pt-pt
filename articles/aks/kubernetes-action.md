---
title: Construir, testar e implantar contentores para o Serviço Azure Kubernetes usando ações do GitHub
description: Saiba como usar as ações do GitHub para implantar o seu contentor em Kubernetes
services: container-service
author: azooinmyluggage
ms.topic: article
ms.date: 11/06/2020
ms.author: atulmal
ms.custom: github-actions-azure
ms.openlocfilehash: 94134360de49a066f825cbb0c85712995d90b37f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98761462"
---
# <a name="github-actions-for-deploying-to-kubernetes-service"></a>GitHub Actions para implantação no serviço Kubernetes

[GitHub Actions](https://docs.github.com/en/actions) dá-lhe a flexibilidade para construir um fluxo de trabalho de ciclo de vida de desenvolvimento de software automatizado. Pode utilizar várias ações de Kubernetes para implantar em contentores desde o Registo de Contentores Azure até ao Serviço Azure Kubernetes com ações do GitHub. 

## <a name="prerequisites"></a>Pré-requisitos 

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Uma conta do GitHub. Se não tiver um, inscreva-se [de graça.](https://github.com/join)  
- Um cluster kubernetes em funcionamento
    - [Tutorial: Preparar uma candidatura para o Serviço Azure Kubernetes](tutorial-kubernetes-prepare-app.md)

## <a name="workflow-file-overview"></a>Visão geral do ficheiro do fluxo de trabalho

Um fluxo de trabalho é definido por um ficheiro YAML (.yml) no caminho do `/.github/workflows/` seu repositório. Esta definição contém os vários passos e parâmetros que compõem o fluxo de trabalho.

Para um fluxo de trabalho direcionado para a AKS, o ficheiro tem três secções:

|Section  |Tarefas  |
|---------|---------|
|**Autenticação** | Iniciar sessão num registo privado de contentores (ACR) |
|**Compilar** | Construa & empurre a imagem do recipiente  |
|**Implementar** | 1. Definir o cluster AKS alvo |
| |2. Criar um segredo genérico/estivador-registo no cluster Kubernetes  |
||3. Implantar no cluster Kubernetes|

## <a name="create-a-service-principal"></a>Criar um principal de serviço

Pode criar um [principal de serviço](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) utilizando o comando [ad sp create-for-rbac](/cli/azure/ad/sp#az-ad-sp-create-for-rbac) no [Azure CLI](/cli/azure/). Pode executar este comando utilizando [a Azure Cloud Shell](https://shell.azure.com/) no portal Azure ou selecionando o botão **Tentar.**

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

A construção e o impulso das imagens do recipiente são feitos usando `Azure/docker-login@v1` a ação. 


```yml
env:
  REGISTRY_NAME: {registry-name}
  CLUSTER_NAME: {cluster-name}
  CLUSTER_RESOURCE_GROUP: {resource-group-name}
  NAMESPACE: {namespace-name}
  APP_NAME: {app-name}
  
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@main
    
    # Connect to Azure Container registry (ACR)
    - uses: azure/docker-login@v1
      with:
        login-server: ${{ env.REGISTRY_NAME }}.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }} 
        password: ${{ secrets.REGISTRY_PASSWORD }}
    
    # Container build and push to a Azure Container registry (ACR)
    - run: |
        docker build . -t ${{ env.REGISTRY_NAME }}.azurecr.io/${{ env.APP_NAME }}:${{ github.sha }}
        docker push ${{ env.REGISTRY_NAME }}.azurecr.io/${{ env.APP_NAME }}:${{ github.sha }}

```

### <a name="deploy-to-azure-kubernetes-service-cluster"></a>Implementar para o cluster de serviços Azure Kubernetes

Para colocar uma imagem de recipiente na AKS, terá de utilizar a `Azure/k8s-deploy@v1` ação. Esta ação tem cinco parâmetros:

| **Parâmetro**  | **Explicação**  |
|---------|---------|
| **espaço de nome** | (Opcional) Escolha o espaço de nomes target Kubernetes. Se o espaço de nome não for fornecido, os comandos serão executados no espaço de nome predefinido | 
| **manifestos** |  (Obrigatório) Caminho para os ficheiros manifestos, que serão usados para implantação |
| **imagens** | (Opcional) URL de recursos totalmente qualificados das imagens a utilizar para substituições nos ficheiros manifestos |
| **imagenspullsecrets** | (Opcional) Nome de um segredo de registo de estivador que já foi criado dentro do aglomerado. Cada um destes nomes secretos é adicionado no campo ImagePullSecrets para as cargas de trabalho encontradas nos ficheiros manifestos de entrada |
| **kubectl-versão** | (Opcional) Instala uma versão específica do binário kubectl |


Antes de poder implantar para a AKS, terá de definir o espaço de nomes de Kubernetes e criar um segredo de imagem. Veja [imagens de um registo de contentores Azure para um cluster Kubernetes,](../container-registry/container-registry-auth-kubernetes.md)para saber mais sobre como as imagens de puxar funcionam. 

```yaml
  # Create namespace if doesn't exist
  - run: |
      kubectl create namespace ${{ env.NAMESPACE }} --dry-run -o json | kubectl apply -f -
  
  # Create image pull secret for ACR
  - uses: azure/k8s-create-secret@v1
    with:
      container-registry-url: ${{ env.REGISTRY_NAME }}.azurecr.io
      container-registry-username: ${{ secrets.REGISTRY_USERNAME }}
      container-registry-password: ${{ secrets.REGISTRY_PASSWORD }}
      secret-name: ${{ env.SECRET }}
      namespace: ${{ env.NAMESPACE }}
      force: true
```


Complete a sua implantação com a `k8s-deploy` ação. Substitua as variáveis ambientais por valores para a sua aplicação. 

```yaml

on: [push]

# Environment variables available to all jobs and steps in this workflow
env:
  REGISTRY_NAME: {registry-name}
  CLUSTER_NAME: {cluster-name}
  CLUSTER_RESOURCE_GROUP: {resource-group-name}
  NAMESPACE: {namespace-name}
  SECRET: {secret-name}
  APP_NAME: {app-name}
  
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@main
    
    # Connect to Azure Container registry (ACR)
    - uses: azure/docker-login@v1
      with:
        login-server: ${{ env.REGISTRY_NAME }}.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }} 
        password: ${{ secrets.REGISTRY_PASSWORD }}
    
    # Container build and push to a Azure Container registry (ACR)
    - run: |
        docker build . -t ${{ env.REGISTRY_NAME }}.azurecr.io/${{ env.APP_NAME }}:${{ github.sha }}
        docker push ${{ env.REGISTRY_NAME }}.azurecr.io/${{ env.APP_NAME }}:${{ github.sha }}
    
    # Set the target Azure Kubernetes Service (AKS) cluster. 
    - uses: azure/aks-set-context@v1
      with:
        creds: '${{ secrets.AZURE_CREDENTIALS }}'
        cluster-name: ${{ env.CLUSTER_NAME }}
        resource-group: ${{ env.CLUSTER_RESOURCE_GROUP }}
    
    # Create namespace if doesn't exist
    - run: |
        kubectl create namespace ${{ env.NAMESPACE }} --dry-run -o json | kubectl apply -f -
    
    # Create image pull secret for ACR
    - uses: azure/k8s-create-secret@v1
      with:
        container-registry-url: ${{ env.REGISTRY_NAME }}.azurecr.io
        container-registry-username: ${{ secrets.REGISTRY_USERNAME }}
        container-registry-password: ${{ secrets.REGISTRY_PASSWORD }}
        secret-name: ${{ env.SECRET }}
        namespace: ${{ env.NAMESPACE }}
        force: true
    
    # Deploy app to AKS
    - uses: azure/k8s-deploy@v1
      with:
        manifests: |
          manifests/deployment.yml
          manifests/service.yml
        images: |
          ${{ env.REGISTRY_NAME }}.azurecr.io/${{ env.APP_NAME }}:${{ github.sha }}
        imagepullsecrets: |
          ${{ env.SECRET }}
        namespace: ${{ env.NAMESPACE }}
```

## <a name="clean-up-resources"></a>Limpar os recursos

Quando o seu cluster Kubernetes, o registo de contentores e o repositório já não forem necessários, limpe os recursos que implementou eliminando o grupo de recursos e o seu repositório GitHub. 

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Saiba mais sobre o Serviço Azure Kubernetes](/azure/architecture/reference-architectures/containers/aks-start-here)

### <a name="more-kubernetes-github-actions"></a>Mais ações do Kubernetes GitHub

* [Instalador de ferramentas Kubectl](https://github.com/Azure/setup-kubectl) `azure/setup-kubectl` (): Instala uma versão específica de kubectl no corredor.
* [Contexto definido por Kubernetes](https://github.com/Azure/k8s-set-context) `azure/k8s-set-context` (): Desajei o contexto de cluster target Kubernetes que será usado por outras ações ou executar quaisquer comandos kubectl.
* [Contexto definido AKS](https://github.com/Azure/aks-set-context) `azure/aks-set-context` (): Desconfiem do contexto de cluster de serviço Azure Kubernetes.
* [Kubernetes criam segredo](https://github.com/Azure/k8s-create-secret) `azure/k8s-create-secret` (): Criar um segredo genérico ou estivador secreto no cluster Kubernetes.
* [Implantação de Kubernetes](https://github.com/Azure/k8s-deploy) `azure/k8s-deploy` (): Assar e implantar manifestos para os clusters de Kubernetes.
* [Montagem helm](https://github.com/Azure/setup-helm) `azure/setup-helm` (): Instale uma versão específica do binário Helm no corredor.
* [Assada kubernetes](https://github.com/Azure/k8s-bake) `azure/k8s-bake` (): Asse o ficheiro manifesto para ser utilizado para implantações com helm2, kustomize ou kompose.
* [Forro kubernetes](https://github.com/azure/k8s-lint) `azure/k8s-lint` (): Validar/atar os seus ficheiros manifestos.
