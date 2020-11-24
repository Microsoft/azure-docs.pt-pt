---
title: Configuração do cluster nos Serviços Azure Kubernetes (AKS)
description: Saiba como configurar um cluster no Serviço Azure Kubernetes (AKS)
services: container-service
ms.topic: conceptual
ms.date: 09/21/2020
ms.author: jpalma
author: palma21
ms.openlocfilehash: 4252e3a7f8c3ff9d0ec782a2a9222553c063463c
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95533281"
---
# <a name="configure-an-aks-cluster"></a>Configurar um cluster do AKS

Como parte da criação de um cluster AKS, poderá necessitar de personalizar a sua configuração de cluster de acordo com as suas necessidades. Este artigo introduz algumas opções para personalizar o seu cluster AKS.

## <a name="os-configuration"></a>Configuração do SO

A AKS suporta agora o Ubuntu 18.04 como o sistema operativo de nó (OS) em disponibilidade geral para clusters em versões kubernetes superiores a 1.18.8. Para versões abaixo de 1.18.x, aKS Ubuntu 16.04 ainda é a imagem base padrão. De kubernetes v1.18.x e em diante, a base padrão é AKS Ubuntu 18.04.

> [!IMPORTANT]
> Piscinas de nó criadas em Kubernetes v1.18 ou maior padrão para a `AKS Ubuntu 18.04` imagem do nó. Os agrupamentos de nó numa versão suportada de Kubernetes menos de 1.18 recebem `AKS Ubuntu 16.04` como imagem de nó, mas serão atualizados assim `AKS Ubuntu 18.04` que a versão do grupo de nó kubernetes for atualizada para v1.18 ou maior.
> 
> É altamente recomendado testar as suas cargas de trabalho em piscinas de nó AKS Ubuntu 18.04 antes de utilizar clusters em 1.18 ou superior. Leia sobre como [testar piscinas de nó Ubuntu 18.04](#use-aks-ubuntu-1804-existing-clusters-preview).

A secção seguinte explicará como utiliza e testa a AKS Ubuntu 18.04 em clusters que ainda não estão a utilizar uma versão de kubernetes 1.18.x ou superior, ou foram criados antes de esta funcionalidade ficar geralmente disponível, utilizando a pré-visualização da configuração do SISTEMA.

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

Pode levar vários minutos para que o estado seja apresentado como **Registado**. Pode verificar o estado de registo utilizando o comando [da lista de funcionalidades AZ:](/cli/azure/feature?view=azure-cli-latest#az-feature-list&preserve-view=true)

```azurecli
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/UseCustomizedUbuntuPreview')].{Name:name,State:properties.state}"
```

Quando o estado aparecer como registado, reaprovida o registo do fornecedor de `Microsoft.ContainerService` recursos utilizando o comando de registo do fornecedor [az:](/cli/azure/provider?view=azure-cli-latest#az-provider-register&preserve-view=true)

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

## <a name="container-runtime-configuration"></a>Configuração do tempo de execução do contentor

Um tempo de funcionação do contentor é um software que executa contentores e gere imagens de contentores num nó. O tempo de execução ajuda a abstrato de chamadas de sys ou funcionalidade específica do sistema operativo (OS) para executar contentores em Linux ou Windows. Aglomerados AKS usando piscinas de nó de kubernetes versão 1.19 e maior utilização `containerd` como tempo de funcionação do contentor. Os agrupamentos AKS que usam Kubernetes antes do v1.19 para piscinas de nó usam [o Moby](https://mobyproject.org/) (estivador a montante) como tempo de execução do contentor.

![Docker CRI 1](media/cluster-configuration/docker-cri.png)

[`Containerd`](https://containerd.io/) é um tempo de funcionamento do recipiente de núcleo compatível com [OCI](https://opencontainers.org/) (Open Container Initiative) que fornece o conjunto mínimo de funcionalidade necessária para executar contentores e gerir imagens num nó. Foi [doado](https://www.cncf.io/announcement/2017/03/29/containerd-joins-cloud-native-computing-foundation/) à Cloud Native Compute Foundation (CNCF) em março de 2017. A versão atual da Moby que a AKS utiliza já aproveita e é construída em cima de `containerd` , como mostrado acima.

Com um `containerd` nó à base e piscinas de nó, em vez de falar com o , o `dockershim` kubelet falará diretamente `containerd` através do plugin CRI (interface de tempo de execução do contentor), removendo saltos extra no fluxo quando comparado com a implementação do Docker CRI. Como tal, verá uma melhor latência de arranque de pod e menos utilização de recursos (CPU e memória).

Ao utilizar `containerd` os nós AKS, a latência do arranque do pod melhora e o consumo de recursos de nó pelo tempo de funcionaamento do recipiente diminui. Estas melhorias são possibilitada por esta nova arquitetura onde kubelet fala diretamente `containerd` através do plugin CRI enquanto em Moby/Docker arquitetura kubelet falaria com o `dockershim` motor e estivador antes `containerd` de alcançar, tendo assim saltos extra no fluxo.

![Docker CRI 2](media/cluster-configuration/containerd-cri.png)

`Containerd` funciona em todas as versões GA de Kubernetes em AKS, e em todas as versões de kubernetes acima acima de v1.19, e suporta todas as funcionalidades de Kubernetes e AKS.

> [!IMPORTANT]
> Aglomerados com piscinas de nó criados em Kubernetes v1.19 ou maior padrão `containerd` para o seu tempo de funcionação do contentor. Os clusters com piscinas de nó numa versão suportada de Kubernetes menos de 1.19 recebem `Moby` para o seu tempo de funcionação do contentor, mas serão atualizados assim que a versão do grupo de nó `ContainerD` Kubernetes for atualizada para v1.19 ou superior. Você ainda pode usar `Moby` piscinas de nó e clusters em versões suportadas mais antigas até que estes caiam fora de suporte.
> 
> É altamente recomendado testar as suas cargas de trabalho em piscinas de nó AKS com `containerD` antes de utilizar clusters em 1.19 ou superior.

A secção seguinte explicará como pode utilizar e testar AKS com `containerD` clusters que ainda não estão a utilizar uma versão 1.19 ou superior de Kubernetes, ou que foram criados antes de esta funcionalidade ficar geralmente disponível, utilizando a pré-visualização da configuração de tempo de funcionação do contentor.

### <a name="use-containerd-as-your-container-runtime-preview"></a>Utilizar `containerd` como tempo de funcionação do seu recipiente (pré-visualização)

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

Pode levar vários minutos para que o estado seja apresentado como **Registado**. Pode verificar o estado de registo utilizando o comando [da lista de funcionalidades AZ:](/cli/azure/feature?view=azure-cli-latest#az-feature-list&preserve-view=true)

```azurecli
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/UseCustomizedContainerRuntime')].{Name:name,State:properties.state}"
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/UseCustomizedUbuntuPreview')].{Name:name,State:properties.state}"
```

Quando o estado aparecer como registado, reaprovida o registo do fornecedor de `Microsoft.ContainerService` recursos utilizando o comando de registo do fornecedor [az:](/cli/azure/provider?view=azure-cli-latest#az-provider-register&preserve-view=true)

```azurecli
az provider register --namespace Microsoft.ContainerService
```  

### <a name="use-containerd-on-new-clusters-preview"></a>Utilização `containerd` em novos clusters (pré-visualização)

Configure o cluster para usar `containerd` quando o cluster for criado. Utilize a `--aks-custom-headers` bandeira para definir como tempo de `containerd` funcionação do recipiente.

> [!NOTE]
> O `containerd` tempo de funcionação só é suportado em nós e piscinas de nó usando a imagem AKS Ubuntu 18.04.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --aks-custom-headers CustomizedUbuntu=aks-ubuntu-1804,ContainerRuntime=containerd
```

Se quiser criar clusters com o tempo de execução Moby (docker), pode fazê-lo omitindo a `--aks-custom-headers` etiqueta personalizada.

### <a name="use-containerd-on-existing-clusters-preview"></a>Utilização `containerd` em clusters existentes (pré-visualização)

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

A Azure suporta [máquinas virtuais geração 2 (Gen2) (VMs)](../virtual-machines/generation-2.md). A geração 2 VMs suporta características-chave que não são suportadas na geração 1 VMs (Gen1). Estas funcionalidades incluem memória aumentada, extensões de guarda de software Intel (Intel SGX) e memória persistente virtualizada (vPMEM).

A geração 2 VMs usa a nova arquitetura de arranque baseada na UEFI em vez da arquitetura baseada em BIOS usada pela geração 1 VMs.
Apenas SKUs e tamanhos específicos suportam Os VMs da Gen2. Verifique a [lista de tamanhos suportados,](../virtual-machines/generation-2.md#generation-2-vm-sizes)para ver se o seu SKU suporta ou requer Gen2.

Além disso, nem todas as imagens VM suportam a Gen2, em VMs AKS Gen2 utilizarão a nova [imagem AKS Ubuntu 18.04](#os-configuration). Esta imagem suporta todos os SKUs e tamanhos da Gen2.

Para utilizar os VMs da Gen2 durante a pré-visualização, você vai precisar:
- A `aks-preview` extensão CLI instalada.
- A `Gen2VMPreview` bandeira de características registada.

Registar a `Gen2VMPreview` função:

```azurecli
az feature register --name Gen2VMPreview --namespace Microsoft.ContainerService
```

Pode levar vários minutos para que o estado seja apresentado como **Registado**. Pode verificar o estado de registo utilizando o comando [da lista de funcionalidades AZ:](/cli/azure/feature?view=azure-cli-latest#az-feature-list&preserve-view=true)

```azurecli
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/Gen2VMPreview')].{Name:name,State:properties.state}"
```

Quando o estado aparecer como registado, reaprovida o registo do fornecedor de `Microsoft.ContainerService` recursos utilizando o comando de registo do fornecedor [az:](/cli/azure/provider?view=azure-cli-latest#az-provider-register&preserve-view=true)

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

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup -s Standard_D2s_v3 --aks-custom-headers usegen2vm=true
```

Se quiser criar um cluster regular utilizando VMs da Geração 1 (Gen1), pode fazê-lo omitindo a `--aks-custom-headers` etiqueta personalizada. Também pode optar por adicionar mais Gen1 ou Gen2 VMs conforme abaixo.

### <a name="use-gen2-vms-on-existing-clusters-preview"></a>Utilizar VMs Gen2 em clusters existentes (Pré-visualização)
Configure uma nova piscina de nós para usar Os VMs da Gen2. Use a `--aks-custom-headers` bandeira para definir a Gen2 como a geração VM para a piscina de nós.

```azurecli
az aks nodepool add --name gen2 --cluster-name myAKSCluster --resource-group myResourceGroup -s Standard_D2s_v3 --aks-custom-headers usegen2vm=true
```

Se quiser criar piscinas regulares de nó gen1, pode fazê-lo omitindo a `--aks-custom-headers` etiqueta personalizada.


## <a name="ephemeral-os"></a>Oss efémeros

Por predefinição, o Azure replica automaticamente o disco do sistema operativo para uma máquina virtual para o armazenamento do Azure para evitar a perda de dados caso o VM precise de ser transferido para outro hospedeiro. No entanto, uma vez que os contentores não são projetados para ter o estado local persistido, este comportamento oferece valor limitado, ao mesmo tempo que fornece alguns inconvenientes, incluindo o fornecimento de nó mais lento e a latência de leitura/escrita mais elevada.

Em contraste, os discos de OS efémeros são armazenados apenas na máquina hospedeira, tal como um disco temporário. Isto proporciona uma latência de leitura/escrita mais baixa, juntamente com a escala mais rápida do nó e upgrades de cluster.

Tal como o disco temporário, um disco de SO efémero está incluído no preço da máquina virtual, pelo que não incorre em custos adicionais de armazenamento.

> [!IMPORTANT]
>Quando um utilizador não solicita explicitamente discos geridos para o SO, o AKS irá predefinir o SISTEMA Efémero, se possível, para uma determinada configuração de nodepool.

Ao utilizar o SISTEMA EFÉMERO, o disco DE TEM deve encaixar na cache VM. Os tamanhos da cache VM estão disponíveis na [documentação Azure](../virtual-machines/dv3-dsv3-series.md) em parênteses ao lado do rendimento de IO ("tamanho da cache em GiB").

Utilizando o tamanho VM padrão AKS Standard_DS2_v2 com o tamanho padrão do disco DE de 100GB como exemplo, este tamanho VM suporta os sistemas efémeros, mas tem apenas 86GB de tamanho de cache. Esta configuração por defeito seria padrão para discos geridos se o utilizador não especificar explicitamente. Se um utilizador solicitasse explicitamente um SO efémero, receberiam um erro de validação.

Se um utilizador solicitar o mesmo Standard_DS2_v2 com um disco OS de 60GB, esta configuração por defeito para o SISTEMA Efémero: o tamanho solicitado de 60GB é menor do que o tamanho máximo de cache de 86GB.

Utilizando Standard_D8s_v3 com disco OS de 100GB, este tamanho VM suporta OS efémeros e tem 200GB de espaço cache. Se um utilizador não especificar o tipo de disco OS, o nodepool receberá um SO efémero por predefinição. 

O SO efémero requer pelo menos a versão 2.15.0 do Azure CLI.

### <a name="use-ephemeral-os-on-new-clusters"></a>Use o SO Efémero em novos clusters

Configure o cluster para utilizar discos EFÉMEROS quando o cluster é criado. Utilize a bandeira para definir o `--node-osdisk-type` SO Efémero como o tipo de disco OS para o novo cluster.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup -s Standard_DS3_v2 --node-osdisk-type Ephemeral
```

Se pretender criar um cluster regular utilizando discos de SISTEMA ligados à rede, pode fazê-lo especificando `--node-osdisk-type=Managed` . Também pode optar por adicionar mais piscinas efémeras de nó de nó de OS, conforme abaixo.

### <a name="use-ephemeral-os-on-existing-clusters"></a>Utilize o SO Efémero nos clusters existentes
Configure uma nova piscina de nós para usar discos DESphemerais. Utilize a `--node-osdisk-type` bandeira para definir como o tipo de disco OS como o tipo de disco OS para a piscina de nó.

```azurecli
az aks nodepool add --name ephemeral --cluster-name myAKSCluster --resource-group myResourceGroup -s Standard_DS3_v2 --node-osdisk-type Ephemeral
```

> [!IMPORTANT]
> Com o SISTEMA Efémero pode implementar imagens VM e exemplo até ao tamanho da cache VM. No caso AKS, a configuração do disco de talos padrão usa 100GiB, o que significa que precisa de um tamanho VM que tenha uma cache maior que 100 GiB. O Standard_DS2_v2 padrão tem um tamanho de cache de 86 GiB, que não é grande o suficiente. O Standard_DS3_v2 tem um tamanho de cache de 172 GiB, que é grande o suficiente. Também pode reduzir o tamanho predefinido do disco SO utilizando `--node-osdisk-size` . O tamanho mínimo para imagens AKS é 30GiB. 

Se pretender criar piscinas de nó com discos de SISTEMA ligados à rede, pode fazê-lo especificando `--node-osdisk-type Managed` .

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
