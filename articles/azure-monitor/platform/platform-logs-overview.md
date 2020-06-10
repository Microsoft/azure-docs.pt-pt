---
title: Visão geral dos registos da plataforma Azure ! Microsoft Docs
description: Visão geral dos registos no Azure Monitor que fornecem dados ricos e frequentes sobre o funcionamento de um recurso Azure.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 12/19/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: c61a7b46a2f1cc6fa518e5001d5e1905946d5e8e
ms.sourcegitcommit: 1de57529ab349341447d77a0717f6ced5335074e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/09/2020
ms.locfileid: "84610088"
---
# <a name="overview-of-azure-platform-logs"></a>Visão geral dos registos da plataforma Azure
Os registos da plataforma fornecem informações detalhadas de diagnóstico e auditoria para os recursos da Azure e para a plataforma Azure em que dependem. São geradas automaticamente, embora seja necessário configurar certos registos da plataforma para serem reencaminhados para um ou mais destinos para serem retidos. Este artigo fornece uma visão geral dos registos da plataforma, incluindo as informações que fornecem e como pode configurá-los para recolha e análise.

## <a name="types-of-platform-logs"></a>Tipos de registos de plataformas
A tabela que se segue lista os registos específicos da plataforma que estão disponíveis em diferentes camadas de Azure.

| Registar | Camada | Descrição |
|:---|:---|:---|
| Registos do recurso | Recursos do Azure | Forneça informações sobre as operações que foram realizadas dentro de um recurso Azure (o plano de *dados),* por exemplo, obter um segredo de um Cofre-Chave ou fazer um pedido para uma base de dados. O conteúdo dos registos de recursos varia consouros e de tipo de recurso.<br><br>*Os registos de recursos eram previamente referidos como registos de diagnóstico.*  |
| Registo de atividades | Subscrição do Azure | Fornece informações sobre as operações de cada recurso Azure na subscrição a partir do exterior (*o plano de gestão*) para além de atualizações sobre eventos de Saúde de Serviço. Utilize o Registo de Atividades, para determinar o _que_, _quem_, e _quando_ para quaisquer operações de escrita (PUT, POST, DELETE) assumiu os recursos na sua subscrição. Também pode compreender o estado da operação e outras propriedades relevantes.  Há um único registo de Atividade para cada subscrição do Azure. |
| Registos de diretório ativo Azure | Inquilino do Azure |  Contém o histórico de atividade de inscrição e pista de auditoria de alterações escoradas no Diretório Ativo Azure para um inquilino em particular. Veja [quais são os relatórios do Azure Ative Directory para](../../active-directory/reports-monitoring/overview-reports.md) uma descrição completa dos Registos de Diretório Ativo da Azure.   |

> [!NOTE]
> O Registo de Atividades Azure destina-se principalmente a atividades que ocorrem no Azure Resource Manager. Não rastreia recursos utilizando o modelo Classic/RDFE. Alguns tipos de recursos clássicos têm um fornecedor de recursos proxy no Azure Resource Manager (por exemplo, Microsoft.ClassicCompute). Se interagir com um tipo de recurso Clássico através do Azure Resource Manager utilizando estes fornecedores de recursos proxy, as operações aparecem no Registo de Atividades. Se interagir com um tipo de recurso clássico fora dos proxies do Azure Resource Manager, as suas ações só são registadas no Registo de Operação. O Registo de Operação pode ser navegado numa secção separada do portal.

![Descrição geral dos registos da plataforma](media/platform-logs-overview/logs-overview.png)




## <a name="viewing-platform-logs"></a>Registos de plataforma de visualização
Existem diferentes opções para visualizar e analisar os diferentes registos da plataforma Azure.

- Ver o registo de Atividade no portal Azure e aceder a eventos de powerShell e CLI. Consulte [os eventos de registo de atividades Azure](activity-log-view.md) para obter mais detalhes. 
- Ver relatórios de Segurança e Atividade do Diretório Ativo Azure no portal Azure. Veja [quais são os relatórios do Azure Ative Directory?](../../active-directory/reports-monitoring/overview-reports.md)  para detalhes.
- Os registos de recursos são gerados automaticamente por recursos Azure suportados, mas não estão disponíveis para serem vistos a menos que os envie para um [destino.](#destinations) 

## <a name="destinations"></a>Destinos
Pode enviar registos de plataforma para um ou mais destinos na tabela seguinte, dependendo dos seus requisitos de monitorização. Configure destinos para registos de plataformas [criando uma definição de Diagnóstico](diagnostic-settings.md).

| Destino | Cenário | Referências |
|:---|:---|:---|:---|
| Área de trabalho do Log Analytics | Analise os registos com outros dados de monitorização e aproveite as funcionalidades do Azure Monitor, tais como consultas de registo e alertas. | [Registos de atividades e registos de recursos](resource-logs-collect-workspace.md)<br>[Registos de diretório ativo Azure](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md) |
| Storage do Azure | Arquivar os registos para auditoria, análise estática ou cópia de segurança. |[Registos de atividades e registos de recursos](archive-diagnostic-logs.md)<br>[Registos de diretório ativo Azure](../../active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md) |
| Hub de eventos | Transmita os registos para sistemas de registo e telemetria de terceiros.  |[Registos de atividades e registos de recursos](resource-logs-stream-event-hubs.md)<br>[Registos de diretório ativo Azure](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md) |



## <a name="next-steps"></a>Próximos passos

* [Ver o esquema de registo de atividades para diferentes categorias](activity-log-schema.md)
* [Ver o esquema de registo de recursos para diferentes serviços Azure](diagnostic-logs-schema.md)
