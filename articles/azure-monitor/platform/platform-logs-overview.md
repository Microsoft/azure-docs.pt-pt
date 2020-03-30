---
title: Visão geral dos registos da plataforma Azure [ Microsoft Docs
description: Visão geral dos registos no Monitor Azure que fornecem dados ricos e frequentes sobre o funcionamento de um recurso Azure.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 12/19/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: 89de6b3737c8a1e91832aba8f749078806b64e90
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77659325"
---
# <a name="overview-of-azure-platform-logs"></a>Visão geral dos registos da plataforma Azure
Os registos da plataforma fornecem informações detalhadas de diagnóstico e auditoria para os recursos do Azure e da plataforma Azure de que dependem. São gerados automaticamente, embora seja necessário configurar determinados registos de plataformas para serem encaminhados para um ou mais destinos a serem retidos. Este artigo fornece uma visão geral dos registos da plataforma, incluindo as informações que fornecem e como pode configurá-los para recolha e análise.

## <a name="types-of-platform-logs"></a>Tipos de registos de plataformas
A tabela seguinte lista os registos específicos da plataforma que estão disponíveis em diferentes camadas do Azure.

| Registar | Camada | Descrição |
|:---|:---|:---|
| Registos do recurso | Recursos do Azure | Forneça informações sobre as operações que foram realizadas dentro de um recurso Azure (o avião de *dados),* por exemplo, obter um segredo de um Cofre chave ou fazer um pedido para uma base de dados. O conteúdo dos registos de recursos varia pelo tipo de serviço e recursos Azure.<br><br>*Os registos de recursos foram anteriormente referidos como registos de diagnóstico.*  |
| Registo de atividades | Subscrição do Azure | Fornece informações sobre as operações em cada recurso Azure na subscrição a partir do exterior ( o plano de*gestão*), além de atualizações sobre eventos de Saúde de Serviço. Utilize o Registo de Atividades, para determinar o _quê,_ _quem, quem, e_ _quando_ para quaisquer operações de escrita (PUT, POST, DELETE) assumiu os recursos na sua subscrição. Também pode compreender o estado da operação e outras propriedades relevantes.  Existe um único registo de Atividade para cada subscrição do Azure. |
| Registos de Diretório Ativo Azure | Inquilino do Azure |  Contém o histórico de atividade sessão e rasto de auditoria das alterações efetuadas no Diretório Ativo Azure para um determinado inquilino. Veja o que são relatórios do [Azure Ative Directory para](../../active-directory/reports-monitoring/overview-reports.md) uma descrição completa dos Registos de Diretórios Ativos do Azure.   |

> [!NOTE]
> O Registo de Atividades do Azure destina-se principalmente a atividades que ocorrem no Gestor de Recursos Azure. Não rastreia os recursos utilizando o modelo Classic/RDFE. Alguns tipos de recursos clássicos têm um fornecedor de recursos proxy no Azure Resource Manager (por exemplo, Microsoft.ClassicCompute). Se interagir com um tipo de recurso Clássico através do Gestor de Recursos Azure utilizando estes fornecedores de recursos proxy, as operações aparecem no Registo de Atividades. Se interagir com um tipo de recurso Clássico fora dos proxies do Gestor de Recursos Azure, as suas ações só são registadas no Registo de Operação. O Registo de Operação pode ser navegado numa secção separada do portal.

![Descrição geral dos registos da plataforma](media/platform-logs-overview/logs-overview.png)




## <a name="viewing-platform-logs"></a>Ver registos de plataformas
Existem diferentes opções para visualizar e analisar os diferentes registos da plataforma Azure.

- Veja o log de Atividade no portal Azure e aceda a eventos da PowerShell e CLI. Consulte ver [e recuperar eventos](activity-log-view.md) de registo da Atividade Azure para mais detalhes. 
- Consulte os relatórios de Segurança e Atividade do Diretório Ativo azure no portal Azure. Vê [o que são relatórios do Azure Ative Directory?](../../active-directory/reports-monitoring/overview-reports.md)  para detalhes.
- Os registos de recursos são gerados automaticamente por recursos Azure suportados, mas não estão disponíveis para serem vistos a menos que os envie para um [destino](#destinations). 

## <a name="destinations"></a>Destinos
Pode enviar registos de plataformas para um ou mais destinos na tabela seguinte, dependendo dos seus requisitos de monitorização. Configure destinos para registos de plataformas [criando uma definição de Diagnóstico](diagnostic-settings.md).

| Destino | Cenário | Referências |
|:---|:---|:---|:---|
| Área de trabalho do Log Analytics | Analise os registos com outros dados de monitorização e aproveite as funcionalidades do Monitor Azure, tais como consultas de registo e alertas. | [Registos de registos de atividades e registos de recursos](resource-logs-collect-workspace.md)<br>[Registos de Diretório de Atividade sinuosa](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md) |
| Storage do Azure | Arquivar os registos para auditoria, análise estática ou cópia de segurança. |[Registos de registos de atividades e registos de recursos](archive-diagnostic-logs.md)<br>[Registos de Diretório de Atividade sinuosa](../../active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md) |
| Hub de eventos | Transmita os registos para sistemas de registo e telemetria de terceiros.  |[Registos de registos de atividades e registos de recursos](resource-logs-stream-event-hubs.md)<br>[Registos de Diretório de Atividade sinuosa](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md) |



## <a name="next-steps"></a>Passos seguintes

* [Ver o esquema de log da Atividade para diferentes categorias](activity-log-schema.md)
* [Veja o esquema de registo de recursos para diferentes serviços Azure](diagnostic-logs-schema.md)
