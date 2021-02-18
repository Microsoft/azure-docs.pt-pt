---
title: 'Quickstart: Implementar um cluster Azure Kubernetes Service (AKS) utilizando o Azure CLI com nós de computação confidencial'
description: Aprenda a criar um cluster AKS com nós confidenciais e implemente uma aplicação hello world usando o Azure CLI.
author: agowdamsft
ms.service: container-service
ms.topic: quickstart
ms.date: 2/8/2020
ms.author: amgowda
ms.openlocfilehash: 866c8340cf9c16d768f4035326aa2ec52dbf1401
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/18/2021
ms.locfileid: "100653368"
---
# <a name="quickstart-deploy-an-azure-kubernetes-service-aks-cluster-with-confidential-computing-nodes-dcsv2-using-azure-cli"></a>Quickstart: Implementar um cluster Azure Kubernetes Service (AKS) com nós de computação confidencial (DCsv2) utilizando O Azure CLI

Este quickstart destina-se a desenvolvedores ou operadores de cluster que pretendam criar rapidamente um cluster AKS e implementar uma aplicação para monitorizar aplicações usando o serviço gerido kubernetes em Azure. Também pode providenciar o cluster e adicionar nós de computação confidencial do portal Azure.

## <a name="overview"></a>Descrição Geral

Neste quickstart, você vai aprender a implementar um cluster Azure Kubernetes Service (AKS) com nós de computação confidencial usando o Azure CLI e executar uma aplicação simples hello world em um enclave. AKS é um serviço gerido pela Kubernetes que permite implementar e gerir rapidamente clusters. Leia mais sobre a AKS [aqui.](../aks/intro-kubernetes.md)

> [!NOTE]
> Os DCsv2 VMs de computação confidencial alavancam hardware especializado que está sujeito a preços mais elevados e disponibilidade de região. Para obter mais informações, consulte a página de máquinas virtuais para [SKUs disponíveis e regiões apoiadas.](virtual-machine-solutions.md)

### <a name="confidential-computing-node-features-dcxs-v2"></a>Características confidenciais do nó de computação (DC <x> s-v2)

1. Nóiro operário Linux suportando apenas contentores Linux
1. Geração 2 VM com Ubuntu 18.04 Nó de Máquinas Virtuais
1. CPU baseado em Intel SGX com memória de cache de página encriptada (EPC). Leia mais [aqui](./faq.md)
1. Suportando a versão 1.16+ de Kubernetes
1. Controlador Intel SGX DCAP Pré-instalado nos nós AKS. Leia mais [aqui](./faq.md)

## <a name="deployment-prerequisites"></a>Pré-requisitos da implementação
O tutorial de implantação requer o seguinte:

1. Uma subscrição ativa do Azure. Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar
1. Versão Azure CLI 2.0.64 ou posteriormente instalada e configurada na sua máquina de implantação (Corra `az --version` para encontrar a versão. Se precisar de instalar ou atualizar, consulte [instalar o Azure CLI](../container-registry/container-registry-get-started-azure-cli.md)
1. Azure [aks-preview versão](https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview) mínima 0.5.0
1. No mínimo, seis núcleos **<x> DC s-v2** disponíveis na sua subscrição para utilização. Por predefinição, a quota de núcleos VM para a computação confidencial por assinatura Azure 8 núcleos. Se planeia providenciar um cluster que exija mais de 8 núcleos, siga [estas](../azure-portal/supportability/per-vm-quota-requests.md) instruções para aumentar o bilhete de aumento de quota

## <a name="cli-based-preparation-steps-required-for-add-on-in-preview---optional-but-recommended"></a>Etapas de preparação baseadas em CLI (necessárias para o addon em pré-visualização - opcional, mas recomendada)
Siga as instruções abaixo para ativar o addon de computação confidencial em AKS.

### <a name="step-1-installing-the-cli-prerequisites"></a>Passo 1: Instalação dos pré-requisitos do CLI

Para instalar a extensão aks-preview 0.5.0 ou posterior, utilize os seguintes comandos Azure CLI:

```azurecli-interactive
az extension add --name aks-preview
az extension list
```
Para atualizar a extensão CLI de pré-visualização aks, utilize os seguintes comandos Azure CLI:

```azurecli-interactive
az extension update --name aks-preview
```
### <a name="step-2-azure-confidential-computing-addon-feature-registration-on-azure"></a>Passo 2: Registo de recurso de addon de computação confidencial Azure no Azure
Registar o AKS-ConfidentialComputingAddon na Assinatura Azure. Esta funcionalidade irá adicionar o plugin do dispositivo SGX, como discutido em detalhes [aqui:](./confidential-nodes-aks-overview.md#confidential-computing-add-on-for-aks)
1. Plugin do controlador de dispositivo SGX
```azurecli-interactive
az feature register --name AKS-ConfidentialComputingAddon --namespace Microsoft.ContainerService
```
Pode levar vários minutos para que o estado apareça como Registado. Pode verificar o estado de registo utilizando o comando 'az feature list'. Esta inscrição de funcionalidade é feita apenas uma vez por subscrição. Se este foi registado anteriormente, pode saltar o passo acima:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKS-ConfidentialComputingAddon')].{Name:name,State:properties.state}"
```
Quando o estado aparecer como registado, reaprote o registo do fornecedor de recursos Microsoft.ContainerService utilizando o comando "registo do fornecedor az":

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```
## <a name="creating-new-aks-cluster-with-confidential-computing-nodes-and-add-on"></a>Criação de novo cluster AKS com nódoas de computação confidencial e add-on
Siga as instruções abaixo para adicionar nós com capacidade de computação confidencial com suplemento.

### <a name="step-1-creating-an-aks-cluster-with-system-node-pool"></a>Passo 1: Criação de um cluster AKS com piscina de nó de sistema

Se já tem um cluster AKS que satisfaz os requisitos acima, [salte para a secção de cluster existente](#existing-cluster) para adicionar um novo conjunto de nós de computação confidencial.

Em primeiro lugar, crie um grupo de recursos para o cluster utilizando o grupo az criar comando. O exemplo a seguir cria um nome de grupo de recursos *myResourceGroup* na região *oeste2:*

```azurecli-interactive
az group create --name myResourceGroup --location westus2
```

Agora crie um cluster AKS usando os az aks criar comando.

```azurecli-interactive
# Create a new AKS cluster with system node pool with Confidential Computing addon enabled
az aks create -g myResourceGroup --name myAKSCluster --generate-ssh-keys --enable-addon confcom
```
O acima cria um novo cluster AKS com piscina de nó de sistema com o add-on ativado. Agora prossiga a adição de um nó de utilizador do tipo Nodepool de Computação Confidencial em AKS (DCsv2)

### <a name="step-2-adding-confidential-computing-node-pool-to-aks-cluster"></a>Passo 2: Adicionar piscina de nó de computação confidencial ao cluster AKS 

Executar o comando abaixo para um nodepool do utilizador de `Standard_DC2s_v2` tamanho com 3 nós. Pode escolher outra lista suportada de SKUs e regiões do DCsv2 a partir [daqui:](../virtual-machines/dcv2-series.md)

```azurecli-interactive
az aks nodepool add --cluster-name myAKSCluster --name confcompool1 --resource-group myResourceGroup --node-vm-size Standard_DC2s_v2
```
O comando acima é completo um novo conjunto de nó com **DC <x> s-v2** deve ser visível com daemonsets adicionais de computação confidencial [(Plugin de dispositivo SGX](confidential-nodes-aks-overview.md#sgx-plugin)
 
### <a name="step-3-verify-the-node-pool-and-add-on"></a>Passo 3: Verifique a piscina do nó e o add-on
Obtenha as credenciais para o seu cluster AKS usando o comando az aks get-credentials:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```
Verifique se os nóles são criados corretamente e os daemonsets relacionados com o SGX estão a funcionar em piscinas de nó **<x> s-v2 DC usando** cápsulas kubectl get & comando dos nosdes como mostrado abaixo:

```console
$ kubectl get pods --all-namespaces

output
kube-system     sgx-device-plugin-xxxx     1/1     Running
```
Se a saída corresponder ao acima, então o seu cluster AKS está agora pronto para executar aplicações confidenciais.

Vá à [secção hello world da](#hello-world) secção de implantação do Enclave para testar uma aplicação num enclave. Ou siga as instruções abaixo para adicionar piscinas de nó adicionais em AKS (AKS suporta misturar piscinas de nó SGX e piscinas de nó não SGX)

## <a name="adding-confidential-computing-node-pool-to-existing-aks-cluster"></a>Adicionar piscina de nó de computação confidencial ao cluster AKS existente<a id="existing-cluster"></a>

Esta secção pressupõe que já tem um cluster AKS em funcionamento que satisfaz os critérios enumerados na secção pré-requisitos (aplica-se ao add-on).

### <a name="step-1-enabling-the-confidential-computing-aks-add-on-on-the-existing-cluster"></a>Passo 1: Permitir o complemento aKS de computação confidencial no cluster existente

Executar o comando abaixo para ativar o add-on de computação confidencial

```azurecli-interactive
az aks enable-addons --addons confcom --name MyManagedCluster --resource-group MyResourceGroup 
```
### <a name="step-2-add-dcxs-v2-user-node-pool-to-the-cluster"></a>Passo 2: Adicione a piscina do nó do utilizador **DC <x> s-v2** ao cluster
    
> [!NOTE]
> Para utilizar a capacidade de computação confidencial, o seu cluster AKS existente precisa de ter no mínimo um conjunto de nós baseado em **DC <x> s-v2** VM SKU. Saiba mais sobre a computação confidencial DCsv2 VMs SKU's aqui [disponível SKUs e regiões apoiadas.](virtual-machine-solutions.md)
    
  ```azurecli-interactive
az aks nodepool add --cluster-name myAKSCluster --name confcompool1 --resource-group myResourceGroup --node-count 1 --node-vm-size Standard_DC4s_v2

output node pool added

Verify

az aks nodepool list --cluster-name myAKSCluster --resource-group myResourceGroup
```
o comando acima deve listar a piscina de nó recente que adicionou com o nome confcompool1.

### <a name="step-3-verify-that-daemonsets-are-running-on-confidential-node-pools"></a>Passo 3: Verifique se os daemonsets estão a funcionar em piscinas confidenciais de nó

Faça login no cluster AKS existente para realizar a verificação abaixo. 

```console
kubectl get nodes
```
A saída deve mostrar o novo confcompool1 adicionado no cluster AKS.

```console
$ kubectl get pods --all-namespaces

output (you may also see other daemonsets along SGX daemonsets as below)
kube-system     sgx-device-plugin-xxxx     1/1     Running
```
Se a saída corresponder ao acima, então o seu cluster AKS está agora pronto para executar aplicações confidenciais. Por favor, siga a implementação da aplicação de teste abaixo.

## <a name="hello-world-from-isolated-enclave-application"></a>Olá Mundo da aplicação isolada do enclave <a id="hello-world"></a>
Crie um ficheiro chamado *hello-world-enclave.yaml* e cole o seguinte manifesto YAML. Este código de aplicação de amostra baseado em Enclave aberto pode ser encontrado no [projeto Open Enclave](https://github.com/openenclave/openenclave/tree/master/samples/helloworld). A colocação abaixo pressupõe que implementou o addon "confcom".

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
```

```console
$ kubectl get jobs -l app=sgx-test
NAME       COMPLETIONS   DURATION   AGE
sgx-test   1/1           1s         23s
```

```console
$ kubectl get pods -l app=sgx-test
```

```console
$ kubectl get pods -l app=sgx-test
NAME             READY   STATUS      RESTARTS   AGE
sgx-test-rchvg   0/1     Completed   0          25s
```

```console
$ kubectl logs -l app=sgx-test
```

```console
$ kubectl logs -l app=sgx-test
Hello world from the enclave
Enclave called into host to print: Hello World!
```

## <a name="clean-up-resources"></a>Limpar os recursos

Para remover as piscinas de nó associados ou eliminar o cluster AKS, utilize os comandos abaixo:

Apagar o cluster AKS
``````azurecli-interactive
az aks delete --resource-group myResourceGroup --name myAKSCluster
```
Removing the confidential computing node pool

``````azurecli-interactive
az aks nodepool delete --cluster-name myAKSCluster --name myNodePoolName --resource-group myResourceGroup
``````

## <a name="next-steps"></a>Passos seguintes

Executar Python, Nó, etc. Aplicações confidencialmente através de contentores confidenciais visitando [amostras confidenciais de contentores.](https://github.com/Azure-Samples/confidential-container-samples)

Executar aplicações de conhecimento do Enclave visitando [as amostras do contentor do Azure do Enclave Aware.](https://github.com/Azure-Samples/confidential-computing/blob/main/containersamples/)
