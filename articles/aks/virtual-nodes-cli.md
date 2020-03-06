---
title: Crie nós virtuais utilizando o Azure CLI em Serviços Azure Kubernetes (AKS)
description: Aprenda a usar o Azure CLI para criar um cluster Azure Kubernetes Services (AKS) que usa nós virtuais para executar cápsulas.
services: container-service
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: 2b726dff1e2c23b94118a11fb6b6ccf1f9622d4d
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78374537"
---
# <a name="create-and-configure-an-azure-kubernetes-services-aks-cluster-to-use-virtual-nodes-using-the-azure-cli"></a>Crie e configure um cluster Azure Kubernetes Services (AKS) para utilizar nós virtuais usando o Azure CLI

Para escalar rapidamente as cargas de trabalho de aplicação num cluster do Serviço Azure Kubernetes (AKS), pode utilizar nós virtuais. Com nós virtuais, você tem um fornecimento rápido de casulos, e só paga por segundo pelo seu tempo de execução. Não é preciso esperar que o autoscaler do cluster Kubernetes implemente os nós de computação VM para executar as cápsulas adicionais. Os nódosos virtuais são suportados apenas com cápsulas linux e nódosos.

Este artigo mostra-lhe como criar e configurar os recursos de rede virtuais e o cluster AKS, e depois ativar nós virtuais.

## <a name="before-you-begin"></a>Antes de começar

Os nós virtuais permitem a comunicação de rede entre as cápsulas que funcionam no ACI e no cluster AKS. Para fornecer esta comunicação, é criada uma subnet de rede virtual e são atribuídas permissões delegadas. Os nós virtuais só funcionam com clusters AKS criados usando redes *avançadas.* Por padrão, os clusters AKS são criados com networking *básico.* Este artigo mostra-lhe como criar uma rede virtual e subredes, em seguida, implementar um cluster AKS que usa networking avançado.

Se não utilizou previamente o ACI, registe o prestador de serviços com a sua subscrição. Pode verificar o estado do registo do fornecedor ACI utilizando o comando da [lista de fornecedores az,][az-provider-list] como se pode ver no seguinte exemplo:

```azurecli-interactive
az provider list --query "[?contains(namespace,'Microsoft.ContainerInstance')]" -o table
```

O fornecedor *Microsoft.ContainerInstance* deve reportar como *Registado*, como mostra a seguinte saída exemplo:

```
Namespace                    RegistrationState
---------------------------  -------------------
Microsoft.ContainerInstance  Registered
```

Se o fornecedor aparecer como *NotRegistered*, registe o fornecedor utilizando o registo do [fornecedor az,][az-provider-register] tal como mostrado no seguinte exemplo:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerInstance
```

## <a name="regional-availability"></a>Disponibilidade regional

As seguintes regiões são apoiadas para implantações de nóvirtuais:

* Austrália Leste (austráliaeast)
* Eua Central (central)
* Leste dos EUA (leste)
* Leste dos EUA 2 (lesteus2)
* Japão Leste (japãoeast)
* Norte da Europa (norte da Europa)
* Sudeste Asiático (sudeste asiático)
* Centro-Oeste dos EUA (centro-oeste)
* Europa Ocidental (Europa Ocidental)
* Oeste dos EUA (westus)
* Oeste dos EUA 2 (westus2)

## <a name="known-limitations"></a>Limitações conhecidas
A funcionalidade Nós virtuais está fortemente dependente do conjunto de funcionalidades da ACI. Os seguintes cenários ainda não são suportados com nódosos virtuais

* Usando o diretor de serviço para puxar as imagens ACR. [Sem margem](https://github.com/virtual-kubelet/virtual-kubelet/blob/master/providers/azure/README.md#Private-registry) de sobra é usar os segredos de [Kubernetes](https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/#create-a-secret-by-providing-credentials-on-the-command-line)
* [Limitações de rede virtuais,](../container-instances/container-instances-vnet.md) incluindo o peering VNet, as políticas de rede Kubernetes e o tráfego de saída para a internet com grupos de segurança de rede.
* Recipientes init
* [Pseudónimos hospedeiros](https://kubernetes.io/docs/concepts/services-networking/add-entries-to-pod-etc-hosts-with-host-aliases/)
* [Argumentos](../container-instances/container-instances-exec.md#restrictions) para executivo no ACI
* [DaemonSets](concepts-clusters-workloads.md#statefulsets-and-daemonsets) não vai implantar cápsulas para o nó virtual
* Os [nós do Windows Server (atualmente em pré-visualização no AKS)](windows-container-cli.md) não são suportados ao lado de nós virtuais. Pode utilizar nós virtuais para agendar os recipientes do Windows Server sem a necessidade de nós do Windows Server num cluster AKS.

## <a name="launch-azure-cloud-shell"></a>Iniciar o Azure Cloud Shell

O Azure Cloud Shell é um shell interativo gratuito que pode utilizar para executar os passos neste artigo. Tem as ferramentas comuns do Azure pré-instaladas e configuradas para utilização com a sua conta.

Para abrir a Cloud Shell, selecione **Experimente** a partir do canto superior direito de um bloco de código. Também pode iniciar o Cloud Shell num separador do browser separado ao aceder a [https://shell.azure.com/bash](https://shell.azure.com/bash). Selecione **Copiar** para copiar os blocos de código, cole-o no Cloud Shell e prima Enter para executá-lo.

Se preferir instalar e utilizar o CLI localmente, este artigo requer a versão Azure CLI 2.0.49 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Criar um grupo de recursos:

Um grupo de recursos do Azure é um grupo lógico, no qual os recursos do Azure são implementados e geridos. Crie um grupo de recursos com o comando [az group create][az-group-create]. O exemplo seguinte cria um grupo de recursos com o nome *myResourceGroup* na localização *westus*.

```azurecli-interactive
az group create --name myResourceGroup --location westus
```

## <a name="create-a-virtual-network"></a>Criar uma rede virtual

Criar uma rede virtual utilizando a [rede az vnet criar][az-network-vnet-create] comando. O exemplo seguinte cria um nome de rede virtual *myVnet* com um prefixo de endereço de *10.0.0.0.8*, e uma sub-rede chamada *myAKSSubnet*. A prefixação de endereço desta sub-rede não se aplica a *10.240.0.0/16:*

```azurecli-interactive
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefixes 10.0.0.0/8 \
    --subnet-name myAKSSubnet \
    --subnet-prefix 10.240.0.0/16
```

Agora crie uma sub-rede adicional para nódos os nódosos virtuais usando a [subnet az rede vnet criar][az-network-vnet-subnet-create] comando. O exemplo seguinte cria uma subnet denominada *myVirtualNodeSubnet* com o prefixo de endereço de *10.241.0.0.0.0/16*.

```azurecli-interactive
az network vnet subnet create \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name myVirtualNodeSubnet \
    --address-prefixes 10.241.0.0/16
```

## <a name="create-a-service-principal"></a>Criar um principal de serviço

Para permitir que um cluster do AKS interaja com outros recursos do Azure, é utilizado um principal de serviço do Azure Active Directory. Este principal de serviço pode ser criado automaticamente com a CLI do Azure ou o portal, ou pode criar previamente um e atribuir permissões adicionais.

Crie um principal de serviço com o comando [az ad sp create-for-rbac][az-ad-sp-create-for-rbac]. O parâmetro `--skip-assignment` limita a atribuição de permissões adicionais.

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

## <a name="assign-permissions-to-the-virtual-network"></a>Atribuir permissões à rede virtual

Para permitir que o seu cluster utilize e gere a rede virtual, deve conceder ao serviço AKS principal os direitos corretos de utilização dos recursos de rede.

Em primeiro lugar, obtenha o ID de recursos de rede virtual usando [a z network vnet show:][az-network-vnet-show]

```azurecli-interactive
az network vnet show --resource-group myResourceGroup --name myVnet --query id -o tsv
```

Para conceder o acesso correto para o cluster AKS usar a rede virtual, crie uma atribuição de funções usando a atribuição de [funções az criar][az-role-assignment-create] comando. Substitua `<appId`> e `<vnetId>` pelos valores recolhidos nos dois passos anteriores.

```azurecli-interactive
az role assignment create --assignee <appId> --scope <vnetId> --role Contributor
```

## <a name="create-an-aks-cluster"></a>Criar um cluster do AKS (Create an AKS cluster)

Implanta um cluster AKS na subnet AKS criada num passo anterior. Obtenha a identificação desta subnet utilizando a [rede az vnet show:][az-network-vnet-subnet-show]

```azurecli-interactive
az network vnet subnet show --resource-group myResourceGroup --vnet-name myVnet --name myAKSSubnet --query id -o tsv
```

Use as [aks az criar][az-aks-create] comando para criar um cluster AKS. O exemplo seguinte cria um cluster com o nome *myAKSCluster* com um nó. Substitua `<subnetId>` com o ID obtido no passo anterior e, em seguida, `<appId>` e `<password>` com o 

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

## <a name="enable-virtual-nodes-addon"></a>Ativar o addon de nós virtuais

Para ativar os nós virtuais, utilize agora o comando [az aks enable-addons.][az-aks-enable-addons] O exemplo seguinte utiliza a subnet denominada *myVirtualNodeSubnet* criada num passo anterior:

```azurecli-interactive
az aks enable-addons \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --addons virtual-node \
    --subnet-name myVirtualNodeSubnet
```

## <a name="connect-to-the-cluster"></a>Ligar ao cluster

Para configurar `kubectl` para se ligar ao seu cluster Kubernetes, use o comando [az aks get-credentials.][az-aks-get-credentials] Este passo transfere credenciais e configura a CLI do Kubernetes para as utilizar.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Para verificar a ligação ao cluster, utilize o comando [kubectl get][kubectl-get] para devolver uma lista de nós do cluster.

```console
kubectl get nodes
```

A saída de exemplo seguinte mostra o nó VM único criado e, em seguida, o nó virtual para Linux, *virtual-node-aci-linux:*

```
$ kubectl get nodes

NAME                          STATUS    ROLES     AGE       VERSION
virtual-node-aci-linux        Ready     agent     28m       v1.11.2
aks-agentpool-14693408-0      Ready     agent     32m       v1.11.2
```

## <a name="deploy-a-sample-app"></a>Implementar uma aplicação de amostra

Crie um ficheiro chamado `virtual-node.yaml` e copie no seguinte YAML. Para agendar o recipiente no nó, é definido um [nodeSelector][node-selector] e [toleração.][toleration]

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

Executar a aplicação com o [kubectl aplicar][kubectl-apply] comando.

```console
kubectl apply -f virtual-node.yaml
```

Use o [kubectl obter comando de cápsulas][kubectl-get] com o argumento `-o wide` para obter uma lista de cápsulas e o nó programado. Note que a cápsula `aci-helloworld` foi programada no nó `virtual-node-aci-linux`.

```
$ kubectl get pods -o wide

NAME                            READY     STATUS    RESTARTS   AGE       IP           NODE
aci-helloworld-9b55975f-bnmfl   1/1       Running   0          4m        10.241.0.4   virtual-node-aci-linux
```

A cápsula é atribuída a um endereço IP interno da subnet de rede virtual Azure delegada para utilização com nódosos virtuais.

> [!NOTE]
> Se utilizar imagens armazenadas no Registo de Contentores Azure, [configure e utilize um segredo kubernetes][acr-aks-secrets]. Uma limitação atual dos nódosos virtuais é que não pode utilizar a autenticação principal integrada do serviço Azure AD. Se não utilizar um segredo, as cápsulas programadas em nós virtuais não começam e reportam o erro `HTTP response status code 400 error code "InaccessibleImage"`.

## <a name="test-the-virtual-node-pod"></a>Teste a vagem de nó virtual

Para testar a cápsula que corre no nó virtual, navegue para a aplicação de demonstração com um cliente web. Como a cápsula é atribuída a um endereço IP interno, você pode testar rapidamente esta conectividade a partir de outra cápsula no cluster AKS. Crie uma cápsula de ensaio e fixe-lhe uma sessão terminal:

```console
kubectl run --generator=run-pod/v1 -it --rm testvk --image=debian
```

Instale `curl` na cápsula utilizando `apt-get`:

```console
apt-get update && apt-get install -y curl
```

Aceda agora ao endereço da sua cápsula utilizando `curl`, como *http://10.241.0.4* . Forneça o seu próprio endereço IP interno mostrado no comando `kubectl get pods` anterior:

```console
curl -L http://10.241.0.4
```

A aplicação de demonstração é apresentada, como mostra a seguinte saída de exemplo condensado:

```
$ curl -L 10.241.0.4

<html>
<head>
  <title>Welcome to Azure Container Instances!</title>
</head>
[...]
```

Feche a sessão terminal na sua cápsula de teste com `exit`. Quando a sua sessão terminar, a cápsula é a eliminada.

## <a name="remove-virtual-nodes"></a>Remover os nódosos virtuais

Se já não pretender utilizar nós virtuais, pode desativá-los utilizando o comando [az aks de desativação.][az aks disable-addons] 

Primeiro, apague a cápsula Helloworld que corre no nó virtual:

```azurecli-interactive
kubectl delete -f virtual-node.yaml
```

O comando de exemplo seguinte desativa os nódosos virtuais Linux:

```azurecli-interactive
az aks disable-addons --resource-group myResourceGroup --name myAKSCluster --addons virtual-node
```

Agora, remova os recursos da rede virtual e o grupo de recursos:

```azurecli-interactive
# Change the name of your resource group, cluster and network resources as needed
RES_GROUP=myResourceGroup
AKS_CLUSTER=myAKScluster
AKS_VNET=myVnet
AKS_SUBNET=myVirtualNodeSubnet

# Get AKS node resource group
NODE_RES_GROUP=$(az aks show --resource-group $RES_GROUP --name $AKS_CLUSTER --query nodeResourceGroup --output tsv)

# Get network profile ID
NETWORK_PROFILE_ID=$(az network profile list --resource-group $NODE_RES_GROUP --query [0].id --output tsv)

# Delete the network profile
az network profile delete --id $NETWORK_PROFILE_ID -y

# Delete the subnet delegation to Azure Container Instances
az network vnet subnet update --resource-group $RES_GROUP --vnet-name $AKS_VNET --name $AKS_SUBNET --remove delegations 0
```

## <a name="next-steps"></a>Passos seguintes

Neste artigo, uma cápsula foi programada no nó virtual e atribuiu um endereço IP privado e interno. Em vez disso, poderia criar uma implantação de serviço e encaminhar o tráfego para a sua cápsula através de um equilibrador de carga ou controlador de entrada. Para mais informações, consulte Criar um controlador básico de [ingresso no AKS][aks-basic-ingress].

Os nódosos virtuais são muitas vezes um componente de uma solução de escala no AKS. Para obter mais informações sobre soluções de escala, consulte os seguintes artigos:

- [Utilize o autoscaler de cápsula horizontal Kubernetes][aks-hpa]
- [Use o autoscaler do cluster Kubernetes][aks-cluster-autoscaler]
- [Confira a amostra de escala automática para nódosos virtuais][virtual-node-autoscale]
- [Leia mais sobre a biblioteca virtual Kubelet open source][virtual-kubelet-repo]

<!-- LINKS - external -->
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[node-selector]:https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[toleration]: https://kubernetes.io/docs/concepts/configuration/taint-and-toleration/
[aks-github]: https://github.com/azure/aks/issues
[virtual-node-autoscale]: https://github.com/Azure-Samples/virtual-node-autoscale
[virtual-kubelet-repo]: https://github.com/virtual-kubelet/virtual-kubelet
[acr-aks-secrets]: https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/

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
