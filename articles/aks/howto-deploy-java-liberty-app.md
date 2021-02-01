---
title: Implementar uma aplicação Java com Open Liberty ou WebSphere Liberty num cluster Azure Kubernetes Service (AKS)
description: Implemente uma aplicação Java com Open Liberty ou WebSphere Liberty num cluster Azure Kubernetes Service (AKS).
author: jiangma
ms.author: jiangma
ms.service: container-service
ms.topic: conceptual
ms.date: 02/01/2021
keywords: java, jacarta, javaee, microprofile, open-liberty, websphere-liberty, aks, kubernetes
ms.openlocfilehash: 93ffa3ded4d0771438c5d6a2dc23e6e184f04fe2
ms.sourcegitcommit: 2dd0932ba9925b6d8e3be34822cc389cade21b0d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/01/2021
ms.locfileid: "99227628"
---
# <a name="deploy-a-java-application-with-open-liberty-or-websphere-liberty-on-an-azure-kubernetes-service-aks-cluster"></a>Implementar uma aplicação Java com Open Liberty ou WebSphere Liberty num cluster Azure Kubernetes Service (AKS)

Este guia demonstra como executar a sua aplicação Java, Java EE, [Jakarta EE,](https://jakarta.ee/)ou [MicroProfile](https://microprofile.io/) no tempo de funcionamento da Open Liberty ou WebSphere Liberty e, em seguida, implementar a aplicação contentorizada num cluster AKS utilizando o Operador da Liberdade Aberta. O Operador da Liberdade Aberta simplifica a implementação e gestão de aplicações em execução em clusters Open Liberty Kubernetes. Também pode realizar operações mais avançadas, como recolher vestígios e despejos utilizando o operador. Este artigo irá acompanhá-lo através da preparação de uma aplicação Liberty, construindo a aplicação Imagem Docker e executando a aplicação contentorizada em um cluster AKS.  Para mais detalhes sobre a Open Liberty, consulte [a página do projeto Open Liberty.](https://openliberty.io/) Para mais detalhes sobre a IBM WebSphere Liberty, consulte [a página de produto WebSphere Liberty](https://www.ibm.com/cloud/websphere-liberty).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

* Este artigo requer a versão mais recente do Azure CLI. Se utilizar o Azure Cloud Shell, a versão mais recente já está instalada.
* Se executar os comandos neste guia localmente (em vez de Azure Cloud Shell):
  * Prepare uma máquina local com sistema operativo unix instalado (por exemplo, Ubuntu, macOS).
  * Instale uma implementação Java SE (por exemplo, [AdoptOpenJDK OpenJDK 8 LTS/OpenJ9](https://adoptopenjdk.net/?variant=openjdk8&jvmVariant=openj9)).
  * Instale [Maven](https://maven.apache.org/download.cgi) 3.5.0 ou superior.
  * Instale [o Docker](https://docs.docker.com/get-docker/) para o seu SISTEMA.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um grupo de recursos do Azure é um grupo lógico, no qual os recursos do Azure são implementados e geridos. Crie um grupo de recursos, *o java-liberty-project* usando o [grupo az criar](/cli/azure/group?view=azure-cli-latest&preserve-view=true#az_group_create) comando na localização *leste.* Será utilizado para a criação da instância do Registo do Contentor Azure (ACR) e do cluster AKS mais tarde. 

```azurecli-interactive
az group create --name java-liberty-project --location eastus
```

## <a name="create-an-acr-instance"></a>Criar uma instância ACR

Utilize o [comando az acr para](/cli/azure/acr?view=azure-cli-latest&preserve-view=true#az_acr_create) criar a instância ACR. O exemplo a seguir cria uma instância ACR chamada *seu uniqueacrname*. Certifique-se *de que o seu uniqueacrname* é único dentro de Azure.

```azurecli-interactive
az acr create --resource-group java-liberty-project --name youruniqueacrname --sku Basic --admin-enabled
```

Após um curto período de tempo, deverá ver uma saída JSON que contém:

```output
  "provisioningState": "Succeeded",
  "publicNetworkAccess": "Enabled",
  "resourceGroup": "java-liberty-project",
```

### <a name="connect-to-the-acr-instance"></a>Ligar-se à instância ACR

Para empurrar uma imagem para a instância ACR, você precisa entrar nele primeiro. Executar os seguintes comandos para verificar a ligação:

```azurecli-interactive
REGISTRY_NAME=youruniqueacrname
LOGIN_SERVER=$(az acr show -n $REGISTRY_NAME --query 'loginServer' -o tsv)
USER_NAME=$(az acr credential show -n $REGISTRY_NAME --query 'username' -o tsv)
PASSWORD=$(az acr credential show -n $REGISTRY_NAME --query 'passwords[0].value' -o tsv)

docker login $LOGIN_SERVER -u $USER_NAME -p $PASSWORD
```

Deverá ver `Login Succeeded` no final da saída de comando se iniciou sessão na instância ACR com sucesso.

## <a name="create-an-aks-cluster"></a>Criar um cluster do AKS (Create an AKS cluster)

Utilize o comando [az aks create](/cli/azure/aks?view=azure-cli-latest&preserve-view=true#az_aks_create) para criar um cluster AKS. O exemplo seguinte cria um cluster com o nome *myAKSCluster* com um nó. Isto levará vários minutos para ser concluído.

```azurecli-interactive
az aks create --resource-group java-liberty-project --name myAKSCluster --node-count 1 --generate-ssh-keys --enable-managed-identity
```

Após alguns minutos, o comando completa e devolve informações formatadas com JSON sobre o cluster, incluindo as seguintes:

```output
  "nodeResourceGroup": "MC_java-liberty-project_myAKSCluster_eastus",
  "privateFqdn": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "java-liberty-project",
```

### <a name="connect-to-the-aks-cluster"></a>Ligue-se ao cluster AKS

Para gerir um cluster Kubernetes, você usa [kubectl,](https://kubernetes.io/docs/reference/kubectl/overview/)o cliente da linha de comando Kubernetes. Se utilizar a Azure Cloud Shell, `kubectl` já está instalada. Para instalar `kubectl` localmente, utilize o comando [az aks instalar-cli:](/cli/azure/aks?view=azure-cli-latest&preserve-view=true#az_aks_install_cli)

```azurecli-interactive
az aks install-cli
```

Para configurar `kubectl` para se ligar ao cluster do Kubernetes, utilize o comando [az aks get-credentials](/cli/azure/aks?view=azure-cli-latest&preserve-view=true#az_aks_get_credentials). Este comando descarrega credenciais e configura o CLI de Kubernetes para usá-las.

```azurecli-interactive
az aks get-credentials --resource-group java-liberty-project --name myAKSCluster --overwrite-existing
```

> [!NOTE]
> O comando acima utiliza a localização padrão para o [ficheiro de configuração Kubernetes,](https://kubernetes.io/docs/concepts/configuration/organize-cluster-access-kubeconfig/)que é `~/.kube/config` . Pode especificar uma localização diferente para o seu ficheiro de configuração Kubernetes utilizando *o ficheiro --file*.

Para verificar a ligação ao cluster, utilize o comando [kubectl get]( https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get) para devolver uma lista de nós do cluster.

```azurecli-interactive
kubectl get nodes
```

A saída de exemplo seguinte mostra o nó único criado nos passos anteriores. Certifique-se de que o estado do nó está *pronto:*

```output
NAME                                STATUS   ROLES   AGE     VERSION
aks-nodepool1-xxxxxxxx-yyyyyyyyyy   Ready    agent   76s     v1.18.10
```

## <a name="install-open-liberty-operator"></a>Instalar operador de liberdade aberta

Depois de criar e ligar ao cluster, instale o [Operador da Liberdade Aberta](https://github.com/OpenLiberty/open-liberty-operator/tree/master/deploy/releases/0.7.0) executando os seguintes comandos.

```azurecli-interactive
OPERATOR_NAMESPACE=default
WATCH_NAMESPACE='""'

# Install Custom Resource Definitions (CRDs) for OpenLibertyApplication
kubectl apply -f https://raw.githubusercontent.com/OpenLiberty/open-liberty-operator/master/deploy/releases/0.7.0/openliberty-app-crd.yaml

# Install cluster-level role-based access
curl -L https://raw.githubusercontent.com/OpenLiberty/open-liberty-operator/master/deploy/releases/0.7.0/openliberty-app-cluster-rbac.yaml \
      | sed -e "s/OPEN_LIBERTY_OPERATOR_NAMESPACE/${OPERATOR_NAMESPACE}/" \
      | kubectl apply -f -

# Install the operator
curl -L https://raw.githubusercontent.com/OpenLiberty/open-liberty-operator/master/deploy/releases/0.7.0/openliberty-app-operator.yaml \
      | sed -e "s/OPEN_LIBERTY_WATCH_NAMESPACE/${WATCH_NAMESPACE}/" \
      | kubectl apply -n ${OPERATOR_NAMESPACE} -f -
```

## <a name="build-application-image"></a>Construir imagem de aplicação

Para implementar e executar a sua aplicação Liberty no cluster AKS, containerize a sua aplicação como uma imagem docker usando [imagens de recipientes Open Liberty](https://github.com/OpenLiberty/ci.docker) ou [imagens de contentores WebSphere Liberty](https://github.com/WASdev/ci.docker).

1. Clone o código de amostra para este guia. A amostra está no [GitHub.](https://github.com/Azure-Samples/open-liberty-on-aks)
1. Mude o diretório para `javaee-app-simple-cluster` o seu clone local.
1. Corra `mvn clean package` para embalar o pedido.
1. Executar um dos seguintes comandos para construir a imagem da aplicação e empurrá-la para a instância ACR.
   * Construa com imagem base Open Liberty se preferir usar o Open Liberty como uma java leve de código aberto™ tempo de execução:

     ```azurecli-interactive
     # Build and tag application image. This will cause the ACR instance to pull the necessary Open Liberty base images.
     az acr build -t javaee-cafe-simple:1.0.0 -r $REGISTRY_NAME .
     ```

   * Construa com a imagem base da WebSphere Liberty se preferir usar uma versão comercial da Open Liberty:

     ```azurecli-interactive
     # Build and tag application image. This will cause the ACR instance to pull the necessary WebSphere Liberty base images.
     az acr build -t javaee-cafe-simple:1.0.0 -r $REGISTRY_NAME --file=Dockerfile-wlp .
     ```

## <a name="deploy-application-on-the-aks-cluster"></a>Implementar aplicação no cluster AKS

Siga os passos abaixo para implementar a aplicação Liberty no cluster AKS.

1. Crie um segredo de puxar para que o cluster AKS seja autenticado para retirar a imagem da instância ACR.

   ```azurecli-interactive
   kubectl create secret docker-registry acr-secret \
      --docker-server=${LOGIN_SERVER} \
      --docker-username=${USER_NAME} \
      --docker-password=${PASSWORD}
   ```

1. Verifique se o diretório de trabalho atual é `javaee-app-simple-cluster` do seu clone local.
1. Execute os seguintes comandos para implementar a sua aplicação Liberty com 3 réplicas para o cluster AKS. A saída do comando também é mostrada em linha.

   ```azurecli-interactive
   # Create OpenLibertyApplication "javaee-app-simple-cluster"
   cat openlibertyapplication.yaml | sed -e "s/\${Container_Registry_URL}/${LOGIN_SERVER}/g" | sed -e "s/\${REPLICAS}/3/g" | kubectl apply -f -

   openlibertyapplication.openliberty.io/javaee-app-simple-cluster created

   # Check if OpenLibertyApplication instance is created
   kubectl get openlibertyapplication javaee-app-simple-cluster

   NAME                        IMAGE                                                   EXPOSED   RECONCILED   AGE
   javaee-app-simple-cluster   youruniqueacrname.azurecr.io/javaee-cafe-simple:1.0.0             True         59s

   # Check if deployment created by Operator is ready
   kubectl get deployment javaee-app-simple-cluster --watch

   NAME                        READY   UP-TO-DATE   AVAILABLE   AGE
   javaee-app-simple-cluster   0/3     3            0           20s
   ```

1. Aguarde até ver `3/3` debaixo da coluna e por baixo da `READY` `3` `AVAILABLE` coluna, use para parar o processo de `CTRL-C` `kubectl` observação.

### <a name="test-the-application"></a>Testar a aplicação

Quando a aplicação é executado, um serviço de balançador de carga Kubernetes expõe a extremidade frontal da aplicação para a internet. Este processo pode demorar um pouco a ser concluído.

Para monitorizar o progresso, utilize o comando [kubectl get service](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get) com o argumento `--watch`.

```azurecli-interactive
kubectl get service javaee-app-simple-cluster --watch

NAME                        TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)          AGE
javaee-app-simple-cluster   LoadBalancer   10.0.251.169   52.152.189.57   9080:31732/TCP   68s
```

Aguarde até que o endereço *EXTERNAL-IP* mude de *pendente* para um endereço IP público real, use `CTRL-C` para parar o processo do `kubectl` relógio.

Abra um navegador web para o endereço IP externo e porta do seu serviço `52.152.189.57:9080` (para o exemplo acima) para ver a página inicial da aplicação. Deve ver o nome do pod das suas réplicas de aplicação exibidas no topo esquerdo da página. Aguarde alguns minutos e refresque a página, provavelmente verá um nome diferente do pod apresentado devido ao equilíbrio de carga fornecido pelo cluster AKS.

:::image type="content" source="./media/howto-deploy-java-liberty-app/java-liberty-app-aks-deployed-success.png" alt-text="Aplicação java liberty implementada com sucesso na AKS":::

>[!NOTE]
> - Atualmente a aplicação não está a utilizar HTTPS. Recomenda-se [o ENABLE TLS com os seus próprios certificados.](ingress-own-tls.md)

## <a name="clean-up-the-resources"></a>Limpar os recursos

Para evitar as acusações do Azure, devias limpar recursos não necessários.  Quando o cluster já não for necessário, utilize o comando de eliminação do [grupo az](/cli/azure/group?view=azure-cli-latest&preserve-view=true#az_group_delete) para remover o grupo de recursos, o serviço de contentores, o registo do contentor e todos os recursos conexos.

```azurecli-interactive
az group delete --name java-liberty-project --yes --no-wait
```

## <a name="next-steps"></a>Passos seguintes

Pode aprender mais com as referências utilizadas neste guia:

* [Azure Kubernetes Service](https://azure.microsoft.com/free/services/kubernetes-service/)
* [Liberdade Aberta](https://openliberty.io/)
* [Operador de Liberdade Aberta](https://github.com/OpenLiberty/open-liberty-operator)
* [Configuração do servidor de liberdade aberta](https://openliberty.io/docs/ref/config/)
* [Liberty Maven Plugin](https://github.com/OpenLiberty/ci.maven#liberty-maven-plugin)
* [Imagens de recipientes de liberdade abertas](https://github.com/OpenLiberty/ci.docker)
* [Imagens do recipiente da liberdade da WebSphere](https://github.com/WASdev/ci.docker)
