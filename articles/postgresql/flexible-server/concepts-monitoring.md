---
title: Monitorização e métricas - Base de Dados Azure para PostgreSQL - Servidor Flexível
description: Este artigo descreve as funcionalidades de monitorização e métricas na Base de Dados Azure para PostgreSQL - Servidor Flexível.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: 1519e0b5cef6055cf8d8b0aded0d8ad323d548a2
ms.sourcegitcommit: 19dce034650c654b656f44aab44de0c7a8bd7efe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/04/2020
ms.locfileid: "91707851"
---
# <a name="monitor-metrics-on-azure-database-for-postgresql---flexible-server"></a>Monitorize métricas na Base de Dados Azure para PostgreSQL - Servidor Flexível

> [!IMPORTANT]
> Azure Database for PostgreSQL - Flexible Server está em pré-visualização

Monitorizar dados sobre os seus servidores ajuda-o a resolver problemas e a otimizar a sua carga de trabalho. A Azure Database for PostgreSQL fornece várias opções de monitorização para fornecer informações sobre o comportamento do seu servidor.

## <a name="metrics"></a>Métricas
A Azure Database for PostgreSQL fornece várias métricas que dão uma visão do comportamento dos recursos que suportam o servidor PostgreSQL. Cada métrica é emitida com uma frequência de um minuto, e tem até [93 dias de história.](../../azure-monitor/platform/data-platform-metrics.md#retention-of-metrics) Pode configurar alertas sobre as métricas. Outras opções incluem a criação de ações automatizadas, a realização de análises avançadas e arquivamento da história. Para mais informações, consulte a Visão Geral das [Métricas Azure](../../azure-monitor/platform/data-platform-metrics.md).

### <a name="list-of-metrics"></a>Lista de métricas
As seguintes métricas estão disponíveis para o servidor flexível PostgreSQL:


|Métrica|Nome de exibição métrica|Unidade|Descrição|
|---|---|---|---|
| active_connections | Ligações Ativas | Contagem | O número de ligações ao seu servidor. | 
| backup_storage_used | Armazenamento de backup usado | Bytes | Quantidade de armazenamento de reserva usado. Esta métrica representa a soma de armazenamento consumida por todas as cópias de segurança completas da base de dados, cópias de segurança diferenciais e cópias de segurança de registo mantidas com base no período de retenção de backup definido para o servidor. A frequência dos backups é gerida pelo serviço. Para o armazenamento geo-redundante, o uso de armazenamento de backup é o dobro do armazenamento localmente redundante. |
| connections_failed | Ligações com Falhas | Contagem | Ligações falhadas. |
| connections_succeeded | Conexões bem sucedidas | Contagem | Ligações bem sucedidas. |
| cpu_credits_consumed | Créditos CPU Consumidos | Contagem | Número de créditos utilizados pelo servidor flexível. Aplicável ao nível burstável. |
| cpu_credits_remaining | Créditos CPU Remanescentes | Contagem | Número de créditos disponíveis para rebentar. Aplicável ao nível burstável. |
| cpu_percent | CPU por cento | Percentagem | Percentagem de CPU em uso. | 
| disk_queue_depth | Profundidade da fila do disco | Contagem | Número de operações de E/S pendentes no disco de dados. |
| iops | IOPS | Contagem | Número de operações de E/S para disco por segundo. |
| maximum_used_transactionIDs | IDs máximos de transações usadas | Contagem | Identificação máxima de transação em uso. |
| memory_percent | Por cento da memória | Percentagem | Percentagem de memória em uso. |
| network_bytes_egress | Saída da Rede | Bytes | Quantidade de tráfego de rede de saída. |
| network_bytes_ingress | Entrada na Rede | Bytes | Quantidade de tráfego de rede de entrada. |
| read_iops | Ler IOPS | Contagem | Número de operações de leitura de e/S do disco de dados por segundo. |
| read_throughput | Ler Produção | Bytes | Bytes lêem por segundo a partir do disco. |
| storage_free | Armazenamento gratuito | Bytes | A quantidade de espaço de armazenamento disponível. |
| storage_percent | Por cento de armazenamento | Percentagem | Por cento do espaço de armazenamento usado. O armazenamento utilizado pelo serviço pode incluir os ficheiros de base de dados, registos de transações e registos do servidor.|
| storage_used | Armazenamento utilizado | Bytes | Por cento do espaço de armazenamento usado. O armazenamento utilizado pelo serviço pode incluir os ficheiros de base de dados, registos de transações e registos do servidor. |
| txlogs_storage_used | Armazenamento de registo de transações utilizado | Bytes | Quantidade de espaço de armazenamento utilizado pelos registos de transações. | 
| write_throughput | Escrever Produção | Bytes | Bytes escritos por segundo para o disco. |
| write_iops | Escrever iops | Contagem | Número de operações de escrita de disco de dados por segundo. |

## <a name="server-logs"></a>Registos do servidor
A Base de Dados Azure para PostgreSQL permite-lhe configurar e aceder aos registos padrão dos Postgres. Para saber mais sobre os registos, visite os [conceitos de registo de registo doc](concepts-logging.md).
