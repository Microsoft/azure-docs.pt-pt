---
title: 'Início rápido: Criar um índice de pesquisa em Java usando APIs REST-Azure Search'
description: Explica como criar um índice, carregar dados e executar consultas usando Java e as APIs REST do Azure Search.
author: lisaleib
manager: nitinme
ms.author: jjed
tags: azure-portal
services: search
ms.service: search
ms.custom: seodec2018, seo-java-july2019, seo-java-august2019
ms.devlang: java
ms.topic: quickstart
ms.date: 09/10/2019
ms.openlocfilehash: 455f3dfdce93d0b39960f9ec87b0938060f87687
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70881568"
---
# <a name="quickstart-create-an-azure-search-index-in-java-using-rest-apis"></a>Início rápido: Criar um índice de Azure Search em Java usando APIs REST
> [!div class="op_single_selector"]
> * [JavaScript](search-get-started-nodejs.md)
> * [C#](search-get-started-dotnet.md)
> * [Java](search-get-started-java.md)
> * [Portal](search-get-started-portal.md)
> * [PowerShell](search-create-index-rest-api.md)
> * [Python](search-get-started-python.md)
> * [Postman](search-get-started-postman.md)

Crie um aplicativo de console Java que cria, carrega e consulta um índice do Azure Search usando [IntelliJ](https://www.jetbrains.com/idea/), [SDK do Java 11](/java/azure/jdk/?view=azure-java-stable)e a [API REST do serviço de Azure Search](/rest/api/searchservice/). Este artigo fornece instruções passo a passo para a criação do aplicativo. Como alternativa, você pode [baixar e executar o aplicativo completo](/samples/azure-samples/azure-search-java-samples/java-sample-quickstart/).

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Usamos o software e os serviços a seguir para criar e testar este exemplo:

+ [IDEIA IntelliJ](https://www.jetbrains.com/idea/)

+ [SDK do Java 11](/java/azure/jdk/?view=azure-java-stable)

+ [Crie um serviço de Azure Search](search-create-service-portal.md) ou [Localize um serviço existente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) em sua assinatura atual. Você pode usar um serviço gratuito para este guia de início rápido.

<a name="get-service-info"></a>

## <a name="get-a-key-and-url"></a>Obter uma chave e uma URL

Chamadas para o serviço exigem um ponto de extremidade de URL e uma chave de acesso em cada solicitação. É criado um serviço de pesquisa com ambos os elementos, pelo que, se tiver adicionado o Azure Search à sua subscrição, siga estes passos para obter as informações necessárias:

1. [Entre no portal do Azure](https://portal.azure.com/)e, em sua página de **visão geral** do serviço de pesquisa, obtenha a URL. Um ponto final de exemplo poderá ser parecido com `https://mydemo.search.windows.net`.

2. Em **configurações** > **chaves**, obtenha uma chave de administração para obter direitos totais sobre o serviço. Há duas chaves de administração intercambiáveis, fornecidas para a continuidade dos negócios, caso você precise fazer uma sobreposição. Você pode usar a chave primária ou secundária em solicitações para adicionar, modificar e excluir objetos.

   Crie uma chave de consulta também. É uma prática recomendada emitir solicitações de consulta com acesso somente leitura.

![Obter o nome do serviço e as chaves de consulta e de administrador](media/search-get-started-nodejs/service-name-and-keys.png)

Cada solicitação enviada ao serviço requer uma chave de API. Ter uma chave válida estabelece fidedignidade, numa base por pedido, entre a aplicação a enviar o pedido e o serviço que o processa.

## <a name="set-up-your-environment"></a>Configurar o ambiente

Comece abrindo IntelliJ IDEA e configurando um novo projeto.

### <a name="create-the-project"></a>Criar o projeto

1. Abra IntelliJ IDEA e selecione **criar novo projeto**.
1. Selecione **Maven**.
1. Na lista **SDK do projeto** , selecione o SDK do Java 11.

    ![Criar um projeto Maven](media/search-get-started-java/java-quickstart-create-new-maven-project.png) 

1. Para **GroupId** e **artefatoid**, insira `AzureSearchQuickstart`.
1. Aceite os padrões restantes para abrir o projeto.

### <a name="specify-maven-dependencies"></a>Especificar dependências do Maven

1. Selecione**configurações**de **arquivo** > .
1. Na janela **configurações** , selecione **compilação, execução, implantação** > **ferramentas** > de compilação**Maven** > **importando**.
1. Marque a caixa de seleção **Importar projetos Maven automaticamente** e clique em **OK** para fechar a janela. Os plug-ins do Maven e outras dependências agora serão sincronizados automaticamente quando você atualizar o arquivo pom. xml na próxima etapa.

    ![Opções de importação do Maven nas configurações do IntelliJ](media/search-get-started-java/java-quickstart-settings-import-maven-auto.png)

1. Abra o arquivo pom. xml e substitua o conteúdo pelos seguintes detalhes de configuração do Maven. Isso inclui referências ao [plug-in exec Maven](https://www.mojohaus.org/exec-maven-plugin/) e a uma [API de interface JSON](https://javadoc.io/doc/org.glassfish/javax.json/1.0.2)

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <project xmlns="http://maven.apache.org/POM/4.0.0"
             xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
             xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
        <modelVersion>4.0.0</modelVersion>
    
        <groupId>AzureSearchQuickstart</groupId>
        <artifactId>AzureSearchQuickstart</artifactId>
        <version>1.0-SNAPSHOT</version>
        <build>
            <sourceDirectory>src</sourceDirectory>
            <plugins>
                <plugin>
                    <artifactId>maven-compiler-plugin</artifactId>
                    <version>3.1</version>
                    <configuration>
                        <source>11</source>
                        <target>11</target>
                    </configuration>
                </plugin>
                <plugin>
                    <groupId>org.codehaus.mojo</groupId>
                    <artifactId>exec-maven-plugin</artifactId>
                    <version>1.6.0</version>
                    <executions>
                        <execution>
                            <goals>
                                <goal>exec</goal>
                            </goals>
                        </execution>
                    </executions>
                    <configuration>
                        <mainClass>main.java.app.App</mainClass>
                        <cleanupDaemonThreads>false</cleanupDaemonThreads>
                    </configuration>
                </plugin>
            </plugins>
        </build>
        <dependencies>
            <dependency>
                <groupId>org.glassfish</groupId>
                <artifactId>javax.json</artifactId>
                <version>1.0.2</version>
            </dependency>
        </dependencies>   
    </project>
    ```

### <a name="set-up-the-project-structure"></a>Configurar a estrutura do projeto

1. Selecione **arquivo** > **estrutura do projeto**.
1. Selecione **módulos**e expanda a árvore de origem para acessar o conteúdo da `src`  >   `main` pasta.
1. Napasta,adicione`src` pastase.`app` `service`  >   `main`  >  `java` Para fazer isso, selecione a `java` pasta, pressione Alt + Insert e insira o nome da pasta.
1. Napasta,adicione`src` pastase.`app` `service`  >   `main`  > `resources`

    Quando terminar, a árvore do projeto deverá ser parecida com a imagem a seguir.

    ![Estrutura do diretório do projeto](media/search-get-started-java/java-quickstart-basic-code-tree.png)

1. Clique em **OK** para fechar a janela.

### <a name="add-azure-search-service-information"></a>Adicionar informações do serviço Azure Search

1. Na janela do **projeto** , expanda a árvore de origem para `src` acessar a  >  >   `resources`  >  `main` `app` pasta e adicione `config.properties` um arquivo. Para fazer isso, selecione a `app` pasta, pressione ALT + Inserir, selecione **arquivo**e, em seguida, insira o nome do arquivo.

1. Copie as configurações a seguir no novo arquivo e substitua `<YOUR-SEARCH-SERVICE-NAME>`, `<YOUR-ADMIN-KEY>`e `<YOUR-QUERY-KEY>` pelo nome do serviço e pelas chaves. Se o ponto de extremidade `https://mydemo.search.windows.net`de serviço for, o nome do serviço será "mydemo".

    ```java
        SearchServiceName=<YOUR-SEARCH-SERVICE-NAME>
        SearchServiceAdminKey=<YOUR-ADMIN-KEY>
        SearchServiceQueryKey=<YOUR-QUERY-KEY>
        IndexName=hotels-quickstart
        ApiVersion=2019-05-06
    ```

### <a name="add-the-main-method"></a>Adicionar o método Main

1. `src` Napasta`app` , adicione uma`App` classe.  >   `main`  >  `java`  >  Para fazer isso, selecione a `app` pasta, pressione Alt + Insert, selecione **classe Java**e, em seguida, insira o nome da classe.
1. Abra a `App` classe e substitua o conteúdo pelo código a seguir. Esse código contém o `main` método. 

    O código não comentado lê os parâmetros do serviço de pesquisa e os usa para criar uma instância do cliente do serviço de pesquisa. O código do cliente do serviço de pesquisa será adicionado na próxima seção.

    O código comentado nesta classe não será comentado em uma seção posterior deste guia de início rápido.

    ```java
    package main.java.app;
    
    import main.java.service.SearchServiceClient;
    import java.io.IOException;
    import java.util.Properties;
    
    public class App {
    
        private static Properties loadPropertiesFromResource(String resourcePath) throws IOException {
            var inputStream = App.class.getResourceAsStream(resourcePath);
            var configProperties = new Properties();
            configProperties.load(inputStream);
            return configProperties;
        }
    
        public static void main(String[] args) {
            try {
                var config = loadPropertiesFromResource("/app/config.properties");
                var client = new SearchServiceClient(
                        config.getProperty("SearchServiceName"),
                        config.getProperty("SearchServiceAdminKey"),
                        config.getProperty("SearchServiceQueryKey"),
                        config.getProperty("ApiVersion"),
                        config.getProperty("IndexName")
                );
    
    
    //Uncomment the next 3 lines in the 1 - Create Index section of the quickstart
    //            if(client.indexExists()){ client.deleteIndex();}
    //            client.createIndex("/service/index.json");
    //            Thread.sleep(1000L); // wait a second to create the index
    
    //Uncomment the next 2 lines in the 2 - Load Documents section of the quickstart
    //            client.uploadDocuments("/service/hotels.json");
    //            Thread.sleep(2000L); // wait 2 seconds for data to upload
    
    //Uncomment the following 5 search queries in the 3 - Search an index section of the quickstart
    //            // Query 1
    //            client.logMessage("\n*QUERY 1****************************************************************");
    //            client.logMessage("Search for: Atlanta'");
    //            client.logMessage("Return: All fields'");
    //            client.searchPlus("Atlanta");
    //
    //            // Query 2
    //            client.logMessage("\n*QUERY 2****************************************************************");
    //            client.logMessage("Search for: Atlanta");
    //            client.logMessage("Return: HotelName, Tags, Address");
    //            SearchServiceClient.SearchOptions options2 = client.createSearchOptions();
    //            options2.select = "HotelName,Tags,Address";
    //            client.searchPlus("Atlanta", options2);
    //
    //            //Query 3
    //            client.logMessage("\n*QUERY 3****************************************************************");
    //            client.logMessage("Search for: wifi & restaurant");
    //            client.logMessage("Return: HotelName, Description, Tags");
    //            SearchServiceClient.SearchOptions options3 = client.createSearchOptions();
    //            options3.select = "HotelName,Description,Tags";
    //            client.searchPlus("wifi,restaurant", options3);
    //
    //            // Query 4 -filtered query
    //            client.logMessage("\n*QUERY 4****************************************************************");
    //            client.logMessage("Search for: all");
    //            client.logMessage("Filter: Ratings greater than 4");
    //            client.logMessage("Return: HotelName, Rating");
    //            SearchServiceClient.SearchOptions options4 = client.createSearchOptions();
    //            options4.filter="Rating%20gt%204";
    //            options4.select = "HotelName,Rating";
    //            client.searchPlus("*",options4);
    //
    //            // Query 5 - top 2 results, ordered by
    //            client.logMessage("\n*QUERY 5****************************************************************");
    //            client.logMessage("Search for: boutique");
    //            client.logMessage("Get: Top 2 results");
    //            client.logMessage("Order by: Rating in descending order");
    //            client.logMessage("Return: HotelId, HotelName, Category, Rating");
    //            SearchServiceClient.SearchOptions options5 = client.createSearchOptions();
    //            options5.top=2;
    //            options5.orderby = "Rating%20desc";
    //            options5.select = "HotelId,HotelName,Category,Rating";
    //            client.searchPlus("boutique", options5);
    
            } catch (Exception e) {
                System.err.println("Exception:" + e.getMessage());
                e.printStackTrace();
            }
        }
    }
    ```

### <a name="add-the-http-operations"></a>Adicionar as operações HTTP

1. `src` Napasta`service` , adicioneuma`SearchServiceClient` classe.  >   `main`  >  `java`  >  Para fazer isso, selecione a `service` pasta, pressione Alt + Insert, selecione **classe Java**e, em seguida, insira o nome da classe.
1. Abra a `SearchServiceClient` classe e substitua o conteúdo pelo código a seguir. Esse código fornece as operações HTTP necessárias para usar a API REST do Azure Search. Métodos adicionais para criar um índice, carregar documentos e consultar o índice serão adicionados em uma seção posterior.

    ```java
    package main.java.service;

    import javax.json.Json;
    import javax.net.ssl.HttpsURLConnection;
    import java.io.IOException;
    import java.io.StringReader;
    import java.net.HttpURLConnection;
    import java.net.URI;
    import java.net.http.HttpClient;
    import java.net.http.HttpRequest;
    import java.net.http.HttpResponse;
    import java.nio.charset.StandardCharsets;
    import java.util.Formatter;
    import java.util.function.Consumer;
    
        /* This class is responsible for implementing HTTP operations for creating the index, uploading documents and searching the data*/
        public class SearchServiceClient {
            private final String _adminKey;
            private final String _queryKey;
            private final String _apiVersion;
            private final String _serviceName;
            private final String _indexName;
            private final static HttpClient client = HttpClient.newHttpClient();
    
        public SearchServiceClient(String serviceName, String adminKey, String queryKey, String apiVersion, String indexName) {
            this._serviceName = serviceName;
            this._adminKey = adminKey;
            this._queryKey = queryKey;
            this._apiVersion = apiVersion;
            this._indexName = indexName;
        }

        private static HttpResponse<String> sendRequest(HttpRequest request) throws IOException, InterruptedException {
            logMessage(String.format("%s: %s", request.method(), request.uri()));
            return client.send(request, HttpResponse.BodyHandlers.ofString());
        }

        private static URI buildURI(Consumer<Formatter> fmtFn)
                {
                    Formatter strFormatter = new Formatter();
                    fmtFn.accept(strFormatter);
                    String url = strFormatter.out().toString();
                    strFormatter.close();
                    return URI.create(url);
        }
    
        public static void logMessage(String message) {
            System.out.println(message);
        }
    
        public static boolean isSuccessResponse(HttpResponse<String> response) {
            try {
                int responseCode = response.statusCode();
    
                logMessage("\n Response code = " + responseCode);
    
                if (responseCode == HttpURLConnection.HTTP_OK || responseCode == HttpURLConnection.HTTP_ACCEPTED
                        || responseCode == HttpURLConnection.HTTP_NO_CONTENT || responseCode == HttpsURLConnection.HTTP_CREATED) {
                    return true;
                }
    
                // We got an error
                var msg = response.body();
                if (msg != null) {
                    logMessage(String.format("\n MESSAGE: %s", msg));
                }
    
            } catch (Exception e) {
                e.printStackTrace();
            }
    
            return false;
        }
    
        public static HttpRequest httpRequest(URI uri, String key, String method, String contents) {
            contents = contents == null ? "" : contents;
            var builder = HttpRequest.newBuilder();
            builder.uri(uri);
            builder.setHeader("content-type", "application/json");
            builder.setHeader("api-key", key);
    
            switch (method) {
                case "GET":
                    builder = builder.GET();
                    break;
                case "HEAD":
                    builder = builder.GET();
                    break;
                case "DELETE":
                    builder = builder.DELETE();
                    break;
                case "PUT":
                    builder = builder.PUT(HttpRequest.BodyPublishers.ofString(contents));
                    break;
                case "POST":
                    builder = builder.POST(HttpRequest.BodyPublishers.ofString(contents));
                    break;
                default:
                    throw new IllegalArgumentException(String.format("Can't create request for method '%s'", method));
            }
            return builder.build();
        }
    }
    
    ```

### <a name="build-the-project"></a>Compilar o projeto

1. Verifique se o seu projeto tem a seguinte estrutura.

    ![Estrutura do diretório do projeto](media/search-get-started-java/java-quickstart-basic-code-tree-plus-classes.png)

1. Abra a janela de ferramentas do **Maven** e execute esta meta do Maven: `verify exec:java`
![Executar meta do Maven: verificar exec: Java](media/search-get-started-java/java-quickstart-execute-maven-goal.png)

Quando o processamento for concluído, procure uma mensagem de êxito de compilação seguida por um código de saída zero (0).

## <a name="1---create-index"></a>1-criar índice

A definição do índice de hotéis contém campos simples e um campo complexo. Exemplos de um campo simples são "Hotelname" ou "Description". O campo "endereço" é um campo complexo porque tem subcampos, como "endereço" e "cidade". Neste guia de início rápido, a definição do índice é especificada usando JSON.

1. Na janela do **projeto** , expanda a árvore de origem para `src` acessar a  >  >   `resources`  >  `main` `service` pasta e adicione `index.json` um arquivo. Para fazer isso, selecione a `app` pasta, pressione ALT + Inserir, selecione **arquivo**e, em seguida, insira o nome do arquivo.

1. Abra o `index.json` arquivo e insira a definição de índice a seguir.

    ```json
    {
      "name": "hotels-quickstart",
      "fields": [
        {
          "name": "HotelId",
          "type": "Edm.String",
          "key": true,
          "filterable": true
        },
        {
          "name": "HotelName",
          "type": "Edm.String",
          "searchable": true,
          "filterable": false,
          "sortable": true,
          "facetable": false
        },
        {
          "name": "Description",
          "type": "Edm.String",
          "searchable": true,
          "filterable": false,
          "sortable": false,
          "facetable": false,
          "analyzer": "en.lucene"
        },
        {
          "name": "Description_fr",
          "type": "Edm.String",
          "searchable": true,
          "filterable": false,
          "sortable": false,
          "facetable": false,
          "analyzer": "fr.lucene"
        },
        {
          "name": "Category",
          "type": "Edm.String",
          "searchable": true,
          "filterable": true,
          "sortable": true,
          "facetable": true
        },
        {
          "name": "Tags",
          "type": "Collection(Edm.String)",
          "searchable": true,
          "filterable": true,
          "sortable": false,
          "facetable": true
        },
        {
          "name": "ParkingIncluded",
          "type": "Edm.Boolean",
          "filterable": true,
          "sortable": true,
          "facetable": true
        },
        {
          "name": "LastRenovationDate",
          "type": "Edm.DateTimeOffset",
          "filterable": true,
          "sortable": true,
          "facetable": true
        },
        {
          "name": "Rating",
          "type": "Edm.Double",
          "filterable": true,
          "sortable": true,
          "facetable": true
        },
        {
          "name": "Address",
          "type": "Edm.ComplexType",
          "fields": [
            {
              "name": "StreetAddress",
              "type": "Edm.String",
              "filterable": false,
              "sortable": false,
              "facetable": false,
              "searchable": true
            },
            {
              "name": "City",
              "type": "Edm.String",
              "searchable": true,
              "filterable": true,
              "sortable": true,
              "facetable": true
            },
            {
              "name": "StateProvince",
              "type": "Edm.String",
              "searchable": true,
              "filterable": true,
              "sortable": true,
              "facetable": true
            },
            {
              "name": "PostalCode",
              "type": "Edm.String",
              "searchable": true,
              "filterable": true,
              "sortable": true,
              "facetable": true
            },
            {
              "name": "Country",
              "type": "Edm.String",
              "searchable": true,
              "filterable": true,
              "sortable": true,
              "facetable": true
            }
          ]
        }
      ]
    }
    ```

    O nome do índice será "Hotéis-início rápido". Os atributos nos campos de índice determinam como os dados indexados podem ser pesquisados em um aplicativo. Por exemplo, o `IsSearchable` atributo deve ser atribuído a cada campo que deve ser incluído em uma pesquisa de texto completo. Para saber mais sobre atributos, confira [campos de coleção e de campo](search-what-is-an-index.md#fields-collection).
    
    O `Description` campo neste índice usa a propriedade opcional `analyzer` para substituir o analisador de idioma padrão Lucene. O `Description_fr` campo está usando o analisador `fr.lucene` Lucene francês porque armazena texto em francês. O `Description` está usando o Microsoft Language Analyzer en. Lucene opcional. Para saber mais sobre analisadores, consulte [analisadores para processamento de texto em Azure Search](search-analyzers.md).

1. Adicione o código a seguir à `SearchServiceClient` classe. Esses métodos criam Azure Search URLs de serviço REST que criam e excluem um índice, e que determinam se existe um índice. Os métodos também fazem a solicitação HTTP.

    ```java
    public boolean indexExists() throws IOException, InterruptedException {
        logMessage("\n Checking if index exists...");
        var uri = buildURI(strFormatter -> strFormatter.format(
                "https://%s.search.windows.net/indexes/%s/docs?api-version=%s&search=*",
                _serviceName,_indexName,_apiVersion));
        var request = httpRequest(uri, _adminKey, "HEAD", "");
        var response = sendRequest(request);
        return isSuccessResponse(response);
    }
    
    public boolean deleteIndex() throws IOException, InterruptedException {
        logMessage("\n Deleting index...");
        var uri = buildURI(strFormatter -> strFormatter.format(
                "https://%s.search.windows.net/indexes/%s?api-version=%s",
                _serviceName,_indexName,_apiVersion));
        var request = httpRequest(uri, _adminKey, "DELETE", "*");
        var response = sendRequest(request);
        return isSuccessResponse(response);
    }
    
    
    public boolean createIndex(String indexDefinitionFile) throws IOException, InterruptedException {
        logMessage("\n Creating index...");
        //Build the search service URL
        var uri = buildURI(strFormatter -> strFormatter.format(
                "https://%s.search.windows.net/indexes/%s?api-version=%s",
                _serviceName,_indexName,_apiVersion));
        //Read in index definition file
        var inputStream = SearchServiceClient.class.getResourceAsStream(indexDefinitionFile);
        var indexDef = new String(inputStream.readAllBytes(), StandardCharsets.UTF_8);
        //Send HTTP PUT request to create the index in the search service
        var request = httpRequest(uri, _adminKey, "PUT", indexDef);
        var response = sendRequest(request);
        return isSuccessResponse(response);
    }
    ```

1. Remova a marca de comentário do código `App` a seguir na classe. Esse código exclui o índice "Hotéis-início rápido", se existir, e cria um novo índice com base na definição de índice no arquivo "index. JSON". 

    Uma pausa de um segundo é inserida após a solicitação de criação de índice. Essa pausa garante que o índice seja criado antes de você carregar documentos.

    ```java
        if (client.indexExists()) { client.deleteIndex();}
          client.createIndex("/service/index.json");
          Thread.sleep(1000L); // wait a second to create the index
    ```

1. Abra a janela de ferramentas do **Maven** e execute esta meta do Maven:`verify exec:java`

    À medida que o código é executado, procure uma mensagem "criando índice" seguida por um código de resposta 201. Esse código de resposta confirma que o índice foi criado. A execução deve terminar com uma mensagem de êxito de compilação e um código de saída zero (0).
    
## <a name="2---load-documents"></a>2-carregar documentos

1. Na janela do **projeto** , expanda a árvore de origem para `src` acessar a  >  >   `resources`  >  `main` `service` pasta e adicione `hotels.json` um arquivo. Para fazer isso, selecione a `app` pasta, pressione ALT + Inserir, selecione **arquivo**e, em seguida, insira o nome do arquivo.
1. Insira os seguintes documentos do hotel no arquivo.

    ```json
    {
      "value": [
        {
          "@search.action": "upload",
          "HotelId": "1",
          "HotelName": "Secret Point Motel",
          "Description": "The hotel is ideally located on the main commercial artery of the city in the heart of New York. A few minutes away is Time's Square and the historic centre of the city, as well as other places of interest that make New York one of America's most attractive and cosmopolitan cities.",
          "Description_fr": "L'hôtel est idéalement situé sur la principale artère commerciale de la ville en plein cœur de New York. A quelques minutes se trouve la place du temps et le centre historique de la ville, ainsi que d'autres lieux d'intérêt qui font de New York l'une des villes les plus attractives et cosmopolites de l'Amérique.",
          "Category": "Boutique",
          "Tags": [ "pool", "air conditioning", "concierge" ],
          "ParkingIncluded": "false",
          "LastRenovationDate": "1970-01-18T00:00:00Z",
          "Rating": 3.60,
          "Address": {
            "StreetAddress": "677 5th Ave",
            "City": "New York",
            "StateProvince": "NY",
            "PostalCode": "10022",
            "Country": "USA"
          }
        },
        {
          "@search.action": "upload",
          "HotelId": "2",
          "HotelName": "Twin Dome Motel",
          "Description": "The hotel is situated in a  nineteenth century plaza, which has been expanded and renovated to the highest architectural standards to create a modern, functional and first-class hotel in which art and unique historical elements coexist with the most modern comforts.",
          "Description_fr": "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
          "Category": "Boutique",
          "Tags": [ "pool", "free wifi", "concierge" ],
          "ParkingIncluded": "false",
          "LastRenovationDate": "1979-02-18T00:00:00Z",
          "Rating": 3.60,
          "Address": {
            "StreetAddress": "140 University Town Center Dr",
            "City": "Sarasota",
            "StateProvince": "FL",
            "PostalCode": "34243",
            "Country": "USA"
          }
        },
        {
          "@search.action": "upload",
          "HotelId": "3",
          "HotelName": "Triple Landscape Hotel",
          "Description": "The Hotel stands out for its gastronomic excellence under the management of William Dough, who advises on and oversees all of the Hotel’s restaurant services.",
          "Description_fr": "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
          "Category": "Resort and Spa",
          "Tags": [ "air conditioning", "bar", "continental breakfast" ],
          "ParkingIncluded": "true",
          "LastRenovationDate": "2015-09-20T00:00:00Z",
          "Rating": 4.80,
          "Address": {
            "StreetAddress": "3393 Peachtree Rd",
            "City": "Atlanta",
            "StateProvince": "GA",
            "PostalCode": "30326",
            "Country": "USA"
          }
        },
        {
          "@search.action": "upload",
          "HotelId": "4",
          "HotelName": "Sublime Cliff Hotel",
          "Description": "Sublime Cliff Hotel is located in the heart of the historic center of Sublime in an extremely vibrant and lively area within short walking distance to the sites and landmarks of the city and is surrounded by the extraordinary beauty of churches, buildings, shops and monuments. Sublime Cliff is part of a lovingly restored 1800 palace.",
          "Description_fr": "Le sublime Cliff Hotel est situé au coeur du centre historique de sublime dans un quartier extrêmement animé et vivant, à courte distance de marche des sites et monuments de la ville et est entouré par l'extraordinaire beauté des églises, des bâtiments, des commerces et Monuments. Sublime Cliff fait partie d'un Palace 1800 restauré avec amour.",
          "Category": "Boutique",
          "Tags": [ "concierge", "view", "24-hour front desk service" ],
          "ParkingIncluded": "true",
          "LastRenovationDate": "1960-02-06T00:00:00Z",
          "Rating": 4.60,
          "Address": {
            "StreetAddress": "7400 San Pedro Ave",
            "City": "San Antonio",
            "StateProvince": "TX",
            "PostalCode": "78216",
            "Country": "USA"
          }
        }
      ]
    }
    ```

1. Insira o código a seguir na `SearchServiceClient` classe. Esse código cria a URL do serviço REST para carregar os documentos do hotel no índice e, em seguida, faz a solicitação HTTP POST.

    ```java
    public boolean uploadDocuments(String documentsFile) throws IOException, InterruptedException {
        logMessage("\n Uploading documents...");
        //Build the search service URL
        var endpoint = buildURI(strFormatter -> strFormatter.format(
                "https://%s.search.windows.net/indexes/%s/docs/index?api-version=%s",
                _serviceName,_indexName,_apiVersion));
        //Read in the data to index
        var inputStream = SearchServiceClient.class.getResourceAsStream(documentsFile);
        var documents = new String(inputStream.readAllBytes(), StandardCharsets.UTF_8);
        //Send HTTP POST request to upload and index the data
        var request = httpRequest(endpoint, _adminKey, "POST", documents);
        var response = sendRequest(request);
        return isSuccessResponse(response);
    }
    ```

1. Remova a marca de comentário do código `App` a seguir na classe. Esse código carrega os documentos em "Hotéis. JSON" no índice.

    ```java
    client.uploadDocuments("/service/hotels.json");
    Thread.sleep(2000L); // wait 2 seconds for data to upload
    ```

    Uma pausa de dois segundos é inserida após a solicitação de upload para garantir que o processo de carregamento de documento seja concluído antes de consultar o índice.

1. Abra a janela de ferramentas do **Maven** e execute esta meta do Maven:`verify exec:java`

    Como você criou um índice "Hotéis-início rápido" na etapa anterior, o código agora o excluirá e o recriará antes de carregar os documentos do hotel.

    À medida que o código é executado, procure uma mensagem "Carregando documentos" seguida por um código de resposta 200. Esse código de resposta confirma que os documentos foram carregados no índice. A execução deve terminar com uma mensagem de êxito de compilação e um código de saída zero (0).

## <a name="3---search-an-index"></a>3 - Pesquisar um índice

Agora que você carregou os documentos de hotéis, pode criar consultas de pesquisa para acessar os dados de hotéis.

1. Adicione o código a seguir à `SearchServiceClient` classe. Esse código cria Azure Search URLs de serviço REST para pesquisar os dados indexados e imprime os resultados da pesquisa.

    A `SearchOptions` classe e `createSearchOptions` o método permitem especificar um subconjunto dos Azure Search opções de consulta da API REST disponíveis. Para obter mais informações sobre as opções de consulta da API REST, consulte [Pesquisar documentos (Azure Search API REST do serviço)](/rest/api/searchservice/search-documents).

    O `SearchPlus` método cria a URL de consulta de pesquisa, faz a solicitação de pesquisa e, em seguida, imprime os resultados no console. 

    ```java
    public SearchOptions createSearchOptions() { return new SearchOptions();}

    //Defines available search parameters that can be set
    public static class SearchOptions {

        public String select = "";
        public String filter = "";
        public int top = 0;
        public String orderby= "";
    }

    //Concatenates search parameters to append to the search request
    private String createOptionsString(SearchOptions options)
    {
        String optionsString = "";
        if (options != null) {
            if (options.select != "")
                optionsString = optionsString + "&$select=" + options.select;
            if (options.filter != "")
                optionsString = optionsString + "&$filter=" + options.filter;
            if (options.top != 0)
                optionsString = optionsString + "&$top=" + options.top;
            if (options.orderby != "")
                optionsString = optionsString + "&$orderby=" +options.orderby;
        }
        return optionsString;
    }
    
    public void searchPlus(String queryString)
    {
        searchPlus( queryString, null);
    }
    
    public void searchPlus(String queryString, SearchOptions options) {
    
        try {
            String optionsString = createOptionsString(options);
            var uri = buildURI(strFormatter -> strFormatter.format(
                    "https://%s.search.windows.net/indexes/%s/docs?api-version=%s&search=%s%s",
                    _serviceName, _indexName, _apiVersion, queryString, optionsString));
            var request = httpRequest(uri, _queryKey, "GET", null);
            var response = sendRequest(request);
            var jsonReader = Json.createReader(new StringReader(response.body()));
            var jsonArray = jsonReader.readObject().getJsonArray("value");
            var resultsCount = jsonArray.size();
            logMessage("Results:\nCount: " + resultsCount);
            for (int i = 0; i <= resultsCount - 1; i++) {
                logMessage(jsonArray.get(i).toString());
            }
    
            jsonReader.close();
    
        }
        catch (Exception e) {
            e.printStackTrace();
        }
    
    }
    ```

1. `App` Na classe, remova os comentários do código a seguir. Esse código configura cinco consultas diferentes, incluindo o texto de pesquisa, os parâmetros de consulta e os campos de dados a serem retornados. 

    ```java
    // Query 1
    client.logMessage("\n*QUERY 1****************************************************************");
    client.logMessage("Search for: Atlanta");
    client.logMessage("Return: All fields'");
    client.searchPlus("Atlanta");

    // Query 2
    client.logMessage("\n*QUERY 2****************************************************************");
    client.logMessage("Search for: Atlanta");
    client.logMessage("Return: HotelName, Tags, Address");
    SearchServiceClient.SearchOptions options2 = client.createSearchOptions();
    options2.select = "HotelName,Tags,Address";
    client.searchPlus("Atlanta", options2);

    //Query 3
    client.logMessage("\n*QUERY 3****************************************************************");
    client.logMessage("Search for: wifi & restaurant");
    client.logMessage("Return: HotelName, Description, Tags");
    SearchServiceClient.SearchOptions options3 = client.createSearchOptions();
    options3.select = "HotelName,Description,Tags";
    client.searchPlus("wifi,restaurant", options3);

    // Query 4 -filtered query
    client.logMessage("\n*QUERY 4****************************************************************");
    client.logMessage("Search for: all");
    client.logMessage("Filter: Ratings greater than 4");
    client.logMessage("Return: HotelName, Rating");
    SearchServiceClient.SearchOptions options4 = client.createSearchOptions();
    options4.filter="Rating%20gt%204";
    options4.select = "HotelName,Rating";
    client.searchPlus("*",options4);

    // Query 5 - top 2 results, ordered by
    client.logMessage("\n*QUERY 5****************************************************************");
    client.logMessage("Search for: boutique");
    client.logMessage("Get: Top 2 results");
    client.logMessage("Order by: Rating in descending order");
    client.logMessage("Return: HotelId, HotelName, Category, Rating");
    SearchServiceClient.SearchOptions options5 = client.createSearchOptions();
    options5.top=2;
    options5.orderby = "Rating%20desc";
    options5.select = "HotelId,HotelName,Category,Rating";
    client.searchPlus("boutique", options5);
    ```



    Há duas [maneiras de fazer a correspondência de termos em uma consulta](search-query-overview.md#types-of-queries): pesquisa de texto completo e filtros. Uma consulta de pesquisa de texto completo pesquisa um ou mais termos em `IsSearchable` campos no índice. Um filtro é uma expressão booliana que é avaliada sobre `IsFilterable` os campos em um índice. Você pode usar a pesquisa de texto completo e os filtros juntos ou separados.

1. Abra a janela de ferramentas do **Maven** e execute esta meta do Maven:`verify exec:java`

    Procure um resumo de cada consulta e seus resultados. A execução deve ser concluída com a mensagem de êxito de BUILD e um código de saída zero (0).

## <a name="clean-up"></a>Limpeza

Quando você está trabalhando em sua própria assinatura, no final de um projeto, é uma boa ideia remover os recursos que não são mais necessários. Os recursos deixados em execução podem custar dinheiro. Você pode excluir os recursos individualmente ou excluir o grupo de recursos para excluir o conjunto inteiro de recursos.

Você pode encontrar e gerenciar recursos no portal, usando o link **todos os recursos** ou **grupos de recursos** no painel de navegação esquerdo.

Se você estiver usando um serviço gratuito, lembre-se de que você está limitado a três índices, indexadores e fontes de dados. Você pode excluir itens individuais no portal para permanecer abaixo do limite. 

## <a name="next-steps"></a>Passos seguintes

Neste início rápido do Java, você trabalhou em uma série de tarefas para criar um índice, carregá-lo com documentos e executar consultas. Se você estiver familiarizado com os conceitos básicos, recomendamos os seguintes artigos para um aprendizado mais profundo.

+ [Operações de índice](/rest/api/searchservice/index-operations)

+ [Operações de documento](/rest/api/searchservice/document-operations)

+ [Operações do indexador](/rest/api/searchservice/indexer-operations)
