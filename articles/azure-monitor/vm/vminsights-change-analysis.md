---
title: Alterar análise no Monitor Azure para VMs
description: O Azure Monitor para integração de VMs com a integração de Análise de Alterações de Aplicação permite-lhe visualizar quaisquer alterações feitas a uma máquina virtual que possa ter afetado o seu desempenho.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/23/2020
ms.openlocfilehash: 59799a09436d5968a441f6f17655d3138a2d84d8
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100618241"
---
# <a name="change-analysis-in-azure-monitor-for-vms"></a>Alterar análise no Monitor Azure para VMs
O Azure Monitor para integração de VMs com a integração [de Análise de Alterações](../app/change-analysis.md) de Aplicação permite-lhe visualizar quaisquer alterações feitas a uma máquina virtual que possa ter afetado o seu desempenho.

## <a name="overview"></a>Descrição Geral
Suponha que tem um VM que está a correr devagar e quer investigar se as recentes alterações na sua configuração podem ter afetado o seu desempenho. Vê o desempenho do VM utilizando o Azure Monitor para VMs e descobre que há um aumento no uso da memória na última hora. A análise de alteração pode ajudá-lo a determinar se quaisquer alterações de configuração efetuadas por esta altura foram a causa deste aumento.

O serviço de Análise de Alterações de Aplicação agrega alterações a partir do [Azure Resource Graph,](../../governance/resource-graph/how-to/get-resource-changes.md) bem como alterações de propriedades aninhadas, tais como regras de segurança de rede do Azure Resource Manager. 

## <a name="enabling-change-analysis"></a>Permitir a análise de mudanças
Para uma análise de mudança a bordo no Azure Monitor para VMs, tem de registar o fornecedor de recursos *Microsoft.ChangeAnalysis.* A primeira vez que lançar O Azure Monitor para VMs ou Análise de Alteração de Aplicações no portal Azure, este fornecedor de recursos será automaticamente registado para si. Application Change Analysis é um serviço gratuito que não tem um desempenho superior aos recursos.

## <a name="view-change-analysis"></a>Ver análise de mudança
A análise de alteração está disponível no **separador Performance** ou **Mapa** do Monitor Azure para VMs selecionando a opção **Alterar.** 

[![Investigar alterações](media/vminsights-change-analysis/investigate-changes-screenshot.png)](media/vminsights-change-analysis/investigate-changes-screenshot-zoom.png#lightbox)


Clique no botão **Descodudo para** lançar a página de Análise de Alteração de Aplicação filtrada para o VM. Pode rever as alterações listadas para ver se existem algumas que possam ter causado o problema. Se não tiver a certeza sobre uma determinada alteração, pode fazer referência à **Alteração por** coluna para determinar a pessoa que fez a alteração.

[![Alterar detalhes](media/vminsights-change-analysis/change-details-screenshot.png)](media/vminsights-change-analysis/change-details-screenshot.png#lightbox)

## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre [a Análise de Alterações de Aplicações.](../app/change-analysis.md)
- Saiba mais sobre [o Azure Resource Graph](../../governance/resource-graph/how-to/get-resource-changes.md). 

