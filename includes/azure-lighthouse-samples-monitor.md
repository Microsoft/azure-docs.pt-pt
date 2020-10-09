---
title: incluir ficheiro
description: incluir ficheiro
services: lighthouse
author: JnHs
ms.service: lighthouse
ms.topic: include
ms.date: 03/30/2020
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: be8aae6308e712449402b002576974743bc125ef
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "80986647"
---
Estes exemplos mostram como utilizar o Azure Monitor para criar alertas para subscrições que foram integradas na gestão de recursos delegados do Azure.

| **Modelo** | **Descrição** |
|---------|---------|
| [monitor-delegation-changes](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/tools/monitor-delegation-changes) | Consulta o último dia de atividade num inquilino de gestão e [informa sobre quaisquer delegações adicionadas ou removidas](../articles/lighthouse/how-to/monitor-delegation-changes.md) (ou tentativas sem êxito).|
| [alert-using-actiongroup](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/alert-using-actiongroup) | Este modelo cria um alerta do Azure e liga-se a um Grupo de Ações existente.|
| [multiple-loganalytics-alerts](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/multiple-loganalytics-alerts) | Cria vários alertas de registo com base nas consultas do Kusto.|
| [delegation-alert-for-customer](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/delegation-alert-for-customer) | Implementa um alerta num inquilino quando um utilizador delega uma subscrição a um inquilino gestor.|
