---
title: 'Tutorial: REST e IA sobre bolhas Azure'
titleSuffix: Azure Cognitive Search
description: Percorra um exemplo de extração de texto e processamento de linguagem natural sobre conteúdos no armazenamento blob usando o Carteiro e as APIs de PESQUISA Cognitiva Azure.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 02/26/2020
ms.openlocfilehash: 8acafa14afab507b704806056efac0f877a47684
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "78190727"
---
# <a name="tutorial-use-rest-and-ai-to-generate-searchable-content-from-azure-blobs"></a>Tutorial: Use REST e IA para gerar conteúdo pesquisável a partir de blobs Azure

Se tiver texto ou imagens não estruturadas no armazenamento do Azure Blob, um pipeline de enriquecimento de [IA](cognitive-search-concept-intro.md) pode extrair informações e criar novos conteúdos que são úteis para cenários de pesquisa de texto completo ou de mineração de conhecimento. Embora um pipeline possa processar imagens, este tutorial REST centra-se no texto, aplicando a deteção de linguagem e processamento de linguagem natural para criar novos campos que você pode alavancar em consultas, facetas e filtros.

Este tutorial utiliza o Carteiro e as [APIs](https://docs.microsoft.com/rest/api/searchservice/) de Repouso de Pesquisa para executar as seguintes tarefas:

> [!div class="checklist"]
> * Comece com documentos inteiros (texto não estruturado) tais como PDF, HTML, DOCX e PPTX no armazenamento azure Blob.
> * Defina um oleoduto que extrai texto, detete linguagem, reconheça entidades e detete frases-chave.
> * Defina um índice para armazenar a saída (conteúdo bruto, mais pares de valor de nome gerados pelo gasoduto).
> * Execute o gasoduto para iniciar transformações e análises, e para criar e carregar o índice.
> * Explore os resultados usando a pesquisa completa de texto e uma sintaxe de consulta rica.

Se não tiver uma subscrição Azure, abra uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

+ [Storage do Azure](https://azure.microsoft.com/services/storage/)
+ [Aplicação de ambiente de trabalho Postman](https://www.getpostman.com/)
+ [Criar](search-create-service-portal.md) ou [encontrar um serviço](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) de pesquisa existente 

> [!Note]
> Pode utilizar o serviço gratuito para este tutorial. Um serviço de pesquisa gratuito limita-o a três índices, três indexadores e três fontes de dados. Este tutorial cria um de cada. Antes de começar, certifique-se de que tem espaço ao seu serviço para aceitar os novos recursos.

## <a name="download-files"></a>Transferir ficheiros

1. Abra esta [pasta OneDrive](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4) e no canto superior esquerdo, clique em **Baixar** para copiar os ficheiros para o seu computador. 

1. Clique no ficheiro zip e **selecione Extrato Tudo**. Existem 14 ficheiros de vários tipos. Vaiusar 7 para este exercício.

## <a name="1---create-services"></a>1 - Criar serviços

Este tutorial utiliza a Pesquisa Cognitiva Azure para indexação e consultas, Serviços Cognitivos no backend para enriquecimento de IA, e armazenamento Azure Blob para fornecer os dados. Este tutorial permanece sob a atribuição gratuita de 20 transações por indexante por dia em Serviços Cognitivos, pelo que os únicos serviços que precisa para criar são a pesquisa e armazenamento.

Se possível, crie tanto na mesma região como grupo de recursos para proximidade e gestão. Na prática, a sua conta de Armazenamento Azure pode estar em qualquer região.

### <a name="start-with-azure-storage"></a>Comece com o Armazenamento Azure

1. [Inscreva-se no portal Azure](https://portal.azure.com/) e clique **em + Criar Recurso**.

1. Procure a conta de *armazenamento* e selecione a oferta da Conta de Armazenamento da Microsoft.

   ![Criar conta de Armazenamento](media/cognitive-search-tutorial-blob/storage-account.png "Criar conta de Armazenamento")

1. No separador Basics, são necessários os seguintes itens. Aceite os incumprimentos para todo o resto.

   + **Grupo de recursos**. Selecione um existente ou crie um novo, mas use o mesmo grupo para todos os serviços para que possa geri-los coletivamente.

   + **Nome da conta de armazenamento.** Se pensa que pode ter vários recursos do mesmo tipo, use o nome para desambiguar por tipo e região, por exemplo *blobstoragewestus*. 

   + **Localização**. Se possível, escolha o mesmo local utilizado para a Pesquisa Cognitiva Azure e Serviços Cognitivos. Um único local anula as cargas de largura de banda.

   + **Tipo de conta.** Escolha o predefinido, *StorageV2 (finalidade geral v2)*.

1. Clique em **Rever + Criar** para criar o serviço.

1. Uma vez criado, clique em **Ir ao recurso** para abrir a página 'Overview'.

1. Clique no serviço **Blobs.**

1. Clique em **+ Recipiente** para criar um recipiente e *nomeá-lo cog-search-demo*.

1. Selecione *cog-search-demo* e, em seguida, clique em **Carregar** para abrir a pasta onde guardou os ficheiros de descarregamento. Selecione todos os ficheiros não-imagem. Devia ter 7 ficheiros. Clique **em OK** para carregar.

   ![Upload de ficheiros de amostras](media/cognitive-search-tutorial-blob/sample-files.png "Upload de ficheiros de amostras")

1. Antes de sair do Azure Storage, obtenha uma cadeia de ligação para que possa formular uma ligação em Azure Cognitive Search. 

   1. Volte para a página de visão geral da sua conta de armazenamento (usamos *blobstragewestus* como exemplo). 
   
   1. No painel de navegação esquerdo, selecione **teclas de acesso** e copie uma das cordas de ligação. 

   A cadeia de ligação é um URL semelhante ao seguinte exemplo:

      ```http
      DefaultEndpointsProtocol=https;AccountName=cogsrchdemostorage;AccountKey=<your account key>;EndpointSuffix=core.windows.net
      ```

1. Guarde a corda de ligação ao Bloco de Notas. Vai precisar mais tarde quando configurar a ligação de fonte de dados.

### <a name="cognitive-services"></a>Serviços Cognitivos

O enriquecimento de IA é apoiado por Serviços Cognitivos, incluindo Text Analytics e Computer Vision para linguagem natural e processamento de imagem. Se o seu objetivo fosse completar um protótipo ou projeto real, nesta altura forneceria serviços cognitivos (na mesma região que a Pesquisa Cognitiva Azure) para que o pudesse anexar a operações de indexação.

Para este exercício, no entanto, você pode saltar o fornecimento de recursos porque a Pesquisa Cognitiva Azure pode ligar-se aos Serviços Cognitivos nos bastidores e dar-lhe 20 transações gratuitas por corrida de indexador. Uma vez que este tutorial utiliza 7 transações, a atribuição gratuita é suficiente. Para projetos maiores, planeie o fornecimento de Serviços Cognitivos no nível S0 pay-as-you-go. Para mais informações, consulte [Anexar Serviços Cognitivos](cognitive-search-attach-cognitive-services.md).

### <a name="azure-cognitive-search"></a>Azure Cognitive Search

O terceiro componente é a Pesquisa Cognitiva Azure, que pode [criar no portal.](search-create-service-portal.md) Pode utilizar o free tier para completar esta passagem. 

Tal como acontece com o armazenamento da Azure Blob, tire um momento para recolher a chave de acesso. Mais à frente, quando começar a estruturar pedidos, terá de fornecer o ponto final e a chave api-key usado para autenticar cada pedido.

### <a name="get-an-admin-api-key-and-url-for-azure-cognitive-search"></a>Obtenha uma chave de api-key e URL para pesquisa cognitiva azure

1. [Faça sessão no portal Azure](https://portal.azure.com/), e na página **'Visão Geral** do serviço de pesquisa', obtenha o nome do seu serviço de pesquisa. Pode confirmar o seu nome de serviço revendo o URL do ponto final. Se o seu `https://mydemo.search.windows.net`URL final fosse, `mydemo`o seu nome de serviço seria .

2. Em **Definições** > **Keys,** obtenha uma chave de administração para todos os direitos sobre o serviço. Existem duas chaves de administração intercambiáveis, previstas para a continuidade do negócio no caso de precisar de rolar uma. Pode utilizar a chave primária ou secundária nos pedidos de adição, modificação e aparas de objetos.

   Pegue a chave de consulta também. É uma boa prática emitir pedidos de consulta com acesso só para leitura.

   ![Obtenha o nome de serviço e as chaves de administração e consulta](media/search-get-started-nodejs/service-name-and-keys.png)

Todos os pedidos requerem uma chave api no cabeçalho de cada pedido enviado ao seu serviço. Uma chave válida estabelece confiança, por pedido, entre o pedido que envia o pedido e o serviço que o trata.

## <a name="2---set-up-postman"></a>2 - Configurar carteiro

Inicie o Postman e configure um pedido de HTTP. Se não estiver familiarizado com esta ferramenta, consulte [a Explore Azure Cognitive Search REST APIs utilizando o Carteiro](search-get-started-postman.md).

Os métodos de pedido utilizados neste tutorial são **POST,** **PUT**e **GET**. Utilizará os métodos para fazer quatro chamadas API para o seu serviço de pesquisa: criar uma fonte de dados, um skillset, um índice e um indexante.

Em Cabeçalhos, desloque `application/json` o `api-key` "Content-type" e coloque-o na chave de api do seu serviço de Pesquisa Cognitiva Azure. Assim que definir os cabeçalhos, pode usá-los para cada pedido neste exercício.

  ![URL de pedido de carteiro e cabeçalho](media/search-get-started-postman/postman-url.png "URL de pedido de carteiro e cabeçalho")

## <a name="3---create-the-pipeline"></a>3 - Criar o gasoduto

Na Pesquisa Cognitiva Azure, o processamento de IA ocorre durante a indexação (ou ingestão de dados). Esta parte do walkthrough cria quatro objetos: fonte de dados, definição de índice, skillset, indexante. 

### <a name="step-1-create-a-data-source"></a>Passo 1: criar uma origem de dados

Um [objeto de origem](https://docs.microsoft.com/rest/api/searchservice/create-data-source) de dados fornece a cadeia de ligação ao recipiente Blob que contém os ficheiros.

1. Utilize **POST** post e o seguinte URL, substituindo o seu NOME DE SERVIÇO pelo nome real do seu serviço.

   ```http
   https://[YOUR-SERVICE-NAME].search.windows.net/datasources?api-version=2019-05-06
   ```

1. No pedido **Body**, copie a seguinte `connectionString` definição JSON, substituindo a ligação real da sua conta de armazenamento. 

   Lembre-se de editar o nome do recipiente também. Sugerimos "cog-search-demo" para o nome do contentor num passo anterior.

    ```json
    {
      "name" : "cog-search-demo-ds",
      "description" : "Demo files to demonstrate cognitive search capabilities.",
      "type" : "azureblob",
      "credentials" :
      { "connectionString" :
        "DefaultEndpointsProtocol=https;AccountName=<YOUR-STORAGE-ACCOUNT>;AccountKey=<YOUR-ACCOUNT-KEY>;"
      },
      "container" : { "name" : "<YOUR-BLOB-CONTAINER-NAME>" }
    }
    ```
1. Envie o pedido. Deve ver um código de estado de 201 confirmando o sucesso. 

Se obtiver um erro 403 ou 404, verifique a construção do pedido: `api-version=2019-05-06` deve estar no ponto final, `api-key` deve estar no Cabeçalho após `Content-Type` e o valor deve ser válido para um serviço de pesquisa. É melhor executar o documento JSON através de um validador JSON online para se certificar de que a sintaxe está correta. 

### <a name="step-2-create-a-skillset"></a>Passo 2: Criar uma habilidade

Um [objeto de habilidade](https://docs.microsoft.com/rest/api/searchservice/create-skillset) é um conjunto de passos de enriquecimento aplicados ao seu conteúdo. 

1. Utilize o **PUT** e o seguinte URL, substituindo o seu NOME DE SERVIÇO pelo nome real do seu serviço.

    ```http
    https://[YOUR-SERVICE-NAME].search.windows.net/skillsets/cog-search-demo-ss?api-version=2019-05-06
    ```

1. No **órgão de**pedido, copie a definição JSON abaixo. Esta habilidade consiste nas seguintes habilidades incorporadas.

   | Habilidade                 | Descrição    |
   |-----------------------|----------------|
   | [Reconhecimento de entidade](cognitive-search-skill-entity-recognition.md) | Extrai os nomes de pessoas, organizações e locais de conteúdo no recipiente de bolhas. |
   | [Deteção de Idioma](cognitive-search-skill-language-detection.md) | Deteta a linguagem do conteúdo. |
   | [Divisão de Texto](cognitive-search-skill-textsplit.md)  | Quebra o conteúdo grande em pedaços menores antes de chamar a frase-chave de extração. A extração de expressões-chave aceita entradas de 50 000 carateres ou menos. Alguns dos ficheiros de exemplo precisam de ser divididos para caberem dentro deste limite. |
   | [Extração de Expressões-Chave](cognitive-search-skill-keyphrases.md) | Tira as frases principais. |

   Cada competência é executada no conteúdo do documento. Durante o processamento, a Pesquisa Cognitiva Azure quebra cada documento para ler conteúdo a partir de diferentes formatos de ficheiros. O texto encontrado proveniente do ficheiro de origem é colocado num campo ```content``` gerado (um para cada documento). Como tal, a ```"/document/content"```entrada torna-se .

   Para a extração de frases-chave, porque usamos a habilidade de splitter de ```"document/pages/*"``` texto para quebrar ficheiros ```"/document/content"```maiores em páginas, o contexto para a habilidade de extração de frases-chave é (para cada página do documento) em vez de .

    ```json
    {
      "description": "Extract entities, detect language and extract key-phrases",
      "skills":
      [
        {
          "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
          "categories": [ "Person", "Organization", "Location" ],
          "defaultLanguageCode": "en",
          "inputs": [
            { "name": "text", "source": "/document/content" }
          ],
          "outputs": [
            { "name": "persons", "targetName": "persons" },
            { "name": "organizations", "targetName": "organizations" },
            { "name": "locations", "targetName": "locations" }
          ]
        },
        {
          "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
          "inputs": [
            { "name": "text", "source": "/document/content" }
          ],
          "outputs": [
            { "name": "languageCode", "targetName": "languageCode" }
          ]
        },
        {
          "@odata.type": "#Microsoft.Skills.Text.SplitSkill",
          "textSplitMode" : "pages",
          "maximumPageLength": 4000,
          "inputs": [
            { "name": "text", "source": "/document/content" },
            { "name": "languageCode", "source": "/document/languageCode" }
          ],
          "outputs": [
            { "name": "textItems", "targetName": "pages" }
          ]
        },
        {
          "@odata.type": "#Microsoft.Skills.Text.KeyPhraseExtractionSkill",
          "context": "/document/pages/*",
          "inputs": [
            { "name": "text", "source": "/document/pages/*" },
            { "name":"languageCode", "source": "/document/languageCode" }
          ],
          "outputs": [
            { "name": "keyPhrases", "targetName": "keyPhrases" }
          ]
        }
      ]
    }
    ```
    Veja a seguir uma representação gráfica do conjunto de competências. 

    ![Compreender uma habilidade](media/cognitive-search-tutorial-blob/skillset.png "Compreender uma habilidade")

1. Envie o pedido. O carteiro deve devolver um código de estado de 201 confirmando o sucesso. 

> [!NOTE]
> As saídas podem ser mapeadas para um índice, utilizadas como entradas para uma competência a jusante ou ambas, como é o caso do código de idioma. No índice, o código de idioma é útil para a filtragem. Como entrada, o código de idioma é utilizado pelas competências de análise de texto para informar sobre as regras linguísticas em torno da separação de palavras. Para obter mais informações acerca das noções básicas do conjunto de competências, veja [Como definir um conjunto de competências](cognitive-search-defining-skillset.md).

### <a name="step-3-create-an-index"></a>Passo 3: Criar um índice

Um [índice](https://docs.microsoft.com/rest/api/searchservice/create-index) fornece o esquema usado para criar a expressão física do seu conteúdo em índices invertidos e outras construções em Pesquisa Cognitiva Azure. O maior componente de um índice é a recolha de campos, onde o tipo de dados e atributos determinam conteúdos e comportamentos na Pesquisa Cognitiva Azure.

1. Utilize o **PUT** e o seguinte URL, substituindo o seu NOME DE SERVIÇO pelo nome real do seu serviço, para nomear o seu índice.

   ```http
   https://[YOUR-SERVICE-NAME].search.windows.net/indexes/cog-search-demo-idx?api-version=2019-05-06
   ```

1. No **pedido Body,** copie a seguinte definição JSON. O `content` campo armazena o documento em si. Campos adicionais para, `languageCode` `keyPhrases`e `organizations` representam novas informações (campos e valores) criadas pela skillset.

    ```json
    {
      "fields": [
        {
          "name": "id",
          "type": "Edm.String",
          "key": true,
          "searchable": true,
          "filterable": false,
          "facetable": false,
          "sortable": true
        },
        {
          "name": "metadata_storage_name",
          "type": "Edm.String",
          "searchable": false,
          "filterable": false,
          "facetable": false,
          "sortable": false
        },
        {
          "name": "content",
          "type": "Edm.String",
          "sortable": false,
          "searchable": true,
          "filterable": false,
          "facetable": false
        },
        {
          "name": "languageCode",
          "type": "Edm.String",
          "searchable": true,
          "filterable": false,
          "facetable": false
        },
        {
          "name": "keyPhrases",
          "type": "Collection(Edm.String)",
          "searchable": true,
          "filterable": false,
          "facetable": false
        },
        {
          "name": "persons",
          "type": "Collection(Edm.String)",
          "searchable": true,
          "sortable": false,
          "filterable": true,
          "facetable": true
        },
        {
          "name": "organizations",
          "type": "Collection(Edm.String)",
          "searchable": true,
          "sortable": false,
          "filterable": true,
          "facetable": true
        },
        {
          "name": "locations",
          "type": "Collection(Edm.String)",
          "searchable": true,
          "sortable": false,
          "filterable": true,
          "facetable": true
        }
      ]
    }
    ```

1. Envie o pedido. O carteiro deve devolver um código de estado de 201 confirmando o sucesso. 

### <a name="step-4-create-and-run-an-indexer"></a>Passo 4: Criar e executar um indexante

Um [Indexer](https://docs.microsoft.com/rest/api/searchservice/create-indexer) conduz o oleoduto. Os três componentes que criou até agora (fonte de dados, skillset, índice) são inputs para um indexante. Criar o indexador em Azure Cognitive Search é o evento que põe todo o oleoduto em movimento. 

1. Utilize o **PUT** e o seguinte URL, substituindo o seu NOME DE SERVIÇO pelo nome real do seu serviço, para nomear o seu indexante.

   ```http
   https://[servicename].search.windows.net/indexers/cog-search-demo-idxr?api-version=2019-05-06
   ```

1. No **órgão de**pedido, copie a definição JSON abaixo. Repare nos elementos de mapeamento de campo; estes mapeamentos são importantes porque definem o fluxo de dados. 

   Os `fieldMappings` são processados antes da habilidade, enviando conteúdo da fonte de dados para campos-alvo em um índice. Utilizará mapeamentos de campo para enviar conteúdo existente e não modificado para o índice. Se os nomes e tipos de campo forem os mesmos em ambas as extremidades, não é necessário mapeamento.

   São `outputFieldMappings` para campos criados por habilidades, e assim processados após a execução da habilidade. As referências `sourceFieldNames` `outputFieldMappings` a in não existem até que a rachadura ou enriquecimento de documentos as crie. O `targetFieldName` é um campo num índice, definido no esquema de índice.

    ```json
    {
      "name":"cog-search-demo-idxr",    
      "dataSourceName" : "cog-search-demo-ds",
      "targetIndexName" : "cog-search-demo-idx",
      "skillsetName" : "cog-search-demo-ss",
      "fieldMappings" : [
        {
          "sourceFieldName" : "metadata_storage_path",
          "targetFieldName" : "id",
          "mappingFunction" :
            { "name" : "base64Encode" }
        },
        {
          "sourceFieldName" : "metadata_storage_name",
          "targetFieldName" : "metadata_storage_name",
          "mappingFunction" :
            { "name" : "base64Encode" }
        },
        {
          "sourceFieldName" : "content",
          "targetFieldName" : "content"
        }
      ],
      "outputFieldMappings" :
      [
        {
          "sourceFieldName" : "/document/persons",
          "targetFieldName" : "persons"
        },
        {
          "sourceFieldName" : "/document/organizations",
          "targetFieldName" : "organizations"
        },
        {
          "sourceFieldName" : "/document/locations",
          "targetFieldName" : "locations"
        },
        {
          "sourceFieldName" : "/document/pages/*/keyPhrases/*",
          "targetFieldName" : "keyPhrases"
        },
        {
          "sourceFieldName": "/document/languageCode",
          "targetFieldName": "languageCode"
        }
      ],
      "parameters":
      {
        "maxFailedItems":-1,
        "maxFailedItemsPerBatch":-1,
        "configuration":
        {
          "dataToExtract": "contentAndMetadata",
          "parsingMode": "default",
          "firstLineContainsHeaders": false,
          "delimitedTextDelimiter": ","
        }
      }
    }
    ```

1. Envie o pedido. O carteiro deve devolver um código de estado de 201 confirmando o processamento bem sucedido. 

   A conclusão deste passo demora vários minutos. Apesar de o conjunto de dados ser pequeno, as competências analíticas realizam um processo de computação intensa. 

> [!NOTE]
> A criação de um indexador invoca o pipeline. Se houver problemas em atingir os dados, com o mapeamento de entradas e saídas ou com a ordem das operações, estes vão surgir nesta fase. Para executar novamente o pipeline com as alterações de código ou script, pode ter de remover primeiro os objetos. Para obter mais informações, veja [Repor e executar novamente](#reset).

#### <a name="about-indexer-parameters"></a>Sobre parâmetros indexantes

O script define ```"maxFailedItems"``` como -1, o que indica ao motor de indexação para ignorar os erros durante a importação de dados. Isto é aceitável porque há tão poucos documentos na fonte de dados da demonstração. Para uma origem de dados maior, deve definir o valor com um número maior que 0.

A ```"dataToExtract":"contentAndMetadata"``` declaração diz ao indexante para extrair automaticamente o conteúdo de diferentes formatos de ficheiros, bem como metadados relacionados com cada ficheiro. 

Quando o conteúdo é extraído, pode definir ```imageAction``` para extrair texto das imagens existentes na origem de dados. A ```"imageAction":"generateNormalizedImages"``` configuração, combinada com a Habilidade de Habilidade oCR e a Habilidade de Fusão de Texto, diz ao indexante para extrair texto das imagens (por exemplo, a palavra "parar" de um sinal de paragem de tráfego), e incorpora-o como parte do campo de conteúdo. Este comportamento aplica-se tanto às imagens incorporadas nos documentos (tal como uma imagem num PDF) como às imagens existentes na origem de dados, por exemplo, um ficheiro JPG.

## <a name="4---monitor-indexing"></a>4 - Monitorização da indexação

A indexação e o enriquecimento começam assim que apresentar o pedido do Indexante criar. Dependendo das habilidades cognitivas que definiu, a indexação pode demorar um pouco. Para saber se o indexador ainda está em execução, envie o seguinte pedido para verificar o estado do indexador.

1. Utilize o **GET** e o seguinte URL, substituindo o seu NOME DE SERVIÇO pelo nome real do seu serviço, para nomear o seu indexante.

   ```http
   https://[YOUR-SERVICE-NAME].search.windows.net/indexers/cog-search-demo-idxr/status?api-version=2019-05-06
   ```

1. Reveja a resposta para saber se o indexante está em execução, ou para visualizar informações de erro e aviso.  

Se estiver a utilizar o nível Livre, espera-se a seguinte mensagem: "Não consegui extrair conteúdo ou metadados do seu documento. Texto extraído truncado para caracteres '32768'". Esta mensagem aparece porque a indexação de blob no nível Livre tem um limite de[32K na extração](search-limits-quotas-capacity.md#indexer-limits)de caracteres . Não verá esta mensagem para este conjunto de dados em níveis mais altos. 

> [!NOTE]
> Os avisos são comuns em alguns cenários e nem sempre indicam um problema. Por exemplo, se um recipiente de blob incluir ficheiros de imagem, e o gasoduto não manusear imagens, receberá um aviso afirmando que as imagens não foram processadas.

## <a name="5---search"></a>5 - Pesquisa

Agora que criou novos campos e informações, vamos fazer algumas consultas para entender o valor da pesquisa cognitiva, uma vez que se relaciona com um cenário típico de pesquisa.

Recorde-se que começamos com conteúdo blob, onde `content` todo o documento é embalado num único campo. Pode pesquisar neste campo e encontrar correspondências com as suas consultas.

1. Utilize o **GET** e o seguinte URL, substituindo o seu NOME DE SERVIÇO pelo nome real do `content` seu serviço, para procurar por exemplos de um termo ou frase, devolvendo o campo e uma contagem dos documentos correspondentes.

   ```http
   https://[YOUR-SERVICE-NAME].search.windows.net/indexes/cog-search-demo-idx?search=*&$count=true&$select=content?api-version=2019-05-06
   ```
   
   Os resultados deste conteúdo do documento de devolução de consulta, que é o mesmo resultado que obteria se usasse o indexador blob sem o pipeline de pesquisa cognitiva. Este campo é pesquisável, mas impraticável se quiser utilizar facetas, filtros ou auto-completar.

   ![Saída de campo de conteúdo](media/cognitive-search-tutorial-blob/content-output.png "Saída de campo de conteúdo")
   
1. Para a segunda consulta, devolva alguns dos novos campos criados pelo oleoduto (pessoas, organizações, locais, idiomaCódigo). Estamos a omitir frases-chave para a brevidade, mas deve incluí-las se quiser ver esses valores.

   ```http
   https://mydemo.search.windows.net/indexes/cog-search-demo-idx/docs?search=*&$count=true&$select=metadata_storage_name,persons,organizations,locations,languageCode&api-version=2019-05-06
   ```
   Os campos da declaração $select contêm novas informações criadas a partir das capacidades naturais de processamento de linguagem dos Serviços Cognitivos. Como seria de esperar, há algum ruído nos resultados e variação entre documentos, mas em muitos casos, os modelos analíticos produzem resultados precisos.

   A imagem que se segue mostra resultados para a carta aberta de Satya Nadella ao assumir o papel de CEO na Microsoft.

   ![Saída de gasoduto](media/cognitive-search-tutorial-blob/pipeline-output.png "Saída de gasoduto")

1. Para ver como pode aproveitar estes campos, adicione um parâmetro de faceta para devolver uma agregação de documentos correspondentes por localização.

   ```http
   https://[YOUR-SERVICE-NAME].search.windows.net/indexes/cog-search-demo-idx/docs?search=*&facet=locations&api-version=2019-05-06
   ``` 

   Neste exemplo, para cada local, existem 2 ou 3 fósforos.

   ![Saída de faceta](media/cognitive-search-tutorial-blob/facet-output.png "Saída de faceta")
   

1. Neste exemplo final, aplique um filtro na coleção de organizações, devolvendo dois fósforos para critérios de filtro baseados no NASDAQ.

   ```http
   cog-search-demo-idx/docs?search=*&$filter=organizations/any(organizations: organizations eq 'NASDAQ')&$select=metadata_storage_name,organizations&$count=true&api-version=2019-05-06
   ```

Estas consultas ilustram algumas das formas de trabalhar com sintaxe de consulta e filtros em novos campos criados pela procura cognitiva. Para obter mais exemplos de consultas, consulte [Exemplos em Documentos de Pesquisa REST API,](https://docs.microsoft.com/rest/api/searchservice/search-documents#bkmk_examples)Exemplos simples de consulta de [sintaxe](search-query-simple-examples.md)e exemplos de [consulta lucene completa.](search-query-lucene-examples.md)

<a name="reset"></a>

## <a name="reset-and-rerun"></a>Repor e executar novamente

Nas fases experimentais iniciais de desenvolvimento, a abordagem mais prática para a iteração do design é apagar os objetos da Pesquisa Cognitiva Azure e permitir que o seu código os reconstrua. Os nomes dos recursos são exclusivos. Quando elimina um objeto, pode recriá-lo com o mesmo nome.

Pode utilizar o portal para eliminar índices, indexadores, fontes de dados e competências. Ao eliminar o indexante, pode eliminar opcionalmente, seletivamente, o índice, a skillset e a fonte de dados ao mesmo tempo.

![Eliminar objetos de pesquisa](./media/cognitive-search-tutorial-blob-python/py-delete-indexer-delete-all.png "Eliminar objetos de pesquisa no portal")

Ou use **DELETE** e forneça URLs a cada objeto. O comando seguinte elimina um indexante.

```http
DELETE https://[YOUR-SERVICE-NAME].search.windows.net/indexers/cog-search-demo-idxr?api-version=2019-05-06
```

O código de estado 204 é devolvido após uma eliminação com êxito.

## <a name="takeaways"></a>Conclusões

Este tutorial demonstra os passos básicos para criar um pipeline de indexação melhorado através da criação de partes do componente: uma origem de dados, um conjunto de competências, um índice e um indexador.

Foram introduzidas [competências incorporadas,](cognitive-search-predefined-skills.md) juntamente com a definição de skillset e a mecânica das habilidades de acorrentar através de inputs e saídas. Também descobriu `outputFieldMappings` que na definição do indexador é necessário para encaminhar valores enriquecidos do oleoduto para um índice pesquisável num serviço de Pesquisa Cognitiva Azure.

Por fim, aprendeu como testar os resultados e repor o sistema para iterações futuras. Aprendeu que a emissão de consultas acerca do índice devolve o resultado criado pelo pipeline de indexação melhorado. 

## <a name="clean-up-resources"></a>Limpar recursos

Quando se trabalha na sua própria subscrição, no final de um projeto, é uma boa ideia remover os recursos de que já não precisa. Os recursos que deixar em execução podem custar-lhe dinheiro. Pode eliminar recursos individualmente ou eliminar o grupo de recursos para eliminar todo o conjunto de recursos.

Pode encontrar e gerir recursos no portal, utilizando a ligação De Todos os recursos ou grupos de Recursos no painel de navegação à esquerda.

## <a name="next-steps"></a>Passos seguintes

Agora que está familiarizado com todos os objetos num oleoduto de enriquecimento de IA, vamos ver mais de perto as definições de habilidades e habilidades individuais.

> [!div class="nextstepaction"]
> [Como criar uma habilidade](cognitive-search-defining-skillset.md)