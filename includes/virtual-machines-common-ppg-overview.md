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
ms.openlocfilehash: daed8dc62fdfd86ecf785a0bfd83b2b6c0b4cc03
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95559802"
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

Também pode mover um recurso existente para um grupo de colocação de proximidade. Ao mover um recurso para um grupo de colocação de proximidade, deve parar (deallocate) o ativo primeiro, uma vez que será redistribuído potencialmente em um centro de dados diferente na região para satisfazer a restrição de colocação. 

No caso de conjuntos de disponibilidade e conjuntos de escala de máquinas virtuais, deve definir o grupo de colocação de proximidade ao nível dos recursos e não às máquinas virtuais individuais. 

Um grupo de colocação de proximidade é uma restrição de colocação em vez de um mecanismo de fixação. Está fixado a um centro de dados específico com a implantação do primeiro recurso para usá-lo. Uma vez que todos os recursos que utilizam o grupo de colocação de proximidade foram interrompidos (deallocated) ou eliminados, ele já não está preso. Portanto, ao utilizar um grupo de colocação de proximidade com várias séries VM, é importante especificar todos os tipos necessários inicialmente num modelo quando possível ou seguir uma sequência de implementação que melhore as suas chances de uma implementação bem sucedida. Se a sua implantação falhar, reinicie a implantação com o tamanho VM que falhou como o primeiro tamanho a ser implantado.

## <a name="what-to-expect-when-using-proximity-placement-groups"></a>O que esperar ao utilizar grupos de colocação de proximidade 
Os grupos de colocação de proximidade oferecem co-localização no mesmo centro de dados. No entanto, como os grupos de colocação de proximidade representam uma restrição adicional de implantação, podem ocorrer falhas de atribuição. Existem poucos casos de utilização em que poderá ver falhas de atribuição ao utilizar grupos de colocação de proximidade:

- Quando se pede a primeira máquina virtual no grupo de colocação de proximidade, o centro de dados é automaticamente selecionado. Em alguns casos, um segundo pedido para uma máquina virtual diferente SKU, pode falhar se não existir nesse centro de dados. Neste caso, é devolvido um erro **exagerado daAllocationRequest.** Para evitar isto, tente alterar a ordem em que implementa os seus SKUs ou tenha ambos os recursos implantados utilizando um único modelo ARM.
-   No caso de cargas de trabalho elásticas, quando adiciona e remove instâncias VM, ter uma restrição de grupo de colocação de proximidade na sua implantação pode resultar numa falha na satisfação do pedido que resulta em erro **de Atribuição.** 
- Parar (deallocate) e iniciar os seus VMs conforme necessário é outra forma de alcançar a elasticidade. Uma vez que a capacidade não é mantida uma vez que você para (deallocate) um VM, reiniciá-lo pode resultar em um erro **de Atribuição.**

## <a name="planned-maintenance-and-proximity-placement-groups"></a>Grupos de manutenção e colocação de proximidade planeados

Eventos de manutenção planeados, como o desmantelamento de hardware num datacenter Azure, podem potencialmente afetar o alinhamento de recursos em grupos de colocação de proximidade. Os recursos podem ser transferidos para um centro de dados diferente, perturbando as expectativas de colocação e latência associadas ao grupo de colocação de proximidade.

### <a name="check-the-alignment-status"></a>Verifique o estado de alinhamento

Pode fazer o seguinte para verificar o estado de alinhamento dos seus grupos de colocação de proximidade.


- O estado de colocação do grupo de colocação de proximidade pode ser visto usando o portal, CLI e PowerShell.

    -   Ao utilizar o PowerShell, o estado de cosão pode ser obtido utilizando Get-AzProximityPlacementGroup cmdlet, incluindo o parâmetro opcional '-ColocationStatus'.

    -   Ao utilizar o CLI, o estado de comarcação pode ser obtido através `az ppg show` da inclusão do parâmetro opcional "--incluir-estado de colocação".

- Para cada grupo de colocação de proximidade, um **imóvel de estado de colocação** fornece o resumo do estado de alinhamento atual dos recursos agrupados. 

    - **Alinhado**: O recurso está dentro da mesma envolvência de latência do grupo de colocação de proximidade.

    - **Desconhecido:** pelo menos um dos recursos VM são transabilitados. Uma vez reiniciando-os com sucesso, o estado deve voltar a **alinhar-se**.

    - **Não alinhado:** pelo menos um recurso VM não está alinhado com o grupo de colocação de proximidade. Os recursos específicos que não estão alinhados também serão chamados separadamente na secção de adesão

- Para conjuntos de disponibilidade, pode ver informações sobre o alinhamento de VMs individuais na página 'Visão Geral' do Conjunto de Disponibilidade.

- Para conjuntos de escalas, as informações sobre o alinhamento de instâncias individuais podem ser vistas no **separador Instâncias** da página **'Vista Geral'** para o conjunto de escalas. 


### <a name="re-align-resources"></a>Realinar recursos 

Se um grupo de colocação de proximidade `Not Aligned` for, pode parar o deallocate e, em seguida, reiniciar os recursos afetados. Se o VM estiver num conjunto de disponibilidade ou numa balança, todos os VMs no conjunto de disponibilidade ou escala devem ser interrompidos primeiro antes de reiniciá-los.

Se houver uma falha de atribuição devido a restrições de implantação, poderá ter de parar o negócio de todos os recursos no grupo de colocação de proximidade afetado (incluindo os recursos alinhados) primeiro e, em seguida, reiniciá-los para restaurar o alinhamento.

## <a name="best-practices"></a>Melhores práticas 
- Para a latência mais baixa, utilize grupos de colocação de proximidade juntamente com a rede acelerada. Para obter mais informações, consulte [Criar uma máquina virtual Linux com rede acelerada](../articles/virtual-network/create-vm-accelerated-networking-cli.md?toc=%252fazure%252fvirtual-machines%252flinux%252ftoc.json) ou criar uma máquina virtual Windows com Rede [Acelerada](../articles/virtual-network/create-vm-accelerated-networking-powershell.md?toc=%252fazure%252fvirtual-machines%252fwindows%252ftoc.json).
- Implemente todos os tamanhos VM num único modelo. Para evitar a aterragem em hardware que não suporta todos os SKUs e tamanhos VM que você precisa, inclua todos os níveis de aplicação em um único modelo para que todos sejam implantados ao mesmo tempo.
- Se estiver a escrever a sua implementação utilizando o PowerShell, o CLI ou o SDK, poderá obter um erro de atribuição `OverconstrainedAllocationRequest` . Neste caso, deve parar/negociar todos os VM existentes e alterar a sequência no script de implementação para começar com os tamanhos VM SKU/tamanhos que falharam. 
- Ao reutilizar um grupo de colocação existente a partir do qual os VMs foram eliminados, aguarde que a supressão esteja completa antes de lhe adicionar VMs.
- Se a latência for a sua primeira prioridade, coloque os VMs num grupo de colocação de proximidade e toda a solução numa zona de disponibilidade. Mas, se a resiliência é a sua prioridade máxima, espalhe as suas instâncias por várias zonas de disponibilidade (um único grupo de colocação de proximidade não pode abranger zonas).