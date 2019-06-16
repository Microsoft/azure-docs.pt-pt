---
title: Executar o Virtual Kubelet num cluster do Azure Kubernetes Service (AKS)
description: Saiba como utilizar o Virtual Kubelet com o Azure Kubernetes Service (AKS) para executar contentores de Linux e Windows no Azure Container Instances.
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 05/31/2019
ms.author: iainfou
ms.openlocfilehash: cc0c3becf21cb54b97a88e9ba35b38308af81a85
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66475418"
---
# <a name="use-virtual-kubelet-with-azure-kubernetes-service-aks"></a>Utilizar o Virtual Kubelet com o serviço Kubernetes do Azure (AKS)

O Azure Container Instances (ACI) fornecem um ambiente de hospedagem para executar contentores no Azure. Ao utilizar o ACI, não é necessário para gerir a infraestrutura de computação subjacentes, o Azure trata esse gerenciamento para. Ao executar contentores no ACI, são cobradas por segundo para cada contentor em execução.

Ao usar o Virtual Kubelet fornecedor do Azure Container Instances, contentores do Linux e Windows podem ser agendadas numa instância de contentor, como se fosse um nó de Kubernetes padrão. Esta configuração permite-lhe tirar partido das capacidades do Kubernetes e o benefício de valor e o custo de gestão de instâncias do contentor.

> [!NOTE]
> AKS agora tem suporte incorporado para agendar contentores no ACI, chamado *nós virtuais*. Estes nós virtuais suportam atualmente as instâncias de contentor do Linux. Se precisar de agendar a instâncias de contentor do Windows, pode continuar a utilizar o Virtual Kubelet. Caso contrário, deve usar nós virtuais, em vez das instruções de Virtual Kubelet manuais indicadas neste artigo. Pode começar a utilizar com nós virtuais utilizando o [CLI do Azure] [ virtual-nodes-cli] ou [portal do Azure][virtual-nodes-portal].
>
> Virtual Kubelet é um projeto de código-fonte aberto experimental e deve ser utilizado como tal. Para contribuir, problemas de ficheiros e ler mais sobre o virtual kubelet, consulte a [projeto Virtual Kubelet GitHub][vk-github].

## <a name="before-you-begin"></a>Antes de começar

Este documento parte do princípio de que tem um cluster do AKS. Se precisar de um cluster do AKS, consulte a [início rápido do Azure Kubernetes Service (AKS)][aks-quick-start].

Também tem a versão da CLI do Azure **2.0.65** ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

Para instalar o Virtual Kubelet, instale e configure [Helm] [ aks-helm] no cluster do AKS. Certifique-se de que o Tiller está [configurada para utilização com o Kubernetes RBAC](#for-rbac-enabled-clusters), se for necessário.

### <a name="register-container-instances-feature-provider"></a>Registar o fornecedor de recursos de instâncias de contentor

Se não tiver utilizado anteriormente o serviço de instância de contentor do Azure (ACI), registe o fornecedor de serviço com a sua subscrição. Pode verificar o estado do uso de registo de fornecedor do ACI a [lista de fornecedores de az] [ az-provider-list] de comando, conforme mostrado no exemplo a seguir:

```azurecli-interactive
az provider list --query "[?contains(namespace,'Microsoft.ContainerInstance')]" -o table
```

O *Microsoft.ContainerInstance* fornecedor deve reportar como *registado*, conforme mostrado no seguinte exemplo:

```console
Namespace                    RegistrationState
---------------------------  -------------------
Microsoft.ContainerInstance  Registered
```

Se o fornecedor é apresentado como *NotRegistered*, registar o fornecedor a utilizar o [Registre-se fornecedor de az] [ az-provider-register] conforme mostrado no exemplo a seguir:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerInstance
```

### <a name="for-rbac-enabled-clusters"></a>Para clusters habilitados em RBAC

Se o seu cluster do AKS tem a capacidade de RBAC, tem de criar uma conta de serviço e o enlace de função para utilização com Tiller. Para obter mais informações, consulte [controlo de acesso baseado em funções do Helm][helm-rbac]. Para criar uma conta de serviço e o enlace de função, crie um ficheiro denominado *rbac virtual kubelet.yaml* e cole a seguinte definição:

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

Aplicam-se a conta de serviço e a ligação com [aplicam-se de kubectl] [ kubectl-apply] e especifique seus *rbac virtual kubelet.yaml* ficheiro, conforme mostrado no exemplo seguinte:

```console
$ kubectl apply -f rbac-virtual-kubelet.yaml

clusterrolebinding.rbac.authorization.k8s.io/tiller created
```

Configure o Helm para utilizar a conta de serviço do tiller:

```console
helm init --service-account tiller
```

Pode continuar a instalar o Virtual Kubelet no seu cluster do AKS.

## <a name="installation"></a>Instalação

Utilize o [az aks install-connector] [ aks-install-connector] comando para instalar o Virtual Kubelet. O exemplo seguinte implementa o conector do Linux e do Windows.

```azurecli-interactive
az aks install-connector \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --connector-name virtual-kubelet \
    --os-type Both
```

Estes argumentos estão disponíveis para o [az aks install-connector] [ aks-install-connector] comando.

| Argumento: | Descrição | Necessário |
|---|---|:---:|
| `--connector-name` | Nome do conector ACI.| Sim |
| `--name` `-n` | Nome do cluster gerido. | Sim |
| `--resource-group` `-g` | Nome do grupo de recursos. | Sim |
| `--os-type` | Tipo de sistema operativo de instâncias de contentor. Valores permitidos: Both, Linux, Windows. predefinição: Linux. | Não |
| `--aci-resource-group` | O grupo de recursos no qual pretende criar os grupos de contentor do ACI. | Não |
| `--location` `-l` | A localização para criar os grupos de contentor do ACI. | Não |
| `--service-principal` | Principal de serviço utilizado para autenticação para APIs do Azure. | Não |
| `--client-secret` | Segredo associado ao principal de serviço. | Não |
| `--chart-url` | URL de um gráfico do Helm que instala o conector ACI. | Não |
| `--image-tag` | A tag de imagem da imagem de contentor virtual kubelet. | Não |

## <a name="validate-virtual-kubelet"></a>Validar o Virtual Kubelet

Para validar que o Virtual Kubelet foi instalado, retornar uma lista de nós do Kubernetes com o [kubectl obter nós] [ kubectl-get] comando:

```console
$ kubectl get nodes

NAME                                             STATUS   ROLES   AGE   VERSION
aks-nodepool1-56577038-0                         Ready    agent   11m   v1.12.8
virtual-kubelet-virtual-kubelet-linux-eastus     Ready    agent   39s   v1.13.1-vk-v0.9.0-1-g7b92d1ee-dev
virtual-kubelet-virtual-kubelet-windows-eastus   Ready    agent   37s   v1.13.1-vk-v0.9.0-1-g7b92d1ee-dev
```

## <a name="run-linux-container"></a>Executar o contentor do Linux

Crie um ficheiro denominado `virtual-kubelet-linux.yaml` e copie o YAML seguinte. Observe que uma [nodeSelector] [ node-selector] e [toleration] [ toleration] estão a ser utilizadas para agendar o contentor no nó.

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

Execute a aplicação com o [criar kubectl] [ kubectl-create] comando.

```console
kubectl create -f virtual-kubelet-linux.yaml
```

Utilize o [kubectl obter pods] [ kubectl-get] comando com o `-o wide` argumento para uma lista de pods com o nó agendado de saída. Tenha em atenção que o `aci-helloworld` pod foi agendada ao `virtual-kubelet-virtual-kubelet-linux` nó.

```console
$ kubectl get pods -o wide

NAME                              READY   STATUS    RESTARTS   AGE     IP               NODE
aci-helloworld-7b9ffbf946-rx87g   1/1     Running   0          22s     52.224.147.210   virtual-kubelet-virtual-kubelet-linux-eastus
```

## <a name="run-windows-container"></a>Executar o contentor do Windows

Crie um ficheiro denominado `virtual-kubelet-windows.yaml` e copie o YAML seguinte. Observe que uma [nodeSelector] [ node-selector] e [toleration] [ toleration] estão a ser utilizadas para agendar o contentor no nó.

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

Execute a aplicação com o [criar kubectl] [ kubectl-create] comando.

```console
kubectl create -f virtual-kubelet-windows.yaml
```

Utilize o [kubectl obter pods] [ kubectl-get] comando com o `-o wide` argumento para uma lista de pods com o nó agendado de saída. Tenha em atenção que o `nanoserver-iis` pod foi agendada ao `virtual-kubelet-virtual-kubelet-windows` nó.

```console
$ kubectl get pods -o wide

NAME                              READY   STATUS    RESTARTS   AGE     IP               NODE
nanoserver-iis-5d999b87d7-6h8s9   1/1     Running   0          47s     52.224.143.39    virtual-kubelet-virtual-kubelet-windows-eastus
```

## <a name="remove-virtual-kubelet"></a>Remover o Virtual Kubelet

Utilize o [az aks remove-connector] [ aks-remove-connector] comando para remover o Virtual Kubelet. Substitua os valores de argumento com o nome do conector do cluster do AKS e o grupo de recursos de cluster do AKS.

```azurecli-interactive
az aks remove-connector \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --connector-name virtual-kubelet \
    --os-type Both
```

> [!NOTE]
> Se encontrar erros, removendo os dois conectores do sistema operacional, ou se pretende remover apenas o conector do Windows ou o SO Linux, pode especificar manualmente o tipo de SO. Adicionar a `--os-type` parâmetro anterior `az aks remove-connector` de comandos e especificar `Windows` ou `Linux`.

## <a name="next-steps"></a>Passos Seguintes

Para possíveis problemas com o Virtual Kubelet, consulte a [conhecido sutilezas e soluções alternativas][vk-troubleshooting]. Reportar problemas com o Virtual Kubelet [abra um problema do GitHub][vk-issues].

Saiba mais sobre o Virtual Kubelet, o [projeto Virtual Kubelet GitHub][vk-github].

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
