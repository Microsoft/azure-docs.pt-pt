---
title: Criar uma loja de conhecimentos (pré-visualização) no portal Azure
titleSuffix: Azure Cognitive Search
description: Utilize o assistente de dados da Importação para criar uma loja de conhecimentos utilizada para persistir em conteúdos enriquecidos. Conecte-se a uma loja de conhecimentos para análise de outras apps ou envie conteúdo enriquecido para processos a jusante. Esta funcionalidade encontra-se atualmente em pré-visualização pública.
author: HeidiSteen
ms.author: heidist
manager: nitinme
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 01/29/2020
ms.openlocfilehash: 21279b2b4735a25210e8373d76d0d63f9c711bfc
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "77472371"
---
# <a name="quickstart-create-an-azure-cognitive-search-knowledge-store-in-the-azure-portal"></a>Quickstart: Criar uma loja de conhecimentos de pesquisa cognitiva Azure no portal Azure

> [!IMPORTANT] 
> A loja de conhecimento está atualmente em pré-visualização pública. A funcionalidade de pré-visualização é fornecida sem um acordo de nível de serviço, e não é recomendada para cargas de trabalho de produção. Para mais informações, consulte [os Termos Suplementares de Utilização para pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)do Microsoft Azure . 

A knowledge store é uma característica da Pesquisa Cognitiva Azure que persiste a produção de um pipeline de habilidades cognitivas para análises subsequentes ou processamento a jusante. 

Um pipeline aceita texto e imagens não estruturados como conteúdo bruto, aplica IA através de Serviços Cognitivos (como OCR, análise de imagem e processamento de linguagem natural), extrai informação e produz novas estruturas e informações. Um dos artefactos físicos criados por um oleoduto é uma loja de [conhecimento,](knowledge-store-concept-intro.md)a que pode aceder através de ferramentas para analisar e explorar conteúdos.

Neste arranque rápido, irá combinar serviços e dados na nuvem Azure para criar uma loja de conhecimento. Uma vez que tudo esteja no lugar, você executará o assistente de **dados de importação** no portal para juntar tudo. O resultado final é conteúdo de texto original mais conteúdo gerado por IA que pode ver no portal ( Explorador de[Armazenamento).](knowledge-store-view-storage-explorer.md)

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="create-services-and-load-data"></a>Criar serviços e dados de carga

Este quickstart utiliza pesquisa cognitiva Azure, armazenamento Azure Blob e [Serviços Cognitivos Azure](https://azure.microsoft.com/services/cognitive-services/) para a IA. 

Como a carga de trabalho é tão pequena, os Serviços Cognitivos são aproveitados nos bastidores para fornecer processamento gratuito até 20 transações diárias. Como o conjunto de dados é tão pequeno, pode ignorar a criação ou a anexação de um recurso dos Serviços Cognitivos.

1. [Baixe HotelReviews_Free.csv.](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Free.csv?sp=r&st=2019-11-04T01:23:53Z&se=2025-11-04T16:00:00Z&spr=https&sv=2019-02-02&sr=b&sig=siQgWOnI%2FDamhwOgxmj11qwBqqtKMaztQKFNqWx00AY%3D) Estes dados são dados de revisão de hotéis guardados num ficheiro CSV (originário de Kaggle.com) e contém 19 peças de feedback do cliente sobre um único hotel. 

1. [Crie uma conta](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal) de armazenamento Azure ou [encontre uma conta existente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/) sob a sua subscrição atual. Você usará o armazenamento Azure para que o conteúdo bruto seja importado, e a loja de conhecimento que é o resultado final.

   Escolha o tipo de conta **StorageV2 (finalidade geral V2).**

1. Abra as páginas dos serviços blob e crie um recipiente chamado *avaliações de hotéis.*

1. Clique em **Carregar**.

    ![Faça upload dos dados](media/knowledge-store-create-portal/upload-command-bar.png "Faça upload das avaliações do hotel")

1. Selecione o ficheiro **HotelReviews-Free.csv** que descarregou no primeiro passo.

    ![Crie o recipiente Azure Blob](media/knowledge-store-create-portal/hotel-reviews-blob-container.png "Crie o recipiente Azure Blob")

1. Está quase a acabar com este recurso, mas antes de sair destas páginas, use um link no painel de navegação esquerdo para abrir a página **Access Keys.** Obtenha uma cadeia de ligação para recuperar dados do armazenamento blob. Uma cadeia de ligação é semelhante ao seguinte exemplo:`DefaultEndpointsProtocol=https;AccountName=<YOUR-ACCOUNT-NAME>;AccountKey=<YOUR-ACCOUNT-KEY>;EndpointSuffix=core.windows.net`

1. Ainda no portal, mude para A Pesquisa Cognitiva Azure. [Crie um novo serviço](search-create-service-portal.md) ou [encontre um serviço existente.](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) Pode utilizar um serviço gratuito para este arranque rápido.

Está agora pronto para avançar com o assistente de dados da Importação.

## <a name="run-the-import-data-wizard"></a>Executar o assistente de dados da Importação

Na página de visão geral do serviço de pesquisa, clique em **dados de importação** na barra de comando para criar uma loja de conhecimento em quatro passos.

  ![Comando de importação de dados](media/cognitive-search-quickstart-blob/import-data-cmd2.png)

### <a name="step-1-create-a-data-source"></a>Passo 1: criar uma origem de dados

1. No **Connect aos seus dados,** escolha o armazenamento Do **Blob Azure,** selecione a conta e o recipiente que criou. 
1. Para o **nome,** insira `hotel-reviews-ds`.
1. Para o **modo Desminagem,** selecione **texto delimitado**e, em seguida, selecione a caixa de verificação First **Line Contém Cabeçalho.** Certifique-se de que o **carácter Delimiter** é uma vírposta (,).
1. Na Cadeia de **Ligação**, cola na corda de ligação que copiou da página **Access Keys** no Armazenamento Azure.
1. Em **Contentores,** introduza o nome do recipiente blob que guarda os dados.

    A sua página deve parecer semelhante à seguinte imagem.

    ![Criar um objeto de origem de dados](media/knowledge-store-create-portal/hotel-reviews-ds.png "Criar um objeto de origem de dados")

1. Continue para a página seguinte.

### <a name="step-2-add-cognitive-skills"></a>Passo 2: adicionar capacidades cognitivas

Neste passo de feiticeiro, criará sintetacom enriquecimentos cognitivos. Os dados de origem consistem em avaliações de clientes em vários idiomas. As competências relevantes para este conjunto de dados incluem a extração de frases-chave, a deteção de sentimentos e a tradução de texto. Num passo posterior, estes enriquecimentos serão "projetados" numa loja de conhecimento como mesas Azure.

1. Expandir **anexar serviços cognitivos**. **Os enriquecimentos gratuitos (enriquecimentos limitados)** são selecionados por defeito. Você pode usar este recurso porque o número de registos em HotelReviews-Free.csv é de 19 e este recurso gratuito permite até 20 transações por dia.
1. Expandir **Adicionar enriquecimentos**.
1. Para o nome `hotel-reviews-ss` **Skillset,** introduza .
1. Para **o campo de dados Fonte,** selecione **reviews_text**.
1. Para o nível de **granularidade**de enriquecimento, selecione **Páginas (5000 caracteres)**
1. Selecione estas habilidades cognitivas:
    + **Extrair expressões-chave**
    + **Traduzir texto**
    + **Detetar sentimento**

      ![Criar um conjunto de competências](media/knowledge-store-create-portal/hotel-reviews-ss.png "Criar um conjunto de competências")

1. Expandir **os enriquecimentos Save para a loja**de conhecimento.
1. Selecione estas projeções da **tabela Azure:**
    + **Documentos**
    + **Páginas**
    + **Frases-chave**
1. Introduza a **conta de armazenamento Linha de Ligação** que guardou num passo anterior.

    ![Configure loja de conhecimento](media/knowledge-store-create-portal/hotel-reviews-ks.png "Configure loja de conhecimento")

1. Opcionalmente, baixe um modelo power BI. Quando acede ao modelo do assistente, o ficheiro .pbit local é adaptado para refletir a forma dos seus dados.

1. Continue para a página seguinte.

### <a name="step-3-configure-the-index"></a>Passo 3: configurar o índice

Neste passo de assistente, irá configurar um índice para consultas opcionais de pesquisa de texto completo. O assistente irá provar a sua fonte de dados para inferir campos e tipos de dados. Só precisa selecionar os atributos para o seu comportamento desejado. Por exemplo, o atributo **Recuperável** permitirá que o serviço de pesquisa devolva um valor de campo enquanto o **Searchable** permitirá a pesquisa completa de texto no campo.

1. Para o nome `hotel-reviews-idx`do **índice,** insira .
1. Para atributos, aceite as seleções padrão: **Recuperável** e **Pesquisável** para os novos campos que o pipeline está a criar.

    O seu índice deve ser semelhante à seguinte imagem. Porque a lista é longa, nem todos os campos são visíveis na imagem.

    ![Configurar um índice](media/knowledge-store-create-portal/hotel-reviews-idx.png "Configurar um índice")

1. Continue para a página seguinte.

### <a name="step-4-configure-the-indexer"></a>Passo 4: configurar o indexador

Neste passo de assistente, irá configurar um indexante que irá reunir a fonte de dados, o skillset e o índice que definiu nos passos do assistente anteriores.

1. Para **Nome,** insira `hotel-reviews-idxr`.
1. Para **agendar,** mantenha o padrão **Uma vez**.
1. Clique **em Submeter** para executar o indexador. A extração de dados, a indexação, a aplicação de competências cognitivas acontecem neste passo.

## <a name="monitor-status"></a>Estado do monitor

A indexação das habilidades cognitivas demora mais tempo a completar do que a indexação típica baseada em texto. O assistente deve abrir a lista indexer na página de visão geral para que possa acompanhar o progresso. Para auto-navegação, vá à página de visão geral e clique em **Indexers**.

No portal Azure, também pode monitorizar o registo de atividade de Notificações para um link de estado de notificação de **pesquisa cognitiva de Azure** clicável. A execução pode levar vários minutos para ser concluída.

## <a name="next-steps"></a>Passos seguintes

Agora que enriqueceu os seus dados usando serviços cognitivos e projetou os resultados numa loja de conhecimento, pode utilizar o Storage Explorer ou o Power BI para explorar o seu conjunto de dados enriquecido.

Pode ver conteúdos no Storage Explorer ou dar um passo mais além com o Power BI para obter insights através da visualização.

> [!div class="nextstepaction"]
> [Vista com Storage Explorer](knowledge-store-view-storage-explorer.md)
> [Connect with Power BI](knowledge-store-connect-power-bi.md)

> [!Tip]
> Se quiser repetir este exercício ou experimentar um ultra-enriquecimento de IA diferente, elimine o indexador de comentários *de hotel-idxr.* A desapagamento do indexante repõe o contador de transações diário gratuito de volta a zero para o processamento de Serviços Cognitivos.
