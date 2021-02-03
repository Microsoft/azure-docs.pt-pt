---
title: 'Quickstart: Criar um índice de pesquisa em Javas'
titleSuffix: Azure Cognitive Search
description: Neste quickstart Java, aprenda a criar um índice, carregar dados e executar consultas usando a biblioteca de clientes Azure Cognitive Search para Java.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.devlang: java
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 01/25/2021
ms.custom: devx-track-java
ms.openlocfilehash: 9e05e41ca0c293e31a29dc25a7b4ec7b87734246
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/03/2021
ms.locfileid: "99509423"
---
# <a name="quickstart-create-an-azure-cognitive-search-index-in-java"></a>Quickstart: Criar um índice de pesquisa cognitiva Azure em Java
> [!div class="op_single_selector"]
> * [Java](search-get-started-java.md)
> * [JavaScript](search-get-started-javascript.md)
> * [C#](search-get-started-dotnet.md)
> * [Portal](search-get-started-portal.md)
> * [PowerShell](search-get-started-powershell.md)
> * [Python](search-get-started-python.md)
> * [REST](search-get-started-rest.md)

Crie uma aplicação de consola Java que cria, carrega e consulta um índice de pesquisa usando [IntelliJ](https://www.jetbrains.com/idea/), [Java 11 SDK,](/java/azure/jdk/)e a [API de Pesquisa Cognitiva Azure](/rest/api/searchservice/). Este artigo fornece instruções passo a passo para a criação da aplicação. Em alternativa, pode [descarregar e executar a aplicação completa.](https://developers.google.com/sheets/api/quickstart/java)

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Utilizamos o seguinte software e serviços para construir e testar este arranque rápido:

+ [IntelliJ IDEA](https://www.jetbrains.com/idea/)

+ [Java 11 SDK](/java/azure/jdk/)

+ [Crie um serviço de Pesquisa Cognitiva Azure](search-create-service-portal.md) ou [encontre um serviço existente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) sob a sua subscrição atual. Pode utilizar um serviço gratuito para este arranque rápido.

<a name="get-service-info"></a>

## <a name="get-a-key-and-url"></a>Obtenha uma chave e URL

As chamadas para o serviço requerem um ponto final URL e uma chave de acesso em cada pedido. Um serviço de pesquisa é criado com ambos, por isso, se adicionar Azure Cognitive Search à sua subscrição, siga estes passos para obter as informações necessárias:

1. [Inscreva-se no portal Azure,](https://portal.azure.com/)e na página **geral do** seu serviço de pesquisa, obtenha o URL. Um ponto final de exemplo poderá ser parecido com `https://mydemo.search.windows.net`.

1. Em **Definições**  >  **Teclas,** obtenha uma chave de administração para todos os direitos sobre o serviço. Existem duas chaves de administração intercambiáveis, previstas para a continuidade do negócio, caso precise de rolar uma. Pode utilizar a tecla primária ou secundária nos pedidos de adição, modificação e eliminação de objetos.

   :::image type="content" source="media/search-get-started-rest/get-url-key.png" alt-text="Obtenha o nome de serviço e as chaves de administração e consulta" border="false":::

Cada pedido enviado ao seu serviço requer uma chave API. Ter uma chave válida estabelece fidedignidade, numa base por pedido, entre a aplicação a enviar o pedido e o serviço que o processa.

## <a name="set-up-your-environment"></a>Configurar o ambiente

Comece por abrir o IntelliJ IDEA e criar um novo projeto.

### <a name="create-the-project"></a>Criar o projeto

1. Abra a ideia intelliJ e selecione **Create New Project**.
1. Selecione **Maven**.
1. Na lista **do Project SDK,** selecione o Java 11 SDK.

    :::image type="content" source="media/search-get-started-java/java-quickstart-create-new-maven-project.png" alt-text="Criar um projeto maven" border="false":::

1. Para **GroupId** e **ArtifactId,** insira `AzureSearchQuickstart` .
1. Aceite os restantes incumprimentos para abrir o projeto.

### <a name="specify-maven-dependencies"></a>Especificar dependências de Maven

1. Selecione   >  **Definições de ficheiros**.
1. Na janela **Definições,** selecione **Construção, Execução, Implementação**  >  **Ferramentas de Construção**  >    >  **Maven Importando**.
1. Selecione os  **projetos Import Maven** verifique automaticamente a caixa e clique **em OK** para fechar a janela. Os plugins maven e outras dependências serão automaticamente sincronizados quando atualizar o ficheiro pom.xml no passo seguinte.

    :::image type="content" source="media/search-get-started-java/java-quickstart-settings-import-maven-auto.png" alt-text="Opções de importação de Maven nas definições de IntelliJ" border="false":::

1. Abra o ficheiro pom.xml e substitua o conteúdo pelos seguintes detalhes de configuração Maven. Estes incluem referências ao [Plugin Maven DoEc e](https://www.mojohaus.org/exec-maven-plugin/) a uma [API de interface JSON](https://javadoc.io/doc/org.glassfish/javax.json/1.0.2)

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <project xmlns="http://maven.apache.org/POM/4.0.0"
             xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
             xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
        <modelVersion>4.0.0</modelVersion>
        <groupId>AzureSearchQuickstart</groupId>
        <artifactId>AzureSearchQuickstart</artifactId>
        <packaging>jar</packaging>
        <version>1.0-SNAPSHOT</version>
        <properties>
            <jackson.version>2.12.1</jackson.version>
            <auto-value.version>1.6.2</auto-value.version>
            <junit.version>5.4.2</junit.version>
            <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        </properties>
        <name>azuresearch-console</name>
        <url>http://maven.apache.org</url>
        <dependencies>
            <!-- https://mvnrepository.com/artifact/com.fasterxml.jackson.core/jackson-core -->
            <dependency>
                <groupId>com.fasterxml.jackson.core</groupId>
                <artifactId>jackson-core</artifactId>
                <version>${jackson.version}</version>
            </dependency>
            <dependency>
                <groupId>com.fasterxml.jackson.core</groupId>
                <artifactId>jackson-databind</artifactId>
                <version>${jackson.version}</version>
            </dependency>
            <dependency>
                <groupId>com.fasterxml.jackson.datatype</groupId>
                <artifactId>jackson-datatype-jdk8</artifactId>
                <version>${jackson.version}</version>
            </dependency>
            <dependency>
                <groupId>com.google.auto.value</groupId>
                <artifactId>auto-value-annotations</artifactId>
                <version>${auto-value.version}</version>
            </dependency>
            <dependency>
                <groupId>com.google.auto.value</groupId>
                <artifactId>auto-value</artifactId>
                <version>${auto-value.version}</version>
                <scope>provided</scope>
            </dependency>
            <dependency>
                <groupId>com.azure</groupId>
                <artifactId>azure-search-documents</artifactId>
                <version>11.1.3</version>
            </dependency>
        </dependencies>
    
        <build>
            <plugins>
                <!--put generated source files to generated-sources-->
                <plugin>
                    <groupId>org.apache.maven.plugins</groupId>
                    <artifactId>maven-compiler-plugin</artifactId>
                    <version>3.8.0</version>
                    <configuration>
                        <source>11</source>
                        <target>11</target>
                    </configuration>
                </plugin>
                <!-- For JUnit -->
                <plugin>
                    <groupId>org.apache.maven.plugins</groupId>
                    <artifactId>maven-surefire-plugin</artifactId>
                    <version>2.22.1</version>
                </plugin>
                <!-- Add exec plugin to run demo program -->
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
                        <mainClass>com.microsoft.azure.search.samples.demo.App</mainClass>
                        <cleanupDaemonThreads>false</cleanupDaemonThreads>
                    </configuration>
                </plugin>
            </plugins>
        </build>
    </project>
    ```

<!-- STOPPED HERE -- SENT EMAIL TO TONG XU ASKING FOR INFO -->
### <a name="set-up-the-project-structure"></a>Configurar a estrutura do projeto

1. Selecione   >  **estrutura de projeto de arquivo**.
1. Selecione **Módulos** e expanda a árvore de origem para aceder ao conteúdo da `src`  >   `main` pasta.
1. Na `src`  >   `main`  >  `java` pasta, adicione pastas para `com` , , , , , , . `microsoft` `azure` `search` `samples` `demo` . Para isso, selecione a `java` pasta, prima Alt + Insira e, em seguida, introduza o nome da pasta.
1. Na `src`  >   `main`  > `resources` pasta, adicione `app` e adicione as `service` pastas.

    Quando terminar, a árvore do projeto deve parecer a seguinte imagem.

    :::image type="content" source="media/search-get-started-java/java-quickstart-basic-code-tree.png" alt-text="Estrutura do diretório do projeto" border="false":::

1. Clique em **OK** para fechar a janela.

### <a name="add-azure-cognitive-search-service-information"></a>Adicionar informações do serviço de pesquisa cognitiva Azure

1. Na janela **do Projeto,** expanda a árvore de origem para aceder à `src`  >   `main`  > `resources`  >  `app` pasta e adicione um `config.properties` ficheiro. Para isso, selecione a `app` pasta, prima Alt + Insira, selecione **'Ficheiro'** e, em seguida, introduza o nome do ficheiro.

1. Copie as seguintes definições para o novo ficheiro e `<YOUR-SEARCH-SERVICE-NAME>` substitua, e com o seu nome de serviço e `<YOUR-ADMIN-KEY>` `<YOUR-QUERY-KEY>` teclas. Se o seu ponto final de serviço `https://mydemo.search.windows.net` for, o nome de serviço será `"mydemo"` .

    ```java
        SearchServiceName=<YOUR-SEARCH-SERVICE-NAME>
        SearchServiceAdminKey=<YOUR-ADMIN-KEY>
        SearchServiceQueryKey=<YOUR-QUERY-KEY>
        IndexName=hotels-quickstart
        ApiVersion=2020-06-30
    ```

### <a name="add-the-main-method"></a>Adicione o método principal

1. Na `src`  >   `main`  >  `java`  >  `app` pasta, adicione uma `App` classe. Para isso, selecione a `app` pasta, prima Alt + Insert, selecione **Classe Java** e, em seguida, introduza o nome da classe.
1. Abra a `App` aula e substitua o conteúdo pelo seguinte código. Este código contém o `main` método. 

    O código não comentado lê os parâmetros do serviço de pesquisa e utiliza-os para criar uma instância do cliente do serviço de pesquisa. O código do cliente do serviço de pesquisa será adicionado na secção seguinte.

    O código comentado nesta classe será descomprimido numa secção posterior deste arranque rápido.

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

### <a name="add-the-http-operations"></a>Adicione as operações HTTP

1. Na `src`  >   `main`  >  `java`  >  `service` pasta, adicione uma `SearchServiceClient` classe. Para isso, selecione a `service` pasta, prima Alt + Insert, selecione **Classe Java** e, em seguida, introduza o nome da classe.
1. Abra a `SearchServiceClient` aula e substitua o conteúdo pelo seguinte código. Este código fornece as operações HTTP necessárias para utilizar a API de Pesquisa Cognitiva Azure. Métodos adicionais para criar um índice, carregar documentos e consultar o índice serão adicionados numa secção posterior.

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

    :::image type="content" source="media/search-get-started-java/java-quickstart-basic-code-tree-plus-classes.png" alt-text="Estrutura de diretório de projeto mais aulas" border="false":::

1. Abra a janela da ferramenta **Maven** e execute este objetivo maven: `verify exec:java` 
 :::image type="content" source="media/search-get-started-java/java-quickstart-execute-maven-goal.png" alt-text="Execute o objetivo maven: verifique exec:java" border="false":::

Ao processar concluído, procure uma mensagem BUILD SUCCESS seguida de um código de saída zero (0).

## <a name="1---create-index"></a>1 - Criar índice

A definição de índice de hotéis contém campos simples e um campo complexo. Exemplos de um campo simples são "HotelName" ou "Descrição". O campo "Address" é um campo complexo porque tem subcampos, como "Street Address" e "City". Neste arranque rápido, a definição de índice é especificada utilizando JSON.

1. Na janela **do Projeto,** expanda a árvore de origem para aceder à `src`  >   `main`  > `resources`  >  `service` pasta e adicione um `index.json` ficheiro. Para isso, selecione a `app` pasta, prima Alt + Insira, selecione **'Ficheiro'** e, em seguida, introduza o nome do ficheiro.

1. Abra o `index.json` ficheiro e insira a seguinte definição de índice.

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

    O nome do índice será "hotels-quickstart". Os atributos nos campos de índice determinam como os dados indexados podem ser pesquisados numa aplicação. Para melhor, todos os casos, é a sua mensagem na pesquisa por `IsSearchable` texto. Pode pesquisar por palavras ou frases da semana. Para saber mais sobre atributos, consulte [Criar Índice (REST)](/rest/api/searchservice/create-index).
    
    O `Description` campo neste índice usa a propriedade opcional para anular o `analyzer` analisador de linguagem Lucene padrão. O `Description_fr` campo está a usar o analisador francês Lucene porque armazena texto `fr.lucene` francês. Está `Description` a usar o analisador de idioma opcional da Microsoft en.lucene. Para saber mais sobre os analisadores, consulte [os Analisadores para processamento de texto na Pesquisa Cognitiva Azure.](search-analyzers.md)

1. Adicione o seguinte código à classe `SearchServiceClient`. Estes métodos constroem URLs de serviço de rest de pesquisa cognitiva Azure que criam e apagam um índice, e que determinam se existe um índice. Os métodos também fazem o pedido HTTP.

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

1. Descomprimir o seguinte código na `App` classe. Este código elimina o índice "hotels-quickstart", se existir, e cria um novo índice baseado na definição de índice no ficheiro "index.json". 

    Uma pausa de um segundo é inserida após o pedido de criação de índice. Esta pausa garante que o índice é criado antes de carregar documentos.

    ```java
        if (client.indexExists()) { client.deleteIndex();}
          client.createIndex("/service/index.json");
          Thread.sleep(1000L); // wait a second to create the index
    ```

1. Abra a janela da ferramenta **Maven** e execute este objetivo maven: `verify exec:java`

    À medida que o código é executado, procure uma mensagem "Criar índice", seguida de um código de resposta 201. Este código de resposta confirma que o índice foi criado. A execução deve terminar com uma mensagem BUILD SUCCESS e um código de saída zero (0).
    
## <a name="2---load-documents"></a>2 - Documentos de carga

1. Na janela **do Projeto,** expanda a árvore de origem para aceder à `src`  >   `main`  > `resources`  >  `service` pasta e adicione um `hotels.json` ficheiro. Para isso, selecione a `app` pasta, prima Alt + Insira, selecione  **'Ficheiro'** e, em seguida, introduza o nome do ficheiro.
1. Insira os seguintes documentos do hotel no ficheiro.

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

1. Insira o seguinte código na `SearchServiceClient` classe. Este código constrói o URL de serviço REST para enviar os documentos do hotel para o índice e, em seguida, faz o pedido HTTP POST.

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

1. Descomprimir o seguinte código na `App` classe. Este código envia os documentos em "hotels.jsligados" ao índice.

    ```java
    client.uploadDocuments("/service/hotels.json");
    Thread.sleep(2000L); // wait 2 seconds for data to upload
    ```

    Uma pausa de dois segundos é inserida após o pedido de upload para garantir que o processo de carregamento do documento esteja concluído antes de consultar o índice.

1. Abra a janela da ferramenta **Maven** e execute este objetivo maven: `verify exec:java`

    Como criou um índice de "hotels-quickstart" no passo anterior, o código irá agora apagá-lo e recriá-lo novamente antes de carregar os documentos do hotel.

    À medida que o código é executado, procure uma mensagem de "upload de documentos" seguida de um código de resposta de 200. Este código de resposta confirma que os documentos foram enviados para o índice. A execução deve terminar com uma mensagem BUILD SUCCESS e um código de saída zero (0).

## <a name="3---search-an-index"></a>3 - Pesquisar um índice

Agora que você carregou os documentos dos hotéis, você pode criar consultas de pesquisa para aceder aos dados dos hotéis.

1. Adicione o seguinte código à classe `SearchServiceClient`. Este código constrói URLs de serviço de rest de pesquisa cognitiva Azure para pesquisar os dados indexados e imprime os resultados da pesquisa.

    A `SearchOptions` classe e o método permitem `createSearchOptions` especificar um subconjunto das opções de consulta API de Pesquisa Cognitiva Azure disponível. Para obter mais informações sobre as opções de consulta rest API, consulte [Documentos de Pesquisa (Azure Cognitive Search REST API)](/rest/api/searchservice/search-documents).

    O `SearchPlus` método cria o URL de consulta de pesquisa, faz o pedido de pesquisa e, em seguida, imprime os resultados para a consola. 

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

1. Na `App` aula, descomprimir o seguinte código. Este código configura cinco consultas diferentes, incluindo o texto de pesquisa, parâmetros de consulta e campos de dados para devolver. 

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



    Existem [duas formas de combinar termos numa consulta:](search-query-overview.md#types-of-queries)pesquisa de texto completo e filtros. Uma consulta de pesquisa por texto completo procura um ou mais termos nos `IsSearchable` campos do seu índice. Um filtro é uma expressão booleana que é avaliada sobre `IsFilterable` campos em um índice. Pode utilizar a pesquisa por texto completo e os filtros juntos ou separadamente.

1. Abra a janela da ferramenta **Maven** e execute este objetivo maven: `verify exec:java`

    Procure um resumo de cada consulta e seus resultados. A execução deve ser completada com a mensagem BUILD SUCCESS e um código de saída zero (0).

## <a name="clean-up-resources"></a>Limpar os recursos

Quando se está a trabalhar na sua própria subscrição, no final de um projeto, é uma boa ideia remover os recursos de que já não precisa. Os recursos que deixar em execução podem custar-lhe dinheiro. Pode eliminar recursos individualmente ou eliminar o grupo de recursos para eliminar todo o conjunto de recursos.

Pode encontrar e gerir recursos no portal, utilizando a ligação **de todos os recursos** ou **grupos** de recursos no painel de navegação à esquerda.

Se estiver a utilizar um serviço gratuito, lembre-se que está limitado a três índices, indexadores e fontes de dados. Pode eliminar itens individuais no portal para ficar abaixo do limite. 

## <a name="next-steps"></a>Passos seguintes

Neste quickstart Java, você trabalhou através de uma série de tarefas para criar um índice, carregá-lo com documentos, e executar consultas. Se estiver confortável com os conceitos básicos, recomendamos o seguinte artigo que lista as operações indexantes em REST.

> [!div class="nextstepaction"]
> [Operações indexantes](/rest/api/searchservice/indexer-operations)