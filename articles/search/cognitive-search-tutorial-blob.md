---
title: 'Tutorial de REST: Criar um pipeline de enriquecimento de ia usando a pesquisa cognitiva-Azure Search'
description: Percorra um exemplo de extração de texto e processamento de idioma natural sobre o conteúdo em BLOBs JSON usando o postmaster e as APIs REST do Azure Search.
manager: pablocas
author: luiscabrer
services: search
ms.service: search
ms.topic: tutorial
ms.date: 08/23/2019
ms.author: luisca
ms.subservice: cognitive-search
ms.openlocfilehash: e647d3c66d339a60278fa7d0f078497157b3fff1
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70102754"
---
# <a name="tutorial-add-structure-to-unstructured-content-with-cognitive-search"></a>Tutorial: Adicionar estrutura a "conteúdo não estruturado" com pesquisa cognitiva

Se você tiver conteúdo de imagem ou texto não estruturado, o recurso de [pesquisa cognitiva](cognitive-search-concept-intro.md) do Azure Search poderá ajudá-lo a extrair informações e criar um novo conteúdo útil para cenários de pesquisa de texto completo ou de mineração de conhecimento. Embora a pesquisa cognitiva possa processar arquivos de imagem (JPG, PNG, TIFF), este tutorial concentra-se no conteúdo baseado em palavras, aplicando detecção de idioma e análise de texto para criar novos campos e informações que você pode aproveitar em consultas, facetas e filtros.

> [!div class="checklist"]
> * Comece com documentos inteiros (texto não estruturado), como PDF, MD, DOCX e PPTX no armazenamento de BLOBs do Azure.
> * Crie um pipeline que extrai texto, detecta o idioma, reconhece entidades e detecta frases-chave.
> * Defina um índice para armazenar a saída (conteúdo bruto, além de pares de nome-valor gerados pelo pipeline).
> * Execute o pipeline para criar e carregar o índice.
> * Explore o conteúdo usando a pesquisa de texto completo e uma sintaxe de consulta avançada.

Você precisará de vários serviços para concluir este passo a passos, além do [aplicativo de área de trabalho do postmaster](https://www.getpostman.com/) ou de outra ferramenta de teste da Web para fazer chamadas à API REST. 

Se você não tiver uma assinatura do Azure, abra uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="download-files"></a>Transferir ficheiros

1. Abra esta [pasta do onedrive](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4) e, no canto superior esquerdo, clique em **baixar** para copiar os arquivos para o computador. 

1. Clique com o botão direito do mouse no arquivo zip e selecione **extrair tudo**. Há 14 arquivos de vários tipos. Você usará 7 para este exercício.

## <a name="1---create-services"></a>1-criar serviços

Este passo a passos usa Azure Search para indexação e consultas, serviços cognitivas para enriquecimento de ia e armazenamento de BLOBs do Azure para fornecer os dados. Se possível, crie todos os três serviços na mesma região e grupo de recursos para proximidade e capacidade de gerenciamento. Na prática, sua conta de armazenamento do Azure pode estar em qualquer região.

### <a name="start-with-azure-storage"></a>Comece com o armazenamento do Azure

1. [Entre no portal do Azure](https://portal.azure.com/) e clique em **+ criar recurso**.

1. Pesquise *conta de armazenamento* e selecione oferta de conta de armazenamento da Microsoft.

   ![Criar conta de armazenamento](media/cognitive-search-tutorial-blob/storage-account.png "Criar conta de armazenamento")

1. Na guia básico, os itens a seguir são necessários. Aceite os padrões para todo o resto.

   + **Grupo de recursos**. Selecione um existente ou crie um novo, mas use o mesmo grupo para todos os serviços para que você possa gerenciá-los coletivamente.

   + **Nome da conta de armazenamento**. Se você acredita que pode ter vários recursos do mesmo tipo, use o nome para eliminar a ambiguidade por tipo e região, por exemplo, *blobstoragewestus*. 

   + **Localização**. Se possível, escolha o mesmo local usado para Azure Search e serviços cognitivas. Um único local anula os encargos de largura de banda.

   + **Tipo de conta**. Escolha o padrão *StorageV2 (uso geral v2)* .

1. Clique em **examinar + criar** para criar o serviço.

1. Após a criação, clique em **ir para o recurso** para abrir a página Visão geral.

1. Clique em serviço BLOBs.

1. Clique em **+ contêiner** para criar um contêiner e nomeie-o *engrenagem-Search-demo*.

1. Selecione *engrenagem-Search-demo* e clique em **carregar** para abrir a pasta em que você salvou os arquivos de download. Selecione todos os arquivos que não sejam de imagem. Você deve ter 7 arquivos. Clique em **OK** para carregar.

   ![Carregar arquivos de exemplo](media/cognitive-search-tutorial-blob/sample-files.png "Carregar arquivos de exemplo")

1. Antes de sair do armazenamento do Azure, obtenha uma cadeia de conexão para que você possa formular uma conexão no Azure Search. 

   1. Navegue de volta para a página de visão geral da sua conta de armazenamento (usamos *blobstragewestus* como exemplo). 
   
   1. No painel de navegação à esquerda, selecione **chaves de acesso** e copie uma das cadeias de conexão. 

   A cadeia de conexão é uma URL semelhante ao exemplo a seguir:

      ```http
      DefaultEndpointsProtocol=https;AccountName=cogsrchdemostorage;AccountKey=<your account key>;EndpointSuffix=core.windows.net
      ```

1. Salve a cadeia de conexão no bloco de notas. Você precisará dela mais tarde ao configurar a conexão de fonte de dados.

### <a name="cognitive-services"></a>Serviços Cognitivos

O enriquecimento de ia na pesquisa cognitiva é apoiado por serviços cognitivas, incluindo Análise de Texto e Pesquisa Visual Computacional para processamento de imagem e linguagem natural. Se seu objetivo era concluir um protótipo ou projeto real, você poderia, neste ponto, provisionar serviços cognitivas (na mesma região que Azure Search) para que você possa anexá-lo às operações de indexação.

Para este exercício, no entanto, você pode ignorar o provisionamento de recursos porque Azure Search pode se conectar aos serviços cognitivas nos bastidores e fornecer a você 20 transações gratuitas por execução de indexador. Como este tutorial usa 7 transações, a alocação gratuita é suficiente. Para projetos maiores, planeje o provisionamento de serviços cognitivas na camada de S0 pago conforme o uso. Para obter mais informações, consulte [Attach cognitiva Services](cognitive-search-attach-cognitive-services.md).

### <a name="azure-search"></a>Azure Search

O terceiro componente é Azure Search, que você pode [criar no portal](search-create-service-portal.md). Você pode usar a camada gratuita para concluir este passo a passos. 

Assim como no armazenamento de BLOBs do Azure, Reserve um momento para coletar a chave de acesso. Além disso, quando você começar a estruturar solicitações, será necessário fornecer o ponto de extremidade e a chave de API de administração usados para autenticar cada solicitação.

### <a name="get-an-admin-api-key-and-url-for-azure-search"></a>Obter uma URL e uma chave de API de administrador para Azure Search

1. [Entre no portal do Azure](https://portal.azure.com/)e, em sua página de **visão geral** do serviço de pesquisa, obtenha o nome do serviço de pesquisa. Você pode confirmar o nome do serviço examinando a URL do ponto de extremidade. Se a URL do ponto `https://mydemo.search.windows.net`de extremidade for, o nome `mydemo`do serviço será.

2. Em **configurações** > **chaves**, obtenha uma chave de administração para obter direitos totais sobre o serviço. Há duas chaves de administração intercambiáveis, fornecidas para a continuidade dos negócios, caso você precise fazer uma sobreposição. Você pode usar a chave primária ou secundária em solicitações para adicionar, modificar e excluir objetos.

    Obtenha também a chave de consulta. É uma prática recomendada emitir solicitações de consulta com acesso somente leitura.

![Obter o nome do serviço e as chaves de consulta e de administrador](media/search-get-started-nodejs/service-name-and-keys.png)

Todas as solicitações exigem uma chave de API no cabeçalho de cada solicitação enviada ao seu serviço. Uma chave válida estabelece confiança, por solicitação, entre o aplicativo que envia a solicitação e o serviço que a manipula.

## <a name="2---set-up-postman"></a>2-configurar o postmaster

Inicie o Postman e configure um pedido de HTTP. Se você não estiver familiarizado com essa ferramenta, consulte [explorar Azure Search APIs REST usando o postmaster](search-get-started-postman.md).

Os métodos de solicitação usados neste tutorial são **post**, **Put**e **Get**. Você usará os métodos para fazer quatro chamadas à API para o serviço de pesquisa: Crie uma fonte de dados, um configurador, um índice e um indexador.

Em cabeçalhos, defina "Content-Type" como `application/json` e defina `api-key` como a chave de API de administração do seu serviço de Azure Search. Depois de definir os cabeçalhos, você poderá usá-los para cada solicitação neste exercício.

  ![URL e cabeçalho da solicitação do postmaster](media/search-get-started-postman/postman-url.png "URL e cabeçalho da solicitação do postmaster")

## <a name="3---create-the-pipeline"></a>3-criar o pipeline

No Azure Search, o processamento de ia ocorre durante a indexação (ou ingestão de dados). Esta parte do passo a passos cria quatro objetos: fonte de dados, definição de índice, qualificar, indexador. 

### <a name="step-1-create-a-data-source"></a>Passo 1: Criar uma origem de dados

Um [objeto de fonte de dados](https://docs.microsoft.com/rest/api/searchservice/create-data-source) fornece a cadeia de conexão para o contêiner de BLOB que contém os arquivos.

1. Use **post** e a URL a seguir, substituindo o-Service-Name pelo nome real do seu serviço.

   ```http
   https://[YOUR-SERVICE-NAME].search.windows.net/datasources?api-version=2019-05-06
   ```

1. Em **corpo**da solicitação, copie a seguinte definição de JSON, `connectionString` substituindo pela conexão real da sua conta de armazenamento. 

   Lembre-se de editar o nome do contêiner também. Sugerimos "engrenagem-Search-demo" para o nome do contêiner em uma etapa anterior.

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
1. Envie o pedido. Você deverá ver um código de status de 201 confirmando o sucesso. 

Se obtiver um erro 403 ou 404, verifique a construção do pedido: `api-version=2019-05-06` deve estar no ponto final, `api-key` deve estar no Cabeçalho após `Content-Type` e o valor deve ser válido para um serviço de pesquisa. Talvez você queira executar o documento JSON por meio de um validador JSON online para verificar se a sintaxe está correta. 

### <a name="step-2-create-a-skillset"></a>Passo 2: Criar um conjunto de competências

Um [objeto skillset](https://docs.microsoft.com/rest/api/searchservice/create-skillset) é um conjunto de etapas de enriquecimento aplicado ao seu conteúdo. 

1. Use **Put** e a URL a seguir, substituindo o-Service-Name pelo nome real do seu serviço.

    ```http
    https://[YOUR-SERVICE-NAME].search.windows.net/skillsets/cog-search-demo-ss?api-version=2019-05-06
    ```

1. Em **corpo**da solicitação, copie a definição de JSON abaixo. Este contratador de qualificações consiste nas seguintes habilidades internas.

   | Técnico                 | Descrição    |
   |-----------------------|----------------|
   | [Reconhecimento de entidade](cognitive-search-skill-entity-recognition.md) | Extrai os nomes de pessoas, organizações e locais do conteúdo no contêiner de BLOBs. |
   | [Deteção de idioma](cognitive-search-skill-language-detection.md) | Detecta o idioma do conteúdo. |
   | [Divisão de texto](cognitive-search-skill-textsplit.md)  | Quebra o conteúdo grande em partes menores antes de chamar a habilidade de extração de frases-chave. A extração de expressões-chave aceita entradas de 50 000 carateres ou menos. Alguns dos ficheiros de exemplo precisam de ser divididos para caberem dentro deste limite. |
   | [Extração de expressões-chave](cognitive-search-skill-keyphrases.md) | Extrai as principais frases-chave. |

   Cada competência é executada no conteúdo do documento. Durante o processamento, o Azure Search abre cada documento para ler o conteúdo de diferentes formatos de ficheiros. O texto encontrado proveniente do ficheiro de origem é colocado num campo ```content``` gerado (um para cada documento). Como tal, a entrada se ```"/document/content"```torna.

   Para extração de frases-chave, como usamos a habilidade de divisor de texto para dividir arquivos maiores em páginas, o contexto para a habilidade ```"document/pages/*"``` de extração de frases-chave é (para ```"/document/content"```cada página no documento) em vez de.

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

    ![Compreender um conjunto de competências](media/cognitive-search-tutorial-blob/skillset.png "Compreender um conjunto de competências")

1. Envie o pedido. O postmaster deve retornar um código de status de 201 confirmando o sucesso. 

> [!NOTE]
> As saídas podem ser mapeadas para um índice, utilizadas como entradas para uma competência a jusante ou ambas, como é o caso do código de idioma. No índice, o código de idioma é útil para a filtragem. Como entrada, o código de idioma é utilizado pelas competências de análise de texto para informar sobre as regras linguísticas em torno da separação de palavras. Para obter mais informações acerca das noções básicas do conjunto de competências, veja [Como definir um conjunto de competências](cognitive-search-defining-skillset.md).

### <a name="step-3-create-an-index"></a>Passo 3: Criar um índice

Um [índice](https://docs.microsoft.com/rest/api/searchservice/create-index) fornece o esquema usado para criar a expressão física do seu conteúdo em índices invertidos e outras construções no Azure Search. O maior componente de um índice é a coleção Fields, em que o tipo de dados e os atributos determinam o conteúdo e os comportamentos no Azure Search.

1. Use **Put** e a URL a seguir, substituindo o-Service-Name pelo nome real do seu serviço, para nomear o índice.

   ```http
   https://[YOUR-SERVICE-NAME].search.windows.net/indexes/cog-search-demo-idx?api-version=2019-05-06
   ```

1. Em **corpo**da solicitação, copie a definição de JSON a seguir. O `content` campo armazena o documento em si. Campos adicionais para `languageCode`, `keyPhrases`e `organizations` representam novas informações (campos e valores) criados pelo configurador de habilidades.

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

1. Envie o pedido. O postmaster deve retornar um código de status de 201 confirmando o sucesso. 

### <a name="step-4-create-and-run-an-indexer"></a>Passo 4: Criar e executar um indexador

Um [indexador](https://docs.microsoft.com/rest/api/searchservice/create-indexer) orienta o pipeline. Os três componentes que você criou até o momento (fonte de dados, Configurador de habilidades, índice) são entradas para um indexador. Criar o indexador em Azure Search é o evento que coloca todo o pipeline em movimento. 

1. Use **Put** e a URL a seguir, substituindo o-Service-Name pelo nome real do seu serviço, para nomear o indexador.

   ```http
   https://[servicename].search.windows.net/indexers/cog-search-demo-idxr?api-version=2019-05-06
   ```

1. Em **corpo**da solicitação, copie a definição de JSON abaixo. Observe os elementos de mapeamento de campo; Esses mapeamentos são importantes porque definem o fluxo de dados. 

   Os `fieldMappings` são processados antes do Configurador de habilidades, enviando conteúdo da fonte de dados para campos de destino em um índice. Você usará mapeamentos de campo para enviar conteúdo existente e não modificado para o índice. Se os nomes e tipos de campo forem os mesmos em ambas as extremidades, nenhum mapeamento será necessário.

   O `outputFieldMappings` são para campos criados por habilidades e, portanto, processados após o configurador de qualificações ser executado. As referências a `sourceFieldNames` `outputFieldMappings` não existem até que o documento seja desprovido ou o enriquecimento as crie. O `targetFieldName` é um campo em um índice, definido no esquema de índice.

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

1. Envie o pedido. O postmaster deve retornar um código de status 201 confirmando o processamento bem-sucedido. 

   A conclusão deste passo demora vários minutos. Apesar de o conjunto de dados ser pequeno, as competências analíticas realizam um processo de computação intensa. 

> [!NOTE]
> A criação de um indexador invoca o pipeline. Se houver problemas em atingir os dados, com o mapeamento de entradas e saídas ou com a ordem das operações, estes vão surgir nesta fase. Para executar novamente o pipeline com as alterações de código ou script, pode ter de remover primeiro os objetos. Para obter mais informações, veja [Repor e executar novamente](#reset).

#### <a name="about-indexer-parameters"></a>Sobre os parâmetros do indexador

O script define ```"maxFailedItems"``` como -1, o que indica ao motor de indexação para ignorar os erros durante a importação de dados. Isso é aceitável porque há poucos documentos na fonte de dados de demonstração. Para uma origem de dados maior, deve definir o valor com um número maior que 0.

A ```"dataToExtract":"contentAndMetadata"``` instrução informa o indexador para extrair automaticamente o conteúdo de diferentes formatos de arquivo, bem como os metadados relacionados a cada arquivo. 

Quando o conteúdo é extraído, pode definir ```imageAction``` para extrair texto das imagens existentes na origem de dados. A ```"imageAction":"generateNormalizedImages"``` configuração, combinada com a habilidade de OCR e a habilidade de mesclagem de texto, diz ao indexador para extrair texto das imagens (por exemplo, a palavra "Stop" de um sinal de parada de tráfego) e inseri-la como parte do campo de conteúdo. Este comportamento aplica-se tanto às imagens incorporadas nos documentos (tal como uma imagem num PDF) como às imagens existentes na origem de dados, por exemplo, um ficheiro JPG.

## <a name="4---monitor-indexing"></a>4-indexação de monitor

A indexação e o enriquecimento são iniciados assim que você envia a solicitação criar indexador. Dependendo de quais habilidades cognitivas você definiu, a indexação pode levar algum tempo. Para saber se o indexador ainda está em execução, envie o seguinte pedido para verificar o estado do indexador.

1. Use **Get** e a URL a seguir, substituindo o-Service-Name pelo nome real do seu serviço, para nomear o indexador.

   ```http
   https://[YOUR-SERVICE-NAME].search.windows.net/indexers/cog-search-demo-idxr/status?api-version=2019-05-06
   ```

1. Examine a resposta para saber se o indexador está em execução ou para exibir informações de erro e aviso.  

Se você estiver usando a camada gratuita, a seguinte mensagem será esperada: ' "não foi possível extrair o conteúdo ou os metadados do documento. Texto extraído truncado para ' 32768 ' caracteres ". Essa mensagem aparece porque a indexação de blob na camada gratuita tem um[limite de 32K na extração de caracteres](search-limits-quotas-capacity.md#indexer-limits). Você não verá essa mensagem para esse conjunto de dados em camadas superiores. 

> [!NOTE]
> Os avisos são comuns em alguns cenários e nem sempre indicam um problema. Por exemplo, se um contêiner de blob incluir arquivos de imagem e o pipeline não tratar imagens, você receberá um aviso informando que as imagens não foram processadas.

## <a name="5---search"></a>5-Pesquisar

Agora que você criou novos campos e informações, vamos executar algumas consultas para entender o valor da pesquisa cognitiva, pois ela se relaciona a um cenário de pesquisa típico.

Lembre-se de que começamos com conteúdo de BLOB, onde todo o documento é empacotado `content` em um único campo. Você pode pesquisar este campo e encontrar correspondências às suas consultas.

1. Use **Get** e a URL a seguir, substituindo seu-Service-Name pelo nome real do seu serviço, para procurar instâncias de um termo ou frase, retornando `content` o campo e uma contagem dos documentos correspondentes.

   ```http
   https://[YOUR-SERVICE-NAME].search.windows.net/indexes/cog-search-demo-idx?search=*&$count=true&$select=content?api-version=2019-05-06
   ```
   
   Os resultados dessa consulta retornam o conteúdo do documento, que é o mesmo resultado que você obteria se usava o indexador de blob sem o pipeline de pesquisa cognitiva. Esse campo é pesquisável, mas não funcionará se você quiser usar facetas, filtros ou preenchimento automático.

   ![Saída do campo de conteúdo](media/cognitive-search-tutorial-blob/content-output.png "Saída do campo de conteúdo")
   
1. Para a segunda consulta, retorne alguns dos novos campos criados pelo pipeline (pessoas, organizações, locais, languageCode). Estamos omitindo as keyfrases para fins de brevidade, mas você deve incluí-las se quiser ver esses valores.

   ```http
   https://mydemo.search.windows.net/indexes/cog-search-demo-idx/docs?search=*&$count=true&$select=metadata_storage_name,persons,organizations,locations,languageCode&api-version=2019-05-06
   ```
   Os campos na instrução $select contêm novas informações criadas com base nos recursos de processamento de idioma natural dos serviços cognitivas. Como você pode esperar, há algum ruído nos resultados e na variação entre documentos, mas em muitas instâncias, os modelos analíticos produzem resultados precisos.

   A imagem a seguir mostra os resultados da letra aberta do Satya Nadella, supondo a função de CEO na Microsoft.

   ![Saída de pipeline](media/cognitive-search-tutorial-blob/pipeline-output.png "Saída de pipeline")

1. Para ver como você pode tirar proveito desses campos, adicione um parâmetro de faceta para retornar uma agregação de documentos correspondentes por local.

   ```http
   https://[YOUR-SERVICE-NAME].search.windows.net/indexes/cog-search-demo-idx/docs?search=*&facet=locations&api-version=2019-05-06
   ``` 

   Neste exemplo, para cada local, há 2 ou 3 correspondências.

   ![Saída da faceta](media/cognitive-search-tutorial-blob/facet-output.png "Saída da faceta")
   

1. Neste exemplo final, aplique um filtro na coleção de organizações, retornando duas correspondências para critérios de filtro com base na NASDAQ.

   ```http
   cog-search-demo-idx/docs?search=*&$filter=organizations/any(organizations: organizations eq 'NASDAQ')&$select=metadata_storage_name,organizations&$count=true&api-version=2019-05-06
   ```

Essas consultas ilustram algumas das maneiras pelas quais você pode trabalhar com a sintaxe de consulta e os filtros em novos campos criados pela pesquisa cognitiva. Para obter mais exemplos de consulta, consulte [exemplos em Pesquisar API REST](https://docs.microsoft.com/rest/api/searchservice/search-documents#bkmk_examples), [exemplos de consulta de sintaxe simples](search-query-simple-examples.md)e [exemplos de consulta Lucene completos](search-query-lucene-examples.md).

<a name="reset"></a>

## <a name="reset-and-rerun"></a>Repor e executar novamente

Nas fases experimentais iniciais de desenvolvimento do pipeline, a abordagem mais prática para as iterações de design consiste em eliminar os objetos do Azure Search e permitir que o seu código os reconstrua. Os nomes dos recursos são exclusivos. Quando elimina um objeto, pode recriá-lo com o mesmo nome.

Para voltar a indexar os documentos com as novas definições:

1. Exclua o indexador, o índice e o skillset.
2. Modificar objetos.
3. Recrie em seu serviço para executar o pipeline. 

Você pode usar o portal para excluir índices, indexadores e habilidades, ou usar **delete** e fornecer URLs para cada objeto. O comando a seguir exclui um indexador.

```http
DELETE https://[YOUR-SERVICE-NAME]].search.windows.net/indexers/cog-search-demo-idxr?api-version=2019-05-06
```

O código de estado 204 é devolvido após uma eliminação com êxito.

À medida que o seu código evoluiu, pode querer refinar uma estratégia de reconstrução. Para obter mais informações, veja [Como reconstruir um índice](search-howto-reindex.md).

## <a name="takeaways"></a>Conclusões

Este tutorial demonstra os passos básicos para criar um pipeline de indexação melhorado através da criação de partes do componente: uma origem de dados, um conjunto de competências, um índice e um indexador.

As [competências predefinidas](cognitive-search-predefined-skills.md) foram introduzidas, juntamente com a definição do conjunto de competências e os mecanismos de encadeamento de competências, através de entradas e saídas. Também aprendeu que precisa de `outputFieldMappings` na definição do indexador para encaminhar valores melhorados do pipeline para um índice pesquisável num serviço do Azure Search.

Por fim, aprendeu como testar os resultados e repor o sistema para iterações futuras. Aprendeu que a emissão de consultas acerca do índice devolve o resultado criado pelo pipeline de indexação melhorado. 

## <a name="clean-up-resources"></a>Limpar recursos

A forma mais rápida de os limpar no final do tutorial passa por eliminar o grupo de recursos que contém o serviço do Azure Search e o serviço Blob do Azure. Assumindo que coloca ambos os serviços no mesmo grupo, elimine o grupo de recursos agora para eliminar definitivamente todo o seu conteúdo, incluindo os serviços e quaisquer conteúdos armazenados criados para este tutorial. No portal, o nome do grupo de recursos está na página Descrição geral de cada serviço.

## <a name="next-steps"></a>Passos Seguintes

Personalize ou expanda o pipeline com competências personalizadas. A criação de uma competência personalizada e a sua adição a um conjunto de competências permite-lhe carregar análises de texto ou imagem que escreveu. 

> [!div class="nextstepaction"]
> [Exemplo: Criando uma habilidade personalizada para pesquisa cognitiva](cognitive-search-create-custom-skill-example.md)