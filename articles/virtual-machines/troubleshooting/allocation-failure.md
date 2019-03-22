---
title: Resolução de problemas de falhas de alocação de VM do Azure | Documentos da Microsoft
description: Resolver problemas de falhas de alocação ao criar, reiniciar ou redimensionar uma VM no Azure
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
ms.openlocfilehash: 72fbdbcfcd94dd41a67bb81314802dd7314ae463
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58316631"
---
# <a name="troubleshoot-allocation-failures-when-you-create-restart-or-resize-vms-in-azure"></a>Resolver problemas de falhas de alocação ao criar, reiniciar ou redimensionar VMs no Azure

Quando criar uma máquina virtual (VM), às VM paradas e (desalocada) de reiniciar ou redimensionar uma VM, o Microsoft Azure aloca recursos de computação à sua subscrição. Estamos continuamente investem na infraestrutura adicional e recursos para se certificar de que tenhamos sempre todos os tipos VM disponíveis para dar suporte a procura dos clientes. No entanto, ocasionalmente, poderá experienciar falhas de alocação de recursos devido a um crescimento inédito nas procura de serviços do Azure em regiões específicas. Este problema pode ocorrer ao tentar criar ou iniciar VMs numa região, enquanto as VMs apresentam o seguinte código de erro e a mensagem:

**Código de erro**: AllocationFailed ou ZonalAllocationFailed

**Mensagem de erro**: "Falha de alocação. Mas não tem capacidade suficiente para o tamanho VM pedida nesta região. Saiba mais sobre como aumentar as probabilidades de êxito na alocação em https:\//aka.ms/allocation-guidance "

Este artigo explica as causas de algumas das falhas de alocação comuns e sugere possíveis soluções.

Se o problema do Azure não é abordado neste artigo, visite o [fóruns do Azure no MSDN e Stack Overflow](https://azure.microsoft.com/support/forums/). Pode publicar o seu problema nesses fóruns ou a @AzureSupport no Twitter. Além disso, pode enviar um pedido de suporte do Azure ao selecionar o suporte de Get sobre o [suporte do Azure](https://azure.microsoft.com/support/options/) site.

Até que o seu tipo VM preferencial está disponível na sua região preferida, recomendamos aos clientes que ocorrerem problemas de implementação a ter em consideração as diretrizes na tabela seguinte como uma solução temporária. 

Identifique o cenário que melhor corresponda ao seu caso e, em seguida, repita o pedido de atribuição ao utilizar a solução sugerida correspondente para aumentar a probabilidade de êxito na alocação. Em alternativa, pode sempre voltar a tentar mais tarde. Isso é porque recursos suficientes podem ter sido liberados no cluster, região ou zona para acomodar o seu pedido. 


## <a name="resize-a-vm-or-add-vms-to-an-existing-availability-set"></a>Redimensionar uma VM ou adicionar VMs a um conjunto de disponibilidade já existente

### <a name="cause"></a>Causa

Um pedido para redimensionar uma VM ou adicionar que uma VM a um conjunto de disponibilidade existente tem de ser testada no cluster original que aloja a disponibilidade existente definido. O tamanho da VM pedida é suportado pelo cluster, mas o cluster pode atualmente tem capacidade suficiente. 

### <a name="workaround"></a>Solução

Se a VM pode ser parte de um conjunto de disponibilidade diferente, crie uma VM num conjunto (na mesma região) de disponibilidade diferente. Essa nova VM, em seguida, pode ser adicionado à mesma rede virtual.

Pare (desaloque) todas as VMs no mesmo disponibilidade definida, em seguida, reiniciar cada um deles.
Para parar a: Clique em grupos de recursos > [seu grupo de recursos] > recursos > [seu conjunto de disponibilidade] > máquinas virtuais > [sua máquina virtual] > parar.
Depois de parar todas as VMs, selecione a primeira VM e, em seguida, clique em Iniciar.
Este passo certifica-se de que uma nova tentativa de alocação é executada e que um novo cluster pode ser selecionado com capacidade suficiente.

## <a name="restart-partially-stopped-deallocated-vms"></a>Reiniciar VMs parcialmente paradas (desalocadas)

### <a name="cause"></a>Causa

Desalocação parcial significa que parada (desalocado) de um ou mais, mas não todas, as VMs num disponibilidade definida. Ao desalocar uma VM, os recursos associados são lançados. Reiniciar VMs num conjunto de disponibilidade parcialmente desalocada é igual a adicionar as VMs a um conjunto de disponibilidade existente. Por conseguinte, o pedido de atribuição tem de ser testado no cluster original que anfitriões que a disponibilidade existente definido que podem não ter capacidade suficiente.

### <a name="workaround"></a>Solução

Pare (desaloque) todas as VMs no mesmo disponibilidade definida, em seguida, reiniciar cada um deles.
Para parar a: Clique em grupos de recursos > [seu grupo de recursos] > recursos > [seu conjunto de disponibilidade] > máquinas virtuais > [sua máquina virtual] > parar.
Depois de parar todas as VMs, selecione a primeira VM e, em seguida, clique em Iniciar.
Esta ação irá garantir que uma nova tentativa de alocação é executada e que um novo cluster pode ser selecionado com capacidade suficiente.

## <a name="restart-fully-stopped-deallocated-vms"></a>Reiniciar VMs completamente paradas (desalocadas)

### <a name="cause"></a>Causa

Significa de Desalocação completo que parada (desalocada) todas as VMs num conjunto de disponibilidade. O pedido de atribuição para reiniciar estas VMs destina-se a todos os clusters que suportam o tamanho pretendido dentro da região ou zona. Alterar o seu pedido de alocação pelas sugestões neste artigo e repita o pedido para aumentar a probabilidade de êxito na alocação. 

### <a name="workaround"></a>Solução

Se usar mais antigos série VM ou tamanhos, como Dv1, DSv1, Av1, o D15v2 ou DS15v2, considere mover para versões mais recentes. Consulte estas recomendações para tamanhos VM específicos.
Se não tiver a opção para utilizar um tamanho VM diferente, experimente implementar noutra região da mesma área geográfica. Para obter mais informações sobre os tamanhos VM disponíveis em cada região em https://aka.ms/azure-regions

Se estiver a utilizar zonas de disponibilidade, tente outra zona dentro da região que possam ter a capacidade disponível para o tamanho da VM pedida.

Se o seu pedido de alocação é grande (mais de 500 núcleos), consulte a documentação de orientação nas secções seguintes para dividir o pedido para implementações mais pequenas.

## <a name="allocation-failures-for-older-vm-sizes-av1-dv1-dsv1-d15v2-ds15v2-etc"></a>Falhas de alocação para tamanhos de VM mais antigas (Av1, Dv1, DSv1, o D15v2, DS15v2, etc.)

À medida que Expandimos a infraestrutura do Azure, iremos implementar hardware de última geração que foi concebido para suportar os tipos de máquina virtual mais recentes. Algumas das VMs de série mais antigas não são executados na nossa infraestrutura de geração mais recente. Por esse motivo, os clientes, ocasionalmente, poderão experienciar falhas de alocação destes SKUs legados. Para evitar este problema, nós o encorajamos os clientes que estão a utilizar máquinas virtuais de série herdados a considerar mover para as VMs mais recentes equivalentes pelas seguintes recomendações: Estas VMs estão otimizadas para o hardware mais recente e irão permitir-lhe tirar partido da melhor preço e desempenho. 

|Herdados-série/tamanho da VM|Mais recente-série/tamanho da VM recomendado|Mais informações|
|----------------------|----------------------------|--------------------|
|Série Av1|[Série Av2](../windows/sizes-general.md#av2-series)|https://azure.microsoft.com/blog/new-av2-series-vm-sizes/
|DSv1 série ou Dv1 (D1 a D5)|[Dv3 ou série DSv3](../windows/sizes-general.md#dsv3-series-1)|https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/
|DSv1 série ou Dv1 (D11 a D14)|[Ev3 ou série ESv3](../windows/sizes-memory.md#ev3-series)|
|O D15v2 ou DS15v2|Se estiver a utilizar o modelo de implementação do Gestor de theResource para tirar o máximo partido dos tamanhos de VM maior, considere mover para D16v3/DS16v3 ou D32v3/DS32v3. Estas entidades foram concebidas para serem executadas em hardware de geração mais recente. Se estiver a utilizar o modelo de implementação do Resource Manager para se certificar de que a sua instância de VM está isolada do hardware dedicado de um único cliente, considere mover para os novo isolados tamanhos de VM, E64i_v3 ou E64is_v3, que são concebidos para serem executados no hardware mais recente geração. |https://azure.microsoft.com/blog/new-isolated-vm-sizes-now-available/

## <a name="allocation-failures-for-large-deployments-more-than-500-cores"></a>Falhas de alocação para grandes implementações (mais de 500 núcleos)

Reduzir o número de instâncias do tamanho VM necessário e, em seguida, repita a operação de implementação. Além disso, para implementações maiores, convém avaliar [conjuntos de dimensionamento de máquina virtual do Azure](https://docs.microsoft.com/azure/virtual-machine-scale-sets/). O número de instâncias VM automaticamente pode aumentar ou diminuir em resposta à procura ou a um agendamento definido, e tem uma chance maior de êxito na alocação porque as implementações de serem distribuídas por vários clusters. 

## <a name="background-information"></a>Informações gerais
### <a name="how-allocation-works"></a>Como funciona a alocação
Os servidores nos datacenters do Azure são divididos em partições em clusters. Normalmente, é tentado um pedido de alocação em vários clusters, mas é possível que determinadas restrições do pedido de alocação forcem a plataforma do Azure para tentar o pedido em apenas um cluster. Neste artigo, faremos referência a este como "afixado a um cluster." Diagrama 1 abaixo ilustra o caso de uma alocação normal, que é tentada em vários clusters. Diagrama 2 ilustra o caso de uma alocação que tenha afixado ao Cluster 2 porque é onde está alojado o conjunto de disponibilidade ou CS_1 de serviço Cloud existente.
![Diagrama de alocação](./media/virtual-machines-common-allocation-failure/Allocation1.png)

### <a name="why-allocation-failures-happen"></a>Por que as falhas de alocação ocorrem
Quando uma solicitação de alocação é afixada a um cluster, há uma chance maior de falha localizar recursos gratuitos, uma vez que o agrupamento de recursos disponível é menor. Além disso, se o seu pedido de alocação está afixado a um cluster, mas o tipo de recurso solicitado não é suportado por esse cluster, o pedido irá falhar, mesmo que o cluster tem recursos gratuitos. 3 de diagrama seguinte ilustra o caso em que uma alocação afixada falha porque o cluster apenas candidato não tem recursos gratuitos. Diagrama 4 ilustra o caso em que uma alocação afixada falha porque o cluster apenas candidato não suporta o tamanho da VM pedida, mesmo que o cluster tem recursos gratuitos.

![Falha de alocação afixado](./media/virtual-machines-common-allocation-failure/Allocation2.png)


