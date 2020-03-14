---
title: Utilize a ingestão de streaming para ingerir dados no Azure Data Explorer
description: Saiba como ingerir (carregar) dados no Azure Data Explorer utilizando a ingestão de streaming.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/30/2019
ms.openlocfilehash: d7d2bcf487c37fbb523b648d5aa4c572add5dfa9
ms.sourcegitcommit: c29b7870f1d478cec6ada67afa0233d483db1181
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79297089"
---
# <a name="streaming-ingestion-preview"></a>Ingestão de streaming (Pré-visualização)

Utilize a ingestão de streaming quando necessitar de baixa latência com um tempo de ingestão inferior a 10 segundos para dados de volume variados. É usado para otimizar o processamento operacional de muitas tabelas, em uma ou mais bases de dados, onde o fluxo de dados em cada tabela é relativamente pequeno (poucos registos por segundo), mas o volume global de ingestão de dados é elevado (milhares de registos por segundo). 

Utilize ingestão a granel em vez de transmitir ingestão quando a quantidade de dados cresce para mais de 1 MB por segundo por tabela. Leia a visão geral da [ingestão](/azure/data-explorer/ingest-data-overview) de dados para saber mais sobre os vários métodos de ingestão.

## <a name="prerequisites"></a>Pré-requisitos

* Se não tiver uma subscrição do Azure, crie uma [conta do Azure gratuita](https://azure.microsoft.com/free/) antes de começar.
* Inscreva-se na [Web UI](https://dataexplorer.azure.com/).
* Criar [um cluster e base de dados azure Data Explorer](create-cluster-database-portal.md)

## <a name="enable-streaming-ingestion-on-your-cluster"></a>Ativar a ingestão de streaming no seu cluster

> [!WARNING]
> Por favor, reveja as [limitações](#limitations) antes de permitir a ingestão a vapor.

1. No portal Azure, vá ao seu cluster Azure Data Explorer. Em **Configurações,** selecione **Configurações**. 
1. No painel de **configurações,** selecione **On** para ativar a **ingestão**de streaming .
1. Selecione **Guardar**.
 
    ![ingestão de streaming em](media/ingest-data-streaming/streaming-ingestion-on.png)
 
1. Na [Web UI,](https://dataexplorer.azure.com/)defina a política de [ingestão](/azure/kusto/management/streamingingestionpolicy) de streaming na tabela ou na ou na base de dados que receberão dados de streaming. 

    > [!NOTE]
    > * Se a política for definida ao nível da base de dados, todas as tabelas da base de dados estão habilitadas para o streaming de ingestão.
    > * A política aplicada só pode fazer referência a dados recentemente ingeridos e não a outros quadros na base de dados.

## <a name="use-streaming-ingestion-to-ingest-data-to-your-cluster"></a>Use a ingestão de streaming para ingerir dados para o seu cluster

Existem dois tipos de ingestão de streaming suportados:


* [**Centro de Eventos**](/azure/data-explorer/ingest-data-event-hub), que é usado como fonte de dados
* **A ingestão personalizada** requer que escreva uma aplicação que utiliza uma das bibliotecas de clientes do Azure Data Explorer. Consulte a amostra de [ingestão](https://github.com/Azure/azure-kusto-samples-dotnet/tree/master/client/StreamingIngestionSample) de streaming para obter uma aplicação de amostragem.

### <a name="choose-the-appropriate-streaming-ingestion-type"></a>Escolha o tipo de ingestão de streaming apropriado

|   |Hub de Eventos  |Ingestão Personalizada  |
|---------|---------|---------|
|Atraso de dados entre o início da ingestão e os dados disponíveis para consulta   |    atraso mais longo     |   atraso mais curto      |
|Despesas gerais de desenvolvimento    |   configuração rápida e fácil, sem despesas de desenvolvimento    |   altas despesas de desenvolvimento para aplicação para lidar com erros e garantir a consistência dos dados     |

## <a name="disable-streaming-ingestion-on-your-cluster"></a>Desative a ingestão de streaming no seu cluster

> [!WARNING]
> Desativar a ingestão de streaming pode demorar algumas horas.

1. Deixe cair a política de [ingestão](/azure/kusto/management/streamingingestionpolicy) de streaming de todas as tabelas e bases de dados relevantes. A remoção da política de ingestão de streaming desencadeia o movimento de dados de ingestão do armazenamento inicial para o armazenamento permanente na loja de colunas (extensões ou fragmentos). O movimento de dados pode durar entre alguns segundos a algumas horas, dependendo da quantidade de dados no armazenamento inicial, e de como o CPU e a memória são utilizados pelo cluster.
1. No portal Azure, vá ao seu cluster Azure Data Explorer. Em **Configurações,** selecione **Configurações**. 
1. No painel de **configurações,** selecione **Off** para desativar a **ingestão**de streaming .
1. Selecione **Guardar**.

    ![Ingestão de streaming fora](media/ingest-data-streaming/streaming-ingestion-off.png)

## <a name="limitations"></a>Limitações

* A ingestão de streaming não suporta [os cursores](/azure/kusto/management/databasecursor) de base de dados ou [o mapeamento de dados.](/azure/kusto/management/mappings) Apenas o mapeamento de dados [pré-criado](/azure/kusto/management/create-ingestion-mapping-command) é suportado. 
* Fluxo de desempenho de ingestão e escalas de capacidade com tamanhos de VM e cluster aumentados. As ingestão simultâneas limitam-se a seis ingestão por núcleo. Por exemplo, para 16 SKUs core, como D14 e L16, a carga máxima suportada é de 96 ingestão simultânea. Para duas SKUs centrais, como o D11, a carga máxima suportada é de 12 ingestão simultânea.
* A limitação do tamanho dos dados por pedido de ingestão é de 4 MB.
* As atualizações de Schema, tais como a criação e modificação de tabelas e mapeamentos de ingestão, podem demorar até cinco minutos para o serviço de ingestão de streaming.
* Permitir o streaming de ingestão num cluster, mesmo quando os dados não são ingeridos através do streaming, utiliza parte do disco SSD local das máquinas de cluster para streaming de dados de ingestão e reduz o armazenamento disponível para cache quente.
* [As etiquetas](/azure/kusto/management/extents-overview#extent-tagging) de extensão não podem ser definidas nos dados de ingestão de streaming.

## <a name="next-steps"></a>Passos Seguintes

* [Dados de consulta no Explorador de Dados do Azure](web-query-data.md)
