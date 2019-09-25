---
title: Visão geral do log de atividades do Azure
description: Saiba o que é o log de atividades do Azure e como você pode usá-lo para entender os eventos que ocorrem dentro de sua assinatura do Azure.
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/20/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: ee3a1fef379e2950172dddc389b30e0a363127ae
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/25/2019
ms.locfileid: "71262131"
---
# <a name="overview-of-azure-activity-log"></a>Visão geral do log de atividades do Azure

O **log de atividades do Azure** fornece informações sobre eventos no nível da assinatura que ocorreram no Azure. Isso inclui um intervalo de dados, de Azure Resource Manager dados operacionais a atualizações em eventos de integridade do serviço. O log de atividades era conhecido anteriormente como _logs de auditoria_ ou _logs operacionais_, já que a categoria administrativa relata eventos de plano de controle para suas assinaturas. 

Use o log de atividades para determinar o _que_, _quem_e _quando_ para qualquer operação de gravação (put, post, Delete) realizada nos recursos em sua assinatura. Também é possível compreender o estado da operação e outras propriedades relevantes. 

O log de atividades não inclui operações de leitura (GET) ou operações para recursos que usam o modelo clássico/RDFE.

## <a name="comparison-to-resource-logs"></a>Comparação com os logs de recursos
Há um único log de atividades para cada assinatura do Azure. Ele fornece dados sobre as operações em um recurso de fora (o "plano de controle"). Os [logs de recursos](resource-logs-overview.md) são emitidos por um recurso e fornecem informações sobre a operação desse recurso (o "plano de dados"). Você deve criar uma configuração de diagnóstico para cada recurso para coletar logs de recursos.

![Logs de atividade em comparação com os logs de recursos](media/activity-logs-overview/Activity_Log_vs_other_logs_v5.png)


> [!NOTE]
> O log de atividades do Azure destina-se principalmente a atividades que ocorrem em Azure Resource Manager. Ele não controla os recursos usando o modelo clássico/RDFE. Alguns tipos de recursos clássicos têm um provedor de recursos de proxy no Azure Resource Manager (por exemplo, Microsoft. ClassicCompute). Se você interagir com um tipo de recurso clássico por meio de Azure Resource Manager usando esses provedores de recursos de proxy, as operações aparecerão no log de atividades. Se você interagir com um tipo de recurso clássico fora dos proxies de Azure Resource Manager, suas ações só serão registradas no log de operação. O log de operação pode ser pesquisado em uma seção separada do Portal.

## <a name="activity-log-retention"></a>Retenção do log de atividades
Depois de criadas, as entradas do log de atividades não são modificadas ou excluídas pelo sistema. Além disso, você não pode alterá-los na interface ou programaticamente. Os eventos do log de atividades são armazenados por 90 dias. Para armazenar esses dados por períodos mais longos, [colete-os em Azure monitor](activity-log-collect.md) ou [exporte-os para o armazenamento ou hubs de eventos](activity-log-export.md).

## <a name="view-the-activity-log"></a>Exibir o log de atividades
Exiba o log de atividades de todos os recursos no menu **monitorar** na portal do Azure. Exiba o log de atividades de um recurso específico da opção **log de atividades** no menu desse recurso. Você também pode recuperar registros de log de atividades com o PowerShell, a CLI ou a API REST.  Consulte [Exibir e recuperar eventos do log de atividades do Azure](activity-log-view.md).

![Exibir log de atividades](./media/activity-logs-overview/view-activity-log.png)

## <a name="collect-activity-log-in-azure-monitor"></a>Coletar log de atividades no Azure Monitor
Colete o log de atividades em um espaço de trabalho Log Analytics no Azure Monitor para analisá-lo com outros dados de monitoramento e manter os dados por mais de 90 dias. Consulte [coletar e analisar logs de atividades do Azure no espaço de trabalho log Analytics no Azure monitor](activity-log-collect.md).

![Log de atividades de consulta](./media/activity-logs-overview/query-activity-log.png)

## <a name="export-activity-log"></a>Exportar Registos de Atividade
Exporte o log de atividades para o armazenamento do Azure para arquivamento ou transmita-o para um hub de eventos para ingestão por um serviço de terceiros ou uma solução de análise personalizada. Consulte [exportar o log de atividades do Azure](activity-log-export.md). Você também pode analisar eventos do log de atividades no Power BI usando o [**pacote de conteúdo do Power bi**](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs/).

## <a name="alert-on-activity-log"></a>Alerta no log de atividades
Você pode criar um alerta quando determinados eventos são criados no log de atividades com um [alerta do log de atividades](activity-log-alerts.md). Você também pode criar um alerta usando uma [consulta de log](alerts-log-query.md) quando o log de atividades estiver conectado a um espaço de trabalho log Analytics, mas houver um custo para registrar alertas de consulta. Não há nenhum custo para alertas do log de atividades.

## <a name="categories-in-the-activity-log"></a>Categorias no log de atividades
Cada evento no log de atividades tem uma categoria específica que são descritas na tabela a seguir. Para todos os detalhes sobre esquemas uma destas categorias, consulte [esquema de eventos de registo de atividades do Azure](activity-log-schema.md). 

| Category | Descrição |
|:---|:---|
| Administrativa | Contém o registro de todas as operações de criação, atualização, exclusão e ação executadas por meio do Resource Manager. Exemplos de eventos administrativos incluem _criar máquina virtual_ e _excluir grupo de segurança de rede_.<br><br>Cada ação tomada por um usuário ou aplicativo usando o Resource Manager é modelada como uma operação em um determinado tipo de recurso. Se o tipo de operação for _gravação_, _exclusão_ou _ação_, os registros de início e êxito ou falha da operação serão registrados na categoria administrativa. Os eventos administrativos também incluem quaisquer alterações no controle de acesso baseado em função em uma assinatura. |
| Service Health | Contém o registro de qualquer incidente de integridade do serviço que ocorreu no Azure. Um exemplo de SQL Azure de eventos de integridade do serviço _no leste dos EUA está apresentando tempo de inatividade_. <br><br>Os eventos de integridade do serviço são fornecidos em seis variedades: _Ação necessária_, _recuperação assistida_, _incidente_, _manutenção_, _informações_ou _segurança_. Esses eventos serão criados somente se você tiver um recurso na assinatura que seria impactado pelo evento.
| Estado de funcionamento do Recurso | Contém o registro dos eventos de integridade do recurso que ocorreram para os recursos do Azure. Um exemplo de um evento de Resource Health é o _status de integridade da máquina virtual alterado para indisponível_.<br><br>Resource Health eventos podem representar um dos quatro status de integridade: _Disponível_, _indisponível_, _degradado_e _desconhecido_. Além disso, Resource Health eventos podem ser categorizados como sendo _iniciado pela plataforma_ ou _pelo usuário_. |
| Alerta | Contém o registro de ativações para alertas do Azure. Um exemplo de um evento de alerta é _% de CPU em myVM tem mais de 80 para os últimos 5 minutos_.|
| Dimensionamento Automático | Contém o registro de todos os eventos relacionados à operação do mecanismo de dimensionamento automático com base em qualquer configuração de dimensionamento automático que você definiu em sua assinatura. Um exemplo de um evento de dimensionamento automático é a _ação de escalabilidade vertical com falha_. |
| Recomendação | Contém eventos de recomendação do Azure Advisor. |
| Segurança | Contém o registro de todos os alertas gerados pela central de segurança do Azure. Um exemplo de um evento de segurança é um _arquivo de extensão dupla suspeito executado_. |
| Política | Contém registros de todas as operações de ação de efeito executadas por Azure Policy. Exemplos de eventos de política incluem _auditoria_ e _negação_. Cada ação tomada pela política é modelada como uma operação em um recurso. |


## <a name="next-steps"></a>Próximos Passos

* [Criar um perfil de log para exportar o log de atividades do Azure](activity-log-export.md)
* [Transmitir o log de atividades do Azure para os hubs de eventos](activity-logs-stream-event-hubs.md)
* [Arquivar o log de atividades do Azure no armazenamento](archive-activity-log.md)

