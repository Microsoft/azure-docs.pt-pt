---
title: Conecte-se a uma loja de conhecimento com o Power BI
titleSuffix: Azure Cognitive Search
description: Ligue uma loja de conhecimentos Azure Cognitive Search com o Power BI para análise e exploração.
author: HeidiSteen
ms.author: heidist
manager: nitinme
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: 91e75b60f5324288c9f1adac59e31b9c1a1b0e9e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "89289176"
---
# <a name="connect-a-knowledge-store-with-power-bi"></a>Ligue uma loja de conhecimento com o Power BI

Neste artigo, aprenda a conectar-se e explore uma loja de conhecimento usando a Power Query na aplicação Power BI Desktop. Você pode começar mais rápido com modelos, ou construir um dashboard personalizado a partir do zero. Este breve vídeo abaixo demonstra como pode enriquecer a sua experiência com os seus dados utilizando a Azure Cognitive Search em combinação com o Power BI.


> [!VIDEO https://www.youtube.com/embed/XWzLBP8iWqg?version=3&start=593&end=663]



+ Siga os passos na [Criar uma loja de conhecimento no portal Azure](knowledge-store-create-portal.md) ou criar uma loja de [conhecimentos Azure Cognitive Search utilizando](knowledge-store-create-rest.md) o REST para criar a loja de conhecimentos de amostras utilizada nesta passagem. Também necessitará do nome da conta Azure Storage que utilizou para criar a loja de conhecimentos, juntamente com a sua chave de acesso a partir do portal Azure.

+ [Install Power BI Desktop (Instalar o Power BI Desktop)](https://powerbi.microsoft.com/downloads/)

## <a name="sample-power-bi-template---azure-portal-only"></a>Modelo de BI de energia de amostra - portal Azure apenas

Ao criar uma loja de [conhecimentos utilizando o portal Azure,](knowledge-store-create-portal.md)tem a opção de descarregar um [modelo de Power BI](https://github.com/Azure-Samples/cognitive-search-templates) na segunda página do assistente de **dados Import.** Este modelo dá-lhe várias visualizações, como o WordCloud e o Network Navigator, para conteúdos baseados em texto. 

Clique em **Obter O Modelo DE BI de Potência** na página Adicionar **habilidades cognitivas** para recuperar e descarregar o modelo a partir da sua localização pública do GitHub. O assistente modifica o modelo para acomodar a forma dos seus dados, tal como capturado nas projeções da loja de conhecimentos especificadas no assistente. Por esta razão, o modelo que descarrega variará cada vez que executar o assistente, assumindo diferentes entradas de dados e seleções de habilidades.

![Modelo de BI de poder de pesquisa cognitiva Azure](media/knowledge-store-connect-power-bi/powerbi-sample-template-portal-only.png "Modelo de BI de energia da amostra")

> [!NOTE]
> Embora o modelo seja descarregado enquanto o assistente está a meio do voo, terás de esperar até que a loja de conhecimentos seja realmente criada no armazenamento da Tabela Azure antes de o poderes utilizar.

## <a name="connect-with-power-bi"></a>Ligar com o Power BI

1. Iniciar Power BI Desktop e clicar **Em Obter dados**.

1. Na janela **Obter Dados,** selecione **Azure** e, em seguida, selecione **Azure Table Storage**.

1. Clique em **Ligar**.

1. Para **Nome de Conta ou URL,** introduza o nome da sua conta de Armazenamento Azure (o URL completo será criado para si).

1. Se solicitado, introduza a chave da conta de armazenamento.

1. Selecione as tabelas que contêm os dados de avaliação do hotel criados pelas revisões anteriores. 

   + Para o portal walkthrough, os nomes das tabelas são *hotelReviewssDocument,* *hotelReviewsSsEntities,* *hotelReviewsSsKeyPhrases,* e *hotelReviewsSSPages.* 
   
   + Para o REST walkthrough, os nomes de mesa são *hotelReviewsDocument,* *hotelReviewsPages,* *hotelReviewsKeyPhrases,* e *hotelReviewsSentiment*.

1. Clique **em Carregar**.

1. Na fita superior, clique em **Editar Consultas** para abrir o **Power Query Editor**.

   ![Consulta de energia aberta](media/knowledge-store-connect-power-bi/powerbi-edit-queries.png "Consulta de energia aberta")

1. Selecione *hotelReviewsSsDocument* e, em seguida, remova as colunas *PartitionKey,* *RowKey* e *Timestamp.* 
   ![Editar tabelas](media/knowledge-store-connect-power-bi/powerbi-edit-table.png "Editar tabelas")

1. Clique no ícone com setas opostas no lado superior direito da tabela para expandir o *Conteúdo*. Quando a lista de colunas aparecer, selecione todas as colunas e, em seguida, desmarcar colunas que começam com 'metadados'. Clique **em OK** para mostrar as colunas selecionadas.

   ![Expandir conteúdo](media/knowledge-store-connect-power-bi/powerbi-expand-content-table.png "Expandir conteúdo")

1. Altere o tipo de dados para as seguintes colunas clicando no ícone ABC-123 na parte superior esquerda da coluna.

   + Para *conteúdo.latitude* e *Conteúdo.longitude,* selecione **Decimal Number**.
   + Para *Content.reviews_date* e *Content.reviews_dateAdded,* selecione **Data/Hora**.

   ![Alterar tipos de dados](media/knowledge-store-connect-power-bi/powerbi-change-type.png "Alterar tipos de dados")

1. Selecione *hotelReviewsSsPages* e, em seguida, repita os passos 9 e 10 para eliminar as colunas e expandir o *Conteúdo*.
1. Alterar o tipo de dados *para Conteúdo.SentimentScore* para **Número Decimal**.
1. Selecione *hotelReviewsSsKeyPhrases* e repita os passos 9 e 10 para eliminar as colunas e expandir o *Conteúdo*. Não existem modificações do tipo de dados para esta tabela.

1. Na barra de comando, clique **em Fechar e Aplicar**.

1. Clique no azulejo do Modelo no painel de navegação esquerdo e valide que o Power BI mostra relações entre as três tabelas.

   ![Validar relações](media/knowledge-store-connect-power-bi/powerbi-relationships.png "Validar relações")

1. Clique duas vezes em cada relação e **certifique-se de** que a direção do filtro cruzado está definida para **Ambos**.  Isto permite que os seus visuais se refresquem quando um filtro é aplicado.

1. Clique no azulejo do Relatório no painel de navegação esquerdo para explorar dados através de visualizações. Para campos de texto, as tabelas e cartões são visualizações úteis. Pode escolher campos de cada uma das três mesas para preencher a mesa ou o cartão. 

<!-- ## Try with larger data sets

We purposely kept the data set small to avoid charges for a demo walkthrough. For a more realistic experience, you can create and then attach a billable Cognitive Services resource to enable a larger number of transactions against the sentiment analyzer, keyphrase extraction, and language detector skills.

Create new containers in Azure Blob storage and upload each CSV file to its own container. Specify one of these containers in the data source creation step in Import data wizard.

| Description | Link |
|-------------|------|
| Free tier   | [HotelReviews_Free.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Free.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D) |
| Small (500 Records) | [HotelReviews_Small.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Small.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D) |
| Medium (6000 Records)| [HotelReviews_Medium.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Medium.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D)
| Large (Full dataset 35000 Records) | [HotelReviews_Large.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Large.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D). Be aware that very large data sets are expensive to process. This one costs roughly $1000 U.S dollars.|

In the enrichment step of the wizard, attach a billable [Cognitive Services](../cognitive-services/cognitive-services-apis-create-account.md) resource, created at the *S0* tier, in the same region as Azure Cognitive Search to use larger data sets. 

  ![Create a Cognitive Services resource](media/knowledge-store-connect-power-bi/create-cognitive-service.png "Create a Cognitive Services resource") -->

## <a name="clean-up"></a>Limpeza

Ao trabalhar na sua própria subscrição, depois de concluir um projeto, recomendamos que verifique se irá precisar dos recursos que criou. Os recursos que deixar em execução podem custar-lhe dinheiro. Pode eliminar recursos individualmente ou eliminar o grupo de recursos para eliminar todo o conjunto de recursos.

Pode encontrar e gerir recursos no portal, utilizando a ligação **de todos os recursos** ou **grupos** de recursos no painel de navegação à esquerda.

Se estiver a utilizar um serviço gratuito, lembre-se que está limitado a três índices, indexadores e fontes de dados. Pode eliminar itens individuais no portal para ficar abaixo do limite.

## <a name="next-steps"></a>Passos seguintes

Para aprender a explorar esta loja de conhecimentos utilizando o Storage Explorer, consulte o seguinte artigo.

> [!div class="nextstepaction"]
> [Ver com o Explorador de Armazenamento](knowledge-store-view-storage-explorer.md)