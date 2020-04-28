---
title: Pesquisar sobre o conteúdo de armazenamento da mesa Azure
titleSuffix: Azure Cognitive Search
description: Saiba como indexar os dados armazenados no armazenamento da Mesa Azure com um indexador de pesquisa cognitiva Azure.
manager: nitinme
author: mgottein
ms.author: magottei
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: e8f6c0454497b1cb1d62417e566e9662469c56d0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74112995"
---
# <a name="how-to-index-tables-from-azure-table-storage-with-azure-cognitive-search"></a>Como indexar tabelas do armazenamento da mesa Azure com pesquisa cognitiva azure

Este artigo mostra como usar a Pesquisa Cognitiva Azure para indexar os dados armazenados no armazenamento da Tabela Azure.

## <a name="set-up-azure-table-storage-indexing"></a>Configurar a indexação de armazenamento de mesa azure

Pode configurar um indexador de armazenamento de mesa azure utilizando estes recursos:

* [Portal do Azure](https://ms.portal.azure.com)
* [API de](https://docs.microsoft.com/rest/api/searchservice/Indexer-operations) pesquisa cognitiva azure
* Pesquisa Cognitiva Azure [.NET SDK](https://aka.ms/search-sdk)

Aqui demonstramos o fluxo usando a API REST. 

### <a name="step-1-create-a-datasource"></a>Passo 1: Criar uma fonte de dados

Uma fonte de dados especifica quais os dados indexados, as credenciais necessárias para aceder aos dados e as políticas que permitem ao Azure Cognitive Search identificar eficientemente as mudanças nos dados.

Para a indexação da tabela, o fonte de dados deve ter as seguintes propriedades:

- **o nome** é o nome único da fonte de dados dentro do seu serviço de pesquisa.
- **tipo** deve `azuretable`ser .
- o parâmetro **de credenciais** contém a cadeia de ligação da conta de armazenamento. Consulte a secção [de credenciais Especifique](#Credentials) para obter mais detalhes.
- **recipiente** define o nome da mesa e uma consulta opcional.
    - Especifique o `name` nome da tabela utilizando o parâmetro.
    - Opcionalmente, especifique uma `query` consulta utilizando o parâmetro. 

> [!IMPORTANT] 
> Sempre que possível, utilize um filtro na PartitionKey para um melhor desempenho. Qualquer outra consulta faz uma varredura completa da tabela, resultando num mau desempenho para grandes mesas. Consulte a secção [de considerações](#Performance) de Desempenho.


Para criar uma fonte de dados:

    POST https://[service name].search.windows.net/datasources?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "table-datasource",
        "type" : "azuretable",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-table", "query" : "PartitionKey eq '123'" }
    }   

Para obter mais informações sobre a Create Datasource API, consulte [Create Datasource](https://docs.microsoft.com/rest/api/searchservice/create-data-source).

<a name="Credentials"></a>
#### <a name="ways-to-specify-credentials"></a>Formas de especificar credenciais ####

Pode fornecer as credenciais para a mesa de uma destas formas: 

- **Cadeia de ligação** `DefaultEndpointsProtocol=https;AccountName=<your storage account>;AccountKey=<your account key>` à conta de armazenamento de acesso completo: **Storage account blade** > Pode obter a cadeia de ligação do portal Azure indo para as**teclas** **definições** > da lâmina de armazenamento (para contas de armazenamento clássicas) ou**teclas** de acesso **de definições** > (para contas de armazenamento do Gestor de Recursos Azure).
- Série de **ligação**de `TableEndpoint=https://<your account>.table.core.windows.net/;SharedAccessSignature=?sv=2016-05-31&sig=<the signature>&spr=https&se=<the validity end time>&srt=co&ss=t&sp=rl` assinatura de acesso partilhado da conta de armazenamento : A assinatura de acesso partilhado deve ter a lista e ler permissões em recipientes (tabelas neste caso) e objetos (linhas de mesa).
-  **Assinatura**de acesso `ContainerSharedAccessUri=https://<your storage account>.table.core.windows.net/<table name>?tn=<table name>&sv=2016-05-31&sig=<the signature>&se=<the validity end time>&sp=r` partilhado tabela : A assinatura de acesso partilhado deve ter permissões de consulta (ler) em cima da mesa.

Para obter mais informações sobre o armazenamento de assinaturas de acesso partilhado, consulte [A utilização de assinaturas de acesso partilhado](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

> [!NOTE]
> Se utilizar credenciais de assinatura de acesso partilhado, terá de atualizar periodicamente as credenciais de fonte de dados com assinaturas renovadas para evitar a sua expiração. Se expirarem as credenciais de assinatura de acesso partilhado, o indexante falha com uma mensagem de erro semelhante a "As credenciais fornecidas na cadeia de ligação são inválidas ou expiraram."  

### <a name="step-2-create-an-index"></a>Passo 2: criar um índice
O índice especifica os campos num documento, os atributos e outras construções que moldam a experiência de pesquisa.

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

Para obter mais informações sobre a criação de índices, consulte [Criar Índice](https://docs.microsoft.com/rest/api/searchservice/create-index).

### <a name="step-3-create-an-indexer"></a>Passo 3: Criar um indexador
Um indexante liga uma fonte de dados a um índice de pesquisa de alvo e fornece um horário para automatizar a atualização de dados. 

Após a criação do índice e da fonte de dados, está pronto para criar o indexante:

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "table-indexer",
      "dataSourceName" : "table-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" }
    }

Este indexante funciona a cada duas horas. (O intervalo de horário está definido para "PT2H".) Para executar um indexante a cada 30 minutos, detete o intervalo para "PT30M". O intervalo suportado mais curto é de cinco minutos. O horário é opcional; se omitido, um indexante funciona apenas uma vez quando é criado. No entanto, pode executar um indexante a qualquer momento.   

Para obter mais informações sobre a API Create Indexer, consulte [Create Indexer](https://docs.microsoft.com/rest/api/searchservice/create-indexer).

Para obter mais informações sobre a definição de horários indexantes, consulte [como agendar indexadores para pesquisa cognitiva azure](search-howto-schedule-indexers.md).

## <a name="deal-with-different-field-names"></a>Lidar com diferentes nomes de campo
Às vezes, os nomes de campo no seu índice existente são diferentes dos nomes de propriedade na sua mesa. Você pode usar mapeamentos de campo para mapear os nomes de propriedade da tabela para os nomes de campo no seu índice de pesquisa. Para saber mais sobre mapeamentos de campo, consulte os mapeamentos de campo do indexante de [pesquisa cognitiva Azure em ponte as diferenças entre fontes](search-indexer-field-mappings.md)de dados e índices de pesquisa .

## <a name="handle-document-keys"></a>Manuseie chaves de documento
Na Pesquisa Cognitiva Azure, a chave do documento identifica um documento de forma única. Cada índice de pesquisa deve ter `Edm.String`exatamente um campo chave de tipo . O campo-chave é necessário para cada documento que está a ser adicionado ao índice. (Na verdade, é o único campo necessário.)

Como as filas de mesa têm uma chave composta, `Key` a Pesquisa Cognitiva Azure gera um campo sintético chamado que é uma concatenação de divisórias chave e valores-chave de linha. Por exemplo, se a Chave `PK1` de Partição de uma linha é e rowKey é `RK1`, então o `Key` valor do campo é `PK1RK1`.

> [!NOTE]
> O `Key` valor pode conter caracteres que são inválidos em chaves de documento, tais como traços. Pode lidar com caracteres inválidos utilizando a função de `base64Encode` [mapeamento](search-indexer-field-mappings.md#base64EncodeFunction)de campo . Se o fizer, lembre-se também de utilizar a codificação Base64 segura para URL ao passar chaves de documentos em chamadas à API, como, por exemplo, Lookup.
>
>

## <a name="incremental-indexing-and-deletion-detection"></a>Deteção incremental de indexação e eliminação
Quando se cria um indexante de tabela para funcionar num horário, reindexa apenas linhas `Timestamp` novas ou atualizadas, conforme determinado pelo valor de uma linha. Não tens de especificar uma política de deteção de mudanças. A indexação incremental está ativada automaticamente para si.

Para indicar que determinados documentos devem ser removidos do índice, pode utilizar uma estratégia de eliminação suave. Em vez de apagar uma linha, adicione uma propriedade para indicar que é eliminada e configurar uma política de deteção de eliminação suave no datasource. Por exemplo, a seguinte política considera que uma linha `IsDeleted` é `"true"`eliminada se a linha tiver um imóvel com o valor:

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

Por predefinição, a Pesquisa Cognitiva Azure utiliza o seguinte filtro de consulta: `Timestamp >= HighWaterMarkValue`. Como as tabelas Azure não têm `Timestamp` um índice secundário no campo, este tipo de consulta requer uma varredura de mesa completa e, portanto, é lento para mesas grandes.


Aqui estão duas abordagens possíveis para melhorar o desempenho da indexação da tabela. Ambas as abordagens dependem da utilização de divisórias de mesa: 

- Se os seus dados puderem naturalmente ser divididos em várias gamas de divisórias, crie uma fonte de dados e um indexante correspondente para cada gama de divisórias. Cada indexante tem agora de processar apenas uma gama específica de divisórias, resultando num melhor desempenho de consulta. Se os dados que precisam de ser indexados tiver um pequeno número de divisórias fixas, ainda melhor: cada indexante só faz uma varredura de partição. Por exemplo, para criar uma fonte de dados `000` `100`para o processamento de uma gama de divisórias com chaves de, use uma consulta como esta: 
    ```
    "container" : { "name" : "my-table", "query" : "PartitionKey ge '000' and PartitionKey lt '100' " }
    ```

- Se os seus dados forem divididos pelo tempo (por exemplo, cria uma nova partição todos os dias ou semanais), considere a seguinte abordagem: 
    - Utilize uma consulta do `(PartitionKey ge <TimeStamp>) and (other filters)`formulário: . 
    - Monitorize o progresso do indexante utilizando a API `<TimeStamp>` do Status Get [Indexer](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status)e atualize periodicamente o estado da consulta com base no mais recente valor bem sucedido da marca de água. 
    - Com esta abordagem, se precisar de desencadear uma reindexação completa, precisa de redefinir a consulta de fonte de dados para além de reajustar o indexante. 


## <a name="help-us-make-azure-cognitive-search-better"></a>Ajude-nos a tornar a Pesquisa Cognitiva Azure melhor
Se tiver pedidos de funcionalidades ou ideias para melhorias, submeta-as no nosso [site userVoice](https://feedback.azure.com/forums/263029-azure-search/).
