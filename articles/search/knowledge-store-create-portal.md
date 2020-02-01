---
title: Criar uma loja de conhecimento (versão prévia) no portal do Azure
titleSuffix: Azure Cognitive Search
description: Use o assistente para importar dados para criar uma loja de conhecimento usada para manter o conteúdo aprimorado. Conecte-se a uma loja de conhecimento para análise de outros aplicativos ou envie conteúdo aprimorado para processos downstream. Este recurso está atualmente em visualização pública.
author: HeidiSteen
ms.author: heidist
manager: nitinme
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 01/29/2020
ms.openlocfilehash: b75b760704511627c74301ae3fff82c24a262e17
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/31/2020
ms.locfileid: "76904900"
---
# <a name="quickstart-create-an-azure-cognitive-search-knowledge-store-in-the-azure-portal"></a>Início rápido: criar um repositório de conhecimento do Azure Pesquisa Cognitiva no portal do Azure

> [!IMPORTANT] 
> A loja de conhecimento está atualmente em visualização pública. A funcionalidade de pré-visualização é fornecida sem um acordo de nível de serviço, e não é recomendada para cargas de trabalho de produção. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 

O repositório de conhecimento é um recurso do Azure Pesquisa Cognitiva que persiste a saída de um pipeline de habilidades cognitivas para análises subsequentes ou processamento de downstream. 

Um pipeline aceita texto não estruturado e imagens como conteúdo bruto, aplica o ia por meio de serviços cognitivas (como OCR, análise de imagem e processamento de idioma natural), extrai informações e gera novas estruturas e informações. Um dos artefatos físicos criados por um pipeline é uma [loja de conhecimento](knowledge-store-concept-intro.md), que pode ser acessada por meio de ferramentas para analisar e explorar o conteúdo.

Neste guia de início rápido, você combinará serviços e dados na nuvem do Azure para criar uma loja de conhecimento. Depois que tudo estiver em vigor, você executará o assistente de **importação de dados** no portal para reunir tudo isso. O resultado final é o conteúdo de texto original mais o conteúdo gerado pelo ia que você pode exibir no portal ([Gerenciador de armazenamento](knowledge-store-view-storage-explorer.md)).

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="create-services-and-load-data"></a>Criar serviços e carregar dados

Este guia de início rápido usa o Azure Pesquisa Cognitiva, o armazenamento de BLOBs do Azure e os [Serviços cognitivas do Azure](https://azure.microsoft.com/services/cognitive-services/) para o ia. 

Como a carga de trabalho é tão pequena, os serviços cognitivas são tocados nos bastidores para fornecer processamento gratuito para até 20 transações diariamente quando invocado do Azure Pesquisa Cognitiva. Desde que você use os dados de exemplo que fornecemos, você pode ignorar a criação ou anexação de um recurso de serviços cognitivas.

1. [Baixe HotelReviews_Free.csv.](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Free.csv?sp=r&st=2019-11-04T01:23:53Z&se=2025-11-04T16:00:00Z&spr=https&sv=2019-02-02&sr=b&sig=siQgWOnI%2FDamhwOgxmj11qwBqqtKMaztQKFNqWx00AY%3D) Esses dados são dados de revisão do Hotel salvos em um arquivo CSV (origina-se de Kaggle.com) e contêm 19 partes de comentários do cliente sobre um único hotel. 

1. [Crie uma conta de armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal) ou [localize uma conta existente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/) em sua assinatura atual. Você usará o armazenamento do Azure para o conteúdo bruto a ser importado e a loja de conhecimento que é o resultado final.

   Escolha o tipo de conta **StorageV2 (uso geral v2)** .

1. Abra as páginas de serviços de BLOB e crie um contêiner chamado *Hotel-Reviews*.

1. Clique em **Carregar**.

    ![Carregar os dados](media/knowledge-store-create-portal/upload-command-bar.png "Carregar as revisões do Hotel")

1. Selecione o arquivo **HotelReviews-Free. csv** que você baixou na primeira etapa.

    ![Criar o contêiner de blob do Azure](media/knowledge-store-create-portal/hotel-reviews-blob-container.png "Criar o contêiner de blob do Azure")

1. Você está quase pronto com esse recurso, mas antes de sair dessas páginas, use um link no painel de navegação esquerdo para abrir a página **chaves de acesso** . Obtenha uma cadeia de conexão para recuperar dados do armazenamento de BLOBs. Uma cadeia de conexão é semelhante ao exemplo a seguir: `DefaultEndpointsProtocol=https;AccountName=<YOUR-ACCOUNT-NAME>;AccountKey=<YOUR-ACCOUNT-KEY>;EndpointSuffix=core.windows.net`

1. Ainda no portal, alterne para o Azure Pesquisa Cognitiva. [Crie um novo serviço](search-create-service-portal.md) ou [encontre um serviço existente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices). Você pode usar um serviço gratuito para este guia de início rápido.

Agora você está pronto para mover o assistente de importação de dados.

## <a name="run-the-import-data-wizard"></a>Executar o assistente de importação de dados

Na página Visão geral do serviço de pesquisa, clique em **importar dados** na barra de comandos para criar uma loja de conhecimento em quatro etapas.

  ![Comando de importação de dados](media/cognitive-search-quickstart-blob/import-data-cmd2.png)

### <a name="step-1-create-a-data-source"></a>Passo 1: criar uma origem de dados

1. Em **conectar-se aos seus dados**, escolha **armazenamento de BLOBs do Azure**, selecione a conta e o contêiner que você criou. 
1. Para o **nome**, insira `hotel-reviews-ds`.
1. Para o **modo de análise**, selecione **texto delimitado**e, em seguida, selecione a caixa de seleção a **primeira linha contém cabeçalho** . Verifique se o **caractere delimitador** é uma vírgula (,).
1. Em **cadeia de conexão**, Cole a cadeia de conexão que você copiou da página **chaves de acesso** no armazenamento do Azure.
1. Em **contêineres**, insira o nome do contêiner de BLOB que contém os dados.

    Sua página deve ser semelhante à captura de tela a seguir.

    ![Criar um objeto de fonte de dados](media/knowledge-store-create-portal/hotel-reviews-ds.png "Criar um objeto de fonte de dados")

1. Vá para a próxima página.

### <a name="step-2-add-cognitive-skills"></a>Passo 2: adicionar capacidades cognitivas

Nesta etapa do assistente, você criará um configurador de habilidades com aprimoramentos de habilidades cognitivas. Os dados de origem consistem em análises de clientes em várias linguagens. As habilidades relevantes para esse conjunto de dados incluem extração de frases-chave, detecção de opiniões e tradução de texto. Em uma etapa posterior, esses aprimoramentos serão "projetados" em uma loja de conhecimento como tabelas do Azure.

1. Expanda **anexar serviços cognitivas**. **Gratuito (aprimoramentos limitados)** é selecionado por padrão. Você pode usar esse recurso porque o número de registros em HotelReviews-Free. csv é 19 e esse recurso gratuito permite até 20 transações por dia.
1. Expandir **Adicionar enriquecimentos**.
1. Para o **nome do skillset**, insira `hotel-reviews-ss`.
1. Para **o campo de dados Fonte,** selecione **reviews_text**.
1. Para **nível de granularidade de enriquecimento**, selecione **páginas (partes de 5000 caracteres)**
1. Selecione essas habilidades cognitivas:
    + **Extrair expressões-chave**
    + **Traduzir texto**
    + **Detectar sentimentos**

      ![Criar um habilidades](media/knowledge-store-create-portal/hotel-reviews-ss.png "Criar um conjunto de competências")

1. Expanda **salvar aprimoramentos na loja de conhecimento**.
1. Selecione estas **projeções de tabela do Azure**:
    + **Documento**
    + **Páginas**
    + **Frases-chave**
1. Insira a **cadeia de conexão da conta de armazenamento** que você salvou em uma etapa anterior.

    ![Configurar o repositório de conhecimento](media/knowledge-store-create-portal/hotel-reviews-ks.png "Configurar o repositório de conhecimento")

1. Opcionalmente, baixe um modelo de Power BI. Quando você acessa o modelo por meio do assistente, o arquivo local. PBit é adaptado para refletir a forma de seus dados.

1. Vá para a próxima página.

### <a name="step-3-configure-the-index"></a>Passo 3: configurar o índice

Nesta etapa do assistente, você configurará um índice para consultas de pesquisa de texto completo opcionais. O assistente criará uma amostra da fonte de dados para inferir campos e tipos de dados. Você só precisa selecionar os atributos para o comportamento desejado. Por exemplo, o atributo **recuperável** permitirá que o serviço de pesquisa retorne um valor de campo enquanto o **pesquisável** habilitará a pesquisa de texto completo no campo.

1. Para **nome do índice**, insira `hotel-reviews-idx`.
1. Para atributos, aceite as seleções padrão: **recuperável** e **pesquisável** para os novos campos que o pipeline está criando.

    O índice deve ser semelhante à imagem a seguir. Como a lista é longa, nem todos os campos são visíveis na imagem.

    ![Configurar um índice](media/knowledge-store-create-portal/hotel-reviews-idx.png "Configurar um índice")

1. Vá para a próxima página.

### <a name="step-4-configure-the-indexer"></a>Passo 4: configurar o indexador

Nesta etapa do assistente, você configurará um indexador que reunirá a fonte de dados, o conjunto de qualificações e o índice que você definiu nas etapas anteriores do assistente.

1. Para **nome**, insira `hotel-reviews-idxr`.
1. Para **agenda**, mantenha o padrão **uma vez**.
1. Clique em **Enviar** para executar o indexador. A extração de dados, a indexação e a aplicação de habilidades cognitivas acontecem nesta etapa.

## <a name="monitor-status"></a>Status do monitor

A indexação de habilidades cognitiva leva mais tempo para ser concluída do que a indexação típica baseada em texto. O assistente deve abrir a lista indexador na página Visão geral para que você possa acompanhar o progresso. Para navegação automática, vá para a página Visão geral e clique em **indexadores**.

No portal do Azure, você também pode monitorar o log de atividades de notificações para obter um link de status de **notificação do Azure pesquisa cognitiva** clicável. A execução pode levar vários minutos para ser concluída.

## <a name="next-steps"></a>Passos seguintes

Agora que você enriqueceu seus dados usando serviços cognitivas e projetou os resultados em uma loja de conhecimento, você pode usar Gerenciador de Armazenamento ou Power BI para explorar seu conjunto de dados aprimorado.

Você pode exibir o conteúdo no Gerenciador de Armazenamento ou levá-lo a um passo adiante com Power BI para obter informações sobre a visualização.

> [!div class="nextstepaction"]
> [Exibir com Gerenciador de Armazenamento](knowledge-store-view-storage-explorer.md)
> [conectar com Power bi](knowledge-store-connect-power-bi.md)

> [!Tip]
> Se você quiser repetir este exercício ou tentar um passo a passos de enriquecimento de ia diferente, exclua o indexador *Hotel-Reviews-idxr* . A exclusão do indexador redefine o contador de transação diária gratuito de volta para zero para o processamento de serviços cognitivas.
