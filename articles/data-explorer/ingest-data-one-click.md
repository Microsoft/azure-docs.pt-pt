---
title: Utilize uma ingestão de um clique para ingerir dados no Azure Data Explorer
description: Visão geral de ingerir (carregar) dados no Azure Data Explorer simplesmente, utilizando uma ingestão de um clique.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: overview
ms.date: 03/29/2020
ms.openlocfilehash: 5bde63427ce76f14832551864bbf2c3d8e015fd6
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521599"
---
# <a name="what-is-one-click-ingestion"></a>O que é ingestão de um clique? 

A ingestão de um clique permite-lhe ingerir rapidamente dados e sugerir automaticamente tabelas e estruturas de mapeamento, com base numa fonte de dados no Azure Data Explorer. 

Utilizando o Azure Data Explorer Web UI, pode ingerir dados a partir de armazenamento (ficheiro blob), um ficheiro local ou um recipiente (até 10.000 blobs). Também pode definir uma grelha de eventos num recipiente para ingestão contínua. Os dados podem ser ingeridos numa tabela existente ou nova em JSON, CSV e [outros formatos.](#file-formats) Uma ingestão de cliques pode sugerir uma estrutura para uma nova tabela e mapeamento de tabela, com base na fonte de dados, e fornecer uma plataforma intuitiva para ajustar a estrutura da tabela de uma tabela existente e mapeamento de tabela. Um clique de ingestão ingerirá os dados na tabela em poucos minutos.

A ingestão de um clique é particularmente útil quando ingerir dados pela primeira vez, ou quando o esquema dos seus dados não lhe é familiar.

## <a name="prerequisites"></a>Pré-requisitos

* Se não tiver uma subscrição do Azure, crie uma [conta do Azure gratuita](https://azure.microsoft.com/free/) antes de começar.
* Criar [um cluster azure Data Explorer e base de dados.](create-cluster-database-portal.md)
* Inscreva-se na UI Web do [Azure Data Explorer](https://dataexplorer.azure.com/) e adicione uma [ligação ao seu cluster](/azure/data-explorer/web-query-data#add-clusters).

## <a name="file-formats"></a>Formatos de ficheiro

A ingestão de um clique suporta a ingestão de uma nova tabela a partir de dados de origem em qualquer um dos seguintes formatos:
* JSON
* CSV
* Rio TSV
* SCSV
* SOHSV
* TSVE
* PSV

## <a name="one-click-ingestion-wizard"></a>Assistente de ingestão de um clique

O assistente de ingestão de um clique guia-o através do processo de ingestão de um clique. 

> [!Note]
> Esta secção descreve o feiticeiro em geral. As opções selecionadas dependem se está a ingerir numa mesa nova ou existente. Para obter mais informações, consulte:
    > * Ingerir [numa nova mesa](one-click-ingestion-new-table.md)
    > * Ingerir numa [mesa existente](one-click-ingestion-existing-table.md) 
    
1. Para aceder ao assistente, clique na base de *dados* ou na linha de *mesa* no menu esquerdo do Azure Data Explorer web UI e selecione **ingest novos dados (pré-visualização)**.

    ![Selecione ingestão de um clique na UI Web](media/ingest-data-one-click/one-click-ingestion-in-webui.png)   

1. O assistente guia-o através das seguintes opções:
       * Ingerir numa [mesa existente](one-click-ingestion-existing-table.md)
       * Ingerir [numa nova mesa](one-click-ingestion-new-table.md)
       * Ingerir dados de: * Armazenamento blob * Um arquivo local * Um recipiente
       * Introduza o tamanho da amostra, de 1 a 10.000 linhas (apenas do recipiente)
       
1. Quando selecionou com sucesso a fonte de dados, é apresentada uma pré-visualização dos dados. 
    Se estiver a ingerir dados a partir de um recipiente, pode filtrar os dados de modo a que apenas sejam ingeridos ficheiros com prefixos específicos ou extensões de ficheiros. Por exemplo, só pode querer ingerir ficheiros com nomes de ficheiros a começar pela palavra *Europa*, ou apenas ficheiros com a extensão *.json*. 

1. Clique em **Editar schema**. Se estiver a ingerir dados numa tabela específica, pode mapear as colunas de origem para as colunas-alvo e decidir se deve ou não incluir nomes de colunas.

1. Inicie o processo de ingestão de dados.

> [!Note]
> Se a sua fonte de dados for um recipiente, note que a política de agregação de dados do Azure Data Explorer (loting) foi concebida para otimizar o processo de ingestão. Por predefinição, a apólice está configurada a 5 minutos ou 500 MB de dados, para que possa experimentar a latência. Consulte [a política de lotação](/azure/kusto/concepts/batchingpolicy) para opções de agregação. Ao ingerir dados de outras fontes, a ingestão produzirá efeitos imediatos.

## <a name="next-steps"></a>Passos seguintes

* Decida se utilizará ingestão de um clique para ingerir dados [numa tabela existente](one-click-ingestion-existing-table.md) ou numa nova [tabela](one-click-ingestion-new-table.md)
* [Dados de consulta na Web UI do Web do Web do Web do Web do Web do Web do Web do Web do Web do Web do Web do Web do Web do Web](/azure/data-explorer/web-query-data)
* [Escreva consultas para O Explorador de Dados Azure usando a linguagem da consulta de Kusto](/azure/data-explorer/write-queries)