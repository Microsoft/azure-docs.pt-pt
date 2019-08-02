---
title: Desenvolver no AKS (serviço kubernetes do Azure) com o rascunho
description: Usar o rascunho com o AKS e o registro de contêiner do Azure
services: container-service
author: zr-msft
ms.service: container-service
ms.topic: article
ms.date: 06/20/2019
ms.author: zarhoads
ms.openlocfilehash: bd099b9d76e17eda36be1650ef5081e5aaa7e53a
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/30/2019
ms.locfileid: "67303533"
---
# <a name="quickstart-develop-on-azure-kubernetes-service-aks-with-draft"></a>Início rápido: Desenvolver no AKS (serviço kubernetes do Azure) com o rascunho

O rascunho é uma ferramenta de código-fonte aberto que ajuda a empacotar e executar contêineres de aplicativos em um cluster kubernetes. Com o rascunho, você pode reimplantar rapidamente um aplicativo para kubernetes à medida que as alterações de código ocorrem sem precisar confirmar suas alterações para o controle de versão. Para obter mais informações sobre o rascunho, consulte a [documentação de rascunho no GitHub][draft-documentation].

Este artigo mostra como usar o pacote de rascunho e executar um aplicativo no AKS.


## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, pode [criar uma conta gratuita](https://azure.microsoft.com/free).
* A [CLI do Azure instalada](/cli/azure/install-azure-cli?view=azure-cli-latest).
* Docker instalado e configurado. O Docker fornece pacotes que configuram o Docker em um sistema [Mac][docker-for-mac], [Windows][docker-for-windows]ou [Linux][docker-for-linux] .
* [Helm instalado](https://github.com/helm/helm/blob/master/docs/install.md).
* [Rascunho instalado][draft-documentation].

## <a name="create-an-azure-kubernetes-service-cluster"></a>Criar um cluster do serviço kubernetes do Azure

Crie um cluster AKS. Os comandos abaixo criam um grupo de recursos chamado MyResource Group e um cluster AKS chamado MyAKS.

```azurecli
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --node-vm-size Standard_DS2_v2 --node-count 1 --generate-ssh-keys
```

## <a name="create-an-azure-container-registry"></a>Criar um Azure Container Registry
Para usar o rascunho para executar seu aplicativo no cluster AKS, você precisa de um registro de contêiner do Azure para armazenar suas imagens de contêiner. O exemplo abaixo usa [AZ ACR Create][az-acr-create] para criar um ACR denominado *MyDraftACR* no grupo de recursos MyResource Group com a SKU *básica* . Você deve fornecer seu próprio nome de registro exclusivo. O nome do registo tem de ser exclusivo no Azure e pode incluir de 5 a 50 carateres alfanuméricos. O SKU *Básico* é um ponto de entrada com otimização de custos para fins de desenvolvimento que fornece um equilíbrio de armazenamento e débito.

```azurecli
az acr create --resource-group MyResourceGroup --name MyDraftACR --sku Basic
```

O resultado será semelhante ao seguinte exemplo. Anote o valor de *loginServer* para seu ACR, pois ele será usado em uma etapa posterior. No exemplo abaixo, *mydraftacr.azurecr.Io* é o *loginServer* para *mydraftacr*.

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


Para que o rascunho use a instância do ACR, primeiro você deve entrar. Use o comando [AZ ACR login][az-acr-login] para entrar. O exemplo abaixo entrará em um ACR chamado *MyDraftACR*.

```azurecli
az acr login --name MyDraftACR
```

O comando devolve a mensagem *Início de sessão com êxito* após a conclusão.

## <a name="create-trust-between-aks-cluster-and-acr"></a>Criar confiança entre o cluster AKS e o ACR

O cluster AKS também precisa de acesso ao ACR para efetuar pull das imagens de contêiner e executá-las. Você permite o acesso ao ACR do AKS estabelecendo uma relação de confiança. Para estabelecer a confiança entre um cluster AKS e um registro ACR, conceda permissões para a entidade de serviço Azure Active Directory usada pelo cluster AKS para acessar o registro ACR. Os comandos a seguir concedem permissões para a entidade de serviço do cluster *MyAKS* no grupo MyResource para o ACR *MyDraftACR* no *MyResource*.

```azurecli
# Get the service principal ID of your AKS cluster
AKS_SP_ID=$(az aks show --resource-group MyResourceGroup --name MyAKS --query "servicePrincipalProfile.clientId" -o tsv)

# Get the resource ID of your ACR instance
ACR_RESOURCE_ID=$(az acr show --resource-group MyResourceGroup --name MyDraftACR --query "id" -o tsv)

# Create a role assignment for your AKS cluster to access the ACR instance
az role assignment create --assignee $AKS_SP_ID --scope $ACR_RESOURCE_ID --role contributor
```

## <a name="connect-to-your-aks-cluster"></a>Conectar-se ao cluster AKS

Para se conectar ao cluster kubernetes do computador local, use [kubectl][kubectl], o cliente de linha de comando kubernetes.

Se utilizar o Azure Cloud Shell, o `kubectl` já está instalado. Também pode instalá-lo a nível local com o comando [az aks install-cli][]:

```azurecli
az aks install-cli
```

Para configurar `kubectl` o para se conectar ao cluster do kubernetes, use o comando [AZ AKs Get-Credentials][] . O exemplo a seguir obtém as credenciais para o cluster AKS chamado *MyAKS* no grupo MyResource:

```azurecli
az aks get-credentials --resource-group MyResourceGroup --name MyAKS
```

## <a name="create-a-service-account-for-helm"></a>Criar uma conta de serviço para Helm

Antes de implantar o Helm em um cluster AKS habilitado para RBAC, você precisa de uma conta de serviço e uma associação de função para o serviço da gaveta. Para obter mais informações sobre como proteger o Helm/gaveta em um cluster habilitado para RBAC, consulte o [gaveta, namespaces e RBAC][tiller-rbac]. Se o cluster AKS não estiver habilitado para RBAC, ignore esta etapa.

Crie um arquivo chamado `helm-rbac.yaml` e copie o seguinte YAML:

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

Crie a conta de serviço e a associação de `kubectl apply` função com o comando:

```console
kubectl apply -f helm-rbac.yaml
```

## <a name="configure-helm"></a>Configurar o Helm
Para implantar um gaveta básico em um cluster AKS, use o comando [init Helm][helm-init] . Se o cluster não estiver habilitado para RBAC, `--service-account` remova o argumento e o valor.

```console
helm init --service-account tiller --node-selectors "beta.kubernetes.io/os"="linux"
```

## <a name="configure-draft"></a>Configurar rascunho

Se você não tiver configurado o rascunho em seu computador local `draft init`, execute:

```console
$ draft init
Installing default plugins...
Installation of default plugins complete
Installing default pack repositories...
...
Happy Sailing!
```

Você também precisa configurar o rascunho para usar o *loginServer* de seu ACR. O comando a seguir `draft config set` usa o `mydraftacr.azurecr.io` para usar como um registro.

```console
draft config set registry mydraftacr.azurecr.io
```

Você configurou o rascunho para usar seu ACR e o rascunho pode enviar imagens de contêiner para o ACR. Quando o rascunho executa seu aplicativo no cluster AKS, nenhuma senha ou segredo é necessário para enviar ou extrair o registro do ACR. Como uma relação de confiança foi criada entre o cluster AKS e o ACR, a autenticação ocorre no nível de Azure Resource Manager, usando Azure Active Directory.

## <a name="download-the-sample-application"></a>Transferir a aplicação de exemplo

Este guia de início rápido usa [um exemplo de aplicativo Java do repositório GitHub de rascunho][example-java]. Clone o aplicativo do GitHub e navegue até o `draft/examples/example-java/` diretório.

```console
git clone https://github.com/Azure/draft
cd draft/examples/example-java/
```

## <a name="run-the-sample-application-with-draft"></a>Executar o aplicativo de exemplo com o rascunho

Use o `draft create` comando para preparar o aplicativo.

```console
draft create
```

Esse comando cria os artefatos que são usados para executar o aplicativo em um cluster kubernetes. Esses itens incluem um Dockerfile, um gráfico do Helm e um arquivo *Draft. toml* , que é o arquivo de configuração de rascunho.

```console
$ draft create

--> Draft detected Java (92.205567%)
--> Ready to sail
```

Para executar o aplicativo de exemplo em seu cluster AKs, use `draft up` o comando.

```console
draft up
```

Esse comando cria o Dockerfile para criar uma imagem de contêiner, envia a imagem por push para o ACR e instala o gráfico Helm para iniciar o aplicativo no AKS. Na primeira vez que você executar esse comando, o envio por push e a extração da imagem de contêiner pode levar algum tempo. Depois que as camadas de base são armazenadas em cache, o tempo necessário para implantar o aplicativo é drasticamente reduzido.

```
$ draft up

Draft Up Started: 'example-java': 01CMZAR1F4T1TJZ8SWJQ70HCNH
example-java: Building Docker Image: SUCCESS ⚓  (73.0720s)
example-java: Pushing Docker Image: SUCCESS ⚓  (19.5727s)
example-java: Releasing Application: SUCCESS ⚓  (4.6979s)
Inspect the logs with `draft logs 01CMZAR1F4T1TJZ8SWJQ70HCNH`
```

## <a name="connect-to-the-running-sample-application-from-your-local-machine"></a>Conectar-se ao aplicativo de exemplo em execução do computador local

Para testar o aplicativo, use o `draft connect` comando.

```console
draft connect
```

Esse comando tem como proxy uma conexão segura com o Pod kubernetes. Ao concluir, o aplicativo pode ser acessado na URL fornecida.

```console
$ draft connect

Connect to java:4567 on localhost:49804
[java]: SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
[java]: SLF4J: Defaulting to no-operation (NOP) logger implementation
[java]: SLF4J: See https://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
[java]: == Spark has ignited ...
[java]: >> Listening on 0.0.0.0:4567
```

Navegue até o aplicativo em um navegador usando a `localhost` URL para ver o aplicativo de exemplo. No exemplo acima, a URL é `http://localhost:49804`. Pare a conexão usando `Ctrl+c`.

## <a name="access-the-application-on-the-internet"></a>Acessar o aplicativo na Internet

A etapa anterior criou uma conexão proxy com o Pod do aplicativo no cluster AKS. À medida que desenvolve e testa seu aplicativo, talvez você queira disponibilizar o aplicativo na Internet. Para expor um aplicativo na Internet, você pode criar um serviço kubernetes com um tipo de balanceador de [carga][kubernetes-service-loadbalancer].

Atualize `charts/example-java/values.yaml` para criar um serviço Balancer. Altere o valor de *Service. Type* de *ClusterIP* paraBalancer.

```yaml
...
service:
  name: java
  type: LoadBalancer
  externalPort: 80
  internalPort: 4567
...
```

Salve as alterações, feche o arquivo e execute `draft up` para executar novamente o aplicativo.

```console
draft up
```

Leva alguns minutos para que o serviço retorne um endereço IP público. Para monitorar o progresso, use o `kubectl get service` comando com o parâmetro *Watch* :

```console
$ kubectl get service --watch

NAME                TYPE          CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
example-java-java   LoadBalancer  10.0.141.72   <pending>     80:32150/TCP   2m
...
example-java-java   LoadBalancer   10.0.141.72   52.175.224.118  80:32150/TCP   7m
```

Navegue até o balanceador de carga do seu aplicativo em um navegador usando o *IP externo* para ver o aplicativo de exemplo. No exemplo acima, o IP é `52.175.224.118`.

## <a name="iterate-on-the-application"></a>Iterar no aplicativo

Você pode iterar seu aplicativo fazendo alterações localmente e executando novamente `draft up`.

Atualize a mensagem retornada na [linha 7 de src/main/java/HelloWorld/Hello. java][example-java-hello-l7]

```java
    public static void main(String[] args) {
        get("/", (req, res) -> "Hello World, I'm Java in AKS!");
    }
```

Execute o `draft up` comando para reimplantar o aplicativo:

```console
$ draft up

Draft Up Started: 'example-java': 01CMZC9RF0TZT7XPWGFCJE15X4
example-java: Building Docker Image: SUCCESS ⚓  (25.0202s)
example-java: Pushing Docker Image: SUCCESS ⚓  (7.1457s)
example-java: Releasing Application: SUCCESS ⚓  (3.5773s)
Inspect the logs with `draft logs 01CMZC9RF0TZT7XPWGFCJE15X4`
```

Para ver o aplicativo atualizado, navegue até o endereço IP do balanceador de carga novamente e verifique se as alterações aparecem.

## <a name="delete-the-cluster"></a>Eliminar o cluster

Quando o cluster não for mais necessário, use o comando [AZ Group Delete][az-group-delete] para remover o grupo de recursos, o cluster AKs, o registro de contêiner, as imagens de contêiner armazenadas lá e todos os recursos relacionados.

```azurecli-interactive
az group delete --name MyResourceGroup --yes --no-wait
```

> [!NOTE]
> Quando elimina o cluster, o principal de serviço do Azure Active Directory utilizado pelo cluster do AKS não é removido. Para obter as etapas sobre como remover a entidade de serviço, consulte [considerações e exclusão da entidade de serviço AKs][sp-delete].

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre como usar o rascunho, consulte a documentação de rascunho no GitHub.

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
