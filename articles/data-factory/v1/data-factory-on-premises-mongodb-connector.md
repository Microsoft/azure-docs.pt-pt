---
title: Mover dados do MongoDB
description: Saiba como mover dados da base de dados MongoDB utilizando a Azure Data Factory.
services: data-factory
author: linda33wj
ms.author: jingwang
manager: shwang
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/13/2018
ms.openlocfilehash: edddd100bddab1d642a8169353298a2d20620274
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281343"
---
# <a name="move-data-from-mongodb-using-azure-data-factory"></a>Mova dados de MongoDB usando a Fábrica de Dados Azure

> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que está a utilizar:"]
> * [Versão 1](data-factory-on-premises-mongodb-connector.md)
> * [Versão 2 (versão atual)](../connector-mongodb.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, consulte o [conector MongoDB em V2](../connector-mongodb.md).


Este artigo explica como usar a Atividade de Cópia na Fábrica de Dados Azure para mover dados de uma base de dados mongoDB no local. Baseia-se no artigo Atividades do Movimento de [Dados,](data-factory-data-movement-activities.md) que apresenta uma visão geral do movimento de dados com a atividade de cópia.

Pode copiar dados de uma loja de dados MongoDB no local para qualquer loja de dados de sink suportado. Para obter uma lista de lojas de dados suportadas como pias pela atividade de cópia, consulte a tabela de lojas de [dados suportadas.](data-factory-data-movement-activities.md#supported-data-stores-and-formats) Atualmente, a fábrica de dados suporta apenas a transferência de dados de uma loja de dados MongoDB para outras lojas de dados, mas não para transferir dados de outras lojas de dados para uma loja de dados MongoDB.

## <a name="prerequisites"></a>Pré-requisitos
Para que o serviço Azure Data Factory possa ligar-se à sua base de dados MongoDB no local, deve instalar os seguintes componentes:

- As versões Suportadas MongoDB são: 2.4, 2.6, 3.0, 3.2, 3.4 e 3.6.
- Gateway de Gestão de Dados na mesma máquina que acolhe a base de dados ou numa máquina separada para evitar competir por recursos com a base de dados. Data Management Gateway é um software que liga fontes de dados no local a serviços de nuvem de forma segura e gerida. Consulte o artigo gateway de [gestão](data-factory-data-management-gateway.md) de dados para obter detalhes sobre gateway de gestão de dados. Consulte [mover os dados do local para](data-factory-move-data-between-onprem-and-cloud.md) o artigo em nuvem para obter instruções passo a passo sobre a configuração do gateway de um pipeline de dados para mover dados.

    Quando instala o gateway, instala automaticamente um controlador Microsoft MongoDB ODBC utilizado para ligar ao MongoDB.

    > [!NOTE]
    > Você precisa usar a porta de entrada para ligar ao MongoDB mesmo que esteja hospedado em VMs Azure IaaS. Se estiver a tentar ligar-se a uma instância de MongoDB alojada na nuvem, também pode instalar a instância de gateway no IaaS VM.

## <a name="getting-started"></a>Introdução
Pode criar um pipeline com uma atividade de cópia que transfere dados de uma loja de dados MongoDB no local utilizando diferentes ferramentas/APIs.

A maneira mais fácil de criar um pipeline é utilizar o **Assistente de Cópia**. Ver [Tutorial: Crie um pipeline utilizando o Copy Wizard](data-factory-copy-data-wizard-tutorial.md) para uma rápida passagem na criação de um pipeline utilizando o assistente de dados Copy.

Também pode utilizar as seguintes ferramentas para criar um pipeline: **Estúdio Visual,** **Azure PowerShell,** **Modelo de Gestor de Recursos Azure,** **.NET API**e **REST API**. Consulte o tutorial de [atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções passo a passo para criar um pipeline com uma atividade de cópia.

Quer utilize as ferramentas ou APIs, executa os seguintes passos para criar um pipeline que transfere dados de uma loja de dados de origem para uma loja de dados de sumidouro:

1. Crie **serviços ligados** para ligar as lojas de dados de entrada e saída à sua fábrica de dados.
2. Crie **conjuntos** de dados para representar dados de entrada e saída para a operação de cópia.
3. Crie um **pipeline** com uma atividade de cópia que tome um conjunto de dados como entrada e um conjunto de dados como uma saída.

Quando utiliza o assistente, as definições jSON para estas entidades da Fábrica de Dados (serviços ligados, conjuntos de dados e o pipeline) são automaticamente criadas para si. Quando utiliza ferramentas/APIs (exceto .NET API), define estas entidades da Fábrica de Dados utilizando o formato JSON.  Para obter uma amostra com definições JSON para entidades da Data Factory que são usadas para copiar dados de uma loja de dados MongoDB no local, consulte o [exemplo da JSON: Copiar dados do MongoDB para a secção Azure Blob](#json-example-copy-data-from-mongodb-to-azure-blob) deste artigo.

As seguintes secções fornecem detalhes sobre as propriedades JSON que são usadas para definir entidades da Fábrica de Dados específicas da fonte mongoDB:

## <a name="linked-service-properties"></a>Propriedades de serviço seletos
O quadro seguinte fornece descrição para elementos JSON específicos do serviço ligado **onPremisesMongoDB.**

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| tipo |A propriedade tipo deve ser definida para: **OnPremisesMongoDb** |Sim |
| servidor |Endereço IP ou nome de anfitrião do servidor MongoDB. |Sim |
| porta |Porta TCP que o servidor MongoDB usa para ouvir ligações ao cliente. |Opcional, valor por defeito: 27017 |
| authenticationType |Básico, ou Anónimo. |Sim |
| o nome de utilizador |Conta de utilizador para aceder ao MongoDB. |Sim (se for utilizada a autenticação básica). |
| palavra-passe |A palavra-passe do utilizador. |Sim (se for utilizada a autenticação básica). |
| authSource |Nome da base de dados MongoDB que pretende utilizar para verificar as suas credenciais para autenticação. |Opcional (se for utilizada a autenticação básica). predefinição: utiliza a conta de administração e a base de dados especificada utilizando a propriedade databaseName. |
| nome da base de dados |Nome da base de dados MongoDB a que pretende aceder. |Sim |
| nome gateway |Nome do portal que acede à loja de dados. |Sim |
| credenta encriptado |Credencial encriptado por portal. |Opcional |

## <a name="dataset-properties"></a>Dataset properties (Propriedades do conjunto de dados)
Para obter uma lista completa de secções & propriedades disponíveis para definir conjuntos de dados, consulte o artigo Criação de conjuntos de [dados.](data-factory-create-datasets.md) Secções como estrutura, disponibilidade e política de um conjunto de dados JSON são semelhantes para todos os tipos de conjuntos de dados (Azure SQL, Azure blob, tabela Azure, etc.).

A secção **typeProperties** é diferente para cada tipo de conjunto de dados e fornece informações sobre a localização dos dados na loja de dados. A secção TypeProperties para conjunto de dados do tipo **MongoDbCollection** tem as seguintes propriedades:

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| coleçãoNome |Nome da coleção na base de dados mongoDB. |Sim |

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy
Para obter uma lista completa de secções & propriedades disponíveis para definir atividades, consulte o artigo [Creating Pipelines.](data-factory-create-pipelines.md) Propriedades como nome, descrição, tabelas de entrada e saída, e a política estão disponíveis para todos os tipos de atividades.

As propriedades disponíveis na secção **tipoPropriedades** da atividade por outro lado variam com cada tipo de atividade. Para a atividade de Cópia, variam dependendo dos tipos de fontes e pias.

Quando a fonte é do tipo **MongoDbSource** as seguintes propriedades estão disponíveis na secção typeProperties:

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| consulta |Use a consulta personalizada para ler dados. |Fio de consulta SQL-92. Por exemplo: selecione * do MyTable. |Não (se for especificado o nome da **recolha do conjunto** de **dados)** |



## <a name="json-example-copy-data-from-mongodb-to-azure-blob"></a>Exemplo jSON: Copiar dados de MongoDB para Azure Blob
Este exemplo fornece definições jSON de amostra que pode usar para criar um pipeline utilizando [o Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou o [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Mostra como copiar dados de um MongoDB no local para um Armazenamento De Blob Azure. No entanto, os dados podem ser copiados para qualquer um dos lavatórios [aqui](data-factory-data-movement-activities.md#supported-data-stores-and-formats) indicados utilizando a Atividade de Cópia na Fábrica de Dados Azure.

A amostra tem as seguintes entidades fabris de dados:

1. Um serviço de tipo [onPremisesMongoDb](#linked-service-properties).
2. Um serviço ligado do tipo [AzureStorage.](data-factory-azure-blob-connector.md#linked-service-properties)
3. Um conjunto de [dados](data-factory-create-datasets.md) de entrada do tipo [MongoDbCollection](#dataset-properties).
4. Um [conjunto](data-factory-create-datasets.md) de dados de saída do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. Um [pipeline](data-factory-create-pipelines.md) com Copy Activity que utiliza [MongoDbSource](#copy-activity-properties) e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

A amostra copia dados de uma consulta resultam na base de dados do MongoDB a uma bolha a cada hora. As propriedades JSON utilizadas nestas amostras são descritas em secções que seguem as amostras.

Como primeiro passo, configurar o portal de gestão de dados de acordo com as instruções do artigo Gateway de Gestão de [Dados.](data-factory-data-management-gateway.md)

**Serviço ligado ao MongoDB:**

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

**Serviço ligado ao Armazenamento Azure:**

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

Conjunto de dados de **entrada MongoDB:** Definição "externa": "verdadeira" informa o serviço data Factory de que a tabela é externa à fábrica de dados e não é produzida por uma atividade na fábrica de dados.

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

**Conjunto de dados de saída de Azure Blob:**

Os dados são escritos para uma nova bolha a cada hora (frequência: hora, intervalo: 1). O caminho da pasta para a bolha é avaliado dinamicamente com base no tempo de início da fatia que está a ser processada. O caminho da pasta utiliza partes ano, mês, dia e horas da hora de início.

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

**Copiar atividade num oleoduto com fonte mongoDB e pia Blob:**

O pipeline contém uma Atividade de Cópia que está configurada para utilizar os conjuntos de dados de entrada e saída acima e está programado para funcionar a cada hora. Na definição JSON do gasoduto, o tipo de **origem** é definido para **MongoDbSource** e o tipo de **pia** é definido para **BlobSink**. A consulta SQL especificada para a propriedade de **consulta** seleciona os dados na última hora para copiar.

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
O serviço Azure Data Factory infere o esquema de uma coleção mongoDB utilizando os últimos 100 documentos da coleção. Se estes 100 documentos não contiverem esquema completo, algumas colunas podem ser ignoradas durante a operação de cópia.

## <a name="type-mapping-for-mongodb"></a>Mapeamento de tipo para MongoDB
Conforme mencionado no artigo de atividades de movimento de [dados,](data-factory-data-movement-activities.md) a atividade de cópia realiza conversões automáticas de tipos de origem para tipos de sink com a seguinte abordagem de 2 passos:

1. Converter de tipos de origem nativa para .NET tipo
2. Converter do tipo .NET para o tipo de pia nativa

Ao mover dados para MongoDB, os seguintes mapeamentos são utilizados de tipos MongoDB para tipos .NET.

| Tipo MongoDB | Tipo de quadro .NET |
| --- | --- |
| Binário |Byte[] |
| Booleano |Booleano |
| Date |DateTime |
| Número Duplo |Double |
| NúmeroInt |Int32 |
| Número longo |Int64 |
| ObjectID |Cadeia |
| Cadeia |Cadeia |
| UUID |GUID |
| Objeto |Renormalizado em colunas achatadas com "_" como separador aninhada |

> [!NOTE]
> Para aprender sobre o suporte para matrizes usando tabelas virtuais, consulte o [Suporte para tipos complexos usando a](#support-for-complex-types-using-virtual-tables) secção de tabelas virtuais abaixo.

Atualmente, os seguintes tipos de dados MongoDB não são suportados: DBPointer, JavaScript, Max/Min key, Regular Expression, Symbol, Timestamp, Undefined

## <a name="support-for-complex-types-using-virtual-tables"></a>Suporte para tipos complexos que usam tabelas virtuais
A Azure Data Factory utiliza um controlador ODBC incorporado para ligar e copiar dados da sua base de dados MongoDB. Para tipos complexos como matrizes ou objetos com diferentes tipos em todos os documentos, o condutor renormaliza os dados em tabelas virtuais correspondentes. Especificamente, se uma tabela contiver tais colunas, o condutor gera as seguintes tabelas virtuais:

* Uma **tabela base,** que contém os mesmos dados que a tabela real, exceto as colunas de tipo complexos. A tabela base usa o mesmo nome que a verdadeira tabela que representa.
* Uma **tabela virtual** para cada coluna de tipo complexo, que expande os dados aninhados. As tabelas virtuais são nomeadas usando o nome da mesa real, um separador "_" e o nome da matriz ou objeto.

As tabelas virtuais referem-se aos dados na tabela real, permitindo ao condutor aceder aos dados desnormalizados. Consulte a secção Exemplo abaixo dos detalhes. Você pode aceder ao conteúdo das matrizes MongoDB consultando e juntando as tabelas virtuais.

Pode utilizar o [Copy Wizard](data-factory-data-movement-activities.md#create-a-pipeline-with-copy-activity) para visualizar intuitivamente a lista de tabelas na base de dados do MongoDB, incluindo as tabelas virtuais, e pré-visualizar os dados no seu interior. Também pode construir uma consulta no Copy Wizard e validar para ver o resultado.

### <a name="example"></a>Exemplo
Por exemplo, "ExampleTable" abaixo é uma tabela MongoDB que tem uma coluna com uma matriz de Objetos em cada célula – Faturas e uma coluna com uma matriz de tipos Scalar – Classificações.

| _id | Nome do cliente | Faturas | Nível de Serviço | Classificações |
| --- | --- | --- | --- | --- |
| 1111 |ABC |[{invoice_id:"123", item:"torradeira", preço:"456", desconto:"0.2"}, {invoice_id:"124", item:"forno", preço: "1235", desconto: "0,2"}] |Silver |[5,6] |
| 2222 |XYZ |[{invoice_id:"135", item:"frigorífico", preço: "12543", desconto: "0.0"}] |Gold |[1,2] |

O condutor geraria várias tabelas virtuais para representar esta única tabela. A primeira tabela virtual é a tabela base chamada "ExampleTable", mostrada abaixo. A tabela base contém todos os dados da tabela original, mas os dados das matrizes foram omitidos e são expandidos nas tabelas virtuais.

| _id | Nome do cliente | Nível de Serviço |
| --- | --- | --- |
| 1111 |ABC |Silver |
| 2222 |XYZ |Gold |

As tabelas seguintes mostram as tabelas virtuais que representam as matrizes originais no exemplo. Estas tabelas contêm o seguinte:

* Uma referência de volta à coluna principal original correspondente à linha da matriz original (através da coluna _id)
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
Para aprender sobre as colunas de mapeamento em conjunto de dados de origem para colunas em conjunto de dados de sumidouro, consulte [mapeando colunas](data-factory-map-columns.md)de conjunto de dados na Azure Data Factory .

## <a name="repeatable-read-from-relational-sources"></a>Leitura repetível de fontes relacionais
Ao copiar dados de lojas de dados relacionais, tenha em mente a repetível para evitar resultados não intencionais. Na Azure Data Factory, pode reproduzir uma fatia manualmente. Também pode configurar a política de retry para um conjunto de dados para que uma fatia seja reexecutada quando ocorre uma falha. Quando uma fatia é reexecutada de qualquer forma, você precisa ter certeza de que os mesmos dados são lidos, não importa quantas vezes uma fatia é executada. Ver [Leitura repetível a partir de fontes relacionais](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Desempenho e Afinação
Consulte o [Copy Activity Performance & Tuning Guide](data-factory-copy-activity-performance.md) para conhecer os fatores-chave que impactam o desempenho do movimento de dados (Copy Activity) na Fábrica de Dados Do Azure e várias formas de o otimizar.

## <a name="next-steps"></a>Passos Seguintes
Consulte os dados entre as [instalações e o](data-factory-move-data-between-onprem-and-cloud.md) artigo em nuvem para obter instruções passo a passo para criar um pipeline de dados que transfifique os dados de uma loja de dados no local para uma loja de dados Azure.
