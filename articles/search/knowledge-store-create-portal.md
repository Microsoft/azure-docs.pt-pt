---
title: Criar uma loja de conhecimento no portal Azure
titleSuffix: Azure Cognitive Search
description: Utilize o assistente de dados Import para criar uma loja de conhecimentos utilizada para o conteúdo enriquecido persistente. Conecte-se a uma loja de conhecimentos para análise de outras apps ou envie conteúdo enriquecido para processos a jusante.
author: HeidiSteen
ms.author: heidist
manager: nitinme
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 06/30/2020
ms.openlocfilehash: df6da5ce1dcd5213b3e54abe54c087bfdd8480c5
ms.sourcegitcommit: 8a1ba1ebc76635b643b6634cc64e137f74a1e4da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2020
ms.locfileid: "94379978"
---
# <a name="quickstart-create-an-azure-cognitive-search-knowledge-store-in-the-azure-portal"></a>Quickstart: Criar uma loja de conhecimentos Azure Cognitive Search no portal Azure

A loja de conhecimento é uma característica da Azure Cognitive Search que persiste na saída de um pipeline de processamento de conteúdos para análises subsequentes ou processamento a jusante. 

Um pipeline aceita conteúdo de texto e imagem não estruturado, aplica IA alimentada por Serviços Cognitivos (como OCR e processamento de linguagem natural), e produz novas estruturas e informações que não existiam anteriormente. Um dos artefactos físicos criados por um oleoduto é uma [loja de conhecimento,](knowledge-store-concept-intro.md)que pode aceder através de ferramentas para analisar e explorar conteúdos.

Neste arranque rápido, você combinará serviços e dados na nuvem Azure para criar uma loja de conhecimento. Uma vez que tudo esteja no lugar, você executará o assistente **de dados Import** no portal para juntar tudo. O resultado final é conteúdo de texto original mais conteúdo gerado por IA que pode ver no portal[(Storage Explorer).](knowledge-store-view-storage-explorer.md)

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, deve ter o seguinte:

+ Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/)

+ Um serviço de Pesquisa Cognitiva Azure. [Crie um serviço](search-create-service-portal.md) ou [encontre um serviço existente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) sob a sua subscrição atual. Pode utilizar um serviço gratuito para este arranque rápido. 

+ Uma conta de armazenamento Azure com [armazenamento Blob](../storage/blobs/index.yml).

> [!NOTE]
> Este quickstart também usa [serviços cognitivos Azure](https://azure.microsoft.com/services/cognitive-services/) para a IA. Como a carga de trabalho é tão pequena, os Serviços Cognitivos são aproveitados nos bastidores para processamento gratuito até 20 transações. Isto significa que você pode completar este exercício sem ter que criar um recurso adicional de Serviços Cognitivos.

## <a name="set-up-your-data"></a>Configurar os seus dados

Nos passos seguintes, instale um recipiente blob no Azure Storage para armazenar ficheiros de conteúdo heterogéneo.

1. [Descarregue HotelReviews_Free.csv. ](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Free.csv?sp=r&st=2019-11-04T01:23:53Z&se=2025-11-04T16:00:00Z&spr=https&sv=2019-02-02&sr=b&sig=siQgWOnI%2FDamhwOgxmj11qwBqqtKMaztQKFNqWx00AY%3D) Estes dados são dados de revisão do hotel guardados num ficheiro CSV (originários de Kaggle.com) e contém 19 peças de feedback do cliente sobre um único hotel. 

1. [Crie uma conta de armazenamento Azure](../storage/common/storage-account-create.md?tabs=azure-portal) ou [encontre uma conta existente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/) na sua subscrição atual. Você usará o armazenamento Azure para que tanto o conteúdo bruto seja importado, como a loja de conhecimento que é o resultado final.

   + Escolha o tipo de conta **StorageV2 (finalidade geral V2).**

1. Abra as páginas de serviços Blob e crie um recipiente chamado *hot-reviews*.

1. Clique em **Carregar**.

    ![Faça o upload dos dados](media/knowledge-store-create-portal/upload-command-bar.png "Faça upload das avaliações do hotel")

1. Selecione o ficheiro **HotelReviews-Free.csv** que descarregou no primeiro passo.

    ![Criar o recipiente Azure Blob](media/knowledge-store-create-portal/hotel-reviews-blob-container.png "Criar o recipiente Azure Blob")

1. Antes de deixar as páginas de armazenamento Blob, utilize um link no painel de navegação esquerdo para abrir a página **'Chaves de Acesso'.** Obtenha uma cadeia de ligação para obter dados do armazenamento blob. Uma cadeia de ligação é semelhante ao seguinte exemplo: `DefaultEndpointsProtocol=https;AccountName=<YOUR-ACCOUNT-NAME>;AccountKey=<YOUR-ACCOUNT-KEY>;EndpointSuffix=core.windows.net`

Está agora pronto para avançar com o assistente **de dados de Importação.**

## <a name="run-the-import-data-wizard"></a>Executar o assistente de dados de importação

1. Inicie sessão no [portal do Azure](https://portal.azure.com/) com a sua conta do Azure.

1. [Encontre o seu serviço de pesquisa](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/) e na página 'Vista Geral', clique em Importar **dados** na barra de comando para criar uma loja de conhecimento em quatro etapas.

   ![Comando de importação de dados](media/cognitive-search-quickstart-blob/import-data-cmd2.png)

### <a name="step-1-create-a-data-source"></a>Passo 1: criar uma origem de dados

1. Em **Ligar aos seus dados,** escolha o armazenamento **Azure Blob,** selecione a conta e o recipiente que criou. 

1. Para o **Nome,** insira `hotel-reviews-ds` .

1. Para **o modo de análise** , selecione texto **delimitado** e, em seguida, selecione a caixa de verificação **First Line Contains Header.** Certifique-se de que o **personagem Delimiter** é uma vírgula (,).

1. Em **Connection String** , cole na cadeia de ligação que copiou da página **'Teclas de acesso'** no Azure Storage.

1. Em **Recipientes, insira** o nome do recipiente blob que guarda os dados.

    A sua página deve ser semelhante à seguinte.

    ![Criar um objeto de origem de dados](media/knowledge-store-create-portal/hotel-reviews-ds.png "Criar um objeto de origem de dados")

1. Continue para a próxima página.

### <a name="step-2-add-cognitive-skills"></a>Passo 2: adicionar capacidades cognitivas

Neste passo de feiticeiro, você vai criar um skillset com enriquecimentos cognitivos de habilidades. Os dados de origem consistem em avaliações de clientes em vários idiomas. As competências relevantes para este conjunto de dados incluem a extração de frases-chave, a deteção de sentimentos e a tradução de texto. Posteriormente, estes enriquecimentos serão "projetados" numa loja de conhecimento como mesas Azure.

1. Expandir **Serviços Cognitivos anexados**. **Os enriquecimentos gratuitos (enriquecimentos limitados)** são selecionados por padrão. Pode utilizar este recurso porque o número de registos em HotelReviews-Free.csv é de 19 e este recurso gratuito permite até 20 transações por dia.

1. Expandir **Adicione os enriquecimentos.**

1. Para **o nome Skillset,** insira `hotel-reviews-ss` .

1. Para **o campo de dados de origem** , selecione **reviews_text**.

1. Para **o nível de granularidade de enriquecimento,** selecione **Páginas (pedaços de 5000 caracteres)**

1. Selecione estas habilidades cognitivas:
    + **Extrair frases-chave**
    + **Traduzir texto**
    + **Detetar sentimento**

      ![Criar um conjunto de competências](media/knowledge-store-create-portal/hotel-reviews-ss.png "Criar um conjunto de competências")

1. Expandir **Guardar enriquecimentos para loja de conhecimento.**

1. Selecione estas **projeções da tabela Azure:**
    + **Documentos**
    + **Páginas**
    + **Frases-chave**

1. Introduza a **cadeia de conexão da conta de armazenamento** que guardou num passo anterior.

    ![Configure loja de conhecimento](media/knowledge-store-create-portal/hotel-reviews-ks.png "Configure loja de conhecimento")

1. Opcionalmente, faça o download de um modelo de Power BI. Ao aceder ao modelo a partir do assistente, o ficheiro local .pbit é adaptado para refletir a forma dos seus dados.

1. Continue para a próxima página.

### <a name="step-3-configure-the-index"></a>Passo 3: configurar o índice

Neste passo de assistente, irá configurar um índice para consultas de pesquisa de texto completo opcional. O assistente irá amostrar a sua fonte de dados para inferir campos e tipos de dados. Basta selecionar os atributos para o comportamento pretendido. Por exemplo, o atributo **Retrievable** permitirá que o serviço de pesquisa devolva um valor de campo enquanto o **Searchable** permitirá a pesquisa completa por texto no campo.

1. Para **o nome do índice,** insira `hotel-reviews-idx` .

1. Para atributos, aceite as seleções padrão: **Recuperável** e **Pesmável** para os novos campos que o pipeline está a criar.

    O seu índice deve ser semelhante à seguinte imagem. Como a lista é longa, nem todos os campos são visíveis na imagem.

    ![Configure um índice](media/knowledge-store-create-portal/hotel-reviews-idx.png "Configure um índice")

1. Continue para a próxima página.

### <a name="step-4-configure-the-indexer"></a>Passo 4: configurar o indexador

Neste passo de assistente, irá configurar um indexante que reunirá a fonte de dados, o skillset e o índice que definiu nos passos de assistente anteriores.

1. Para **nome,** insira `hotel-reviews-idxr` .

1. Para **Agendar** , mantenha o predefinido **Uma vez**.

1. Clique **em Submeter** para executar o indexante. Extração de dados, indexação, aplicação de competências cognitivas acontecem neste passo.

## <a name="monitor-status"></a>Estado do monitor

A indexação cognitiva de habilidades demora mais tempo a completar do que a indexação típica baseada em texto. O assistente deve abrir a lista de Indexante na página de visão geral para que possa acompanhar o progresso. Para auto-navegação, vá à página 'Vista Geral' e clique em **Indexers**.

No portal Azure, também pode monitorizar o registo de atividades de Notificações para um link de **estado de notificação de pesquisa cognitiva Azure** clicável. A execução pode levar vários minutos para ser concluída.

## <a name="next-steps"></a>Próximos passos

Agora que enriqueceu os seus dados usando os Serviços Cognitivos e projetou os resultados numa loja de conhecimento, pode utilizar o Storage Explorer ou o Power BI para explorar o seu conjunto de dados enriquecido.

Pode ver conteúdo no Storage Explorer ou dar um passo mais além com o Power BI para obter insights através da visualização.

> [!div class="nextstepaction"]
> [Vista com Explorador de Armazenamento](knowledge-store-view-storage-explorer.md) 
>  [Conecte-se com Power BI](knowledge-store-connect-power-bi.md)

> [!Tip]
> Se quiser repetir este exercício ou experimentar uma passagem diferente de enriquecimento de IA, elimine o indexante *de avaliações de hotéis-idxr.* A eliminação do indexante repõe o contador de transações diários gratuito de volta a zero para o processamento de Serviços Cognitivos.
