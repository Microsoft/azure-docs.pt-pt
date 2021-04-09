---
title: 'Tutorial: REST e IA sobre bolhas de Azure'
titleSuffix: Azure Cognitive Search
description: Passe por um exemplo de extração de texto e processamento de linguagem natural sobre o conteúdo no armazenamento blob usando o Carteiro e as APIs de Repouso de Pesquisa Cognitiva Azure.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 11/17/2020
ms.openlocfilehash: 21f0d141567f17c470732088c6a93a2ae7ed3c67
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "94738055"
---
# <a name="tutorial-use-rest-and-ai-to-generate-searchable-content-from-azure-blobs"></a>Tutorial: Use REST e IA para gerar conteúdo pesmável a partir de bolhas Azure

Se tiver texto ou imagens não estruturados no armazenamento do Azure Blob, um [gasoduto de enriquecimento de IA](cognitive-search-concept-intro.md) pode extrair informações e criar novos conteúdos a partir de bolhas que são úteis para pesquisas de texto completo ou cenários de mineração de conhecimento. Embora um pipeline possa processar imagens, este tutorial REST foca-se no texto, aplicando a deteção de linguagem e processamento de linguagem natural para criar novos campos que você pode alavancar em consultas, facetas e filtros.

Este tutorial utiliza o Carteiro e as [APIs search REST](/rest/api/searchservice/) para executar as seguintes tarefas:

> [!div class="checklist"]
> * Crie serviços e uma coleção de Carteiro.
> * Criar um oleoduto de enriquecimento que extraia texto, deteta linguagem, reconhece entidades e deteta frases-chave.
> * Crie um índice para armazenar a saída (conteúdo bruto, mais pares de valor-nome gerados pelo gasoduto).
> * Execute o oleoduto para realizar transformações e análises, e para carregar o índice.
> * Explore resultados utilizando a pesquisa completa por texto e uma sintaxe de consulta rica.

Se não tiver uma subscrição do Azure, abra uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="overview"></a>Descrição Geral

Este tutorial utiliza C# e o Azure Cognitive Search REST APIs para criar uma fonte de dados, índice, indexante e skillset. Você começará com documentos inteiros (texto não estruturado) como PDF, HTML, DOCX e PPTX no armazenamento Azure Blob, e depois executá-los através de um skillset para extrair entidades, frases-chave e outro texto nos ficheiros de conteúdo.

Este skillset usa habilidades incorporadas baseadas em APIs de Serviços Cognitivos. As etapas no oleoduto incluem a deteção de linguagem em texto, extração de frases-chave e reconhecimento de entidades (organizações). Novas informações são armazenadas em novos campos que pode alavancar em consultas, facetas e filtros.

## <a name="prerequisites"></a>Pré-requisitos

+ [Armazenamento do Azure](https://azure.microsoft.com/services/storage/)
+ [Aplicação de ambiente de trabalho Postman](https://www.getpostman.com/)
+ [Criar](search-create-service-portal.md) ou [encontrar um serviço de pesquisa existente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) 

> [!Note]
> Você pode usar o serviço gratuito para este tutorial. Um serviço de pesquisa gratuito limita-o a três índices, três indexantes e três fontes de dados. Este tutorial cria um de cada. Antes de começar, certifique-se de ter espaço no seu serviço para aceitar os novos recursos.

## <a name="download-files"></a>Transferir ficheiros

1. Abra esta [pasta OneDrive](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4) e no canto superior esquerdo, clique em **Baixar** para copiar os ficheiros para o seu computador. 

1. Clique com o botão direito no ficheiro zip e selecione **Extract All**. Existem 14 ficheiros de vários tipos. Vais usar 7 para este exercício.

Opcionalmente, também pode descarregar o código fonte, um ficheiro de recolha do Carteiro, para este tutorial. O código-fonte pode ser encontrado em [https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Tutorial](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Tutorial) .

## <a name="1---create-services"></a>1 - Criar serviços

Este tutorial usa a Azure Cognitive Search para indexar e consultas, Serviços Cognitivos no backend para enriquecimento de IA, e armazenamento Azure Blob para fornecer os dados. Este tutorial fica sob a alocação gratuita de 20 transações por indexante por dia em Serviços Cognitivos, pelo que os únicos serviços que precisa de criar são a pesquisa e armazenamento.

Se possível, crie tanto na mesma região como no grupo de recursos para proximidade e gestão. Na prática, a sua conta de Armazenamento Azure pode estar em qualquer região.

### <a name="start-with-azure-storage"></a>Comece com o armazenamento Azure

1. [Inscreva-se no portal Azure](https://portal.azure.com/) e clique **+ Criar Recurso.**

1. Procure a *conta de armazenamento* e selecione a oferta de Conta de Armazenamento da Microsoft.

   ![Criar conta de Armazenamento](media/cognitive-search-tutorial-blob/storage-account.png "Criar conta de Armazenamento")

1. No separador Básicos, são necessários os seguintes itens. Aceite os incumprimentos para tudo o resto.

   + **Grupo de recursos**. Selecione um existente ou crie um novo, mas use o mesmo grupo para todos os serviços para que possa geri-los coletivamente.

   + **Nome da conta de armazenamento**. Se acha que pode ter múltiplos recursos do mesmo tipo, use o nome para desambiguar por tipo e região, por *exemplo, blobstoragewestus*. 

   + **Localização**. Se possível, escolha o mesmo local utilizado para a Azure Cognitive Search and Cognitive Services. Um único local anula as cargas de largura de banda.

   + **Tipo de Conta**. Escolha o predefinido, *StorageV2 (finalidade geral v2)*.

1. Clique **em 'Rever + Criar'** para criar o serviço.

1. Uma vez criado, clique em **Ir ao recurso** para abrir a página 'Vista Geral'.

1. Clique no serviço **Blobs.**

1. Clique **em + Recipiente** para criar um recipiente e nomeie-o *cog-search-demo*.

1. Selecione *a demonstração de pesquisa de cog e,* em seguida, clique em **Upload** para abrir a pasta onde guardou os ficheiros de descarregamento. Selecione todos os ficheiros de não imagem. Devia ter 7 ficheiros. Clique **em OK** para carregar.

   ![Carregar ficheiros de amostras](media/cognitive-search-tutorial-blob/sample-files.png "Carregar ficheiros de amostras")

1. Antes de sair do Azure Storage, obtenha uma cadeia de ligação para que possa formular uma ligação na Pesquisa Cognitiva Azure. 

   1. Procure de volta à página geral da sua conta de armazenamento *(usamos blobstragewestus* como exemplo). 
   
   1. No painel de navegação esquerdo, selecione **as teclas de acesso** e copie uma das cordas de ligação. 

   A cadeia de ligação é um URL semelhante ao seguinte exemplo:

      ```http
      DefaultEndpointsProtocol=https;AccountName=cogsrchdemostorage;AccountKey=<your account key>;EndpointSuffix=core.windows.net
      ```

1. Guarde o fio de ligação para o Bloco de Notas. Vai precisar mais tarde ao configurar a ligação à fonte de dados.

### <a name="cognitive-services"></a>Serviços Cognitivos

O enriquecimento de IA é apoiado por Serviços Cognitivos, incluindo Análise de Texto e Visão Computacional para o processamento de linguagem natural e imagem. Se o seu objetivo fosse completar um protótipo ou projeto real, você iria neste momento providenciar Serviços Cognitivos (na mesma região que a Azure Cognitive Search) para que você possa anexá-lo a operações de indexação.

Para este exercício, no entanto, você pode ignorar o fornecimento de recursos porque a Azure Cognitive Search pode ligar-se aos Serviços Cognitivos nos bastidores e dar-lhe 20 transações gratuitas por indexer executado. Uma vez que este tutorial utiliza 7 transações, a atribuição gratuita é suficiente. Para projetos maiores, planeie o fornecimento de Serviços Cognitivos no nível S0 pay-as-you-go. Para obter mais informações, consulte [Os Serviços Cognitivos anexados.](cognitive-search-attach-cognitive-services.md)

### <a name="azure-cognitive-search"></a>Azure Cognitive Search

O terceiro componente é Azure Cognitive Search, que pode [criar no portal.](search-create-service-portal.md) Pode utilizar o nível Free para completar este walkthrough. 

Tal como acontece com o armazenamento da Azure Blob, aproveite um momento para recolher a chave de acesso. Mais à frente, quando iniciar os pedidos estruturantes, terá de fornecer o ponto final e a tecla api de administração utilizada para autenticar cada pedido.

### <a name="copy-an-admin-api-key-and-url-for-azure-cognitive-search"></a>Copie uma chave api-chave de administrador e URL para pesquisa cognitiva Azure

1. [Inscreva-se no portal Azure,](https://portal.azure.com/)e na página **de Visão Geral** do seu serviço de pesquisa, obtenha o nome do seu serviço de pesquisa. Pode confirmar o seu nome de serviço revendo o URL do ponto final. Se o seu URL de ponto final `https://mydemo.search.windows.net` fosse, o seu nome de serviço seria `mydemo` .

2. Em **Definições**  >  **Teclas,** obtenha uma chave de administração para todos os direitos sobre o serviço. Existem duas chaves de administração intercambiáveis, previstas para a continuidade do negócio, caso precise de rolar uma. Pode utilizar a tecla primária ou secundária nos pedidos de adição, modificação e eliminação de objetos.

   Pegue a chave de consulta também. É uma boa prática emitir pedidos de consulta com acesso apenas de leitura.

   ![Obtenha o nome de serviço e as chaves de administração e consulta](media/search-get-started-javascript/service-name-and-keys.png)

Todos os pedidos requerem uma chave API no cabeçalho de cada pedido enviado ao seu serviço. Uma chave válida estabelece confiança, por pedido, entre o pedido de envio do pedido e o serviço que o trata.

## <a name="2---set-up-postman"></a>2 - Configurar carteiro

Inicie o Postman e configure um pedido de HTTP. Se não estiver familiarizado com esta ferramenta, consulte As APIs de [Pesquisa Cognitiva explore Azure](search-get-started-rest.md).

Os métodos de pedido utilizados neste tutorial são **POST,** **PUT** e **GET.** Utilizará os métodos para fazer quatro chamadas API para o seu serviço de pesquisa: criar uma fonte de dados, um skillset, um índice e um indexante.

Em Cabeçalhos, desconfie "Content-type" `application/json` e desaver-se `api-key` com a chave api de administração do seu serviço de Pesquisa Cognitiva Azure. Uma vez definidos os cabeçalhos, pode usá-los para todos os pedidos neste exercício.

  ![Carteiro solicita URL e cabeçalho](media/search-get-started-rest/postman-url.png "Carteiro solicita URL e cabeçalho")

## <a name="3---create-the-pipeline"></a>3 - Criar o gasoduto

Na Pesquisa Cognitiva Azure, o enriquecimento ocorre durante a indexação (ou ingestão de dados). Esta parte do walkthrough cria quatro objetos: fonte de dados, definição de índice, skillset, indexante. 

### <a name="step-1-create-a-data-source"></a>Passo 1: criar uma origem de dados

Um [objeto de origem de dados](/rest/api/searchservice/create-data-source) fornece o fio de ligação ao recipiente Blob que contém os ficheiros.

1. Utilize **o POST** e o seguinte URL, substituindo o SEU NOME DE SERVIÇO pelo nome real do seu serviço.

   ```http
   https://[YOUR-SERVICE-NAME].search.windows.net/datasources?api-version=2020-06-30
   ```

1. A pedido **Body**, copie a seguinte definição JSON, substituindo a `connectionString` com a ligação real da sua conta de armazenamento. 

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

Se obtiver um erro 403 ou 404, verifique a construção do pedido: `api-version=2020-06-30` deve estar no ponto final, `api-key` deve estar no Cabeçalho após `Content-Type` e o valor deve ser válido para um serviço de pesquisa. É melhor executar o documento JSON através de um validador JSON online para se certificar de que a sintaxe está correta. 

### <a name="step-2-create-a-skillset"></a>Passo 2: Criar um skillset

Um [objeto skillset](/rest/api/searchservice/create-skillset) é um conjunto de passos de enriquecimento aplicados ao seu conteúdo. 

1. Utilize **o PUT** e o seguinte URL, substituindo o SEU NOME DE SERVIÇO pelo nome real do seu serviço.

    ```http
    https://[YOUR-SERVICE-NAME].search.windows.net/skillsets/cog-search-demo-sd?api-version=2020-06-30
    ```

1. A pedido **Body**, copie a definição JSON abaixo. Esta habilidade consiste nas seguintes competências incorporadas.

   | Habilidade                 | Description    |
   |-----------------------|----------------|
   | [Reconhecimento de entidade](cognitive-search-skill-entity-recognition.md) | Extrai os nomes de pessoas, organizações e locais de conteúdo no recipiente blob. |
   | [Deteção de Idioma](cognitive-search-skill-language-detection.md) | Deteta a linguagem do conteúdo. |
   | [Divisão de Texto](cognitive-search-skill-textsplit.md)  | Quebra o conteúdo grande em pedaços menores antes de chamar a habilidade de extração da frase-chave. A extração de expressões-chave aceita entradas de 50 000 carateres ou menos. Alguns dos ficheiros de exemplo precisam de ser divididos para caberem dentro deste limite. |
   | [Extração de Expressões-Chave](cognitive-search-skill-keyphrases.md) | Tira as frases-chave de topo. |

   Cada competência é executada no conteúdo do documento. Durante o processamento, a Azure Cognitive Search quebra cada documento para ler conteúdo de diferentes formatos de ficheiros. O texto encontrado proveniente do ficheiro de origem é colocado num campo ```content``` gerado (um para cada documento). Como tal, a entrada torna-se ```"/document/content"``` .

   Para a extração de frases-chave, porque utilizamos a habilidade do separador de texto para dividir ficheiros maiores em páginas, o contexto para a habilidade de extração de frases-chave é ```"document/pages/*"``` (para cada página no documento) em vez de ```"/document/content"``` .

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

    ![Compreender um skillset](media/cognitive-search-tutorial-blob/skillset.png "Compreender um skillset")

1. Envie o pedido. O carteiro deve devolver um código de estado de 201 confirmando o sucesso. 

> [!NOTE]
> As saídas podem ser mapeadas para um índice, utilizadas como entradas para uma competência a jusante ou ambas, como é o caso do código de idioma. No índice, o código de idioma é útil para a filtragem. Como entrada, o código de idioma é utilizado pelas competências de análise de texto para informar sobre as regras linguísticas em torno da separação de palavras. Para obter mais informações acerca das noções básicas do conjunto de competências, veja [Como definir um conjunto de competências](cognitive-search-defining-skillset.md).

### <a name="step-3-create-an-index"></a>Passo 3: Criar um índice

Um [índice](/rest/api/searchservice/create-index) fornece o esquema usado para criar a expressão física do seu conteúdo em índices invertidos e outras construções em Azure Cognitive Search. O maior componente de um índice é a recolha de campos, onde o tipo de dados e os atributos determinam conteúdos e comportamentos na Pesquisa Cognitiva Azure.

1. Utilize **o PUT** e o seguinte URL, substituindo o SEU NOME DE SERVIÇO pelo nome real do seu serviço, para nomear o seu índice.

   ```http
   https://[YOUR-SERVICE-NAME].search.windows.net/indexes/cog-search-demo-idx?api-version=2020-06-30
   ```

1. A pedido **Body**, copiar a seguinte definição JSON. O `content` campo armazena o documento em si. Campos adicionais `languageCode` `keyPhrases` para, e `organizations` representam novas informações (campos e valores) criadas pelo skillset.

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

### <a name="step-4-create-and-run-an-indexer"></a>Passo 4: Criar e executar um indexador

Um [Indexer](/rest/api/searchservice/create-indexer) conduz o oleoduto. Os três componentes que criou até agora (fonte de dados, skillset, índice) são entradas para um indexante. Criar o indexante na Azure Cognitive Search é o evento que coloca todo o oleoduto em movimento. 

1. Utilize **o PUT** e o seguinte URL, substituindo o SEU NOME DE SERVIÇO pelo nome real do seu serviço, para nomear o seu indexante.

   ```http
   https://[servicename].search.windows.net/indexers/cog-search-demo-idxr?api-version=2020-06-30
   ```

1. A pedido **Body**, copie a definição JSON abaixo. Note os elementos de mapeamento de campo; estes mapeamentos são importantes porque definem o fluxo de dados. 

   Os `fieldMappings` são processados antes do skillset, enviando conteúdo da fonte de dados para campos-alvo num índice. Você usará mapeamentos de campo para enviar conteúdo existente e não modificado para o índice. Se os nomes e tipos de campo forem os mesmos em ambas as extremidades, não é necessário mapear.

   São `outputFieldMappings` para campos criados por competências, e assim processados após a corrida do skillset. As referências ao `sourceFieldNames` in `outputFieldMappings` não existem até que o documento de rachadura ou enriquecimento as crie. É `targetFieldName` um campo num índice, definido no esquema de índice.

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

#### <a name="about-indexer-parameters"></a>Sobre os parâmetros indexantes

O script define ```"maxFailedItems"``` como -1, o que indica ao motor de indexação para ignorar os erros durante a importação de dados. Isto é aceitável porque há tão poucos documentos na fonte de dados de demonstração. Para uma origem de dados maior, deve definir o valor com um número maior que 0.

A ```"dataToExtract":"contentAndMetadata"``` declaração diz ao indexante para extrair automaticamente o conteúdo de diferentes formatos de ficheiros, bem como metadados relacionados com cada ficheiro. 

Quando o conteúdo é extraído, pode definir ```imageAction``` para extrair texto das imagens existentes na origem de dados. A ```"imageAction":"generateNormalizedImages"``` configuração, combinada com a Habilidade de Fusão de OCR e A Capacidade de Fusão de Texto, diz ao indexante para extrair texto das imagens (por exemplo, a palavra "parar" de um sinal de stop de tráfego), e incorporá-lo como parte do campo de conteúdo. Este comportamento aplica-se tanto às imagens incorporadas nos documentos (tal como uma imagem num PDF) como às imagens existentes na origem de dados, por exemplo, um ficheiro JPG.

## <a name="4---monitor-indexing"></a>4 - Monitorização da indexação

A indexação e enriquecimento começam assim que submeter o pedido de 'Criar Indexer'. Dependendo das capacidades cognitivas que definiu, a indexação pode demorar um pouco. Para saber se o indexador ainda está em execução, envie o seguinte pedido para verificar o estado do indexador.

1. Utilize o **GET** e o seguinte URL, substituindo o SEU NOME DE SERVIÇO pelo nome real do seu serviço, para nomear o seu indexante.

   ```http
   https://[YOUR-SERVICE-NAME].search.windows.net/indexers/cog-search-demo-idxr/status?api-version=2020-06-30
   ```

1. Reveja a resposta para saber se o indexante está em funcionamento ou para ver informações de erro e aviso.  

Se estiver a utilizar o nível Free, espera-se a seguinte mensagem: "Não foi possível extrair conteúdo ou metadados do seu documento. Texto truncado extraído para caracteres '32768'". Esta mensagem aparece porque a indexação de blob no nível Livre tem um[limite de 32K na extração de caracteres](search-limits-quotas-capacity.md#indexer-limits). Não verá esta mensagem para este conjunto de dados em níveis mais altos. 

> [!NOTE]
> Os avisos são comuns em alguns cenários e nem sempre indicam um problema. Por exemplo, se um recipiente de bolhas incluir ficheiros de imagem e o pipeline não manusear imagens, receberá um aviso indicando que as imagens não foram processadas.

## <a name="5---search"></a>5 - Pesquisar

Agora que criou novos campos e informações, vamos fazer algumas perguntas para entender o valor da pesquisa cognitiva, uma vez que se relaciona com um cenário típico de pesquisa.

Lembre-se que começamos com conteúdo blob, onde todo o documento é embalado em um único `content` campo. Pode pesquisar neste campo e encontrar correspondências para as suas consultas.

1. Utilize o **GET** e o seguinte URL, substituindo o SEU NOME DE SERVIÇO pelo nome real do seu serviço, para procurar casos de um termo ou frase, devolvendo o `content` campo e uma contagem dos documentos correspondentes.

   ```http
   https://[YOUR-SERVICE-NAME].search.windows.net/indexes/cog-search-demo-idx/docs?search=*&$count=true&$select=content&api-version=2020-06-30
   ```
   
   Os resultados deste conteúdo do documento de devolução de consultas, que é o mesmo resultado que obteria se usasse o indexante blob sem o pipeline de pesquisa cognitiva. Este campo é pesquisável, mas inviável se quiser utilizar facetas, filtros ou precontos automáticos.

   ![Saída de campo de conteúdo](media/cognitive-search-tutorial-blob/content-output.png "Saída de campo de conteúdo")
   
1. Para a segunda consulta, devolva alguns dos novos campos criados pelo oleoduto (pessoas, organizações, localizações, languageCode). Estamos a omitir as palavras-chave para a brevidade, mas deve incluí-la se quiser ver esses valores.

   ```http
   https://[YOUR-SERVICE-NAME].search.windows.net/indexes/cog-search-demo-idx/docs?search=*&$count=true&$select=metadata_storage_name,persons,organizations,locations,languageCode&api-version=2020-06-30
   ```
   Os campos da declaração $select contêm novas informações criadas a partir das capacidades naturais de processamento de linguagem dos Serviços Cognitivos. Como seria de esperar, há algum ruído nos resultados e variações entre documentos, mas em muitos casos, os modelos analíticos produzem resultados precisos.

   A imagem que se segue mostra os resultados da carta aberta de Satya Nadella ao assumir o papel de CEO na Microsoft.

   ![Saída do gasoduto](media/cognitive-search-tutorial-blob/pipeline-output.png "Saída do gasoduto")

1. Para ver como pode tirar partido destes campos, adicione um parâmetro faceta para devolver uma agregação de documentos correspondentes por localização.

   ```http
   https://[YOUR-SERVICE-NAME].search.windows.net/indexes/cog-search-demo-idx/docs?search=*&facet=locations&api-version=2020-06-30
   ``` 

   Neste exemplo, para cada local, existem 2 ou 3 partidas.

   ![Saída faceta](media/cognitive-search-tutorial-blob/facet-output.png "Saída faceta")
   

1. Neste exemplo final, aplique um filtro na recolha das organizações, devolvendo dois fósforos para critérios de filtro baseados no NASDAQ.

   ```http
   https://[YOUR-SERVICE-NAME].search.windows.net/indexes/cog-search-demo-idx/docs?search=*&$filter=organizations/any(organizations: organizations eq 'NASDAQ')&$select=metadata_storage_name,organizations&$count=true&api-version=2020-06-30
   ```

Estas consultas ilustram algumas das formas de trabalhar com sintaxe de consulta e filtros em novos campos criados pela pesquisa cognitiva. Para obter mais exemplos de consulta, consulte [Exemplos em Documentos de Busca REST API,](/rest/api/searchservice/search-documents#bkmk_examples) [exemplos simples de consulta de sintaxe](search-query-simple-examples.md)e [exemplos de consulta de Lucene Completo.](search-query-lucene-examples.md)

<a name="reset"></a>

## <a name="reset-and-rerun"></a>Repor e executar novamente

Nas fases experimentais iniciais de desenvolvimento, a abordagem mais prática para a iteração de design é eliminar os objetos da Azure Cognitive Search e permitir que o seu código os reconstrua. Os nomes dos recursos são exclusivos. Quando elimina um objeto, pode recriá-lo com o mesmo nome.

Pode utilizar o portal para eliminar índices, indexadores, fontes de dados e skillsets. Quando elimina o indexador, pode, opcionalmente, eliminar seletivamente o índice, o skillset e a fonte de dados ao mesmo tempo.

![Eliminar objetos de pesquisa](./media/cognitive-search-tutorial-blob-python/py-delete-indexer-delete-all.png "Eliminar objetos de pesquisa no portal")

Ou use **DELETE** e forneça URLs a cada objeto. O seguinte comando elimina um indexante.

```http
DELETE https://[YOUR-SERVICE-NAME].search.windows.net/indexers/cog-search-demo-idxr?api-version=2020-06-30
```

O código de estado 204 é devolvido após uma eliminação com êxito.

## <a name="takeaways"></a>Conclusões

Este tutorial demonstra os passos básicos para criar um pipeline de indexação melhorado através da criação de partes do componente: uma origem de dados, um conjunto de competências, um índice e um indexador.

[Foram introduzidas competências incorporadas,](cognitive-search-predefined-skills.md) juntamente com a definição de skillset e a mecânica das habilidades de acorrentamento em conjunto através de entradas e saídas. Também aprendeu que `outputFieldMappings` na definição do indexante é necessário encaminhar valores enriquecidos do pipeline para um índice pesmável num serviço de Pesquisa Cognitiva Azure.

Por fim, aprendeu como testar os resultados e repor o sistema para iterações futuras. Aprendeu que a emissão de consultas acerca do índice devolve o resultado criado pelo pipeline de indexação melhorado. 

## <a name="clean-up-resources"></a>Limpar os recursos

Quando se está a trabalhar na sua própria subscrição, no final de um projeto, é uma boa ideia remover os recursos de que já não precisa. Os recursos que deixar em execução podem custar-lhe dinheiro. Pode eliminar recursos individualmente ou eliminar o grupo de recursos para eliminar todo o conjunto de recursos.

Pode encontrar e gerir recursos no portal, utilizando a ligação de todos os recursos ou grupos de recursos no painel de navegação à esquerda.

## <a name="next-steps"></a>Passos seguintes

Agora que está familiarizado com todos os objetos num oleoduto de enriquecimento de IA, vamos olhar mais de perto as definições de skillset e habilidades individuais.

> [!div class="nextstepaction"]
> [Como criar um skillset](cognitive-search-defining-skillset.md)
