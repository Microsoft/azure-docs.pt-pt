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
ms.openlocfilehash: 3215f5952daef053c94432bc8fdef15e1775047a
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73171096"
---
Colocar VMs em uma única região reduz a distância física entre as instâncias. Colocá-los em uma única zona de disponibilidade também os colocará fisicamente juntos. No entanto, à medida que a superfície do Azure cresce, uma única zona de disponibilidade pode abranger vários data centers físicos, o que pode resultar em uma latência de rede impactando seu aplicativo. 

Para obter as VMs o mais próximo possível, alcançando a menor latência possível, você deve implantá-las em um grupo de posicionamento de proximidade.

Um grupo de posicionamento de proximidade é um agrupamento lógico usado para garantir que os recursos de computação do Azure estejam fisicamente localizados próximos um do outro. Os grupos de posicionamento de proximidade são úteis para cargas de trabalho em que a baixa latência é um requisito.


- Baixa latência entre VMs autônomas.
- Baixa latência entre VMs em um único conjunto de disponibilidade ou um conjunto de dimensionamento de máquinas virtuais. 
- Baixa latência entre VMs autônomas, VMs em vários conjuntos de disponibilidade ou vários conjuntos de dimensionamento. Você pode ter vários recursos de computação em um único grupo de posicionamento para reunir um aplicativo em várias camadas. 
- Baixa latência entre várias camadas de aplicativo usando tipos de hardware diferentes. Por exemplo, executar o back-end usando a série M em um conjunto de disponibilidade e o front-end em uma instância da série D, em um conjunto de dimensionamento, em um único grupo de posicionamento de proximidade.


![Gráfico para grupos de posicionamento de proximidade](./media/virtual-machines-common-ppg/ppg.png)

## <a name="using-proximity-placement-groups"></a>Usando grupos de posicionamento de proximidade 

Um grupo de posicionamento de proximidade é um novo tipo de recurso no Azure. Você precisa criar um antes de usá-lo com outros recursos. Depois de criado, ele pode ser usado com máquinas virtuais, conjuntos de disponibilidade ou conjuntos de dimensionamento de máquinas virtuais. Você especifica um grupo de posicionamento de proximidade ao criar recursos de computação, fornecendo a ID de grupo de posicionamento de proximidade. 

Você também pode mover um recurso existente para um grupo de posicionamento de proximidade. Ao mover um recurso para um grupo de posicionamento de proximidade, você deve parar (desalocar) o ativo primeiro, pois ele será reimplantado potencialmente em um data center diferente na região para atender à restrição de colocalização. 

No caso de conjuntos de disponibilidade e conjuntos de dimensionamento de máquinas virtuais, você deve definir o grupo de posicionamento de proximidade no nível de recurso em vez de máquinas virtuais individuais. 

Um grupo de posicionamento de proximidade é uma restrição de colocalização em vez de um mecanismo de fixação. Ele é fixado em um data center específico com a implantação do primeiro recurso para usá-lo. Depois que todos os recursos que usam o grupo de posicionamento de proximidade tiverem sido interrompidos (desalocados) ou excluídos, eles não serão mais fixados. Portanto, ao usar um grupo de posicionamento de proximidade com várias séries de VM, é importante especificar todos os tipos necessários antecipadamente em um modelo quando possível ou seguir uma sequência de implantação que melhorará suas chances de uma implantação bem-sucedida. Se sua implantação falhar, reinicie a implantação com o tamanho da VM que falhou como o primeiro tamanho a ser implantado.


## <a name="best-practices"></a>Melhores práticas 
- Para a menor latência, use grupos de posicionamento de proximidade junto com a rede acelerada. Para obter mais informações, consulte [criar uma máquina virtual Linux com rede acelerada](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) ou [criar uma máquina virtual do Windows com rede acelerada](/azure/virtual-network/create-vm-accelerated-networking-powershell?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
- Implante todos os tamanhos de VM em um único modelo. Para evitar a aterrissagem de hardware que não dá suporte a todos os tamanhos e SKUs de VM necessários, inclua todas as camadas de aplicativo em um único modelo para que todas sejam implantadas ao mesmo tempo.
- Se você estiver criando scripts para sua implantação usando o PowerShell, a CLI ou o SDK, poderá obter um erro de alocação `OverconstrainedAllocationRequest`. Nesse caso, você deve parar/desalocar todas as VMs existentes e alterar a sequência no script de implantação para começar com os tamanhos/SKU da VM que falharam. 
- Ao reutilizar um grupo de posicionamento existente do qual as VMs foram excluídas, aguarde até que a exclusão seja concluída completamente antes de adicionar VMs a ela.
- Se a latência for sua primeira prioridade, coloque as VMs em um grupo de posicionamento de proximidade e toda a solução em uma zona de disponibilidade. Mas, se a resiliência for sua prioridade máxima, espalhe suas instâncias entre várias zonas de disponibilidade (um único grupo de posicionamento de proximidade não pode abranger zonas).