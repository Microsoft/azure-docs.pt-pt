---
title: Criar nós virtuais utilizando o portal nos Serviços Azure Kubernetes (AKS)
description: Saiba como usar o portal Azure para criar um cluster Azure Kubernetes Services (AKS) que usa nós virtuais para executar cápsulas.
services: container-service
ms.topic: conceptual
ms.date: 03/15/2021
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: c1ecaa88dd5329d86818565983a6ba891a6d8424
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104577835"
---
# <a name="create-and-configure-an-azure-kubernetes-services-aks-cluster-to-use-virtual-nodes-in-the-azure-portal"></a>Criar e configurar um cluster Azure Kubernetes Services (AKS) para usar nós virtuais no portal Azure

Este artigo mostra-lhe como usar o portal Azure para criar e configurar os recursos de rede virtual e um cluster AKS com nós virtuais ativados.

> [!NOTE]
> [Este artigo](virtual-nodes.md) dá-lhe uma visão geral da disponibilidade e limitações da região usando nós virtuais.

## <a name="before-you-begin"></a>Antes de começar

Os nós virtuais permitem a comunicação de rede entre as cápsulas que funcionam em Instâncias de Contentores Azure (ACI) e o cluster AKS. Para fornecer esta comunicação, é criada uma sub-rede de rede virtual e são atribuídas permissões delegadas. Os nódigos virtuais só funcionam com clusters AKS criados com rede *avançada* (Azure CNI). Por padrão, os clusters AKS são criados com rede *básica* (kubenet). Este artigo mostra-lhe como criar uma rede virtual e sub-redes, em seguida, implementar um cluster AKS que utiliza networking avançado.

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

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no portal do Azure em https://portal.azure.com.

## <a name="create-an-aks-cluster"></a>Criar um cluster do AKS (Create an AKS cluster)

No canto superior esquerdo do portal Azure, selecione **Criar um**  >  **serviço kubernetes de** recurso .

Na página **Informações Básicas**, configure as opções seguintes:

- *DETALHES DO PROJETO*: selecione uma subscrição do Azure e selecione ou crie um grupo de recursos do Azure, como *myResourceGroup*. Introduza um **nome para o cluster do Kubernetes**, como *myAKSCluster*.
- *DETALHES DO CLUSTER*: Selecione uma versão de região e Kubernetes para o cluster AKS.
- *PISCINA DE NODE PRIMÁRIO*: Selecione um tamanho VM para os nós AKS. O tamanho da VM **não pode** ser alterado após a implementação de um cluster de AKS.
     - Selecione o número de nós a implementar no cluster. Para este artigo, decrete a **contagem de nó para** *1*. O número de nós **pode** ser ajustado após a implementação do cluster.

Clique **em seguida: Node Pools**.

Na página **"Agrupamentos de Nó",** selecione *Ativar os nós virtuais*.

:::image type="content" source="media/virtual-nodes-portal/enable-virtual-nodes.png" alt-text="Num navegador, mostra a criação de um cluster com nós virtuais ativados no portal Azure. A opção 'Ativar nós virtuais' é realçada.":::

Por padrão, uma identidade de cluster é criada. Esta identidade de cluster é usada para comunicação de cluster e integração com outros serviços Azure. Por padrão, esta identidade de cluster é uma identidade gerida. Para obter mais informações, consulte [utilização de identidades geridas.](use-managed-identity.md) Também pode usar um principal de serviço como identidade de cluster.

O cluster também está configurado para networking avançado. Os nós virtuais são configurados para usar a sua própria sub-rede de rede virtual Azure. Esta sub-rede delegou permissões para ligar os recursos Azure entre o cluster AKS. Se ainda não tiver uma sub-rede delegada, o portal Azure cria e configura a rede virtual Azure e a sub-rede para utilização com os nós virtuais.

Selecione **Rever + criar**. Depois de concluída a validação, selecione **Criar**.

O cluster do AKS demora alguns minutos a ser criado e a estar pronto para utilização.

## <a name="connect-to-the-cluster"></a>Ligar ao cluster

O Azure Cloud Shell é um shell interativo gratuito que pode utilizar para executar os passos neste artigo. Tem as ferramentas comuns do Azure pré-instaladas e configuradas para utilização com a sua conta. Para gerir um cluster de Kubernetes, utilize [kubectl][kubectl], o cliente de linha de comandos do Kubernetes. O cliente `kubectl` está pré-instalado no Azure Cloud Shell.

Para abrir a Cloud Shell, selecione **Experimente-a** a partir do canto superior direito de um bloco de código. Também pode lançar cloud Shell num separador de navegador indo para [https://shell.azure.com/bash](https://shell.azure.com/bash) . Selecione **Copiar** para copiar os blocos de código, cole-o no Cloud Shell e prima Enter para executá-lo.

Utilize o comando [az aks get-credentials][az-aks-get-credentials] para configurar `kubectl` para se ligar ao seu cluster do Kubernetes. O exemplo seguinte obtém credenciais para o nome do cluster *myAKSCluster* no grupo de recursos denominado *myResourceGroup*:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Para verificar a ligação ao cluster, utilize o comando [kubectl get][kubectl-get] para devolver uma lista de nós do cluster.

```console
kubectl get nodes
```

A saída de exemplo a seguir mostra o único nó VM criado e, em seguida, o nó virtual para Linux, *virtual-nó-aci-linux*:

```output
NAME                           STATUS    ROLES     AGE       VERSION
virtual-node-aci-linux         Ready     agent     28m       v1.11.2
aks-agentpool-14693408-0       Ready     agent     32m       v1.11.2
```

## <a name="deploy-a-sample-app"></a>Implementar uma aplicação de amostra

Na Azure Cloud Shell, crie um ficheiro nomeado `virtual-node.yaml` e copie no YAML seguinte. Para agendar o recipiente no nó, é definido um [nóselector][node-selector] e [tolerância.][toleration] Estas definições permitem que o pod seja programado no nó virtual e confirme que a funcionalidade está ativada com sucesso.

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
        image: mcr.microsoft.com/azuredocs/aci-helloworld
        ports:
        - containerPort: 80
      nodeSelector:
        kubernetes.io/role: agent
        beta.kubernetes.io/os: linux
        type: virtual-kubelet
      tolerations:
      - key: virtual-kubelet.io/provider
        operator: Exists
```

Executar a aplicação com o [comando de aplicação de kubectl.][kubectl-apply]

```azurecli-interactive
kubectl apply -f virtual-node.yaml
```

Utilize o [comando de pods de kubectl][kubectl-get] com o `-o wide` argumento para des outputar uma lista de cápsulas e o nó programado. Note que a `virtual-node-helloworld` cápsula foi programada no `virtual-node-linux` nó.

```console
kubectl get pods -o wide
```

```output
NAME                                     READY     STATUS    RESTARTS   AGE       IP           NODE
virtual-node-helloworld-9b55975f-bnmfl   1/1       Running   0          4m        10.241.0.4   virtual-node-aci-linux
```

O pod é atribuído um endereço IP interno da sub-rede virtual Azure delegada para utilização com nós virtuais.

> [!NOTE]
> Se utilizar imagens armazenadas no Registo do Contentor Azure, [configuure e utilize um segredo de Kubernetes][acr-aks-secrets]. Uma limitação atual dos nós virtuais é que você não pode usar a autenticação principal integrada do serviço Azure AD. Se não utilizar um segredo, as cápsulas programadas nos nós virtuais não começam e reportam o erro `HTTP response status code 400 error code "InaccessibleImage"` .

## <a name="test-the-virtual-node-pod"></a>Teste a vagem de nó virtual

Para testar o pod em execução no nó virtual, navegue para a aplicação de demonstração com um cliente web. À medida que a cápsula é atribuída a um endereço IP interno, pode testar rapidamente esta conectividade a partir de outra cápsula no cluster AKS. Crie uma cápsula de teste e anexe-lhe uma sessão terminal:

```console
kubectl run -it --rm virtual-node-test --image=mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11
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

## <a name="next-steps"></a>Passos seguintes

Neste artigo, foi agendada uma cápsula no nó virtual e atribuída um endereço IP interno e privado. Em vez disso, pode criar uma implementação de serviço e encaminhar o tráfego para a sua cápsula através de um equilibrador de carga ou controlador de entrada. Para obter mais informações, consulte Criar um controlador básico de entrada [em AKS][aks-basic-ingress].

Os nós virtuais são um componente de uma solução de escala em AKS. Para obter mais informações sobre soluções de escala, consulte os seguintes artigos:

- [Utilize o pod horizontal Kubernetes autoscaler][aks-hpa]
- [Utilize o cluster Kubernetes autoscaler][aks-cluster-autoscaler]
- [Confira a amostra de autoescala para nóns virtuais][virtual-node-autoscale]
- [Leia mais sobre a biblioteca virtual de fonte aberta de Kubelet][virtual-kubelet-repo]

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
[aks-network]: ./configure-azure-cni.md
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[aks-hpa]: tutorial-kubernetes-scale.md
[aks-cluster-autoscaler]: cluster-autoscaler.md
[aks-basic-ingress]: ingress-basic.md
[az-provider-list]: /cli/azure/provider#az-provider-list
[az-provider-register]: /cli/azure/provider#az-provider-register
