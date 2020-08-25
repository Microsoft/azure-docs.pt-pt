---
title: Configuração do cluster nos Serviços Azure Kubernetes (AKS)
description: Saiba como configurar um cluster no Serviço Azure Kubernetes (AKS)
services: container-service
ms.topic: conceptual
ms.date: 08/06/2020
ms.author: jpalma
author: palma21
ms.openlocfilehash: daffcbf0a2ceb6f28cbb539906d4c6387840aa20
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/22/2020
ms.locfileid: "88752094"
---
# <a name="configure-an-aks-cluster"></a>Configurar um cluster do AKS

Como parte da criação de um cluster AKS, poderá necessitar de personalizar a sua configuração de cluster de acordo com as suas necessidades. Este artigo introduz algumas opções para personalizar o seu cluster AKS.

## <a name="os-configuration-preview"></a>Configuração do SO (Pré-visualização)

AKS suporta agora ubuntu 18.04 como o sistema operativo do nó (OS) na pré-visualização. Durante o período de pré-visualização, tanto Ubuntu 16.04 como Ubuntu 18.04 estão disponíveis.

> [!IMPORTANT]
> Piscinas de nó criadas em Kubernetes v1.18 ou maior padrão para uma `AKS Ubuntu 18.04` imagem de nó requerida. Os agrupamentos de nó numa versão suportada de Kubernetes menos de 1.18 recebem `AKS Ubuntu 16.04` como imagem de nó, mas serão atualizados assim `AKS Ubuntu 18.04` que a versão do grupo de nó kubernetes for atualizada para v1.18 ou maior.
> 
> É altamente recomendado testar as suas cargas de trabalho em piscinas de nó AKS Ubuntu 18.04 antes de utilizar clusters em 1.18 ou superior. Leia sobre como [testar piscinas de nó Ubuntu 18.04](#use-aks-ubuntu-1804-existing-clusters-preview).

Deve ter os seguintes recursos instalados:

- [O Azure CLI][azure-cli-install], versão 2.2.0 ou mais tarde
- A extensão aks-preview 0.4.35

Para instalar a extensão aks-preview 0.4.35 ou posterior, utilize os seguintes comandos Azure CLI:

```azurecli
az extension add --name aks-preview
az extension list
```

Registar a `UseCustomizedUbuntuPreview` função:

```azurecli
az feature register --name UseCustomizedUbuntuPreview --namespace Microsoft.ContainerService
```

Pode levar vários minutos para que o estado seja apresentado como **Registado**. Pode verificar o estado de registo utilizando o comando [da lista de funcionalidades AZ:](/cli/azure/feature?view=azure-cli-latest#az-feature-list)

```azurecli
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/UseCustomizedUbuntuPreview')].{Name:name,State:properties.state}"
```

Quando o estado aparecer como registado, reaprovida o registo do fornecedor de `Microsoft.ContainerService` recursos utilizando o comando de registo do fornecedor [az:](/cli/azure/provider?view=azure-cli-latest#az-provider-register)

```azurecli
az provider register --namespace Microsoft.ContainerService
```

### <a name="use-aks-ubuntu-1804-on-new-clusters-preview"></a>Utilize AKS Ubuntu 18.04 em novos clusters (Pré-visualização)

Configure o cluster para usar Ubuntu 18.04 quando o cluster é criado. Utilize a `--aks-custom-headers` bandeira para definir o Ubuntu 18.04 como o SISTEMA padrão.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --aks-custom-headers CustomizedUbuntu=aks-ubuntu-1804
```

Se quiser criar clusters com a imagem AKS Ubuntu 16.04, pode fazê-lo omitindo a `--aks-custom-headers` etiqueta personalizada.

### <a name="use-aks-ubuntu-1804-existing-clusters-preview"></a>Utilize clusters AKS Ubuntu 18.04 existentes (Pré-visualização)

Configure uma nova piscina de nó para usar Ubuntu 18.04. Utilize a `--aks-custom-headers` bandeira para definir o Ubuntu 18.04 como o SISTEMA padrão para a piscina de nó.

```azurecli
az aks nodepool add --name ubuntu1804 --cluster-name myAKSCluster --resource-group myResourceGroup --aks-custom-headers CustomizedUbuntu=aks-ubuntu-1804
```

Se quiser criar piscinas de nó com a imagem AKS Ubuntu 16.04, pode fazê-lo omitindo a `--aks-custom-headers` etiqueta personalizada.


## <a name="container-runtime-configuration-preview"></a>Configuração do tempo de execução do contentor (pré-visualização)

Um tempo de funcionação do contentor é um software que executa contentores e gere imagens de contentores num nó. O tempo de execução ajuda a abstrato de chamadas de sys ou funcionalidade específica do sistema operativo (OS) para executar contentores em Linux ou Windows. Hoje a AKS está a usar [o Moby](https://mobyproject.org/) (estivador a montante) como tempo de funcionação do contentor. 
    
![Docker CRI 1](media/cluster-configuration/docker-cri.png)

[`Containerd`](https://containerd.io/) é um tempo de funcionamento do recipiente de núcleo compatível com [OCI](https://opencontainers.org/) (Open Container Initiative) que fornece o conjunto mínimo de funcionalidade necessária para executar contentores e gerir imagens num nó. Foi [doado](https://www.cncf.io/announcement/2017/03/29/containerd-joins-cloud-native-computing-foundation/) à Cloud Native Compute Foundation (CNCF) em março de 2017. A versão atual da Moby que a AKS utiliza hoje já aproveita e é construída em cima de `containerd` , como mostrado acima. 

Com um nó à base de contentores e piscinas de nó, em vez de falar com o `dockershim` , o kubelet falará diretamente `containerd` através do plugin CRI (interface de tempo de execução do contentor), removendo saltos extra no fluxo quando comparado com a implementação do Docker CRI. Como tal, verá uma melhor latência de arranque de pod e menos utilização de recursos (CPU e memória).

Ao utilizar `containerd` os nós AKS, a latência do arranque do pod melhora e o consumo de recursos de nó pelo tempo de funcionaamento do recipiente diminui. Estas melhorias são possibilitada por esta nova arquitetura onde kubelet fala diretamente `containerd` através do plugin CRI enquanto em Moby/Docker arquitetura kubelet falaria com o `dockershim` motor e estivador antes `containerd` de alcançar, tendo assim saltos extra no fluxo.

![Docker CRI 2](media/cluster-configuration/containerd-cri.png)

`Containerd` funciona em todas as versões GA de kubernetes em AKS, e em todas as versões de kubernetes acima acima de v1.10, e suporta todas as funcionalidades de kubernetes e AKS.

> [!IMPORTANT]
> Depois de `containerd` ficar geralmente disponível em AKS, será a opção padrão e única disponível para o tempo de funcionação do contentor em novos clusters. Você ainda pode usar nodepools e clusters Moby em versões suportadas mais antigas até que estes caiam fora de suporte. 
> 
> Recomendamos que teste as suas cargas de trabalho em `containerd` piscinas de nó antes de atualizar ou criar novos aglomerados com este tempo de funcionamento do recipiente.

### <a name="use-containerd-as-your-container-runtime-preview"></a>Utilizar `containerd` como tempo de funcionação do seu recipiente (Pré-visualização)

Deve ter os seguintes pré-requisitos:

- [O Azure CLI][azure-cli-install], versão 2.8.0 ou posteriormente instalado
- A versão de extensão de pré-visualização aks 0.4.53 ou mais tarde
- A `UseCustomizedContainerRuntime` bandeira de recurso registada
- A `UseCustomizedUbuntuPreview` bandeira de recurso registada

Para instalar a extensão aks-preview 0.4.53 ou posterior, utilize os seguintes comandos Azure CLI:

```azurecli
az extension add --name aks-preview
az extension list
```

Registar as `UseCustomizedContainerRuntime` `UseCustomizedUbuntuPreview` características:

```azurecli
az feature register --name UseCustomizedContainerRuntime --namespace Microsoft.ContainerService
az feature register --name UseCustomizedUbuntuPreview --namespace Microsoft.ContainerService

```

Pode levar vários minutos para que o estado seja apresentado como **Registado**. Pode verificar o estado de registo utilizando o comando [da lista de funcionalidades AZ:](/cli/azure/feature?view=azure-cli-latest#az-feature-list)

```azurecli
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/UseCustomizedContainerRuntime')].{Name:name,State:properties.state}"
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/UseCustomizedUbuntuPreview')].{Name:name,State:properties.state}"
```

Quando o estado aparecer como registado, reaprovida o registo do fornecedor de `Microsoft.ContainerService` recursos utilizando o comando de registo do fornecedor [az:](/cli/azure/provider?view=azure-cli-latest#az-provider-register)

```azurecli
az provider register --namespace Microsoft.ContainerService
```  

### <a name="use-containerd-on-new-clusters-preview"></a>Utilização `containerd` em novos clusters (Pré-visualização)

Configure o cluster para usar `containerd` quando o cluster for criado. Utilize a `--aks-custom-headers` bandeira para definir como tempo de `containerd` funcionação do recipiente.

> [!NOTE]
> O `containerd` tempo de funcionação só é suportado em nós e piscinas de nó usando a imagem AKS Ubuntu 18.04.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --aks-custom-headers CustomizedUbuntu=aks-ubuntu-1804,ContainerRuntime=containerd
```

Se quiser criar clusters com o tempo de execução Moby (docker), pode fazê-lo omitindo a `--aks-custom-headers` etiqueta personalizada.

### <a name="use-containerd-on-existing-clusters-preview"></a>Utilização `containerd` em clusters existentes (Pré-visualização)

Configure uma nova piscina de nós para usar `containerd` . Use a `--aks-custom-headers` bandeira para definir como tempo de `containerd` execução para a piscina de nós.

```azurecli
az aks nodepool add --name ubuntu1804 --cluster-name myAKSCluster --resource-group myResourceGroup --aks-custom-headers CustomizedUbuntu=aks-ubuntu-1804,ContainerRuntime=containerd
```

Se quiser criar piscinas de nó com o tempo de execução Moby (docker), pode fazê-lo omitindo a `--aks-custom-headers` etiqueta personalizada.


### <a name="containerd-limitationsdifferences"></a>`Containerd` limitações/diferenças

* Para utilizar `containerd` como tempo de funcionação do recipiente, deve utilizar o AKS Ubuntu 18.04 como imagem base do SO.
* Enquanto o estivador de ferramentas ainda está presente nos nós, Kubernetes usa `containerd` como tempo de funcionação do recipiente. Portanto, uma vez que a Moby/Docker não gere os recipientes criados pela Kubernetes nos nós, não é possível ver ou interagir com os seus contentores usando comandos Docker `docker ps` (como) ou a API do Docker.
* Para `containerd` , recomendamos a utilização [`crictl`](https://kubernetes.io/docs/tasks/debug-application-cluster/crictl) como CLI de substituição em vez do CLI do Docker para **resolução de problemas** de cápsulas, contentores e imagens de contentores nos nós kubernetes (por exemplo, `crictl ps` ). 
   * Não fornece a funcionalidade completa do Estivador CLI. Destina-se apenas a resolver problemas.
   * `crictl` oferece uma visão mais amiga dos kubernetes dos recipientes, com conceitos como pods, etc. estar presentes.
* `Containerd` configura o registo utilizando o formato de registo normalizado `cri` (que é diferente do que obtém atualmente do condutor json do Docker). A sua solução de registo precisa de suportar o `cri` formato de registo (como o [Azure Monitor for Containers)](../azure-monitor/insights/container-insights-enable-new-cluster.md)
* Já não pode aceder ao motor do estivador, `/var/run/docker.sock` ou utilizar o Docker-in-Docker (DinD).
  * Se atualmente extrair registos de aplicações ou dados de monitorização do Docker Engine, utilize algo como [Azure Monitor para contentores.](../azure-monitor/insights/container-insights-enable-new-cluster.md) Além disso, a AKS não suporta a execução de nenhum comando de banda nos nós do agente que possam causar instabilidade.
  * Mesmo quando se usa Moby/docker, construir imagens e alavancar diretamente o motor do estivador através dos métodos acima é fortemente desencorajado. Kubernetes não está totalmente ciente desses recursos consumidos, e essas abordagens apresentam numerosas questões detalhadas [aqui](https://jpetazzo.github.io/2015/09/03/do-not-use-docker-in-docker-for-ci/) e [aqui](https://securityboulevard.com/2018/05/escaping-the-whale-things-you-probably-shouldnt-do-with-docker-part-1/), por exemplo.
* Imagens de construção - A abordagem recomendada para a construção de imagens é utilizar [tarefas ACR](../container-registry/container-registry-quickstart-task-cli.md). Uma abordagem alternativa é utilizar opções mais seguras no cluster, como [o estivador buildx.](https://github.com/docker/buildx)

## <a name="generation-2-virtual-machines-preview"></a>Máquinas virtuais de geração 2 (Pré-visualização)

A Azure suporta [máquinas virtuais geração 2 (Gen2) (VMs)](../virtual-machines/windows/generation-2.md). A geração 2 VMs suporta características-chave que não são suportadas na geração 1 VMs (Gen1). Estas funcionalidades incluem memória aumentada, extensões de guarda de software Intel (Intel SGX) e memória persistente virtualizada (vPMEM).

A geração 2 VMs usa a nova arquitetura de arranque baseada na UEFI em vez da arquitetura baseada em BIOS usada pela geração 1 VMs.
Apenas SKUs e tamanhos específicos suportam Os VMs da Gen2. Verifique a [lista de tamanhos suportados,](../virtual-machines/windows/generation-2.md#generation-2-vm-sizes)para ver se o seu SKU suporta ou requer Gen2.

Além disso, nem todas as imagens VM suportam a Gen2, em VMs AKS Gen2 utilizarão a nova [imagem AKS Ubuntu 18.04](#os-configuration-preview). Esta imagem suporta todos os SKUs e tamanhos da Gen2.

Para utilizar os VMs da Gen2 durante a pré-visualização, você vai precisar:
- A `aks-preview` extensão CLI instalada.
- A `Gen2VMPreview` bandeira de características registada.

Registar a `Gen2VMPreview` função:

```azurecli
az feature register --name Gen2VMPreview --namespace Microsoft.ContainerService
```

Pode levar vários minutos para que o estado seja apresentado como **Registado**. Pode verificar o estado de registo utilizando o comando [da lista de funcionalidades AZ:](/cli/azure/feature?view=azure-cli-latest#az-feature-list)

```azurecli
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/Gen2VMPreview')].{Name:name,State:properties.state}"
```

Quando o estado aparecer como registado, reaprovida o registo do fornecedor de `Microsoft.ContainerService` recursos utilizando o comando de registo do fornecedor [az:](/cli/azure/provider?view=azure-cli-latest#az-provider-register)

```azurecli
az provider register --namespace Microsoft.ContainerService
```

Para instalar a extensão CLI de pré-visualização aks, utilize os seguintes comandos Azure CLI:

```azurecli
az extension add --name aks-preview
```

Para atualizar a extensão CLI de pré-visualização aks, utilize os seguintes comandos Azure CLI:

```azurecli
az extension update --name aks-preview
```

### <a name="use-gen2-vms-on-new-clusters-preview"></a>Utilize VMs Gen2 em novos clusters (Pré-visualização)
Configure o cluster para usar Os VMs da Gen2 para o SKU selecionado quando o cluster é criado. Use a `--aks-custom-headers` bandeira para definir a Gen2 como a geração VM num novo aglomerado.

```azure-cli
az aks create --name myAKSCluster --resource-group myResourceGroup -s Standard_D2s_v3 --aks-custom-headers usegen2vm=true
```

Se quiser criar um cluster regular utilizando VMs da Geração 1 (Gen1), pode fazê-lo omitindo a `--aks-custom-headers` etiqueta personalizada. Também pode optar por adicionar mais Gen1 ou Gen2 VMs conforme abaixo.

### <a name="use-gen2-vms-on-existing-clusters-preview"></a>Utilizar VMs Gen2 em clusters existentes (Pré-visualização)
Configure uma nova piscina de nós para usar Os VMs da Gen2. Use a `--aks-custom-headers` bandeira para definir a Gen2 como a geração VM para a piscina de nós.

```azure-cli
az aks nodepool add --name gen2 --cluster-name myAKSCluster --resource-group myResourceGroup -s Standard_D2s_v3 --aks-custom-headers usegen2vm=true
```

Se quiser criar piscinas regulares de nó gen1, pode fazê-lo omitindo a `--aks-custom-headers` etiqueta personalizada.


## <a name="ephemeral-os-preview"></a>OS Efémeros (Pré-visualização)

Por predefinição, o disco do sistema operativo de uma máquina virtual Azure é automaticamente replicado para o armazenamento do Azure para evitar a perda de dados caso o VM precise de ser transferido para outro hospedeiro. No entanto, uma vez que os contentores não são projetados para ter o estado local persistido, este comportamento oferece valor limitado, ao mesmo tempo que fornece alguns inconvenientes, incluindo o fornecimento de nó mais lento e a latência de leitura/escrita mais elevada.

Em contraste, os discos de OS efémeros são armazenados apenas na máquina hospedeira, tal como um disco temporário. Isto proporciona uma latência de leitura/escrita mais baixa, juntamente com a escala mais rápida do nó e upgrades de cluster.

Tal como o disco temporário, um disco de SO efémero está incluído no preço da máquina virtual, pelo que não incorre em custos adicionais de armazenamento.

Registar a `EnableEphemeralOSDiskPreview` função:

```azurecli
az feature register --name EnableEphemeralOSDiskPreview --namespace Microsoft.ContainerService
```

Pode levar vários minutos para que o estado seja apresentado como **Registado**. Pode verificar o estado de registo utilizando o comando [da lista de funcionalidades AZ:](/cli/azure/feature?view=azure-cli-latest#az-feature-list)

```azurecli
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/EnableEphemeralOSDiskPreview')].{Name:name,State:properties.state}"
```

Quando o estado aparecer como registado, reaprovida o registo do fornecedor de `Microsoft.ContainerService` recursos utilizando o comando de registo do fornecedor [az:](/cli/azure/provider?view=azure-cli-latest#az-provider-register)

```azurecli
az provider register --namespace Microsoft.ContainerService
```

Para instalar a extensão CLI de pré-visualização aks, utilize os seguintes comandos Azure CLI:

```azurecli
az extension add --name aks-preview
```

Para atualizar a extensão CLI de pré-visualização aks, utilize os seguintes comandos Azure CLI:

```azurecli
az extension update --name aks-preview
```

### <a name="use-ephemeral-os-on-new-clusters-preview"></a>Utilize o SO Efémero em novos clusters (Pré-visualização)

Configure o cluster para utilizar discos EFÉMEROS quando o cluster é criado. Utilize a bandeira para definir o `--aks-custom-headers` SO Efémero como o tipo de disco OS para o novo cluster.

```azure-cli
az aks create --name myAKSCluster --resource-group myResourceGroup -s Standard_DS3_v2 --aks-custom-headers EnableEphemeralOSDisk=true
```

Se pretender criar um cluster regular utilizando discos de SISTEMA ligados à rede, pode fazê-lo omitindo a `--aks-custom-headers` etiqueta personalizada. Também pode optar por adicionar mais piscinas efémeras de nó de nó de OS, conforme abaixo.

### <a name="use-ephemeral-os-on-existing-clusters-preview"></a>Utilize o SO Efémero nos clusters existentes (Pré-visualização)
Configure uma nova piscina de nós para usar discos DESphemerais. Utilize a `--aks-custom-headers` bandeira para definir como o tipo de disco OS como o tipo de disco OS para a piscina de nó.

```azure-cli
az aks nodepool add --name ephemeral --cluster-name myAKSCluster --resource-group myResourceGroup -s Standard_DS3_v2 --aks-custom-headers EnableEphemeralOSDisk=true
```

> [!IMPORTANT]
> Com o SISTEMA Efémero pode implementar imagens VM e exemplo até ao tamanho da cache VM. No caso AKS, a configuração do disco de talos padrão usa 100GiB, o que significa que precisa de um tamanho VM que tenha uma cache maior que 100 GiB. O Standard_DS2_v2 padrão tem um tamanho de cache de 86 GiB, que não é grande o suficiente. O Standard_DS3_v2 tem um tamanho de cache de 172 GiB, que é grande o suficiente. Também pode reduzir o tamanho predefinido do disco SO utilizando `--node-osdisk-size` . O tamanho mínimo para imagens AKS é 30GiB. 

Se pretender criar piscinas de nó com discos de SISTEMA ligados à rede, pode fazê-lo omitindo a `--aks-custom-headers` etiqueta personalizada.

## <a name="custom-resource-group-name"></a>Nome do grupo de recursos personalizados

Quando você implanta um cluster de serviço Azure Kubernetes em Azure, um segundo grupo de recursos é criado para os nós operários. Por predefinição, a AKS nomeará o grupo de recursos de `MC_resourcegroupname_clustername_location` nó, mas também pode fornecer o seu próprio nome.

Para especificar o nome do seu próprio grupo de recursos, instale a versão de extensão Azure CLI de pré-visualização aks-preview 0.3.2 ou posterior. Utilizando o CLI Azure, utilize o `--node-resource-group` parâmetro do comando para `az aks create` especificar um nome personalizado para o grupo de recursos. Se utilizar um modelo de Gestor de Recursos Azure para implantar um cluster AKS, pode definir o nome do grupo de recursos utilizando a `nodeResourceGroup` propriedade.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --node-resource-group myNodeResourceGroup
```

O grupo de recursos secundários é automaticamente criado pelo fornecedor de recursos Azure na sua própria subscrição. Só é possível especificar o nome do grupo de recursos personalizados quando o cluster for criado. 

Enquanto trabalha com o grupo de recursos de nó, lembre-se que não pode:

- Especifique um grupo de recursos existente para o grupo de recursos de nó.
- Especifique uma subscrição diferente para o grupo de recursos de nó.
- Mude o nome do grupo de recursos do nó após a criação do cluster.
- Especificar os nomes dos recursos geridos dentro do grupo de recursos de nó.
- Modifique ou elimine as tags criadas pelo Azure de recursos geridos dentro do grupo de recursos do nó.

## <a name="next-steps"></a>Passos seguintes

- Aprenda a usar para aplicar atualizações de `Kured` segurança e [kernel aos nós Linux](node-updates-kured.md) no seu cluster.
- Consulte [o Upgrade de um cluster Azure Kubernetes Service (AKS)](upgrade-cluster.md) para aprender a atualizar o seu cluster para a versão mais recente do Kubernetes.
- Ler mais sobre [ `containerd` e Kubernetes](https://kubernetes.io/blog/2018/05/24/kubernetes-containerd-integration-goes-ga/)
- Consulte a lista de [perguntas frequentes sobre aKS](faq.md) para encontrar respostas para algumas perguntas comuns da AKS.
- Leia mais sobre [discos Efemérides DE SO](../virtual-machines/ephemeral-os-disks.md).


<!-- LINKS - internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
