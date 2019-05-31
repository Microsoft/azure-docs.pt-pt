---
title: Descrição geral do registo de atividades do Azure
description: Saiba o que é o registo de atividades do Azure e como pode usá-lo a compreender os eventos que ocorrem na sua subscrição do Azure.
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/19/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: 34857108cf7f0580c380ffbd4bbcedb5cd5a807a
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2019
ms.locfileid: "66245032"
---
# <a name="overview-of-azure-activity-log"></a>Descrição geral do registo de atividades do Azure

O **registo de atividades do Azure** fornece informações sobre os eventos de nível de assinatura que ocorreram no Azure. Isto inclui uma variedade de dados de dados operacionais do Azure Resource Manager para as atualizações em eventos de estado de funcionamento do serviço. O registo de atividades anteriormente era conhecido como _registos de auditoria_ ou _registos operacionais_, uma vez que a categoria administrativa eventos de plano de controlo para as suas subscrições de relatórios. 

Utilizar o registo de atividades, para determinar a _o que_, _quem_, e _quando_ para quaisquer operações (PUT, POST, DELETE) efetuadas nos recursos na sua subscrição de escrita. Também é possível compreender o estado da operação e outras propriedades relevantes. 

O registo de Atividades não incluem operações de leitura (GET) nem operações para os recursos que utilizam o modelo de clássica/RDFE.

## <a name="comparison-to-diagnostic-logs"></a>Comparação com os registos de diagnóstico
Há um único registo de atividades para cada subscrição do Azure. Ele fornece dados sobre as operações num recurso de fora (o "plano de controlo"). [Os registos de diagnóstico](diagnostic-logs-overview.md) são emitidos por um recurso e fornecer informações sobre o funcionamento desse recurso (o "plano de dados"). Tem de ativar as definições de diagnóstico para cada recurso.

![Registos de atividades em comparação comparados os registos de diagnóstico](./media/activity-logs-overview/Activity_Log_vs_other_logs_v5.png)


> [!NOTE]
> O registo de atividades do Azure é principalmente para atividades que ocorrem no Azure Resource Manager. Não regista ugresources usando o modelo de clássica/RDFE. Alguns tipos de recursos clássicos têm um fornecedor de recursos de proxy no Azure Resource Manager (por exemplo, Microsoft. classiccompute). Se interage com um tipo de recurso clássico através do Azure Resource Manager com estes fornecedores de recursos de proxy, as operações aparecem no registo de atividades. Se interage com um tipo de recurso clássico fora de proxies do Azure Resource Manager, suas ações só são registadas no registo de operação. O registo de operação pode ser navegado numa seção à parte do portal.

## <a name="activity-log-retention"></a>Retenção do registo de atividade
Eventos de registo de atividade são armazenados durante 90 dias. Para armazenar estes dados por períodos mais longos, [reuni-los no Azure Monitor](activity-log-collect.md) ou [exportá-lo para o armazenamento ou Hubs de eventos](activity-log-export.md).

## <a name="view-the-activity-log"></a>Ver o registo de atividade
Ver o registo de atividade para todos os recursos a partir da **Monitor** menu no portal do Azure. Ver o registo de atividade para um recurso específico do **registo de atividades** opção no menu desse recurso. Também pode obter registos de registo de atividades com o PowerShell, CLI ou REST API.  Ver [modo de exibição e obter a atividade de Azure registar eventos](activity-log-view.md).

![Ver o registo de atividade](./media/activity-logs-overview/view-activity-log.png)

## <a name="collect-activity-log-in-azure-monitor"></a>Recolher o registo de atividades no Azure Monitor
Recolha o registo de atividades para uma área de trabalho do Log Analytics no Azure Monitor para analisá-los com outros dados de monitorização e para manter os dados durante mais de 90 dias. Ver [recolher e analisar registos de atividades do Azure na área de trabalho do Log Analytics no Azure Monitor](activity-log-collect.md).

![Registo de atividades de consulta](./media/activity-logs-overview/query-activity-log.png)

## <a name="export-activity-log"></a>Exportar registos de atividade
Exportar o registo de atividades para o armazenamento do Azure para arquivar ou transmitir para um Hub de eventos para ingestão por um serviço de terceiros ou de uma solução de análise personalizada. Ver [exportar o registo de atividades do Azure](activity-log-export.md). Também pode analisar os eventos de registo de atividades no Power BI com o [ **pacote de conteúdos do Power BI**](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs/).

## <a name="alert-on-activity-log"></a>Alerta sobre o registo de atividades
Pode criar um alerta quando eventos particulares são criados no registo de atividades com um [alerta de registo de atividades](activity-log-alerts.md). Também pode criar um alerta utilizando uma [consulta de registo](alerts-log-query.md) quando o registo de atividades está ligado a uma área de trabalho do Log Analytics, mas há um custo para iniciar sessão consultar alertas. Não existe nenhum custo para alertas de registo de atividades.

## <a name="categories-in-the-activity-log"></a>Categorias no registo de atividades
Cada evento no registo de atividades tem uma categoria específica, que são descritas na tabela seguinte. Para todos os detalhes sobre esquemas uma destas categorias, consulte [esquema de eventos de registo de atividades do Azure](activity-log-schema.md). 

| Category | Descrição |
|:---|:---|
| Administrativa | Contém o registo de todos os criar, operações de atualização, eliminação e ação executada através do Resource Manager. Exemplos de eventos administrativos _criar máquina virtual_ e _eliminar o grupo de segurança de rede_.<br><br>Cada ação tomada por um utilizador ou aplicação com o Resource Manager é modelada como uma operação num tipo de recurso específico. Se for o tipo de operação _escrever_, _eliminar_, ou _ação_, os registos de início e de êxito ou falha dessa operação é registada na categoria administrativa. Eventos administrativos também incluem todas as alterações ao controlo de acesso baseado em funções numa subscrição. |
| Service Health | Contém o registo de qualquer incidentes de estado de funcionamento do serviço que ocorreram no Azure. Um exemplo de um evento de estado de funcionamento do serviço _do SQL Azure na região E.U.A. Leste está a sofrer um período de indisponibilidade_. <br><br>Eventos de estado de funcionamento do serviço têm cinco variedades: _Ação necessária_, _assistido recuperação_, _incidente_, _manutenção_, _informações_, ou  _Segurança_. Estes eventos são criados apenas se tiver um recurso na subscrição que seria afetada pelo evento.
| Estado de Funcionamento de Recursos | Contém o registo de quaisquer eventos de estado de funcionamento do recurso que ocorreram aos recursos do Azure. Um exemplo de um evento de estado de funcionamento do recurso é _estado de funcionamento da Máquina Virtual foi alterado para indisponível_.<br><br>Eventos de estado de funcionamento de recursos podem representar um dos quatro Estados de estado de funcionamento: _Disponível_, _indisponível_, _degradado_, e _desconhecido_. Além disso, os eventos de estado de funcionamento do recurso podem ser categorizados como sendo _plataforma iniciada_ ou _iniciada pelo utilizador_. |
| Alerta | Contém o registo de ativações para alertas do Azure. É um exemplo de um evento de alerta _% de CPU em myVM foi mais de 80 durante os últimos 5 minutos_.|
| Dimensionamento Automático | Contém o registo de quaisquer eventos relacionados com a operação do mecanismo de dimensionamento automático com base em quaisquer definições de dimensionamento automático que definiu na sua subscrição. É um exemplo de um evento de dimensionamento automático _ação do dimensionamento automático falhou_. |
| Recomendação | Contém eventos de recomendação do Assistente do Azure. |
| Segurança | Contém o registo de todos os alertas gerados pelo centro de segurança do Azure. Um exemplo de um evento de segurança é _ficheiros de extensão dupla suspeito executado_. |
| Política | Contém os registos de todas as operações de ação de efeito efetuadas pelo Azure Policy. Exemplos de eventos de política _auditoria_ e _negar_. Cada ação tomada pela política é modelada como uma operação num recurso. |


## <a name="next-steps"></a>Próximos Passos

* [Criar um perfil de registo para exportar o registo de atividades do Azure](activity-log-export.md)
* [O registo de atividades do Azure para os Hubs de eventos do Stream](activity-logs-stream-event-hubs.md)
* [Arquivar o registo de atividades do Azure para o armazenamento](archive-activity-log.md)

