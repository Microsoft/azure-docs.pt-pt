---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/30/2019
ms.author: zivr
ms.custom: include file
ms.openlocfilehash: fb2eb2d237a1245627bbdb6f4f2eacbb9966a2c6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "81422079"
---
A colocação de VMs numa única região reduz a distância física entre os casos. Colocá-los dentro de uma única zona de disponibilidade também os aproximará fisicamente. No entanto, à medida que a pegada Azure cresce, uma única zona de disponibilidade pode abranger vários centros de dados físicos, o que pode resultar numa latência de rede com impacto na sua aplicação. 

Para obter VMs o mais próximo possível, alcançando a menor latência possível, você deve implantá-los dentro de um grupo de colocação de proximidade.

Um grupo de colocação de proximidade é um agrupamento lógico usado para garantir que os recursos de computação Azure estão fisicamente localizados perto uns dos outros. Os grupos de colocação de proximidade são úteis para cargas de trabalho onde a baixa latência é um requisito.


- Baixa latência entre VMs autónomos.
- Baixa latência entre VMs num único conjunto de disponibilidade ou um conjunto de escala de máquina virtual. 
- Baixa latência entre VMs autónomos, VMs em vários conjuntos de disponibilidade ou conjuntos de várias escalas. Você pode ter vários recursos de computação em um único grupo de colocação para reunir uma aplicação multi-camadas. 
- Baixa latência entre vários níveis de aplicação usando diferentes tipos de hardware. Por exemplo, executar o backend usando série M num conjunto de disponibilidade e a extremidade frontal em uma instância da série D, em um conjunto de escala, em um único grupo de colocação de proximidade.


![Gráfico para grupos de colocação de proximidade](./media/virtual-machines-common-ppg/ppg.png)

## <a name="using-proximity-placement-groups"></a>Usando grupos de colocação de proximidade 

Um grupo de colocação de proximidade é um novo tipo de recurso em Azure. É preciso criar um antes de o utilizar com outros recursos. Uma vez criado, pode ser usado com máquinas virtuais, conjuntos de disponibilidade ou conjuntos de escala de máquina virtual. Especifica um grupo de colocação de proximidade ao criar recursos de computação que fornecem o ID do grupo de colocação de proximidade. 

Também pode mover um recurso existente para um grupo de colocação de proximidade. Ao mover um recurso para um grupo de colocação de proximidade, deve parar (deallocate) o ativo primeiro, uma vez que será redistribuído potencialmente para um centro de dados diferente na região para satisfazer a restrição de colocação. 

No caso de conjuntos de disponibilidade e conjuntos de escala de máquinas virtuais, deve definir o grupo de colocação de proximidade ao nível dos recursos e não às máquinas virtuais individuais. 

Um grupo de colocação de proximidade é uma restrição de colocação em vez de um mecanismo de fixação. Está fixado a um centro de dados específico com a implantação do primeiro recurso para usá-lo. Uma vez que todos os recursos que utilizam o grupo de colocação de proximidade foram interrompidos (deallocated) ou eliminados, ele já não está preso. Portanto, ao utilizar um grupo de colocação de proximidade com várias séries VM, é importante especificar todos os tipos necessários inicialmente num modelo quando possível ou seguir uma sequência de implementação que melhore as suas chances de uma implementação bem sucedida. Se a sua implantação falhar, reinicie a implantação com o tamanho VM que falhou como o primeiro tamanho a ser implantado.

## <a name="what-to-expect-when-using-proximity-placement-groups"></a>O que esperar ao utilizar grupos de colocação de proximidade 
Os grupos de colocação de proximidade oferecem co-localização no mesmo centro de dados. No entanto, como os grupos de colocação de proximidade representam uma restrição adicional de implantação, podem ocorrer falhas de atribuição. Existem poucos casos de utilização em que poderá ver falhas de atribuição ao utilizar grupos de colocação de proximidade:

- Quando se pede a primeira máquina virtual no grupo de colocação de proximidade, o centro de dados é automaticamente selecionado. Em alguns casos, um segundo pedido para uma máquina virtual diferente SKU, pode falhar se não existir nesse centro de dados. Neste caso, é devolvido um erro **exagerado daAllocationRequest.** Para evitar isto, tente alterar a ordem em que implementa os seus SKUs ou tenha ambos os recursos implantados utilizando um único modelo ARM.
-   No caso de cargas de trabalho elásticas, quando adiciona e remove instâncias VM, ter uma restrição de grupo de colocação de proximidade na sua implantação pode resultar numa falha na satisfação do pedido que resulta em erro **de Atribuição.** 
- Parar (deallocate) e iniciar os seus VMs conforme necessário é outra forma de alcançar a elasticidade. Uma vez que a capacidade não é mantida uma vez que você para (deallocate) um VM, reiniciá-lo pode resultar em um erro **de Atribuição.**


## <a name="best-practices"></a>Melhores práticas 
- Para a latência mais baixa, utilize grupos de colocação de proximidade juntamente com a rede acelerada. Para obter mais informações, consulte [Criar uma máquina virtual Linux com rede acelerada](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) ou criar uma máquina virtual Windows com Rede [Acelerada](/azure/virtual-network/create-vm-accelerated-networking-powershell?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
- Implemente todos os tamanhos VM num único modelo. Para evitar a aterragem em hardware que não suporta todos os SKUs e tamanhos VM que você precisa, inclua todos os níveis de aplicação em um único modelo para que todos sejam implantados ao mesmo tempo.
- Se estiver a escrever a sua implementação utilizando o PowerShell, o CLI ou o SDK, poderá obter um erro de atribuição `OverconstrainedAllocationRequest` . Neste caso, deve parar/negociar todos os VM existentes e alterar a sequência no script de implementação para começar com os tamanhos VM SKU/tamanhos que falharam. 
- Ao reutilizar um grupo de colocação existente a partir do qual os VMs foram eliminados, aguarde que a supressão esteja completa antes de lhe adicionar VMs.
- Se a latência for a sua primeira prioridade, coloque os VMs num grupo de colocação de proximidade e toda a solução numa zona de disponibilidade. Mas, se a resiliência é a sua prioridade máxima, espalhe as suas instâncias por várias zonas de disponibilidade (um único grupo de colocação de proximidade não pode abranger zonas).
