---
title: Ligue-se a uma loja de conhecimentos (pré-visualização) com power BI
titleSuffix: Azure Cognitive Search
description: Ligue uma loja de conhecimentos de Pesquisa Cognitiva Azure (pré-visualização) com o Power BI para análise e exploração.
author: HeidiSteen
ms.author: heidist
manager: nitinme
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/26/2019
ms.openlocfilehash: 4fd71a7f322cb2672eb485f17e4de2619a7c2d2c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78270034"
---
# <a name="connect-a-knowledge-store-with-power-bi"></a>Ligue uma loja de conhecimentos com o Power BI

> [!IMPORTANT] 
> A loja de conhecimento está atualmente em pré-visualização pública. A funcionalidade de pré-visualização é fornecida sem um acordo de nível de serviço, e não é recomendada para cargas de trabalho de produção. Para mais informações, consulte [os Termos Suplementares de Utilização para pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)do Microsoft Azure . A [versão REST API 2019-05-06-Preview](search-api-preview.md) fornece funcionalidades de pré-visualização. Existe atualmente um suporte de portal limitado e nenhum suporte sdk .NET.

Neste artigo, aprenda a conectar-se e explore uma loja de conhecimentos usando power query na aplicação Power BI Desktop. Você pode começar mais rápido com modelos, ou construir um dashboard personalizado de raiz.

+ Siga os passos na [Create a knowledge store no portal Azure](knowledge-store-create-portal.md) ou Crie uma loja de conhecimentos de Pesquisa Cognitiva [Azure utilizando](knowledge-store-create-rest.md) o REST para criar a loja de conhecimento de amostras utilizada nesta passagem. Também necessitará do nome da conta De armazenamento Azure que usou para criar a loja de conhecimentos, juntamente com a sua chave de acesso a partir do portal Azure.

+ [Instalar o Power BI Desktop](https://powerbi.microsoft.com/downloads/)

## <a name="sample-power-bi-template---azure-portal-only"></a>Modelo de Sample Power BI - apenas portal Azure

Ao criar uma loja de [conhecimentos utilizando o portal Azure,](knowledge-store-create-portal.md)tem a opção de descarregar um modelo power [BI](https://github.com/Azure-Samples/cognitive-search-templates) na segunda página do assistente de dados **da Importação.** Este modelo dá-lhe várias visualizações, como o WordCloud e o Network Navigator, para conteúdos baseados em texto. 

Clique em **obter o modelo de Power BI** na página adicionar **habilidades cognitivas** para recuperar e baixar o modelo a partir da sua localização pública gitHub. O assistente modifica o modelo para acomodar a forma dos seus dados, conforme capturado nas projeções da loja de conhecimento especificadas no assistente. Por esta razão, o modelo que descarrega variará cada vez que executa o assistente, assumindo diferentes inputs de dados e seleções de habilidades.

![Modelo de BI de pesquisa cognitiva azure](media/knowledge-store-connect-power-bi/powerbi-sample-template-portal-only.png "Modelo de Bi de potência da amostra")

> [!NOTE]
> Embora o modelo seja descarregado enquanto o assistente está a meio do voo, terá de esperar até que a loja de conhecimento seja realmente criada no armazenamento da Mesa Azure antes de o poder utilizar.

## <a name="connect-with-power-bi"></a>Ligar com o Power BI

1. Inicie o Power BI Desktop e clique em **Obter dados**.

1. Na janela **Obter Dados,** selecione **Azure**, e, em seguida, selecione **Azure Table Storage**.

1. Clique em **Ligar**.

1. Para **nome de conta ou URL,** introduza o nome da sua conta de Armazenamento Azure (o URL completo será criado para si).

1. Se solicitado, introduza a chave da conta de armazenamento.

1. Selecione as tabelas que contêm os dados de avaliação do hotel criados pelos walkthroughs anteriores. 

   + Para o portal walkthrough, os nomes de mesa são *hotelReviewsSsDocument,* *hotelReviewsSSEntidades,* *hotelReviewsSKeyPhrases*e *hotelReviewsSSPages*. 
   
   + Para o rest walkthrough, os nomes de mesa são *hotelReviewsDocument,* *hotelReviewsPages,* *hotelReviewsKeyPhrases*e *hotelReviewsSentiment*.

1. Clique em **Carregar**.

1. Na fita superior, clique em **Editar Consultas** para abrir o **Power Query Editor**.

   ![Consulta de poder aberto](media/knowledge-store-connect-power-bi/powerbi-edit-queries.png "Consulta de poder aberto")

1. Selecione *hotelReviewsSsDocument*, e, em seguida, remova as colunas *PartitionKey,* *RowKey*e *Timestamp.* 
   ![Editar tabelas](media/knowledge-store-connect-power-bi/powerbi-edit-table.png "Editar tabelas")

1. Clique no ícone com setas opostas no lado superior direito da tabela para expandir o *Conteúdo*. Quando a lista de colunas aparecer, selecione todas as colunas e, em seguida, desmarque colunas que comecem com 'metadados'. Clique em **OK** para mostrar as colunas selecionadas.

   ![Editar tabelas](media/knowledge-store-connect-power-bi/powerbi-expand-content-table.png "Expandir conteúdo")

1. Altere o tipo de dados para as seguintes colunas clicando no ícone ABC-123 na parte superior esquerda da coluna.

   + Para *conteúdo.latitude* e *Conteúdo.longitude,* selecione **Número Decimal**.
   + Para *Content.reviews_date* e *Content.reviews_dateAdded,* selecione **Data/Hora**.

   ![Alterar tipos de dados](media/knowledge-store-connect-power-bi/powerbi-change-type.png "Alterar tipos de dados")

1. Selecione *hotelReviewsSsPages*, e, em seguida, repita os passos 9 e 10 para apagar as colunas e expandir o *Conteúdo*.
1. Altere o tipo de dados para *Conteúdo.SentimentScore* para **Número Decimal**.
1. Selecione *hotelReviewsSKeyPhrases* e repita os passos 9 e 10 para eliminar as colunas e expandir o *Conteúdo*. Não existem modificações do tipo de dados para esta tabela.

1. Na barra de comando, clique **em Fechar e Aplicar**.

1. Clique no azulejo Model no painel de navegação esquerdo e valide que o Power BI mostra relações entre as três tabelas.

   ![Validar relações](media/knowledge-store-connect-power-bi/powerbi-relationships.png "Validar relações")

1. Clique duas vezes em cada relação e **certifique-se** de que a direção do filtro transversal está definida para **ambos**.  Isto permite que os seus visuais se refresque quando um filtro é aplicado.

1. Clique no azulejo report no painel de navegação à esquerda para explorar dados através de visualizações. Para campos de texto, tabelas e cartões são visualizações úteis. Pode escolher campos entre cada uma das três mesas para preencher a mesa ou o cartão. 

<!-- ## Try with larger data sets

We purposely kept the data set small to avoid charges for a demo walkthrough. For a more realistic experience, you can create and then attach a billable Cognitive Services resource to enable a larger number of transactions against the sentiment analyzer, keyphrase extraction, and language detector skills.

Create new containers in Azure Blob storage and upload each CSV file to its own container. Specify one of these containers in the data source creation step in Import data wizard.

| Description | Link |
|-------------|------|
| Free tier   | [HotelReviews_Free.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Free.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D) |
| Small (500 Records) | [HotelReviews_Small.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Small.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D) |
| Medium (6000 Records)| [HotelReviews_Medium.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Medium.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D)
| Large (Full dataset 35000 Records) | [HotelReviews_Large.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Large.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D). Be aware that very large data sets are expensive to process. This one costs roughly $1000 U.S dollars.|

In the enrichment step of the wizard, attach a billable [Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) resource, created at the *S0* tier, in the same region as Azure Cognitive Search to use larger data sets. 

  ![Create a Cognitive Services resource](media/knowledge-store-connect-power-bi/create-cognitive-service.png "Create a Cognitive Services resource") -->

## <a name="clean-up"></a>Limpeza

Ao trabalhar na sua própria subscrição, recomendamos que verifique, depois de concluir um projeto, se irá precisar dos recursos que criou. Os recursos que deixar em execução podem custar-lhe dinheiro. Pode eliminar recursos individualmente ou eliminar o grupo de recursos para eliminar todo o conjunto de recursos.

Pode encontrar e gerir recursos no portal, utilizando a ligação **De Todos os recursos** ou **grupos de Recursos** no painel de navegação à esquerda.

Se estiver a utilizar um serviço gratuito, lembre-se de que está limitado a três índices, indexadores e fontes de dados. Pode eliminar itens individuais no portal para se manter abaixo do limite.

## <a name="next-steps"></a>Passos seguintes

Para aprender a explorar esta loja de conhecimento usando o Storage Explorer, consulte o seguinte artigo.

> [!div class="nextstepaction"]
> [Ver com o Explorador de Armazenamento](knowledge-store-view-storage-explorer.md)