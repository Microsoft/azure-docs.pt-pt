---
title: Executar Kubelet virtuais em um cluster do AKS (serviço kubernetes do Azure)
description: Saiba como usar o Kubelet virtual com o AKS (serviço kubernetes do Azure) para executar contêineres do Linux e do Windows em instâncias de contêiner do Azure.
services: container-service
author: mlearned
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 05/31/2019
ms.author: mlearned
ms.openlocfilehash: f18992be353d2d6cc739412d98ccd97d5e78d4c7
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/12/2019
ms.locfileid: "67613866"
---
# <a name="use-virtual-kubelet-with-azure-kubernetes-service-aks"></a>Usar Kubelet virtual com o serviço kubernetes do Azure (AKS)

As ACI (instâncias de contêiner do Azure) fornecem um ambiente hospedado para executar contêineres no Azure. Ao usar o ACI, não é necessário gerenciar a infraestrutura de computação subjacente, o Azure cuida desse gerenciamento para você. Ao executar contêineres no ACI, você será cobrado pelo segundo para cada contêiner em execução.

Ao usar o provedor de Kubelet virtual para instâncias de contêiner do Azure, os contêineres do Linux e do Windows podem ser agendados em uma instância de contêiner como se fosse um nó kubernetes padrão. Essa configuração permite que você aproveite os recursos de kubernetes e o valor de gerenciamento e o benefício de custo das instâncias de contêiner.

> [!NOTE]
> AKS agora tem suporte interno para o agendamento de contêineres em ACI, chamados de *nós virtuais*. Atualmente, esses nós virtuais dão suporte a instâncias de contêiner do Linux. Se você precisar agendar instâncias de contêiner do Windows, poderá continuar usando o Kubelet virtual. Caso contrário, você deve usar nós virtuais em vez das instruções de Kubelet virtual manuais indicadas neste artigo. Você pode começar com nós virtuais usando o [CLI do Azure][virtual-nodes-cli] ou [portal do Azure][virtual-nodes-portal].
>
> O virtual Kubelet é um projeto de código-fonte aberto experimental e deve ser usado como tal. Para contribuir, arquivar problemas e ler mais sobre kubelet virtuais, consulte o [projeto do GitHub do Kubelet virtual][vk-github].

## <a name="before-you-begin"></a>Antes de começar

Este documento pressupõe que você tenha um cluster AKS. Se você precisar de um cluster AKS, consulte o guia de [início rápido do AKS (serviço kubernetes do Azure)][aks-quick-start].

Você também precisa do CLI do Azure versão **2.0.65** ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

Para instalar o Kubelet virtual, instale e configure o [Helm][aks-helm] em seu cluster AKs. Verifique se o seu gaveta está [configurado para uso com o RBAC kubernetes](#for-rbac-enabled-clusters), se necessário.

### <a name="register-container-instances-feature-provider"></a>Registrar provedor de recursos de instâncias de contêiner

Se você não usou anteriormente o serviço ACI (instância de contêiner do Azure), registre o provedor de serviços com sua assinatura. Você pode verificar o status do registro do provedor de ACI usando o comando [AZ Provider List][az-provider-list] , conforme mostrado no exemplo a seguir:

```azurecli-interactive
az provider list --query "[?contains(namespace,'Microsoft.ContainerInstance')]" -o table
```

O provedor *Microsoft. ContainerInstance* deve relatar como *registrado*, conforme mostrado na seguinte saída de exemplo:

```console
Namespace                    RegistrationState
---------------------------  -------------------
Microsoft.ContainerInstance  Registered
```

Se o provedor aparecer como não *registrado*, registre o provedor usando o [registro do provedor AZ][az-provider-register] , conforme mostrado no exemplo a seguir:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerInstance
```

### <a name="for-rbac-enabled-clusters"></a>Para clusters habilitados para RBAC

Se o cluster do AKS estiver habilitado para RBAC, você deverá criar uma conta de serviço e uma associação de função para uso com o Gavetar. Para obter mais informações, consulte [controle de acesso baseado em função do Helm][helm-rbac]. Para criar uma conta de serviço e uma associação de função, crie um arquivo chamado *RBAC-virtual-kubelet. YAML* e cole a seguinte definição:

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

Aplique a conta de serviço e a associação com [kubectl aplicar][kubectl-apply] e especifique seu arquivo *RBAC-virtual-kubelet. YAML* , conforme mostrado no exemplo a seguir:

```console
$ kubectl apply -f rbac-virtual-kubelet.yaml

clusterrolebinding.rbac.authorization.k8s.io/tiller created
```

Configure o Helm para usar a conta de serviço da gaveta:

```console
helm init --service-account tiller
```

Agora você pode continuar a instalar o Kubelet virtual em seu cluster AKS.

## <a name="installation"></a>Instalação

Use o comando [AZ AKs install-Connector][aks-install-connector] para instalar o Kubelet virtual. O exemplo a seguir implanta o conector do Linux e do Windows.

```azurecli-interactive
az aks install-connector \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --connector-name virtual-kubelet \
    --os-type Both
```

Esses argumentos estão disponíveis para o comando [AZ AKs install-Connector][aks-install-connector] .

| Argumento | Descrição | Requerido |
|---|---|:---:|
| `--connector-name` | Nome do conector do ACI.| Sim |
| `--name` `-n` | Nome do cluster gerenciado. | Sim |
| `--resource-group` `-g` | Nome do grupo de recursos. | Sim |
| `--os-type` | Tipo de sistema operacional de instâncias de contêiner. Valores permitidos: Ambos, Linux e Windows. Predefinição: Linux. | Não |
| `--aci-resource-group` | O grupo de recursos no qual criar os grupos de contêineres ACI. | Não |
| `--location` `-l` | O local para criar os grupos de contêineres ACI. | Não |
| `--service-principal` | Entidade de serviço usada para autenticação para APIs do Azure. | Não |
| `--client-secret` | Segredo associado à entidade de serviço. | Não |
| `--chart-url` | URL de um gráfico do Helm que instala o conector do ACI. | Não |
| `--image-tag` | A marca de imagem da imagem de contêiner kubelet virtual. | Não |

## <a name="validate-virtual-kubelet"></a>Validar Kubelet virtuais

Para validar que o Kubelet virtual foi instalado, retorne uma lista de nós kubernetes usando o comando [kubectl Get Nodes][kubectl-get] :

```console
$ kubectl get nodes

NAME                                             STATUS   ROLES   AGE   VERSION
aks-nodepool1-56577038-0                         Ready    agent   11m   v1.12.8
virtual-kubelet-virtual-kubelet-linux-eastus     Ready    agent   39s   v1.13.1-vk-v0.9.0-1-g7b92d1ee-dev
virtual-kubelet-virtual-kubelet-windows-eastus   Ready    agent   37s   v1.13.1-vk-v0.9.0-1-g7b92d1ee-dev
```

## <a name="run-linux-container"></a>Executar contêiner do Linux

Crie um arquivo chamado `virtual-kubelet-linux.yaml` e copie o seguinte YAML. Observe que um [nodeSelector][node-selector] e [toleration][toleration] estão sendo usados para agendar o contêiner no nó.

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
        beta.kubernetes.io/os: linux
        kubernetes.io/role: agent
        type: virtual-kubelet
      tolerations:
      - key: virtual-kubelet.io/provider
        operator: Equal
        value: azure
        effect: NoSchedule
```

Execute o aplicativo com o comando [kubectl Create][kubectl-create] .

```console
kubectl create -f virtual-kubelet-linux.yaml
```

Use o comando [kubectl Get pods][kubectl-get] com o `-o wide` argumento para gerar uma lista de pods com o nó agendado. Observe que o `aci-helloworld` Pod foi agendado `virtual-kubelet-virtual-kubelet-linux` no nó.

```console
$ kubectl get pods -o wide

NAME                              READY   STATUS    RESTARTS   AGE     IP               NODE
aci-helloworld-7b9ffbf946-rx87g   1/1     Running   0          22s     52.224.147.210   virtual-kubelet-virtual-kubelet-linux-eastus
```

## <a name="run-windows-container"></a>Executar o contentor do Windows

Crie um arquivo chamado `virtual-kubelet-windows.yaml` e copie o seguinte YAML. Observe que um [nodeSelector][node-selector] e [toleration][toleration] estão sendo usados para agendar o contêiner no nó.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nanoserver-iis
spec:
  replicas: 1
  selector:
    matchLabels:
      app: nanoserver-iis
  template:
    metadata:
      labels:
        app: nanoserver-iis
    spec:
      containers:
      - name: nanoserver-iis
        image: microsoft/iis:nanoserver
        ports:
        - containerPort: 80
      nodeSelector:
        beta.kubernetes.io/os: windows
        kubernetes.io/role: agent
        type: virtual-kubelet
      tolerations:
      - key: virtual-kubelet.io/provider
        operator: Equal
        value: azure
        effect: NoSchedule
```

Execute o aplicativo com o comando [kubectl Create][kubectl-create] .

```console
kubectl create -f virtual-kubelet-windows.yaml
```

Use o comando [kubectl Get pods][kubectl-get] com o `-o wide` argumento para gerar uma lista de pods com o nó agendado. Observe que o `nanoserver-iis` Pod foi agendado `virtual-kubelet-virtual-kubelet-windows` no nó.

```console
$ kubectl get pods -o wide

NAME                              READY   STATUS    RESTARTS   AGE     IP               NODE
nanoserver-iis-5d999b87d7-6h8s9   1/1     Running   0          47s     52.224.143.39    virtual-kubelet-virtual-kubelet-windows-eastus
```

## <a name="remove-virtual-kubelet"></a>Remover Kubelet virtual

Use o comando [AZ AKs remove-Connector][aks-remove-connector] para remover Kubelet virtuais. Substitua os valores de argumento pelo nome do conector, do cluster AKS e do grupo de recursos de cluster AKS.

```azurecli-interactive
az aks remove-connector \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --connector-name virtual-kubelet \
    --os-type Both
```

> [!NOTE]
> Se você encontrar erros ao remover ambos os conectores do sistema operacional, ou se quiser remover apenas o conector do sistema operacional Windows ou Linux, poderá especificar manualmente o tipo de sistema operacional. Adicione o `--os-type` parâmetro ao comando anterior `az aks remove-connector` e especifique `Windows` ou `Linux`.

## <a name="next-steps"></a>Passos seguintes

Para possíveis problemas com o Kubelet virtual, consulte as [sutilezas e soluções alternativas conhecidas][vk-troubleshooting]. Para relatar problemas com o Kubelet virtual, [abra um problema do GitHub][vk-issues].

Leia mais sobre o virtual Kubelet no [projeto Kubelet GitHub virtual][vk-github].

<!-- LINKS - internal -->
[aks-quick-start]: ./kubernetes-walkthrough.md
[aks-remove-connector]: /cli/azure/aks#az-aks-remove-connector
[az-container-list]: /cli/azure/aks#az-aks-list
[aks-install-connector]: /cli/azure/aks#az-aks-install-connector
[virtual-nodes-cli]: virtual-nodes-cli.md
[virtual-nodes-portal]: virtual-nodes-portal.md
[aks-helm]: kubernetes-helm.md
[az-provider-list]: /cli/azure/provider#az-provider-list
[az-provider-register]: /cli/azure/provider#az-provider-register

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[node-selector]:https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[toleration]: https://kubernetes.io/docs/concepts/configuration/taint-and-toleration/
[vk-github]: https://github.com/virtual-kubelet/virtual-kubelet
[helm-rbac]: https://docs.helm.sh/using_helm/#role-based-access-control
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[vk-troubleshooting]: https://github.com/virtual-kubelet/virtual-kubelet#known-quirks-and-workarounds
[vk-issues]: https://github.com/virtual-kubelet/virtual-kubelet/issues
