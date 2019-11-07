---
title: Compilar, testar e implantar contêineres no serviço kubernetes do Azure usando ações do GitHub
description: Saiba como usar as ações do GitHub para implantar seu contêiner no kubernetes
services: container-service
author: azooinmyluggage
ms.service: container-service
ms.topic: article
ms.date: 11/04/2019
ms.author: atulmal
ms.openlocfilehash: c82cbb8d7406c8d6f4063f52590e050438283ace
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73693387"
---
# <a name="github-actions-for-deploying-to-kubernetes-service"></a>Ações do GitHub para implantação no serviço kubernetes

As [ações do GitHub](https://help.github.com/en/articles/about-github-actions) oferecem a flexibilidade para criar um fluxo de trabalho de desenvolvimento automatizado do software. A ação kubernetes [azure/aks-set-context@v1]((https://github.com/Azure/aks-set-context)) facilitar implantações em clusters do serviço kubernetes do Azure. A ação define o contexto de cluster AKS de destino, que pode ser usado por outras ações, como [Azure/K8S-Deploy](https://github.com/Azure/k8s-deploy/tree/master), [Azure/K8S-Create-Secret,](https://github.com/Azure/k8s-create-secret/tree/master) etc. ou executar qualquer comando kubectl.

> [!IMPORTANT]
> No momento, as ações do GitHub estão em beta. Primeiro, você deve [se inscrever para ingressar na versão prévia](https://github.com/features/actions) usando sua conta do github.
> 

Um fluxo de trabalho é definido por um arquivo YAML (. yml) no caminho `/.github/workflows/` em seu repositório. Essa definição contém as várias etapas e parâmetros que compõem o fluxo de trabalho.

Para um fluxo de trabalho direcionado a AKS, o arquivo tem três seções:

|Section  |Tarefas  |
|---------|---------|
|**Autenticação** | Fazer logon em um ACR (registro de contêiner privado) |
|**Integrado** | Compilar & enviar por push a imagem de contêiner  |
|**Implementar** | 1. definir o cluster AKS de destino |
| |2. criar um segredo de registro genérico/Docker no cluster kubernetes  |
||3. implantar no cluster kubernetes|

## <a name="create-a-service-principal"></a>Criar um principal de serviço

Você pode criar uma [entidade de serviço](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals#service-principal-object) usando o comando [AZ ad SP Create-for-RBAC](https://docs.microsoft.com/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) na [CLI do Azure](https://docs.microsoft.com/cli/azure/). Você pode executar esse comando usando [Azure cloud Shell](https://shell.azure.com/) na portal do Azure ou selecionando o botão **experimentar** .

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP> --sdk-auth
```

No comando acima, substitua os espaços reservados por sua ID de assinatura e grupo de recursos. A saída são as credenciais de atribuição de função que fornecem acesso ao recurso. O comando deve gerar um objeto JSON semelhante a este.

```json
  {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
  }
```
Copie esse objeto JSON, que você pode usar para autenticar do GitHub.

## <a name="configure-the-github-secrets"></a>Configurar os segredos do GitHub

Siga as etapas para configurar os segredos:

1. No [GitHub](https://github.com/), navegue até o repositório, selecione **configurações > segredos > Adicionar um novo segredo**.

    ![confidenciais](media/kubernetes-action/secrets.png)

2. Cole o conteúdo do comando `az cli` acima como o valor da variável secreta. Por exemplo, `AZURE_CREDENTIALS`.

3. Da mesma forma, defina os seguintes segredos adicionais para as credenciais de registro de contêiner e defina-as na ação de logon do Docker. 

    - REGISTRY_USERNAME
    - REGISTRY_PASSWORD

4. Você verá os segredos conforme mostrado abaixo, uma vez definidos.

    ![kubernetes-segredos](media/kubernetes-action/kubernetes-secrets.png)

##  <a name="build-a-container-image-and-deploy-to-azure-kubernetes-service-cluster"></a>Criar uma imagem de contêiner e implantar no cluster do serviço kubernetes do Azure

A criação e o envio por push das imagens de contêiner são feitas usando `Azure/docker-login@v1` ação. Para implantar uma imagem de contêiner no AKS, você precisará usar a ação `Azure/k8s-deploy@v1`. Esta ação tem cinco parâmetros:

| **Meter**  | **Explicação**  |
|---------|---------|
| **namespace** | Adicional Escolha o namespace kubernetes de destino. Se o namespace não for fornecido, os comandos serão executados no namespace padrão | 
| **manifestos** |  Necessária Caminho para os arquivos de manifesto que serão usados para implantação |
| **imagens** | Adicional URL de recurso totalmente qualificado das imagens a serem usadas para substituições nos arquivos de manifesto |
| **imagepullsecrets** | Adicional Nome de um segredo do registro do Docker que já foi configurado no cluster. Cada um desses nomes de segredo é adicionado no campo imagePullSecrets para as cargas de trabalho encontradas nos arquivos de manifesto de entrada |
| **kubectl-versão** | Adicional Instala uma versão específica do binário kubectl |

### <a name="deploy-to-azure-kubernetes-service-cluster"></a>Implantar no cluster do serviço kubernetes do Azure

Fluxo de trabalho de ponta a ponta para a criação de imagens de contêiner e implantação em um cluster do serviço kubernetes do Azure.

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

Você pode encontrar nosso conjunto de ações em repositórios diferentes no GitHub, cada um contendo documentação e exemplos para ajudá-lo a usar o GitHub para CI/CD e implantar seus aplicativos no Azure.

- [instalação-kubectl](https://github.com/Azure/setup-kubectl)

- [K8S-Set-Context](https://github.com/Azure/k8s-set-context)

- [AKs-Set-Context](https://github.com/Azure/aks-set-context)

- [K8S-criar-segredo](https://github.com/Azure/k8s-create-secret)

- [K8S-implantar](https://github.com/Azure/k8s-deploy)

- [webapps-contêiner-implantar](https://github.com/Azure/webapps-container-deploy)

- [ações-fluxo de trabalho-amostras](https://github.com/Azure/actions-workflow-samples)
