---
title: Falhas na atribuição de Azure VM Microsoft Docs
description: Falhas de atribuição de problemas quando cria, reinicia ou redimensiona um VM em Azure
services: virtual-machines
documentationcenter: ''
author: JiangChen79
manager: felixwu
editor: ''
tags: top-support-issue,azure-resource-manager,azure-service-management
ms.assetid: 1ef41144-6dd6-4a56-b180-9d8b3d05eae7
ms.service: virtual-machines
ms.topic: troubleshooting
ms.date: 04/13/2018
ms.author: cjiang
ms.openlocfilehash: 9bb228725d5ad8e3583c73be09c582478f74a1e8
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/19/2020
ms.locfileid: "77471895"
---
# <a name="troubleshoot-allocation-failures-when-you-create-restart-or-resize-vms-in-azure"></a>Falhas de atribuição de problemas quando cria, reinicia ou redimensiona VMs em Azure

Quando cria uma máquina virtual (VM), reiniciar os VMs (deallocated) ou redimensionar um VM, o Microsoft Azure atribui recursos de computação à sua subscrição. Estamos continuamente a investir em infraestruturas e funcionalidades adicionais para garantir que temos sempre todos os tipos de VM disponíveis para apoiar a procura do cliente. No entanto, pode ocasionalmente experimentar falhas na atribuição de recursos devido ao crescimento sem precedentes da procura de serviços Azure em regiões específicas. Este problema pode ocorrer quando se tenta criar ou iniciar VMs numa região enquanto os VMs apresentam o seguinte código de erro e mensagem:

**Código de erro**: Alocação Falhada ou ZonalAllocationFailed

**Error message**: "A locação falhou. Não dispomos de capacidade suficiente para a dimensão vm solicitada nesta região. Leia mais sobre a melhoria da probabilidade de sucesso na atribuição em https:\//aka.ms/allocation-guidance"

Este artigo explica as causas de algumas das falhas comuns de atribuição e sugere possíveis soluções.

Se o seu problema azure não for abordado neste artigo, visite os [fóruns do Azure sobre mSDN e Stack Overflow](https://azure.microsoft.com/support/forums/). Pode publicar o seu problema nestes fóruns ou @AzureSupport no Twitter. Além disso, pode apresentar um pedido de suporte Azure selecionando suporte para obter suporte no site de [suporte do Azure.](https://azure.microsoft.com/support/options/)

Até que o seu tipo de VM preferido esteja disponível na sua região preferida, aconselhamos os clientes que se deparam com problemas de implementação a considerar em que a orientação na tabela seguinte é uma suposição temporária. 

Identifique o cenário que melhor corresponda ao seu caso e, em seguida, retente o pedido de atribuição utilizando a seleção sugerida correspondente para aumentar a probabilidade de sucesso de atribuição. Em alternativa, pode sempre voltar a tentar mais tarde. Isto porque recursos suficientes podem ter sido libertados no aglomerado, região ou zona para acomodar o seu pedido. 


## <a name="resize-a-vm-or-add-vms-to-an-existing-availability-set"></a>Redimensionar uma VM ou adicionar VMs a um conjunto de disponibilidade já existente

### <a name="cause"></a>Causa

Um pedido para redimensionar um VM ou adicionar um VM a um conjunto de disponibilidade existente deve ser experimentado no cluster original que acolhe o conjunto de disponibilidade existente. O tamanho de VM solicitado é suportado pelo cluster, mas o cluster pode não ter capacidade suficiente. 

### <a name="workaround"></a>Solução

Se o VM pode fazer parte de um conjunto de disponibilidade diferente, crie um VM num conjunto de disponibilidade diferente (na mesma região). Este novo VM pode então ser adicionado à mesma rede virtual.

Pare (desalocar) todos os VMs no mesmo conjunto de disponibilidade e, em seguida, reinicie cada um deles.
Para parar: Clique nos grupos de recursos > [o seu grupo de recursos] > Resources > [o seu conjunto de disponibilidade] > Máquinas Virtuais > [a sua máquina virtual] > Stop.
Depois de todas as vMs pararem, selecione o primeiro VM e, em seguida, clique em Iniciar.
Este passo assegura que seja executada uma nova tentativa de atribuição e que seja selecionado um novo cluster que tenha capacidade suficiente.

## <a name="restart-partially-stopped-deallocated-vms"></a>Reiniciar VMs parcialmente paradas (desalocadas)

### <a name="cause"></a>Causa

Desafetação parcial significa que parou (deallocated) um ou mais, mas não todos, VMs em um conjunto de disponibilidade. Quando desaloca um VM, os recursos associados são libertados. Reiniciar os VMs num conjunto de disponibilidade parcialmente localizado é o mesmo que adicionar VMs a um conjunto de disponibilidade existente. Por conseguinte, o pedido de atribuição deve ser julgado no cluster original que acolhe o conjunto de disponibilidade existente que pode não ter capacidade suficiente.

### <a name="workaround"></a>Solução

Pare (desalocar) todos os VMs no mesmo conjunto de disponibilidade e, em seguida, reinicie cada um deles.
Para parar: Clique nos grupos de recursos > [o seu grupo de recursos] > Resources > [o seu conjunto de disponibilidade] > Máquinas Virtuais > [a sua máquina virtual] > Stop.
Depois de todas as vMs pararem, selecione o primeiro VM e, em seguida, clique em Iniciar.
Isto assegurará a execução de uma nova tentativa de atribuição e de selecionar um novo cluster que tenha capacidade suficiente.

## <a name="restart-fully-stopped-deallocated-vms"></a>Reiniciar VMs completamente paradas (desalocadas)

### <a name="cause"></a>Causa

A deatribuição total significa que parou (deallocated) todos os VMs num conjunto de disponibilidade. O pedido de atribuição de reinício destes VMs destinar-se-á a todos os clusters que suportem a dimensão desejada dentro da região ou zona. Altere o seu pedido de atribuição de acordo com as sugestões deste artigo e reencontre o pedido para melhorar as hipóteses de sucesso da atribuição. 

### <a name="workaround"></a>Solução

Se utilizar séries ou tamanhos VM mais antigos, tais como Dv1, DSv1, Av1, D15v2 ou DS15v2, considere mudar-se para versões mais recentes. Consulte estas recomendações para tamanhos de VM específicos.
Se não tiver a opção de utilizar um tamanho VM diferente, tente implantar-se numa região diferente dentro do mesmo geo. Para obter mais informações sobre os tamanhos de VM disponíveis em cada região em https://aka.ms/azure-regions

Se estiver a utilizar zonas de disponibilidade, experimente outra zona dentro da região que possa ter capacidade disponível para o tamanho de VM solicitado.

Se o seu pedido de atribuição for grande (mais de 500 núcleos), consulte a orientação nas seguintes secções para dividir o pedido em implementações menores.

Tente [reimplantar o VM](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/redeploy-to-new-node-windows). A reafectação do VM atribui o VM a um novo aglomerado na região.

## <a name="allocation-failures-for-older-vm-sizes-av1-dv1-dsv1-d15v2-ds15v2-etc"></a>Falhas de atribuição para tamanhos vm mais antigos (Av1, Dv1, DSv1, D15v2, DS15v2, etc.)

À medida que expandimos a infraestrutura azure, implementamos hardware de nova geração que foi projetado para suportar os mais recentes tipos de máquinas virtuais. Algumas das séries vMs mais antigas não funcionam na nossa infraestrutura de última geração. Por esta razão, os clientes podem ocasionalmente experimentar falhas de atribuição para estas SKUs legados. Para evitar este problema, encorajamos os clientes que estão a usar máquinas virtuais da série legacy a considerar em mudar-se para os VMs mais recentes equivalentes de acordo com as seguintes recomendações: Estes VMs estão otimizados para o hardware mais recente e vão permitir-lhe tirar partido de melhor preços e desempenho. 

|Série/tamanho VM legado|VM/tamanho recomendado|Mais informações|
|----------------------|----------------------------|--------------------|
|Série Av1|[Série Av2](../windows/sizes-general.md#av2-series)|https://azure.microsoft.com/blog/new-av2-series-vm-sizes/
|Série Dv1 ou DSv1 (D1 a D5)|[Série Dv3 ou DSv3](../windows/sizes-general.md#dsv3-series-1)|https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/
|Série Dv1 ou DSv1 (D11 a D14)|[Série Ev3 ou ESv3](../windows/sizes-memory.md#ev3-series)|
|D15v2 ou DS15v2|Se estiver a utilizar o modelo de implementação do Gestor de Recursos para tirar partido dos tamanhos de VM maiores, considere mudar-se para D16v3/DS16v3 ou D32v3/DS32v3. Estes são projetados para funcionar no hardware de última geração. Se estiver a utilizar o modelo de implementação do Gestor de Recursos para se certificar de que a sua instância VM está isolada em hardware dedicado a um único cliente, considere mudar-se para os novos tamanhos de VM isolados, E64i_v3 ou E64is_v3, que são projetados para funcionar no hardware de última geração. |https://azure.microsoft.com/blog/new-isolated-vm-sizes-now-available/

## <a name="allocation-failures-for-large-deployments-more-than-500-cores"></a>Falhas de atribuição de grandes implantações (mais de 500 núcleos)

Reduza o número de instâncias do tamanho de VM solicitado e, em seguida, tente novamente a operação de implantação. Além disso, para implementações maiores, pode querer avaliar conjuntos de [escala de máquinas virtuais Azure](https://docs.microsoft.com/azure/virtual-machine-scale-sets/). O número de casos de VM pode aumentar ou diminuir automaticamente em resposta à procura ou a um calendário definido, e você tem uma maior chance de sucesso de atribuição porque as implementações podem ser distribuídas por vários clusters. 

## <a name="background-information"></a>Informação de fundo
### <a name="how-allocation-works"></a>Como funciona a atribuição
Os servidores nos datacenters do Azure são divididos em partições em clusters. Normalmente, é tentado um pedido de alocação em vários clusters, mas é possível que determinadas restrições do pedido de alocação forcem a plataforma do Azure para tentar o pedido em apenas um cluster. Neste artigo, vamos referir-nos a isto como "presoa a um aglomerado". O diagrama 1 abaixo ilustra o caso de uma alocação normal que é tentada em vários clusters. Diagrama 2 ilustra o caso de uma alocação que está fixada ao Cluster 2 porque é aí que o serviço de nuvem existente CS_1 ou conjunto de disponibilidade é hospedado.
![alocação](./media/virtual-machines-common-allocation-failure/Allocation1.png)

### <a name="why-allocation-failures-happen"></a>Por que falhas na atribuição acontecem
Quando um pedido de atribuição é fixado a um cluster, há uma maior chance de não encontrar recursos gratuitos, uma vez que o conjunto de recursos disponíveis é menor. Além disso, se o seu pedido de atribuição for fixado a um cluster, mas o tipo de recurso que solicitou não for suportado por esse cluster, o seu pedido falhará mesmo que o cluster tenha recursos gratuitos. O diagrama seguinte 3 ilustra o caso em que uma atribuição fixada falha porque o único aglomerado de candidatos não tem recursos livres. O diagrama 4 ilustra o caso em que uma atribuição fixada falha porque o único cluster de candidatos não suporta a dimensão vm solicitada, mesmo que o cluster tenha recursos livres.

![Falha na atribuição de fixações](./media/virtual-machines-common-allocation-failure/Allocation2.png)


