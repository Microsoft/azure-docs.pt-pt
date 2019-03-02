---
title: Blobs JSON de indexação do indexador de Blobs do Azure para pesquisa em texto completo - Azure Search
description: Pesquise blobs JSON do Azure para o conteúdo de texto com o indexador Blob do Azure Search. Indexadores automatizam ingestão de dados para origens de dados selecionadas, como o armazenamento de Blobs do Azure.
ms.date: 02/28/2019
author: HeidiSteen
manager: cgronlun
ms.author: heidist
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: f287648758d2883226132c0f45418dacaaf27652
ms.sourcegitcommit: c712cb5c80bed4b5801be214788770b66bf7a009
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/01/2019
ms.locfileid: "57216327"
---
# <a name="indexing-json-blobs-with-azure-search-blob-indexer"></a>Indexar blobs JSON com o indexador Blob do Azure Search
Este artigo mostra-lhe como configurar um indexador de Blobs do Azure Search para extrair conteúdo estruturado de documentos JSON no armazenamento de Blobs do Azure e permitir pesquisável no Azure Search. Este fluxo de trabalho cria um índice da Azure Search e carrega-os com existente textos extraídos dos JSON blobs. 

Pode utilizar o [portal](#json-indexer-portal), [REST APIs](#json-indexer-rest), ou [SDK de .NET](#json-indexer-dotnet) para indexar conteúdo JSON. Comum a todas as abordagens é que os documentos JSON estão localizados num contentor de BLOBs numa conta de armazenamento do Azure. Para obter orientações sobre como enviar documentos JSON a partir de outras plataformas não pertencente ao Azure, consulte [importação de dados no Azure Search](search-what-is-data-import.md).

Blobs JSON no armazenamento de Blobs do Azure são, normalmente, um único documento JSON ou uma coleção de entidades JSON. Para coleções de JSON, o blob pode ter uma **matriz** dos elementos JSON bem formados. BLOBs também poderiam ser compostos por várias entidades individuais de JSON separadas por uma nova linha. O indexador blob do Azure Search pode analisar esse tipo de construção, dependendo de como definir o **parsingMode** parâmetro na solicitação.

> [!IMPORTANT]
> `json` e `jsonArray` modos de análise estão disponíveis em geral, mas `jsonLines` modo de análise está em pré-visualização pública e não deve ser usado em ambientes de produção. Para obter mais informações, consulte [REST api-version = 2017-11-11-pré-visualização](search-api-2017-11-11-preview.md). 

> [!NOTE]
> Siga as recomendações de configuração do indexador nas [um-para-muitos indexação](search-howto-index-one-to-many-blobs.md) para vários documentos de pesquisa de um blob do Azure de saída.

<a name="json-indexer-portal"></a>

## <a name="use-the-portal"></a>Utilizar o portal

O método mais fácil para indexar documentos JSON é usar um assistente no [portal do Azure](https://portal.azure.com/). Ao analisar metadados no contentor de Blobs do Azure, o [ **importar dados** ](search-import-data-portal.md) assistente pode criar um índice predefinido, mapear campos de origem para campos de índice de destino e carregar o índice numa única operação. Dependendo do tamanho e complexidade da origem de dados, poderia ter um índice de pesquisa de texto completo operacional em minutos.

Recomendamos que utilize a mesma subscrição do Azure para o Azure Search e o armazenamento do Azure, de preferência na mesma região.

### <a name="1---prepare-source-data"></a>1 - preparar os dados de origem

Deve ter uma conta de armazenamento do Azure, com armazenamento de BLOBs e um contentor de documentos JSON. Se não estiver familiarizado com qualquer uma destas tarefas, reveja o pré-requisito "Configurar dados de exemplo de serviço e o carregamento de Blobs do Azure" na [cognitivo pesquisa-quickstart](cognitive-search-quickstart-blob.md#set-up-azure-blob-service-and-load-sample-data).

> [!Important]
> No contentor, certifique-se que **nível de acesso público** está definido como "Contentor (acesso de leitura anónimo para contentores e blobs)".

### <a name="2---start-import-data-wizard"></a>2 - iniciar o Assistente de importação de dados

Pode [iniciar o assistente](search-import-data-portal.md) na barra de comandos na página do serviço de pesquisa do Azure ou ao clicar **adicionar Azure Search** no **serviço Blob** secção da sua conta de armazenamento Painel de navegação esquerdo.

   ![Importar o comando de dados no portal](./media/search-import-data-portal/import-data-cmd2.png "iniciar o Assistente de importação de dados")

### <a name="3---set-the-data-source"></a>3 - definir a origem de dados

Na **origem de dados** página, a origem tem de ser **armazenamento de Blobs do Azure**, com as seguintes especificações:

+ **Dados a extrair** deve ser *conteúdo e metadados*. Escolher esta opção permite que o Assistente para inferir um esquema de índice e mapear os campos para importação.
   
+ **Modo de análise** deve ser definido como *JSON*, *matriz JSON* ou *linhas JSON*. 

  *JSON* articule toda cada blob como um documento único de pesquisa, aparecer como um item independente nos resultados da pesquisa. 

  *Matriz JSON* é para blobs que contêm dados JSON bem formados, o JSON bem formado corresponde a uma matriz de objetos ou tem uma propriedade que é uma matriz de objetos e pretende que cada elemento a ser articulado como autónomo, independente de pesquisar no documento. Se os blobs são complexos e não escolher *matriz JSON* todo o blob é ingerido como um único documento.

  *Linhas JSON* é para blobs é composto por várias entidades JSON separadas por uma nova linha, onde pretende que cada entidade para ser articulada como um documento de pesquisa independente autónomo. Se os blobs são complexos e não escolher *linhas JSON* modo, em seguida, todo o blob de análise é ingerido como um único documento.
   
+ **Contentor de armazenamento** tem de especificar a conta de armazenamento e contentor ou uma cadeia de ligação que é resolvido para o contentor. Pode obter cadeias de ligação na página de portal do serviço Blob.

   ![Definição de origem de dados de blob](media/search-howto-index-json/import-wizard-json-data-source.png)

### <a name="4---skip-the-add-cognitive-search-page-in-the-wizard"></a>4 - ignorar a página de "Adicionar a pesquisa cognitiva" no Assistente

Adicionar capacidades cognitivas não é necessário para importação de documentos JSON. A menos que tenha uma necessidade específica de [incluem APIs serviços cognitivos e transformações](cognitive-search-concept-intro.md) para o seu pipeline de indexação, deve ignorar este passo.

Para ignorar a etapa, primeiro, ir até a página seguinte.

   ![Botão seguinte da página de pesquisa cognitiva](media/search-get-started-portal/next-button-add-cog-search.png)

Dessa página pode avançar diretamente para personalização do índice.

   ![Ignorar o passo de capacidades cognitivas](media/search-get-started-portal/skip-cog-skill-step.png)

### <a name="5---set-index-attributes"></a>5 - conjunto de atributos de índice

Na **índice** página, deve ver uma lista de campos com um tipo de dados e uma série de caixas de seleção para a definição de atributos de índice. O assistente pode gerar um índice predefinido com base nos metadados e a origem de dados de amostragem. 

As predefinições, muitas vezes, produzem uma solução funcional: selecionar um campo (como cadeia de caracteres de conversão) para servir como a chave ou ID para identificar exclusivamente cada documento, bem como campos que são bons candidatos para a pesquisa em texto completo e recuperáveis no conjunto de resultados de documentos. Para blobs, a `content` campo é o melhor candidato para conteúdo pesquisável.

Pode aceitar as predefinições ou, reveja a descrição da [atributos de índice](https://docs.microsoft.com/rest/api/searchservice/create-index#bkmk_indexAttrib) e [analisadores de idiomas](https://docs.microsoft.com/rest/api/searchservice/language-support) para substituir ou complementar os valores iniciais. 

Dedique uns momentos para rever as suas seleções. Depois de executar o assistente, estruturas de dados físico são criadas e não será possível editar estes campos sem remover e recriar todos os objetos.

   ![Definição de índice de blob](media/search-howto-index-json/import-wizard-json-index.png)

### <a name="6---create-indexer"></a>6 - criar indexador

Total especificado, o assistente cria 3 objetos distintos no seu serviço de pesquisa. Um objeto de origem de dados e o objeto de índice são guardadas como recursos nomeados do seu serviço Azure Search. A última etapa cria um objeto de indexador. O indexador de nomenclatura permite que ele existe como um recurso de autónomo, o que pode agendar e gerir de forma independente o objeto de origem dados e índice, criado na mesma sequência em Assistente.

Se não estiver familiarizado com indexadores, um *indexador* é um recurso no Azure Search, que pesquisa uma origem de dados externos para conteúdo pesquisável. A saída do **importar dados** assistente é um indexador que rastreia a sua origem de dados JSON, extrai o conteúdo pesquisável e importa-lo para um índice na Azure Search.

   ![Definição de indexador de BLOBs](media/search-howto-index-json/import-wizard-json-indexer.png)

Clique em **OK** para executar o assistente e criar todos os objetos. Indexação começa imediatamente.

Pode monitorizar a importação de dados nas páginas de portais. Notificações de progresso indicam o estado de indexação e o número de documentos são carregados. 

Quando a indexação é concluída, pode utilizar [Explorador de pesquisa](search-explorer.md) para consultar o índice.

> [!NOTE]
> Se não vir os dados esperados, poderá ter de definir mais atributos em mais campos. Elimine o índice e indexador que acabou de criar e siga os passos do assistente mais uma vez, modificar as suas seleções para atributos de índice no passo 5. 

<a name="json-indexer-rest"></a>

## <a name="use-rest-apis"></a>Utilizar APIs REST

Pode utilizar a API REST para indexar JSON blobs, segue um fluxo de trabalho de três partes comuns a todos os indexadores na pesquisa do Azure: criar uma origem de dados, criar um índice, criar um indexador. Extração de dados do armazenamento de BLOBs ocorre ao submeter o pedido de criar indexador. Após a conclusão deste pedido, terá um índice queryable. Para ver os pedidos de exemplo que criar todos os três objetos, veja [exemplo de REST](#rest-example) no final desta secção.

Para JSON baseadas em código indexação, utilize [Postman](search-fiddler.md) e a API de REST para criar esses objetos:

+ [index](https://docs.microsoft.com/rest/api/searchservice/create-index)
+ [Origem de dados](https://docs.microsoft.com/rest/api/searchservice/create-data-source)
+ [indexer](https://docs.microsoft.com/rest/api/searchservice/create-indexer)

Em contraste com o Assistente do portal, uma abordagem de código requer que tenha um índice no local, pronto para aceitar os documentos JSON ao enviar o **criar indexador** pedido.

Blobs JSON no armazenamento de Blobs do Azure são, normalmente, um único documento JSON ou um JSON "matriz". O indexador blob do Azure Search pode analisar a construção, dependendo de como definir o **parsingMode** parâmetro na solicitação.

| Documento JSON | parsingMode | Descrição | Disponibilidade |
|--------------|-------------|--------------|--------------|
| Um por blob | `json` | Analisa JSON blobs como um único segmento de texto. Cada blob JSON torna-se um único documento de Azure Search. | Disponível em geral nos dois [REST](https://docs.microsoft.com/rest/api/searchservice/indexer-operations) API e [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer) SDK. |
| Vários por blob | `jsonArray` | Analisa uma matriz JSON no blob, onde cada elemento da matriz se torna um documento separado do Azure Search.  | Disponível em pré-visualização em ambos [REST](https://docs.microsoft.com/rest/api/searchservice/indexer-operations) API e [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer) SDK. |
| Vários por blob | `jsonLines` | Analisa um blob que contém várias entidades JSON (uma "matriz") separadas por uma nova linha, onde cada entidade se torna um documento separado do Azure Search. | Disponível em pré-visualização em ambos [REST](https://docs.microsoft.com/rest/api/searchservice/indexer-operations) API e [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer) SDK. |

### <a name="1---assemble-inputs-for-the-request"></a>1 - montar entradas para o pedido

Para cada pedido, tem de fornecer o nome do serviço e a chave de administrador para o Azure Search (no cabeçalho da mensagem) e o nome da conta de armazenamento e a chave para o armazenamento de Blobs. Pode usar [Postman](search-fiddler.md) para enviar solicitações HTTP para o Azure Search.

Copie os seguintes quatro valores no bloco de notas, para que pode colá-los numa solicitação:

+ Nome do serviço de pesquisa do Azure
+ Chave de administração de pesquisa do Azure
+ Nome da conta de armazenamento do Azure
+ Chave de conta de armazenamento do Azure

Pode encontrar estes valores no portal do:

1. As páginas do portal do Azure Search, copie o URL do serviço de pesquisa da página de descrição geral.

2. No painel de navegação esquerdo, clique em **chaves** e, em seguida, copie qualquer um da chave primária ou secundária (ambos são equivalentes).

3. Mude para as páginas de portal para a sua conta de armazenamento. No painel de navegação esquerdo, sob **configurações**, clique em **chaves de acesso**. Esta página fornece o nome da conta e a chave. Copie o nome da conta de armazenamento e uma das chaves para o bloco de notas.

### <a name="2---create-a-data-source"></a>2 - criar uma origem de dados

Este passo fornece as informações de ligação de origem de dados o indexador. A origem de dados é um objeto nomeado no Azure Search que persiste as informações de ligação. Os dados da origem de tipo, `azureblob`, determina quais comportamentos de extração de dados são invocados o indexador. 

Substitua os valores válidos para o nome do serviço, a chave de administrador, a conta de armazenamento e os marcadores de posição de chave de conta.

    POST https://[service name].search.windows.net/datasources?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key for Azure Search]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "optional, my-folder" }
    }   

### <a name="3---create-a-target-search-index"></a>3 - criar um índice de pesquisa de destino 

Indexadores são associados um esquema de índice. Se estiver a utilizar a API (em vez do portal), prepare um índice com antecedência para que pode especificá-lo sobre a operação de indexador.

O índice armazena conteúdo pesquisável no Azure Search. Para criar um índice, fornece um esquema que especifica os campos num documento, atributos e outras construções que moldar a experiência de pesquisa. Se criar um índice com os mesmos nomes de campos e tipos de dados como a origem, o indexador corresponderá aos campos de origem e de destino, poupando o trabalho de ter de mapear explicitamente os campos.

A exemplo a seguir mostra um [Create Index](https://docs.microsoft.com/rest/api/searchservice/create-index) pedido. O índice terão um pesquisável `content` campo para armazenar os textos extraídos dos blobs:   

    POST https://[service name].search.windows.net/indexes?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key for Azure Search]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
          ]
    }


### <a name="4---configure-and-run-the-indexer"></a>4 - configurar e executar o indexador

Como com um índice e um dados de origem e indexador está também com um nome de objeto que cria e reutilizar num serviço Azure Search. Um pedido totalmente especificado para criar um indexador será semelhante ao seguinte:

    POST https://[service name].search.windows.net/indexers?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key for Azure Search]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "json" } }
    }

É a configuração do indexador no corpo do pedido. Exige uma origem de dados e um índice de destino vazia que já existe no Azure Search. 

Agenda e os parâmetros são opcionais. Se omiti-los, o indexador é executado imediatamente, usando `json` como o modo de análise.

Este indexador específico não inclui [mapeamentos de campo](#field-mappings). Dentro da definição de indexador, pode omitir **mapeamentos de campo** se as propriedades do documento JSON de origem correspondem os campos do seu índice de pesquisa de destino. 

### <a name="parsing-modes"></a>Modos de análise

Até agora, as definições para a origem de dados e índice foram parsingMode independente. No entanto, o passo para a configuração do indexador, o caminho diverges dependendo de como pretende que o blob JSON conteúdo até serem examinados e estruturadas no índice da Azure Search. As opções são `json` ou `jsonArray`:

+ Definir **parsingMode** para `json` para cada blob como um único documento de índice.

+ Definir **parsingMode** para `jsonArray` se os blobs consistem em matrizes JSON, e precisar de cada elemento da matriz para se tornar um documento separado no Azure Search. 

+ Definir **parsingMode** para `jsonLines` se os blobs são compostas por várias entidades JSON, que são separadas por uma nova linha, e terá de cada entidade para se tornar um documento separado no Azure Search.

Pode pensar num documento como um único item nos resultados da pesquisa. Se pretender que cada elemento da matriz ser apresentado nos resultados da pesquisa como um item independente, em seguida, utilize o `jsonArray` ou `jsonLines` opção conforme apropriado.

Dentro da definição de indexador, opcionalmente, pode utilizar [mapeamentos de campo](search-indexer-field-mappings.md) escolher quais propriedades do documento JSON de origem são usadas para preencher o índice de pesquisa de destino. Para `jsonArray` análise modo, se a matriz existe como uma propriedade de nível inferior, pode definir uma raiz do documento que indica em que a matriz é colocada dentro do blob.

> [!IMPORTANT]
> Quando utiliza `json`, `jsonArray` ou `jsonLines` modo de análise, o Azure Search assume que todos os blobs na sua origem de dados contenham JSON. Se tiver de suportar uma mistura de blobs JSON e não-JSON na mesma origem de dados, fale na [nosso site do UserVoice](https://feedback.azure.com/forums/263029-azure-search).


### <a name="how-to-parse-single-json-blobs"></a>Como analisar únicos de JSON de blobs

Por predefinição, [indexador de Blobs do Azure Search](search-howto-indexing-azure-blob-storage.md) analisa JSON blobs como um único segmento de texto. Muitas vezes, deseja preservar a estrutura dos seus documentos JSON. Por exemplo, suponha que tem o seguinte documento JSON no armazenamento de Blobs do Azure:

    {
        "article" : {
            "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13",
            "tags" : [ "search", "storage", "howto" ]    
        }
    }

O indexador blob analisa o documento JSON num único documento de Azure Search. O indexador carrega um índice por "text", "datePublished" e "etiquetas" da origem em relação a campos de índice de destino de forma idêntica com nome e com tipos correspondentes.

Como observado, os mapeamentos de campo não são necessários. Tendo em conta um índice com "text", "datePublished e"etiquetas"campos, o blob indexador pode inferir o mapeamento correto sem um campo de mapeamento presentes no pedido.

### <a name="how-to-parse-json-arrays-in-a-well-formed-json-document"></a>Como analisar matrizes JSON num documento JSON bem formado

Em alternativa, pode optar por para a funcionalidade de matriz JSON. Esta funcionalidade é útil quando os blobs de conter uma *matriz de objetos JSON*, e pretender que cada elemento para se tornar um documento separado do Azure Search. Por exemplo, tendo em conta o blob JSON seguinte, pode preencher o índice da Azure Search com três documentos separados, cada um com campos "id" e "text".  

    [
        { "id" : "1", "text" : "example 1" },
        { "id" : "2", "text" : "example 2" },
        { "id" : "3", "text" : "example 3" }
    ]

Para uma matriz JSON, a definição de indexador deve ter um aspeto semelhante ao seguinte exemplo. Tenha em atenção que o parâmetro parsingMode Especifica o `jsonArray` analisador. Especificando o analisador correto e ter os dados corretos entrada são apenas dois requisitos específicos de matriz para indexar JSON blobs.

    POST https://[service name].search.windows.net/indexers?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "jsonArray" } }
    }

Novamente, tenha em atenção que os mapeamentos de campo podem ser omitidos. Supondo que um índice com "id" com nomes idênticos e campos de "text", o indexador blob pode inferir o mapeamento correto sem uma lista de mapeamento de campo explícita.

<a name="nested-json-arrays"></a>

### <a name="nested-json-arrays"></a>Matrizes JSON aninhadas
E se deseja indexar uma matriz de objetos JSON, mas essa matriz está aninhada em algum lugar dentro do documento? Pode escolher qual propriedade contém a matriz usando o `documentRoot` propriedade de configuração. Por exemplo, se os blobs ter este aspeto:

    {
        "level1" : {
            "level2" : [
                { "id" : "1", "text" : "Use the documentRoot property" },
                { "id" : "2", "text" : "to pluck the array you want to index" },
                { "id" : "3", "text" : "even if it's nested inside the document" }  
            ]
        }
    }

Utilize esta configuração para indexar a matriz contida no `level2` propriedade:

    {
        "name" : "my-json-array-indexer",
        ... other indexer properties
        "parameters" : { "configuration" : { "parsingMode" : "jsonArray", "documentRoot" : "/level1/level2" } }
    }

### <a name="how-to-parse-blobs-with-multiple-json-entities-separated-by-newlines"></a>Como analisar a blobs com várias entidades JSON separadas por garantidamente

Se o blob contém várias entidades JSON separadas por uma nova linha e pretende que cada elemento para se tornar um documento separado do Azure Search, pode optar pela funcionalidade de linhas JSON. Por exemplo, com o blob seguinte (em que existem três diferentes entidades JSON), que pode preencher o índice da Azure Search com três seprate documentos, cada um com campos "id" e "text".

    { "id" : "1", "text" : "example 1" }
    { "id" : "2", "text" : "example 2" }
    { "id" : "3", "text" : "example 3" }

Para linhas JSON, a definição de indexador deve ter um aspeto semelhante ao seguinte exemplo. Tenha em atenção que o parâmetro parsingMode Especifica o `jsonLines` analisador. 

    POST https://[service name].search.windows.net/indexers?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "jsonLines" } }
    }

Novamente, tenha em atenção que os mapeamentos de campo podem ser omitido, semelhante a `jsonArray` modo de análise.

### <a name="using-field-mappings-to-build-search-documents"></a>Através de mapeamentos de campo para criar documentos de pesquisa

Quando os campos de origem e destino não estão alinhados perfeitamente, pode definir uma secção de mapeamento de campo no corpo do pedido para associações de campo a campo explícitas.

Atualmente, do Azure Search não consegue indexar documentos JSON arbitrários diretamente uma vez que suporta apenas os tipos de dados primitivos, matrizes de seqüência de caracteres e pontos de GeoJSON. No entanto, pode usar **mapeamentos de campo** para escolher partes do documento JSON e "lift-los" nos campos de nível superior do documento de pesquisa. Para saber mais sobre noções básicas de mapeamentos de campo, veja [mapeamentos de campo em indexadores do Azure Search](search-indexer-field-mappings.md).

Revisitando nosso documento JSON de exemplo:

    {
        "article" : {
            "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13"
            "tags" : [ "search", "storage", "howto" ]    
        }
    }

Partem do princípio de um índice de pesquisa com os seguintes campos: `text` typu `Edm.String`, `date` typu `Edm.DateTimeOffset`, e `tags` do tipo `Collection(Edm.String)`. Tenha em atenção a discrepância entre "datePublished" na origem e `date` campo no índice. Para mapear o seu JSON para o formato pretendido, utilize os seguintes mapeamentos de campo:

    "fieldMappings" : [
        { "sourceFieldName" : "/article/text", "targetFieldName" : "text" },
        { "sourceFieldName" : "/article/datePublished", "targetFieldName" : "date" },
        { "sourceFieldName" : "/article/tags", "targetFieldName" : "tags" }
      ]

Os nomes de campos de origem os mapeamentos são especificados com o [ponteiro JSON](https://tools.ietf.org/html/rfc6901) notação. Começar com uma barra de segurança para fazer referência à raiz do seu documento JSON, em seguida, selecionar a propriedade pretendida (no nível arbitrário de aninhamento) com o caminho de barra por vírgulas de encaminhamento.

Também pode consultar elementos individuais da matriz usando um índice baseado em zero. Por exemplo, para escolher o primeiro elemento da matriz "etiquetas" do exemplo acima, utilize um mapeamento de campo como este:

    { "sourceFieldName" : "/article/tags/0", "targetFieldName" : "firstTag" }

> [!NOTE]
> Se um nome de campo de origem num caminho de mapeamento de campo se refere a uma propriedade que não existe em JSON, esse mapeamento é ignorado sem erros. Isso é feito para que podemos oferecer suporte a documentos com um esquema diferente (que é um caso de utilização comuns). Porque não existe nenhuma validação, precisa tomar cuidado para evitar erros de digitação na sua especificação de mapeamento de campo.
>
>

### <a name="rest-example"></a>Exemplo do REST

Esta secção é um resumo de todos os pedidos utilizado para a criação de objetos. Para ver um debate das partes de componente, consulte as secções anteriores neste artigo.

### <a name="data-source-request"></a>Pedido de origem de dados

Todos os indexadores exigem um objeto de origem de dados que fornece informações de ligação aos dados existentes. 

    POST https://[service name].search.windows.net/datasources?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key for Azure Search]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "optional, my-folder" }
    }  


### <a name="index-request"></a>Pedido de índice

Todos os indexadores requerem um índice de destino que recebe os dados. O corpo do pedido define o esquema de índice, consistindo em campos, atribuídos para oferecer suporte os comportamentos desejados num índice pesquisável. Este índice deve estar vazio quando executar o indexador. 

    POST https://[service name].search.windows.net/indexes?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key for Azure Search]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
          ]
    }


### <a name="indexer-request"></a>Pedido de indexador

Este pedido mostra um indexador completamente especificado. Ele inclui [mapeamentos de campo](#field-mappings), que foram omitido nos exemplos anteriores. Lembre-se de que "agenda", "parâmetros", e "fieldMappings" são opcionais, desde que houver um padrão disponível. Omitir "agendar" faz com que o indexador para executar imediatamente. Omitir "parsingMode" faz com que o índice para utilizar a predefinição de "json".

Criar o indexador no Azure Search aciona a importação de dados. Ele é executado imediatamente e, posteriormente, com base numa agenda se forneceu uma.

    POST https://[service name].search.windows.net/indexers?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key for Azure Search]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "json" } },
      "fieldMappings" : [
        { "sourceFieldName" : "/article/text", "targetFieldName" : "text" },
        { "sourceFieldName" : "/article/datePublished", "targetFieldName" : "date" },
        { "sourceFieldName" : "/article/tags", "targetFieldName" : "tags" }
        ]
    }


<a name="json-indexer-dotnet"></a>

## <a name="use-net-sdk"></a>Utilizar o .NET SDK

O SDK de .NET totalmente tem paridade com a API REST. Recomendamos que reveja a secção de REST API anterior para aprender conceitos, fluxo de trabalho e os requisitos. Em seguida, pode consultar a seguinte documentação de referência de .NET API para implementar um indexador JSON em código gerenciado.

+ [microsoft.azure.search.models.datasource](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasource?view=azure-dotnet)
+ [microsoft.azure.search.models.datasourcetype](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasourcetype?view=azure-dotnet) 
+ [microsoft.azure.search.models.index](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index?view=azure-dotnet) 
+ [microsoft.azure.search.models.indexer](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer?view=azure-dotnet)

## <a name="see-also"></a>Consulte também

+ [Indexadores na Azure Search](search-indexer-overview.md)
+ [Indexação do armazenamento de Blobs do Azure com o Azure Search](search-howto-index-json-blobs.md)
+ [Indexar blobs CSV com o indexador de Blobs do Azure Search](search-howto-index-csv-blobs.md)
+ [Tutorial: Pesquisar dados semiestruturados do armazenamento de Blobs do Azure](search-semi-structured-data.md)
