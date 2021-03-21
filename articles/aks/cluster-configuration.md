---
title: Configuração do cluster nos Serviços Azure Kubernetes (AKS)
description: Saiba como configurar um cluster no Serviço Azure Kubernetes (AKS)
services: container-service
ms.topic: article
ms.date: 02/09/2020
ms.author: jpalma
author: palma21
ms.openlocfilehash: eaf512915532b482c25e830cd9f2e01d61aa4524
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100572782"
---
# <a name="configure-an-aks-cluster"></a>Configurar um cluster do AKS

Como parte da criação de um cluster AKS, poderá necessitar de personalizar a sua configuração de cluster de acordo com as suas necessidades. Este artigo introduz algumas opções para personalizar o seu cluster AKS.

## <a name="os-configuration"></a>Configuração do SO

A AKS suporta agora o Ubuntu 18.04 como o sistema operativo de nó padrão (OS) em disponibilidade geral (GA) para clusters em versões kubernetes superiores a 1.18 Para versões abaixo de 1.18, a AKS Ubuntu 16.04 ainda é a imagem base predefinido. De kubernetes v1.18 e superior, a base padrão é AKS Ubuntu 18.04.

> [!IMPORTANT]
> Piscinas de nó criadas em Kubernetes v1.18 ou maior padrão para a `AKS Ubuntu 18.04` imagem do nó. Os agrupamentos de nó numa versão suportada de Kubernetes menos de 1.18 recebem `AKS Ubuntu 16.04` como imagem de nó, mas serão atualizados assim `AKS Ubuntu 18.04` que a versão do grupo de nó kubernetes for atualizada para v1.18 ou maior.
> 
> É altamente recomendado testar as suas cargas de trabalho em piscinas de nó AKS Ubuntu 18.04 antes de utilizar clusters em 1.18 ou superior.


### <a name="use-aks-ubuntu-1804-ga-on-new-clusters"></a>Utilizar AKS Ubuntu 18.04 (GA) em novos clusters

Clusters criados em Kubernetes v1.18 ou maior padrão para a `AKS Ubuntu 18.04` imagem do nó. Os agrupamentos de nó numa versão suportada de Kubernetes com menos de 1.18 ainda receberão `AKS Ubuntu 16.04` como imagem de nó, mas serão atualizados assim `AKS Ubuntu 18.04` que a versão do cluster ou do node Pool Kubernetes for atualizada para v1.18 ou superior.

É altamente recomendado testar as suas cargas de trabalho em piscinas de nó AKS Ubuntu 18.04 antes de utilizar clusters em 1.18 ou superior.

Para criar um cluster usando `AKS Ubuntu 18.04` a imagem do nó, basta criar um cluster de kubernetes de execução v1.18 ou maior, como mostrado abaixo

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --kubernetes-version 1.18.14
```

### <a name="use-aks-ubuntu-1804-ga-on-existing-clusters"></a>Utilizar AKS Ubuntu 18.04 (GA) em clusters existentes

Clusters criados em Kubernetes v1.18 ou maior padrão para a `AKS Ubuntu 18.04` imagem do nó. Os agrupamentos de nó numa versão suportada de Kubernetes com menos de 1.18 ainda receberão `AKS Ubuntu 16.04` como imagem de nó, mas serão atualizados assim `AKS Ubuntu 18.04` que a versão do cluster ou do node Pool Kubernetes for atualizada para v1.18 ou superior.

É altamente recomendado testar as suas cargas de trabalho em piscinas de nó AKS Ubuntu 18.04 antes de utilizar clusters em 1.18 ou superior.

Se os seus aglomerados ou piscinas de nó estiverem prontos para `AKS Ubuntu 18.04` a imagem do nó, pode simplesmente atualizá-los para um v1.18 ou superior como abaixo.

```azurecli
az aks upgrade --name myAKSCluster --resource-group myResourceGroup --kubernetes-version 1.18.14
```

Se você só quer atualizar apenas uma piscina de nó:

```azurecli
az aks nodepool upgrade -name ubuntu1804 --cluster-name myAKSCluster --resource-group myResourceGroup --kubernetes-version 1.18.14
```

### <a name="test-aks-ubuntu-1804-ga-on-existing-clusters"></a>Teste AKS Ubuntu 18.04 (GA) em clusters existentes

Piscinas de nó criadas em Kubernetes v1.18 ou maior padrão para a `AKS Ubuntu 18.04` imagem do nó. Os agrupamentos de nó numa versão suportada de Kubernetes com menos de 1.18 ainda receberão `AKS Ubuntu 16.04` como imagem de nó, mas serão atualizados assim `AKS Ubuntu 18.04` que a versão do grupo de nó Kubernetes for atualizada para v1.18 ou superior.

É altamente recomendado testar as suas cargas de trabalho em piscinas de nó AKS Ubuntu 18.04 antes de atualizar as piscinas de nó de produção.

Para criar uma piscina de nó usando `AKS Ubuntu 18.04` a imagem do nó, basta criar uma piscina de nó que executa kubernetes v1.18 ou maior. O seu plano de controlo de clusters precisa de estar pelo menos em v1.18 ou maior também, mas as suas outras piscinas de nó podem permanecer numa versão mais antiga dos kubernetes.
Abaixo estamos primeiro a atualizar o plano de controlo e depois a criar um novo conjunto de nós com v1.18 que receberá a nova versão OS de imagem de nó.

```azurecli
az aks upgrade --name myAKSCluster --resource-group myResourceGroup --kubernetes-version 1.18.14 --control-plane-only

az aks nodepool add --name ubuntu1804 --cluster-name myAKSCluster --resource-group myResourceGroup --kubernetes-version 1.18.14
```

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

### <a name="containerd-limitationsdifferences"></a>`Containerd` limitações/diferenças

* Para utilizar `containerd` como tempo de funcionação do recipiente, deve utilizar o AKS Ubuntu 18.04 como imagem base do SO.
* Enquanto o estivador de ferramentas ainda está presente nos nós, Kubernetes usa `containerd` como tempo de funcionação do recipiente. Portanto, uma vez que a Moby/Docker não gere os recipientes criados pela Kubernetes nos nós, não é possível ver ou interagir com os seus contentores usando comandos Docker `docker ps` (como) ou a API do Docker.
* Para `containerd` , recomendamos a utilização [`crictl`](https://kubernetes.io/docs/tasks/debug-application-cluster/crictl) como CLI de substituição em vez do CLI do Docker para **resolução de problemas** de cápsulas, contentores e imagens de contentores nos nós kubernetes (por exemplo, `crictl ps` ). 
   * Não fornece a funcionalidade completa do Estivador CLI. Destina-se apenas a resolver problemas.
   * `crictl` oferece uma visão mais amiga dos kubernetes dos recipientes, com conceitos como pods, etc. estar presentes.
* `Containerd` configura o registo utilizando o formato de registo normalizado `cri` (que é diferente do que obtém atualmente do condutor json do Docker). A sua solução de registo precisa de suportar o `cri` formato de registo (como o [Azure Monitor for Containers)](../azure-monitor/containers/container-insights-enable-new-cluster.md)
* Já não pode aceder ao motor do estivador, `/var/run/docker.sock` ou utilizar o Docker-in-Docker (DinD).
  * Se atualmente extrair registos de aplicações ou dados de monitorização do Docker Engine, utilize algo como [Azure Monitor para contentores.](../azure-monitor/containers/container-insights-enable-new-cluster.md) Além disso, a AKS não suporta a execução de nenhum comando de banda nos nós do agente que possam causar instabilidade.
  * Mesmo quando se usa Moby/docker, construir imagens e alavancar diretamente o motor do estivador através dos métodos acima é fortemente desencorajado. Kubernetes não está totalmente ciente desses recursos consumidos, e essas abordagens apresentam numerosas questões detalhadas [aqui](https://jpetazzo.github.io/2015/09/03/do-not-use-docker-in-docker-for-ci/) e [aqui](https://securityboulevard.com/2018/05/escaping-the-whale-things-you-probably-shouldnt-do-with-docker-part-1/), por exemplo.
* Imagens de construção - Pode continuar a utilizar o seu atual estivador construir fluxo de trabalho normalmente, a menos que esteja a construir imagens dentro do seu cluster AKS. Neste caso, considere mudar para a abordagem recomendada para a construção de imagens utilizando [tarefas ACR](../container-registry/container-registry-quickstart-task-cli.md), ou uma opção de cluster mais segura como [o estivador buildx](https://github.com/docker/buildx).

## <a name="generation-2-virtual-machines"></a>Máquinas virtuais de 2ª Geração

A Azure suporta [máquinas virtuais geração 2 (Gen2) (VMs)](../virtual-machines/generation-2.md). A geração 2 VMs suporta características-chave que não são suportadas na geração 1 VMs (Gen1). Estas funcionalidades incluem memória aumentada, extensões de guarda de software Intel (Intel SGX) e memória persistente virtualizada (vPMEM).

A geração 2 VMs usa a nova arquitetura de arranque baseada na UEFI em vez da arquitetura baseada em BIOS usada pela geração 1 VMs.
Apenas SKUs e tamanhos específicos suportam Os VMs da Gen2. Verifique a [lista de tamanhos suportados,](../virtual-machines/generation-2.md#generation-2-vm-sizes)para ver se o seu SKU suporta ou requer Gen2.

Além disso, nem todas as imagens VM suportam a Gen2, em VMs AKS Gen2 utilizarão a nova [imagem AKS Ubuntu 18.04](#os-configuration). Esta imagem suporta todos os SKUs e tamanhos da Gen2.

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
> Com o SISTEMA Efémero pode implementar imagens VM e exemplo até ao tamanho da cache VM. No caso AKS, a configuração do disco de nó padrão USA 128GB, o que significa que precisa de um tamanho VM que tenha uma cache maior que 128GB. O Standard_DS2_v2 padrão tem um tamanho de cache de 86GB, que não é grande o suficiente. O Standard_DS3_v2 tem um tamanho de cache de 172GB, que é grande o suficiente. Também pode reduzir o tamanho predefinido do disco SO utilizando `--node-osdisk-size` . O tamanho mínimo para imagens AKS é de 30GB. 

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

- Saiba como [atualizar as imagens do nó](node-image-upgrade.md) no seu cluster.
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
