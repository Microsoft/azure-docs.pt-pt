---
title: Use a analítica de armazenamento Azure para recolher dados de registos e métricas Microsoft Docs
description: O Storage Analytics permite-lhe rastrear dados de métricas para todos os serviços de armazenamento e recolher registos para o armazenamento de Blob, Queue e Table.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 03/03/2017
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.custom: monitoring
ms.openlocfilehash: 9a081a28d4c96e3c38986cbb3c0990bc89c5ab99
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "83684476"
---
# <a name="storage-analytics"></a>Análise de Armazenamento

A Azure Storage Analytics realiza o registo e fornece dados de métricas para uma conta de armazenamento. Pode utilizar estes dados para rastrear pedidos, analisar tendências de utilização e diagnosticar problemas com a sua conta de armazenamento.

Para utilizar o Storage Analytics, deve ative-lo individualmente para cada serviço que pretende monitorizar. Pode ative-lo a partir do [portal Azure.](https://portal.azure.com) Para mais informações, consulte [uma conta de armazenamento no portal Azure](storage-monitor-storage-account.md). Também pode ativar o Storage Analytics programáticamente através da API REST ou da biblioteca do cliente. Utilize as [propriedades de serviço de blob definidas](/rest/api/storageservices/set-blob-service-properties), [desemboe propriedades de serviço de fila,](/rest/api/storageservices/set-queue-service-properties) [detive as propriedades do serviço de mesa](/rest/api/storageservices/set-table-service-properties)e de configurar as operações de propriedades de serviço de [ficheiros](/rest/api/storageservices/Get-File-Service-Properties) para permitir o Armazenamento Analytics para cada serviço.

Os dados agregados são armazenados numa bolha bem conhecida (para registo) e em tabelas bem conhecidas (para métricas), que podem ser acedidas através do serviço Blob e apIs de serviço de tabela.

A Storage Analytics tem um limite de 20 TB na quantidade de dados armazenados que é independente do limite total para a sua conta de armazenamento. Para obter mais informações sobre os limites da conta de armazenamento, consulte [os objetivos de Escalabilidade e desempenho para contas de armazenamento padrão.](scalability-targets-standard-account.md)

Para obter um guia aprofundado sobre a utilização de Storage Analytics e outras ferramentas para identificar, diagnosticar e resolver problemas relacionados com o Armazenamento Azure, consulte [Monitor, diagnóstico e resolução de problemas do Microsoft Azure Storage](storage-monitoring-diagnosing-troubleshooting.md).

## <a name="billing-for-storage-analytics"></a>Faturação para analítica de armazenamento

Todos os dados métricos são escritos pelos serviços de uma conta de armazenamento. Como resultado, cada operação de escrita realizada pela Storage Analytics é faturada. Além disso, a quantidade de armazenamento utilizada pelos dados das métricas também é faturada.

As seguintes ações realizadas pela Storage Analytics são faturadas:

* Pedidos para criar bolhas para registo.
* Pedidos de criação de entidades de tabela para métricas.

Se tiver configurado uma política de retenção de dados, não é cobrado para apagar transações quando o Storage Analytics elimina dados antigos de registo e métricas. No entanto, eliminar as transações de um cliente são facturantes. Para obter mais informações sobre as políticas de retenção, consulte [configurar uma política de retenção de dados de análise de armazenamento](https://msdn.microsoft.com/library/azure/hh343263.aspx).

### <a name="understanding-billable-requests"></a>Compreender pedidos de faturação

Todos os pedidos feitos ao serviço de armazenamento de uma conta são faturais ou não faturam. Armazenamento Analytics regista cada pedido individual feito a um serviço, incluindo uma mensagem de estado que indica como o pedido foi tratado. Da mesma forma, o Storage Analytics armazena métricas para um serviço e as operações da API desse serviço, incluindo as percentagens e a contagem de determinadas mensagens de estado. Em conjunto, estas funcionalidades podem ajudá-lo a analisar os seus pedidos de faturação, a fazer melhorias na sua aplicação e a diagnosticar problemas com pedidos aos seus serviços. Para obter mais informações sobre faturação, consulte [a Compreensão da Faturação de Armazenamento Azure - Largura de Banda, Transações e Capacidade](https://docs.microsoft.com/archive/blogs/windowsazurestorage/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity).

Ao analisar os dados do Storage Analytics, pode utilizar as tabelas no tópico [de Operações registadas e Mensagens de Estado](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) do Armazenamento Analytics para determinar quais os pedidos que são faturados. Em seguida, pode comparar os seus dados de registos e métricas com as mensagens de estado para ver se foi cobrado um pedido específico. Também pode utilizar as tabelas no tópico anterior para investigar a disponibilidade para um serviço de armazenamento ou operação API individual.

## <a name="next-steps"></a>Passos seguintes
* [Monitorizar uma conta de armazenamento no portal do Azure](storage-monitor-storage-account.md)
* [Métricas de Análise de Armazenamento](storage-analytics-metrics.md)
* [Registo da Análise de Armazenamento](storage-analytics-logging.md)
