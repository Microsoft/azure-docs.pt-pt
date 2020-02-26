---
title: Desenvolver no Serviço Azure Kubernetes (AKS) com Draft
description: Use O Projeto com AKS e Registo de Contentores Azure
services: container-service
author: zr-msft
ms.topic: article
ms.date: 06/20/2019
ms.author: zarhoads
ms.openlocfilehash: b03256ee65a3c40d8a64d70b877c49e44e68f822
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77595226"
---
# <a name="quickstart-develop-on-azure-kubernetes-service-aks-with-draft"></a>Quickstart: Desenvolver no Serviço Azure Kubernetes (AKS) com Draft

O projeto é uma ferramenta de código aberto que ajuda a embalar e executar recipientes de aplicação num cluster Kubernetes. Com o Draft, pode recolocar rapidamente uma aplicação para kubernetes à medida que ocorrem alterações de código sem ter de comprometer as suas alterações no controlo da versão. Para obter mais informações sobre o Projeto, consulte o [Projeto de Documentação no GitHub][draft-documentation].

Este artigo mostra-lhe como usar o Draft para embalar e executar uma aplicação no AKS.


## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, pode [criar uma conta gratuita](https://azure.microsoft.com/free).
* A [CLI do Azure instalada](/cli/azure/install-azure-cli?view=azure-cli-latest).
* Docker instalado e configurado. O Docker fornece pacotes que configuram o Docker num sistema [Mac,][docker-for-mac] [Windows][docker-for-windows]ou [Linux.][docker-for-linux]
* [Leme v2 instalado.][helm-install]
* [Projeto instalado][draft-documentation].

## <a name="create-an-azure-kubernetes-service-cluster"></a>Criar um cluster de serviço Azure Kubernetes

Criar um aglomerado AKS. Os comandos abaixo criam um grupo de recursos chamado MyResourceGroup e um cluster AKS chamado MyAKS.

```azurecli
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --node-vm-size Standard_DS2_v2 --node-count 1 --generate-ssh-keys
```

## <a name="create-an-azure-container-registry"></a>Criar um Azure Container Registry
Para utilizar o Draft para executar a sua aplicação no seu cluster AKS, precisa de um Registo de Contentores Azure para armazenar as imagens do seu contentor. O exemplo abaixo usa a criação de [az acr][az-acr-create] para criar um ACR chamado *MyDraftACR* no grupo de recursos *MyResourceGroup* com o *Basic* SKU. Deve fornecer o seu próprio nome único de registo. O nome do registo tem de ser exclusivo no Azure e pode incluir de 5 a 50 carateres alfanuméricos. O SKU *Básico* é um ponto de entrada com otimização de custos para fins de desenvolvimento que fornece um equilíbrio de armazenamento e débito.

```azurecli
az acr create --resource-group MyResourceGroup --name MyDraftACR --sku Basic
```

O resultado será semelhante ao seguinte exemplo. Tome nota do valor do *loginServer* para o seu ACR, uma vez que será utilizado num passo posterior. No exemplo abaixo, *mydraftacr.azurecr.io* é o *loginServer* para *MyDraftACR*.

```console
{
  "adminUserEnabled": false,
  "creationDate": "2019-06-11T13:35:17.998425+00:00",
  "id": "/subscriptions/<ID>/resourceGroups/MyResourceGroup/providers/Microsoft.ContainerRegistry/registries/MyDraftACR",
  "location": "eastus",
  "loginServer": "mydraftacr.azurecr.io",
  "name": "MyDraftACR",
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


Para que o Draft utilize a instância ACR, tem de iniciar sessão. Use o comando de [login az acr][az-acr-login] para iniciar sessão. O exemplo abaixo irá iniciar sessão num ACR chamado *MyDraftACR*.

```azurecli
az acr login --name MyDraftACR
```

O comando devolve a mensagem *Início de sessão com êxito* após a conclusão.

## <a name="create-trust-between-aks-cluster-and-acr"></a>Criar confiança entre o cluster AKS e o ACR

O seu cluster AKS também precisa de acesso ao seu ACR para puxar as imagens do contentor e executá-las. Permite o acesso ao ACR a partir da AKS, estabelecendo um fundo. Para estabelecer a confiança entre um cluster AKS e um registo ACR, conceda permissões para o principal de serviço de Diretório Ativo Azure utilizado pelo cluster AKS para aceder ao registo ACR. Os seguintes comandos concedem permissões ao diretor de serviço do cluster *MyAKS* no *MyResourceGroup* para o *MyDraftACR* ACR no *MyResourceGroup*.

```azurecli
# Get the service principal ID of your AKS cluster
AKS_SP_ID=$(az aks show --resource-group MyResourceGroup --name MyAKS --query "servicePrincipalProfile.clientId" -o tsv)

# Get the resource ID of your ACR instance
ACR_RESOURCE_ID=$(az acr show --resource-group MyResourceGroup --name MyDraftACR --query "id" -o tsv)

# Create a role assignment for your AKS cluster to access the ACR instance
az role assignment create --assignee $AKS_SP_ID --scope $ACR_RESOURCE_ID --role contributor
```

## <a name="connect-to-your-aks-cluster"></a>Ligue-se ao seu cluster AKS

Para se ligar ao cluster Kubernetes a partir do seu computador local, utiliza [kubectl,][kubectl]o cliente da linha de comando Kubernetes.

Se utilizar o Azure Cloud Shell, o `kubectl` já está instalado. Também pode instalá-lo a nível local com o comando [az aks install-cli][]:

```azurecli
az aks install-cli
```

Para configurar `kubectl` para se ligar ao seu cluster Kubernetes, use o comando [az aks get-credentials.][] O exemplo que se segue obtém credenciais para o cluster AKS denominado *MyAKS* no *MyResourceGroup:*

```azurecli
az aks get-credentials --resource-group MyResourceGroup --name MyAKS
```

## <a name="create-a-service-account-for-helm"></a>Criar uma conta de serviço para helm

Antes de poder implantar o Helm num cluster AKS ativado pelo RBAC, precisa de uma conta de serviço e de uma ligação de funções para o serviço Tiller. Para obter mais informações sobre a segurança do Leme / Tiller num cluster ativado por RBAC, consulte [Tiller, Namespaces e RBAC][tiller-rbac]. Se o seu cluster AKS não estiver ativado por RBAC, ignore este passo.

Crie um ficheiro chamado `helm-rbac.yaml` e copie no seguinte YAML:

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: tiller
  namespace: kube-system
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: tiller
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
  - kind: ServiceAccount
    name: tiller
    namespace: kube-system
```

Criar a conta de serviço e a vinculação de funções com o comando `kubectl apply`:

```console
kubectl apply -f helm-rbac.yaml
```

## <a name="configure-helm"></a>Configure Helm
Para implantar um Tiller básico num aglomerado AKS, use o comando de entrada do [leme.][helm-init] Se o seu cluster não estiver ativado, remova o argumento e o valor `--service-account`.

```console
helm init --service-account tiller --node-selectors "beta.kubernetes.io/os"="linux"
```

## <a name="configure-draft"></a>Configurar Rascunho

Se ainda não configurar o Projeto na sua máquina local, faça `draft init`:

```console
$ draft init
Installing default plugins...
Installation of default plugins complete
Installing default pack repositories...
...
Happy Sailing!
```

Também precisa de configurar o Draft para utilizar o *loginServer* do seu ACR. O comando seguinte utiliza `draft config set` para usar `mydraftacr.azurecr.io` como registo.

```console
draft config set registry mydraftacr.azurecr.io
```

Configurou o Draft para usar o seu ACR, e o Draft pode empurrar imagens de contentores para o seu ACR. Quando o Draft executa a sua aplicação no seu cluster AKS, não são necessárias senhas ou segredos para empurrar ou retirar do registo ACR. Uma vez que foi criado um fundo entre o seu cluster AKS e o seu ACR, a autenticação acontece ao nível do Gestor de Recursos Azure, utilizando o Diretório Ativo Azure.

## <a name="download-the-sample-application"></a>Transferir a aplicação de exemplo

Este quickstart utiliza [um exemplo de aplicação Java do repositório Draft GitHub][example-java]. Clone a aplicação do GitHub e navegue para o diretório `draft/examples/example-java/`.

```console
git clone https://github.com/Azure/draft
cd draft/examples/example-java/
```

## <a name="run-the-sample-application-with-draft"></a>Executar a aplicação da amostra com O Projeto

Utilize o comando `draft create` para preparar a aplicação.

```console
draft create
```

Este comando cria os artefactos que são usados para executar a aplicação num aglomerado de Kubernetes. Estes itens incluem um Dockerfile, um gráfico Helm e um ficheiro *de rascunho.toml,* que é o ficheiro de configuração do Projeto.

```console
$ draft create

--> Draft detected Java (92.205567%)
--> Ready to sail
```

Para executar a aplicação da amostra no seu cluster AKS, use o comando `draft up`.

```console
draft up
```

Este comando constrói o Dockerfile para criar uma imagem de contentor, empurra a imagem para o seu ACR, e instala o gráfico Helm para iniciar a aplicação em AKS. A primeira vez que executar este comando, empurrar e puxar a imagem do recipiente pode demorar algum tempo. Uma vez que as camadas base são emcache, o tempo necessário para implementar a aplicação é drasticamente reduzido.

```
$ draft up

Draft Up Started: 'example-java': 01CMZAR1F4T1TJZ8SWJQ70HCNH
example-java: Building Docker Image: SUCCESS ⚓  (73.0720s)
example-java: Pushing Docker Image: SUCCESS ⚓  (19.5727s)
example-java: Releasing Application: SUCCESS ⚓  (4.6979s)
Inspect the logs with `draft logs 01CMZAR1F4T1TJZ8SWJQ70HCNH`
```

## <a name="connect-to-the-running-sample-application-from-your-local-machine"></a>Ligue-se à aplicação da amostra de execução da sua máquina local

Para testar a aplicação, utilize o comando `draft connect`.

```console
draft connect
```

Este comando proxide uma ligação segura à cápsula Kubernetes. Quando concluída, a aplicação pode ser acedida no URL fornecido.

```console
$ draft connect

Connect to java:4567 on localhost:49804
[java]: SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
[java]: SLF4J: Defaulting to no-operation (NOP) logger implementation
[java]: SLF4J: See https://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
[java]: == Spark has ignited ...
[java]: >> Listening on 0.0.0.0:4567
```

Navegue para a aplicação num browser usando o URL `localhost` para ver a aplicação da amostra. No exemplo acima, o URL é `http://localhost:49804`. Pare a ligação utilizando `Ctrl+c`.

## <a name="access-the-application-on-the-internet"></a>Aceda à aplicação na internet

O passo anterior criou uma ligação proxy ao casulo de aplicação no seu cluster AKS. À medida que desenvolve e testa a sua aplicação, pode querer disponibilizar a aplicação na internet. Para expor uma aplicação na internet, pode criar um serviço Kubernetes com um tipo de [LoadBalancer][kubernetes-service-loadbalancer].

Atualizar `charts/example-java/values.yaml` criar um serviço *LoadBalancer.* Altere o valor do *serviço.type* de *ClusterIP* para *LoadBalancer*.

```yaml
...
service:
  name: java
  type: LoadBalancer
  externalPort: 80
  internalPort: 4567
...
```

Guarde as suas alterações, feche o ficheiro e faça `draft up` para reexecutar a aplicação.

```console
draft up
```

Leva alguns minutos para o serviço devolver um endereço IP público. Para monitorizar o progresso, utilize o comando `kubectl get service` com o parâmetro de *relógio:*

```console
$ kubectl get service --watch

NAME                TYPE          CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
example-java-java   LoadBalancer  10.0.141.72   <pending>     80:32150/TCP   2m
...
example-java-java   LoadBalancer   10.0.141.72   52.175.224.118  80:32150/TCP   7m
```

Navegue para o equilibrador de carga da sua aplicação num browser utilizando o *EXTERNAL-IP* para ver a aplicação da amostra. No exemplo acima, o PI é `52.175.224.118`.

## <a name="iterate-on-the-application"></a>Iterado na aplicação

Pode iterar a sua aplicação fazendo alterações localmente e reexecutando `draft up`.

Atualizar a mensagem devolvida na [linha 7 do sRC/main/java/helloworld/Hello.java][example-java-hello-l7]

```java
    public static void main(String[] args) {
        get("/", (req, res) -> "Hello World, I'm Java in AKS!");
    }
```

Executar o comando `draft up` para reimplantar a aplicação:

```console
$ draft up

Draft Up Started: 'example-java': 01CMZC9RF0TZT7XPWGFCJE15X4
example-java: Building Docker Image: SUCCESS ⚓  (25.0202s)
example-java: Pushing Docker Image: SUCCESS ⚓  (7.1457s)
example-java: Releasing Application: SUCCESS ⚓  (3.5773s)
Inspect the logs with `draft logs 01CMZC9RF0TZT7XPWGFCJE15X4`
```

Para ver a aplicação atualizada, navegue novamente para o endereço IP do seu equilibrador de carga e verifique se as suas alterações aparecem.

## <a name="delete-the-cluster"></a>Eliminar o cluster

Quando o cluster já não for necessário, utilize o [grupo AZ eliminar][az-group-delete] o comando para remover o grupo de recursos, o cluster AKS, o registo do contentor, as imagens de contentores armazenadas lá e todos os recursos relacionados.

```azurecli-interactive
az group delete --name MyResourceGroup --yes --no-wait
```

> [!NOTE]
> Quando elimina o cluster, o principal de serviço do Azure Active Directory utilizado pelo cluster do AKS não é removido. Para obter medidas sobre como remover o diretor de serviço, consulte as [principais considerações e a eliminação do serviço AKS.][sp-delete]

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a utilização do Projeto, consulte o Projeto de Documentação no GitHub.

> [!div class="nextstepaction"]
> [Projeto de documentação][draft-documentation]


[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-create]: /cli/azure/acr#az-acr-login
[az-group-delete]: /cli/azure/group#az-group-delete
[az aks get-credentials.]: /cli/azure/aks#az-aks-get-credentials
[az aks install-cli]: /cli/azure/aks#az-aks-install-cli
[kubernetes-ingress]: ./ingress-basic.md

[docker-for-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-for-mac]: https://docs.docker.com/docker-for-mac/
[docker-for-windows]: https://docs.docker.com/docker-for-windows/
[draft-documentation]: https://github.com/Azure/draft/tree/master/docs
[example-java]: https://github.com/Azure/draft/tree/master/examples/example-java
[example-java-hello-l7]: https://github.com/Azure/draft/blob/master/examples/example-java/src/main/java/helloworld/Hello.java#L7
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubernetes-service-loadbalancer]: https://kubernetes.io/docs/concepts/services-networking/service/#type-loadbalancer
[helm-init]: https://v2.helm.sh/docs/helm/#helm-init
[helm-install]: https://v2.helm.sh/docs/using_helm/#installing-helm
[sp-delete]: kubernetes-service-principal.md#additional-considerations
[tiller-rbac]: https://v2.helm.sh/docs/using_helm/#tiller-namespaces-and-rbac
