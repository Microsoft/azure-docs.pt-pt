---
title: Experiência de logs simples em Azure Monitor (versão prévia) | Microsoft Docs
description: A experiência de logs simples permite que você crie consultas básicas no Azure Monitor sem interagir diretamente com o KQL.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/12/2019
ms.author: bwren
ms.openlocfilehash: bff80b7083f2fb2e35af7db652849d527a5b8ba4
ms.sourcegitcommit: dd69b3cda2d722b7aecce5b9bd3eb9b7fbf9dc0a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/12/2019
ms.locfileid: "70963450"
---
# <a name="simple-logs-experience-in-azure-monitor-preview"></a>Experiência de logs simples em Azure Monitor (versão prévia)
Azure Monitor fornece uma [experiência rica](get-started-portal.md) para criar [consultas de log](log-query-overview.md) usando a linguagem KQL. Talvez você não precise de toda a potência do KQL, mas prefira uma experiência simplificada para os requisitos básicos de consulta. A experiência de logs simples permite que você crie consultas básicas sem interagir diretamente com o KQL. Você também pode usar logs simples como uma ferramenta de aprendizado para KQL, pois você precisa de consultas mais sofisticadas.

> [!NOTE]
> Atualmente, os logs simples são implementados como um teste para apenas Cosmos DB e cofres de chaves. Compartilhe sua experiência com a Microsoft por meio do [User Voice](https://feedback.azure.com/forums/913690-azure-monitor) para nos ajudar a determinar se iremos expandir e lançar esse recurso.


## <a name="scope"></a>Scope
A experiência de logs simples recupera dados da tabela *AzureDiagnostics*, *AzureMetrics*e *AzureActivity* para o recurso selecionado. 

## <a name="using-simple-logs"></a>Usando logs simples
Navegue até qualquer Cosmos DB ou Key Vault em sua assinatura do Azure com [as configurações de diagnóstico configuradas para coletar logs em um espaço de trabalho log Analytics](../platform/diagnostic-logs-stream-log-store.md). Clique em **logs** no menu **monitoramento** para abrir a experiência de logs simples.

![Menu](media/simple-logs/menu.png)

Selecione um **campo** e um **operador** e especifique um **valor** para comparação. Clique **+** e especifique **e/ou** para adicionar critérios adicionais.

![Critérios](media/simple-logs/criteria.png)

Clique em **executar** para exibir os resultados da consulta.

## <a name="view-and-edit-kql"></a>Exibir e editar KQL
Selecione **Editor de consultas** para abrir o KQL gerado pela consulta de logs simples na experiência de log Analytics completa. 

![Editor de consultas](media/simple-logs/query-editor.png)

Você pode editar diretamente o KQL e usar outros recursos em Log Analytics como filtros para refinar ainda mais seus resultados.

![Editar KQL](media/simple-logs/edit-kql.png)


## <a name="next-steps"></a>Passos Seguintes

- Conclua um tutorial sobre como [usar log Analytics no portal do Azure](get-started-portal.md).
- Conclua um tutorial sobre como [escrever consultas de log](get-started-portal.md).
