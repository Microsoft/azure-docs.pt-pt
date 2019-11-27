---
title: Usar a ingestão de streaming para ingerir dados no Azure Data Explorer
description: Saiba mais sobre como ingerir (carregar) dados no Azure Data Explorer usando a ingestão de streaming.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/30/2019
ms.openlocfilehash: 4f9804ed0e7d6c83a4f6fc732f836fcecce1c2e7
ms.sourcegitcommit: a678f00c020f50efa9178392cd0f1ac34a86b767
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/26/2019
ms.locfileid: "74548344"
---
# <a name="streaming-ingestion-preview"></a>Ingestão de streaming (visualização)

A ingestão de streaming é destinada a cenários que exigem baixa latência com um tempo de ingestão de menos de 10 segundos para dados de volume variados. Ele é usado para a otimização do processamento operacional de muitas tabelas, em um ou mais bancos de dados em que o fluxo de dado em cada tabela é relativamente pequeno (poucos registros por segundo), mas o volume de ingestão de dados geral é alto (milhares de registros por segundo).

Use a ingestão clássica (em massa) em vez da ingestão de streaming quando a quantidade de dados aumentar para mais de 1 MB por segundo por tabela. Leia [visão geral de ingestão de dados](/azure/data-explorer/ingest-data-overview) para saber mais sobre os vários métodos de ingestão.

> [!NOTE]
> A ingestão de streaming não dá suporte aos seguintes recursos:
> * [Cursores de banco de dados](/azure/kusto/management/databasecursor).
> * [Mapeamento de dados](/azure/kusto/management/mappings). Somente o mapeamento [de dados pré-criado](/azure/kusto/management/tables#create-ingestion-mapping) tem suporte. 

## <a name="prerequisites"></a>Pré-requisitos

* Se não tiver uma subscrição do Azure, crie uma [conta do Azure gratuita](https://azure.microsoft.com/free/) antes de começar.
* Entre na [interface do usuário da Web](https://dataexplorer.azure.com/).
* Criar [um cluster de data Explorer do Azure e um banco de dados](create-cluster-database-portal.md)

## <a name="enable-streaming-ingestion-on-your-cluster"></a>Habilitar a ingestão de streaming no cluster

1. Na portal do Azure, vá para o cluster de Data Explorer do Azure. Em **configurações**, selecione **configurações**. 
1. No painel **configurações** , selecione **ativado** para habilitar a **ingestão de streaming**.
1. Selecione **Guardar**.
 
    ![ingestão de streaming em](media/ingest-data-streaming/streaming-ingestion-on.png)
 
1. Na [interface do usuário da Web](https://dataexplorer.azure.com/), defina a [política de ingestão de streaming](/azure/kusto/concepts/streamingingestionpolicy) em tabela (s) ou banco (s) de dados que receberão o streaming de dado. 

    > [!NOTE]
    > * Se a política for definida no nível de banco de dados, todas as tabelas no banco de dados serão habilitadas para ingestão de streaming.
    > * A política aplicada pode fazer referência somente a dados recentemente ingeridos e não a outras tabelas no banco de dados.

## <a name="use-streaming-ingestion-to-ingest-data-to-your-cluster"></a>Usar a ingestão de streaming para ingerir dados para o cluster

Há dois tipos de ingestão de streaming com suporte:

* [Hub de eventos](/azure/data-explorer/ingest-data-event-hub) usado como uma fonte de dados
* A ingestão personalizada exige que você escreva um aplicativo que usa uma das bibliotecas de cliente do Azure Data Explorer. Consulte [exemplo de ingestão de streaming](https://github.com/Azure/azure-kusto-samples-dotnet/tree/master/client/StreamingIngestionSample) para um aplicativo de exemplo.

### <a name="choose-the-appropriate-streaming-ingestion-type"></a>Escolha o tipo de ingestão de streaming apropriado

|   |Hub de Eventos  |Ingestão personalizada  |
|---------|---------|---------|
|Atraso de dados entre o início da ingestão e os dados disponíveis para consulta   |    atraso mais longo     |   atraso mais curto      |
|Sobrecarga de desenvolvimento    |   configuração rápida e fácil, sem sobrecarga de desenvolvimento    |   alta sobrecarga de desenvolvimento para o aplicativo manipular erros e garantir a consistência dos dados     |

## <a name="disable-streaming-ingestion-on-your-cluster"></a>Desabilitar a ingestão de streaming no cluster

> [!WARNING]
> Desabilitar a ingestão de streaming pode levar algumas horas.

1. Remova a [política de ingestão de streaming](/azure/kusto/concepts/streamingingestionpolicy) de todas as tabelas e bancos de dados relevantes. A remoção da política de ingestão de streaming dispara a movimentação de dados de ingestão de streaming do armazenamento inicial para o armazenamento permanente no repositório de coluna (extensões ou fragmentos). A movimentação de dados pode durar entre alguns segundos e algumas horas, dependendo da quantidade de dados no armazenamento inicial e da utilização de CPU e memória do cluster.
1. Na portal do Azure, vá para o cluster de Data Explorer do Azure. Em **configurações**, selecione **configurações**. 
1. No painel **configurações** , selecione **desativado** para desabilitar a **ingestão de streaming**.
1. Selecione **Guardar**.

    ![Ingestão de streaming desativada](media/ingest-data-streaming/streaming-ingestion-off.png)

## <a name="limitations"></a>Limitações

* O desempenho de ingestão de streaming e a capacidade aumentam com tamanhos de VM e cluster maiores. Ingestãos simultâneas são limitadas a 6 ingestãos por núcleo. Por exemplo, para SKUs de 16 núcleos, como D14 e L16, a carga com suporte máxima é de 96 ingestãos simultâneas. Para SKUs de 2 núcleos, como D11, a carga com suporte máxima é de 12 ingestãos simultâneas.
* A limitação de tamanho de dados por solicitação de ingestão é 4 MB.
* As atualizações de esquema, como criação e modificação de tabelas e mapeamentos de ingestão, podem levar até 5 minutos para o serviço de ingestão de streaming.
* Habilitar a ingestão de streaming em um cluster, mesmo quando os dados não são ingeridos via streaming, o usa parte do disco SSD local das máquinas de cluster para dados de ingestão de streaming e reduz o armazenamento disponível para cache ativo.

## <a name="next-steps"></a>Passos Seguintes

* [Consultar dados no Azure Data Explorer](web-query-data.md)
