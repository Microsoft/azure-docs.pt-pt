---
title: Desenvolver no Serviço Azure Kubernetes (AKS) com Helm
description: Utilize o Helm com o Registo de Contentores AKS e Azure para embalar e executar recipientes de aplicação num cluster.
services: container-service
author: zr-msft
ms.topic: article
ms.date: 04/20/2020
ms.author: zarhoads
ms.openlocfilehash: 1f67605918e093e9ab28aa88be777d27acd831ef
ms.sourcegitcommit: b1e25a8a442656e98343463aca706f4fde629867
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "82169573"
---
# <a name="quickstart-develop-on-azure-kubernetes-service-aks-with-helm"></a>Quickstart: Desenvolver no Serviço Azure Kubernetes (AKS) com Helm

[O Helm][helm] é uma ferramenta de embalagem de código aberto que o ajuda a instalar e gerir o ciclo de vida das aplicações kubernetes. Semelhante aos gestores de pacotes Linux, como *apt* e *Yum,* Helm é usado para gerir gráficos Kubernetes, que são pacotes de recursos kubernetes pré-reconfigurados.

Este artigo mostra-lhe como usar o Helm para embalar e executar uma aplicação no AKS. Para obter mais detalhes sobre a instalação de uma aplicação existente utilizando o Helm, consulte [Instalar aplicações existentes com o Helm no AKS][helm-existing].

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição Azure, pode criar uma [conta gratuita.](https://azure.microsoft.com/free)
* A [CLI do Azure instalada](/cli/azure/install-azure-cli?view=azure-cli-latest).
* Docker instalado e configurado. O Docker disponibiliza pacotes que o configuram num sistema [Mac][docker-for-mac], [Windows][docker-for-windows] ou [Linux][docker-for-linux].
* [Leme v3 instalado.][helm-install]

## <a name="create-an-azure-container-registry"></a>Criar um Azure Container Registry
Para utilizar o Helm para executar a sua aplicação no seu cluster AKS, precisa de um Registo de Contentores Azure para armazenar as imagens do seu contentor. O exemplo abaixo usa a criação de [az acr][az-acr-create] para criar um ACR chamado *MyHelmACR* no grupo de recursos *MyResourceGroup* com *o* Basic SKU. Deve fornecer o seu próprio nome único de registo. O nome do registo tem de ser exclusivo no Azure e pode incluir de 5 a 50 carateres alfanuméricos. O SKU *Básico* é um ponto de entrada com otimização de custos para fins de desenvolvimento que fornece um equilíbrio de armazenamento e débito.

```azurecli
az group create --name MyResourceGroup --location eastus
az acr create --resource-group MyResourceGroup --name MyHelmACR --sku Basic
```

O resultado será semelhante ao seguinte exemplo. Tome nota do valor do *loginServer* para o seu ACR, uma vez que será utilizado num passo posterior. No exemplo abaixo, *myhelmacr.azurecr.io* é o *loginServer* para *MyHelmACR*.

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

Para utilizar a instância ACR, tem de iniciar sessão. Use o comando de [login az acr][az-acr-login] para iniciar sessão. O exemplo abaixo irá assinar num ACR chamado *MyHelmACR*.

```azurecli
az acr login --name MyHelmACR
```

O comando devolve uma mensagem *login bem sucedida* uma vez concluída.

## <a name="create-an-azure-kubernetes-service-cluster"></a>Criar um cluster de serviço Azure Kubernetes

Criar um aglomerado AKS. O comando abaixo cria um aglomerado AKS chamado MyAKS e anexa o MyHelmACR.

```azurecli
az aks create -g MyResourceGroup -n MyAKS --location eastus  --attach-acr MyHelmACR --generate-ssh-keys
```

O seu cluster AKS precisa de acesso ao seu ACR para puxar as imagens do contentor e executá-las. O comando acima também concede ao cluster *MyAKS* acesso ao seu *MyHelmACR* ACR.

## <a name="connect-to-your-aks-cluster"></a>Ligue-se ao seu cluster AKS

Para ligar ao cluster de Kubernetes a partir do computador local, utilize [kubectl][kubectl], o cliente de linha de comandos do Kubernetes.

Se utilizar o Azure Cloud Shell, o `kubectl` já está instalado. Também pode instalá-lo a nível local com o comando [az aks install-cli][]:

```azurecli
az aks install-cli
```

Para configurar `kubectl` para se ligar ao cluster do Kubernetes, utilize o comando [az aks get-credentials][]. O exemplo que se segue obtém credenciais para o cluster AKS denominado *MyAKS* no *MyResourceGroup:*

```azurecli
az aks get-credentials --resource-group MyResourceGroup --name MyAKS
```

## <a name="download-the-sample-application"></a>Transferir a aplicação de exemplo

Este quickstart usa um exemplo de [aplicação Node.js do repositório de amostras Azure Dev Spaces][example-nodejs]. Clone a aplicação do GitHub e navegue para o `dev-spaces/samples/nodejs/getting-started/webfrontend` diretório.

```console
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/nodejs/getting-started/webfrontend
```

## <a name="create-a-dockerfile"></a>Criar um Dockerfile

Crie um novo ficheiro *Dockerfile* utilizando o seguinte:

```dockerfile
FROM node:latest

WORKDIR /webfrontend

COPY package.json ./

RUN npm install

COPY . .

EXPOSE 80
CMD ["node","server.js"]
```

## <a name="build-and-push-the-sample-application-to-the-acr"></a>Construir e empurrar a aplicação da amostra para o ACR

Obtenha o endereço do servidor de login utilizando o comando da [lista az acr][az-acr-list] e consultando o *loginServer:*

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Utilize o Docker para construir, etiquetar e empurrar o seu recipiente de aplicação de amostras para o ACR:

```console
docker build -t webfrontend:latest .
docker tag webfrontend <acrLoginServer>/webfrontend:v1
docker push <acrLoginServer>/webfrontend:v1
```

## <a name="create-your-helm-chart"></a>Crie o seu gráfico helm

Gere o seu `helm create` gráfico helm usando o comando.

```console
helm create webfrontend
```

Faça as seguintes atualizações para *webfrontend/values.yaml:*

* Mudar `image.repository` para`<acrLoginServer>/webfrontend`
* Mudar `service.type` para`LoadBalancer`

Por exemplo:

```yml
# Default values for webfrontend.
# This is a YAML-formatted file.
# Declare variables to be passed into your templates.

replicaCount: 1

image:
  repository: <acrLoginServer>/webfrontend
  pullPolicy: IfNotPresent
...
service:
  type: LoadBalancer
  port: 80
...
```

`appVersion` Atualização `v1` para *em webfrontend/Chart.yaml*. Por exemplo

```yml
apiVersion: v2
name: webfrontend
...
# This is the version number of the application being deployed. This version number should be
# incremented each time you make changes to the application.
appVersion: v1
```

## <a name="run-your-helm-chart"></a>Execute o seu gráfico de Leme

Utilize `helm install` o comando para instalar a sua aplicação utilizando o seu gráfico Helm.

```console
helm install webfrontend webfrontend/
```

Leva alguns minutos para o serviço devolver um endereço IP público. Para monitorizar o progresso, utilize o `kubectl get service` comando com o parâmetro do *relógio:*

```console
$ kubectl get service --watch

NAME                TYPE          CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
webfrontend         LoadBalancer  10.0.141.72   <pending>     80:32150/TCP   2m
...
webfrontend         LoadBalancer  10.0.141.72   <EXTERNAL-IP> 80:32150/TCP   7m
```

Navegue para o equilibrador de carga `<EXTERNAL-IP>` da sua aplicação num browser utilizando a aplicação para ver a amostra.

## <a name="delete-the-cluster"></a>Eliminar o cluster

Quando o cluster já não for necessário, utilize o [grupo AZ eliminar][az-group-delete] o comando para remover o grupo de recursos, o cluster AKS, o registo do contentor, as imagens de contentores armazenadas lá e todos os recursos relacionados.

```azurecli-interactive
az group delete --name MyResourceGroup --yes --no-wait
```

> [!NOTE]
> Quando elimina o cluster, o principal de serviço do Azure Active Directory utilizado pelo cluster do AKS não é removido. Para obter passos sobre como remover o principal de serviço, consulte [Considerações sobre e eliminação do principal de serviço AKS][sp-delete]. Se usou uma identidade gerida, a identidade é gerida pela plataforma e não requer remoção.

## <a name="next-steps"></a>Passos seguintes

Para mais informações sobre a utilização do Helm, consulte a documentação helm.

> [!div class="nextstepaction"]
> [Documentação do leme][helm-documentation]

[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-list]: /cli/azure/acr#az-acr-list
[az-group-delete]: /cli/azure/group#az-group-delete
[az aks get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az aks install-cli]: /cli/azure/aks#az-aks-install-cli

[docker-for-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-for-mac]: https://docs.docker.com/docker-for-mac/
[docker-for-windows]: https://docs.docker.com/docker-for-windows/
[example-nodejs]: https://github.com/Azure/dev-spaces/tree/master/samples/nodejs/getting-started/webfrontend
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[helm]: https://helm.sh/
[helm-documentation]: https://helm.sh/docs/
[helm-existing]: kubernetes-helm.md
[helm-install]: https://helm.sh/docs/intro/install/
[sp-delete]: kubernetes-service-principal.md#additional-considerations
