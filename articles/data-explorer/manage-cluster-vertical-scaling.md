---
title: Gerir a escala vertical do cluster (escala para cima) para corresponder à procura no Azure Data Explorer
description: Este artigo descreve passos para escalar e escalar um cluster do Azure Data Explorer com base na mudança da procura.
author: radennis
ms.author: radennis
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/14/2019
ms.openlocfilehash: 95275598febae2b6b0355a7bc3e512490dae500d
ms.sourcegitcommit: dd3db8d8d31d0ebd3e34c34b4636af2e7540bd20
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/22/2020
ms.locfileid: "77560444"
---
# <a name="manage-cluster-vertical-scaling-scale-up-in-azure-data-explorer-to-accommodate-changing-demand"></a>Gerir a escala vertical do cluster (escala para cima) no Azure Data Explorer para acomodar a mudança da procura

Dimensionar um cluster adequadamente é fundamental para o desempenho do Azure Data Explorer. Um tamanho de cluster estático pode levar à subutilização ou à utilização excessiva, nenhuma das quais é ideal.

Uma vez que a procura num cluster não pode ser prevista com precisão absoluta, uma melhor abordagem é *escalar* um cluster, adicionando e removendo a capacidade e os recursos da CPU com a mudança da procura. 

Existem dois fluxos de trabalho para escalar um cluster Azure Data Explorer:

* [Escala horizontal,](manage-cluster-horizontal-scaling.md)também chamada de escala dentro e fora.
* Escala vertical, também chamada de escala para cima e para baixo.

Este artigo explica o fluxo de trabalho vertical de escalação:

## <a name="configure-vertical-scaling"></a>Configurar escala vertical

1. No portal Azure, aceda ao seu recurso de cluster Azure Data Explorer. Em **Definições,** **selecione Scale up**.

1. Na janela **Scale up,** você verá uma lista de SKUs disponíveis para o seu cluster. Por exemplo, na figura seguinte, apenas quatro UsK estão disponíveis.

    ![Aumentar verticalmente](media/manage-cluster-vertical-scaling/scale-up.png)

    As SKUs são desativadas porque são o Atual SKU, ou não estão disponíveis na região onde o cluster está localizado.

1. Para alterar o seu SKU, selecione um novo SKU e clique em **Selecionar**.

> [!NOTE]
> * O processo de escala vertical pode demorar alguns minutos e durante esse tempo o seu cluster será suspenso. 
> * Escalonar pode prejudicar o desempenho do seu cluster.
> * O preço é uma estimativa das máquinas virtuais do cluster e dos custos do serviço Azure Data Explorer. Outros custos não estão incluídos. Consulte a página de estimativa de [custos](https://dataexplorer.azure.com/AzureDataExplorerCostEstimator.html) do Azure Data Explorer para obter uma estimativa e a página de [preços](https://azure.microsoft.com/pricing/details/data-explorer/) do Azure Data Explorer para obter informações completas sobre preços.

Configurao escala vertical para o seu cluster Azure Data Explorer. Adicione outra regra para uma escala horizontal. Se precisar de assistência com problemas de escala de cluster, [abra um pedido](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) de apoio no portal Azure.

## <a name="next-steps"></a>Passos seguintes

* [Gerencie](manage-cluster-horizontal-scaling.md) a escala horizontal do cluster para reduzir dinamicamente a contagem de instâncias com base em métricas que especifica.

* Monitorize o seu uso de recursos seguindo este artigo: Monitor Ize o desempenho, a saúde e o uso do Explorador de [Dados Do Azure com métricas](using-metrics.md).

