---
title: Desenvolva-se no Serviço Azure Kubernetes (AKS) com Helm
description: Utilize o Helm com registo de contentores AKS e Azure para embalar e executar recipientes de aplicação num cluster.
services: container-service
author: zr-msft
ms.topic: article
ms.date: 07/28/2020
ms.author: zarhoads
ms.openlocfilehash: 0ca2d7ccc863e2208db1212ef3d3f10fa709d069
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96006649"
---
# <a name="quickstart-develop-on-azure-kubernetes-service-aks-with-helm"></a>Quickstart: Desenvolva-se no Serviço Azure Kubernetes (AKS) com Helm

[Helm][helm] é uma ferramenta de embalagem de código aberto que o ajuda a instalar e gerir o ciclo de vida das aplicações Kubernetes. Semelhante aos gestores de pacotes Linux, como *APT* e *Yum,* o Helm é usado para gerir gráficos kubernetes, que são pacotes de recursos kubernetes pré-configurados.

Este artigo mostra-lhe como usar o Helm para embalar e executar uma aplicação na AKS. Para obter mais detalhes sobre a instalação de uma aplicação existente utilizando o Helm, consulte [instalar as aplicações existentes com Helm em AKS][helm-existing].

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, pode criar uma [conta gratuita.](https://azure.microsoft.com/free)
* A [CLI do Azure instalada](/cli/azure/install-azure-cli?view=azure-cli-latest).
* [Helm v3 instalado][helm-install].

## <a name="create-an-azure-container-registry"></a>Criar um Azure Container Registry
Para utilizar o Helm para executar a sua aplicação no seu cluster AKS, precisa de um Registo de Contentores Azure para armazenar as imagens do seu contentor. O exemplo abaixo utiliza [a az acr criar][az-acr-create] para criar um ACR chamado *MyHelmACR* no grupo de recursos *MyResourceGroup* com o *SKU Básico.* Deve fornecer o seu próprio nome de registo único. O nome do registo tem de ser exclusivo no Azure e pode incluir de 5 a 50 carateres alfanuméricos. O SKU *Básico* é um ponto de entrada com otimização de custos para fins de desenvolvimento que fornece um equilíbrio de armazenamento e débito.

```azurecli
az group create --name MyResourceGroup --location eastus
az acr create --resource-group MyResourceGroup --name MyHelmACR --sku Basic
```

O resultado será semelhante ao seguinte exemplo. Tome nota do valor do *loginServer* para o seu ACR, uma vez que será utilizado num passo posterior. No exemplo abaixo, *myhelmacr.azurecr.io* é o *loginServer* da *MyHelmACR*.

```console
{
  "adminUserEnabled": false,
  "creationDate": "2019-06-11T13:35:17.998425+00:00",
  "id": "/subscriptions/<ID>/resourceGroups/MyResourceGroup/providers/Microsoft.ContainerRegistry/registries/MyHelmACR",
  "location": "eastus",
  "loginServer": "myhelmacr.azurecr.io",
  "name": "MyHelmACR",
  "networkRuleSet": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "MyResourceGroup",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "status": null,
  "storageAccount": null,
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

## <a name="create-an-azure-kubernetes-service-cluster"></a>Criar um cluster do Azure Kubernetes Service

Crie um cluster AKS. O comando abaixo cria um cluster AKS chamado MyAKS e anexa o MyHelmACR.

```azurecli
az aks create -g MyResourceGroup -n MyAKS --location eastus  --attach-acr MyHelmACR --generate-ssh-keys
```

O seu cluster AKS precisa de acesso ao seu ACR para retirar as imagens do contentor e executá-las. O comando acima também concede ao cluster *MyAKS* acesso ao seu *ACR MyHelmACR.*

## <a name="connect-to-your-aks-cluster"></a>Ligue-se ao seu cluster AKS

Para ligar ao cluster de Kubernetes a partir do computador local, utilize [kubectl][kubectl], o cliente de linha de comandos do Kubernetes.

Se utilizar o Azure Cloud Shell, o `kubectl` já está instalado. Também pode instalá-lo a nível local com o comando [az aks install-cli][]:

```azurecli
az aks install-cli
```

Para configurar `kubectl` para se ligar ao cluster do Kubernetes, utilize o comando [az aks get-credentials][]. O exemplo a seguir obtém credenciais para o cluster AKS chamado *MyAKS* no *MyResourceGroup:*

```azurecli
az aks get-credentials --resource-group MyResourceGroup --name MyAKS
```

## <a name="download-the-sample-application"></a>Transferir a aplicação de exemplo

Este quickstart usa [um exemplo Node.js aplicação do repositório de amostras Azure Dev Spaces][example-nodejs]. Clone a aplicação do GitHub e navegue para o `dev-spaces/samples/nodejs/getting-started/webfrontend` diretório.

```console
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/nodejs/getting-started/webfrontend
```

## <a name="create-a-dockerfile"></a>Criar um Dockerfile

Criar um novo ficheiro *Dockerfile* utilizando o seguinte:

```dockerfile
FROM node:latest

WORKDIR /webfrontend

COPY package.json ./

RUN npm install

COPY . .

EXPOSE 80
CMD ["node","server.js"]
```

## <a name="build-and-push-the-sample-application-to-the-acr"></a>Construa e empurre a aplicação da amostra para o ACR

Utilize o comando [de construção az acr][az-acr-build] para construir e empurrar uma imagem para o registo, utilizando o Dockerfile anterior. O `.` fim do comando define a localização do Dockerfile, neste caso o atual diretório.

```azurecli
az acr build --image webfrontend:v1 \
  --registry MyHelmACR \
  --file Dockerfile .
```

## <a name="create-your-helm-chart"></a>Crie o seu gráfico Helm

Gere o seu gráfico Helm usando o `helm create` comando.

```console
helm create webfrontend
```

Faça as seguintes atualizações para *webfrontend/values.yaml*. Substitua o loginServer do seu registo que observou num passo anterior, como *myhelmacr.azurecr.io:*

* Alterar `image.repository` para `<loginServer>/webfrontend`
* Alterar `service.type` para `LoadBalancer`

Por exemplo:

```yml
# Default values for webfrontend.
# This is a YAML-formatted file.
# Declare variables to be passed into your templates.

replicaCount: 1

image:
  repository: *myhelmacr.azurecr.io*/webfrontend
  pullPolicy: IfNotPresent
...
service:
  type: LoadBalancer
  port: 80
...
```

Atualização `appVersion` para `v1` *webfrontend/Chart.yaml*. Por exemplo

```yml
apiVersion: v2
name: webfrontend
...
# This is the version number of the application being deployed. This version number should be
# incremented each time you make changes to the application.
appVersion: v1
```

## <a name="run-your-helm-chart"></a>Executar o seu gráfico Helm

Utilize o `helm install` comando para instalar a sua aplicação utilizando a sua tabela Helm.

```console
helm install webfrontend webfrontend/
```

O serviço demora alguns minutos a devolver um endereço IP público. Para monitorizar o progresso, utilize o `kubectl get service` comando com o parâmetro do *relógio:*

```console
$ kubectl get service --watch

NAME                TYPE          CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
webfrontend         LoadBalancer  10.0.141.72   <pending>     80:32150/TCP   2m
...
webfrontend         LoadBalancer  10.0.141.72   <EXTERNAL-IP> 80:32150/TCP   7m
```

Navegue até ao equilibrador de carga da sua aplicação num browser utilizando `<EXTERNAL-IP>` a aplicação para ver a amostra.

## <a name="delete-the-cluster"></a>Eliminar o cluster

Quando o cluster já não for necessário, utilize o comando de eliminação do [grupo az][az-group-delete] para remover o grupo de recursos, o cluster AKS, o registo do contentor, as imagens do contentor aí armazenadas e todos os recursos conexos.

```azurecli-interactive
az group delete --name MyResourceGroup --yes --no-wait
```

> [!NOTE]
> Quando elimina o cluster, o principal de serviço do Azure Active Directory utilizado pelo cluster do AKS não é removido. Para obter passos sobre como remover o principal de serviço, consulte [Considerações sobre e eliminação do principal de serviço AKS][sp-delete]. Se usou uma identidade gerida, a identidade é gerida pela plataforma e não necessita de remoção.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a utilização do Helm, consulte a documentação helm.

> [!div class="nextstepaction"]
> [Documentação do Helm][helm-documentation]

[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-group-delete]: /cli/azure/group#az-group-delete
[az aks get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az aks install-cli]: /cli/azure/aks#az-aks-install-cli
[example-nodejs]: https://github.com/Azure/dev-spaces/tree/master/samples/nodejs/getting-started/webfrontend
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[helm]: https://helm.sh/
[helm-documentation]: https://helm.sh/docs/
[helm-existing]: kubernetes-helm.md
[helm-install]: https://helm.sh/docs/intro/install/
[sp-delete]: kubernetes-service-principal.md#additional-considerations
