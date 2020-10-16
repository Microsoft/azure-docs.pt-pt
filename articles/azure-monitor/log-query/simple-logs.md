---
title: Experiência de Registos Simples no Monitor Azure (Pré-visualização) Microsoft Docs
description: A experiência Simple Logs permite-lhe criar consultas básicas no Azure Monitor sem interagir diretamente com o KQL.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/12/2019
ms.openlocfilehash: 4926e18aa6b00fe36608843ea5253903ace774e2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90089113"
---
# <a name="simple-logs-experience-in-azure-monitor-preview"></a>Experiência de Logs simples no Azure Monitor (Pré-visualização)
O Azure Monitor proporciona uma [experiência rica](get-started-portal.md) para criar consultas [de log](log-query-overview.md) utilizando a linguagem KQL. No entanto, pode não necessitar de toda a potência do KQL e prefere uma experiência simplificada para requisitos básicos de consulta. A experiência Simple Logs permite-lhe criar consultas básicas sem interagir diretamente com o KQL. Também pode utilizar Os Registos Simples como uma ferramenta de aprendizagem para o KQL, uma vez que necessita de consultas mais sofisticadas.

> [!NOTE]
> Os Registos Simples são atualmente implementados como um teste apenas para Cosmos DB e Key Vaults. Por favor, partilhe a sua experiência com a Microsoft através [do User Voice](https://feedback.azure.com/forums/913690-azure-monitor) para nos ajudar a determinar se vamos expandir e lançar esta funcionalidade.


## <a name="scope"></a>Âmbito
A experiência Simple Logs recolhe dados da tabela *AzureDiagnostics*, *AzureMetrics*e *AzureActivity* para o recurso selecionado. 

## <a name="using-simple-logs"></a>Usando registos simples
Navegue para qualquer Cosmos DB ou Key Vault na sua assinatura Azure com [definições de diagnóstico configuradas para recolher registos num espaço de trabalho do Log Analytics](../platform/resource-logs.md#send-to-azure-storage). Clique em **Registos** no menu **de Monitorização** para abrir a experiência Registos Simples.

![A screenshot mostra o menu de monitorização com registos selecionados.](media/simple-logs/menu.png)

Selecione um **Campo** e um **Operador** e especifique um **Valor** para comparação. Clique **+** e especifique **E/Ou** para adicionar critérios adicionais.

![A screenshot mostra a pesquisa no painel de registos com registos simples selecionados.](media/simple-logs/criteria.png)

Clique em **Executar** para ver os resultados da consulta.

## <a name="view-and-edit-kql"></a>Ver e editar KQL
Selecione **o editor de consulta** para abrir o KQL gerado pela consulta De Registos Simples na experiência completa do Log Analytics. 

![Editor de consultas](media/simple-logs/query-editor.png)

Pode editar diretamente o KQL e utilizar outras funcionalidades no Log Analytics, como filtros para aperfeiçoar ainda mais os seus resultados.

![Editar KQL](media/simple-logs/edit-kql.png)


## <a name="next-steps"></a>Passos seguintes

- Preencha um tutorial sobre [a utilização do Log Analytics no portal Azure](get-started-portal.md).
- Complete um tutorial sobre [consultas de registo de escrita](get-started-portal.md).
