---
title: Utilize a análise de armazenamento do Azure para recolher dados de registos e métricas | Documentos da Microsoft
description: Análise de armazenamento permite-lhe para controlar os dados de métricas para todos os serviços de armazenamento e para recolher registos para o armazenamento de BLOBs, filas e tabelas.
services: storage
author: roygara
ms.service: storage
ms.devlang: dotnet
ms.topic: article
ms.date: 03/03/2017
ms.author: rogarana
ms.subservice: common
ms.openlocfilehash: eb85f8c756e7373a8dedabbce362cfa534e56fd8
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57849181"
---
# <a name="storage-analytics"></a>Análise de Armazenamento

A Análise de Armazenamento do Azure leva a cabo o registo e fornece dados métricos relativamente a uma conta de armazenamento. Pode utilizar estes dados para rastrear pedidos, analisar tendências de utilização e diagnosticar problemas relacionados com a sua conta de armazenamento.

Para utilizar a análise de armazenamento, terá de a ativar individualmente para cada serviço que pretende monitorizar. Pode ativá-lo a partir da [portal do Azure](https://portal.azure.com). Para obter detalhes, consulte [monitorizar uma conta de armazenamento no portal do Azure](storage-monitor-storage-account.md). Também pode ativar a análise de armazenamento através de programação com a API REST ou a biblioteca de cliente. Utilize o [definir as propriedades de serviço Blob](/rest/api/storageservices/set-blob-service-properties), [definir as propriedades do serviço de fila](/rest/api/storageservices/set-queue-service-properties), [definir as propriedades do serviço de tabela](/rest/api/storageservices/set-table-service-properties), e [definir as propriedades do serviço de arquivo](/rest/api/storageservices/Get-File-Service-Properties)operações para ativar a análise de armazenamento para cada serviço.

Os dados agregados são armazenados num blob bem conhecido (para o registo) e em tabelas bem conhecidas (para métricas), que podem ser acessadas utilizando o serviço de BLOBs e as APIs de serviço de tabela.

Análise de armazenamento tem um limite de 20 TB na quantidade de dados armazenados, que é independentes do limite total para a sua conta de armazenamento. Para obter mais informações sobre os limites de conta de armazenamento, consulte [metas de desempenho e escalabilidade do armazenamento do Azure](storage-scalability-targets.md).

Para obter um guia detalhado sobre como utilizar a análise de armazenamento e outras ferramentas para identificar, diagnosticar e resolver problemas relacionados com o armazenamento do Azure, consulte [monitorizar, diagnosticar e resolver problemas de armazenamento do Microsoft Azure](storage-monitoring-diagnosing-troubleshooting.md).

## <a name="billing-for-storage-analytics"></a>Faturação de análise de armazenamento

Todos os dados de métricas é escrito pelos serviços de uma conta de armazenamento. Como resultado, cada operação de escrita efetuada por análise de armazenamento está sujeitos a faturação. Além disso, a quantidade de armazenamento utilizada pelo dados das métricas também está sujeitos a faturação.

As seguintes ações realizadas por análise de armazenamento são a cobrar:

* Pedidos para criar blobs para o registo.
* Pedidos para criar entidades da tabela de métricas.

Se tiver configurado uma política de retenção de dados, não é cobradas para a eliminação de transações quando a análise de armazenamento elimina dados antigos de registo e as métricas. No entanto, a eliminação de transações de um cliente é a cobrar. Para obter mais informações sobre as políticas de retenção, consulte [definir uma política de retenção de dados de análise de armazenamento](https://msdn.microsoft.com/library/azure/hh343263.aspx).

### <a name="understanding-billable-requests"></a>Noções básicas sobre pedidos faturáveis

Cada solicitação feita ao serviço de uma conta de armazenamento está sujeito a faturação ou não faturável. Análise de armazenamento de registos cada pedidos individuais efetuados para um serviço, incluindo uma mensagem de estado que indica como o pedido foi processado. Da mesma forma, a análise de armazenamento armazena as métricas para um serviço e as operações de API do serviço, incluindo as percentagens e a contagem de determinadas mensagens de estado. Juntos, esses recursos podem ajudar a analisar seus pedidos faturáveis, fazer melhorias na sua aplicação e diagnosticar problemas com os pedidos aos seus serviços. Para obter mais informações sobre a faturação, consulte [Noções básicas sobre faturação do Azure Storage - largura de banda, transações e capacidade](https://blogs.msdn.com/b/windowsazurestorage/archive/2010/07/09/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity.aspx).

Ao analisar os dados de análise de armazenamento, pode utilizar as tabelas a [operações com sessão iniciada da análise de armazenamento e as mensagens de estado](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) tópico para determinar que pedidos são a cobrar. Em seguida, pode comparar os dados de métricas para as mensagens de estado para ver se foram bem cobrados por uma solicitação específica e registos. Também pode utilizar as tabelas no tópico anterior para investigar a disponibilidade para um serviço de armazenamento ou a operação de API individual.

## <a name="next-steps"></a>Passos Seguintes
* [Monitorizar uma conta de armazenamento no portal do Azure](storage-monitor-storage-account.md)
* [Métricas da análise de armazenamento](storage-analytics-metrics.md)
* [Registo de análise de armazenamento](storage-analytics-logging.md)
