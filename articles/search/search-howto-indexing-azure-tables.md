---
title: Pesquisa sobre o conteúdo de armazenamento da Azure Table
titleSuffix: Azure Cognitive Search
description: Saiba como indexar os dados armazenados no armazenamento da Tabela Azure com um indexador de pesquisa cognitiva Azure.
manager: nitinme
author: mgottein
ms.author: magottei
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 07/11/2020
ms.openlocfilehash: 2c67cd4d071660da2ca5714623695ca434329263
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91275188"
---
# <a name="how-to-index-tables-from-azure-table-storage-with-azure-cognitive-search"></a>Como indexar tabelas a partir do armazenamento da Tabela Azure com Azure Cognitive Search

Este artigo mostra como usar a Azure Cognitive Search para indexar dados armazenados no armazenamento da Tabela Azure.

## <a name="set-up-azure-table-storage-indexing"></a>Configurar a indexação de armazenamento da tabela Azure

Pode configurar um indexador de armazenamento de mesa Azure utilizando estes recursos:

* [Portal do Azure](https://ms.portal.azure.com)
* Azure Cognitive Search [REST API](/rest/api/searchservice/Indexer-operations)
* Pesquisa Cognitiva Azure [.NET SDK](/dotnet/api/overview/azure/search)

Aqui demonstramos o fluxo utilizando a API REST. 

### <a name="step-1-create-a-datasource"></a>Passo 1: Criar uma fonte de dados

Um fonte de dados especifica quais os dados a indexar, as credenciais necessárias para aceder aos dados, e as políticas que permitem ao Azure Cognitive Search identificar de forma eficiente as alterações nos dados.

Para a indexação da tabela, o dado fonte deve ter as seguintes propriedades:

- **nome** é o nome único da fonte de dados dentro do seu serviço de pesquisa.
- **tipo** deve ser `azuretable` .
- O parâmetro **de credenciais** contém a cadeia de ligação da conta de armazenamento. Consulte a secção [de credenciais Especificar](#Credentials) para mais detalhes.
- **recipiente** define o nome da mesa e uma consulta opcional.
    - Especifique o nome da tabela utilizando o `name` parâmetro.
    - Opcionalmente, especifique uma consulta utilizando o `query` parâmetro. 

> [!IMPORTANT] 
> Sempre que possível, utilize um filtro no PartitionKey para obter um melhor desempenho. Qualquer outra consulta faz uma varredura de mesa completa, resultando num mau desempenho para mesas grandes. Consulte a secção [de considerações de desempenho.](#Performance)


Para criar um dadosource:

```http
    POST https://[service name].search.windows.net/datasources?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "table-datasource",
        "type" : "azuretable",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-table", "query" : "PartitionKey eq '123'" }
    }   
```

Para obter mais informações sobre a API de Fonte de Dados, consulte [Criar Fonte de Dados.](/rest/api/searchservice/create-data-source)

<a name="Credentials"></a>
#### <a name="ways-to-specify-credentials"></a>Formas de especificar credenciais ####

Pode fornecer as credenciais para a mesa de uma destas formas: 

- **Cadeia de ligação de identidade gerida**: `ResourceId=/subscriptions/<your subscription ID>/resourceGroups/<your resource group name>/providers/Microsoft.Storage/storageAccounts/<your storage account name>/;` Esta cadeia de ligação não requer uma chave de conta, mas deve seguir as instruções para [configurar uma ligação a uma conta de Armazenamento Azure utilizando uma identidade gerida](search-howto-managed-identities-storage.md).
- **Cadeia de ligação à conta de armazenamento de acesso completo**: Pode obter a cadeia de `DefaultEndpointsProtocol=https;AccountName=<your storage account>;AccountKey=<your account key>` ligação do portal Azure indo para as chaves de chaves de chaves de **chaves de chaves de conta de armazenamento de armazenamento**  >  **Settings**  >  **Keys** (para contas de armazenamento clássico) ou chaves de acesso **de definições**  >  **Access keys** (para contas de armazenamento do Gestor de Recursos Azure).
- **Cadeia de ligação de assinatura de acesso partilhado**da conta de armazenamento : A assinatura de acesso partilhado deve ter a lista e ler `TableEndpoint=https://<your account>.table.core.windows.net/;SharedAccessSignature=?sv=2016-05-31&sig=<the signature>&spr=https&se=<the validity end time>&srt=co&ss=t&sp=rl` permissões em recipientes (tabelas neste caso) e objetos (linhas de mesa).
-  **Assinatura de acesso partilhado da tabela**: A assinatura de acesso partilhado deve ter `ContainerSharedAccessUri=https://<your storage account>.table.core.windows.net/<table name>?tn=<table name>&sv=2016-05-31&sig=<the signature>&se=<the validity end time>&sp=r` permissões de consulta (ler) na tabela.

Para obter mais informações sobre as assinaturas de acesso partilhado de armazenamento, consulte [utilização de assinaturas de acesso partilhado.](../storage/common/storage-sas-overview.md)

> [!NOTE]
> Se utilizar credenciais de assinatura de acesso partilhado, terá de atualizar periodicamente as credenciais de fonte de dados com assinaturas renovadas para impedir a sua expiração. Se as credenciais de assinatura de acesso partilhado expirarem, o indexante falha com uma mensagem de erro semelhante a "As credenciais fornecidas na cadeia de ligação são inválidas ou caducaram".  

### <a name="step-2-create-an-index"></a>Passo 2: criar um índice
O índice especifica os campos num documento, os atributos e outras construções que moldam a experiência de pesquisa.

Para criar um índice:

```http
    POST https://[service name].search.windows.net/indexes?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "key", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "SomeColumnInMyTable", "type": "Edm.String", "searchable": true }
          ]
    }
```

Para obter mais informações sobre a criação de índices, consulte [Criar Índice.](/rest/api/searchservice/create-index)

### <a name="step-3-create-an-indexer"></a>Passo 3: Criar um indexador
Um indexante conecta uma fonte de dados com um índice de pesquisa alvo e fornece um calendário para automatizar a atualização de dados. 

Após a criação do índice e da fonte de dados, está pronto para criar o indexador:

```http
    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "table-indexer",
      "dataSourceName" : "table-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" }
    }
```

Este indexante funciona a cada duas horas. (O intervalo de horário é definido para "PT2H".) Para executar um indexante a cada 30 minutos, desajuste o intervalo para "PT30M". O intervalo mais curto suportado é de cinco minutos. O horário é opcional; se omitido, um indexante funciona apenas uma vez quando é criado. No entanto, pode executar um indexante a qualquer momento.   

Para obter mais informações sobre a API do Indexante Criar, consulte [Criar Indexer](/rest/api/searchservice/create-indexer).

Para obter mais informações sobre a definição de horários de indexantes, consulte [Como agendar indexadores para a Pesquisa Cognitiva do Azure](search-howto-schedule-indexers.md).

## <a name="deal-with-different-field-names"></a>Lidar com diferentes nomes de campo
Por vezes, os nomes de campo no seu índice existente são diferentes dos nomes de propriedade na sua tabela. Pode utilizar mapeamentos de campo para mapear os nomes da propriedade da tabela para os nomes de campo no seu índice de pesquisa. Para saber mais sobre mapeamentos de campo, consulte [o campo do indexante de pesquisa cognitiva Azure que faz a ponte entre as fontes de dados e os índices de pesquisa.](search-indexer-field-mappings.md)

## <a name="handle-document-keys"></a>Manuseie as teclas do documento
Na Pesquisa Cognitiva Azure, a chave do documento identifica um documento de forma única. Cada índice de pesquisa deve ter exatamente um campo chave do tipo `Edm.String` . O campo-chave é necessário para cada documento que está a ser adicionado ao índice. (Na verdade, é o único campo necessário.)

Como as linhas de mesa têm uma chave composta, a Azure Cognitive Search gera um campo sintético chamado `Key` que é uma concatenação de valores-chave de partição e linha. Por exemplo, se uma linha é o PartitionKey `PK1` e o RowKey é `RK1` , então o valor do campo `Key` é `PK1RK1` .

> [!NOTE]
> O `Key` valor pode conter caracteres inválidos nas teclas de documento, tais como traços. Pode lidar com caracteres inválidos utilizando a `base64Encode` [função de mapeamento de campo](search-indexer-field-mappings.md#base64EncodeFunction). Se o fizer, lembre-se também de utilizar a codificação Base64 segura para URL ao passar chaves de documentos em chamadas à API, como, por exemplo, Lookup.
>
>

## <a name="incremental-indexing-and-deletion-detection"></a>Deteção incremental de indexação e eliminação
Quando configura um indexador de tabelas para executar num horário, ele reindexe apenas linhas novas ou atualizadas, como determinado pelo valor de uma `Timestamp` linha. Não tens de especificar uma política de deteção de mudanças. A indexação incremental é ativada automaticamente para si.

Para indicar que certos documentos devem ser removidos do índice, pode utilizar uma estratégia de eliminação suave. Em vez de eliminar uma linha, adicione uma propriedade para indicar que é eliminada, e crie uma política de deteção de eliminação suave na fonte de dados. Por exemplo, a seguinte política considera que uma linha é eliminada se a linha tiver um imóvel `IsDeleted` com o valor `"true"` :

```http
    PUT https://[service name].search.windows.net/datasources?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "my-table-datasource",
        "type" : "azuretable",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "table name", "query" : "<query>" },
        "dataDeletionDetectionPolicy" : { "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy", "softDeleteColumnName" : "IsDeleted", "softDeleteMarkerValue" : "true" }
    }   
```

<a name="Performance"></a>
## <a name="performance-considerations"></a>Considerações de desempenho

Por predefinição, a Azure Cognitive Search utiliza o seguinte filtro de consulta: `Timestamp >= HighWaterMarkValue` . Como as tabelas Azure não têm um índice secundário no `Timestamp` campo, este tipo de consulta requer uma varredura completa da tabela e, portanto, é lento para grandes tabelas.


Aqui estão duas abordagens possíveis para melhorar o desempenho da indexação da tabela. Ambas as abordagens dependem da utilização de divisórias de mesa: 

- Se os seus dados puderem naturalmente ser divididos em várias gamas de partição, crie uma fonte de dados e um indexante correspondente para cada intervalo de partição. Cada indexante tem agora de processar apenas uma gama específica de divisórias, resultando num melhor desempenho de consulta. Se os dados que precisam de ser indexados têm um pequeno número de divisórias fixas, melhor ainda: cada indexante faz apenas uma digitalização de partição. Por exemplo, para criar um fonte de dados para o processamento de uma gama de divisórias com chaves `000` `100` de, utilize uma consulta como esta: 
    ```
    "container" : { "name" : "my-table", "query" : "PartitionKey ge '000' and PartitionKey lt '100' " }
    ```

- Se os seus dados forem divididos pelo tempo (por exemplo, criar uma nova partição todos os dias ou semanas), considere a seguinte abordagem: 
    - Utilize uma consulta do formulário: `(PartitionKey ge <TimeStamp>) and (other filters)` . 
    - Monitorize o progresso do indexante utilizando [a API do Estado do Indexante](/rest/api/searchservice/get-indexer-status)e atualize periodicamente o `<TimeStamp>` estado da consulta com base no último valor de marca de alta água bem-sucedido. 
    - Com esta abordagem, se precisar de desencadear uma reindexação completa, tem de redefinir a consulta de fonte de dados para além de redefinir o indexante. 


## <a name="help-us-make-azure-cognitive-search-better"></a>Ajude-nos a melhorar a pesquisa cognitiva do Azure
Se tiver pedidos de funcionalidades ou ideias para melhorias, envie-os no nosso [site UserVoice](https://feedback.azure.com/forums/263029-azure-search/).