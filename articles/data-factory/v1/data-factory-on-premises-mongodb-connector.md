---
title: Mover dados do MongoDB com o Data Factory | Documentos da Microsoft
description: Saiba mais sobre como mover dados de base de dados do MongoDB com o Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 10ca7d9a-7715-4446-bf59-2d2876584550
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/13/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 433a8b2f9fb1f4c4599afbb807e9270992a98a52
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60824189"
---
# <a name="move-data-from-mongodb-using-azure-data-factory"></a>Mover dados do MongoDB com o Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1](data-factory-on-premises-mongodb-connector.md)
> * [Versão 2 (versão atual)](../connector-mongodb.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, veja [conector do MongoDB no V2](../connector-mongodb.md).


Este artigo explica como utilizar a atividade de cópia no Azure Data Factory para mover dados de um banco de dados do MongoDB local. Ele se baseia no [atividades de movimento de dados](data-factory-data-movement-activities.md) artigo, que apresenta uma visão geral do movimento de dados com a atividade de cópia.

Pode copiar dados de um arquivo de dados do MongoDB no local para qualquer arquivo de dados de sink suportados. Para obter uma lista dos arquivos de dados suportados como sinks a atividade de cópia, consulte a [arquivos de dados suportados](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tabela. O Data factory suporta, atualmente, apenas mover dados de um arquivo de dados do MongoDB para outros arquivos de dados, mas não para mover dados de outros arquivos de dados para um arquivo de dados do MongoDB.

## <a name="prerequisites"></a>Pré-requisitos
Para o serviço do Azure Data Factory conseguir ligar à base de dados MongoDB no local, tem de instalar os seguintes componentes:

- Versões suportadas do MongoDB são: 2.4, 2.6, 3.0, 3.2, 3.4 e 3.6.
- O Data Management Gateway no mesmo computador que aloja a base de dados ou numa máquina separada para evitar competindo por recursos com a base de dados. O Data Management Gateway é um software que liga origens de dados no local para serviços cloud de forma segura e gerida. Ver [Data Management Gateway](data-factory-data-management-gateway.md) artigo para obter detalhes sobre o Data Management Gateway. Ver [mover dados do local para a cloud](data-factory-move-data-between-onprem-and-cloud.md) artigo para obter instruções passo a passo sobre como configurar o gateway de um pipeline de dados mover dados.

    Ao instalar o gateway, ele automaticamente instala um driver de Microsoft MongoDB ODBC utilizado para ligar ao MongoDB.

    > [!NOTE]
    > Tem de utilizar o gateway para ligar ao MongoDB, mesmo que ele está hospedado em VMs IaaS do Azure. Se estiver a tentar ligar a uma instância de MongoDB alojada na cloud, também pode instalar a instância de gateway na IaaS VM.

## <a name="getting-started"></a>Introdução
Pode criar um pipeline com uma atividade de cópia que move os dados de um arquivo de dados do MongoDB no local através de APIs/ferramentas diferentes.

A maneira mais fácil para criar um pipeline é utilizar o **Assistente para copiar**. Consulte [Tutorial: Criar um pipeline com o Assistente para copiar](data-factory-copy-data-wizard-tutorial.md) para um rápido passo a passo sobre como criar um pipeline com o Assistente para copiar dados.

Também pode utilizar as seguintes ferramentas para criar um pipeline: **Portal do Azure**, **Visual Studio**, **Azure PowerShell**, **modelo Azure Resource Manager**, **.NET API**e  **REST API**. Ver [tutorial da atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções passo a passo Criar um pipeline com uma atividade de cópia.

Se usar as ferramentas ou APIs, que execute os seguintes passos para criar um pipeline que move os dados de um arquivo de dados de origem para um arquivo de dados de sink:

1. Crie **serviços ligados** para ligar a dados de entrada e saídos armazena à fábrica de dados.
2. Crie **conjuntos de dados** para representar os dados de entrada e saídos da operação de cópia.
3. Criar uma **pipeline** com uma atividade de cópia que usa um conjunto de dados como entrada e um conjunto de dados como uma saída.

Quando utiliza o assistente, definições de JSON para estas entidades do Data Factory (serviços ligados, conjuntos de dados e pipeline) são criadas automaticamente para. Ao utilizar ferramentas/APIs (exceto a .NET API), define essas entidades do Data Factory com o formato JSON.  Para obter um exemplo com definições de JSON para entidades do Data Factory que são utilizadas para copiar dados de um arquivo de dados do MongoDB no local, consulte [exemplo de JSON: Copiar dados do MongoDB para BLOBs do Azure](#json-example-copy-data-from-mongodb-to-azure-blob) seção deste artigo.

As secções seguintes fornecem detalhes sobre as propriedades JSON, que são utilizadas para definir entidades do Data Factory específicas para a origem do MongoDB:

## <a name="linked-service-properties"></a>Propriedades do serviço ligado
A tabela seguinte fornece uma descrição para elementos JSON específicos **OnPremisesMongoDB** serviço ligado.

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| type |A propriedade de tipo tem de ser definida como: **OnPremisesMongoDb** |Sim |
| servidor |Nome anfitrião ou endereço IP do servidor do MongoDB. |Sim |
| porta |Porta TCP que o servidor do MongoDB utiliza para escutar ligações de cliente. |Opcional, valor de predefinido: 27017 |
| authenticationType |Básico ou anónimo. |Sim |
| o nome de utilizador |Conta de utilizador para aceder a MongoDB. |Sim (se for utilizada autenticação básica). |
| password |A palavra-passe do utilizador. |Sim (se for utilizada autenticação básica). |
| authSource |Nome da base de dados do MongoDB que pretende utilizar para verificar as suas credenciais para autenticação. |Opcional (se for utilizada autenticação básica). predefinição: utiliza a conta de administrador e a base de dados especificada, utilizando a propriedade databaseName. |
| databaseName |Nome da base de dados do MongoDB que pretende aceder. |Sim |
| gatewayName |Nome do gateway que acessa o arquivo de dados. |Sim |
| encryptedCredential |Credencial encriptada pelo gateway. |Opcional |

## <a name="dataset-properties"></a>Propriedades do conjunto de dados
Para obter uma lista completa das secções e propriedades disponíveis para definir conjuntos de dados, consulte a [criar conjuntos de dados](data-factory-create-datasets.md) artigo. Seções, como a estrutura, disponibilidade e a política de um conjunto de dados JSON são semelhantes para todos os tipos de conjunto de dados (Azure SQL, BLOBs do Azure, tabela do Azure, etc.).

O **typeProperties** secção é diferente para cada tipo de conjunto de dados e fornece informações sobre a localização dos dados no arquivo de dados. Os typeProperties secção para o conjunto de dados do tipo **MongoDbCollection** tem as seguintes propriedades:

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| CollectionName |Nome da coleção na base de dados do MongoDB. |Sim |

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy
Para obter uma lista completa das secções e propriedades disponíveis para a definição de atividades, consulte a [criar Pipelines](data-factory-create-pipelines.md) artigo. Propriedades, tais como o nome, descrição, entrada e saída de tabelas e a política estão disponíveis para todos os tipos de atividades.

Propriedades disponíveis no **typeProperties** secção da atividade variar por outro lado, com cada tipo de atividade. Para a atividade de cópia, elas variam consoante os tipos de origens e sinks.

Quando a origem é do tipo **MongoDbSource** as seguintes propriedades estão disponíveis na secção typeProperties:

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| consulta |Utilize a consulta personalizada para ler dados. |Cadeia de consulta de SQL-92. Por exemplo: selecionar * de MyTable. |Não (se **collectionName** dos **conjunto de dados** for especificado) |



## <a name="json-example-copy-data-from-mongodb-to-azure-blob"></a>Exemplo JSON: Copiar dados do MongoDB para BLOBs do Azure
Este exemplo fornece definições de JSON de exemplo que pode utilizar para criar um pipeline com [portal do Azure](data-factory-copy-activity-tutorial-using-azure-portal.md) ou [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Ele mostra como copiar dados de uma MongoDB no local para um armazenamento de Blobs do Azure. No entanto, os dados podem ser copiados para qualquer um dos sinks indicados [aqui](data-factory-data-movement-activities.md#supported-data-stores-and-formats) usando a atividade de cópia no Azure Data Factory.

O exemplo possui as seguintes entidades do data factory:

1. Um serviço ligado do tipo [OnPremisesMongoDb](#linked-service-properties).
2. Um serviço ligado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Entrada [conjunto de dados](data-factory-create-datasets.md) do tipo [MongoDbCollection](#dataset-properties).
4. Uma saída [conjunto de dados](data-factory-create-datasets.md) do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. R [pipeline](data-factory-create-pipelines.md) com a atividade de cópia que utiliza [MongoDbSource](#copy-activity-properties) e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

O exemplo copia dados de um resultado de consulta na base de dados do MongoDB para um blob a cada hora. As propriedades JSON utilizadas nestes exemplos são descritas nas seções a seguir os exemplos.

Como primeiro passo, o gateway de gestão de dados de acordo com as instruções de configuração do [Data Management Gateway](data-factory-data-management-gateway.md) artigo.

**Serviço ligado do MongoDB:**

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

**Serviço ligado do armazenamento do Azure:**

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

**Conjunto de dados entrado de MongoDB:** A definição "externo": "true" informa o serviço Data Factory que a tabela é externa à fábrica de dados e não é produzida por uma atividade na fábrica de dados.

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

**Conjunto de dados de Blobs do Azure:**

Os dados são escritos para um blob novo a cada hora (frequência: hora, intervalo: 1). O caminho da pasta para o blob é avaliado dinamicamente com base na hora de início do setor que está a ser processado. O caminho da pasta utiliza ano, mês, dia e partes de horas da hora de início.

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

**Atividade de cópia num pipeline com o MongoDB de origem e sink do Blob:**

O pipeline contém uma atividade de cópia que está configurado para usar a entrada acima e conjuntos de dados de saída e é agendada para ser executada a cada hora. No pipeline de definição de JSON, o **origem** tipo está definido como **MongoDbSource** e **sink** tipo está definido como **BlobSink**. A consulta SQL especificada para o **consulta** propriedade seleciona os dados na hora anterior para copiar.

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
O serviço do Azure Data Factory infere o esquema de uma coleção do MongoDB ao utilizar os mais recentes 100 documentos na coleção. Se esses 100 documentos não contêm esquema completo, algumas colunas podem ser ignoradas durante a operação de cópia.

## <a name="type-mapping-for-mongodb"></a>Mapeamento de tipo para o MongoDB
Conforme mencionado na [atividades de movimento de dados](data-factory-data-movement-activities.md) artigo, a atividade de cópia executa conversões de tipos automáticas de tipos de origem para o sink de tipos com a seguinte abordagem de passo 2:

1. Converter entre tipos de origem nativas para o tipo de .NET
2. Converter de tipo de .NET para o tipo de sink nativo

Ao mover dados para o MongoDB os seguintes mapeamentos de servem de tipos de MongoDB para tipos .NET.

| Tipo de MongoDB | Tipo de .NET framework |
| --- | --- |
| Binário |Byte[] |
| Boolean |Boolean |
| Date |DateTime |
| NumberDouble |Double |
| NumberInt |Int32 |
| NumberLong |Int64 |
| ObjectID |String |
| Cadeia |String |
| UUID |Guid |
| Object |Renormalized em colunas com "_" como separador aninhada de nivelamento |

> [!NOTE]
> Para saber mais sobre o suporte para matrizes com tabelas virtuais, consulte [suporte para tipos complexos usando tabelas virtuais](#support-for-complex-types-using-virtual-tables) secção abaixo.

Atualmente, não são suportados os seguintes tipos de dados do MongoDB: Máx. de DBPointer, JavaScript, por minuto da chave, expressões regulares, símbolo, Timestamp, indefinido

## <a name="support-for-complex-types-using-virtual-tables"></a>Suporte para tipos complexos usando tabelas virtuais
O Azure Data Factory utiliza um controlador ODBC incorporado para ligar e copiar dados de sua base de dados do MongoDB. Para obter tipos complexos, como matrizes ou objetos com diferentes tipos de documentos, o driver normaliza novamente dados em tabelas virtuais correspondentes. Especificamente, se uma tabela contém este tipo de colunas, o driver gera as tabelas virtuais seguintes:

* R **tabela base**, que contém os mesmos dados que a tabela real, exceto as colunas de tipo complexo. A tabela base utiliza o mesmo nome, como a tabela real que representa.
* R **tabela virtual** para cada coluna de tipo complexo, que expande os dados aninhados. As tabelas virtuais são nomeadas usando o nome da tabela real, um separador de "_" e o nome do objeto ou matriz.

Tabelas virtuais referem-se aos dados na tabela real, permitindo que o driver acessar os dados desnormalizados. Consulte a secção de exemplo abaixo detalhes. Pode acessar o conteúdo das matrizes de MongoDB consultando e associar as tabelas virtuais.

Pode utilizar o [Assistente para copiar](data-factory-data-movement-activities.md#create-a-pipeline-with-copy-activity) intuitivamente ver a lista de tabelas na base de dados do MongoDB incluindo as tabelas virtuais e pré-visualizar os dados dentro. Também pode construir uma consulta no Assistente de cópia e validar para ver o resultado.

### <a name="example"></a>Exemplo
Por exemplo, "ExampleTable" abaixo é uma tabela do MongoDB que tem uma coluna com uma matriz de objetos em cada célula – notas fiscais e uma coluna com uma matriz de tipos escalares – classificações.

| _id | Nome do cliente | Faturas | Nível de Serviço | Classificações |
| --- | --- | --- | --- | --- |
| 1111 |ABC |[{invoice_id: item "123": "torradeira", price: Desconto "456",: "0,2"}, {invoice_id: item "124": "oven", price: Desconto "1235",: "0,2"}] |Prateado |[5,6] |
| 2222 |XYZ |[{invoice_id:”135”, item:”fridge”, price: ”12543”, discount: ”0.0”}] |Dourado |[1,2] |

O controlador irá gerar várias tabelas virtuais para representar nesta única tabela. A primeira tabela virtual é a tabela de base com o nome "ExampleTable", mostrado abaixo. A tabela base contém todos os dados da tabela original, mas os dados das matrizes foram omitidos e são expandidos nas tabelas virtuais.

| _id | Nome do cliente | Nível de Serviço |
| --- | --- | --- |
| 1111 |ABC |Prateado |
| 2222 |XYZ |Dourado |

As tabelas seguintes mostram as tabelas virtuais que representam as matrizes originais no exemplo. Estas tabelas contenham o seguinte:

* Uma referência para a coluna de chave primária original correspondente para a linha da matriz original (através da coluna de ID)
* Uma indicação de que a posição dos dados dentro da matriz original
* Os dados expandidos para cada elemento dentro da matriz

Tabela de "ExampleTable_Invoices":

| _id | ExampleTable_Invoices_dim1_idx | invoice_id | item | preço | Desconto |
| --- | --- | --- | --- | --- | --- |
| 1111 |0 |123 |torradeira |456 |0.2 |
| 1111 |1 |124 |oven |1235 |0.2 |
| 2222 |0 |135 |frigorífico |12543 |0.0 |

Tabela de "ExampleTable_Ratings":

| _id | ExampleTable_Ratings_dim1_idx | ExampleTable_Ratings |
| --- | --- | --- |
| 1111 |0 |5 |
| 1111 |1 |6 |
| 2222 |0 |1 |
| 2222 |1 |2 |

## <a name="map-source-to-sink-columns"></a>Origem do mapa para colunas de sink
Para saber mais sobre as colunas de mapeamento no conjunto de dados de origem para colunas no conjunto de dados de sink, veja [mapeamento de colunas do conjunto de dados no Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Leitura passível de repetição de fontes relacionais
Quando armazena a cópia de dados de dados relacionais, tenha a capacidade de repetição em mente para evitar resultados indesejados. No Azure Data Factory, pode voltar a executar um setor manualmente. Também pode configurar a política de repetição para um conjunto de dados para que um setor será novamente executado quando ocorre uma falha. Quando um setor será novamente executado de qualquer forma, terá de certificar-se de que os mesmos dados é de leitura não questão número de vezes que um setor é executado. Ver [Repeatable ler a partir de origens relacionais](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Desempenho e Otimização
Ver [desempenho de atividade de cópia e guia de ajuste](data-factory-copy-activity-performance.md) para saber mais sobre os fatores chave a que um impacto no desempenho de movimento de dados (atividade de cópia) no Azure Data Factory e diversas maneiras para otimizá-lo.

## <a name="next-steps"></a>Próximos Passos
Ver [mover dados entre locais e na cloud](data-factory-move-data-between-onprem-and-cloud.md) artigo para obter instruções passo a passo para criar um pipeline de dados que move dados de um arquivo de dados no local para um arquivo de dados do Azure.
