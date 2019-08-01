---
title: Conectar-se à loja de conhecimento com Power BI Azure Search
description: Crie um repositório de conhecimento usando o assistente para importar dados no portal do Azure e, em seguida, conecte-se com Power BI para análise e exploração.
author: heidisteen
services: search
ms.service: search
ms.devlang: NA
ms.topic: tutorial
ms.date: 07/30/2019
ms.author: heidist
ms.openlocfilehash: 66a051c430be9f59569a5843c0138c7ddc15b87c
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68707158"
---
# <a name="create-an-azure-search-knowledge-store-and-connect-using-power-bi"></a>Criar um Azure Search repositório de conhecimento e conectar-se usando Power BI

> [!Note]
> A loja de conhecimento está em versão prévia e não deve ser usada na produção. A [API REST do Azure Search versão 2019-05-06-Preview](search-api-preview.md) fornece esse recurso. Não há suporte para o SDK do .NET no momento.
>

O repositório de conhecimento é um recurso do Azure Search que persiste a saída de um pipeline de enriquecimento de ia para análise subsequente ou outro processamento downstream. Um pipeline aprimorado de ia aceita arquivos de imagem ou arquivos de texto não estruturados de uma fonte de dados com suporte, envia-os para Azure Search indexação, aplica os aprimoramentos de ia dos serviços cognitivas (como análise de imagem e processamento de linguagem natural) e, em seguida, salva os resultados para uma loja de conhecimento no armazenamento do Azure. Na loja de conhecimento, você pode anexar ferramentas como Power BI ou Gerenciador de Armazenamento para explorar os resultados.

Neste artigo, crie uma loja de conhecimento no portal e, em seguida, conecte-se e explore usando Power Query em Power BI Desktop. 

Este passo a passos usa um conjunto de dados que consiste em análises e avaliações de clientes, pontuação de sentimentos dos serviços cognitivas e, em seguida, Power Query para consultar seus documentos. Os dados são provenientes de dados de resenhas do hotel no Kaggle.com. 

## <a name="prerequisites"></a>Pré-requisitos

+ [Baixar arquivo CSV de revisões do Hotel (HotelReviews_Free. csv)](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Free.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D). Este conjunto de dados contém registros de comentários do cliente sobre hotéis.

+ [Power BI Desktop](https://powerbi.microsoft.com/downloads/)

+ [Azure Search](search-create-service-portal.md). Você pode usar um serviço gratuito para esta explicação. 

+ [Armazenamento do Azure](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal). Verifique se a conta é "finalidade geral", ou seja, *StorageV2 (uso geral v2)* , que é o padrão ou *armazenamento (uso geral v1)* . Escolha a mesma região que Azure Search.
 
## <a name="prepare-data"></a>Preparar dados 

Carregue o arquivo. csv no armazenamento de BLOBs do Azure para que ele possa ser acessado por um indexador Azure Search.

1. [Entre no portal do Azure](https://portal.azure.com), navegue até sua conta de armazenamento do Azure, cliqueem BLOBs e, em seguida, clique em **+ contêiner**.

1. [Crie um contêiner](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) de BLOBs para conter dados de exemplo: 

   1. Nomeie o contêiner `hotel-reviews`. 
   
   1. Defina o nível de acesso público como qualquer um de seus valores válidos. Usamos o padrão.

1. Depois que o contêiner for criado, abra-o e selecione **carregar** na barra de comandos.

1. Navegue até a pasta que contém o **HotelReviews-Free. csv**, selecione o arquivo e clique em **carregar**.

   ![Carregar o arquivo. csv](media/knowledge-store-howto-powerbi/hotel-reviews-blob-container.png "Carregar o arquivo. csv")

1. Enquanto você estiver no armazenamento do Azure, obtenha a cadeia de conexão e o nome do contêiner.  Você precisará dessas duas cadeias de caracteres ao criar um objeto de fonte de dados:

   1. Na página Visão geral, clique em **chaves de acesso** e copie uma cadeia de *conexão*. Ele começa com `DefaultEndpointsProtocol=https;` e termina com. `EndpointSuffix=core.windows.net` O nome da conta e a chave estão entre. 

   1. O nome do contêiner deve `hotel-reviews` ser ou qualquer nome que você atribuiu. 

## <a name="create-and-run-ai-enrichments"></a>Criar e executar aprimoramentos de ia

Use o assistente para importar dados para criar a loja de conhecimento. Você importará o conjunto de dados, escolherá aprimoramentos, configurará uma loja de conhecimento e um índice e, em seguida, executará.

1. [Localize seu serviço de pesquisa](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) em portal do Azure.

1. Clique em **importar dados** na barra de comandos.

1. Crie o objeto de fonte de dados na primeira página do assistente.

   - Selecione **armazenamento de BLOBs do Azure**.

   - Dê um nome à fonte de dados, como *Hotel-Reviews-DS*.

   - Como os dados são. csv, selecione **texto delimitado** para o modo de análise, selecione a **primeira linha contém cabeçalho**e verifique se o caractere delimitador é uma vírgula.

   - A cadeia de conexão para sua conta de armazenamento do Azure pode ser obtida no portal, em **chaves de acesso**.

   - O nome do contêiner também pode ser obtido no portal na sua lista de BLOBs do armazenamento do Azure.

      ![Criar um objeto de fonte de dados](media/knowledge-store-howto-powerbi/hotel-reviews-ds.png "Criar um objeto de fonte de dados")

1. Adicione aprimoramentos e configure um repositório de conhecimento na segunda página do assistente.

   - Em **anexar serviços cognitivas**, você pode usar o recurso gratuito que permite até 20 transações por dia. O número de registros em HotelReviews-Free. csv é menor que 20.

   - Em **Adicionar aprimoramentos**, nomeie o nome do conhecimento *Hotel-Reviews-SS*, escolha o campo *reviews_text* , escolha um nível de granularidade de *páginas (blocos de 5000 caracteres)* e, em seguida, selecione essas três habilidades cognitivas: *Extraia frases-chave*, detecte a *linguagem*, detecte *sentimentos*.

      ![Criar um habilidades](media/knowledge-store-howto-powerbi/hotel-reviews-ss.png "Criar um habilidades")

   - Em **salvar aprimoramentos na loja de conhecimento**, forneça a cadeia de conexão para sua conta de armazenamento do Azure de uso geral. Uma loja de conhecimento é criada no armazenamento de tabelas do Azure quando você seleciona as opções de *projetos de tabela do Azure* nesta seção.

      ![Configurar o repositório de conhecimento](media/knowledge-store-howto-powerbi/hotel-reviews-ks.png "Configurar o repositório de conhecimento")
   
   Clique **em Avançar: Personalize o índice** de destino para continuar na próxima etapa.

1. Configure um índice para consultas de pesquisa de texto completo opcionais no Azure Search. Embora o foco deste passo a passo seja Power BI conectado ao armazenamento de tabelas do Azure, o assistente de **importação de dados** também pode criar um índice usado para pesquisa de texto completo no Azure Search. 

   O assistente faz amostras da fonte de dados para inferir campos e tipos de dados, portanto, tudo o que você precisa fazer é selecionar os atributos necessários para obter os comportamentos desejados. Por exemplo, *recuperável* significa que o conteúdo do campo pode ser recuperado do serviço, enquanto *pesquisável* permite a pesquisa de texto completo em campos selecionados.

   - Dê um nome ao índice, como *Hotel-Reviews-IDX*.
   - Defina todos os camposcomo recuperáveis.
   - Defina *cidade*, *nome*, *reviews_text*, *idioma*, prefrases como **pesquisáveis**.
   - Defina *sentimentos*, *idioma*, prefrases como **filtrável** e **facetable**. 
   
    O índice deve ser semelhante à imagem a seguir.

     ![Configurar um índice](media/knowledge-store-howto-powerbi/hotel-reviews-idx.png "Configurar um índice")

   Clique **em Avançar: Crie um indexador** para continuar na próxima etapa.

1. Configure um indexador dando a ele uma cadência de nome e execução. Para esta explicação, use *Hotel-Reviews-idxr* como o nome do indexador e use a agenda de **uma vez** padrão para executar o indexador imediatamente.

   A execução do indexador coloca todas as configurações anteriores em movimento. A extração, o processamento e a ingestão de tudo acontece aqui nesta etapa.

1. Monitore a indexação na fila de notificações no Portal. A execução leva vários minutos para ser concluída.

## <a name="connect-with-power-bi"></a>Ligar com o Power BI

1. Inicie o Power BI Desktop e selecione **obter dados**.

1. Em obter dados, selecione **Azure** e, em seguida, **armazenamento de tabelas do Azure**. Clique em **Ligar**.

1. Em nome da conta ou URL, Cole o nome da conta de armazenamento do Azure (a URL completa é resolvida para você).

1. Insira a chave da conta.

1. Selecione documento, keyfrases e páginas. Essas são as tabelas criadas pelo assistente de importação de dados quando você seleciona os itens de mesmo nome na configuração da loja de conhecimento. Clique em **carregar**.

1. Abra Power Query clicando no comando **editar consultas** .

   ![Abrir Power Query](media/knowledge-store-howto-powerbi/powerbi-edit-queries.png "Abrir Power Query")

1. Para documentos:

   - Remova as colunas PartitionKey, RowKey e timestamp criadas pelo armazenamento de tabelas do Azure. A loja de conhecimento fornece relações usadas nesta análise.

   - Expanda a coluna conteúdo.

     ![Editar tabelas](media/knowledge-store-howto-powerbi/powerbi-edit-table.png "Editar tabelas")

1. Selecione todos os campos e, em seguida, desmarque-os começando com "metadados".

1. Corrija o tipo de dados para as seguintes colunas usando o ícone ABC-123 em cada coluna:

   - As colunas de data devem ser **DateTime**
   - A *latitude* deve ser um **número decimal**
   - A *longitude* deve ser um **número decimal**

1. Repita as etapas anteriores para keyfrases, removendo PartitionKey e outras colunas, expandindo colunas de conteúdo, definindo *SentimentScore* como **número decimal**.

1. Repita novamente para páginas, removendo PartitionKey e outras colunas, expandindo colunas de conteúdo. Não há nenhuma modificação de tipo de dados para esta tabela.

1. Clique em **fechar e aplicar** na extrema esquerda da barra de comandos Power Query.

1. Valide se Power BI reconhece as relações que o repositório de conhecimento criou em seus dados. Clique no bloco relações no painel de navegação à esquerda. Todas as três tabelas devem estar relacionadas. Edite as relações e verifique se a "direção do filtro cruzado" está definida como ambas, isso garantirá que todos os visuais sejam atualizados quando um filtro for aplicado.

   ![Validar relações](media/knowledge-store-howto-powerbi/powerbi-relationships.png "Validar relações")

## <a name="try-with-larger-data-sets"></a>Experimente com conjuntos de dados maiores

Nós mantivemos o conjunto de dados pequeno para evitar encargos por uma demonstração. Para uma experiência mais realista, você pode criar e anexar um recurso de serviços de cognitiva Faturável para habilitar um número maior de transações em relação ao analisador de sentimentos, à extração de keyphrase e às habilidades do detector de idiomas.

Crie novos contêineres no armazenamento de BLOBs do Azure e carregue cada arquivo CSV em seu próprio contêiner. Especifique um desses contêineres na etapa de criação da fonte de dados no assistente de importação de dados.

| Descrição | Associar |
|-------------|------|
| Escalão gratuito   | [HotelReviews_Free. csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Free.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D) |
| Pequeno (500 registros) | [HotelReviews_Small. csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Small.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D) |
| Médio (6000 registros)| [HotelReviews_Medium. csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Medium.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D)
| Grande (registros completos de DataSet 35000) | [HotelReviews_Large. csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Large.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D). Lembre-se de que grandes conjuntos de dados são caros de processar. Esse um custa aproximadamente $1000 U. S dólares.|

Na etapa de enriquecimento do assistente, anexe um recurso de serviços de [cognitiva](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) Faturável, criado na camada *S0* , na mesma região que Azure Search para usar conjuntos de dados maiores. 

  ![Criar um recurso de serviços cognitivas](media/knowledge-store-howto-powerbi/create-cognitive-service.png "Criar um recurso de serviços cognitivas")

## <a name="next-steps"></a>Passos Seguintes

Se você quiser repetir este exercício ou fazer outro passo a passos de reutilização de AI, exclua o indexador *Hotel-Reviews-IDX* que você acabou de criar. A exclusão do indexador redefine o contador de transação diária gratuito de volta para zero. 

Para obter instruções passo a passo demonstrando a loja de conhecimento, continue com o próximo artigo que mostra como criar uma loja de conhecimento usando as APIs REST e o postmaster.

> [!div class="nextstepaction"]
> [Introdução à loja de conhecimento](knowledge-store-howto.md)
