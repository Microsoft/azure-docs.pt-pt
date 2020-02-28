---
title: Experiência de Registos Simples no Monitor Azure (Pré-visualização) / Microsoft Docs
description: A experiência Simple Logs permite-lhe criar consultas básicas no Monitor Azure sem interagir diretamente com a KQL.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/12/2019
ms.openlocfilehash: 59bcb42edaf7d46498a3514b4f1c919c6e8cc0c4
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77660262"
---
# <a name="simple-logs-experience-in-azure-monitor-preview"></a>Experiência de Registos Simples no Monitor Azure (Pré-visualização)
O Azure Monitor proporciona uma [experiência rica](get-started-portal.md) para criar consultas de [log](log-query-overview.md) utilizando a língua KQL. No entanto, pode não necessitar de toda a potência da KQL e preferir uma experiência simplificada para requisitos básicos de consulta. A experiência Simple Logs permite-lhe criar consultas básicas sem interagir diretamente com a KQL. Também pode utilizar registos simples como ferramenta de aprendizagem para a KQL, uma vez que necessita de consultas mais sofisticadas.

> [!NOTE]
> A Simple Logs é atualmente implementada como um teste apenas para Cosmos DB e Key Vaults. Por favor, partilhe a sua experiência com a Microsoft através do [User Voice](https://feedback.azure.com/forums/913690-azure-monitor) para nos ajudar a determinar se vamos expandir e lançar esta funcionalidade.


## <a name="scope"></a>Âmbito
A experiência Simple Logs recupera dados da tabela *AzureDiagnostics,* *AzureMetrics*e *AzureActivity* para o recurso selecionado. 

## <a name="using-simple-logs"></a>Usando registos simples
Navegue para qualquer Cosmos DB ou Key Vault na sua subscrição Azure com configurações de [diagnóstico configuradas para recolher registos num espaço](../platform/resource-logs-collect-storage.md)de trabalho de Log Analytics . Clique em **Registos** no menu **de Monitorização** para abrir a experiência De Registos Simples.

![Menu](media/simple-logs/menu.png)

Selecione um **Campo** e um **Operador** e especifique um **Valor** para comparação. Clique em **+** e especifique **E/Ou** para adicionar critérios adicionais.

![Critérios](media/simple-logs/criteria.png)

Clique **em Correr** para ver os resultados da consulta.

## <a name="view-and-edit-kql"></a>Ver e editar KQL
Selecione **O editor** da Consulta para abrir o KQL gerado pela consulta de Registos Simples em toda a experiência Log Analytics. 

![Editor de consulta](media/simple-logs/query-editor.png)

Pode editar diretamente o KQL e utilizar outras funcionalidades no Log Analytics, como filtros para aperfeiçoar ainda mais os seus resultados.

![Editar KQL](media/simple-logs/edit-kql.png)


## <a name="next-steps"></a>Passos seguintes

- Complete um tutorial sobre [a utilização de Log Analytics no portal Azure](get-started-portal.md).
- Complete um tutorial sobre consultas de [registo de escrita.](get-started-portal.md)
