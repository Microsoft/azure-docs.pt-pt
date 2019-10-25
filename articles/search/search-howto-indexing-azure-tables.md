---
title: Indexar conteúdo do armazenamento de tabelas do Azure para pesquisa de texto completo
titleSuffix: Azure Cognitive Search
description: Saiba como indexar dados armazenados no armazenamento de tabelas do Azure com um indexador Pesquisa Cognitiva do Azure.
manager: nitinme
author: mgottein
ms.author: magottei
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: ae99145178fba8e204267546dc1cedf42df412eb
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72793737"
---
# <a name="how-to-index-tables-from-azure-table-storage-with-azure-cognitive-search"></a>Como indexar tabelas do armazenamento de tabelas do Azure com o Azure Pesquisa Cognitiva

Este artigo mostra como usar os Pesquisa Cognitiva do Azure para indexar dados armazenados no armazenamento de tabelas do Azure.

## <a name="set-up-azure-table-storage-indexing"></a>Configurar a indexação do armazenamento de tabelas do Azure

Você pode configurar um indexador de armazenamento de tabela do Azure usando estes recursos:

* [Portal do Azure](https://ms.portal.azure.com)
* [API REST](https://docs.microsoft.com/rest/api/searchservice/Indexer-operations) do Azure pesquisa cognitiva
* SDK do [.net](https://aka.ms/search-sdk) pesquisa cognitiva do Azure

Aqui, demonstramos o fluxo usando a API REST. 

### <a name="step-1-create-a-datasource"></a>Etapa 1: criar uma fonte de fontes

Um DataSource especifica quais dados indexar, as credenciais necessárias para acessar os dados e as políticas que permitem ao Azure Pesquisa Cognitiva identificar com eficiência as alterações nos dados.

Para a indexação de tabela, a fonte de fontes deve ter as seguintes propriedades:

- **nome** é o nome exclusivo da fonte de origem no serviço de pesquisa.
- o **tipo** deve ser `azuretable`.
- o parâmetro de **credenciais** contém a cadeia de conexão da conta de armazenamento. Consulte a seção [especificar credenciais](#Credentials) para obter detalhes.
- **contêiner** define o nome da tabela e uma consulta opcional.
    - Especifique o nome da tabela usando o parâmetro `name`.
    - Opcionalmente, especifique uma consulta usando o parâmetro `query`. 

> [!IMPORTANT] 
> Sempre que possível, use um filtro em PartitionKey para melhorar o desempenho. Qualquer outra consulta faz uma verificação de tabela completa, resultando em baixo desempenho para tabelas grandes. Consulte a seção [Considerações sobre desempenho](#Performance) .


Para criar uma fonte de fontes:

    POST https://[service name].search.windows.net/datasources?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "table-datasource",
        "type" : "azuretable",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-table", "query" : "PartitionKey eq '123'" }
    }   

Para obter mais informações sobre a API criar fonte de dados, consulte [criar fonte](https://docs.microsoft.com/rest/api/searchservice/create-data-source)de dados.

<a name="Credentials"></a>
#### <a name="ways-to-specify-credentials"></a>Maneiras de especificar credenciais ####

Você pode fornecer as credenciais para a tabela de uma das seguintes maneiras: 

- **Cadeia de conexão da conta de armazenamento de acesso completo**: `DefaultEndpointsProtocol=https;AccountName=<your storage account>;AccountKey=<your account key>` você pode obter a cadeia de conexão do portal do Azure acessando a **folha da conta de armazenamento** > **configurações** > **chaves** (para contas de armazenamento clássicas) ou **Configurações** > **chaves de acesso** (para contas de armazenamento de Azure Resource Manager).
- **Cadeia de conexão de assinatura de acesso compartilhado da conta de armazenamento**: `TableEndpoint=https://<your account>.table.core.windows.net/;SharedAccessSignature=?sv=2016-05-31&sig=<the signature>&spr=https&se=<the validity end time>&srt=co&ss=t&sp=rl` a assinatura de acesso compartilhado deve ter as permissões de lista e leitura em contêineres (tabelas neste caso) e objetos (linhas de tabela).
-  **Assinatura de acesso compartilhado de tabela**: `ContainerSharedAccessUri=https://<your storage account>.table.core.windows.net/<table name>?tn=<table name>&sv=2016-05-31&sig=<the signature>&se=<the validity end time>&sp=r` a assinatura de acesso compartilhado deve ter permissões de consulta (leitura) na tabela.

Para obter mais informações sobre assinaturas de acesso compartilhado de armazenamento, consulte [usando assinaturas de acesso compartilhado](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

> [!NOTE]
> Se você usar credenciais de assinatura de acesso compartilhado, precisará atualizar as credenciais de DataSource periodicamente com assinaturas renovadas para evitar sua expiração. Se as credenciais de assinatura de acesso compartilhado expirarem, o indexador falhará com uma mensagem de erro semelhante a "as credenciais fornecidas na cadeia de conexão são inválidas ou expiraram."  

### <a name="step-2-create-an-index"></a>Passo 2: criar um índice
O índice especifica os campos em um documento, os atributos e outras construções que modelam a experiência de pesquisa.

Para criar um índice:

    POST https://[service name].search.windows.net/indexes?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "key", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "SomeColumnInMyTable", "type": "Edm.String", "searchable": true }
          ]
    }

Para obter mais informações sobre como criar índices, consulte [criar índice](https://docs.microsoft.com/rest/api/searchservice/create-index).

### <a name="step-3-create-an-indexer"></a>Etapa 3: criar um indexador
Um indexador conecta uma fonte de dados a um índice de pesquisa de destino e fornece uma agenda para automatizar a atualização de data. 

Depois que o índice e a fonte de fontes forem criados, você estará pronto para criar o indexador:

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "table-indexer",
      "dataSourceName" : "table-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" }
    }

Esse indexador é executado a cada duas horas. (O intervalo de agendamento é definido como "PT2H".) Para executar um indexador a cada 30 minutos, defina o intervalo como "PT30M". O intervalo mais curto com suporte é de cinco minutos. A agenda é opcional; Se omitido, um indexador será executado apenas uma vez quando ele for criado. No entanto, você pode executar um indexador sob demanda a qualquer momento.   

Para obter mais informações sobre a API criar indexador, consulte [criar indexador](https://docs.microsoft.com/rest/api/searchservice/create-indexer).

Para obter mais informações sobre como definir agendas do indexador, consulte [como agendar indexadores para o Azure pesquisa cognitiva](search-howto-schedule-indexers.md).

## <a name="deal-with-different-field-names"></a>Lidar com nomes de campos diferentes
Às vezes, os nomes de campo no índice existente são diferentes dos nomes de propriedade em sua tabela. Você pode usar mapeamentos de campo para mapear os nomes de propriedade da tabela para os nomes de campo no índice de pesquisa. Para saber mais sobre mapeamentos de campo, confira [mapeamentos de campo do indexador de pesquisa cognitiva do Azure ponte das diferenças entre fontes de pesquisa e índices](search-indexer-field-mappings.md).

## <a name="handle-document-keys"></a>Manipular chaves de documento
No Azure Pesquisa Cognitiva, a chave do documento identifica exclusivamente um documento. Cada índice de pesquisa deve ter exatamente um campo de chave do tipo `Edm.String`. O campo de chave é necessário para cada documento que está sendo adicionado ao índice. (Na verdade, é o único campo obrigatório.)

Como as linhas de tabela têm uma chave composta, o Azure Pesquisa Cognitiva gera um campo sintético chamado `Key` que é uma concatenação dos valores de chave de partição e de linha de coluna. Por exemplo, se PartitionKey de uma linha for `PK1` e RowKey for `RK1`, o valor do campo `Key` será `PK1RK1`.

> [!NOTE]
> O valor de `Key` pode conter caracteres inválidos em chaves de documento, como traços. Você pode lidar com caracteres inválidos usando a [função de mapeamento de campo](search-indexer-field-mappings.md#base64EncodeFunction)`base64Encode`. Se o fizer, lembre-se também de utilizar a codificação Base64 segura para URL ao passar chaves de documentos em chamadas à API, como, por exemplo, Lookup.
>
>

## <a name="incremental-indexing-and-deletion-detection"></a>Indexação incremental e detecção de exclusão
Quando você configura um indexador de tabela para ser executado em um agendamento, ele reindexa apenas as linhas novas ou atualizadas, conforme determinado pelo valor de `Timestamp` de uma linha. Você não precisa especificar uma política de detecção de alteração. A indexação incremental é habilitada para você automaticamente.

Para indicar que determinados documentos devem ser removidos do índice, você pode usar uma estratégia de exclusão reversível. Em vez de excluir uma linha, adicione uma propriedade para indicar que ela foi excluída e configure uma política de detecção de exclusão reversível na fonte de fontes. Por exemplo, a política a seguir considerará que uma linha será excluída se a linha tiver uma propriedade `IsDeleted` com o valor `"true"`:

    PUT https://[service name].search.windows.net/datasources?api-version=2019-05-06
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

Por padrão, o Azure Pesquisa Cognitiva usa o seguinte filtro de consulta: `Timestamp >= HighWaterMarkValue`. Como as tabelas do Azure não têm um índice secundário no campo `Timestamp`, esse tipo de consulta requer uma verificação de tabela completa e, portanto, é lento para tabelas grandes.


Aqui estão duas abordagens possíveis para melhorar o desempenho de indexação de tabela. Ambas as abordagens dependem do uso de partições de tabela: 

- Se seus dados puderem ser naturalmente particionados em vários intervalos de partição, crie uma fonte de dados e um indexador correspondente para cada intervalo de partição. Cada indexador agora precisa processar apenas um intervalo de partição específico, resultando em melhor desempenho de consulta. Se os dados que precisam ser indexados tiverem um pequeno número de partições fixas, ainda melhor: cada indexador faz apenas uma verificação de partição. Por exemplo, para criar uma fonte de fontes para processar um intervalo de partição com chaves de `000` para `100`, use uma consulta como esta: 
    ```
    "container" : { "name" : "my-table", "query" : "PartitionKey ge '000' and PartitionKey lt '100' " }
    ```

- Se os dados forem particionados por tempo (por exemplo, você cria uma nova partição todos os dias ou semanas), considere a seguinte abordagem: 
    - Use uma consulta do formulário: `(PartitionKey ge <TimeStamp>) and (other filters)`. 
    - Monitore o progresso do indexador usando a [API obter status do indexador](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status)e atualize periodicamente a condição de `<TimeStamp>` da consulta com base no valor de marca d' água alta com êxito mais recente. 
    - Com essa abordagem, se você precisar disparar uma reindexação completa, será necessário redefinir a consulta DataSource, além de redefini-lo. 


## <a name="help-us-make-azure-cognitive-search-better"></a>Ajude-nos a tornar o Azure Pesquisa Cognitiva melhor
Se você tiver solicitações de recursos ou ideias para melhorias, envie-as em nosso [site UserVoice](https://feedback.azure.com/forums/263029-azure-search/).
