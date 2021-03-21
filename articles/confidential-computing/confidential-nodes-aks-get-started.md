---
title: 'Quickstart: Implementar um cluster Azure Kubernetes Service (AKS) utilizando o Azure CLI com nós de computação confidencial'
description: Neste quickstart, você aprenderá a criar um cluster AKS com nós confidenciais e implementar uma aplicação hello world usando o Azure CLI.
author: agowdamsft
ms.service: container-service
ms.topic: quickstart
ms.date: 03/18/2020
ms.author: amgowda
ms.custom: contentperf-fy21q3
ms.openlocfilehash: a7566cdb22d62bc46df82a3ef0aa78a748769531
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104657710"
---
# <a name="quickstart-deploy-an-azure-kubernetes-service-aks-cluster-with-confidential-computing-nodes-dcsv2-using-azure-cli"></a>Quickstart: Implementar um cluster Azure Kubernetes Service (AKS) com nós de computação confidencial (DCsv2) utilizando O Azure CLI

Este quickstart destina-se a desenvolvedores ou operadores de cluster que pretendam criar rapidamente um cluster AKS e implementar uma aplicação para monitorizar aplicações usando o serviço gerido kubernetes em Azure. Também pode providenciar o cluster e adicionar nós de computação confidencial do portal Azure.

## <a name="overview"></a>Descrição Geral

Neste quickstart, você vai aprender a implementar um cluster Azure Kubernetes Service (AKS) com nós de computação confidencial usando o Azure CLI e executar uma aplicação simples hello world em um enclave. AKS é um serviço gerido pela Kubernetes que permite implementar e gerir rapidamente clusters. Para saber mais, leia a Introdução da [AKS](../aks/intro-kubernetes.md) e a [Visão Geral dos Nós Confidenciais da AKS](confidential-nodes-aks-overview.md).

> [!NOTE]
> Os DCsv2 VMs de computação confidencial alavancam hardware especializado que está sujeito a preços mais elevados e disponibilidade de região. Para obter mais informações, consulte a página de máquinas virtuais para [SKUs disponíveis e regiões apoiadas.](virtual-machine-solutions.md)

### <a name="confidential-computing-node-features-dcsv2"></a>Características confidenciais do nó de computação (DCsv2)

1. Nódores de Trabalhadores Linux que suportam contentores Linux.
1. Geração 2 VM com Ubuntu 18.04 Nóns de Máquinas Virtuais.
1. CPU baseado em Intel SGX com memória de cache de página encriptada (EPC). Leia mais [aqui](./faq.md).
1. Suporte para a versão 1.16+.
1. Intel SGX DCAP Driver pré-instalado nos nós AKS. Leia mais [aqui](./faq.md).

## <a name="prerequisites"></a>Pré-requisitos

Este início rápido requer:

1. Uma subscrição ativa do Azure. Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.
1. Versão Azure CLI 2.0.64 ou posteriormente instalada e configurada na sua máquina de implantação (Corra `az --version` para encontrar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)](../container-registry/container-registry-get-started-azure-cli.md).
1. Um mínimo de seis **núcleos DCsv2** disponíveis na sua subscrição para uso. Por predefinição, a quota de núcleos VM para a computação confidencial por subscrição Azure é de oito núcleos. Se planeia providenciar um cluster que requer mais de oito núcleos, siga [estas](../azure-portal/supportability/per-vm-quota-requests.md) instruções para aumentar a quota.

## <a name="create-a-new-aks-cluster-with-confidential-computing-nodes-and-add-on"></a>Criar um novo cluster AKS com nódoas de computação confidencial e add-on

Siga as instruções abaixo para adicionar nós com capacidade de computação confidencial com suplemento.

### <a name="create-an-aks-cluster-with-a-system-node-pool"></a>Criar um cluster AKS com uma piscina de nó de sistema

Se já tem um cluster AKS que satisfaz os requisitos acima, [salte para a secção de cluster existente](#existing-cluster) para adicionar um novo conjunto de nós de computação confidencial.

Em primeiro lugar, crie um grupo de recursos para o cluster utilizando o [grupo az criar][az-group-create] comando. O exemplo a seguir cria um nome de grupo de recursos *myResourceGroup* na região *oeste2:*

```azurecli-interactive
az group create --name myResourceGroup --location westus2
```

Agora crie um cluster AKS usando os [az aks criar][az-aks-create] comando:

```azurecli-interactive
az aks create -g myResourceGroup --name myAKSCluster --generate-ssh-keys --enable-addon confcom
```

O acima cria um novo cluster AKS com um conjunto de nós de sistema com o add-on ativado. Em seguida, adicione um conjunto de nós com capacidades de computação confidenciais ao cluster AKS.

### <a name="add-a-confidential-computing-node-pool-to-the-aks-cluster"></a>Adicione uma piscina de nó de computação confidencial ao cluster AKS 

Executar o seguinte comando para adicionar uma piscina de nó de utilizador de `Standard_DC2s_v2` tamanho com três nós. Pode escolher outro SKU da lista suportada de [SKUs e regiões do DCsv2.](../virtual-machines/dcv2-series.md)

```azurecli-interactive
az aks nodepool add --cluster-name myAKSCluster --name confcompool1 --resource-group myResourceGroup --node-vm-size Standard_DC2s_v2
```

Depois de correr, um novo conjunto de nó com **DCsv2** deve ser visível com daemonsets adicionais de computação confidenciais [(Plugin de dispositivo SGX](confidential-nodes-aks-overview.md#sgx-plugin)).

### <a name="verify-the-node-pool-and-add-on"></a>Verifique a piscina do nó e o add-on

Obtenha as credenciais para o seu cluster AKS usando o comando [az aks get-credentials:][az-aks-get-credentials]

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Verifique se os nóles são criados corretamente e os daemonsets relacionados com a SGX estão a funcionar em piscinas de nó de **DCsv2** utilizando cápsulas kubectl get & comando dos nosdes como mostrado abaixo:

```console
$ kubectl get pods --all-namespaces

kube-system     sgx-device-plugin-xxxx     1/1     Running
```

Se a saída corresponder ao acima, então o seu cluster AKS está agora pronto para executar aplicações confidenciais.

Vá à secção [hello world da](#hello-world) secção de implantação do Enclave para testar uma aplicação num enclave. Ou siga as instruções abaixo para adicionar piscinas de nó adicionais em AKS (AKS suporta misturar piscinas de nó SGX e piscinas de nó não SGX).

## <a name="add-a-confidential-computing-node-pool-to-an-existing-aks-cluster"></a>Adicione um conjunto de nó de computação confidencial a um cluster AKS existente<a id="existing-cluster"></a>

Esta secção pressupõe que já tem um cluster AKS em funcionamento que satisfaz os critérios enumerados na secção pré-requisitos (aplica-se ao add-on).

### <a name="enable-the-confidential-computing-aks-add-on-on-the-existing-cluster"></a>Ativar o addon AKS de computação confidencial no cluster existente

Executar o seguinte comando para permitir o complemento de computação confidencial:

```azurecli-interactive
az aks enable-addons --addons confcom --name MyManagedCluster --resource-group MyResourceGroup 
```

### <a name="add-a-dcsv2-user-node-pool-to-the-cluster"></a>Adicione uma piscina de nó de utilizador **DCsv2** ao cluster

> [!NOTE]
> Para utilizar a capacidade de computação confidencial, o seu cluster AKS existente precisa de ter no mínimo um conjunto de nós baseado em **DCsv2** VM SKU. Para saber mais sobre a computação confidencial DCs-v2 VMs SKU's, consulte [SKUs disponíveis e regiões apoiadas.](virtual-machine-solutions.md)

Executar o seguinte comando para criar uma nova piscina de nó:

```azurecli-interactive
az aks nodepool add --cluster-name myAKSCluster --name confcompool1 --resource-group myResourceGroup --node-count 1 --node-vm-size Standard_DC4s_v2
```

Verifique se o novo conjunto de nó com o nome confcompool1 foi criado:

```azurecli-interactive
az aks nodepool list --cluster-name myAKSCluster --resource-group myResourceGroup
```

### <a name="verify-that-daemonsets-are-running-on-confidential-node-pools"></a>Verifique se os daemonsets estão a funcionar em piscinas confidenciais de nó

Inscreva-se no cluster AKS existente para realizar a seguinte verificação.

```console
kubectl get nodes
```

A saída deve mostrar o novo confcompool1 adicionado no cluster AKS. Também pode ver outros daemonsets.

```console
$ kubectl get pods --all-namespaces

kube-system     sgx-device-plugin-xxxx     1/1     Running
```

Se a saída corresponder ao acima, então o seu cluster AKS está agora pronto para executar aplicações confidenciais. Siga as instruções abaixo para colocar uma aplicação de teste.

## <a name="hello-world-from-isolated-enclave-application"></a>Olá Mundo da aplicação isolada do enclave <a id="hello-world"></a>
Crie um ficheiro chamado *hello-world-enclave.yaml* e cole o seguinte manifesto YAML. Este código de aplicação de amostra baseado em Enclave aberto pode ser encontrado no [projeto Open Enclave](https://github.com/openenclave/openenclave/tree/master/samples/helloworld). A seguinte implantação pressupõe que tenha implantado o addon "confcom".

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
            kubernetes.azure.com/sgx_epc_mem_in_MiB: 5 # This limit will automatically place the job into confidential computing node. Alternatively you can target deployment to nodepools
      restartPolicy: Never
  backoffLimit: 0
  ```

Agora use o comando de aplicação de kubectl para criar uma amostra que será lançada num enclave seguro, como mostra a saída do exemplo seguinte:

```console
$ kubectl apply -f hello-world-enclave.yaml

job "sgx-test" created
```

Pode confirmar que a carga de trabalho criou com sucesso um Ambiente de Execução Fidedigna (Enclave) executando os seguintes comandos:

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

Para remover as piscinas de nó associados ou eliminar o cluster AKS, utilize os seguintes comandos:

### <a name="remove-the-confidential-computing-node-pool"></a>Remova a piscina confidencial do nó de computação

```azurecli-interactive
az aks nodepool delete --cluster-name myAKSCluster --name myNodePoolName --resource-group myResourceGroup
```

### <a name="delete-the-aks-cluster"></a>Eliminar o cluster AKS

```azurecli-interactive
az aks delete --resource-group myResourceGroup --name myAKSCluster
```

## <a name="next-steps"></a>Passos seguintes

* Executar Python, Nó, etc. aplicações confidencialmente através de contentores confidenciais, visitando [amostras confidenciais de contentores.](https://github.com/Azure-Samples/confidential-container-samples)

* Executar aplicações de conhecimento do Enclave visitando [as amostras do contentor do Azure do Enclave Aware.](https://github.com/Azure-Samples/confidential-computing/blob/main/containersamples/)

<!-- LINKS -->
[az-group-create]: /cli/azure/group#az_group_create
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials