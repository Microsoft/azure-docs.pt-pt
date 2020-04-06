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
ms.openlocfilehash: aacf1c287dca01b5993d35b311eed22e88ef7e87
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2020
ms.locfileid: "80421143"
---
Estes exemplos mostram como utilizar o Azure Monitor para criar alertas para subscrições que foram integradas na gestão de recursos delegados do Azure.

| **Modelo** | **Descrição** |
|---------|---------|
| [monitor-delegation-changes](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/tools/monitor-delegation-changes) | Consulta o último dia de atividade num inquilino de gestão e [informa sobre quaisquer delegações adicionadas ou removidas](../articles/lighthouse/how-to/monitor-delegation-changes.md) (ou tentativas sem êxito).|
| [alert-using-actiongroup](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/alert-using-actiongroup) | Este modelo cria um alerta do Azure e liga-se a um Grupo de Ações existente.|
| [multiple-loganalytics-alerts](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/multiple-loganalytics-alerts) | Cria vários alertas de registo com base nas consultas do Kusto.|
| [delegation-alert-for-customer](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/delegation-alert-for-customer) | Implementa um alerta num inquilino quando um utilizador delega uma subscrição a um inquilino gestor.|
