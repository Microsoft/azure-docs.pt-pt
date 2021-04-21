---
title: 'Quickstart: Implementar um cluster AKS com nódoas de computação confidencial utilizando o Azure CLI'
description: Aprenda a criar um cluster Azure Kubernetes Service (AKS) com nós confidenciais e implemente uma aplicação Hello World utilizando o Azure CLI.
author: agowdamsft
ms.service: container-service
ms.subservice: confidential-computing
ms.topic: quickstart
ms.date: 04/08/2021
ms.author: amgowda
ms.custom: contentperf-fy21q3, devx-track-azurecli
ms.openlocfilehash: b1ef397c1a3f6770d197ca8fd0faa83c9e8360f5
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107816463"
---
# <a name="quickstart-deploy-an-aks-cluster-with-confidential-computing-nodes-by-using-the-azure-cli"></a>Quickstart: Implementar um cluster AKS com nódoas de computação confidencial utilizando o Azure CLI

Neste quickstart, você usará o CLI Azure para implementar um cluster Azure Kubernetes Service (AKS) com nós de computação confidencial (DCsv2). Em seguida, você vai executar uma simples aplicação Hello World em um enclave. Você também pode providenciar um cluster e adicionar nós de computação confidencial do portal Azure, mas este quickstart foca-se no Azure CLI.

AKS é um serviço gerido pela Kubernetes que permite aos desenvolvedores ou operadores de cluster implementar e gerir rapidamente clusters. Para saber mais, leia a introdução da [AKS](../aks/intro-kubernetes.md) e a [visão geral dos nós confidenciais da AKS.](confidential-nodes-aks-overview.md)

As características dos nóns de computação confidencial incluem:

- Os nós dos trabalhadores linux suportam contentores Linux.
- Máquina virtual de geração 2 (VM) com nós Ubuntu 18.04 VM.
- CpU capaz de Intel SGX para ajudar a executar os seus contentores em enclave protegido de confidencialidade alavancando a memória de cache de página encriptada (EPC). Para obter mais informações, consulte [perguntas frequentes para a computação confidencial Azure](./faq.md).
- Intel SGX DCAP Driver pré-instalado nos nós de computação confidencial. Para obter mais informações, consulte [perguntas frequentes para a computação confidencial Azure](./faq.md).

> [!NOTE]
> Os DCsv2 VMs utilizam hardware especializado que está sujeito a preços mais elevados e disponibilidade de região. Para mais informações, consulte as [SKUs disponíveis e as regiões apoiadas.](virtual-machine-solutions.md)

## <a name="prerequisites"></a>Pré-requisitos

Este início rápido requer:

- Uma subscrição ativa do Azure. Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.
- Versão Azure CLI 2.0.64 ou posteriormente instalada e configurada na sua máquina de implantação. 

  Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](../container-registry/container-registry-get-started-azure-cli.md).
- Um mínimo de seis núcleos DCsv2 disponíveis na sua subscrição. 

  Por predefinição, a quota de computação confidencial por subscrição Azure é de oito núcleos VM. Se planeia providenciar um cluster que requer mais de oito núcleos, siga [estas instruções](../azure-portal/supportability/per-vm-quota-requests.md) para levantar um bilhete de aumento de quota.

## <a name="create-an-aks-cluster-with-confidential-computing-nodes-and-add-on"></a>Criar um cluster AKS com nódoas de computação confidencial e add-on

Utilize as seguintes instruções para criar um cluster AKS com o addon de computação confidencial ativado, adicione um conjunto de nós ao cluster e verifique o que criou.

### <a name="create-an-aks-cluster-with-a-system-node-pool"></a>Criar um cluster AKS com uma piscina de nó de sistema

> [!NOTE]
> Se já tem um cluster AKS que satisfaz os critérios pré-requisitos listados anteriormente, [salte para a secção seguinte](#add-a-user-node-pool-with-confidential-computing-capabilities-to-the-aks-cluster) para adicionar um conjunto de nós de computação confidencial.

Em primeiro lugar, crie um grupo de recursos para o cluster utilizando o [grupo az criar][az-group-create] comando. O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* na região *oeste2:*

```azurecli-interactive
az group create --name myResourceGroup --location westus2
```

Agora crie um cluster AKS, com o addon de computação confidencial ativado, utilizando o comando [az aks create:][az-aks-create]

```azurecli-interactive
az aks create -g myResourceGroup --name myAKSCluster --generate-ssh-keys --enable-addon confcom
```

### <a name="add-a-user-node-pool-with-confidential-computing-capabilities-to-the-aks-cluster"></a>Adicione um conjunto de nó de utilizador com capacidades de computação confidenciais ao cluster AKS 

Executar o seguinte comando para adicionar um conjunto de nós de `Standard_DC2s_v2` tamanho com três nós ao cluster AKS. Pode escolher outro SKU da [lista de SKUs e regiões do DCsv2 suportados.](../virtual-machines/dcv2-series.md)

```azurecli-interactive
az aks nodepool add --cluster-name myAKSCluster --name confcompool1 --resource-group myResourceGroup --node-vm-size Standard_DC2s_v2
```

Depois de executar o comando, um novo conjunto de nó com DCsv2 deve ser visível com daemonSets de computação confidencial[(plug-in de dispositivo SGX).](confidential-nodes-aks-overview.md#confidential-computing-add-on-for-aks)

### <a name="verify-the-node-pool-and-add-on"></a>Verifique a piscina do nó e o add-on

Obtenha as credenciais para o seu cluster AKS usando o comando [az aks get-credentials:][az-aks-get-credentials]

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Utilize o `kubectl get pods` comando para verificar se os nóleiros são criados corretamente e os DaemonSets relacionados com a SGX estão a funcionar em piscinas de nó de DCsv2:

```console
$ kubectl get pods --all-namespaces

kube-system     sgx-device-plugin-xxxx     1/1     Running
```

Se a saída corresponder ao código anterior, o seu cluster AKS está agora pronto para executar aplicações confidenciais.

Você pode ir ao Deploy Hello World a partir de uma secção [de aplicação de enclave isolada](#hello-world) neste quickstart para testar uma aplicação em um enclave. Ou use as seguintes instruções para adicionar mais piscinas de nó em AKS. (AKS suporta a mistura de piscinas de nó SGX e piscinas de nóns não SGX.)

## <a name="add-a-confidential-computing-node-pool-to-an-existing-aks-cluster"></a>Adicione um conjunto de nó de computação confidencial a um cluster AKS existente<a id="existing-cluster"></a>

Esta secção pressupõe que já está a executar um cluster AKS que satisfaz os critérios pré-requisitos listados anteriormente neste arranque rápido.

### <a name="enable-the-confidential-computing-aks-add-on-on-the-existing-cluster"></a>Ativar o addon AKS de computação confidencial no cluster existente

Executar o seguinte comando para permitir o complemento de computação confidencial:

```azurecli-interactive
az aks enable-addons --addons confcom --name MyManagedCluster --resource-group MyResourceGroup 
```

### <a name="add-a-dcsv2-user-node-pool-to-the-cluster"></a>Adicione uma piscina de nó de utilizador DCsv2 ao cluster

> [!NOTE]
> Para utilizar a capacidade de computação confidencial, o seu cluster AKS existente precisa de ter um mínimo de um conjunto de nós que é baseado num DCsv2 VM SKU. Para saber mais sobre os SKUs DCs-v2 VMs para computação confidencial, consulte os [SKUs disponíveis e as regiões apoiadas.](virtual-machine-solutions.md)

Executar o seguinte comando para criar uma piscina de nó:

```azurecli-interactive
az aks nodepool add --cluster-name myAKSCluster --name confcompool1 --resource-group myResourceGroup --node-count 1 --node-vm-size Standard_DC4s_v2
```

Verifique se o novo conjunto de nó com o nome *confcompool1* foi criado:

```azurecli-interactive
az aks nodepool list --cluster-name myAKSCluster --resource-group myResourceGroup
```

### <a name="verify-that-daemonsets-are-running-on-confidential-node-pools"></a>Verifique se os DaemonSets estão a funcionar em piscinas confidenciais de nóns

Inscreva-se no seu cluster AKS existente para realizar a seguinte verificação:

```console
kubectl get nodes
```

A saída deve mostrar a nova piscina *de confcompool1* adicionada no cluster AKS. Também pode ver outros DaemonSets.

```console
$ kubectl get pods --all-namespaces

kube-system     sgx-device-plugin-xxxx     1/1     Running
```

Se a saída corresponder ao código anterior, o seu cluster AKS está agora pronto para executar aplicações confidenciais. 

## <a name="deploy-hello-world-from-an-isolated-enclave-application"></a>Implementar Hello World a partir de uma aplicação isolada do enclave <a id="hello-world"></a>
Está pronto para lançar uma aplicação de teste. 

Crie um ficheiro chamado *hello-world-enclave.yaml* e cole no seguinte manifesto YAML. Pode encontrar este código de aplicação de amostra no [projeto Open Enclave.](https://github.com/openenclave/openenclave/tree/master/samples/helloworld) Esta implantação pressupõe que implementou o addon *confcom.*

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: sgx-test
  labels:
    app: sgx-test
spec:
  template:
    metadata:
      labels:
        app: sgx-test
    spec:
      containers:
      - name: sgxtest
        image: oeciteam/sgx-test:1.0
        resources:
          limits:
            sgx.intel.com/epc: 5Mi # This limit will automatically place the job into a confidential computing node and mount the required driver volumes. Alternatively, you can target deployment to node pools with node selector.
      restartPolicy: Never
  backoffLimit: 0
  ```

Agora use o `kubectl apply` comando para criar uma amostra de trabalho que abrirá num enclave seguro, como mostra a saída do exemplo seguinte:

```console
$ kubectl apply -f hello-world-enclave.yaml

job "sgx-test" created
```

Pode confirmar que a carga de trabalho criou com sucesso um Ambiente de Execução Fidedigna (enclave) executando os seguintes comandos:

```console
$ kubectl get jobs -l app=sgx-test

NAME       COMPLETIONS   DURATION   AGE
sgx-test   1/1           1s         23s
```

```console
$ kubectl get pods -l app=sgx-test

NAME             READY   STATUS      RESTARTS   AGE
sgx-test-rchvg   0/1     Completed   0          25s
```

```console
$ kubectl logs -l app=sgx-test

Hello world from the enclave
Enclave called into host to print: Hello World!
```

## <a name="clean-up-resources"></a>Limpar os recursos

Para remover o conjunto de nó de computação confidencial que criou neste arranque rápido, utilize o seguinte comando: 

```azurecli-interactive
az aks nodepool delete --cluster-name myAKSCluster --name confcompool1 --resource-group myResourceGroup
```

Para eliminar o cluster AKS, utilize o seguinte comando: 

```azurecli-interactive
az aks delete --resource-group myResourceGroup --cluster-name myAKSCluster
```

## <a name="next-steps"></a>Passos seguintes

* Executar Python, Nó ou outras aplicações através de recipientes confidenciais utilizando as [amostras confidenciais de contentores no GitHub](https://github.com/Azure-Samples/confidential-container-samples).

* Executar aplicações conscientes do enclave utilizando as [amostras de recipientes Azure conscientes](https://github.com/Azure-Samples/confidential-computing/blob/main/containersamples/)do enclave no GitHub .

<!-- LINKS -->
[az-group-create]: /cli/azure/group#az_group_create
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
