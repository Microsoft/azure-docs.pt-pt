---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/30/2019
ms.author: zivr
ms.custom: include file
ms.openlocfilehash: fb2eb2d237a1245627bbdb6f4f2eacbb9966a2c6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81422079"
---
A colocação de VMs numa única região reduz a distância física entre as instâncias. Colocá-los dentro de uma única zona de disponibilidade também os aproximará fisicamente. No entanto, à medida que a pegada Azure cresce, uma única zona de disponibilidade pode abranger vários centros de dados físicos, o que pode resultar numa latência da rede que impacta a sua aplicação. 

Para obter VMs o mais próximo possível, alcançando a menor latência possível, você deve implantá-los dentro de um grupo de colocação de proximidade.

Um grupo de colocação de proximidade é um agrupamento lógico usado para garantir que os recursos da computação Azure estão fisicamente localizados perto uns dos outros. Os grupos de colocação de proximidade são úteis para cargas de trabalho onde a baixa latência é um requisito.


- Baixa latência entre VMs autónomos.
- Baixa Latência entre VMs num único conjunto de disponibilidade ou num conjunto de escala de máquina virtual. 
- Baixa latência entre VMs autónomos, VMs em vários Conjuntos de Disponibilidade ou conjuntos de escala múltiplas. Você pode ter vários recursos de computação em um único grupo de colocação para reunir uma aplicação multi-camadas. 
- Baixa latência entre vários níveis de aplicação usando diferentes tipos de hardware. Por exemplo, executar o backend usando a série M num conjunto de disponibilidade e a extremidade frontal em uma instância da série D, em um conjunto de escala, em um único grupo de colocação de proximidade.


![Gráfico para grupos de colocação de proximidade](./media/virtual-machines-common-ppg/ppg.png)

## <a name="using-proximity-placement-groups"></a>Usando grupos de colocação de proximidade 

Um grupo de colocação de proximidade é um novo tipo de recurso em Azure. Precisa criar um antes de usá-lo com outros recursos. Uma vez criado, pode ser usado com máquinas virtuais, conjuntos de disponibilidade ou conjuntos de escala de máquinavirtual. Especifica um grupo de colocação de proximidade ao criar recursos computacionais que fornecem o ID do grupo de colocação de proximidade. 

Você também pode mover um recurso existente para um grupo de colocação de proximidade. Ao mover um recurso para um grupo de colocação de proximidade, deve parar (desalocar) o ativo primeiro, uma vez que será reimplantado potencialmente num centro de dados diferente na região para satisfazer a restrição de colocalização. 

No caso de conjuntos de disponibilidade e conjuntos de escala de máquinas virtuais, deve definir o grupo de colocação de proximidade ao nível dos recursos e não às máquinas virtuais individuais. 

Um grupo de colocação de proximidade é uma restrição de colocalização em vez de um mecanismo de fixação. Está fixado a um centro de dados específico com a implementação do primeiro recurso para usá-lo. Uma vez que todos os recursos que utilizam o grupo de colocação de proximidade tenham sido interrompidos (deallocated) ou eliminados, este já não está fixado. Portanto, ao utilizar um grupo de colocação de proximidade com várias séries VM, é importante especificar todos os tipos necessários frontalmente num modelo quando possível ou seguir uma sequência de implementação que irá melhorar as suas chances de uma implementação bem sucedida. Se a sua implantação falhar, reinicie a implantação com o tamanho VM que falhou como o primeiro tamanho a ser implantado.

## <a name="what-to-expect-when-using-proximity-placement-groups"></a>O que esperar quando se utilizam os Grupos de Colocação de Proximidade 
Os grupos de colocação de proximidade oferecem co-localização no mesmo centro de dados. No entanto, uma vez que os grupos de colocação de proximidade representam um constrangimento adicional de implantação, podem ocorrer falhas de atribuição. Existem poucos casos de utilização em que pode ver falhas na atribuição ao utilizar grupos de colocação de proximidade:

- Quando se pede a primeira máquina virtual no grupo de colocação de proximidade, o centro de dados é automaticamente selecionado. Em alguns casos, um segundo pedido de uma máquina virtual diferente SKU, pode falhar se não existir nesse centro de dados. Neste caso, é devolvido um erro **de Alocação Excessiva.** Para evitar isto, tente alterar a ordem em que implementa as suas SKUs ou tem ambos os recursos implantados usando um único modelo ARM.
-   No caso de cargas elásticas, quando adiciona e remove as instâncias vM, ter uma restrição de grupo de colocação de proximidade na sua implantação pode resultar numa falha no cumprimento do pedido que resulta num erro de **Alocação Falha.** 
- Parar (desalocar) e iniciar os seus VMs conforme necessário é outra forma de alcançar a elasticidade. Uma vez que a capacidade não é mantida uma vez que pare (desolocar) um VM, reiniciá-lo pode resultar num erro de **Alocação Falha.**


## <a name="best-practices"></a>Melhores práticas 
- Para obter a latência mais baixa, utilize grupos de colocação de proximidade juntamente com o networking acelerado. Para mais informações, consulte [Criar uma máquina virtual Linux com Networking Acelerado](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) ou Criar uma máquina virtual Windows com [Networking Acelerado](/azure/virtual-network/create-vm-accelerated-networking-powershell?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
- Implante todos os tamanhos vm num único modelo. Para evitar a aterragem em hardware que não suporta todos os VM SKUs e tamanhos que você precisa, inclua todos os níveis de aplicação em um único modelo para que todos eles sejam implantados ao mesmo tempo.
- Se estiver a escrever a sua implementação utilizando powerShell, CLI ou `OverconstrainedAllocationRequest`sDK, poderá obter um erro de atribuição . Neste caso, deve parar/desalocar todos os VMs existentes e alterar a sequência no script de implementação para começar com o VM SKU/tamanhos que falharam. 
- Ao reutilizar um grupo de colocação existente a partir do qual os VMs foram eliminados, aguarde que a eliminação esteja completa antes de adicionar VMs.
- Se a latência for a sua primeira prioridade, coloque os VMs num grupo de colocação de proximidade e toda a solução numa zona de disponibilidade. Mas, se a resiliência é a sua prioridade máxima, espalhe os seus casos por múltiplas zonas de disponibilidade (um único grupo de colocação de proximidade não pode abranger zonas).
