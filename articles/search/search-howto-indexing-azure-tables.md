---
title: Indexar conteúdo do armazenamento de tabelas do Azure para pesquisa em texto completo - Azure Search
description: Aprenda a indexar dados armazenados no armazenamento de tabelas do Azure com um indexador de Azure Search.
ms.date: 03/01/2019
author: mgottein
manager: cgronlun
ms.author: magottei
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: e1b411ab54a5b666849893ba9d246eff85e7e54e
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2019
ms.locfileid: "57306889"
---
# <a name="index-azure-table-storage-with-azure-search"></a>Armazenamento de tabelas do índice do Azure com o Azure Search
Este artigo mostra como utilizar o Azure Search para dados de índice armazenados no armazenamento de tabelas do Azure.

## <a name="set-up-azure-table-storage-indexing"></a>Configurar a indexação do armazenamento de tabelas do Azure

Pode configurar um indexador do armazenamento de tabelas do Azure ao utilizar estes recursos:

* [Portal do Azure](https://ms.portal.azure.com)
* O Azure Search [REST API](https://docs.microsoft.com/rest/api/searchservice/Indexer-operations)
* O Azure Search [.NET SDK](https://aka.ms/search-sdk)

Aqui Vamos demonstrar o fluxo com a API de REST. 

### <a name="step-1-create-a-datasource"></a>Passo 1: Criar uma origem de dados

Uma origem de dados especifica os dados a indexar, as credenciais necessárias para acessar os dados e as políticas que permitem a Azure Search identificar com eficiência as alterações nos dados.

Para tabela indexação, a origem de dados tem de ter as seguintes propriedades:

- **nome** é o nome exclusivo da origem de dados dentro do seu serviço de pesquisa.
- **tipo** tem de ser `azuretable`.
- **credenciais** parâmetro contém a cadeia de ligação de conta de armazenamento. Consulte a [especificar as credenciais](#Credentials) secção para obter detalhes.
- **contentor** define o nome da tabela e uma consulta opcional.
    - Especifique o nome da tabela com o `name` parâmetro.
    - Opcionalmente, especifique uma consulta com o `query` parâmetro. 

> [!IMPORTANT] 
> Sempre que possível, utilize um filtro no PartitionKey para um melhor desempenho. Qualquer outra consulta faz uma análise da tabela completa, resultando num fraco desempenho para tabelas grandes. Consulte a [considerações de desempenho](#Performance) secção.


Para criar uma origem de dados:

    POST https://[service name].search.windows.net/datasources?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "table-datasource",
        "type" : "azuretable",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-table", "query" : "PartitionKey eq '123'" }
    }   

Para obter mais informações sobre a API para criar origem de dados, consulte [criar origem de dados](https://docs.microsoft.com/rest/api/searchservice/create-data-source).

<a name="Credentials"></a>
#### <a name="ways-to-specify-credentials"></a>Maneiras de especificar credenciais ####

Pode fornecer as credenciais para a tabela de uma das seguintes formas: 

- **Cadeia de ligação da conta de armazenamento de acesso total**: `DefaultEndpointsProtocol=https;AccountName=<your storage account>;AccountKey=<your account key>` Pode obter a cadeia de ligação do portal do Azure ao aceder a **painel de conta de armazenamento** > **definições** > **chaves** (para a clássica contas de armazenamento) ou **configurações** > **chaves de acesso** (para contas de armazenamento do Azure Resource Manager).
- **Conta de armazenamento partilhado a cadeia de ligação de assinatura de acesso**: `TableEndpoint=https://<your account>.table.core.windows.net/;SharedAccessSignature=?sv=2016-05-31&sig=<the signature>&spr=https&se=<the validity end time>&srt=co&ss=t&sp=rl` A assinatura de acesso partilhado deve ter a lista e permissões de leitura no contentores (tabelas neste caso) e objetos (linhas de tabela).
-  **Assinatura de acesso partilhado de tabela**: `ContainerSharedAccessUri=https://<your storage account>.table.core.windows.net/<table name>?tn=<table name>&sv=2016-05-31&sig=<the signature>&se=<the validity end time>&sp=r` A assinatura de acesso partilhado deve ter permissões de consulta (ler) na tabela.

Para obter mais informações sobre o armazenamento partilhado assinaturas de acesso, consulte [assinaturas de acesso partilhado do Using](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

> [!NOTE]
> Se utilizar credenciais de assinatura de acesso partilhado, terá de atualizar as credenciais de origem de dados periodicamente com assinaturas renovadas para evitar que sua expiração. Se as credenciais de assinatura de acesso partilhado expirarem, o indexador irá falhar com uma mensagem de erro semelhante a "Credenciais fornecidas na cadeia de ligação são inválidas ou tem expirado."  

### <a name="step-2-create-an-index"></a>Passo 2: Criar um índice
O índice Especifica os campos num documento, os atributos, e a experiência de outras construções que a pesquisa de forma.

Para criar um índice:

    POST https://[service name].search.windows.net/indexes?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "key", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "SomeColumnInMyTable", "type": "Edm.String", "searchable": true }
          ]
    }

Para obter mais informações sobre a criação de índices, consulte [Create Index](https://docs.microsoft.com/rest/api/searchservice/create-index).

### <a name="step-3-create-an-indexer"></a>Passo 3: Criar um indexador
Um indexador liga uma origem de dados com um índice de pesquisa de destino e fornece uma agenda para automatizar a atualização de dados. 

Depois do índice e a origem de dados são criados, está pronto para criar o indexador:

    POST https://[service name].search.windows.net/indexers?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "table-indexer",
      "dataSourceName" : "table-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" }
    }

Este indexador é executado a cada duas horas. (O intervalo de agendamento está definido como "PT2H".) Para executar um indexador a cada 30 minutos, defina o intervalo para "PT30M". O mais curto intervalo suportado é de cinco minutos. A agenda é opcional; Se for omitido, um indexador é executado apenas uma vez, quando é criado. No entanto, pode executar um indexador a pedido em qualquer altura.   

Para obter mais informações sobre a API para criar indexador, consulte [criar indexador](https://docs.microsoft.com/rest/api/searchservice/create-indexer).

## <a name="deal-with-different-field-names"></a>Lidar com os nomes de campos diferentes
Às vezes, os nomes de campos no seu índice existente são diferentes dos nomes de propriedade na sua tabela. Pode usar os mapeamentos de campo para mapear os nomes das propriedades da tabela para os nomes de campos no seu índice de pesquisa. Para saber mais sobre os mapeamentos de campo, veja [mapeamentos de campo do indexador de Azure Search preenchem as diferenças entre os índices de pesquisa e origens de dados](search-indexer-field-mappings.md).

## <a name="handle-document-keys"></a>Lidar com chaves de documento
No Azure Search, a chave do documento identifica exclusivamente um documento. Todos os índices de pesquisa tem de ter exatamente um campo de chave do tipo `Edm.String`. O campo de chave é necessário para cada documento que está a ser adicionado ao índice. (Na verdade, é o único campo obrigatório.)

Uma vez que linhas da tabela tem uma chave composta, o Azure Search gera um campo sintético chamado `Key` que é uma concatenação dos valores de chave linha e de chave de partição. Por exemplo, se uma linha 's PartitionKey é `PK1` e é RowKey `RK1`, o `Key` é o valor do campo `PK1RK1`.

> [!NOTE]
> O `Key` valor pode conter carateres que são inválidas no chaves de documento, como travessões. Pode lidar com carateres inválidos ao utilizar o `base64Encode` [função de mapeamento de campo](search-indexer-field-mappings.md#base64EncodeFunction). Se fizer isso, não se esqueça de também usar a codificação de Base64 de seguros de URL ao transmitir as chaves de documento na API de chamadas, como pesquisa.
>
>

## <a name="incremental-indexing-and-deletion-detection"></a>Deteção de indexação e a eliminação incremental
Quando configurar um indexador de tabelas para executar com base numa agenda, ele reindexes linhas apenas novas ou atualizadas, conforme determinado por uma linha `Timestamp` valor. Não precisa especificar uma política de deteção de alteração. A indexação incremental é automaticamente ativado para si.

Para indicar que determinados documentos têm de ser removidos do índice, pode usar uma estratégia de eliminação de forma recuperável. Em vez de excluir uma linha, adicione uma propriedade para indicar que foi eliminada e configurar uma política de deteção de eliminação de forma recuperável na origem de dados. Por exemplo, a seguinte política considera que uma linha é eliminada se a linha tem uma propriedade `IsDeleted` com o valor `"true"`:

    PUT https://[service name].search.windows.net/datasources?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "my-table-datasource",
        "type" : "azuretable",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "table name", "query" : "<query>" },
        "dataDeletionDetectionPolicy" : { "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy", "softDeleteColumnName" : "IsDeleted", "softDeleteMarkerValue" : "true" }
    }   

<a name="Performance"></a>
## <a name="performance-considerations"></a>Considerações de desempenho

Por predefinição, o Azure Search utiliza o seguinte filtro de consulta: `Timestamp >= HighWaterMarkValue`. Uma vez que as tabelas do Azure não tem um índice secundário no `Timestamp` campo, esse tipo de consulta requer a análise da tabela completa e, portanto, lento para tabelas grandes.


Seguem-se duas abordagens possíveis para melhorar o desempenho de indexação de tabela. Ambas as abordagens dependem da utilização de partições de tabela: 

- Se seus dados naturalmente podem ser particionados em vários intervalos de partição, crie uma origem de dados e um indexador correspondente para cada intervalo de partição. Agora, cada indexador tem de processar apenas um partição específica intervalo, resultando num melhor desempenho de consulta. Se os dados que precisam ser indexados tem um pequeno número de partições fixos, ainda melhores: cada indexador apenas faz uma análise de partição. Por exemplo, para criar uma origem de dados para o processamento de um intervalo de partição com chaves a partir `000` para `100`, utilize uma consulta como esta: 
    ```
    "container" : { "name" : "my-table", "query" : "PartitionKey ge '000' and PartitionKey lt '100' " }
    ```

- Se seus dados estão particionados por tempo (por exemplo, crie uma nova partição de cada dia ou da semana), considere a seguinte abordagem: 
    - Utilizar uma consulta do formulário: `(PartitionKey ge <TimeStamp>) and (other filters)`. 
    - Monitorizar o progresso do indexador utilizando [obter API de estado de indexador](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status)e atualizar periodicamente o `<TimeStamp>` condição da consulta com base no valor de marca de água alta mais recente concluída com êxito. 
    - Com esta abordagem, se precisar disparar uma reindexação completa, terá de repor a consulta de origem de dados, além de redefinir o indexador. 


## <a name="help-us-make-azure-search-better"></a>Ajude-na melhorar o Azure Search
Se tiver de pedidos de funcionalidades ou ideias para melhorias, submeta-as no nosso [site do UserVoice](https://feedback.azure.com/forums/263029-azure-search/).
