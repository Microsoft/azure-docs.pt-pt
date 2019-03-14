---
title: Criar nós virtuais com a CLI do Azure em serviços do Azure Kubernetes (AKS)
description: Saiba como utilizar a CLI do Azure para criar um cluster de serviços de Kubernetes do Azure (AKS) que usa nós virtuais para executar os pods.
services: container-service
author: iainfoulds
ms.topic: conceptual
ms.service: container-service
ms.date: 12/03/2018
ms.author: iainfou
ms.openlocfilehash: e47817f70a727fe815e5d7e61ad7820121f26d97
ms.sourcegitcommit: d89b679d20ad45d224fd7d010496c52345f10c96
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57791414"
---
# <a name="create-and-configure-an-azure-kubernetes-services-aks-cluster-to-use-virtual-nodes-using-the-azure-cli"></a>Criar e configurar um cluster de serviços de Kubernetes do Azure (AKS) para utilizar nós virtuais com a CLI do Azure

Rapidamente, dimensionar cargas de trabalho de aplicação num cluster do Azure Kubernetes Service (AKS), pode utilizar nós virtuais. Connosco virtuais, tem o rápido provisionamento de pods e pague apenas por segundo para o tempo de execução. Não precisa de esperar para dimensionamento automático de cluster do Kubernetes para implementar nós de computação VM para executar os pods adicionais. Este artigo mostra-lhe como criar e configurar os recursos de rede virtual e o cluster do AKS, em seguida, ativar nós virtuais.

> [!IMPORTANT]
> Nós virtuais para o AKS estão atualmente nos **pré-visualização**. As pré-visualizações são tornadas disponíveis para si na condição de concordar com os [termos suplementares de utilização](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Alguns aspetos desta funcionalidade podem alterar-se após a disponibilidade geral (GA).

## <a name="before-you-begin"></a>Antes de começar

Nós virtuais de ativar a comunicação de rede entre os pods que são executados no ACI e o cluster do AKS. Para fornecer esta comunicação, é criada uma sub-rede de rede virtual e permissões delegadas são atribuídas. Nós virtuais funcionam apenas com clusters do AKS criados usando *avançadas* funcionamento em rede. Por predefinição, os clusters do AKS são criados com *básica* funcionamento em rede. Este artigo mostra-lhe como criar uma rede virtual e sub-redes, em seguida, implementar um cluster do AKS que utiliza o sistema de rede avançado.

Se não tiver utilizado anteriormente ACI, registe o fornecedor de serviço com a sua subscrição. Pode verificar o estado do uso de registo de fornecedor do ACI a [lista de fornecedores de az] [ az-provider-list] de comando, conforme mostrado no exemplo a seguir:

```azurecli-interactive
az provider list --query "[?contains(namespace,'Microsoft.ContainerInstance')]" -o table
```

O *Microsoft.ContainerInstance* fornecedor deve reportar como *registado*, conforme mostrado no seguinte exemplo:

```
Namespace                    RegistrationState
---------------------------  -------------------
Microsoft.ContainerInstance  Registered
```

Se o fornecedor é apresentado como *NotRegistered*, registar o fornecedor a utilizar o [Registre-se fornecedor de az] [ az-provider-register] conforme mostrado no exemplo a seguir:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerInstance
```

## <a name="preview-limitations"></a>Limitações de pré-visualização

Enquanto esta funcionalidade está em pré-visualização, as seguintes regiões são suportadas para implementações:

* Leste da Austrália (australiaeast)
* Este dos E.U.A. (eastus)
* E.u.a. Centro-Oeste (EUA)
* Europa Ocidental (westeurope)
* E.U.A. oeste (westus)

## <a name="launch-azure-cloud-shell"></a>Iniciar o Azure Cloud Shell

O Azure Cloud Shell é um shell interativo gratuito que pode utilizar para executar os passos neste artigo. Tem as ferramentas comuns do Azure pré-instaladas e configuradas para utilização com a sua conta.

Para abrir o Cloud Shell, selecione **experimente** do canto superior direito de um bloco de código. Também pode iniciar o Cloud Shell num separador do browser separado ao aceder a [https://shell.azure.com/bash](https://shell.azure.com/bash). Selecione **Copiar** para copiar os blocos de código, cole-o no Cloud Shell e prima Enter para executá-lo.

Se preferir instalar e utilizar a CLI localmente, este artigo requer a CLI do Azure versão 2.0.49 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um grupo de recursos do Azure é um grupo lógico, no qual os recursos do Azure são implementados e geridos. Crie um grupo de recursos com o comando [az group create][az-group-create]. O exemplo seguinte cria um grupo de recursos com o nome *myResourceGroup* na localização *westus*.

```azurecli-interactive
az group create --name myResourceGroup --location westus
```

## <a name="create-a-virtual-network"></a>Criar uma rede virtual

Criar uma rede virtual com o [criar a vnet de rede de az] [ az-network-vnet-create] comando. O exemplo seguinte cria um nome de rede virtual *myVnet* com o prefixo de endereço *10.0.0.0/8*e uma sub-rede denominada *myAKSSubnet*. O prefixo de endereço desta sub-rede está predefinido para *10.240.0.0/16*:

```azurecli-interactive
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefixes 10.0.0.0/8 \
    --subnet-name myAKSSubnet \
    --subnet-prefix 10.240.0.0/16
```

Agora, crie uma sub-rede adicional para nós virtuais utilizando o [criar a sub-rede de vnet de rede de az] [ az-network-vnet-subnet-create] comando. O exemplo seguinte cria uma sub-rede denominada *myVirtualNodeSubnet* com o prefixo de endereço *10.241.0.0/16*.

```azurecli-interactive
az network vnet subnet create \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name myVirtualNodeSubnet \
    --address-prefix 10.241.0.0/16
```

## <a name="create-a-service-principal"></a>Criar um principal de serviço

Para permitir que um cluster do AKS interaja com outros recursos do Azure, é utilizado um principal de serviço do Azure Active Directory. Este principal de serviço pode ser criado automaticamente com a CLI do Azure ou o portal, ou pode criar previamente um e atribuir permissões adicionais.

Criar um principal de serviço através da [az ad sp create-for-rbac] [ az-ad-sp-create-for-rbac] comando. O parâmetro `--skip-assignment` limita a atribuição de permissões adicionais.

```azurecli-interactive
az ad sp create-for-rbac --skip-assignment
```

O resultado é semelhante ao seguinte exemplo:

```
{
  "appId": "bef76eb3-d743-4a97-9534-03e9388811fc",
  "displayName": "azure-cli-2018-11-21-18-42-00",
  "name": "http://azure-cli-2018-11-21-18-42-00",
  "password": "1d257915-8714-4ce7-a7fb-0e5a5411df7f",
  "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db48"
}
```

Anote o *appId* e a *palavra-passe*. São utilizados os seguintes valores nos passos seguintes.

## <a name="assign-permissions-to-the-virtual-network"></a>Atribuir permissões para a rede virtual

Para permitir que o cluster utilizar e gerir a rede virtual, tem de conceder o principal de serviço do AKS os direitos corretos para usar os recursos de rede.

Primeiro, obtenha o recurso de rede virtual ID usando [show de vnet de rede de az][az-network-vnet-show]:

```azurecli-interactive
az network vnet show --resource-group myResourceGroup --name myVnet --query id -o tsv
```

Para conceder o acesso correto para o cluster do AKS utilizar a rede virtual, criar uma atribuição de função com o [criação da atribuição de função de az] [ az-role-assignment-create] comando. Substitua `<appId`> e `<vnetId>` pelos valores recolhidos nos dois passos anteriores.

```azurecli-interactive
az role assignment create --assignee <appId> --scope <vnetId> --role Contributor
```

## <a name="create-an-aks-cluster"></a>Criar um cluster do AKS (Create an AKS cluster)

Implementar um cluster do AKS para a sub-rede AKS que criou no passo anterior. Obter o ID de utilizar esta sub-rede [show de sub-rede de vnet de rede de az][az-network-vnet-subnet-show]:

```azurecli-interactive
az network vnet subnet show --resource-group myResourceGroup --vnet-name myVnet --name myAKSSubnet --query id -o tsv
```

Utilize o comando [az aks create][az-aks-create] para criar um cluster AKS. O exemplo seguinte cria um cluster com o nome *myAKSCluster* com um nó. Substitua `<subnetId>` com o ID de obteve no passo anterior e, em seguida `<appId>` e `<password>` com o 

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --network-plugin azure \
    --service-cidr 10.0.0.0/16 \
    --dns-service-ip 10.0.0.10 \
    --docker-bridge-address 172.17.0.1/16 \
    --vnet-subnet-id <subnetId> \
    --service-principal <appId> \
    --client-secret <password>
```

Ao fim de vários minutos, o comando é concluído e devolve informações sobre o cluster no formato JSON.

## <a name="enable-virtual-nodes"></a>Ativar nós virtuais

Para fornecer funcionalidade adicional, o conector de nós virtuais utiliza uma extensão de CLI do Azure. Antes de poder ativar o conector de virtual de nós, primeiro de instalar a extensão com o [Adicionar extensão az] [ az-extension-add] comando:

```azurecli-interactive
az extension add --source https://aksvnodeextension.blob.core.windows.net/aks-virtual-node/aks_virtual_node-0.2.0-py2.py3-none-any.whl
```

Para ativar a nós virtuais, agora utilizar o [enable-complementos de az aks] [ az-aks-enable-addons] comando. O exemplo seguinte utiliza a sub-rede denominada *myVirtualNodeSubnet* criado num passo anterior:

```azurecli-interactive
az aks enable-addons \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --addons virtual-node \
    --subnet-name myVirtualNodeSubnet
```

## <a name="connect-to-the-cluster"></a>Ligar ao cluster

Para configurar `kubectl` para se ligar ao cluster do Kubernetes, utilize o comando [az aks get-credentials][az-aks-get-credentials]. Este passo transfere credenciais e configura a CLI do Kubernetes para as utilizar.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Para verificar a ligação ao cluster, utilize o comando [kubectl get][kubectl-get] para devolver uma lista de nós do cluster.

```console
kubectl get nodes
```

O resultado de exemplo seguinte mostra o único nó de VM criada e, em seguida, o nó virtual para Linux, *virtual-nó-aci-linux*:

```
$ kubectl get nodes

NAME                          STATUS    ROLES     AGE       VERSION
virtual-node-aci-linux        Ready     agent     28m       v1.11.2
aks-agentpool-14693408-0      Ready     agent     32m       v1.11.2
```

## <a name="deploy-a-sample-app"></a>Implementar uma aplicação de exemplo

Crie um ficheiro denominado `virtual-node.yaml` e copie o YAML seguinte. Para agendar o contentor no nó, uma [nodeSelector] [ node-selector] e [toleration] [ toleration] são definidos.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: aci-helloworld
spec:
  replicas: 1
  selector:
    matchLabels:
      app: aci-helloworld
  template:
    metadata:
      labels:
        app: aci-helloworld
    spec:
      containers:
      - name: aci-helloworld
        image: microsoft/aci-helloworld
        ports:
        - containerPort: 80
      nodeSelector:
        kubernetes.io/role: agent
        beta.kubernetes.io/os: linux
        type: virtual-kubelet
      tolerations:
      - key: virtual-kubelet.io/provider
        operator: Exists
      - key: azure.com/aci
        effect: NoSchedule
```

Execute a aplicação com o [aplicam-se de kubectl] [ kubectl-apply] comando.

```console
kubectl apply -f virtual-node.yaml
```

Utilize o [kubectl obter pods] [ kubectl-get] comando com o `-o wide` argumento para uma lista de pods e o nó agendado de saída. Tenha em atenção que o `aci-helloworld` pod foi agendada ao `virtual-node-aci-linux` nó.

```
$ kubectl get pods -o wide

NAME                            READY     STATUS    RESTARTS   AGE       IP           NODE
aci-helloworld-9b55975f-bnmfl   1/1       Running   0          4m        10.241.0.4   virtual-node-aci-linux
```

O pod é atribuído um endereço IP da sub-rede da rede virtual do Azure delegada para utilização connosco virtuais.

> [!NOTE]
> Se utilizar imagens armazenadas no Azure Container Registry, [configurar e utilizar um segredo do Kubernetes][acr-aks-secrets]. Uma limitação atual dos nós virtuais de pré-visualização é que não é possível utilizar integrado a autenticação do principal de serviço do Azure AD. Se não usar um segredo, pods agendadas em nós virtuais não conseguem iniciar e reportar o erro `HTTP response status code 400 error code "InaccessibleImage"`.

## <a name="test-the-virtual-node-pod"></a>O pod do nó virtual de teste

Para testar o pod em execução no nó virtual, navegue para a aplicação de demonstração com um cliente web. Como o pod está atribuído um endereço IP interno, pode testar rapidamente a essa conectividade de pod outro no cluster do AKS. Criar um pod de teste e anexar uma sessão de terminal a ele:

```console
kubectl run -it --rm virtual-node-test --image=debian
```

Instale `curl` no pod com `apt-get`:

```console
apt-get update && apt-get install -y curl
```

Agora aceder o endereço da sua utilização de pod `curl`, tal como *http://10.241.0.4*. Fornecer seu próprio endereço IP mostrado no anterior `kubectl get pods` comando:

```console
curl -L http://10.241.0.4
```

A aplicação de demonstração é apresentada, conforme mostrado no seguinte exemplo condensado:

```
$ curl -L 10.241.0.4

<html>
<head>
  <title>Welcome to Azure Container Instances!</title>
</head>
[...]
```

Feche a sessão de terminal para seu pod de teste com `exit`. Quando a sessão seja terminada, o pod está a eliminado.

## <a name="remove-virtual-nodes"></a>Remover nós virtuais

Se já não pretenda utilizar nós virtuais, pode desativá-las com o [az aks disable-complementos] [ az aks disable-addons] comando. O exemplo seguinte desativa os nós virtuais de Linux:

```azurecli-interactive
az aks disable-addons --resource-group myResourceGroup --name myAKSCluster --addons virtual-node
```

Agora, remova os recursos de rede virtual e o grupo de recursos:

```azurecli-interactive
# Change the name of your resource group and network resources as needed
RES_GROUP=myResourceGroup

# Get network profile ID
NETWORK_PROFILE_ID=$(az network profile list --resource-group $RES_GROUP --query [0].id --output tsv)

# Delete the network profile
az network profile delete --id $NETWORK_PROFILE_ID -y

# Get the service association link (SAL) ID
SAL_ID=$(az network vnet subnet show --resource-group $RES_GROUP --vnet-name myVnet --name myVirtualNodeSubnet --query id --output tsv)/providers/Microsoft.ContainerInstance/serviceAssociationLinks/default

# Delete the default SAL ID for the subnet
az resource delete --ids $SAL_ID --api-version 2018-07-01

# Delete the subnet delegation to Azure Container Instances
az network vnet subnet update --resource-group $RES_GROUP --vnet-name myVnet --name myVirtualNodeSubnet --remove delegations 0
```

## <a name="next-steps"></a>Passos Seguintes

Neste artigo, um pod foi agendado no nó virtual e atribuído um endereço IP privado, interno. Em vez disso, pode criar uma implementação de serviço e encaminhar o tráfego para seu pod através de um balanceador de carga ou controlador de entradas. Para obter mais informações, consulte [criar um controlador de entrada básico no AKS][aks-basic-ingress].

Nós virtuais são, muitas vezes, um componente de uma solução de dimensionamento no AKS. Para obter mais informações sobre soluções de dimensionamento, veja os artigos seguintes:

- [Utilizar o dimensionamento automático horizontal de pods de Kubernetes][aks-hpa]
- [Utilizar o dimensionamento automático de cluster de Kubernetes][aks-cluster-autoscaler]

<!-- LINKS - external -->
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[node-selector]:https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[toleration]: https://kubernetes.io/docs/concepts/configuration/taint-and-toleration/

<!-- LINKS - internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[az-group-create]: /cli/azure/group#az-group-create
[az-network-vnet-create]: /cli/azure/network/vnet#az-network-vnet-create
[az-network-vnet-subnet-create]: /cli/azure/network/vnet/subnet#az-network-vnet-subnet-create
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-network-vnet-show]: /cli/azure/network/vnet#az-network-vnet-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet#az-network-vnet-subnet-show
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-enable-addons]: /cli/azure/aks#az-aks-enable-addons
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az aks disable-addons]: /cli/azure/aks#az-aks-disable-addons
[aks-hpa]: tutorial-kubernetes-scale.md
[aks-cluster-autoscaler]: autoscaler.md
[aks-basic-ingress]: ingress-basic.md
[az-provider-list]: /cli/azure/provider#az-provider-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[acr-aks-secrets]: ../container-registry/container-registry-auth-aks.md#access-with-kubernetes-secret
