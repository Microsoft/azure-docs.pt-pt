---
title: Utilize a análise de armazenamento azure para recolher dados de registos e métricas Microsoft Docs
description: O Storage Analytics permite-lhe rastrear os dados das métricas para todos os serviços de armazenamento e recolher registos para armazenamento de Blob, Queue e Mesa.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 03/03/2017
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.openlocfilehash: 4ad9f13bcdf36b67400adb62d58ee260ff256bb3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80637151"
---
# <a name="storage-analytics"></a>Análise de Armazenamento

O Azure Storage Analytics realiza a exploração madeireira e fornece dados métricos para uma conta de armazenamento. Pode utilizar estes dados para rastrear pedidos, analisar tendências de utilização e diagnosticar problemas com a sua conta de armazenamento.

Para utilizar o Storage Analytics, deve ative-o individualmente para cada serviço que pretende monitorizar. Pode permitir a partir do [portal Azure.](https://portal.azure.com) Para mais detalhes, consulte Monitor uma conta de [armazenamento no portal Azure](storage-monitor-storage-account.md). Também pode ativar o Storage Analytics programaticamente através da API REST ou da biblioteca do cliente. Utilize as propriedades do [serviço set Blob,](/rest/api/storageservices/set-blob-service-properties)definir propriedades do serviço de [fila,](/rest/api/storageservices/set-queue-service-properties)definir propriedades de serviço de [mesa,](/rest/api/storageservices/set-table-service-properties)e definir as propriedades do serviço de [ficheiros](/rest/api/storageservices/Get-File-Service-Properties) para ativar o Armazenamento Analytics para cada serviço.

Os dados agregados são armazenados numa bolha bem conhecida (para exploração madeireira) e em tabelas bem conhecidas (para métricas), que podem ser acedidas através do serviço Blob e das APIs do serviço de mesa.

O Storage Analytics tem um limite de 20 TB na quantidade de dados armazenados que é independente do limite total para a sua conta de armazenamento. Para obter mais informações sobre os limites da conta de armazenamento, consulte [a escalabilidade e os objetivos](scalability-targets-standard-account.md)de desempenho para as contas de armazenamento padrão .

Para um guia aprofundado sobre a utilização de Storage Analytics e outras ferramentas para identificar, diagnosticar e resolver problemas relacionados com o armazenamento do Azure, consulte [monitor, diagnóstico e resolução](storage-monitoring-diagnosing-troubleshooting.md)de problemas do Armazenamento Microsoft Azure .

## <a name="billing-for-storage-analytics"></a>Faturação para Análise de Armazenamento

Todos os dados das métricas são escritos pelos serviços de uma conta de armazenamento. Como resultado, cada operação de escrita realizada pela Storage Analytics é faturada. Adicionalmente, a quantidade de armazenamento utilizado pelos dados das métricas também é faturada.

As seguintes ações realizadas pelo Storage Analytics são faturadas:

* Pedidos para criar bolhas para abate de árvores.
* Pedidos para criar entidades de tabela para métricas.

Se configurar uma política de retenção de dados, não é cobrado para eliminar transações quando o Storage Analytics elimina dados antigos de registo e métricas. No entanto, eliminar transações de um cliente são faturados. Para obter mais informações sobre as políticas de retenção, consulte [a Definição de uma Política](https://msdn.microsoft.com/library/azure/hh343263.aspx)de Retenção de Dados de Análise de Armazenamento .

### <a name="understanding-billable-requests"></a>Compreender os pedidos de faturação

Todos os pedidos feitos ao serviço de armazenamento de uma conta são faturados ou não faturados. O Storage Analytics regista cada pedido individual feito a um serviço, incluindo uma mensagem de estado que indica como o pedido foi tratado. Da mesma forma, o Storage Analytics armazena métricas tanto para um serviço como para as operações da API desse serviço, incluindo as percentagens e contagem de determinadas mensagens de estado. Juntos, estas funcionalidades podem ajudá-lo a analisar os seus pedidos de faturação, a fazer melhorias na sua aplicação e a diagnosticar problemas com pedidos aos seus serviços. Para obter mais informações sobre faturação, consulte [Understanding Azure Storage Billing - Width-banda, Transações e Capacidade](https://docs.microsoft.com/archive/blogs/windowsazurestorage/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity).

Ao analisar os dados do Storage Analytics, pode utilizar as tabelas no tópico de [Operações e Mensagens](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) de Estado de Armazenamento Analytics para determinar quais os pedidos que são faturados. Em seguida, pode comparar os seus dados de registos e métricas com as mensagens de estado para ver se foi cobrado por um pedido específico. Também pode utilizar as tabelas no tópico anterior para investigar a disponibilidade de um serviço de armazenamento ou operação API individual.

## <a name="next-steps"></a>Passos seguintes
* [Monitorizar uma conta de armazenamento no portal do Azure](storage-monitor-storage-account.md)
* [Métricas de Análise de Armazenamento](storage-analytics-metrics.md)
* [Registo da Análise de Armazenamento](storage-analytics-logging.md)
