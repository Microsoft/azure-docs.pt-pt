---
title: Programar no serviço Kubernetes do Azure (AKS) com o Draft
description: Utilizar o Draft com o AKS e registo de contentor do Azure
services: container-service
author: zr-msft
ms.service: container-service
ms.topic: article
ms.date: 06/20/2019
ms.author: zarhoads
ms.openlocfilehash: bd099b9d76e17eda36be1650ef5081e5aaa7e53a
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/20/2019
ms.locfileid: "67303533"
---
# <a name="quickstart-develop-on-azure-kubernetes-service-aks-with-draft"></a>Início rápido: Programar no serviço Kubernetes do Azure (AKS) com o Draft

Rascunho é uma ferramenta de código-fonte aberto que ajuda o pacote e executar contentores da aplicação num cluster do Kubernetes. Com o Draft, pode rapidamente voltar a implementar uma aplicação no Kubernetes conforme ocorrerem alterações de código sem que seja necessário que consolidar as alterações ao controle de versão. Para obter mais informações sobre o rascunho, consulte a [rascunho da documentação no GitHub][draft-documentation].

Este artigo mostra-lhe como utilizar o pacote de rascunho e executar um aplicativo no AKS.


## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, pode [criar uma conta gratuita](https://azure.microsoft.com/free).
* A [CLI do Azure instalada](/cli/azure/install-azure-cli?view=azure-cli-latest).
* Docker instalado e configurado. Docker disponibiliza pacotes que configuram num [Mac][docker-for-mac], [Windows][docker-for-windows], ou [Linux][docker para linux] system.
* [Helm instalado](https://github.com/helm/helm/blob/master/docs/install.md).
* [Rascunho instalado][draft-documentation].

## <a name="create-an-azure-kubernetes-service-cluster"></a>Criar um cluster do Azure Kubernetes Service

Crie um cluster do AKS. Os comandos abaixo, criar um grupo de recursos denominado MyResourceGroup e um cluster do AKS chamado MyAKS.

```azurecli
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --node-vm-size Standard_DS2_v2 --node-count 1 --generate-ssh-keys
```

## <a name="create-an-azure-container-registry"></a>Criar um Azure Container Registry
Para utilizar o Draft para executar a sua aplicação no cluster do AKS, terá de um registo de contentor do Azure para armazenar as imagens de contentor. O exemplo abaixo utiliza [criar az acr][az-acr-create] para criar um ACR com o nome *MyDraftACR* no *MyResourceGroup* grupo de recursos com o *básica* SKU. Deve fornecer seu próprio nome exclusivo do registo. O nome do registo tem de ser exclusivo no Azure e pode incluir de 5 a 50 carateres alfanuméricos. O SKU *Básico* é um ponto de entrada com otimização de custos para fins de desenvolvimento que fornece um equilíbrio de armazenamento e débito.

```azurecli
az acr create --resource-group MyResourceGroup --name MyDraftACR --sku Basic
```

O resultado será semelhante ao seguinte exemplo. Anote o *loginServer* valor para o ACR, uma vez que vai ser utilizado num passo posterior. No exemplo abaixo, *mydraftacr.azurecr.io* é o *loginServer* para *MyDraftACR*.

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


Para o Draft utilizar a instância do ACR, tem primeiro de iniciar sessão. Utilize o [início de sessão az acr][az-acr-login] comando para iniciar sessão. O exemplo abaixo irá iniciar sessão para um com o nome de ACR *MyDraftACR*.

```azurecli
az acr login --name MyDraftACR
```

O comando devolve a mensagem *Início de sessão com êxito* após a conclusão.

## <a name="create-trust-between-aks-cluster-and-acr"></a>Criar fidedignidade entre o cluster do AKS e ACR

O cluster do AKS também precisa de acesso para o ACR para extrair as imagens de contentor e executá-los. Permitir o acesso para o ACR do AKS, estabelecendo uma relação de confiança. Para estabelecer confiança entre um cluster do AKS e registo do ACR, conceda permissões para o principal de serviço do Azure Active Directory utilizadas pelo cluster do AKS para aceder ao registo do ACR. Os seguintes comandos concedem permissões ao principal de serviço do *MyAKS* cluster no *MyResourceGroup* para o *MyDraftACR* ACR no  *MyResourceGroup*.

```azurecli
# Get the service principal ID of your AKS cluster
AKS_SP_ID=$(az aks show --resource-group MyResourceGroup --name MyAKS --query "servicePrincipalProfile.clientId" -o tsv)

# Get the resource ID of your ACR instance
ACR_RESOURCE_ID=$(az acr show --resource-group MyResourceGroup --name MyDraftACR --query "id" -o tsv)

# Create a role assignment for your AKS cluster to access the ACR instance
az role assignment create --assignee $AKS_SP_ID --scope $ACR_RESOURCE_ID --role contributor
```

## <a name="connect-to-your-aks-cluster"></a>Ligar ao cluster do AKS

Para ligar ao cluster de Kubernetes a partir do computador local, utilize [kubectl][kubectl], o cliente de linha de comandos do Kubernetes.

Se utilizar o Azure Cloud Shell, o `kubectl` já está instalado. Também pode instalá-lo a nível local com o comando [az aks install-cli][]:

```azurecli
az aks install-cli
```

Para configurar `kubectl` para ligar ao seu cluster do Kubernetes, utilize o [az aks get-credentials][] comando. O exemplo seguinte obtém credenciais para o cluster do AKS com o nome *MyAKS* no *MyResourceGroup*:

```azurecli
az aks get-credentials --resource-group MyResourceGroup --name MyAKS
```

## <a name="create-a-service-account-for-helm"></a>Criar uma conta de serviço para Helm

Antes de poder implementar Helm num cluster do AKS habilitados no RBAC, terá uma conta de serviço e o enlace de função para o serviço de Tiller. Para obter mais informações sobre como proteger o Helm / Tiller num RBAC ativado o cluster, consulte [Tiller, espaços de nomes e RBAC][tiller-rbac]. Se o cluster do AKS não estiver ativada de RBAC, ignore este passo.

Crie um ficheiro denominado `helm-rbac.yaml` e copie o YAML seguinte:

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

Criar a conta de serviço e a associação de função com o `kubectl apply` comando:

```console
kubectl apply -f helm-rbac.yaml
```

## <a name="configure-helm"></a>Configurar o Helm
Para implementar um Tiller básico para um cluster do AKS, utilize o [helm init][helm-init] comando. Se o seu cluster não estiver RBAC ativada, remova o `--service-account` argumento e o valor.

```console
helm init --service-account tiller --node-selectors "beta.kubernetes.io/os"="linux"
```

## <a name="configure-draft"></a>Configurar o Draft

Se ainda não configurou o rascunho no seu computador local, execute `draft init`:

```console
$ draft init
Installing default plugins...
Installation of default plugins complete
Installing default pack repositories...
...
Happy Sailing!
```

Terá também de configurar o Draft para utilizar o *loginServer* do ACR. O seguinte comando utiliza `draft config set` utilizar `mydraftacr.azurecr.io` como um registo.

```console
draft config set registry mydraftacr.azurecr.io
```

Configurar o Draft para utilizar o ACR e rascunho pode enviar imagens de contentor para o ACR. Quando o rascunho executa a sua aplicação no cluster do AKS, sem senhas ou segredos são necessários para push ou pull do registo ACR. Uma vez que foi criada uma confiança entre o ACR e o seu cluster do AKS, autenticação é realizada ao nível do Azure Resource Manager, com o Azure Active Directory.

## <a name="download-the-sample-application"></a>Transferir a aplicação de exemplo

Este início rápido utiliza [uma aplicação de java de exemplo do repositório GitHub de rascunho][example-java]. Clonar a aplicação a partir do GitHub e navegue para o `draft/examples/example-java/` diretório.

```console
git clone https://github.com/Azure/draft
cd draft/examples/example-java/
```

## <a name="run-the-sample-application-with-draft"></a>Executar a aplicação de exemplo com o Draft

Utilize o `draft create` comando para preparar a aplicação.

```console
draft create
```

Este comando cria os artefatos que são utilizados para executar a aplicação num cluster do Kubernetes. Esses itens incluem um Dockerfile, um gráfico Helm e um *draft.toml* arquivo, que é o ficheiro de configuração de rascunho.

```console
$ draft create

--> Draft detected Java (92.205567%)
--> Ready to sail
```

Para executar o aplicativo de exemplo no cluster do AKS, utilize o `draft up` comando.

```console
draft up
```

Este comando cria o Dockerfile para criar uma imagem de contentor, envia a imagem para o ACR e instala o gráfico Helm para iniciar o aplicativo no AKS. Na primeira vez que executar este comando, emitir e solicitar a imagem de contentor podem demorar algum tempo. Depois das camadas de bases são colocadas em cache, o tempo necessário para implementar a aplicação é drasticamente reduzido.

```
$ draft up

Draft Up Started: 'example-java': 01CMZAR1F4T1TJZ8SWJQ70HCNH
example-java: Building Docker Image: SUCCESS ⚓  (73.0720s)
example-java: Pushing Docker Image: SUCCESS ⚓  (19.5727s)
example-java: Releasing Application: SUCCESS ⚓  (4.6979s)
Inspect the logs with `draft logs 01CMZAR1F4T1TJZ8SWJQ70HCNH`
```

## <a name="connect-to-the-running-sample-application-from-your-local-machine"></a>Ligue-se para a execução do exemplo de aplicação a partir do seu computador local

Para testar a aplicação, utilize o `draft connect` comando.

```console
draft connect
```

Este comando proxies de uma ligação segura para o pod de Kubernetes. Quando terminar, o aplicativo pode ser acessado no URL fornecido.

```console
$ draft connect

Connect to java:4567 on localhost:49804
[java]: SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
[java]: SLF4J: Defaulting to no-operation (NOP) logger implementation
[java]: SLF4J: See https://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
[java]: == Spark has ignited ...
[java]: >> Listening on 0.0.0.0:4567
```

Navegue para a aplicação num browser com o `localhost` url para ver a aplicação de exemplo. No exemplo acima, o url é `http://localhost:49804`. Parar de utilizar a ligação `Ctrl+c`.

## <a name="access-the-application-on-the-internet"></a>Aceder à aplicação na internet

O passo anterior, criado uma ligação de proxy para o pod da aplicação no cluster do AKS. À medida que desenvolve e testar a sua aplicação, pode querer disponibilizar a aplicação na internet. Para expor um aplicativo na internet, pode criar um serviço do Kubernetes com um tipo de [LoadBalancer][kubernetes-service-loadbalancer].

Atualização `charts/example-java/values.yaml` para criar um *LoadBalancer* serviço. Alterar o valor de *service.type* partir *ClusterIP* para *LoadBalancer*.

```yaml
...
service:
  name: java
  type: LoadBalancer
  externalPort: 80
  internalPort: 4567
...
```

Guardar as alterações, feche o ficheiro e execute `draft up` voltar a executar o aplicativo.

```console
draft up
```

Demora alguns minutos para que o serviço retornar um endereço IP público. Para monitorizar o progresso, utilize o `kubectl get service` comando com o *watch* parâmetro:

```console
$ kubectl get service --watch

NAME                TYPE          CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
example-java-java   LoadBalancer  10.0.141.72   <pending>     80:32150/TCP   2m
...
example-java-java   LoadBalancer   10.0.141.72   52.175.224.118  80:32150/TCP   7m
```

Navegue para o Balanceador de carga da sua aplicação num browser com o *EXTERNAL-IP* para ver a aplicação de exemplo. No exemplo acima, o IP é `52.175.224.118`.

## <a name="iterate-on-the-application"></a>Iterar sobre a aplicação

É possível iterar seu aplicativo ao fazer alterações localmente e execute novamente `draft up`.

Atualizar a mensagem devolvida no [linha 7 de src/main/java/helloworld/Hello.java][example-java-hello-l7]

```java
    public static void main(String[] args) {
        get("/", (req, res) -> "Hello World, I'm Java in AKS!");
    }
```

Execute o `draft up` comando para voltar a implementar a aplicação:

```console
$ draft up

Draft Up Started: 'example-java': 01CMZC9RF0TZT7XPWGFCJE15X4
example-java: Building Docker Image: SUCCESS ⚓  (25.0202s)
example-java: Pushing Docker Image: SUCCESS ⚓  (7.1457s)
example-java: Releasing Application: SUCCESS ⚓  (3.5773s)
Inspect the logs with `draft logs 01CMZC9RF0TZT7XPWGFCJE15X4`
```

Para ver a aplicação atualizada, navegue novamente para o endereço IP do seu Balanceador de carga e verifique se que as suas alterações são apresentados.

## <a name="delete-the-cluster"></a>Eliminar o cluster

Quando o cluster já não é necessário, utilize o [eliminação do grupo de az][az-group-delete] comando remover o grupo de recursos, o cluster do AKS, o registo de contentor, as imagens de contentor armazenadas nele e todos os recursos relacionados.

```azurecli-interactive
az group delete --name MyResourceGroup --yes --no-wait
```

> [!NOTE]
> Quando elimina o cluster, o principal de serviço do Azure Active Directory utilizado pelo cluster do AKS não é removido. Para obter passos sobre como remover o principal de serviço, consulte [considerações principal e de eliminação do serviço AKS][sp-delete].

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre como utilizar o Draft, consulte a documentação de rascunho no GitHub.

> [!div class="nextstepaction"]
> [Documentação de rascunho][draft-documentation]


[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-create]: /cli/azure/acr#az-acr-login
[az-group-delete]: /cli/azure/group#az-group-delete
[az aks get-credentials]: /cli/azure/aks#az-aks-get-credentials
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
[helm-init]: https://docs.helm.sh/helm/#helm-init
[sp-delete]: kubernetes-service-principal.md#additional-considerations
[tiller-rbac]: https://docs.helm.sh/using_helm/#tiller-namespaces-and-rbac
