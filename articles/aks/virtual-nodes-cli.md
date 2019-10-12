---
title: Criar nós virtuais usando o CLI do Azure nos AKS (serviços Kubernetess do Azure)
description: Saiba como usar o CLI do Azure para criar um cluster AKS (serviços Kubernetess do Azure) que usa nós virtuais para executar pods.
services: container-service
author: mlearned
ms.topic: conceptual
ms.service: container-service
ms.date: 05/06/2019
ms.author: mlearned
ms.openlocfilehash: d3651c63b206c37b1f41ecab7f69e24fc94ddffd
ms.sourcegitcommit: b4665f444dcafccd74415fb6cc3d3b65746a1a31
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2019
ms.locfileid: "72263863"
---
# <a name="create-and-configure-an-azure-kubernetes-services-aks-cluster-to-use-virtual-nodes-using-the-azure-cli"></a>Criar e configurar um cluster AKS (serviços Kubernetess do Azure) para usar nós virtuais usando o CLI do Azure

Para dimensionar rapidamente cargas de trabalho de aplicativo em um cluster AKS (serviço kubernetes do Azure), você pode usar nós virtuais. Com nós virtuais, você tem provisionamento rápido de pods e só paga por segundo durante seu tempo de execução. Você não precisa aguardar que o dimensionador de cluster kubernetes implante os nós de computação da VM para executar os pods adicionais. Os nós virtuais só têm suporte com os pods e nós do Linux.

Este artigo mostra como criar e configurar os recursos de rede virtual e o cluster AKS e, em seguida, habilitar nós virtuais.

## <a name="before-you-begin"></a>Antes de começar

Os nós virtuais habilitam a comunicação de rede entre os pods que são executados no ACI e no cluster AKS. Para fornecer essa comunicação, uma sub-rede de rede virtual é criada e permissões delegadas são atribuídas. Os nós virtuais funcionam apenas com clusters AKS criados usando a rede *avançada* . Por padrão, os clusters AKS são criados com a rede *básica* . Este artigo mostra como criar uma rede virtual e sub-redes e, em seguida, implantar um cluster AKS que usa a rede avançada.

Se você não tiver usado o ACI anteriormente, registre o provedor de serviços com sua assinatura. Você pode verificar o status do registro do provedor de ACI usando o comando [AZ Provider List][az-provider-list] , conforme mostrado no exemplo a seguir:

```azurecli-interactive
az provider list --query "[?contains(namespace,'Microsoft.ContainerInstance')]" -o table
```

O provedor *Microsoft. ContainerInstance* deve relatar como *registrado*, conforme mostrado na seguinte saída de exemplo:

```
Namespace                    RegistrationState
---------------------------  -------------------
Microsoft.ContainerInstance  Registered
```

Se o provedor aparecer como não *registrado*, registre o provedor usando o [registro do provedor AZ][az-provider-register] , conforme mostrado no exemplo a seguir:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerInstance
```

## <a name="regional-availability"></a>Disponibilidade regional

As seguintes regiões têm suporte para implantações de nó virtual:

* Leste da Austrália (australiaeast)
* EUA Central (centralus)
* Leste dos EUA (lesteus)
* Leste dos EUA 2 (eastus2)
* Leste do Japão (japaneast)
* Europa Setentrional (northeurope)
* Sudeste Asiático (southeastasia)
* EUA Central ocidental (westcentralus)
* Europa Ocidental (westeurope)
* Oeste dos EUA (westus)
* Oeste dos EUA 2 (westus2)

## <a name="known-limitations"></a>Limitações conhecidas
A funcionalidade de nós virtuais é altamente dependente do conjunto de recursos de ACI. Os cenários a seguir ainda não têm suporte com nós virtuais

* Usando a entidade de serviço para extrair imagens de ACR. A [solução alternativa](https://github.com/virtual-kubelet/virtual-kubelet/blob/master/providers/azure/README.md#Private-registry) é usar [segredos do kubernetes](https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/#create-a-secret-by-providing-credentials-on-the-command-line)
* [Limitações de rede virtual](../container-instances/container-instances-vnet.md) , incluindo emparelhamento VNet, políticas de rede kubernetes e tráfego de saída para a Internet com grupos de segurança de rede.
* Inicializar contêineres
* [Aliases de host](https://kubernetes.io/docs/concepts/services-networking/add-entries-to-pod-etc-hosts-with-host-aliases/)
* [Argumentos](../container-instances/container-instances-exec.md#restrictions) para exec em ACI
* [DaemonSets](concepts-clusters-workloads.md#statefulsets-and-daemonsets) não implantará pods no nó virtual
* Os [nós do Windows Server (atualmente em visualização no AKs)](windows-container-cli.md) não têm suporte juntamente com nós virtuais. Você pode usar nós virtuais para agendar contêineres do Windows Server sem a necessidade de nós do Windows Server em um cluster AKS.

## <a name="launch-azure-cloud-shell"></a>Iniciar o Azure Cloud Shell

O Azure Cloud Shell é um shell interativo gratuito que pode utilizar para executar os passos neste artigo. Tem as ferramentas comuns do Azure pré-instaladas e configuradas para utilização com a sua conta.

Para abrir o Cloud Shell, selecione **Experimente** no canto superior direito de um bloco de código. Também pode iniciar o Cloud Shell num separador do browser separado ao aceder a [https://shell.azure.com/bash](https://shell.azure.com/bash). Selecione **Copiar** para copiar os blocos de código, cole-o no Cloud Shell e prima Enter para executá-lo.

Se você preferir instalar e usar a CLI localmente, este artigo exigirá CLI do Azure versão 2.0.49 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um grupo de recursos do Azure é um grupo lógico, no qual os recursos do Azure são implementados e geridos. Crie um grupo de recursos com o comando [az group create][az-group-create]. O exemplo seguinte cria um grupo de recursos com o nome *myResourceGroup* na localização *westus*.

```azurecli-interactive
az group create --name myResourceGroup --location westus
```

## <a name="create-a-virtual-network"></a>Criar rede virtual

Crie uma rede virtual usando o comando [AZ Network vnet Create][az-network-vnet-create] . O exemplo a seguir cria um nome de rede virtual *myVnet* com um prefixo de endereço *10.0.0.0/8*e uma sub-rede chamada *myAKSSubnet*. O prefixo de endereço dessa sub-rede assume o padrão de *10.240.0.0/16*:

```azurecli-interactive
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefixes 10.0.0.0/8 \
    --subnet-name myAKSSubnet \
    --subnet-prefix 10.240.0.0/16
```

Agora, crie uma sub-rede adicional para nós virtuais usando o comando [AZ Network vnet subnet Create][az-network-vnet-subnet-create] . O exemplo a seguir cria uma sub-rede chamada *myVirtualNodeSubnet* com o prefixo de endereço de *10.241.0.0/16*.

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

Para permitir que o cluster use e gerencie a rede virtual, você deve conceder à entidade de serviço AKS os direitos corretos para usar os recursos de rede.

Primeiro, obtenha a ID de recurso de rede virtual usando [AZ Network vnet show][az-network-vnet-show]:

```azurecli-interactive
az network vnet show --resource-group myResourceGroup --name myVnet --query id -o tsv
```

Para conceder o acesso correto para o cluster AKS usar a rede virtual, crie uma atribuição de função usando o comando [AZ role Assignment Create][az-role-assignment-create] . Substitua `<appId`> e `<vnetId>` pelos valores recolhidos nos dois passos anteriores.

```azurecli-interactive
az role assignment create --assignee <appId> --scope <vnetId> --role Contributor
```

## <a name="create-an-aks-cluster"></a>Criar um cluster do AKS (Create an AKS cluster)

Você implanta um cluster AKS na sub-rede AKS criada em uma etapa anterior. Obtenha a ID desta sub-rede usando [AZ Network vnet subnet show][az-network-vnet-subnet-show]:

```azurecli-interactive
az network vnet subnet show --resource-group myResourceGroup --vnet-name myVnet --name myAKSSubnet --query id -o tsv
```

Use o comando [AZ AKs Create][az-aks-create] para criar um cluster AKs. O exemplo seguinte cria um cluster com o nome *myAKSCluster* com um nó. Substitua `<subnetId>` pela ID obtida na etapa anterior e, em seguida, `<appId>` e `<password>` com o 

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

## <a name="enable-virtual-nodes-addon"></a>Habilitar complemento de nós virtuais

Para habilitar nós virtuais, agora use o comando [AZ AKs Enable-addons][az-aks-enable-addons] . O exemplo a seguir usa a sub-rede chamada *myVirtualNodeSubnet* criada em uma etapa anterior:

```azurecli-interactive
az aks enable-addons \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --addons virtual-node \
    --subnet-name myVirtualNodeSubnet
```

## <a name="connect-to-the-cluster"></a>Ligar ao cluster

Para configurar `kubectl` para se conectar ao cluster kubernetes, use o comando [AZ AKs Get-Credentials][az-aks-get-credentials] . Este passo transfere credenciais e configura a CLI do Kubernetes para as utilizar.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Para verificar a ligação ao cluster, utilize o comando [kubectl get][kubectl-get] para devolver uma lista de nós do cluster.

```console
kubectl get nodes
```

A saída de exemplo a seguir mostra o nó de VM único criado e, em seguida, o nó virtual para Linux, *virtual-node-ACI-Linux*:

```
$ kubectl get nodes

NAME                          STATUS    ROLES     AGE       VERSION
virtual-node-aci-linux        Ready     agent     28m       v1.11.2
aks-agentpool-14693408-0      Ready     agent     32m       v1.11.2
```

## <a name="deploy-a-sample-app"></a>Implantar um aplicativo de exemplo

Crie um arquivo chamado `virtual-node.yaml` e copie o YAML a seguir. Para agendar o contêiner no nó, um [nodeSelector][node-selector] e [toleration][toleration] são definidos.

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

Execute o aplicativo com o comando [kubectl Apply][kubectl-apply] .

```console
kubectl apply -f virtual-node.yaml
```

Use o comando [kubectl Get pods][kubectl-get] com o argumento `-o wide` para gerar uma lista de pods e o nó agendado. Observe que o Pod `aci-helloworld` foi agendado no nó `virtual-node-aci-linux`.

```
$ kubectl get pods -o wide

NAME                            READY     STATUS    RESTARTS   AGE       IP           NODE
aci-helloworld-9b55975f-bnmfl   1/1       Running   0          4m        10.241.0.4   virtual-node-aci-linux
```

O pod é atribuído a um endereço IP interno da sub-rede de rede virtual do Azure delegada para uso com nós virtuais.

> [!NOTE]
> Se você usar imagens armazenadas no registro de contêiner do Azure, [Configure e use um segredo kubernetes][acr-aks-secrets]. Uma limitação atual de nós virtuais é que você não pode usar a autenticação de entidade de serviço do Azure AD integrada. Se você não usar um segredo, os pods agendados em nós virtuais falharão ao iniciar e reportar o erro `HTTP response status code 400 error code "InaccessibleImage"`.

## <a name="test-the-virtual-node-pod"></a>Testar o Pod do nó virtual

Para testar o pod em execução no nó virtual, navegue até o aplicativo de demonstração com um cliente Web. À medida que o Pod recebe um endereço IP interno, você pode testar rapidamente essa conectividade de outro pod no cluster AKS. Crie um pod de teste e anexe uma sessão de terminal a ele:

```console
kubectl run -it --rm virtual-node-test --image=debian
```

Instale `curl` no pod usando `apt-get`:

```console
apt-get update && apt-get install -y curl
```

Agora acesse o endereço do seu Pod usando `curl`, como *http://10.241.0.4* . Forneça seu próprio endereço IP interno mostrado no comando anterior `kubectl get pods`:

```console
curl -L http://10.241.0.4
```

O aplicativo de demonstração é exibido, conforme mostrado na seguinte saída de exemplo condensada:

```
$ curl -L 10.241.0.4

<html>
<head>
  <title>Welcome to Azure Container Instances!</title>
</head>
[...]
```

Feche a sessão de terminal para o pod de teste com `exit`. Quando a sessão for encerrada, o Pod será excluído.

## <a name="remove-virtual-nodes"></a>Remover nós virtuais

Se você não deseja mais usar nós virtuais, você pode desabilitá-los usando o comando [AZ AKs Disable-Complementos][az aks disable-addons] . 

Primeiro, exclua o seu pod de HelloWorld em execução no nó virtual:

```azurecli-interactive
kubectl delete -f virtual-node.yaml
```

O comando de exemplo a seguir desabilita os nós virtuais do Linux:

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

# Get the service association link (SAL) ID
SAL_ID=$(az network vnet subnet show --resource-group $RES_GROUP --vnet-name $AKS_VNET --name $AKS_SUBNET --query id --output tsv)/providers/Microsoft.ContainerInstance/serviceAssociationLinks/default

# Delete the default SAL ID for the subnet
az resource delete --ids $SAL_ID --api-version 2018-07-01

# Delete the subnet delegation to Azure Container Instances
az network vnet subnet update --resource-group $RES_GROUP --vnet-name $AKS_VNET --name $AKS_SUBNET --remove delegations 0
```

## <a name="next-steps"></a>Passos seguintes

Neste artigo, um pod foi agendado no nó virtual e atribuído um endereço IP interno e privado. Em vez disso, você pode criar uma implantação de serviço e rotear o tráfego para o Pod por meio de um balanceador de carga ou controlador de entrada. Para obter mais informações, consulte [criar um controlador de entrada básico em AKs][aks-basic-ingress].

Nós virtuais são geralmente um componente de uma solução de dimensionamento em AKS. Para obter mais informações sobre soluções de dimensionamento, consulte os seguintes artigos:

- [Usar o dimensionador de kubernetes horizontal Pod][aks-hpa]
- [Usar o dimensionador de cluster kubernetes][aks-cluster-autoscaler]
- [Confira a amostra de dimensionamento automático para nós virtuais][virtual-node-autoscale]
- [Leia mais sobre a biblioteca de código-fonte aberto Kubelet virtual][virtual-kubelet-repo]

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
