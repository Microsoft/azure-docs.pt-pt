---
title: Criar nós virtuais usando o portal nos serviços Kubernetess do Azure (AKS)
description: Saiba como usar o portal do Azure para criar um cluster AKS (serviços Kubernetess do Azure) que usa nós virtuais para executar pods.
services: container-service
author: mlearned
ms.topic: conceptual
ms.service: container-service
ms.date: 05/06/2019
ms.author: mlearned
ms.openlocfilehash: ab0aebf0b66ac01e19699795b14063df31cb9621
ms.sourcegitcommit: b4665f444dcafccd74415fb6cc3d3b65746a1a31
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2019
ms.locfileid: "72263755"
---
# <a name="create-and-configure-an-azure-kubernetes-services-aks-cluster-to-use-virtual-nodes-in-the-azure-portal"></a>Criar e configurar um cluster AKS (serviços Kubernetess do Azure) para usar nós virtuais no portal do Azure

Para implantar cargas de trabalho rapidamente em um cluster do AKS (serviço kubernetes do Azure), você pode usar nós virtuais. Com nós virtuais, você tem provisionamento rápido de pods e só paga por segundo durante seu tempo de execução. Em um cenário de dimensionamento, você não precisa esperar que o dimensionador automático do cluster kubernetes implante nós de computação da VM para executar os pods adicionais. Os nós virtuais só têm suporte com os pods e nós do Linux.

Este artigo mostra como criar e configurar os recursos de rede virtual e um cluster AKS com nós virtuais habilitados.

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

Se o provedor aparecer como não *registrado*, registre o provedor usando o [AZ Provider Register] [AZ-Provider-Register], conforme mostrado no exemplo a seguir:

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

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no portal do Azure em https://portal.azure.com.

## <a name="create-an-aks-cluster"></a>Criar um cluster do AKS (Create an AKS cluster)

No canto superior esquerdo do portal do Azure, selecione **Criar um recurso** > **Serviço Kubernetes**.

Na página **noções básicas** , configure as seguintes opções:

- *DETALHES DO PROJETO*: selecione uma subscrição do Azure e selecione ou crie um grupo de recursos do Azure, como *myResourceGroup*. Introduza um **nome para o cluster do Kubernetes**, como *myAKSCluster*.
- *DETALHES DO CLUSTER*: selecione uma região, a versão do Kubernetes e o prefixo do nome DNS do cluster do AKS.
- *Pool de nós primário*: selecione um tamanho de VM para os nós AKs. O tamanho da VM **não pode** ser alterado após a implementação de um cluster de AKS.
     - Selecione o número de nós a implementar no cluster. Para este artigo, defina **contagem de nós** como *1*. O número de nós **pode** ser ajustado após a implementação do cluster.

Clique em **Avançar: escala**.

Na página **escala** , selecione *habilitado* em **nós virtuais**.

![Criar um cluster AKS e habilitar os nós virtuais](media/virtual-nodes-portal/enable-virtual-nodes.png)

Por padrão, uma entidade de serviço Azure Active Directory é criada. Essa entidade de serviço é usada para comunicação de cluster e integração com outros serviços do Azure.

O cluster também é configurado para rede avançada. Os nós virtuais são configurados para usar sua própria sub-rede de rede virtual do Azure. Essa sub-rede tem permissões delegadas para conectar recursos do Azure entre o cluster AKS. Se você ainda não tiver uma sub-rede delegada, o portal do Azure criará e configurará a rede virtual e a sub-rede do Azure para uso com os nós virtuais.

Selecione **Rever + criar**. Depois que a validação for concluída, selecione **criar**.

O cluster do AKS demora alguns minutos a ser criado e a estar pronto para utilização.

## <a name="connect-to-the-cluster"></a>Ligar ao cluster

O Azure Cloud Shell é um shell interativo gratuito que pode utilizar para executar os passos neste artigo. Tem as ferramentas comuns do Azure pré-instaladas e configuradas para utilização com a sua conta. Para gerir um cluster de Kubernetes, utilize [kubectl][kubectl], o cliente de linha de comandos do Kubernetes. O cliente `kubectl` está pré-instalado no Azure Cloud Shell.

Para abrir o Cloud Shell, selecione **Experimente** no canto superior direito de um bloco de código. Também pode iniciar o Cloud Shell num separador do browser separado ao aceder a [https://shell.azure.com/bash](https://shell.azure.com/bash). Selecione **Copiar** para copiar os blocos de código, cole-o no Cloud Shell e prima Enter para executá-lo.

Use o comando [AZ AKs Get-Credentials][az-aks-get-credentials] para configurar o `kubectl` para se conectar ao cluster kubernetes. O exemplo seguinte obtém credenciais para o nome do cluster *myAKSCluster* no grupo de recursos denominado *myResourceGroup*:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Para verificar a ligação ao cluster, utilize o comando [kubectl get][kubectl-get] para devolver uma lista de nós do cluster.

```azurecli-interactive
kubectl get nodes
```

A saída de exemplo a seguir mostra o nó de VM único criado e, em seguida, o nó virtual para Linux, *virtual-node-ACI-Linux*:

```
$ kubectl get nodes

NAME                           STATUS    ROLES     AGE       VERSION
virtual-node-aci-linux         Ready     agent     28m       v1.11.2
aks-agentpool-14693408-0       Ready     agent     32m       v1.11.2
```

## <a name="deploy-a-sample-app"></a>Implantar um aplicativo de exemplo

No Azure Cloud Shell, crie um arquivo chamado `virtual-node.yaml` e copie o YAML a seguir. Para agendar o contêiner no nó, um [nodeSelector][node-selector] e [toleration][toleration] são definidos. Essas configurações permitem que o Pod seja agendado no nó virtual e confirme se o recurso foi habilitado com êxito.

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

```azurecli-interactive
kubectl apply -f virtual-node.yaml
```

Use o comando [kubectl Get pods][kubectl-get] com o argumento `-o wide` para gerar uma lista de pods e o nó agendado. Observe que o Pod `virtual-node-helloworld` foi agendado no nó `virtual-node-linux`.

```
$ kubectl get pods -o wide

NAME                                     READY     STATUS    RESTARTS   AGE       IP           NODE
virtual-node-helloworld-9b55975f-bnmfl   1/1       Running   0          4m        10.241.0.4   virtual-node-aci-linux
```

O pod é atribuído a um endereço IP interno da sub-rede de rede virtual do Azure delegada para uso com nós virtuais.

> [!NOTE]
> Se você usar imagens armazenadas no registro de contêiner do Azure, [Configure e use um segredo kubernetes][acr-aks-secrets]. Uma limitação atual de nós virtuais é que você não pode usar a autenticação de entidade de serviço do Azure AD integrada. Se você não usar um segredo, os pods agendados em nós virtuais falharão ao iniciar e reportar o erro `HTTP response status code 400 error code "InaccessibleImage"`.

## <a name="test-the-virtual-node-pod"></a>Testar o Pod do nó virtual

Para testar o pod em execução no nó virtual, navegue até o aplicativo de demonstração com um cliente Web. À medida que o Pod recebe um endereço IP interno, você pode testar rapidamente essa conectividade de outro pod no cluster AKS. Crie um pod de teste e anexe uma sessão de terminal a ele:

```azurecli-interactive
kubectl run -it --rm virtual-node-test --image=debian
```

Instale `curl` no pod usando `apt-get`:

```azurecli-interactive
apt-get update && apt-get install -y curl
```

Agora acesse o endereço do seu Pod usando `curl`, como *http://10.241.0.4* . Forneça seu próprio endereço IP interno mostrado no comando anterior `kubectl get pods`:

```azurecli-interactive
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

## <a name="next-steps"></a>Passos seguintes

Neste artigo, um pod foi agendado no nó virtual e atribuído um endereço IP interno e privado. Em vez disso, você pode criar uma implantação de serviço e rotear o tráfego para o Pod por meio de um balanceador de carga ou controlador de entrada. Para obter mais informações, consulte [criar um controlador de entrada básico em AKs][aks-basic-ingress].

Nós virtuais são um componente de uma solução de dimensionamento no AKS. Para obter mais informações sobre soluções de dimensionamento, consulte os seguintes artigos:

- [Usar o dimensionador de kubernetes horizontal Pod][aks-hpa]
- [Usar o dimensionador de cluster kubernetes][aks-cluster-autoscaler]
- [Confira a amostra de dimensionamento automático para nós virtuais][virtual-node-autoscale]
- [Leia mais sobre a biblioteca de código-fonte aberto Kubelet virtual][virtual-kubelet-repo]

<!-- LINKS - external -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[node-selector]:https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[toleration]: https://kubernetes.io/docs/concepts/configuration/taint-and-toleration/
[azure-cni]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[aks-github]: https://github.com/azure/aks/issues]
[virtual-node-autoscale]: https://github.com/Azure-Samples/virtual-node-autoscale
[virtual-kubelet-repo]: https://github.com/virtual-kubelet/virtual-kubelet
[acr-aks-secrets]: https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/

<!-- LINKS - internal -->
[aks-network]: ./networking-overview.md
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[aks-hpa]: tutorial-kubernetes-scale.md
[aks-cluster-autoscaler]: cluster-autoscaler.md
[aks-basic-ingress]: ingress-basic.md
[az-provider-list]: /cli/azure/provider#az-provider-list