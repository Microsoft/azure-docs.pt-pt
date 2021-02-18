---
title: Monitorização - Base de Dados Azure para MySQL - Servidor Flexível
description: Este artigo descreve as métricas para monitorização e alerta para a Base de Dados Azure para o MySQL Flexible Server, incluindo CPU, armazenamento e estatísticas de conexão.
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: conceptual
ms.date: 9/21/2020
ms.openlocfilehash: c2883a2ddba9a322c09a318aa9f232de5f4704b4
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100595158"
---
# <a name="monitor-azure-database-for-mysql-flexible-servers-with-built-in-metrics"></a>Monitor Azure Database para Servidores Flexíveis MySQL com métricas incorporadas

> [!IMPORTANT] 
> A azure Database for MySQL - Flexible Server encontra-se atualmente em pré-visualização pública.

A Azure Database for MySQL Flexible Server fornece monitorização de servidores através do Azure Monitor. As métricas são valores numéricos que descrevem algum aspeto dos recursos do seu servidor num determinado momento. Monitorizar os recursos do seu servidor ajuda-o a resolver problemas e a otimizar a sua carga de trabalho, permitindo-lhe monitorizar o que mais importa para si. Monitorizar as métricas certas ajuda-o a manter o desempenho, a fiabilidade e a disponibilidade do seu servidor e aplicações.

Neste artigo, irá conhecer as várias métricas disponíveis para o seu servidor flexível que dão uma visão do comportamento do seu servidor.

## <a name="available-metrics"></a>Métricas disponíveis

A Azure Database for MySQL Flexible Server fornece várias métricas para entender como a sua carga de trabalho está a funcionar e com base nestes dados, pode compreender o impacto no seu servidor e aplicação. Por exemplo, no servidor flexível, é possível monitorizar **o CPU anfitrião por cento,** **As Ligações Ativas,** **IO%** e **o Host Memory Percent** para identificar quando há um impacto de desempenho. A partir daí, poderá ter de otimizar a sua carga de trabalho, escalar verticalmente alterando os níveis de cálculo ou escalando horizontalmente usando réplicas de leitura.

Todas as métricas Azure têm uma frequência de um minuto, e cada métrica fornece 30 dias de história. Pode configurar alertas sobre as métricas. Para obter orientação passo a passo, consulte [como configurar alertas](./how-to-alert-on-metric.md). Outras tarefas incluem a criação de ações automatizadas, a realização de análises avançadas e arquivamento da história. Para mais informações, consulte a Visão Geral das [Métricas Azure](../../azure-monitor/data-platform.md).

### <a name="list-of-metrics"></a>Lista de métricas
Estas métricas estão disponíveis para Azure Database para MySQL:

|Nome de exibição métrica|Metric|Unidade|Description|
|---|---|---|---|
|CpU anfitrião por cento|cpu_percent|Percentagem|A percentagem de utilização do CPU no servidor, incluindo a utilização do CPU tanto da carga de trabalho do cliente como dos processos Azure MySQL|
|Rede de anfitriões em |network_bytes_ingress|Bytes|O tráfego de rede de entrada no servidor, incluindo o tráfego da base de dados do cliente e as funcionalidades Azure MySQL como replicação, monitorização, registos, etc.|
|Rede de anfitriões fora|network_bytes_egress|Bytes|Tráfego de rede de saída no servidor, incluindo tráfego da base de dados do cliente e funcionalidades Azure MySQL como replicação, monitorização, registos, etc.|
|Atraso de replicação|replication_lag|Segundos|O tempo desde a última transação reproduzida. Esta métrica está disponível apenas para servidores de réplicas.|
|Ligações Ativas|active_connection|de palavras|O número de ligações ativas ao servidor.|
|Armazenamento de backup usado|backup_storage_used|Bytes|A quantidade de armazenamento de reserva usado.|
|IO por cento|io_consumption_percent|Percentagem|A percentagem de IO em uso.|
|Por cento da Memória do Anfitrião|memory_percent|Percentagem|A percentagem de memória em uso no servidor, incluindo a utilização da memória tanto da carga de trabalho do cliente como dos processos Azure MySQL|
|Limite de armazenamento|storage_limit|Bytes|O armazenamento máximo para este servidor.|
|Por cento de Armazenamento|storage_percent|Percentagem|A percentagem de armazenamento utilizada fora do máximo do servidor.|
|Armazenamento utilizado|storage_used|Bytes|A quantidade de armazenamento em uso. O armazenamento utilizado pelo serviço pode incluir os ficheiros de base de dados, registos de transações e registos do servidor.|
|Total de ligações|total_connections|de palavras|O número total de ligações ao servidor|
|Ligações Abortadas|aborted_connections|de palavras|O número de tentativas falhadas de ligação ao MySQL, por exemplo, falhou na ligação devido a más credenciais.|
|Consultas|consultas|de palavras|O número de consultas por segundo|

## <a name="next-steps"></a>Passos seguintes
- Veja [como configurar alertas](./how-to-alert-on-metric.md) para orientação sobre a criação de um alerta sobre uma métrica.
- Saiba mais sobre [o escalonamento do IOPS](./concepts/../concepts-compute-storage.md#iops) para melhorar o desempenho.