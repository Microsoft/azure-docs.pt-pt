---
title: Falhas na atribuição de VM da Azure VM Microsoft Docs
description: Falhas de atribuição de resolução de problemas quando cria, reinicia ou redimensiona um VM em Azure
services: virtual-machines
documentationcenter: ''
author: DavidCBerry13
manager: felixwu
editor: ''
tags: top-support-issue,azure-resource-manager,azure-service-management
ms.assetid: 1ef41144-6dd6-4a56-b180-9d8b3d05eae7
ms.service: virtual-machines
ms.topic: troubleshooting
ms.date: 11/06/2020
ms.author: daberry
ms.openlocfilehash: 79bc043a991404a3ee9da954b9639bf1a41f2c51
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2020
ms.locfileid: "94365878"
---
# <a name="troubleshoot-allocation-failures-when-you-create-restart-or-resize-vms-in-azure"></a>Resolver falhas de alocação ao criar, reiniciar ou redimensionar VMs no Azure

Quando cria uma máquina virtual (VM), reinicie os VMs (deallocados) ou redimensione um VM, o Microsoft Azure atribui recursos de cálculo à sua subscrição. Estamos continuamente a investir em infraestruturas adicionais e funcionalidades para garantir que temos sempre todos os tipos de VM disponíveis para apoiar a procura do cliente. No entanto, pode ocasionalmente sofrer falhas na alocação de recursos devido ao crescimento sem precedentes da procura de serviços Azure em regiões específicas. Este problema pode ocorrer quando tenta criar ou iniciar VMs numa região enquanto os VMs apresentam o seguinte código de erro e mensagem:

**Código de erro** : AtribuiçãoFailada ou ZonalAllocationFailed

**Error message** : "A atribuição falhou. Não temos capacidade suficiente para a dimensão VM solicitada nesta região. Leia mais sobre a melhoria da probabilidade de alocação de sucesso em https: \/ /aka.ms/allocation-guidance"

> [!NOTE]
> Se estiver a resolver problemas com um conjunto de escala de máquina virtual (VMSS), o processo é o mesmo que um VM padrão. Para resolver o problema, deve seguir as instruções deste artigo.
> 
>**Error message** : "A atribuição falhou. Se estiver a tentar adicionar um novo VM a um Conjunto de Balanças de Máquina Virtual com um único grupo de colocação ou atualizar/redimensionar um VM existente num Conjunto de Balanças de Máquina Virtual com um único grupo de colocação, tenha em atenção que essa alocação é alargada a um único cluster, e é possível que o cluster esteja fora de capacidade. Por favor leia mais sobre a melhoria da probabilidade de alocação de sucesso em http: \/ /aka.ms/allocation-guidance."

Este artigo explica as causas de algumas falhas comuns da atribuição e sugere possíveis soluções.

Se a sua questão Azure não for abordada neste artigo, visite os [fóruns Azure sobre MSDN e Stack Overflow](https://azure.microsoft.com/support/forums/). Pode publicar o seu problema nestes fóruns ou @AzureSupport no Twitter. Além disso, pode apresentar um pedido de suporte Azure selecionando Obter suporte no site [de suporte do Azure.](https://azure.microsoft.com/support/options/)

Até que o seu tipo VM preferido esteja disponível na sua região preferida, aconselhamos os clientes que deparam com problemas de implantação a considerarem a orientação na tabela seguinte como uma solução temporária. 

Identifique o cenário que melhor se aqueca o seu caso e, em seguida, relemque o pedido de atribuição utilizando a alternativa sugerida correspondente para aumentar a probabilidade de sucesso de atribuição. Em alternativa, pode sempre voltar a tentar mais tarde. Isto porque recursos suficientes podem ter sido libertados no cluster, região ou zona para acomodar o seu pedido. 


## <a name="resize-a-vm-or-add-vms-to-an-existing-availability-set"></a>Redimensionar uma VM ou adicionar VMs a um conjunto de disponibilidade já existente

### <a name="cause"></a>Causa

Um pedido para redimensionar um VM ou adicionar um VM a um conjunto de disponibilidade existente deve ser experimentado no cluster original que acolhe o conjunto de disponibilidade existente. O tamanho de VM solicitado é suportado pelo cluster, mas o cluster pode não ter atualmente capacidade suficiente. 

### <a name="workaround"></a>Solução

Se o VM puder fazer parte de um conjunto de disponibilidade diferente, crie um VM num conjunto de disponibilidade diferente (na mesma região). Este novo VM pode então ser adicionado à mesma rede virtual.

Pare (deallocate) todos os VMs no mesmo conjunto de disponibilidade e, em seguida, reinicie cada um deles.
Para parar: Clique em grupos de recursos > [o seu grupo de recursos] > recursos > [o seu conjunto de disponibilidade] > Máquinas Virtuais > [a sua máquina virtual] > Parar.
Depois de todas as paragens de VMs, selecione o primeiro VM e, em seguida, clique em Iniciar.
Este passo assegura a execução de uma nova tentativa de atribuição e a seleção de um novo cluster com capacidade suficiente.

## <a name="restart-partially-stopped-deallocated-vms"></a>Reiniciar VMs parcialmente paradas (desalocadas)

### <a name="cause"></a>Causa

Alocação parcial significa que paraste (deallocated) um ou mais, mas não todos, VMs num conjunto de disponibilidade. Quando se negoceia um VM, os recursos associados são libertados. Reiniciar VMs num conjunto de disponibilidade parcialmente translocado é o mesmo que adicionar VMs a um conjunto de disponibilidade existente. Por conseguinte, o pedido de atribuição deve ser julgado no cluster original que acolhe o conjunto de disponibilidade existente que pode não ter capacidade suficiente.

### <a name="workaround"></a>Solução

Pare (deallocate) todos os VMs no mesmo conjunto de disponibilidade e, em seguida, reinicie cada um deles.
Para parar: Clique em grupos de recursos > [o seu grupo de recursos] > recursos > [o seu conjunto de disponibilidade] > Máquinas Virtuais > [a sua máquina virtual] > Parar.
Depois de todas as paragens de VMs, selecione o primeiro VM e, em seguida, clique em Iniciar.
Isto assegurará a execução de uma nova tentativa de atribuição e a seleção de um novo cluster com capacidade suficiente.

## <a name="restart-fully-stopped-deallocated-vms"></a>Reiniciar VMs completamente paradas (desalocadas)

### <a name="cause"></a>Causa

A negociação total significa que parou (deallocated) todos os VMs num conjunto de disponibilidade. O pedido de atribuição para reiniciar estes VMs destinar-se-á a todos os clusters que suportam a dimensão desejada dentro da região ou zona. Altere o seu pedido de atribuição de acordo com as sugestões deste artigo e recandidú-lo para melhorar a possibilidade de alocação de sucesso. 

### <a name="workaround"></a>Solução

Se utilizar séries ou tamanhos VM mais antigos, como Dv1, DSv1, Av1, D15v2 ou DS15v2, considere mudar-se para versões mais recentes. Consulte estas recomendações para tamanhos VM específicos.
Se não tiver a opção de usar um tamanho VM diferente, tente implementar para uma região diferente dentro do mesmo geo. Para mais informações sobre os tamanhos VM disponíveis em cada região em https://aka.ms/azure-regions

Se estiver a utilizar zonas de disponibilidade, experimente outra zona dentro da região que possa ter capacidade disponível para o tamanho VM solicitado.

Se o seu pedido de atribuição for grande (mais de 500 núcleos), consulte as orientações nas seguintes secções para dividir o pedido em implementações mais pequenas.

Tente [recolocar o VM](./redeploy-to-new-node-windows.md). A reafectação do VM atribui o VM a um novo cluster na região.

## <a name="allocation-failures-for-older-vm-sizes-av1-dv1-dsv1-d15v2-ds15v2-etc"></a>Falhas de alocação em tamanhos de VM mais antigos [Av1, Dv1, DSv1, D15v2, DS15v2, etc.]

À medida que expandimos a infraestrutura Azure, implementamos hardware de nova geração que é projetado para suportar os mais recentes tipos de máquinas virtuais. Algumas das séries mais antigas VMs não funcionam na nossa infraestrutura de última geração. Por esta razão, os clientes podem ocasionalmente experimentar falhas de atribuição para estes SKUs legados. Para evitar este problema, encorajamos os clientes que estão a usar máquinas virtuais de séries antigas a considerarem mudar-se para os VMs mais recentes equivalentes pelas seguintes recomendações: Estes VMs são otimizados para o hardware mais recente e permitir-lhe-ão tirar partido de melhores preços e desempenho. 

|Legacy VM série/tamanho|Mais recente vm-series/tamanho recomendado|Mais informações|
|----------------------|----------------------------|--------------------|
|Série Av1|[Série Av2](../av2-series.md)|https://azure.microsoft.com/blog/new-av2-series-vm-sizes/
|Série Dv1 ou DSv1 (D1 a D5)|[Série Dv3 ou DSv3](../dv3-dsv3-series.md)|https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/
|Série Dv1 ou DSv1 (D11 a D14)|[Série Ev3 ou ESv3](../ev3-esv3-series.md)|
|D15v2 ou DS15v2|Se estiver a utilizar o modelo de implementação do Resource Manager para tirar partido dos tamanhos VM maiores, considere mudar-se para D16v3/DS16v3 ou D32v3/DS32v3. Estes são projetados para funcionar no hardware de última geração. Se estiver a utilizar o modelo de implementação do Gestor de Recursos para garantir que a sua instância VM está isolada para hardware dedicado a um único cliente, considere mudar-se para os novos tamanhos de VM isolados, E64i_v3 ou E64is_v3, que são projetados para funcionar no hardware de última geração. |https://azure.microsoft.com/blog/new-isolated-vm-sizes-now-available/

## <a name="allocation-failures-for-large-deployments-more-than-500-cores"></a>Falhas de alocação para implementações grandes [mais de 500 núcleos]

Reduza o número de casos do tamanho de VM solicitado e, em seguida, re-teste à operação de implantação. Além disso, para implementações maiores, pode querer avaliar [conjuntos de escala de máquina virtual Azure](../../virtual-machine-scale-sets/index.yml). O número de instâncias VM pode automaticamente aumentar ou diminuir em resposta à procura ou a um horário definido, e você tem uma maior chance de alocação de sucesso porque as implementações podem ser espalhadas por vários clusters. 

## <a name="background-information"></a>Informações de fundo
### <a name="how-allocation-works"></a>Como funciona a atribuição
Os servidores nos datacenters do Azure são divididos em partições em clusters. Normalmente, é tentado um pedido de alocação em vários clusters, mas é possível que determinadas restrições do pedido de alocação forcem a plataforma do Azure para tentar o pedido em apenas um cluster. Neste artigo, vamos referir-nos a isto como "preso a um aglomerado". O diagrama 1 abaixo ilustra o caso de uma alocação normal que é tentada em múltiplos aglomerados. O diagrama 2 ilustra o caso de uma alocação que está fixada ao Cluster 2 porque é aí que está hospedado o CS_1 ou disponibilidade existentes.
![Diagrama de atribuição](./media/virtual-machines-common-allocation-failure/Allocation1.png)

### <a name="why-allocation-failures-happen"></a>Por que as falhas de alocação acontecem
Quando um pedido de atribuição é fixado a um cluster, há uma maior probabilidade de não encontrar recursos gratuitos uma vez que o conjunto de recursos disponíveis é menor. Além disso, se o seu pedido de atribuição for fixado a um cluster, mas o tipo de recurso que solicitou não for suportado por esse cluster, o seu pedido falhará mesmo que o cluster tenha recursos gratuitos. O diagrama 3 seguinte ilustra o caso em que uma dotação fixa falha porque o único cluster candidato não tem recursos livres. O diagrama 4 ilustra o caso em que uma dotação fixa falha porque o único cluster candidato não suporta a dimensão VM solicitada, mesmo que o cluster tenha recursos livres.

![Falha na atribuição de fixação](./media/virtual-machines-common-allocation-failure/Allocation2.png)
