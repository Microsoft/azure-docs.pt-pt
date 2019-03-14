---
title: Criar nós virtuais com o portal dos serviços de Kubernetes no Azure (AKS)
description: Saiba como utilizar o portal do Azure para criar um cluster de serviços de Kubernetes do Azure (AKS) que usa nós virtuais para executar os pods.
services: container-service
author: iainfoulds
ms.topic: conceptual
ms.service: container-service
ms.date: 12/03/2018
ms.author: iainfou
ms.openlocfilehash: 550918292d8cf8baca9403cdc687f336672150f7
ms.sourcegitcommit: d89b679d20ad45d224fd7d010496c52345f10c96
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57790882"
---
# <a name="create-and-configure-an-azure-kubernetes-services-aks-cluster-to-use-virtual-nodes-in-the-azure-portal"></a>Criar e configurar um cluster de serviços de Kubernetes do Azure (AKS) para utilizar nós virtuais no portal do Azure

Para implementar rapidamente as cargas de trabalho num cluster do Azure Kubernetes Service (AKS), pode utilizar nós virtuais. Connosco virtuais, tem o aprovisionamento rápido de pods e pague apenas por segundo para o tempo de execução. Num cenário de dimensionamento, não terá de aguardar que o cluster de Kubernetes dimensionamento automático implementar nós de computação VM para executar os pods adicionais. Este artigo mostra-lhe como criar e configurar os recursos de rede virtual e um cluster do AKS connosco virtuais ativados.

> [!IMPORTANT]
> Nós virtuais para o AKS estão atualmente nos **pré-visualização**. As pré-visualizações são tornadas disponíveis para si na condição de concordar com os [termos suplementares de utilização](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Alguns aspetos desta funcionalidade podem alterar-se após a disponibilidade geral (GA).

## <a name="preview-limitations"></a>Limitações de pré-visualização

Enquanto esta funcionalidade está em pré-visualização, as seguintes regiões são suportadas para implementações:

* Leste da Austrália (australiaeast)
* Este dos E.U.A. (eastus)
* E.u.a. Centro-Oeste (EUA)
* Europa Ocidental (westeurope)
* E.U.A. oeste (westus)

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no portal do Azure em https://portal.azure.com.

## <a name="create-an-aks-cluster"></a>Criar um cluster do AKS (Create an AKS cluster)

No canto superior esquerdo do portal do Azure, selecione **Criar um recurso** > **Serviço Kubernetes**.

Sobre o **Noções básicas** página, configure as seguintes opções:

- *DETALHES DO PROJETO*: Selecione uma subscrição do Azure, em seguida, selecionar ou criar um grupo de recursos do Azure, tal como *myResourceGroup*. Introduza um **nome para o cluster do Kubernetes**, como *myAKSCluster*.
- *DETALHES DO CLUSTER*: Selecione uma região, a versão do Kubernetes e o prefixo de nome de DNS para o cluster do AKS.
- *DIMENSIONAMENTO*: Selecione um tamanho VM para os nós do AKS. O tamanho da VM **não pode** ser alterado após a implementação de um cluster de AKS.
    - Selecione o número de nós a implementar no cluster. Neste artigo, definido **contagem de nós** ao *1*. O número de nós **pode** ser ajustado após a implementação do cluster.
    - Sob **nós virtuais**, selecione *ativado*.

![Criar um cluster do AKS e ativar os nós virtuais](media/virtual-nodes-portal/enable-virtual-nodes.png)

Por predefinição, é criado um principal de serviço do Azure Active Directory. Este principal de serviço é utilizado para comunicação de cluster e integração com outros serviços do Azure.

O cluster também está configurado para funcionamento em rede avançado. Os nós virtuais estão configurados para utilizar a sua própria sub-rede da rede virtual do Azure. Esta sub-rede tem permissões delegadas para ligar recursos do Azure entre o cluster do AKS. Se ainda não tiver delegado sub-rede, o portal do Azure cria e configura a rede virtual do Azure e a sub-rede para utilização com os nós virtuais.

Selecione **Rever + criar**. Depois de concluída a validação, selecione **criar**.

O cluster do AKS demora alguns minutos a ser criado e a estar pronto para utilização.

## <a name="connect-to-the-cluster"></a>Ligar ao cluster

O Azure Cloud Shell é um shell interativo gratuito que pode utilizar para executar os passos neste artigo. Tem as ferramentas comuns do Azure pré-instaladas e configuradas para utilização com a sua conta. Para gerir um cluster de Kubernetes, utilize [kubectl][kubectl], o cliente de linha de comandos do Kubernetes. O cliente `kubectl` está pré-instalado no Azure Cloud Shell.

Para abrir o Cloud Shell, selecione **experimente** do canto superior direito de um bloco de código. Também pode iniciar o Cloud Shell num separador do browser separado ao aceder a [https://shell.azure.com/bash](https://shell.azure.com/bash). Selecione **Copiar** para copiar os blocos de código, cole-o no Cloud Shell e prima Enter para executá-lo.

Utilize o comando [az aks get-credentials][az-aks-get-credentials] para configurar `kubectl` para se ligar ao seu cluster do Kubernetes. O exemplo seguinte obtém credenciais para o nome do cluster *myAKSCluster* no grupo de recursos denominado *myResourceGroup*:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Para verificar a ligação ao cluster, utilize o comando [kubectl get][kubectl-get] para devolver uma lista de nós do cluster.

```azurecli-interactive
kubectl get nodes
```

O resultado de exemplo seguinte mostra o único nó de VM criada e, em seguida, o nó virtual para Linux, *virtual-nó-aci-linux*:

```
$ kubectl get nodes

NAME                           STATUS    ROLES     AGE       VERSION
virtual-node-aci-linux         Ready     agent     28m       v1.11.2
aks-agentpool-14693408-0       Ready     agent     32m       v1.11.2
```

## <a name="deploy-a-sample-app"></a>Implementar uma aplicação de exemplo

No Azure Cloud Shell, crie um ficheiro denominado `virtual-node.yaml` e copie o YAML seguinte. Para agendar o contentor no nó, uma [nodeSelector] [ node-selector] e [toleration] [ toleration] são definidos. Estas definições permitem o pod ao ser agendada no nó virtual e confirme que a funcionalidade é ativada com êxito.

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

```azurecli-interactive
kubectl apply -f virtual-node.yaml
```

Utilize o [kubectl obter pods] [ kubectl-get] comando com o `-o wide` argumento para uma lista de pods e o nó agendado de saída. Tenha em atenção que o `virtual-node-helloworld` pod foi agendada ao `virtual-node-linux` nó.

```
$ kubectl get pods -o wide

NAME                                     READY     STATUS    RESTARTS   AGE       IP           NODE
virtual-node-helloworld-9b55975f-bnmfl   1/1       Running   0          4m        10.241.0.4   virtual-node-aci-linux
```

O pod é atribuído um endereço IP da sub-rede da rede virtual do Azure delegada para utilização connosco virtuais.

> [!NOTE]
> Se utilizar imagens armazenadas no Azure Container Registry, [configurar e utilizar um segredo do Kubernetes][acr-aks-secrets]. Uma limitação atual dos nós virtuais de pré-visualização é que não é possível utilizar integrado a autenticação do principal de serviço do Azure AD. Se não usar um segredo, pods agendadas em nós virtuais não conseguem iniciar e reportar o erro `HTTP response status code 400 error code "InaccessibleImage"`.

## <a name="test-the-virtual-node-pod"></a>O pod do nó virtual de teste

Para testar o pod em execução no nó virtual, navegue para a aplicação de demonstração com um cliente web. Como o pod está atribuído um endereço IP interno, pode testar rapidamente a essa conectividade de pod outro no cluster do AKS. Criar um pod de teste e anexar uma sessão de terminal a ele:

```azurecli-interactive
kubectl run -it --rm virtual-node-test --image=debian
```

Instale `curl` no pod com `apt-get`:

```azurecli-interactive
apt-get update && apt-get install -y curl
```

Agora aceder o endereço da sua utilização de pod `curl`, tal como *http://10.241.0.4*. Fornecer seu próprio endereço IP mostrado no anterior `kubectl get pods` comando:

```azurecli-interactive
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

## <a name="next-steps"></a>Passos Seguintes

Neste artigo, um pod foi agendado no nó virtual e atribuído um endereço IP privado, interno. Em vez disso, pode criar uma implementação de serviço e encaminhar o tráfego para seu pod através de um balanceador de carga ou controlador de entradas. Para obter mais informações, consulte [criar um controlador de entrada básico no AKS][aks-basic-ingress].

Nós virtuais são um componente de uma solução de dimensionamento no AKS. Para obter mais informações sobre soluções de dimensionamento, veja os artigos seguintes:

- [Utilizar o dimensionamento automático horizontal de pods de Kubernetes][aks-hpa]
- [Utilizar o dimensionamento automático de cluster de Kubernetes][aks-cluster-autoscaler]

<!-- LINKS - external -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[node-selector]:https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[toleration]: https://kubernetes.io/docs/concepts/configuration/taint-and-toleration/
[azure-cni]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md

<!-- LINKS - internal -->
[aks-network]: ./networking-overview.md
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[aks-hpa]: tutorial-kubernetes-scale.md
[aks-cluster-autoscaler]: autoscaler.md
[aks-basic-ingress]: ingress-basic.md
[acr-aks-secrets]: ../container-registry/container-registry-auth-aks.md#access-with-kubernetes-secret

