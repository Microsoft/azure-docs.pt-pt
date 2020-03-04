---
title: Monitorização - Base de Dados Azure para MySQL
description: Este artigo descreve as métricas para monitorização e alerta para a Base de Dados Azure para o MySQL, incluindo CPU, armazenamento e estatísticas de conexão.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: e9ae19e503b6b54e881af3c6477f77ffa1c930b0
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2020
ms.locfileid: "78252620"
---
# <a name="monitoring-in-azure-database-for-mysql"></a>Monitorização na Base de Dados Azure para MySQL
Monitorizar dados sobre os seus servidores ajuda-o a resolver problemas e a otimizar para a sua carga de trabalho. A Base de Dados Azure para MySQL fornece várias métricas que dão uma visão do comportamento do seu servidor.

## <a name="metrics"></a>Métricas
Todas as métricas azure têm uma frequência de um minuto, e cada métrica fornece 30 dias de história. Pode configurar alertas sobre as métricas. Para orientação passo a passo, consulte [como configurar alertas](howto-alert-on-metric.md). Outras tarefas incluem a criação de ações automatizadas, a realização de análises avançadas e o histórico de arquivamento. Para mais informações, consulte a visão geral das [Métricas Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md).

### <a name="list-of-metrics"></a>Lista de métricas
Estas métricas estão disponíveis para a Base de Dados Azure para o MySQL:

|Métrica|Nome a apresentar de métrica|Unidade|Descrição|
|---|---|---|---|
|cpu_percent|Por cento do CPU|Percentagem|A percentagem de CPU em uso.|
|memory_percent|Por cento da memória|Percentagem|A percentagem de memória em uso.|
|io_consumption_percent|IO por cento|Percentagem|A percentagem de IO em uso.|
|storage_percent|Percentagem de armazenamento|Percentagem|A percentagem de armazenamento usado no máximo do servidor.|
|storage_used|Armazenamento utilizado|Bytes|A quantidade de armazenamento em uso. O armazenamento utilizado pelo serviço pode incluir os ficheiros da base de dados, registos de transações e os registos do servidor.|
|serverlog_storage_percent|Percentagem de armazenamento de registo do servidor|Percentagem|A percentagem de armazenamento de registo do servidor usado a partir do armazenamento máximo de registo do servidor.|
|serverlog_storage_usage|Armazenamento de registo do servidor utilizado|Bytes|A quantidade de armazenamento de registo do servidor em uso.|
|serverlog_storage_limit|Limite de armazenamento de registo do servidor|Bytes|O armazenamento máximo de registo do servidor para este servidor.|
|storage_limit|Limite de armazenamento|Bytes|O armazenamento máximo para este servidor.|
|active_connections|Conexões ativas|Contagem|O número de ligações ativas ao servidor.|
|connections_failed|Ligações com Falhas|Contagem|O número de ligações falhadas ao servidor.|
|seconds_behind_master|Rema de replicação em segundos|Contagem|O número de segundos em que o servidor de réplica está atrasado contra o servidor principal.|
|network_bytes_egress|Saída da Rede|Bytes|Rede Para fora através de conexões ativas.|
|network_bytes_ingress|Entrada na Rede|Bytes|Rede Em todas as ligações ativas.|
|backup_storage_used|Armazenamento de backup usado|Bytes|A quantidade de armazenamento de reserva usado.|

## <a name="server-logs"></a>Registos do servidor
Pode ativar consultas lentas e registo de auditoria no seu servidor. Estes registos também estão disponíveis através de registos de diagnóstico do Azure em registos do Monitor Azure, Centros de Eventos e Conta de Armazenamento. Para saber mais sobre a exploração madeireira, visite os [registos](concepts-audit-logs.md) de auditoria e os artigos de [registo sinuoso.](concepts-server-logs.md)

## <a name="query-store"></a>Arquivo de Consultas
[A Consulta Store](concepts-query-store.md) é uma funcionalidade que acompanha o desempenho da consulta ao longo do tempo, incluindo estatísticas de tempo de execução de consulta e eventos de espera. A funcionalidade persiste na consulta de informação sobre o desempenho do tempo de execução no esquema **mysql.** Pode controlar a recolha e armazenamento de dados através de vários botões de configuração.

## <a name="query-performance-insight"></a>Query Performance Insight
[Consulta Performance Insight](concepts-query-performance-insight.md) trabalha em conjunto com a Query Store para fornecer visualizações acessíveis a partir do portal Azure. Estes gráficos permitem identificar consultas chave que impactam o desempenho. A Consulta Performance Insight está acessível na secção **de Desempenho Inteligente** da sua Base de Dados Azure para a página do portal do servidor MySQL.

## <a name="performance-recommendations"></a>Recomendações de Desempenho
O recurso [De saúde](concepts-performance-recommendations.md) em Recomendações de Desempenho identifica oportunidades para melhorar o desempenho da carga de trabalho. As Recomendações de Desempenho fornecem-lhe recomendações para a criação de novos índices que têm o potencial de melhorar o desempenho das suas cargas de trabalho. Para produzir recomendações de índice, a funcionalidade tem em conta várias características da base de dados, incluindo o seu esquema e a carga de trabalho, conforme reportado pela Query Store. Após a implementação de qualquer recomendação de desempenho, os clientes devem testar o desempenho para avaliar o impacto dessas alterações.

## <a name="planned-maintenance-notification"></a>Notificação de manutenção planeada

**As notificações** de manutenção planeadas permitem-lhe receber alertas para a próxima manutenção planeada para a sua Base de Dados Azure para mySQL. Estas notificações estão integradas com a manutenção planeada [da Service Health](../service-health/overview.md) e permitem-lhe visualizar toda a manutenção programada para as suas subscrições num só local. Também ajuda a escalar a notificação para o público certo para diferentes grupos de recursos, pois pode ter diferentes contactos responsáveis por diferentes recursos. Receberá a notificação sobre a próxima manutenção 72 horas antes do evento.

> [!Note]
> Faremos todas as tentativas para fornecer **notificação** de manutenção planeada 72 horas de antecedência para todos os eventos. No entanto, em caso de patches críticos ou de segurança, as notificações podem ser enviadas mais perto do evento ou ser omitidas.

### <a name="to-receive-planned-maintenance-notification"></a>Para receber a notificação de manutenção planeada

1. No [portal,](https://portal.azure.com)selecione **Service Health**.
2. Na secção **Alertas,** selecione alertas de **saúde**.
3. Selecione + Adicione o alerta de **saúde** do serviço e preencha os campos.
4. Preencha os campos necessários. 
5. Escolha o **tipo de Evento,** selecione **manutenção planeada** ou **selecione tudo**
6. Em **Grupos de Ação** definem como gostaria de receber o alerta (receba um e-mail, desencadeie uma aplicação lógica, etc.)  
7. Assegurar a regra de ativação sobre a criação está definida para Sim.
8. Selecione **Criar regra** de alerta para completar o seu alerta

Para obter **passos**detalhados sobre como criar alertas de saúde de serviço, consulte a Create alerts de registo de [atividade sonantes em notificações](../service-health/alerts-activity-log-service-notifications.md)de serviço .

> [!IMPORTANT]
> As notificações de manutenção planeadas estão atualmente em pré-visualização

## <a name="next-steps"></a>Passos seguintes
- Veja [como configurar alertas](howto-alert-on-metric.md) para orientação sobre a criação de um alerta numa métrica.
- Para obter mais informações sobre como aceder e exportar métricas utilizando o portal Azure, REST API, ou CLI, consulte a visão geral das [Métricas Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md).
- Leia o nosso blog sobre [as melhores práticas para monitorizar o seu servidor.](https://azure.microsoft.com/blog/best-practices-for-alerting-on-metrics-with-azure-database-for-mysql-monitoring/)
