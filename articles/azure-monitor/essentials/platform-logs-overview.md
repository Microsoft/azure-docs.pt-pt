---
title: Visão geral dos registos da plataforma Azure | Microsoft Docs
description: Visão geral dos registos no Azure Monitor que fornecem dados ricos e frequentes sobre o funcionamento de um recurso Azure.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 12/19/2019
ms.author: bwren
ms.openlocfilehash: 3300b0587cbb6c2193b56c9152af0cb11ea51936
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102033304"
---
# <a name="overview-of-azure-platform-logs"></a>Descrição geral dos registos de plataforma Azure
Os registos da plataforma fornecem informações detalhadas de diagnóstico e auditoria para os recursos da Azure e para a plataforma Azure em que dependem. São geradas automaticamente, embora seja necessário configurar certos registos da plataforma para serem reencaminhados para um ou mais destinos para serem retidos. Este artigo fornece uma visão geral dos registos da plataforma, incluindo as informações que fornecem e como pode configurá-los para recolha e análise.

## <a name="types-of-platform-logs"></a>Tipos de registos de plataformas
A tabela que se segue lista os registos específicos da plataforma que estão disponíveis em diferentes camadas de Azure.

| Registo | Camada | Descrição |
|:---|:---|:---|
| [Registos do recurso](./resource-logs.md) | Recursos do Azure | Forneça informações sobre as operações que foram realizadas dentro de um recurso Azure (o plano de *dados),* por exemplo, obter um segredo de um Cofre-Chave ou fazer um pedido para uma base de dados. O conteúdo dos registos de recursos varia consouros e de tipo de recurso.<br><br>*Os registos de recursos eram previamente referidos como registos de diagnóstico.*  |
| [Registo de atividades](../essentials/activity-log.md) | Subscrição do Azure | Fornece informações sobre as operações de cada recurso Azure na subscrição a partir do exterior (*o plano de gestão*) para além de atualizações sobre eventos de Saúde de Serviço. Utilize o Registo de Atividades, para determinar o _que_, _quem_, e _quando_ para quaisquer operações de escrita (PUT, POST, DELETE) assumiu os recursos na sua subscrição. Há um único registo de Atividade para cada subscrição do Azure. |
| [Registos do Azure Active Directory](../../active-directory/reports-monitoring/overview-reports.md) | Inquilino do Azure |  Contém o histórico de atividade de inscrição e pista de auditoria de alterações escoradas no Diretório Ativo Azure para um inquilino em particular.   |

> [!NOTE]
> O Registo de Atividades Azure destina-se principalmente a atividades que ocorrem no Azure Resource Manager. Não rastreia recursos utilizando o modelo Classic/RDFE. Alguns tipos de recursos clássicos têm um fornecedor de recursos proxy no Azure Resource Manager (por exemplo, Microsoft.ClassicCompute). Se interagir com um tipo de recurso Clássico através do Azure Resource Manager utilizando estes fornecedores de recursos proxy, as operações aparecem no Registo de Atividades. Se interagir com um tipo de recurso clássico fora dos proxies do Azure Resource Manager, as suas ações só são registadas no Registo de Operação. O Registo de Operação pode ser navegado numa secção separada do portal.

![Descrição geral dos registos da plataforma](media/platform-logs-overview/logs-overview.png)




## <a name="viewing-platform-logs"></a>Registos de plataforma de visualização
Existem diferentes opções para visualizar e analisar os diferentes registos da plataforma Azure.

- Ver o registo de Atividade no portal Azure e aceder a eventos de powerShell e CLI. Consulte [o registo de atividade](../essentials/activity-log.md#view-the-activity-log) para mais detalhes. 
- Ver relatórios de Segurança e Atividade do Diretório Ativo Azure no portal Azure. Veja [quais são os relatórios do Azure Ative Directory?](../../active-directory/reports-monitoring/overview-reports.md)  para detalhes.
- Os registos de recursos são gerados automaticamente por recursos Azure suportados, mas não estão disponíveis para serem vistos a menos que os envie para um [destino.](#destinations) 

## <a name="destinations"></a>Destinos
Pode enviar registos de plataforma para um ou mais destinos na tabela seguinte, dependendo dos seus requisitos de monitorização. Configure destinos para registos de plataformas [criando uma definição de Diagnóstico](../essentials/diagnostic-settings.md).

| Destino | Description |
|:---|:---|
| Área de trabalho do Log Analytics | Analise os registos de todos os seus recursos Azure em conjunto e aproveite todas as funcionalidades disponíveis para [os Registos do Monitor Azure,](../logs/data-platform-logs.md) incluindo [consultas de registo](../logs/log-query-overview.md) e [alertas de registo.](../alerts/alerts-log.md) Fixar os resultados de uma consulta de registo a um dashboard Azure ou incluí-lo num livro como parte de um relatório interativo. |  |
| Hub de eventos | Envie dados de registo de plataforma fora do Azure, por exemplo, para uma plataforma de telemetria personalizada ou siem de terceiros.
| Storage do Azure | Arquivar os registos para auditoria ou cópia de segurança. |

- Para obter detalhes sobre a criação de uma definição de diagnóstico para registos de atividade ou registos de recursos, consulte [Criar definições de diagnóstico para enviar registos e métricas da plataforma para diferentes destinos](../essentials/diagnostic-settings.md). 
- Para obter detalhes sobre a criação de uma definição de diagnóstico para registos do Azure Ative Directory, consulte os seguintes artigos.
  - [Integre registos AD AD com registos do Azure Monitor](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)
  - [Tutorial: Stream Azure Ative Directory registra-se para um centro de eventos Azure](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md)
  - [Tutorial: Arquivo Azure AD regista para uma conta de armazenamento Azure](../../active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md)



## <a name="next-steps"></a>Passos seguintes

* [Leia mais detalhes sobre o registo de atividades](../essentials/activity-log.md)
* [Leia mais detalhes sobre registos de recursos](./resource-logs.md)