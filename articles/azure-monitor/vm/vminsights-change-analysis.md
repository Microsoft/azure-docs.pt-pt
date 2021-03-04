---
title: Alterar análises em insights de VM
description: A integração de insights VM com a integração de Application Change Analysis permite-lhe visualizar quaisquer alterações feitas a uma máquina virtual que possa ter afetado o seu desempenho.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/23/2020
ms.openlocfilehash: 375812813d704eb9b48d0ed8fbbc65dd5e47da49
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102046776"
---
# <a name="change-analysis-in-vm-insights"></a>Alterar análises em insights de VM
A integração de insights VM com a integração [de Application Change Analysis](../app/change-analysis.md) permite-lhe visualizar quaisquer alterações feitas a uma máquina virtual que possa ter afetado o seu desempenho.

## <a name="overview"></a>Descrição Geral
Suponha que tem um VM que está a correr devagar e quer investigar se as recentes alterações na sua configuração podem ter afetado o seu desempenho. Você vê o desempenho do VM usando insights VM e descobre que há um aumento no uso da memória na última hora. A análise de alteração pode ajudá-lo a determinar se quaisquer alterações de configuração efetuadas por esta altura foram a causa deste aumento.

O serviço de Análise de Alterações de Aplicação agrega alterações a partir do [Azure Resource Graph,](../../governance/resource-graph/how-to/get-resource-changes.md) bem como alterações de propriedades aninhadas, tais como regras de segurança de rede do Azure Resource Manager. 

## <a name="enabling-change-analysis"></a>Permitir a análise de mudanças
Para uma análise de mudança a bordo em insights VM, tem de registar o fornecedor de recursos *Microsoft.ChangeAnalysis.* A primeira vez que lançar insights VM ou Análise de Mudança de Aplicação no portal Azure, este fornecedor de recursos será automaticamente registado para si. Application Change Analysis é um serviço gratuito que não tem um desempenho superior aos recursos.

## <a name="view-change-analysis"></a>Ver análise de mudança
A análise de alteração está disponível a partir do **separador Performance** ou **Mapa** dos insights de VM selecionando a opção **Alterar.** 

[![Investigar alterações](media/vminsights-change-analysis/investigate-changes-screenshot.png)](media/vminsights-change-analysis/investigate-changes-screenshot-zoom.png#lightbox)


Clique no botão **Descodudo para** lançar a página de Análise de Alteração de Aplicação filtrada para o VM. Pode rever as alterações listadas para ver se existem algumas que possam ter causado o problema. Se não tiver a certeza sobre uma determinada alteração, pode fazer referência à **Alteração por** coluna para determinar a pessoa que fez a alteração.

[![Alterar detalhes](media/vminsights-change-analysis/change-details-screenshot.png)](media/vminsights-change-analysis/change-details-screenshot.png#lightbox)

## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre [a Análise de Alterações de Aplicações.](../app/change-analysis.md)
- Saiba mais sobre [o Azure Resource Graph](../../governance/resource-graph/how-to/get-resource-changes.md). 

