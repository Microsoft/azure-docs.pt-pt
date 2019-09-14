---
title: Conectar-se a uma loja de conhecimento com Power BI Azure Search
description: Conecte um Azure Search repositório de conhecimento com Power BI para análise e exploração.
author: lisaleib
services: search
ms.service: search
ms.subservice: cognitive-search
ms.topic: tutorial
ms.date: 09/03/2019
ms.author: v-lilei
ms.openlocfilehash: fa6187dc270b18f513b5ee4046d0a6c085f9ae12
ms.sourcegitcommit: dd69b3cda2d722b7aecce5b9bd3eb9b7fbf9dc0a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/12/2019
ms.locfileid: "70963051"
---
# <a name="connect-a-knowledge-store-with-power-bi"></a>Conectar uma loja de conhecimento com Power BI

> [!Note]
> A loja de conhecimento está em versão prévia e não deve ser usada na produção. A [API REST do Azure Search versão 2019-05-06-Preview](search-api-preview.md) fornece esse recurso. Não há suporte para o SDK do .NET no momento.
>
Neste artigo, você aprenderá a se conectar e explorar uma loja de conhecimento usando Power Query no aplicativo Power BI Desktop. Para criar a amostra da loja de conhecimento usada neste passo a passos, consulte [criar uma loja de conhecimento em portal do Azure](knowledge-store-create-portal.md).

## <a name="prerequisites"></a>Pré-requisitos

+ Siga as etapas em [criar uma loja de conhecimento no portal do Azure](knowledge-store-create-portal.md) para criar o exemplo de armazenamento de conhecimento usado neste passo a passos. Você também precisará do nome da conta de armazenamento do Azure que usou para criar a loja de conhecimento, junto com sua chave de acesso do portal do Azure.

+ [Instalar Power BI Desktop](https://powerbi.microsoft.com/downloads/)

## <a name="connect-with-power-bi"></a>Ligar com o Power BI

1. Inicie o Power BI Desktop e clique em **obter dados**.

1. Na janela **obter dados** , selecione **Azure**e, em seguida, selecione **armazenamento de tabelas do Azure**.

1. Clique em **Ligar**.

1. Para **nome da conta ou URL**, insira no nome da conta de armazenamento do Azure (a URL completa será criada para você).

1. Se solicitado, insira a chave da conta de armazenamento.

1. Selecione as tabelas *hotelReviewsSsDocument*, *hotelReviewsSsKeyPhrases*e *hotelReviewsSsPages* . Essas tabelas são projeções de tabela do Azure dos dados de exemplo de revisões de Hotel e incluem os aprimoramentos de serviço cognitiva que foram selecionados quando a loja de conhecimento foi criada.

1. Clique em **carregar**.

1. Na faixa de lista superior, clique em **editar consultas** para abrir o **Editor de Power Query**.

   ![Abrir Power Query](media/knowledge-store-connect-power-bi/powerbi-edit-queries.png "Abrir Power Query")

1. Selecione *hotelReviewsSsDocument*e, em seguida, remova as colunas *PartitionKey*, *RowKey*e *timestamp* . 

   ![Editar tabelas](media/knowledge-store-connect-power-bi/powerbi-edit-table.png "Editar tabelas")

1. Clique no ícone com setas opostas no lado superior direito da tabela para expandir o *conteúdo*. Quando a lista de colunas for exibida, selecione todas as colunas e, em seguida, desmarque as colunas que começam com ' metadados '. Clique em **OK** para mostrar as colunas selecionadas.

   ![Editar tabelas](media/knowledge-store-connect-power-bi/powerbi-expand-content-table.png "Expandir conteúdo")

1. Altere o tipo de dados para as colunas a seguir clicando no ícone ABC-123 na parte superior esquerda da coluna.

   + Para *Content. latitude* e *Content. longitude*, selecione **número decimal**.
   + Para *Content. reviews_date* e *Content. Reviews_dateAdded*, selecione **data/hora**.

   ![Alterar tipos de dados](media/knowledge-store-connect-power-bi/powerbi-change-type.png "Alterar tipos de dados")

1. Selecione *hotelReviewsSsPages*e repita as etapas 9 e 10 para excluir as colunas e expandir o *conteúdo*.
1. Altere o tipo de dados para *Content. SentimentScore* para **número decimal**.
1. Selecione *hotelReviewsSsKeyPhrases* e repita as etapas 9 e 10 para excluir as colunas e expandir o *conteúdo*. Não há nenhuma modificação de tipo de dados para esta tabela.

1. Na barra de comandos, clique em **fechar e aplicar**.

1. Clique no bloco modelo no painel de navegação à esquerda e valide se Power BI mostra as relações entre todas as três tabelas.

   ![Validar relações](media/knowledge-store-connect-power-bi/powerbi-relationships.png "Validar relações")

1. Clique duas vezes em cada relação e verifique se a **direção do filtro cruzado** está definida como **ambas**.  Isso permite que os visuais sejam atualizados quando um filtro é aplicado.

<!-- ## Try with larger data sets

We purposely kept the data set small to avoid charges for a demo walkthrough. For a more realistic experience, you can create and then attach a billable Cognitive Services resource to enable a larger number of transactions against the sentiment analyzer, keyphrase extraction, and language detector skills.

Create new containers in Azure Blob storage and upload each CSV file to its own container. Specify one of these containers in the data source creation step in Import data wizard.

| Description | Link |
|-------------|------|
| Free tier   | [HotelReviews_Free.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Free.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D) |
| Small (500 Records) | [HotelReviews_Small.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Small.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D) |
| Medium (6000 Records)| [HotelReviews_Medium.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Medium.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D)
| Large (Full dataset 35000 Records) | [HotelReviews_Large.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Large.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D). Be aware that very large data sets are expensive to process. This one costs roughly $1000 U.S dollars.|

In the enrichment step of the wizard, attach a billable [Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) resource, created at the *S0* tier, in the same region as Azure Search to use larger data sets. 

  ![Create a Cognitive Services resource](media/knowledge-store-connect-power-bi/create-cognitive-service.png "Create a Cognitive Services resource") -->

## <a name="clean-up"></a>Limpeza

Quando você está trabalhando em sua própria assinatura, é uma boa ideia no final de um projeto identificar se você ainda precisa dos recursos que criou. Os recursos deixados em execução podem custar dinheiro. Você pode excluir os recursos individualmente ou excluir o grupo de recursos para excluir o conjunto inteiro de recursos.

Você pode encontrar e gerenciar recursos no portal, usando o link **todos os recursos** ou **grupos de recursos** no painel de navegação esquerdo.

Se você estiver usando um serviço gratuito, lembre-se de que você está limitado a três índices, indexadores e fontes de dados. Você pode excluir itens individuais no portal para permanecer abaixo do limite.

## <a name="next-steps"></a>Passos Seguintes

Para saber como explorar essa loja de conhecimento usando Gerenciador de Armazenamento, consulte o artigo a seguir.

> [!div class="nextstepaction"]
> [Exibir com Gerenciador de Armazenamento](knowledge-store-view-storage-explorer.md)

Para saber como criar uma loja de conhecimento usando as APIs REST e o postmaster, consulte o artigo a seguir.  

> [!div class="nextstepaction"]
> [Criar um repositório de conhecimento em repouso](knowledge-store-howto.md)
