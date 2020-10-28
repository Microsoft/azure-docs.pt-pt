---
title: Criar nódes virtuais usando O Azure CLI
titleSuffix: Azure Kubernetes Service
description: Aprenda a usar o CLI Azure para criar um cluster Azure Kubernetes Services (AKS) que usa nós virtuais para executar cápsulas.
services: container-service
ms.topic: conceptual
ms.date: 05/06/2019
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 96c47ed59fd904f1523347d9f0ef7bc00edb866f
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92745664"
---
# <a name="create-and-configure-an-azure-kubernetes-services-aks-cluster-to-use-virtual-nodes-using-the-azure-cli"></a>Criar e configurar um cluster Azure Kubernetes Services (AKS) para usar nós virtuais usando o Azure CLI

Para escalar rapidamente as cargas de trabalho da aplicação num cluster Azure Kubernetes Service (AKS), pode utilizar nós virtuais. Com nós virtuais, você tem um fornecimento rápido de cápsulas, e só paga por segundo pelo seu tempo de execução. Não é preciso esperar que o cluster de Kubernetes possa implantar nós de computação VM para executar as cápsulas adicionais. Os nós virtuais só são suportados com cápsulas e nós Linux.

Este artigo mostra-lhe como criar e configurar os recursos de rede virtuais e o cluster AKS, em seguida, ativar nós virtuais.

## <a name="before-you-begin"></a>Antes de começar

Os nós virtuais permitem a comunicação de rede entre as cápsulas que funcionam em Instâncias de Contentores Azure (ACI) e o cluster AKS. Para fornecer esta comunicação, é criada uma sub-rede de rede virtual e são atribuídas permissões delegadas. Os nós virtuais só funcionam com clusters AKS criados com redes *avançadas.* Por padrão, os clusters AKS são criados com rede *básica.* Este artigo mostra-lhe como criar uma rede virtual e sub-redes, em seguida, implementar um cluster AKS que utiliza networking avançado.

Se não tiver utilizado o ACI anteriormente, registe o prestador de serviços com a sua assinatura. Pode verificar o estado do registo do fornecedor ACI utilizando o comando da [lista de fornecedores az,][az-provider-list] como mostra o seguinte exemplo:

```azurecli-interactive
az provider list --query "[?contains(namespace,'Microsoft.ContainerInstance')]" -o table
```

O fornecedor *Microsoft.ContainerInstance* deve reportar como *Registado,* como mostrado na seguinte saída de exemplo:

```output
Namespace                    RegistrationState    RegistrationPolicy
---------------------------  -------------------  --------------------
Microsoft.ContainerInstance  Registered           RegistrationRequired
```

Se o fornecedor mostrar como *Não Registado,* registe o fornecedor utilizando o registo do [fornecedor az,][az-provider-register] como indicado no seguinte exemplo:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerInstance
```

## <a name="regional-availability"></a>Disponibilidade regional

As seguintes regiões são apoiadas para implantações virtuais de nó:

* Austrália Oriental (austrália)
* Central DOS EUA (central)
* Leste dos EUA (leste)
* Leste dos EUA 2 (leste)
* Japão Oriental (japãoeast)
* Norte da Europa (northeurope)
* Sudeste Asiático (sudeste asiático)
* West Central US (westcentralus)
* Europa Ocidental (westeurope)
* Eua Ocidentais (westus)
* West US 2 (westus2)

## <a name="known-limitations"></a>Limitações conhecidas
A funcionalidade Nódes Virtuais está fortemente dependente do conjunto de funcionalidades do ACI. Para além das [quotas e limites para as instâncias do contentor Azure,](../container-instances/container-instances-quotas.md)os seguintes cenários ainda não são suportados com nóns virtuais:

* Utilizar o principal de serviço para retirar imagens ACR. [A solução](https://github.com/virtual-kubelet/azure-aci/blob/master/README.md#private-registry) é usar [segredos de Kubernetes](https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/#create-a-secret-by-providing-credentials-on-the-command-line)
* [Limitações de rede virtuais,](../container-instances/container-instances-vnet.md) incluindo o peering VNet, as políticas de rede Kubernetes e o tráfego de saída para a internet com grupos de segurança de rede.
* Recipientes init
* [Pseudónimos anfitrião](https://kubernetes.io/docs/concepts/services-networking/add-entries-to-pod-etc-hosts-with-host-aliases/)
* [Argumentos](../container-instances/container-instances-exec.md#restrictions) para executivos em ACI
* [Os DaemonSets](concepts-clusters-workloads.md#statefulsets-and-daemonsets) não vão implantar cápsulas para o nó virtual
* Os nós virtuais suportam o agendamento de cápsulas Linux. Pode instalar manualmente o fornecedor [Virtual Kubelet ACI](https://github.com/virtual-kubelet/azure-aci) de fonte aberta para agendar os contentores do Windows Server para ACI.
* Nós virtuais requerem clusters AKS com rede CNI Azure

## <a name="launch-azure-cloud-shell"></a>Iniciar o Azure Cloud Shell

O Azure Cloud Shell é um shell interativo gratuito que pode utilizar para executar os passos neste artigo. Tem as ferramentas comuns do Azure pré-instaladas e configuradas para utilização com a sua conta.

Para abrir a Cloud Shell, selecione **Experimente-a** a partir do canto superior direito de um bloco de código. Também pode lançar cloud Shell num separador de navegador indo para [https://shell.azure.com/bash](https://shell.azure.com/bash) . Selecione **Copiar** para copiar os blocos de código, cole-o no Cloud Shell e prima Enter para executá-lo.

Se preferir instalar e utilizar o CLI localmente, este artigo requer a versão 2.0.49 ou posterior do Azure CLI. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um grupo de recursos do Azure é um grupo lógico, no qual os recursos do Azure são implementados e geridos. Crie um grupo de recursos com o comando [az group create][az-group-create]. O exemplo seguinte cria um grupo de recursos com o nome *myResourceGroup* na localização *westus* .

```azurecli-interactive
az group create --name myResourceGroup --location westus
```

## <a name="create-a-virtual-network"></a>Criar uma rede virtual

Criar uma rede virtual utilizando o [vnet de rede az criar][az-network-vnet-create] comando. O exemplo a seguir cria um *myVnet* de nome de rede virtual com um prefixo de endereço de *10.0.0.0/8* , e uma sub-rede chamada *myAKSSubnet* . O prefixo de endereço desta sub-rede é de *10.240.0.0/16* :

```azurecli-interactive
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefixes 10.0.0.0/8 \
    --subnet-name myAKSSubnet \
    --subnet-prefix 10.240.0.0/16
```

Agora crie uma sub-rede adicional para nós virtuais utilizando o comando de criação de rede vnet de [rede az.][az-network-vnet-subnet-create] O exemplo a seguir cria uma sub-rede chamada *myVirtualNodeSubnet* com o prefixo de endereço de *10.241.0.0/16* .

```azurecli-interactive
az network vnet subnet create \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name myVirtualNodeSubnet \
    --address-prefixes 10.241.0.0/16
```

## <a name="create-a-service-principal-or-use-a-managed-identity"></a>Criar um principal de serviço ou usar uma identidade gerida

Para permitir que um cluster do AKS interaja com outros recursos do Azure, é utilizado um principal de serviço do Azure Active Directory. Este principal de serviço pode ser criado automaticamente com a CLI do Azure ou o portal, ou pode criar previamente um e atribuir permissões adicionais. Em alternativa, pode utilizar uma identidade gerida para permissões em vez de um principal de serviço. Para obter mais informações, consulte [utilização de identidades geridas.](use-managed-identity.md)

Crie um principal de serviço com o comando [az ad sp create-for-rbac][az-ad-sp-create-for-rbac]. O parâmetro `--skip-assignment` limita a atribuição de permissões adicionais.

```azurecli-interactive
az ad sp create-for-rbac --skip-assignment
```

O resultado é semelhante ao seguinte exemplo:

```output
{
  "appId": "bef76eb3-d743-4a97-9534-03e9388811fc",
  "displayName": "azure-cli-2018-11-21-18-42-00",
  "name": "http://azure-cli-2018-11-21-18-42-00",
  "password": "1d257915-8714-4ce7-a7fb-0e5a5411df7f",
  "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db48"
}
```

Anote o *appId* e a *palavra-passe* . São utilizados os seguintes valores nos passos seguintes.

## <a name="assign-permissions-to-the-virtual-network"></a>Atribuir permissões à rede virtual

Para permitir que o seu cluster utilize e gere a rede virtual, deve conceder ao chefe de serviço AKS os direitos corretos de utilização dos recursos de rede.

Em primeiro lugar, obtenha o ID de recursos de rede virtual usando [o programa vnet da rede AZ:][az-network-vnet-show]

```azurecli-interactive
az network vnet show --resource-group myResourceGroup --name myVnet --query id -o tsv
```

Para conceder o acesso correto para o cluster AKS usar a rede virtual, crie uma tarefa de função usando a [atribuição de função az criar][az-role-assignment-create] comando. Substitua `<appId`> e `<vnetId>` pelos valores recolhidos nos dois passos anteriores.

```azurecli-interactive
az role assignment create --assignee <appId> --scope <vnetId> --role Contributor
```

## <a name="create-an-aks-cluster"></a>Criar um cluster do AKS (Create an AKS cluster)

Coloca-se um cluster AKS na sub-rede AKS criada num passo anterior. Obtenha a ID desta sub-rede utilizando [o programa de sub-redes da rede Az:][az-network-vnet-subnet-show]

```azurecli-interactive
az network vnet subnet show --resource-group myResourceGroup --vnet-name myVnet --name myAKSSubnet --query id -o tsv
```

Utilize o comando [az aks create][az-aks-create] para criar um cluster AKS. O exemplo seguinte cria um cluster com o nome *myAKSCluster* com um nó. `<subnetId>`Substitua-o pelo ID obtido no passo anterior e, em seguida, `<appId>` e pelos `<password>` valores recolhidos na secção anterior.

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

## <a name="enable-virtual-nodes-addon"></a>Ativar o addon de nóis virtuais

Para ativar os nós virtuais, utilize agora o comando [az aks enable-addons.][az-aks-enable-addons] O exemplo a seguir utiliza a sub-rede chamada *myVirtualNodeSubnet* criada num passo anterior:

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

A saída de exemplo a seguir mostra o único nó VM criado e, em seguida, o nó virtual para Linux, *virtual-nó-aci-linux* :

```output
NAME                          STATUS    ROLES     AGE       VERSION
virtual-node-aci-linux        Ready     agent     28m       v1.11.2
aks-agentpool-14693408-0      Ready     agent     32m       v1.11.2
```

## <a name="deploy-a-sample-app"></a>Implementar uma aplicação de amostra

Crie um ficheiro nomeado `virtual-node.yaml` e copie no seguinte YAML. Para agendar o recipiente no nó, é definido um [nóselector][node-selector] e [tolerância.][toleration]

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

Executar a aplicação com o [comando de aplicação de kubectl.][kubectl-apply]

```console
kubectl apply -f virtual-node.yaml
```

Utilize o [comando de pods de kubectl][kubectl-get] com o `-o wide` argumento para des outputar uma lista de cápsulas e o nó programado. Note que a `aci-helloworld` cápsula foi programada no `virtual-node-aci-linux` nó.

```console
kubectl get pods -o wide
```

```output
NAME                            READY     STATUS    RESTARTS   AGE       IP           NODE
aci-helloworld-9b55975f-bnmfl   1/1       Running   0          4m        10.241.0.4   virtual-node-aci-linux
```

O pod é atribuído um endereço IP interno da sub-rede virtual Azure delegada para utilização com nós virtuais.

> [!NOTE]
> Se utilizar imagens armazenadas no Registo do Contentor Azure, [configuure e utilize um segredo de Kubernetes][acr-aks-secrets]. Uma limitação atual dos nós virtuais é que você não pode usar a autenticação principal integrada do serviço Azure AD. Se não utilizar um segredo, as cápsulas programadas nos nós virtuais não começam e reportam o erro `HTTP response status code 400 error code "InaccessibleImage"` .

## <a name="test-the-virtual-node-pod"></a>Teste a vagem de nó virtual

Para testar o pod em execução no nó virtual, navegue para a aplicação de demonstração com um cliente web. À medida que a cápsula é atribuída a um endereço IP interno, pode testar rapidamente esta conectividade a partir de outra cápsula no cluster AKS. Crie uma cápsula de teste e anexe-lhe uma sessão terminal:

```console
kubectl run -it --rm testvk --image=debian
```

Instale `curl` na cápsula `apt-get` utilizando:

```console
apt-get update && apt-get install -y curl
```

Agora aceda ao endereço do seu pod `curl` utilizando, tal como *http://10.241.0.4* . Forneça o seu próprio endereço IP interno mostrado no `kubectl get pods` comando anterior:

```console
curl -L http://10.241.0.4
```

A aplicação de demonstração é apresentada, como mostra a seguinte saída de exemplo condensado:

```output
<html>
<head>
  <title>Welcome to Azure Container Instances!</title>
</head>
[...]
```

Feche a sessão de terminais à sua cápsula de teste com `exit` . Quando a sessão terminar, a cápsula é apagada.

## <a name="remove-virtual-nodes"></a>Remover os nóns virtuais

Se já não pretender utilizar nós virtuais, pode desativá-los utilizando o comando [az aks desativar os addons.][az aks disable-addons] 

Se necessário, vá [https://shell.azure.com](https://shell.azure.com) abrir a Azure Cloud Shell no seu navegador.

Primeiro, elimine o `aci-helloworld` pod que funciona no nó virtual:

```console
kubectl delete -f virtual-node.yaml
```

O seguinte exemplo de comando desativa os nóns virtuais Linux:

```azurecli-interactive
az aks disable-addons --resource-group myResourceGroup --name myAKSCluster --addons virtual-node
```

Agora, remova os recursos de rede virtual e o grupo de recursos:

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

Neste artigo, foi agendada uma cápsula no nó virtual e atribuída um endereço IP interno e privado. Em vez disso, pode criar uma implementação de serviço e encaminhar o tráfego para a sua cápsula através de um equilibrador de carga ou controlador de entrada. Para obter mais informações, consulte Criar um controlador básico de entrada [em AKS][aks-basic-ingress].

Os nós virtuais são frequentemente um componente de uma solução de escala em AKS. Para obter mais informações sobre soluções de escala, consulte os seguintes artigos:

- [Utilize o pod horizontal Kubernetes autoscaler][aks-hpa]
- [Utilize o cluster Kubernetes autoscaler][aks-cluster-autoscaler]
- [Confira a amostra de autoescala para nóns virtuais][virtual-node-autoscale]
- [Leia mais sobre a biblioteca virtual de fonte aberta de Kubelet][virtual-kubelet-repo]

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
[aks-cluster-autoscaler]: ./cluster-autoscaler.md
[aks-basic-ingress]: ingress-basic.md
[az-provider-list]: /cli/azure/provider#az-provider-list
[az-provider-register]: /cli/azure/provider#az-provider-register
