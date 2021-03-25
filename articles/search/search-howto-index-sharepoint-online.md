---
title: Configure um indexador Online SharePoint (pré-visualização)
titleSuffix: Azure Cognitive Search
description: Crie um indexador SharePoint Online para automatizar a indexação do conteúdo da biblioteca documental na Pesquisa Cognitiva Azure.
manager: luisca
author: MarkHeff
ms.author: maheff
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/01/2021
ms.openlocfilehash: 5888a7cc8aa58d1c6edab191e1243ebc60000fd6
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2021
ms.locfileid: "105048872"
---
# <a name="how-to-configure-sharepoint-online-indexing-in-cognitive-search-preview"></a>Como configurar a indexação online do SharePoint em Pesquisa Cognitiva (pré-visualização)

> [!IMPORTANT] 
> O suporte do SharePoint Online encontra-se atualmente numa **pré-visualização pública fechada.** Pode solicitar o acesso à pré-visualização fechada preenchendo [este formulário](https://aka.ms/azure-cognitive-search/indexer-preview).
>
> A funcionalidade de pré-visualização é fornecida sem um contrato de nível de serviço, e não é recomendada para cargas de trabalho de produção. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
> 
> A [versão REST API 2020-06-30-Preview](search-api-preview.md) fornece esta funcionalidade. Não existe atualmente nenhum portal ou suporte SDK.

> [!NOTE]
> O SharePoint Online suporta um modelo de autorização granular que determina o acesso por utilizador ao nível do documento. O indexante SharePoint Online não puxa estas permissões para o índice de pesquisa, e a Pesquisa Cognitiva não suporta a autorização de nível de documento. Quando um documento é indexado do SharePoint Online a um serviço de pesquisa, o conteúdo está disponível para qualquer pessoa que tenha lido o acesso ao índice. Se necessitar de permissões ao nível do documento, deverá investigar filtros de segurança para aparar os resultados de conteúdo não autorizado. Para obter mais informações, consulte [aparar segurança utilizando identidades do Ative Directory](search-security-trimming-for-azure-search-with-aad.md).

Este artigo descreve como usar a Azure Cognitive Search para indexar documentos (tais como PDFs, documentos do Microsoft Office e vários outros formatos comuns) armazenados em bibliotecas de documentos SharePoint Online num índice de Pesquisa Cognitiva Azure. Primeiro, explica o básico da configuração e configuração do indexante. Depois, oferece uma exploração mais profunda de comportamentos e cenários que é provável que encontre.

## <a name="functionality"></a>Funcionalidade
Um indexante em Azure Cognitive Search é um crawler que extrai dados pes pesjáveis e metadados de uma fonte de dados. O indexador SharePoint Online ligar-se-á ao seu site SharePoint Online e a um índice de documentos de uma ou mais Bibliotecas documentais. O indexante fornece a seguinte funcionalidade:
+ Conteúdo de índice de uma ou mais Bibliotecas de Documentos Online SharePoint.
+ Conteúdo de índice das Bibliotecas de Documentos Online SharePoint que estão no mesmo inquilino que o seu serviço de Pesquisa Cognitiva Azure. O indexante não funcionará com sites SharePoint que estejam num inquilino diferente do seu serviço de Pesquisa Cognitiva Azure. 
+ O indexante apoiará a indexação incremental, o que significa que identificará qual o conteúdo alterado na Biblioteca documental e apenas indexa o conteúdo atualizado em futuras operações de indexação. Por exemplo, se 5 PDFs são originalmente indexados pelo indexante, então 1 é atualizado, então o indexante corre novamente, o indexante apenas indexará o 1 PDF que foi atualizado.
+ O texto e as imagens normalizadas serão extraídos por padrão dos documentos indexados. Opcionalmente, pode ser adicionado um skillset ao gasoduto para um maior enriquecimento de conteúdo. Mais informações sobre skillsets podem ser encontradas no artigo [Conceitos skillset em Azure Cognitive Search.](cognitive-search-working-with-skillsets.md)

## <a name="supported-document-formats"></a>Formatos de documento suportados

O indexador Azure Cognitive Search SharePoint Online pode extrair texto dos seguintes formatos de documento:

[!INCLUDE [search-document-data-sources](../../includes/search-blob-data-sources.md)]

## <a name="incremental-indexing-and-deletion-detection"></a>Deteção incremental de indexação e eliminação
Por predefinição, o indexante SharePoint Online suporta uma indexação incremental, o que significa que identificará qual o conteúdo na Biblioteca documental que mudou e apenas indexa o conteúdo atualizado em futuras operações de indexação. Por exemplo, se os documentos de 5 Palavras forem originalmente indexados pelo indexante, então 1 é atualizado, então o indexante corre novamente, o indexante apenas re-indexará o documento 1 Word que foi atualizado.

A deteção de eliminação também é suportada por padrão. Isto significa que se um documento for eliminado de uma biblioteca de documentos SharePoint Online, o indexante detetará a eliminação durante uma futura execução do indexante e removerá o documento do índice.

## <a name="setting-up-sharepoint-online-indexing"></a>Criação da indexação online do SharePoint
Para configurar o Índice Online SharePoint, terá de realizar algumas ações no portal Azure e algumas ações utilizando a pré-visualização REST API. Esta pré-visualização não é apoiada pelo SDK.

### <a name="step-1-enable-system-assigned-managed-identity"></a>Passo 1: Permitir o sistema atribuído identidade gerida
Quando uma identidade gerida atribuída ao sistema está ativada, o Azure cria uma identidade para o seu serviço de pesquisa que pode ser usado pelo indexante.

![Ativar a identidade gerida atribuída pelo sistema](media/search-howto-index-sharepoint-online/enable-managed-identity.png "Ativar a identidade gerida atribuída pelo sistema")

Depois de **selecionar, verá** um ID de objeto que foi atribuído ao seu serviço de pesquisa.

![Identidade gerida atribuída ao sistema](media/search-howto-index-sharepoint-online/system-assigned-managed-identity.png "Identidade gerida atribuída ao sistema")

### <a name="step-2-create-an-aad-application"></a>Passo 2: Criar uma aplicação AAD
O indexante SharePoint Online utilizará esta aplicação AAD para autenticação. 

1.  Navegue até ao [portal Azure.](https://portal.azure.com/)

1.  Abra o menu no lado esquerdo da página principal e selecione **O Diretório Ativo Azure** e, em seguida, selecione **registos de Aplicações**. Selecione **+ Novo registo.**
    1.  Forneça um nome para a sua aplicação.
    2.  Selecione **Único inquilino**.
    3.  Não é necessário redirecionar o URI.
    4.  Selecione **Registar-se**

1.  Selecione **permissões API** do menu à esquerda e, em seguida, **Adicione uma permissão**, em seguida, **Microsoft Graph** e depois **permissões delegadas**. Adicione as seguintes permissões API: 
    1.  **Delegado - Ficheiros.Ler.Todos** 
    2.  **Delegado - Sites.Read.All** 
    3.  **Delegado - Utilizador.Ler**

    ![Permissões delegadas da API](media/search-howto-index-sharepoint-online/delegated-api-permissions.png "Permissões delegadas da API")

    A utilização de permissões delegadas significa que o indexante acederá ao site SharePoint no contexto do utilizador. Assim, quando executar o indexante, só terá acesso ao conteúdo a que o utilizador iniciado tem acesso. O login do utilizador acontece ao criar o indexante ou atualizar a fonte de data. O passo de login é descrito mais tarde neste artigo.

1.  Selecione o **separador Autenticação.** Definir **Deixe o cliente público fluir** para **Sim** e, em seguida, selecione **Guardar**.

1.  **Selecione + Adicione uma plataforma,** em seguida, **aplicações móveis e desktop,** em seguida, verifique `https://login.microsoftonline.com/common/oauth2/nativeclient` , em seguida, **Configure**.

    ![Configuração de autenticação de aplicativos AAD](media/search-howto-index-sharepoint-online/aad-app-authentication-configuration.png "Configuração de autenticação de aplicativos AAD")

1.  Dê consentimento administrativo (apenas necessário para determinados inquilinos).

    Alguns inquilinos estão presos de tal forma que o consentimento administrativo é necessário para estas permissões delegadas da API. Se for esse o caso, terá de ter um consentimento administrativo para este pedido de AAD antes de criar o indexante. 

    Porque nem todos os inquilinos têm este requisito, recomendamos primeiro que saltem este passo e continuem com as instruções. Saberá se precisa de consentimento administrativo se ao criar o indexante, a autenticação não lhe diz que precisa de um administrador para aprovar a autenticação. Nesse caso, tenha um consentimento de administração de inquilino usando o botão abaixo.

    ![AAD app concede consentimento administrativo](media/search-howto-index-sharepoint-online/aad-app-grant-admin-consent.png "AAD app concede consentimento administrativo")

<a name="create-data-source"></a>

### <a name="step-3-create-data-source"></a>Passo 3: Criar fonte de dados
> [!IMPORTANT] 
> A partir desta secção, é necessário utilizar a API REST de pré-visualização para os restantes passos. Se não está familiarizado com a AZure Cognitive Search REST API, sugerimos dar uma olhada neste [Quickstart](search-get-started-rest.md).

Uma fonte de dados especifica quais os dados a indexar, credenciais necessárias para aceder aos dados e políticas para identificar de forma eficiente as alterações nos dados (linhas novas, modificadas ou eliminadas). Uma fonte de dados pode ser usada por vários indexadores no mesmo serviço de pesquisa.

Para a indexação do SharePoint, a fonte de dados deve ter as seguintes propriedades necessárias:
+ **nome** é o nome único da fonte de dados dentro do seu serviço de pesquisa.
+ **tipo** deve ser "sharepoint". Isto é sensível a casos.
+ **as credenciais** fornecem o ponto final do SharePoint Online e o ID de aplicação AAD (cliente). Um exemplo do sharePoint Online é `https://microsoft.sharepoint.com/teams/MySharePointSite` . Pode obter o ponto de final do SharePoint Online navegando na página inicial do seu site SharePoint e copiando o URL do navegador.
+ **o recipiente** especifica qual a biblioteca documental a indexar. Mais informações sobre a criação do contentor podem ser encontradas no [Controlo quais os documentos que estão indexados](#controlling-which-documents-are-indexed) nesta secção deste documento.

Para criar uma fonte de dados:

```http
POST https://[service name].search.windows.net/datasources?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [admin key]

{
    "name" : "sharepoint-datasource",
    "type" : "sharepoint",
    "credentials" : { "connectionString" : "SharePointOnlineEndpoint=[SharePoint Online site url];ApplicationId=[AAD App ID]" },
    "container" : { "name" : "defaultSiteLibrary", "query" : null }
}
```

### <a name="step-4-create-an-index"></a>Passo 4: Criar um índice
O índice especifica os campos num documento, atributos e outras construções que moldam a experiência de pesquisa.

Aqui está como criar um índice com um campo de conteúdo pesquisável para armazenar o texto extraído de documentos numa Biblioteca documental:

```http
POST https://[service name].search.windows.net/indexes?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "sharepoint-index",
    "fields": [
        { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
        { "name": "metadata_spo_item_name", "type": "Edm.String", "key": false, "searchable": true, "filterable": false, "sortable": false, "facetable": false },
        { "name": "metadata_spo_item_path", "type": "Edm.String", "key": false, "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "metadata_spo_item_content_type", "type": "Edm.String", "key": false, "searchable": false, "filterable": true, "sortable": false, "facetable": true },
        { "name": "metadata_spo_item_last_modified", "type": "Edm.DateTimeOffset", "key": false, "searchable": false, "filterable": false, "sortable": true, "facetable": false },
        { "name": "metadata_spo_item_size", "type": "Edm.Int64", "key": false, "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
    ]
}

```

Para obter mais informações, consulte [Create Index (REST API)](/rest/api/searchservice/create-index).

### <a name="step-5-create-an-indexer"></a>Passo 5: Criar um indexador
Um indexante conecta uma fonte de dados com um índice de pesquisa de destino e fornece um calendário para automatizar a atualização de dados. Uma vez criado o índice e a fonte de dados, está pronto para criar o indexante!

Durante esta secção, será solicitado que faça login com as credenciais da sua organização que tenham acesso ao site do SharePoint. Se possível, recomendamos a criação de uma nova conta de utilizador organizacional e dar a esse novo utilizador as permissões exatas que pretende que o indexante tenha.

Existem alguns passos para criar o indexante:

1.  Envie um pedido para tentar criar o indexante.

    ```http
    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30-Preview
    Content-Type: application/json
    api-key: [admin key]
    
        {
          "name" : "sharepoint-indexer",
          "dataSourceName" : "sharepoint-datasource",
          "targetIndexName" : "sharepoint-index",
          "fieldMappings" : [
            { 
              "sourceFieldName" : "metadata_spo_site_library_item_id", 
              "targetFieldName" : "id", 
              "mappingFunction" : { 
                "name" : "base64Encode" 
              } 
            }
          ]
        }
    
    ```

1.  Ao criar o indexador pela primeira vez, falhará e verá o seguinte erro. Aceda ao link na mensagem de erro. Se não for ao link dentro de 10 minutos, o código expirará e terá de recriar a fonte de [dados](#create-data-source).

    ```http
    {
        "error": {
            "code": "",
            "message": "Error with data source: To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code <CODE> to authenticate.  Please adjust your data source definition in order to proceed."
        }
    }
    ```

1.  Forneça o código que foi fornecido na mensagem de erro.

    ![Insira o código do dispositivo](media/search-howto-index-sharepoint-online/enter-device-code.png "Insira o código do dispositivo")

1.  O indexante SharePoint acederá ao conteúdo do SharePoint como utilizador inscrito. O utilizador que iniciar sessão durante este passo será o utilizador inscrito. Assim, se iniciar sessão com uma conta de utilizador que não tenha acesso a um documento na Biblioteca de Documentos que pretende indexar, o indexante não terá acesso a esse documento.

    Se possível, recomendamos a criação de uma nova conta de utilizador e dar a esse novo utilizador as permissões exatas que pretende que o indexante tenha.

1.  Aprovar as permissões que estão a ser solicitadas.

    ![Aprovar permissões da API](media/search-howto-index-sharepoint-online/aad-app-approve-api-permissions.png "Aprovar permissões da API")

1.  Reencaende o indexante criar pedido. Desta vez, o pedido deve ter sucesso. 

    ```http
    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30-Preview
    Content-Type: application/json
    api-key: [admin key]
    
    {
        "name" : "sharepoint-indexer",
        "dataSourceName" : "sharepoint-datasource",
        "targetIndexName" : "sharepoint-index"
    }
    ```

### <a name="step-6-check-the-indexer-status"></a>Passo 6: Verifique o estado do indexante
Após a criação do indexador, pode verificar o estado do indexante fazendo o seguinte pedido.

```http
GET https://[service name].search.windows.net/indexers/sharepoint-indexer/status?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [admin key]
```

Mais informações sobre o estado do indexante podem ser consultadas aqui: [Obter o Estado do Indexante](/rest/api/searchservice/get-indexer-status).

## <a name="updating-the-data-source"></a>Atualizar a fonte de dados
Se não houver atualizações no objeto de origem de dados, o indexante pode ser executado num horário sem qualquer interação do utilizador. No entanto, sempre que o objeto de origem de dados de pesquisa cognitiva Azure for atualizado, terá de iniciar sessão novamente para que o indexante seja executado. Por exemplo, se alterar a consulta de origem de dados, terá de iniciar sessão novamente utilizando o `https://microsoft.com/devicelogin` e um novo código.

Uma vez atualizada a fonte de dados, siga os passos abaixo:

1.  Inicia manualmente uma corrida do indexante.

    ```http
    POST https://[service name].search.windows.net/indexers/sharepoint-indexer/run?api-version=2020-06-30-Preview  
    Content-Type: application/json
    api-key: [admin key]
    ```

    Mais informações sobre o pedido de execução do indexante podem ser consultada aqui: [Run Indexer](/rest/api/searchservice/run-indexer).

1.  Verifique o estado do indexante. Se a última execução do indexante tiver um erro a dizer-lhe para `https://microsoft.com/devicelogin` ir, vá a essa página e forneça o novo código. 

    ```http
    GET https://[service name].search.windows.net/indexers/sharepoint-indexer/status?api-version=2020-06-30-Preview
    Content-Type: application/json
    api-key: [admin key]
    ```

    Mais informações sobre o estado do indexante podem ser consultadas aqui: [Obter o Estado do Indexante](/rest/api/searchservice/get-indexer-status).

1.  Iniciar sessão

1.  Inicie manualmente uma corrida de indexante novamente e verifique o estado do indexante. Desta vez, a corrida do indexante deve começar com sucesso.

## <a name="indexing-document-metadata"></a>Indexação de metadados de documentos
Se tiver definido o indexante para indexar metadados documentais, os seguintes metadados estarão disponíveis para indexar.

> [!NOTE]
> Os metadados personalizados não estão incluídos na versão atual da pré-visualização.

| Identificador | Tipo | Description | 
| ------------- | -------------- | ----------- |
| metadata_spo_site_library_item_id | Edm.String | A chave combinada do ID do site, iD da biblioteca e iD do item que identifica exclusivamente um item numa biblioteca de documentos para um site. |
| metadata_spo_site_id | Edm.String | O ID do site SharePoint Online. |
| metadata_spo_library_id | Edm.String | A identificação da biblioteca de documentos. |
| metadata_spo_item_id | Edm.String | O ID do item (documento) na biblioteca. |
| metadata_spo_item_last_modified | Edm.DateTimeOffset | A última data/hora modificada (UTC) do artigo. |
| metadata_spo_item_name | Edm.String | O nome do artigo. |
| metadata_spo_item_size | Edm.Int64 | O tamanho (em bytes) do item. | 
| metadata_spo_item_content_type | Edm.String | O tipo de conteúdo do item. | 
| metadata_spo_item_extension | Edm.String | A extensão do item. |
| metadata_spo_item_weburi | Edm.String | O URI do item. |
| metadata_spo_item_path | Edm.String | A combinação do caminho dos pais e do nome do item. | 

O indexante SharePoint Online também suporta metadados específicos de cada tipo de documento. Mais informações podem ser encontradas nas [propriedades de metadados de conteúdo utilizadas na Pesquisa Cognitiva Azure.](search-blob-metadata-properties.md)

<a name="controlling-which-documents-are-indexed"></a>

## <a name="controlling-which-documents-are-indexed"></a>Controlo dos documentos indexados
Um indexador único do SharePoint Online pode indexar o conteúdo de uma ou mais Bibliotecas documentais. Utilize o parâmetro do *recipiente* ao criar a sua fonte de dados para indicar as bibliotecas de documentos que pretende indexar. O recipiente de origem *de* dados tem duas propriedades: *nome* e *consulta.* 

### <a name="name"></a>Name
A propriedade *do nome* é necessária e deve ser um dos três valores:
+ *defaultSiteLibrary*
    + Indexe todo o conteúdo da biblioteca de documentos predefinidos dos sites.
+   *allSiteLibraries*
    + Indexe todo o conteúdo de todas as bibliotecas de documentos num site. Isto não indexará bibliotecas de documentos a partir de um subsite. Mas estes podem ser especificados na *consulta.*
+   *useQuery*
    + Apenas o conteúdo do índice definido na *consulta.*

### <a name="query"></a>Consulta
A *propriedade de consulta* é composta por pares de palavras-chave/valor. As palavras-chave abaixo são as palavras-chave que podem ser usadas. Os valores são urls de site ou urls de biblioteca de documentos.

> [!NOTE]
> Para obter o valor de uma determinada palavra-chave, recomendamos a abertura do SharePoint Online num browser, navegando para a Biblioteca de Documentos que está a tentar incluir/excluir e copiar o URI do navegador. Esta é a maneira mais fácil de obter o valor a usar com uma palavra-chave na consulta.

| Palavra-chave | Descrição de consulta | Exemplo |
| ------------- | -------------- | ----------- |
| nulo | Se nula ou vazia, indexe a biblioteca de documentos predefinidos ou todas as bibliotecas documentais dependendo do nome do recipiente. | Indexar todos os conteúdos da biblioteca do site predefinido: <br><br>  ``` "container" : { "name" : "defaultSiteLibrary", "query" : null } ``` |
| incluem LibrariesInSite | Conteúdo de índice de todas as bibliotecas em site definido na cadeia de ligação. Estes estão limitados a subsites do seu site <br><br> O valor *de consulta* desta palavra-chave deve ser o URI do site ou subsite. | Indexe todos os conteúdos de todas as bibliotecas de documentos no meu local. <br><br> ``` "container" : { "name" : "useQuery", "query" : "includeLibrariesInSite=https://mycompany.sharepoint.com/mysite" } ``` |
| incluir Library | Conteúdo de índice desta biblioteca. <br><br> O valor *de consulta* desta palavra-chave deve estar num dos seguintes formatos: <br><br> Exemplo 1: <br><br> *incluemLibrary=[site ou subsite]/[biblioteca de documentos]* <br><br> Exemplo 2: <br><br> URI copiado do seu navegador. | Indexe todos os conteúdos do MyDocumentLibrary: <br><br> Exemplo 1: <br><br> ``` "container" : { "name" : "useQuery", "query" : "includeLibrary=https://mycompany.sharepoint.com/mysite/MyDocumentLibrary" } ``` <br><br> Exemplo 2: <br><br> ``` "container" : { "name" : "useQuery", "query" : "includeLibrary=https://mycompany.sharepoint.com/teams/mysite/MyDocumentLibrary/Forms/AllItems.aspx" } ``` |
| excluirLibraria |  Não indexe o conteúdo desta biblioteca. <br><br> O valor *de consulta* desta palavra-chave deve estar num dos seguintes formatos: <br><br> Exemplo 1: <br><br> *excluirLibrary=[site ou subsite URI]/[biblioteca de documentos]* <br><br> Exemplo 2: <br><br> URI copiado do seu navegador. | Indexe todos os conteúdos de todas as minhas bibliotecas, com exceção do MyDocumentLibrary: <br><br> Exemplo 1: <br><br> ``` "container" : { "name" : "useQuery", "query" : "includeLibrariesInSite=https://mysite.sharepoint.com/subsite1; excludeLibrary=https://mysite.sharepoint.com/subsite1/MyDocumentLibrary" } ``` <br><br> Exemplo 2: <br><br> ``` "container" : { "name" : "useQuery", "query" : "includeLibrariesInSite=https://mycompany.sharepoint.com/teams/mysite; excludeLibrary=https://mycompany.sharepoint.com/teams/mysite/MyDocumentLibrary/Forms/AllItems.aspx" } ``` |

## <a name="index-by-file-type"></a>Índice por tipo de ficheiro
Pode controlar quais os documentos indexados e que são ignorados.

### <a name="include-documents-having-specific-file-extensions"></a>Incluir documentos com extensões de ficheiros específicas
Só pode indexar os documentos com as extensões de nome de ficheiro que especifica utilizando o parâmetro de configuração do `indexedFileNameExtensions` indexante. O valor é uma cadeia que contém uma lista separada por vírgula de extensões de ficheiros (com um ponto principal). Por exemplo, para indexar apenas o . PDF e . Documentos DOCX, faça isto:

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [admin key]

{
    ... other parts of indexer definition
    "parameters" : { "configuration" : { "indexedFileNameExtensions" : ".pdf,.docx" } }
}
```

### <a name="exclude-documents-having-specific-file-extensions"></a>Excluir documentos com extensões de ficheiros específicas
Pode excluir documentos com extensões específicas de nome de ficheiros da indexação utilizando o `excludedFileNameExtensions` parâmetro de configuração. O valor é uma cadeia que contém uma lista separada por vírgula de extensões de ficheiros (com um ponto principal). Por exemplo, para indexar todos os conteúdos, exceto aqueles com o . PNG e . Extensões JPEG, faça isto:

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [admin key]

{
    ... other parts of indexer definition
    "parameters" : { "configuration" : { "excludedFileNameExtensions" : ".png,.jpeg" } }
}
```

Se ambos `indexedFileNameExtensions` e `excludedFileNameExtensions` parâmetros estiverem presentes, a Azure Cognitive Search primeiro olha `indexedFileNameExtensions` para , em seguida, em `excludedFileNameExtensions` . Isto significa que, se a mesma extensão de ficheiro estiver presente em ambas as listas, será excluída da indexação.

## <a name="handling-errors"></a>Processar erros
Por predefinição, o indexante SharePoint Online para assim que encontra um documento com um tipo de conteúdo não suportado (por exemplo, uma imagem). É claro que pode utilizar o `excludedFileNameExtensions` parâmetro para saltar certos tipos de conteúdo. No entanto, poderá ser necessário indexar documentos sem conhecer antecipadamente todos os tipos de conteúdo possíveis. Para continuar a indexar quando for encontrado um tipo de conteúdo não suportado, defina o parâmetro de `failOnUnsupportedContentType` configuração em falso:

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [admin key]

{
    ... other parts of indexer definition
    "parameters" : { "configuration" : { "failOnUnsupportedContentType" : false } }
}
```

Para alguns documentos, a Azure Cognitive Search não consegue determinar o tipo de conteúdo, ou não é capaz de processar um documento do tipo de conteúdo suportado de outra forma. Para ignorar este modo de falha, desa ajuste o parâmetro de `failOnUnprocessableDocument` configuração para falso:

```http
"parameters" : { "configuration" : { "failOnUnprocessableDocument" : false } }
```

A Azure Cognitive Search limita o tamanho dos documentos que estão indexados. Estes limites estão documentados nos [Limites de Serviço na Pesquisa Cognitiva Azure.](./search-limits-quotas-capacity.md) Documentos de grandes dimensões são tratados como erros por defeito. No entanto, ainda pode indexar metadados de armazenamento de documentos de grandes dimensões se definir `indexStorageMetadataOnlyForOversizedDocuments` o parâmetro de configuração para ser verdadeiro:

```http
"parameters" : { "configuration" : { "indexStorageMetadataOnlyForOversizedDocuments" : true } }
```

Também pode continuar a indexar se os erros ocorrerem em qualquer ponto de processamento, quer enquanto analisa documentos, quer adicionando documentos a um índice. Para ignorar um número específico de erros, desa um determinado `maxFailedItems` parâmetros e `maxFailedItemsPerBatch` configurar para os valores pretendidos. Por exemplo:

```http
{
    ... other parts of indexer definition
    "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 10 }
}
```

## <a name="see-also"></a>Ver também
+ [Indexadores na Pesquisa Cognitiva do Azure](search-indexer-overview.md)
+ [Propriedades de metadados de conteúdo utilizadas na Pesquisa Cognitiva Azure](search-blob-metadata-properties.md)