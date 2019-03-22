---
title: Capacidade de folha de cálculo de planeamento para o Azure Stack | Documentos da Microsoft
description: Saiba mais sobre a folha de cálculo para implementações do Azure Stack de planeamento da capacidade.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/11/2018
ms.author: jeffgilb
ms.reviewer: prchint
ms.lastreviewed: 12/11/2018
ms.openlocfilehash: 459398f5f40ae5fb28dfa4ac73b94aecca7304eb
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58099421"
---
# <a name="azure-stack-capacity-planner"></a>O Azure Stack Capacity Planner
O planeador de capacidade do Azure Stack é uma folha de cálculo utilizada para planejamento de capacidade de recursos do Azure Stack. O planeador de capacidade fornece a capacidade de criar várias alocações de recursos de computação e veja como se encaixa numa seleção de ofertas de hardware. São fornecidas instruções detalhadas para a utilização da Calculadora do Azure Stack abaixo.

## <a name="worksheet-descriptions"></a>Descrições de folha de cálculo
Segue-se um breve resumo de folhas de cálculo de contidas na folha de cálculo Planeador de capacidade de pilha do Azure que pode ser transferida a partir [ https://aka.ms/azstackcapacityplanner ](https://aka.ms/azstackcapacityplanner):

|Nome do separador|Descrição|
|-----|-----|
|Exclusão de responsabilidade de versão|Visão geral resumida da finalidade da Calculadora, número de versão e data de lançamento.|
|Instruções|Fornece instruções detalhadas para a utilização do Planeador de capacidade de pilha do Azure.|
|DefinedSolutionSKUs|Com várias coluna tabela que contém definições de hardware até cinco. As entradas nesta folha são exemplos. A intenção é que o utilizador será alterado detalhes para corresponder as configurações do sistema que está a ser consideradas para utilização ou compra.|
|DefineByVMFootprint|Encontre o SKU de hardware adequado através da criação de uma coleção de vários tamanhos e as quantidades de VMs.|
|DefineByWorkloadFootprint|Encontre o SKU de hardware adequado através da criação de uma coleção de cargas de trabalho do Azure Stack.|
|  |  |

## <a name="definedsolutionskus-instructions"></a>Instruções de DefinedSolutionSKUs
Esta folha de cálculo contém exemplos de definição de hardware até cinco. Alterar detalhes de acordo com as configurações de sistema a ser consideradas para utilização ou compra.

### <a name="hardware-selections-provided-by-authorized-hardware-partners"></a>Seleções de hardware fornecidas pelos parceiros de Hardware autorizado
Azure Stack é oferecido como um sistema integrado com o software instalado pelos parceiros de solução. Estes parceiros de solução terão suas próprias, versões autoritativas do Azure Stack, ferramentas e as ferramentas de planeamento da capacidade deve ser usada para finalizar discussões da capacidade de solução.

### <a name="multiple-ways-to-model-computing-resources"></a>Várias formas para modelar a recursos de computação
Os blocos modulares básicos usados para o recurso de modelagem dentro do Planeador de implementações do Azure Stack são vários tamanhos de máquinas virtuais do Azure Stack (VMs). Estas variam de VMs de tamanho mais pequeno, de "0 básica", até a VM de maior atual, "Standard_Fsv2." Com base nas suas necessidades, várias quantidades de diferentes VMs podem ser utilizadas para criar as alocações de recursos de computação com esta ferramenta de duas formas diferentes.

1. O utilizador do Planeador de implementações seleciona uma oferta de hardware específico e vê que combinações de vários recursos se encaixa neste recurso de hardware. 

2. O utilizador do Planeador de implementações cria uma combinação de alocações de VM e, em seguida, permite que mostra a Calculadora de recursos do Azure que os SKUs de hardware disponíveis são capazes de suportar esta configuração de VM.

Essa ferramenta fornece dois métodos de alocação de recursos da VM; seja como uma única coleção de alocações de recursos VM ou como uma coleção de até seis diferentes configurações de carga de trabalho. Cada configuração de carga de trabalho pode conter uma alocação diferente de recursos VM disponíveis. Informações detalhadas para criar e utilizar cada um desses modelos de alocação são fornecidas abaixo. Apenas valores contidos no plano de fundo não sombreados células ou dentro de listas de pendente SKU nesta folha de cálculo devem ser modificados. As alterações efetuadas no células com sombreado poderão interromper os cálculos de recursos.


## <a name="definebyvmfootprint-instructions"></a>Instruções de DefineByVMFootprint
Para criar um modelo com uma coleção única de vários tamanhos e as quantidades de VMs, selecione o separador "DefineByVMFootprint" e siga essa seqüência de etapas.

1. No canto superior direito desta folha de cálculo, use os controles de caixa de lista fornecida de pendente para selecionar um número inicial de servidores (entre 4 e 16) pretende que sejam instaladas em cada sistema de hardware (SKU). Este número de servidores pode ser modificado em qualquer altura durante o processo de modelagem para ver como isso afeta geral recursos disponíveis para o modelo de alocação de recursos.
2. Se deseja modelar várias alocações de recursos VM em relação a uma configuração de hardware específico, encontre a caixa de listagem de pendente azul diretamente abaixo a etiqueta "SKU atual" no canto superior direito da página. Obter esta caixa de lista e selecione o SKU de hardware pretendido.
3. Agora está pronto para começar a adicionar várias VMs de tamanho para o seu modelo. Para incluir um determinado tipo VM, introduza um valor de quantidade na caixa delineada de azul à esquerda dessa entrada VM.

   > [!NOTE]
   > Armazenamento total da VM refere-se para a capacidade total do disco de dados da VM (número de discos suportados * a capacidade máxima de um único disco (1 TB)). Com base nos indicadores de configuração preenchemos a tabela de configurações de armazenamento disponível, para que pode escolher o nível desejado de recursos de armazenamento para cada VM do Azure Stack. No entanto, é importante observar que pode adicionar ou alterar a tabela de configurações de armazenamento disponíveis conforme necessário.<br><br>Cada VM começa com um armazenamento de temp local inicialmente atribuída. Para refletir o aprovisionamento dinâmico de armazenamento temporário, o número de local temp pode ser alterado a qualquer destino na lista pendente de menu, incluindo o valor máximo permitido de armazenamento temporário.

4. À medida que adiciona as VMs, verá os gráficos que mostram os recursos disponíveis do SKU a alteração. Isto permite-lhe ver os efeitos da adição de diversos tamanhos e as quantidades de VMs durante o processo de modelagem. Outra forma de ver o efeito das alterações é ver os números de consumida e continuam disponíveis listados diretamente abaixo da lista de VMs de disponibilidade. Estes números refletem valores estimados com base no SKU de hardware atualmente selecionada.
5. Depois de criar o seu conjunto de VMs, pode encontrar o SKU de hardware sugerido ao clicar no botão "Sugeridas SKU" encontrado no canto superior direito da página, diretamente abaixo a etiqueta "SKU atual". Utilizar este botão, pode, em seguida, modificar suas configurações de VM e ver qual hardware suporta cada configuração.


## <a name="definebyworkloadfootprint-instructions"></a>Instruções de DefineByWorkloadFootprint
Para criar um modelo com uma coleção de cargas de trabalho do Azure Stack, selecione o separador "DefineByWorkloadFootprint" e siga essa seqüência de etapas. Cargas de trabalho de pilha do Azure são criadas usando os recursos VM disponíveis.   

> [!TIP]
> Para alterar o tamanho de armazenamento fornecido para uma VM do Azure Stack, consulte a nota na etapa três na secção anterior.

1. No canto superior direito desta página, use os controles de caixa de lista fornecida de pendente para selecionar um número inicial de servidores (entre 4 e 16) pretende que sejam instaladas em cada sistema de hardware (SKU).
2. Se deseja modelar várias alocações de recursos VM em relação a uma configuração de hardware específico, encontre a caixa de listagem de pendente azul diretamente abaixo a etiqueta "SKU atual" no canto superior direito da página. Obter esta caixa de lista e selecione o SKU de hardware pretendido.
3. Selecione o tamanho de armazenamento adequado para cada uma das suas VMs de pilha do Azure pretendido na página DefineByVMFootprint, conforme descrito acima no terceiro passo de DefineByVMFootprint instruções. O tamanho de armazenamento por VM é definido na folha de DefineByVMFootprint.
4. A iniciar no canto superior esquerdo da página DefineByWorkloadFootprint, crie configurações para até seis diferentes tipos de carga de trabalho ao introduzir a quantidade de cada tipo VM contido essa carga de trabalho. Isso é feito colocando em valores numéricos na coluna diretamente abaixo do nome da carga de trabalho. Nomes de carga de trabalho podem ser modificados para refletir o tipo de cargas de trabalho que serão suportadas por esta configuração específica.
5. Pode incluir uma quantidade específica de cada tipo de carga de trabalho ao introduzir um valor na parte inferior dessa coluna diretamente abaixo a etiqueta "Quantidade".
6. Assim que tem sido criadas tipos de carga de trabalho e as quantidades, clicar em "SKU sugerida" botão encontrado no canto superior direito da página, diretamente abaixo da etiqueta "SKU atual", fará com que o SKU de menor com recursos suficientes para suportar este geral configuração de cargas de trabalho a ser exibido.
7. Ainda mais a Modelagem pode ser efetuada ao modificar o número de servidores selecionados para um hardware SKU, ou alterar as alocações de VM ou quantidades dentro de suas configurações de carga de trabalho. Os gráficos associados irão apresentar um feedback imediato que mostra como as alterações afetam o consumo de recursos global.
8. Quando estiver satisfeito com as suas alterações, clicar no botão de "Sugeridas SKU" novamente apresentará o SKU sugerido para a nova configuração.


## <a name="next-steps"></a>Passos Seguintes
Saiba mais sobre o [considerações de integração do Centro de dados para o Azure Stack](azure-stack-datacenter-integration.md)
