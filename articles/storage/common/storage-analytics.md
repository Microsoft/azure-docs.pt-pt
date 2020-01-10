---
title: Usar a análise de armazenamento do Azure para coletar dados de logs e métricas | Microsoft Docs
description: O Análise de Armazenamento permite que você acompanhe dados de métricas para todos os serviços de armazenamento e colete logs para armazenamento de BLOBs, filas e tabelas.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 03/03/2017
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.openlocfilehash: efca3dad6d8bfadbc334067b0189d2bea5aef445
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2020
ms.locfileid: "75750519"
---
# <a name="storage-analytics"></a>Análise de Armazenamento

A Análise de Armazenamento do Azure leva a cabo o registo e fornece dados métricos relativamente a uma conta de armazenamento. Pode utilizar estes dados para rastrear pedidos, analisar tendências de utilização e diagnosticar problemas relacionados com a sua conta de armazenamento.

Para usar Análise de Armazenamento, você deve habilitá-lo individualmente para cada serviço que deseja monitorar. Você pode habilitá-lo no [portal do Azure](https://portal.azure.com). Para obter detalhes, consulte [monitorar uma conta de armazenamento no portal do Azure](storage-monitor-storage-account.md). Você também pode habilitar Análise de Armazenamento programaticamente por meio da API REST ou da biblioteca de cliente. Use as operações definir propriedades do [serviço blob](/rest/api/storageservices/set-blob-service-properties), [definir propriedades](/rest/api/storageservices/set-queue-service-properties)do serviço de fila, [definir propriedades do serviço tabela](/rest/api/storageservices/set-table-service-properties)e [definir propriedades do serviço de arquivo](/rest/api/storageservices/Get-File-Service-Properties) para habilitar a análise de armazenamento para cada serviço.

Os dados agregados são armazenados em um blob conhecido (para registro em log) e em tabelas conhecidas (para métricas), que podem ser acessados usando as APIs serviço de BLOB e serviço de tabela.

Análise de Armazenamento tem um limite de 20 TB na quantidade de dados armazenados que é independente do limite total para sua conta de armazenamento. Para obter mais informações sobre limites de conta de armazenamento, consulte [escalabilidade e metas de desempenho para contas de armazenamento Standard](scalability-targets-standard-account.md).

Para obter um guia detalhado sobre como usar Análise de Armazenamento e outras ferramentas para identificar, diagnosticar e solucionar problemas relacionados ao armazenamento do Azure, consulte [monitorar, diagnosticar e solucionar problemas armazenamento do Microsoft Azure](storage-monitoring-diagnosing-troubleshooting.md).

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="billing-for-storage-analytics"></a>Cobrança por Análise de Armazenamento

Todos os dados de métricas são gravados pelos serviços de uma conta de armazenamento. Como resultado, cada operação de gravação executada por Análise de Armazenamento é faturável. Além disso, a quantidade de armazenamento usada pelos dados de métrica também é faturável.

As seguintes ações executadas por Análise de Armazenamento são faturáveis:

* Solicitações para criar BLOBs para registro em log.
* Solicitações para criar entidades de tabela para métricas.

Se você tiver configurado uma política de retenção de dados, não será cobrado por transações de exclusão quando Análise de Armazenamento excluir dados antigos de log e métricas. No entanto, as transações de exclusão de um cliente são faturáveis. Para obter mais informações sobre políticas de retenção, consulte [Setting a análise de armazenamento Data Retention Policy](https://msdn.microsoft.com/library/azure/hh343263.aspx).

### <a name="understanding-billable-requests"></a>Noções básicas sobre solicitações faturáveis

Cada solicitação feita ao serviço de armazenamento de uma conta é Faturável ou não faturável. Análise de Armazenamento registra cada solicitação individual feita a um serviço, incluindo uma mensagem de status que indica como a solicitação foi tratada. Da mesma forma, o Análise de Armazenamento armazena métricas para um serviço e as operações de API desse serviço, incluindo as porcentagens e a contagem de determinadas mensagens de status. Juntos, esses recursos podem ajudá-lo a analisar suas solicitações faturáveis, fazer melhorias em seu aplicativo e diagnosticar problemas com solicitações para seus serviços. Para obter mais informações sobre cobrança, consulte [noções básicas sobre cobrança de armazenamento do Azure – largura de banda, transações e capacidade](https://blogs.msdn.com/b/windowsazurestorage/archive/2010/07/09/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity.aspx).

Ao examinar Análise de Armazenamento dados, você pode usar as tabelas no tópico [análise de armazenamento de operações registradas e mensagens de status](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) para determinar quais solicitações são faturáveis. Em seguida, você pode comparar seus logs e dados de métricas com as mensagens de status para ver se você foi cobrado por uma solicitação específica. Você também pode usar as tabelas no tópico anterior para investigar a disponibilidade de um serviço de armazenamento ou de uma operação de API individual.

## <a name="next-steps"></a>Passos seguintes
* [Monitorizar uma conta de armazenamento no portal do Azure](storage-monitor-storage-account.md)
* [Métricas de Análise de Armazenamento](storage-analytics-metrics.md)
* [Log de Análise de Armazenamento](storage-analytics-logging.md)
