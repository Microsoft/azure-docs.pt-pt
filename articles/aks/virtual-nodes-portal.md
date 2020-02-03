---
title: Crie nós virtuais utilizando o portal nos Serviços Azure Kubernetes (AKS)
description: Aprenda a usar o portal Azure para criar um cluster Azure Kubernetes Services (AKS) que usa nós virtuais para executar cápsulas.
services: container-service
author: mlearned
ms.topic: conceptual
ms.service: container-service
ms.date: 05/06/2019
ms.author: mlearned
ms.openlocfilehash: 6a50663fd0cc907e0dc97b50decd8b6edbaa42cb
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76713217"
---
# <a name="create-and-configure-an-azure-kubernetes-services-aks-cluster-to-use-virtual-nodes-in-the-azure-portal"></a>Criar e configurar um cluster azure Kubernetes Services (AKS) para usar nós virtuais no portal Azure

Para implementar rapidamente cargas de trabalho num cluster do Serviço Azure Kubernetes (AKS), pode utilizar nós virtuais. Com nós virtuais, você tem um fornecimento rápido de casulos, e só paga por segundo pelo seu tempo de execução. Num cenário de escala, não é preciso esperar que o autoscaler do cluster Kubernetes implemente nós de computação VM para executar as cápsulas adicionais. Os nódosos virtuais são suportados apenas com cápsulas linux e nódosos.

Este artigo mostra-lhe como criar e configurar os recursos de rede virtuais e um cluster AKS com nós virtuais habilitados.

## <a name="before-you-begin"></a>Antes de começar

Os nós virtuais permitem a comunicação de rede entre as cápsulas que funcionam em Instâncias de Contentores Azure (ACI) e o cluster AKS. Para fornecer esta comunicação, é criada uma subnet de rede virtual e são atribuídas permissões delegadas. Os nós virtuais só funcionam com clusters AKS criados usando redes *avançadas.* Por padrão, os clusters AKS são criados com networking *básico.* Este artigo mostra-lhe como criar uma rede virtual e subredes, em seguida, implementar um cluster AKS que usa networking avançado.

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

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no portal do Azure em https://portal.azure.com.

## <a name="create-an-aks-cluster"></a>Criar um cluster do AKS (Create an AKS cluster)

No canto superior esquerdo do portal do Azure, selecione **Criar um recurso** > **Serviço Kubernetes**.

Na página **Basics,** configure as seguintes opções:

- *DETALHES DO PROJETO*: selecione uma subscrição do Azure e selecione ou crie um grupo de recursos do Azure, como *myResourceGroup*. Introduza um **nome para o cluster do Kubernetes**, como *myAKSCluster*.
- *DETALHES DO CLUSTER*: selecione uma região, a versão do Kubernetes e o prefixo do nome DNS do cluster do AKS.
- *PISCINA DO NÓ PRIMÁRIO*: Selecione um tamanho VM para os nódosos AKS. O tamanho da VM **não pode** ser alterado após a implementação de um cluster de AKS.
     - Selecione o número de nós a implementar no cluster. Para este artigo, coloque o **Nó a contar** até *1*. O número de nós **pode** ser ajustado após a implementação do cluster.

Clique **em seguir: Escala**.

Na página **Escala,** selecione *Ativado* sob **nós virtuais**.

![Criar cluster AKS e ativar os nós virtuais](media/virtual-nodes-portal/enable-virtual-nodes.png)

Por padrão, é criado um diretor de serviço de Diretório Ativo Azure. Este diretor de serviço é utilizado para comunicação de cluster e integração com outros serviços Azure.

O cluster também está configurado para networking avançado. Os nódosos virtuais estão configurados para usar a sua própria rede virtual Azure. Esta sub-rede delegou permissões para ligar os recursos do Azure entre o cluster AKS. Se ainda não tiver subnet delegado, o portal Azure cria e configura a rede virtual Azure e a subnet para utilização com os nós virtuais.

Selecione **Rever + criar**. Depois da validação estar completa, selecione **Criar**.

O cluster do AKS demora alguns minutos a ser criado e a estar pronto para utilização.

## <a name="connect-to-the-cluster"></a>Ligar ao cluster

O Azure Cloud Shell é um shell interativo gratuito que pode utilizar para executar os passos neste artigo. Tem as ferramentas comuns do Azure pré-instaladas e configuradas para utilização com a sua conta. Para gerir um cluster de Kubernetes, utilize [kubectl][kubectl], o cliente de linha de comandos do Kubernetes. O cliente `kubectl` está pré-instalado no Azure Cloud Shell.

Para abrir a Cloud Shell, selecione **Experimente** a partir do canto superior direito de um bloco de código. Também pode iniciar o Cloud Shell num separador do browser separado ao aceder a [https://shell.azure.com/bash](https://shell.azure.com/bash). Selecione **Copiar** para copiar os blocos de código, cole-o no Cloud Shell e prima Enter para executá-lo.

Utilize o comando [az aks get-credentials][az-aks-get-credentials] para configurar `kubectl` para se ligar ao seu cluster Kubernetes. O exemplo seguinte obtém credenciais para o nome do cluster *myAKSCluster* no grupo de recursos denominado *myResourceGroup*:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Para verificar a ligação ao cluster, utilize o comando [kubectl get][kubectl-get] para devolver uma lista de nós do cluster.

```azurecli-interactive
kubectl get nodes
```

A saída de exemplo seguinte mostra o nó VM único criado e, em seguida, o nó virtual para Linux, *virtual-node-aci-linux:*

```
$ kubectl get nodes

NAME                           STATUS    ROLES     AGE       VERSION
virtual-node-aci-linux         Ready     agent     28m       v1.11.2
aks-agentpool-14693408-0       Ready     agent     32m       v1.11.2
```

## <a name="deploy-a-sample-app"></a>Implementar uma aplicação de amostra

Na Casca de Nuvem Azure, crie um ficheiro chamado `virtual-node.yaml` e copie no seguinte YAML. Para agendar o recipiente no nó, é definido um [nodeSelector][node-selector] e [toleração.][toleration] Estas definições permitem que a cápsula seja programada no nó virtual e confirme que a funcionalidade está ativada com sucesso.

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

```azurecli-interactive
kubectl apply -f virtual-node.yaml
```

Use o [kubectl obter comando de cápsulas][kubectl-get] com o argumento `-o wide` para obter uma lista de cápsulas e o nó programado. Note que a cápsula `virtual-node-helloworld` foi programada no nó `virtual-node-linux`.

```
$ kubectl get pods -o wide

NAME                                     READY     STATUS    RESTARTS   AGE       IP           NODE
virtual-node-helloworld-9b55975f-bnmfl   1/1       Running   0          4m        10.241.0.4   virtual-node-aci-linux
```

A cápsula é atribuída a um endereço IP interno da subnet de rede virtual Azure delegada para utilização com nódosos virtuais.

> [!NOTE]
> Se utilizar imagens armazenadas no Registo de Contentores Azure, [configure e utilize um segredo kubernetes][acr-aks-secrets]. Uma limitação atual dos nódosos virtuais é que não pode utilizar a autenticação principal integrada do serviço Azure AD. Se não utilizar um segredo, as cápsulas programadas em nós virtuais não começam e reportam o erro `HTTP response status code 400 error code "InaccessibleImage"`.

## <a name="test-the-virtual-node-pod"></a>Teste a vagem de nó virtual

Para testar a cápsula que corre no nó virtual, navegue para a aplicação de demonstração com um cliente web. Como a cápsula é atribuída a um endereço IP interno, você pode testar rapidamente esta conectividade a partir de outra cápsula no cluster AKS. Crie uma cápsula de ensaio e fixe-lhe uma sessão terminal:

```azurecli-interactive
kubectl run -it --rm virtual-node-test --image=debian
```

Instale `curl` na cápsula utilizando `apt-get`:

```azurecli-interactive
apt-get update && apt-get install -y curl
```

Aceda agora ao endereço da sua cápsula utilizando `curl`, como *http://10.241.0.4* . Forneça o seu próprio endereço IP interno mostrado no comando `kubectl get pods` anterior:

```azurecli-interactive
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

## <a name="next-steps"></a>Passos Seguintes

Neste artigo, uma cápsula foi programada no nó virtual e atribuiu um endereço IP privado e interno. Em vez disso, poderia criar uma implantação de serviço e encaminhar o tráfego para a sua cápsula através de um equilibrador de carga ou controlador de entrada. Para mais informações, consulte Criar um controlador básico de [ingresso no AKS][aks-basic-ingress].

Os nódosos virtuais são um componente de uma solução de escala no AKS. Para obter mais informações sobre soluções de escala, consulte os seguintes artigos:

- [Utilize o autoscaler de cápsula horizontal Kubernetes][aks-hpa]
- [Use o autoscaler do cluster Kubernetes][aks-cluster-autoscaler]
- [Confira a amostra de escala automática para nódosos virtuais][virtual-node-autoscale]
- [Leia mais sobre a biblioteca virtual Kubelet open source][virtual-kubelet-repo]

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
[az-provider-register]: /cli/azure/provider?view=azure-cli-latest#az-provider-register
