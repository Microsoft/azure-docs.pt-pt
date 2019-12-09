---
title: Mover dados do MongoDB
description: Saiba mais sobre como mover dados do banco de dados MongoDB usando Azure Data Factory.
services: data-factory
author: linda33wj
ms.author: jingwang
manager: shwang
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/13/2018
ms.openlocfilehash: edddd100bddab1d642a8169353298a2d20620274
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74928115"
---
# <a name="move-data-from-mongodb-using-azure-data-factory"></a>Mover dados do MongoDB usando o Azure Data Factory

> [!div class="op_single_selector" title1="Selecione a versão do serviço de Data Factory que você está usando:"]
> * [Versão 1](data-factory-on-premises-mongodb-connector.md)
> * [Versão 2 (versão atual)](../connector-mongodb.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se você estiver usando a versão atual do serviço de Data Factory, consulte [conector do MongoDB na v2](../connector-mongodb.md).


Este artigo explica como usar a atividade de cópia no Azure Data Factory para mover dados de um banco de dado MongoDB local. Ele se baseia no artigo [atividades de movimentação de dados](data-factory-data-movement-activities.md) , que apresenta uma visão geral da movimentação de dados com a atividade de cópia.

Você pode copiar dados de um armazenamento de dados do MongoDB local para qualquer armazenamento de dados de coletor com suporte. Para obter uma lista de armazenamentos de dados com suporte como coletores pela atividade de cópia, consulte a tabela [armazenamentos de dados com suporte](data-factory-data-movement-activities.md#supported-data-stores-and-formats) . Atualmente, o data Factory dá suporte apenas à movimentação de dados de um armazenamento de dados do MongoDB para outros armazenamentos de dados, mas não para a movimentação de dados de outros armazenamentos de dados para um armazenamento de dado MongoDB.

## <a name="prerequisites"></a>Pré-requisitos
Para que o serviço de Azure Data Factory seja capaz de se conectar ao seu banco de dados MongoDB local, você deve instalar os seguintes componentes:

- As versões do MongoDB com suporte são: 2,4, 2,6, 3,0, 3,2, 3,4 e 3,6.
- Gerenciamento de Dados gateway no mesmo computador que hospeda o banco de dados ou em um computador separado para evitar a competição por recursos com o banco de dados. Gerenciamento de Dados gateway é um software que conecta fontes de dados locais a serviços de nuvem de maneira segura e gerenciada. Consulte o artigo [Gerenciamento de dados gateway](data-factory-data-management-gateway.md) para obter detalhes sobre o gateway de gerenciamento de dados. Consulte o artigo [mover dados do local para a nuvem](data-factory-move-data-between-onprem-and-cloud.md) para obter instruções passo a passo sobre como configurar o gateway como um pipeline de dados para mover dados.

    Quando você instala o gateway, ele instala automaticamente um driver ODBC do Microsoft MongoDB usado para se conectar ao MongoDB.

    > [!NOTE]
    > Você precisa usar o gateway para se conectar ao MongoDB, mesmo se ele estiver hospedado em VMs IaaS do Azure. Se você estiver tentando se conectar a uma instância do MongoDB hospedada na nuvem, também poderá instalar a instância de gateway na VM IaaS.

## <a name="getting-started"></a>Introdução
Você pode criar um pipeline com uma atividade de cópia que mova dados de um armazenamento de dados do MongoDB local usando diferentes ferramentas/APIs.

A maneira mais fácil de criar um pipeline é usar o **Assistente de cópia**. Consulte [tutorial: criar um pipeline usando o assistente de cópia](data-factory-copy-data-wizard-tutorial.md) para obter uma explicação rápida sobre como criar um pipeline usando o assistente para copiar dados.

Você também pode usar as seguintes ferramentas para criar um pipeline: **Visual Studio**, **Azure PowerShell**, **modelo de Azure Resource Manager**, **API .net**e **API REST**. Ver [tutorial da atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções passo a passo Criar um pipeline com uma atividade de cópia.

Se você usar as ferramentas ou APIs, execute as seguintes etapas para criar um pipeline que move dados de um armazenamento de dados de origem para um armazenamento de dados de coletor:

1. Crie **Serviços vinculados** para vincular armazenamentos de dados de entrada e saída ao seu data Factory.
2. Crie **conjuntos** de dados para representar o dado de entrada e saída para a operação de cópia.
3. Crie um **pipeline** com uma atividade de cópia que usa um conjunto de dados como uma entrada e um conjunto como uma saída.

Quando você usa o assistente, as definições de JSON para essas entidades de Data Factory (serviços vinculados, conjuntos de valores e o Pipeline) são criadas automaticamente para você. Ao usar ferramentas/APIs (exceto a API .NET), você define essas entidades de Data Factory usando o formato JSON.  Para obter um exemplo com definições de JSON para Data Factory entidades usadas para copiar dados de um armazenamento de dados do MongoDB local, confira [a seção exemplo de JSON: copiar dados do MongoDB para o blob do Azure](#json-example-copy-data-from-mongodb-to-azure-blob) deste artigo.

As seções a seguir fornecem detalhes sobre as propriedades JSON que são usadas para definir Data Factory entidades específicas para a origem do MongoDB:

## <a name="linked-service-properties"></a>Propriedades do serviço ligado
A tabela a seguir fornece a descrição para elementos JSON específicos do serviço vinculado **OnPremisesMongoDB** .

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| tipo |A propriedade Type deve ser definida como: **OnPremisesMongoDb** |Sim |
| servidor |Endereço IP ou nome do host do servidor MongoDB. |Sim |
| porta |Porta TCP que o servidor MongoDB usa para escutar conexões de cliente. |Opcional, valor padrão: 27017 |
| authenticationType |Básico ou anônimo. |Sim |
| o nome de utilizador |Conta de usuário para acessar o MongoDB. |Sim (se a autenticação básica for usada). |
| palavra-passe |A palavra-passe do utilizador. |Sim (se a autenticação básica for usada). |
| authSource |Nome do banco de dados MongoDB que você deseja usar para verificar suas credenciais para autenticação. |Opcional (se a autenticação básica for usada). padrão: usa a conta de administrador e o banco de dados especificado usando a propriedade databaseName. |
| databaseName |Nome do banco de dados MongoDB que você deseja acessar. |Sim |
| gatewayName |Nome do gateway que acessa o armazenamento de dados. |Sim |
| encryptedCredential |Credencial criptografada pelo Gateway. |Opcional |

## <a name="dataset-properties"></a>Propriedades do conjunto de dados
Para obter uma lista completa das seções & propriedades disponíveis para definir os conjuntos de valores, consulte o artigo [criando conjuntos](data-factory-create-datasets.md) de itens. As seções como estrutura, disponibilidade e política de um conjunto de dados JSON são semelhantes para todos os tipos de conjunto de dados (SQL do Azure, BLOB do Azure, tabela do Azure, etc.).

A seção **typeproperties** é diferente para cada tipo de conjunto de dados e fornece informações sobre o local dos dados no repositório de dados. A seção typeproperties para o conjunto de um do tipo **mongodbcollection** tem as seguintes propriedades:

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| collectionName |Nome da coleção no banco de dados MongoDB. |Sim |

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy
Para obter uma lista completa das seções & propriedades disponíveis para definir as atividades, consulte o artigo [criando pipelines](data-factory-create-pipelines.md) . Propriedades como nome, descrição, tabelas de entrada e saída e política estão disponíveis para todos os tipos de atividades.

As propriedades disponíveis na seção **typeproperties** da atividade, por outro lado, variam de acordo com cada tipo de atividade. Para a atividade de cópia, elas variam de acordo com os tipos de fontes e coletores.

Quando a origem for do tipo **MongoDB** , as seguintes propriedades estarão disponíveis na seção typeproperties:

| Propriedade | Descrição | Valores permitidos | Obrigatório |
| --- | --- | --- | --- |
| consulta |Use a consulta personalizada para ler os dados. |Cadeia de caracteres de consulta SQL-92. Por exemplo: selecione * em MyTable. |Não (se **CollectionName** de **DataSet** for especificado) |



## <a name="json-example-copy-data-from-mongodb-to-azure-blob"></a>Exemplo de JSON: copiar dados do MongoDB para o blob do Azure
Este exemplo fornece definições de JSON de exemplo que você pode usar para criar um pipeline usando o [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Ele mostra como copiar dados de um MongoDB local para um armazenamento de BLOBs do Azure. No entanto, os dados podem ser copiados para qualquer um dos coletores declarados [aqui](data-factory-data-movement-activities.md#supported-data-stores-and-formats) usando a atividade de cópia no Azure data Factory.

O exemplo tem as seguintes entidades de data factory:

1. Um serviço vinculado do tipo [OnPremisesMongoDb](#linked-service-properties).
2. Um serviço vinculado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Um [conjunto](data-factory-create-datasets.md) de dados de entrada do tipo [mongodbcollection](#dataset-properties).
4. Um [conjunto](data-factory-create-datasets.md) de uma saída do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. Um [pipeline](data-factory-create-pipelines.md) com a atividade de cópia que usa o [MongoDB](#copy-activity-properties) e o [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

O exemplo copia dados de um resultado de consulta no banco de dado MongoDB para um blob a cada hora. As propriedades JSON usadas nesses exemplos são descritas em seções após os exemplos.

Como uma primeira etapa, configure o gateway de gerenciamento de dados de acordo com as instruções no artigo [Gerenciamento de dados gateway](data-factory-data-management-gateway.md) .

**Serviço vinculado do MongoDB:**

```json
{
    "name": "OnPremisesMongoDbLinkedService",
    "properties":
    {
        "type": "OnPremisesMongoDb",
        "typeProperties":
        {
            "authenticationType": "<Basic or Anonymous>",
            "server": "< The IP address or host name of the MongoDB server >",
            "port": "<The number of the TCP port that the MongoDB server uses to listen for client connections.>",
            "username": "<username>",
            "password": "<password>",
            "authSource": "< The database that you want to use to check your credentials for authentication. >",
            "databaseName": "<database name>",
            "gatewayName": "<mygateway>"
        }
    }
}
```

**Serviço vinculado do armazenamento do Azure:**

```json
{
  "name": "AzureStorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```

**Conjunto de dados de entrada do MongoDB:** A configuração "external": "true" informa ao serviço de Data Factory que a tabela é externa à data factory e não é produzida por uma atividade no data factory.

```json
{
    "name": "MongoDbInputDataset",
    "properties": {
        "type": "MongoDbCollection",
        "linkedServiceName": "OnPremisesMongoDbLinkedService",
        "typeProperties": {
            "collectionName": "<Collection name>"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```

**Conjunto de resultados de saída de blob do Azure:**

Os dados são gravados em um novo BLOB a cada hora (frequência: hora, intervalo: 1). O caminho da pasta para o blob é avaliado dinamicamente com base na hora de início da fatia que está sendo processada. O caminho da pasta usa as partes ano, mês, dia e horas da hora de início.

```json
{
    "name": "AzureBlobOutputDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/frommongodb/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
                "columnDelimiter": "\t"
            },
            "partitionedBy": [
                {
                    "name": "Year",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "yyyy"
                    }
                },
                {
                    "name": "Month",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "MM"
                    }
                },
                {
                    "name": "Day",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "dd"
                    }
                },
                {
                    "name": "Hour",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "HH"
                    }
                }
            ]
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**Atividade de cópia em um pipeline com origem e coletor de blob do MongoDB:**

O pipeline contém uma atividade de cópia configurada para usar os conjuntos de dados de entrada e saída acima e está agendada para ser executada a cada hora. Na definição de JSON do pipeline, o tipo de **origem** está definido como **MongoDB** e o tipo de **coletor** está definido como **BlobSink**. A consulta SQL especificada para a propriedade de **consulta** seleciona os dados na última hora a serem copiados.

```json
{
    "name": "CopyMongoDBToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "MongoDbSource",
                        "query": "$$Text.Format('select * from MyTable where LastModifiedDate >= {{ts\'{0:yyyy-MM-dd HH:mm:ss}\'}} AND LastModifiedDate < {{ts\'{1:yyyy-MM-dd HH:mm:ss}\'}}', WindowStart, WindowEnd)"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "MongoDbInputDataset"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutputDataSet"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "MongoDBToAzureBlob"
            }
        ],
        "start": "2016-06-01T18:00:00Z",
        "end": "2016-06-01T19:00:00Z"
    }
}
```


## <a name="schema-by-data-factory"></a>Esquema pelo Data Factory
Azure Data Factory Service infere o esquema de uma coleção do MongoDB usando os últimos 100 documentos na coleção. Se esses 100 documentos não contiverem esquema completo, algumas colunas poderão ser ignoradas durante a operação de cópia.

## <a name="type-mapping-for-mongodb"></a>Mapeamento de tipo para MongoDB
Conforme mencionado no artigo sobre as [atividades de movimentação de dados](data-factory-data-movement-activities.md) , a atividade de cópia executa conversões automáticas dos tipos de origem nos tipos de coletor com a seguinte abordagem de duas etapas:

1. Converter de tipos de origem nativos em tipo .NET
2. Converter do tipo .NET para o tipo de coletor nativo

Ao mover dados para o MongoDB, os seguintes mapeamentos são usados de tipos do MongoDB para tipos .NET.

| Tipo do MongoDB | Tipo de .NET Framework |
| --- | --- |
| Binary |Byte[] |
| Booleano |Booleano |
| Date |DateTime |
| NumberDouble |Double |
| NumberInt |Int32 |
| NumberLong |Int64 |
| ObjectID |String |
| String |String |
| UUID |GUID |
| Object |Renormalizado para colunas achatadas com "_" como separador aninhado |

> [!NOTE]
> Para saber mais sobre o suporte para matrizes usando tabelas virtuais, consulte a seção [suporte para tipos complexos usando tabelas virtuais](#support-for-complex-types-using-virtual-tables) abaixo.

Atualmente, não há suporte para os seguintes tipos de dados do MongoDB: DBPointer, JavaScript, Max/min Key, expressão regular, símbolo, carimbo de data/hora, indefinido

## <a name="support-for-complex-types-using-virtual-tables"></a>Suporte para tipos complexos usando tabelas virtuais
Azure Data Factory usa um driver ODBC interno para se conectar e copiar dados de seu banco de dados MongoDB. Para tipos complexos, como matrizes ou objetos com diferentes tipos em todos os documentos, o driver renormaliza os dados em tabelas virtuais correspondentes. Especificamente, se uma tabela contiver essas colunas, o driver gerará as seguintes tabelas virtuais:

* Uma **tabela base**, que contém os mesmos dados que a tabela real, exceto para as colunas de tipo complexo. A tabela base usa o mesmo nome que a tabela real que ela representa.
* Uma **tabela virtual** para cada coluna de tipo complexo, que expande os dados aninhados. As tabelas virtuais são nomeadas usando o nome da tabela real, um separador "_" e o nome da matriz ou objeto.

As tabelas virtuais referem-se aos dados na tabela real, permitindo que o driver acesse os dados desnormalizados. Consulte a seção de exemplo abaixo detalhes. Você pode acessar o conteúdo de matrizes do MongoDB consultando e unindo as tabelas virtuais.

Você pode usar o [Assistente de cópia](data-factory-data-movement-activities.md#create-a-pipeline-with-copy-activity) para exibir intuitivamente a lista de tabelas no banco de dados MongoDB, incluindo as tabelas virtuais, e visualizar os dados dentro. Você também pode construir uma consulta no assistente de cópia e validar para ver o resultado.

### <a name="example"></a>Exemplo
Por exemplo, "Exemplotable" abaixo é uma tabela do MongoDB que tem uma coluna com uma matriz de objetos em cada célula – faturas e uma coluna com uma matriz de tipos escalares – classificações.

| _id | Nome do cliente | Faturas | Nível de Serviço | Classificações |
| --- | --- | --- | --- | --- |
| 1111 |ABC |[{invoice_id: "123", item: "torradeira", Price: "456", Discount: "0.2"}, {invoice_id: "124", item: "forno", Price: "1235", Discount: "0,2"}] |Silver |[5,6] |
| 2222 |XYZ |[{invoice_id: "135", item: "geladeira", preço: "12543", desconto: "0,0"}] |Gold |[1,2] |

O driver geraria várias tabelas virtuais para representar essa única tabela. A primeira tabela virtual é a tabela base chamada "ExampleTable", mostrada abaixo. A tabela base contém todos os dados da tabela original, mas os dados das matrizes foram omitidos e expandidos nas tabelas virtuais.

| _id | Nome do cliente | Nível de Serviço |
| --- | --- | --- |
| 1111 |ABC |Silver |
| 2222 |XYZ |Gold |

As tabelas a seguir mostram as tabelas virtuais que representam as matrizes originais no exemplo. Essas tabelas contêm o seguinte:

* Uma referência de volta à coluna de chave primária original correspondente à linha da matriz original (por meio da coluna _id)
* Uma indicação da posição dos dados na matriz original
* Os dados expandidos para cada elemento dentro da matriz

Tabela "ExampleTable_Invoices":

| _id | ExampleTable_Invoices_dim1_idx | invoice_id | item | preço | Desconto |
| --- | --- | --- | --- | --- | --- |
| 1111 |0 |123 |torradeira |456 |0.2 |
| 1111 |1 |124 |forno |1235 |0.2 |
| 2222 |0 |135 |geladeira |12543 |0.0 |

Tabela "ExampleTable_Ratings":

| _id | ExampleTable_Ratings_dim1_idx | ExampleTable_Ratings |
| --- | --- | --- |
| 1111 |0 |5 |
| 1111 |1 |6 |
| 2222 |0 |1 |
| 2222 |1 |2 |

## <a name="map-source-to-sink-columns"></a>Mapear origem para colunas do coletor
Para saber mais sobre como mapear colunas no conjunto de informações de origem para colunas no conjunto de informações do coletor, confira [mapeando colunas Azure data Factory do conjunto de](data-factory-map-columns.md)informações

## <a name="repeatable-read-from-relational-sources"></a>Leitura repetida de fontes relacionais
Ao copiar dados de armazenamentos de dados relacionais, tenha em mente a capacidade de repetição para evitar resultados indesejados. No Azure Data Factory, você pode executar novamente uma fatia manualmente. Você também pode configurar a política de repetição para um conjunto de uma para que uma fatia seja executada novamente quando ocorrer uma falha. Quando uma fatia é executada novamente de qualquer forma, você precisa garantir que os mesmos dados sejam lidos, independentemente de quantas vezes uma fatia é executada. Consulte [leitura repetida de fontes relacionais](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Desempenho e ajuste
Consulte [Guia de ajuste do desempenho de atividade de cópia &](data-factory-copy-activity-performance.md) para saber mais sobre os principais fatores que afetam o desempenho da movimentação de dados (atividade de cópia) no Azure data Factory e várias maneiras de otimizá-lo.

## <a name="next-steps"></a>Próximos Passos
Confira o artigo [mover dados entre o local e a nuvem](data-factory-move-data-between-onprem-and-cloud.md) para obter instruções passo a passo para criar um pipeline de dados que move dados de um armazenamento de dados local para um armazenamento de dados do Azure.
