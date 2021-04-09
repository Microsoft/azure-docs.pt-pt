---
title: Mover dados do MongoDB
description: Saiba como mover dados da base de dados mongoDB usando a Azure Data Factory.
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.topic: conceptual
ms.date: 04/13/2018
ms.openlocfilehash: cedb0b99f04df00763a3ee83287eec90bd5fb45d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100387517"
---
# <a name="move-data-from-mongodb-using-azure-data-factory"></a>Mover dados da MongoDB usando a Azure Data Factory

> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que está a utilizar:"]
> * [Versão 1](data-factory-on-premises-mongodb-connector.md)
> * [Versão 2 (versão atual)](../connector-mongodb.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, consulte o [conector MongoDB em V2](../connector-mongodb.md).


Este artigo explica como utilizar a Atividade de Cópia na Fábrica de Dados Azure para mover dados de uma base de dados mongoDB no local. Baseia-se no artigo de Atividades de Movimento de [Dados,](data-factory-data-movement-activities.md) que apresenta uma visão geral do movimento de dados com a atividade da cópia.

Pode copiar dados de uma loja de dados mongoDB no local para qualquer loja de dados de lavatórios suportados. Para obter uma lista de lojas de dados suportadas como pias pela atividade de cópia, consulte a tabela [de lojas de dados suportadas.](data-factory-data-movement-activities.md#supported-data-stores-and-formats) Atualmente, a fábrica de dados suporta apenas a transferência de dados de uma loja de dados MongoDB para outras lojas de dados, mas não para transferir dados de outras lojas de dados para uma loja de dados mongoDB.

## <a name="prerequisites"></a>Pré-requisitos
Para que o serviço Azure Data Factory possa ligar-se à base de dados MongoDB no local, deve instalar os seguintes componentes:

- As versões MongoDB suportadas são: 2.4, 2.6, 3.0, 3.2, 3.4 e 3.6.
- Data Management Gateway na mesma máquina que hospeda a base de dados ou numa máquina separada para evitar competir por recursos com a base de dados. Data Management Gateway é um software que liga fontes de dados no local aos serviços na nuvem de forma segura e gerida. Consulte o artigo [do Data Management Gateway](data-factory-data-management-gateway.md) para obter mais informações sobre o Data Management Gateway. Consulte [os dados de mover os dados das instalações para o](data-factory-move-data-between-onprem-and-cloud.md) artigo em nuvem para obter instruções passo a passo sobre a configuração do gateway de um pipeline de dados para mover dados.

    Quando instala o gateway, instala automaticamente um controlador ODBC do Microsoft MongoDB utilizado para ligar ao MongoDB.

    > [!NOTE]
    > Você precisa usar o gateway para ligar a MongoDB mesmo que esteja hospedado em VMs Azure Iaa. Se estiver a tentar ligar-se a um caso de MongoDB alojado em nuvem, também pode instalar a instância de gateway no IaaS VM.

## <a name="getting-started"></a>Introdução
Pode criar um pipeline com uma atividade de cópia que move dados de uma loja de dados MongoDB no local utilizando diferentes ferramentas/APIs.

A forma mais fácil de criar um oleoduto é utilizar o **Copy Wizard**. Ver [Tutorial: Criar um pipeline utilizando o Copy Wizard](data-factory-copy-data-wizard-tutorial.md) para uma rápida passagem na criação de um oleoduto utilizando o assistente de dados Copy.

Também pode utilizar as seguintes ferramentas para criar um pipeline: **Visual Studio**, **Azure PowerShell,** **Azure Resource Manager,** **.NET API** e **REST API**. Consulte o tutorial de [atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções passo a passo para criar um oleoduto com uma atividade de cópia.

Quer utilize as ferramentas ou APIs, executa os seguintes passos para criar um pipeline que transfere dados de uma loja de dados de origem para uma loja de dados de lavatórios:

1. Crie **serviços ligados** para ligar as lojas de dados de entrada e saída à sua fábrica de dados.
2. Crie **conjuntos de dados** para representar dados de entrada e saída para a operação de cópia.
3. Crie um **pipeline** com uma atividade de cópia que leva um conjunto de dados como entrada e um conjunto de dados como uma saída.

Quando utiliza o assistente, as definições de JSON para estas entidades da Data Factory (serviços ligados, conjuntos de dados e o pipeline) são automaticamente criadas para si. Quando utiliza ferramentas/APIs (exceto .NET API), define estas entidades da Data Factory utilizando o formato JSON.  Para uma amostra com definições JSON para entidades da Data Factory que são usadas para copiar dados de uma loja de dados mongoDB no local, consulte [o exemplo JSON: Copiar dados da secção MongoDB para Azure Blob](#json-example-copy-data-from-mongodb-to-azure-blob) deste artigo.

As seguintes secções fornecem detalhes sobre as propriedades JSON que são usadas para definir entidades da Data Factory específicas da fonte mongoDB:

## <a name="linked-service-properties"></a>Propriedades de serviço ligadas
A tabela seguinte fornece descrição para elementos JSON específicos do serviço ligado **onPremisesMongoDB.**

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| tipo |A propriedade tipo deve ser definida para: **OnPremisesMongoDb** |Yes |
| servidor |Endereço IP ou nome de anfitrião do servidor MongoDB. |Yes |
| porta |Porta TCP que o servidor MongoDB utiliza para ouvir as ligações do cliente. |Valor opcional, padrão: 27017 |
| authenticationType |Básico, ou Anónimo. |Yes |
| nome de utilizador |Conta de utilizador para aceder a MongoDB. |Sim (se for utilizada a autenticação básica). |
| palavra-passe |A palavra-passe do utilizador. |Sim (se for utilizada a autenticação básica). |
| authSource |Nome da base de dados MongoDB que pretende utilizar para verificar as suas credenciais para autenticação. |Opcional (se for utilizada a autenticação básica). padrão: utiliza a conta de administração e a base de dados especificada através da base de dados PropriedadeName. |
| base de dados Nome |Nome da base de dados mongoDB a que pretende aceder. |Yes |
| gatewayName |Nome do portal que acede à loja de dados. |Yes |
| criptografadoCredential |Credencial encriptada por gateway. |Opcional |

## <a name="dataset-properties"></a>Dataset properties (Propriedades do conjunto de dados)
Para obter uma lista completa de secções & propriedades disponíveis para definir conjuntos de dados, consulte o artigo [Criar conjuntos de dados.](data-factory-create-datasets.md) Secções como estrutura, disponibilidade e política de um conjunto de dados JSON são semelhantes para todos os tipos de conjunto de dados (Azure SQL, Azure blob, Azure table, etc.).

A secção **typeProperties** é diferente para cada tipo de conjunto de dados e fornece informações sobre a localização dos dados na loja de dados. A secção typeProperties para conjunto de dados do tipo **MongoDbCollection** tem as seguintes propriedades:

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| coleçãoName |Nome da coleção na base de dados mongoDB. |Yes |

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy
Para obter uma lista completa das secções & propriedades disponíveis para definir atividades, consulte o artigo [Criar Pipelines.](data-factory-create-pipelines.md) Propriedades como nome, descrição, tabelas de entrada e saída, e política estão disponíveis para todos os tipos de atividades.

As propriedades disponíveis na secção **de tipoProperties** da atividade, por outro lado, variam com cada tipo de atividade. Para a atividade copy, variam dependendo dos tipos de fontes e pias.

Quando a fonte é do tipo **MongoDbSource,** as seguintes propriedades estão disponíveis na secção de tipos de propriedades:

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| consulta |Utilize a consulta personalizada para ler dados. |Cadeia de consulta SQL-92. Por exemplo: selecione * do MyTable. |Não (se o nome de **recolha** do conjunto de **dados** for especificado) |



## <a name="json-example-copy-data-from-mongodb-to-azure-blob"></a>Exemplo JSON: Copiar dados de MongoDB para Azure Blob
Este exemplo fornece definições JSON de amostra que pode usar para criar um oleoduto utilizando [o Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou o [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Mostra como copiar dados de um MongoDB no local para um Azure Blob Storage. No entanto, os dados podem ser copiados para qualquer um dos lavatórios [aqui](data-factory-data-movement-activities.md#supported-data-stores-and-formats) indicados utilizando a Atividade de Cópia na Fábrica de Dados Azure.

A amostra tem as seguintes entidades de fábrica de dados:

1. Um serviço ligado do tipo [OnPremisesMongoDb](#linked-service-properties).
2. Um serviço ligado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Um conjunto de [dados](data-factory-create-datasets.md) de entrada do tipo [MongoDbCollection](#dataset-properties).
4. Um conjunto de [dados](data-factory-create-datasets.md) de saída do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. Um [oleoduto](data-factory-create-pipelines.md) com Copy Activity que utiliza [MongoDbSource](#copy-activity-properties) e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

A amostra copia dados de uma consulta resulta na base de dados de MongoDB para uma bolha a cada hora. As propriedades JSON utilizadas nestas amostras são descritas em secções que seguem as amostras.

Como primeiro passo, configurar o gateway de gestão de dados de acordo com as instruções no artigo do [Data Management Gateway.](data-factory-data-management-gateway.md)

**Serviço ligado a MongoDB:**

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

**Serviço ligado a Azure Storage:**

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

**Conjunto de dados de entrada mongoDB:** Definição "externa": "verdadeiro" informa o serviço data Factory que a tabela é externa à fábrica de dados e não é produzida por uma atividade na fábrica de dados.

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

**Conjunto de dados de saída Azure Blob:**

Os dados são escritos para uma nova bolha a cada hora (frequência: hora, intervalo: 1). O caminho da pasta para a bolha é avaliado dinamicamente com base na hora de início da fatia que está a ser processada. O caminho da pasta utiliza partes do ano, mês, dia e horas da hora de início.

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

**Copiar a atividade num oleoduto com fonte mongoDB e pia Blob:**

O pipeline contém uma Atividade de Cópia que está configurada para utilizar os conjuntos de dados de entrada e saída acima e está programado para ser executado a cada hora. Na definição de JSON do gasoduto, o tipo **de fonte** é definido para **MongoDbSource** e o tipo **de pia** é definido para **BlobSink**. A consulta SQL especificada para a propriedade **de consulta** seleciona os dados na hora passada para copiar.

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


## <a name="schema-by-data-factory"></a>Schema by Data Factory
O serviço Azure Data Factory infere o esquema de uma coleção mongoDB utilizando os mais recentes 100 documentos da coleção. Se estes 100 documentos não contiverem esquema completo, algumas colunas podem ser ignoradas durante a operação da cópia.

## <a name="type-mapping-for-mongodb"></a>Tipo de mapeamento para MongoDB
Conforme mencionado no artigo [de atividades](data-factory-data-movement-activities.md) de movimento de dados, a atividade copy realiza conversões automáticas de tipo de origem para tipos de pia com a seguinte abordagem de 2 etapas:

1. Converter de tipos de origem nativa para .NET tipo
2. Converter de tipo .NET para tipo de pia nativa

Ao mover dados para o MongoDB, os seguintes mapeamentos são utilizados dos tipos MongoDB para os tipos .NET.

| Tipo MongoDB | .NET Tipo de quadro |
| --- | --- |
| Binário |Byte[] |
| Booleano |Booleano |
| Date |DateTime |
| NúmeroDouble |Double (Duplo) |
| NúmeroInt |Int32 |
| NúmeroLong |Int64 |
| ObjectID |Cadeia |
| Cadeia |Cadeia |
| UUID |GUID |
| Objeto |Renormalizado em colunas planas com "_" como separador aninhado |

> [!NOTE]
> Para saber mais sobre o suporte a matrizes que utilizem tabelas virtuais, consulte [o Suporte para tipos complexos utilizando](#support-for-complex-types-using-virtual-tables) a secção de tabelas virtuais abaixo.

Atualmente, os seguintes tipos de dados MongoDB não são suportados: DBPointer, JavaScript, Tecla Max/Min, Expressão Regular, Símbolo, Relógio, Indefinido

## <a name="support-for-complex-types-using-virtual-tables"></a>Suporte para tipos complexos que usam tabelas virtuais
A Azure Data Factory utiliza um controlador ODBC incorporado para ligar e copiar dados da sua base de dados MongoDB. Para tipos complexos como matrizes ou objetos com diferentes tipos nos documentos, o condutor re normaliza os dados em tabelas virtuais correspondentes. Especificamente, se uma tabela contiver tais colunas, o controlador gera as seguintes tabelas virtuais:

* Uma **tabela base**, que contém os mesmos dados que a tabela real, exceto para as colunas tipo complexas. A tabela base usa o mesmo nome que a mesa real que representa.
* Uma **tabela virtual** para cada coluna tipo complexo, que expande os dados aninhados. As tabelas virtuais são nomeadas usando o nome da mesa real, um separador "_" e o nome da matriz ou objeto.

As tabelas virtuais referem-se aos dados na tabela real, permitindo ao condutor aceder aos dados denormalizados. Consulte a secção Exemplo abaixo dos detalhes. Pode aceder ao conteúdo das matrizes mongoDB consultando e juntando as tabelas virtuais.

Pode utilizar o [Copy Wizard](data-factory-data-movement-activities.md#create-a-pipeline-with-copy-activity) para visualizar intuitivamente a lista de tabelas na base de dados mongoDB, incluindo as tabelas virtuais, e visualizar os dados no seu interior. Também pode construir uma consulta no Copy Wizard e validar para ver o resultado.

### <a name="example"></a>Exemplo
Por exemplo, "ExemploTable" abaixo é uma tabela MongoDB que tem uma coluna com uma matriz de Objetos em cada célula – Faturas, e uma coluna com uma matriz de tipos Scalar – Classificações.

| _id | Nome do Cliente | Faturas | Nível de Serviço | Classificações |
| --- | --- | --- | --- | --- |
| 1111 |ABC |[{invoice_id:"123", item:"torradeira", preço:"456", desconto:"0,2"}, {invoice_id:"124", item:"forno", preço: "1235", desconto: "0,2"}} |Silver |[5,6] |
| 2222 |XYZ |[{invoice_id:"135", item:"frigorífico", preço: "12543", desconto: "0,0"}] |Gold |[1,2] |

O condutor geraria várias tabelas virtuais para representar esta única tabela. A primeira tabela virtual é a tabela base denominada "ExemploTable", mostrada abaixo. A tabela base contém todos os dados da tabela original, mas os dados das matrizes foram omitidos e expandidos nas tabelas virtuais.

| _id | Nome do Cliente | Nível de Serviço |
| --- | --- | --- |
| 1111 |ABC |Silver |
| 2222 |XYZ |Gold |

As tabelas seguintes mostram as tabelas virtuais que representam as matrizes originais no exemplo. Estas tabelas contêm os seguintes:

* Uma referência à coluna de chave primária original correspondente à linha da matriz original (através da coluna _id)
* Uma indicação da posição dos dados dentro da matriz original
* Os dados expandidos para cada elemento dentro da matriz

Tabela "ExampleTable_Invoices":

| _id | ExampleTable_Invoices_dim1_idx | invoice_id | item | preço | Desconto |
| --- | --- | --- | --- | --- | --- |
| 1111 |0 |123 |torradeira |456 |0,2 |
| 1111 |1 |124 |forno |1235 |0,2 |
| 2222 |0 |135 |frigorífico |12543 |0,0 |

Tabela "ExampleTable_Ratings":

| _id | ExampleTable_Ratings_dim1_idx | ExampleTable_Ratings |
| --- | --- | --- |
| 1111 |0 |5 |
| 1111 |1 |6 |
| 2222 |0 |1 |
| 2222 |1 |2 |

## <a name="map-source-to-sink-columns"></a>Fonte do mapa para afundar colunas
Para obter informações sobre as colunas de mapeamento em conjunto de dados de origem para colunas no conjunto de dados da pia, consulte [as colunas de conjunto de dados de mapeamento na Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Leitura repetível de fontes relacionais
Ao copiar dados de lojas de dados relacionais, tenha em mente a repetibilidade para evitar resultados não intencionais. Na Azure Data Factory, pode repetir manualmente uma fatia. Também pode configurar a política de reagem para um conjunto de dados para que uma fatia seja re-executada quando ocorre uma falha. Quando uma fatia é reexame de qualquer forma, você precisa ter certeza de que os mesmos dados são lidos, não importa quantas vezes uma fatia é executada. Ver [leitura repetível de fontes relacionais](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Performance e Afinação
Consulte [copy Activity Performance & Guia de Afinação](data-factory-copy-activity-performance.md) para conhecer os fatores-chave que impactam o desempenho do movimento de dados (Copy Activity) na Azure Data Factory e várias formas de otimizá-lo.

## <a name="next-steps"></a>Passos Seguintes
Consulte [os dados de movimento entre as instalações e](data-factory-move-data-between-onprem-and-cloud.md) o artigo em nuvem para obter instruções passo a passo para a criação de um pipeline de dados que transfere dados de uma loja de dados no local para uma loja de dados Azure.
