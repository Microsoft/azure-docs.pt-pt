---
title: Experiência de Registos Simples no Monitor Azure (Pré-visualização) Microsoft Docs
description: A experiência Simple Logs permite-lhe criar consultas básicas no Azure Monitor sem interagir diretamente com o KQL.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/12/2019
ms.openlocfilehash: 59bcb42edaf7d46498a3514b4f1c919c6e8cc0c4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "77660262"
---
# <a name="simple-logs-experience-in-azure-monitor-preview"></a>Experiência de Logs simples no Azure Monitor (Pré-visualização)
O Azure Monitor proporciona uma [experiência rica](get-started-portal.md) para criar consultas [de log](log-query-overview.md) utilizando a linguagem KQL. No entanto, pode não necessitar de toda a potência do KQL e prefere uma experiência simplificada para requisitos básicos de consulta. A experiência Simple Logs permite-lhe criar consultas básicas sem interagir diretamente com o KQL. Também pode utilizar Os Registos Simples como uma ferramenta de aprendizagem para o KQL, uma vez que necessita de consultas mais sofisticadas.

> [!NOTE]
> Os Registos Simples são atualmente implementados como um teste apenas para Cosmos DB e Key Vaults. Por favor, partilhe a sua experiência com a Microsoft através [do User Voice](https://feedback.azure.com/forums/913690-azure-monitor) para nos ajudar a determinar se vamos expandir e lançar esta funcionalidade.


## <a name="scope"></a>Âmbito
A experiência Simple Logs recolhe dados da tabela *AzureDiagnostics*, *AzureMetrics*e *AzureActivity* para o recurso selecionado. 

## <a name="using-simple-logs"></a>Usando registos simples
Navegue para qualquer Cosmos DB ou Key Vault na sua assinatura Azure com [definições de diagnóstico configuradas para recolher registos num espaço de trabalho do Log Analytics](../platform/resource-logs-collect-storage.md). Clique em **Registos** no menu **de Monitorização** para abrir a experiência Registos Simples.

![Menu](media/simple-logs/menu.png)

Selecione um **Campo** e um **Operador** e especifique um **Valor** para comparação. Clique **+** e especifique **E/Ou** para adicionar critérios adicionais.

![Critérios](media/simple-logs/criteria.png)

Clique em **Executar** para ver os resultados da consulta.

## <a name="view-and-edit-kql"></a>Ver e editar KQL
Selecione **o editor de consulta** para abrir o KQL gerado pela consulta De Registos Simples na experiência completa do Log Analytics. 

![Editor de consultas](media/simple-logs/query-editor.png)

Pode editar diretamente o KQL e utilizar outras funcionalidades no Log Analytics, como filtros para aperfeiçoar ainda mais os seus resultados.

![Editar KQL](media/simple-logs/edit-kql.png)


## <a name="next-steps"></a>Próximos passos

- Preencha um tutorial sobre [a utilização do Log Analytics no portal Azure](get-started-portal.md).
- Complete um tutorial sobre [consultas de registo de escrita](get-started-portal.md).
