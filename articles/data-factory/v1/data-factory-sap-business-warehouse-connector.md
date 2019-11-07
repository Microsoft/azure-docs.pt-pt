---
title: Mover dados do SAP Business Warehouse usando o Azure Data Factory
description: Saiba mais sobre como mover dados do SAP Business Warehouse usando o Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 47bc2db8730ebdedd180646d2fb86b642bbc631d
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73666045"
---
# <a name="move-data-from-sap-business-warehouse-using-azure-data-factory"></a>Mover dados do SAP Business Warehouse usando o Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço de Data Factory que você está usando:"]
> * [Versão 1](data-factory-sap-business-warehouse-connector.md)
> * [Versão 2 (versão atual)](../connector-sap-business-warehouse.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se você estiver usando a versão atual do serviço de Data Factory, consulte [conector do SAP Business Warehouse na v2](../connector-sap-business-warehouse.md).


Este artigo explica como usar a atividade de cópia no Azure Data Factory para mover dados de um SAP Business Warehouse (BW) local. Ele se baseia no artigo [atividades de movimentação de dados](data-factory-data-movement-activities.md) , que apresenta uma visão geral da movimentação de dados com a atividade de cópia.

Você pode copiar dados de um armazenamento de dados do SAP Business Warehouse local para qualquer armazenamento de dados de coletor com suporte. Para obter uma lista de armazenamentos de dados com suporte como coletores pela atividade de cópia, consulte a tabela [armazenamentos de dados com suporte](data-factory-data-movement-activities.md#supported-data-stores-and-formats) . Atualmente, o data Factory dá suporte apenas à movimentação de dados de um SAP Business Warehouse para outros armazenamentos de dados, mas não para a movimentação de dados de outros armazenamentos de dados para um SAP Business Warehouse. 

## <a name="supported-versions-and-installation"></a>Versões e instalação com suporte
Este conector dá suporte ao SAP Business Warehouse versão 7. x. Ele dá suporte à cópia de dados de InfoCubes e QueryCubes (incluindo consultas BEx) usando consultas MDX.

Para habilitar a conectividade com a instância do SAP BW, instale os seguintes componentes:
- **Gateway de gerenciamento de dados**: o serviço data Factory dá suporte à conexão com armazenamentos de dados locais (incluindo o SAP Business Warehouse) usando um componente chamado gateway gerenciamento de dados. Para saber mais sobre Gerenciamento de Dados gateway e instruções passo a passo para configurar o gateway, consulte o artigo [mover dados entre o repositório de dados local para o armazenamento de dados na nuvem](data-factory-move-data-between-onprem-and-cloud.md) . O gateway é necessário mesmo que o SAP Business Warehouse esteja hospedado em uma VM (máquina virtual) IaaS do Azure. Você pode instalar o gateway na mesma VM que o armazenamento de dados ou em uma VM diferente, contanto que o gateway possa se conectar ao banco de dado.
- **Biblioteca do SAP NetWeaver** no computador do gateway. Você pode obter a biblioteca do SAP NetWeaver do administrador do SAP ou diretamente do [centro de download de software SAP](https://support.sap.com/swdc). Pesquise o **#1025361 de observação SAP** para obter o local de download para a versão mais recente. Verifique se a arquitetura da biblioteca do SAP NetWeaver (32 bits ou 64 bits) corresponde à instalação do seu gateway. Em seguida, instale todos os arquivos incluídos no SDK RFC do SAP NetWeaver de acordo com a nota SAP. A biblioteca do SAP NetWeaver também está incluída na instalação das ferramentas de cliente SAP.

> [!TIP]
> Coloque as DLLs extraídas do SDK da RFC NetWeaver na pasta system32.

## <a name="getting-started"></a>Introdução
Você pode criar um pipeline com uma atividade de cópia que move dados de um armazenamento de dados Cassandra local usando diferentes ferramentas/APIs. 

- A maneira mais fácil de criar um pipeline é usar o **Assistente de cópia**. Consulte [tutorial: criar um pipeline usando o assistente de cópia](data-factory-copy-data-wizard-tutorial.md) para obter uma explicação rápida sobre como criar um pipeline usando o assistente para copiar dados. 
- Você também pode usar as seguintes ferramentas para criar um pipeline: **Visual Studio**, **Azure PowerShell**, **modelo de Azure Resource Manager**, **API .net**e **API REST**. Confira o [tutorial de atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções detalhadas para criar um pipeline com uma atividade de cópia. 

Se você usar as ferramentas ou APIs, execute as seguintes etapas para criar um pipeline que move dados de um armazenamento de dados de origem para um armazenamento de dados de coletor:

1. Crie **Serviços vinculados** para vincular armazenamentos de dados de entrada e saída ao seu data Factory.
2. Crie **conjuntos** de dados para representar o dado de entrada e saída para a operação de cópia. 
3. Crie um **pipeline** com uma atividade de cópia que usa um conjunto de dados como uma entrada e um conjunto como uma saída. 

Quando você usa o assistente, as definições de JSON para essas entidades de Data Factory (serviços vinculados, conjuntos de valores e o Pipeline) são criadas automaticamente para você. Ao usar ferramentas/APIs (exceto a API .NET), você define essas entidades de Data Factory usando o formato JSON.  Para obter um exemplo com definições de JSON para Data Factory entidades usadas para copiar dados de um SAP Business Warehouse local, confira a seção [exemplo de JSON: copiar dados do SAP Business Warehouse para o blob do Azure](#json-example-copy-data-from-sap-business-warehouse-to-azure-blob) deste artigo. 

As seções a seguir fornecem detalhes sobre as propriedades JSON que são usadas para definir Data Factory entidades específicas para um armazenamento de dados de SAP BW:

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado
A tabela a seguir fornece a descrição para elementos JSON específicos para o serviço vinculado do SAP Business Warehouse (BW).

Propriedade | Descrição | Valores permitidos | Necessário
-------- | ----------- | -------------- | --------
servidor | Nome do servidor no qual reside a instância de SAP BW. | string | Sim
Número | Número de sistema do sistema de SAP BW. | Número decimal de dois dígitos representado como uma cadeia de caracteres. | Sim
clientId | ID do cliente do cliente no sistema SAP W. | Número decimal de três dígitos representado como uma cadeia de caracteres. | Sim
o nome de utilizador | Nome do usuário que tem acesso ao servidor SAP | string | Sim
palavra-passe | A palavra-passe do utilizador. | string | Sim
gatewayName | Nome do gateway que o serviço de Data Factory deve usar para se conectar à instância de SAP BW local. | string | Sim
encryptedCredential | A cadeia de caracteres de credencial criptografada. | string | Não

## <a name="dataset-properties"></a>Propriedades de DataSet
Para obter uma lista completa das seções & propriedades disponíveis para definir os conjuntos de valores, consulte o artigo [criando conjuntos](data-factory-create-datasets.md) de itens. As seções como estrutura, disponibilidade e política de um conjunto de dados JSON são semelhantes para todos os tipos de conjunto de dados (SQL do Azure, BLOB do Azure, tabela do Azure, etc.).

A seção **typeproperties** é diferente para cada tipo de conjunto de dados e fornece informações sobre o local dos dados no repositório de dados. Não há propriedades específicas do tipo com suporte para o conjunto de SAP BW do tipo **RelationalTable**. 


## <a name="copy-activity-properties"></a>Propriedades da atividade Copy
Para obter uma lista completa das seções & propriedades disponíveis para definir as atividades, consulte o artigo [criando pipelines](data-factory-create-pipelines.md) . As propriedades, como nome, descrição, tabelas de entrada e saída, são políticas disponíveis para todos os tipos de atividades.

Enquanto que as propriedades disponíveis na seção **typeproperties** da atividade variam de acordo com cada tipo de atividade. Para a atividade de cópia, elas variam de acordo com os tipos de fontes e coletores.

Quando a origem na atividade de cópia é do tipo **RelationalSource** (que inclui SAP BW), as seguintes propriedades estão disponíveis na seção typeproperties:

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| consulta | Especifica a consulta MDX para ler dados da instância de SAP BW. | Consulta MDX. | Sim |


## <a name="json-example-copy-data-from-sap-business-warehouse-to-azure-blob"></a>Exemplo de JSON: copiar dados do SAP Business Warehouse para o blob do Azure
O exemplo a seguir fornece exemplos de definições de JSON que você pode usar para criar um pipeline usando o [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Este exemplo mostra como copiar dados de um SAP Business Warehouse local para um armazenamento de BLOBs do Azure. No entanto, os dados podem ser copiados **diretamente** para qualquer um dos coletores declarados [aqui](data-factory-data-movement-activities.md#supported-data-stores-and-formats) usando a atividade de cópia no Azure data Factory.  

> [!IMPORTANT]
> Este exemplo fornece trechos de código JSON. Ele não inclui instruções passo a passo para criar o data factory. Confira o artigo [movendo dados entre os locais e a nuvem no local](data-factory-move-data-between-onprem-and-cloud.md) para obter instruções passo a passo.

O exemplo tem as seguintes entidades de data factory:

1. Um serviço vinculado do tipo [SapBw](#linked-service-properties).
2. Um serviço vinculado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Um [conjunto](data-factory-create-datasets.md) de dados de entrada do tipo [RelationalTable](#dataset-properties).
4. Um [conjunto](data-factory-create-datasets.md) de uma saída do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. Um [pipeline](data-factory-create-pipelines.md) com atividade de cópia que usa [RelationalSource](#copy-activity-properties) e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

O exemplo copia dados de uma instância do SAP Business Warehouse para um blob do Azure a cada hora. As propriedades JSON usadas nesses exemplos são descritas em seções após os exemplos.

Como uma primeira etapa, configure o gateway de gerenciamento de dados. As instruções estão no artigo [movendo dados entre os locais e a nuvem do local](data-factory-move-data-between-onprem-and-cloud.md) .

### <a name="sap-business-warehouse-linked-service"></a>Serviço vinculado do SAP Business Warehouse
Esse serviço vinculado vincula sua instância de SAP BW ao data factory. A propriedade Type é definida como **SapBw**. A seção typeproperties fornece informações de conexão para a instância de SAP BW. 

```json
{
    "name": "SapBwLinkedService",
    "properties":
    {
        "type": "SapBw",
        "typeProperties":
        {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client id>",
            "username": "<SAP user>",
            "password": "<Password for SAP user>",
            "gatewayName": "<gateway name>"
        }
    }
}
```

### <a name="azure-storage-linked-service"></a>Serviço ligado do Storage do Azure
Esse serviço vinculado vincula sua conta de armazenamento do Azure ao data factory. A propriedade Type é definida como **AzureStorage**. A seção typeproperties fornece informações de conexão para a conta de armazenamento do Azure.

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

### <a name="sap-bw-input-dataset"></a>SAP BW conjunto de dados de entrada
Este conjunto de DataSet define o conjunto de negócios do SAP Business Warehouse. Você define o tipo do conjunto de Data Factory DataSet como **RelationalTable**. No momento, você não especifica nenhuma propriedade específica de tipo para um conjunto de SAP BW DataSet. A consulta na definição da atividade de cópia especifica quais dados ler da instância do SAP BW. 

Definir a propriedade external como true informa ao serviço de Data Factory que a tabela é externa à data factory e não é produzida por uma atividade no data factory.

As propriedades Frequency e Interval definem a agenda. Nesse caso, os dados são lidos na instância de SAP BW por hora. 

```json
{
    "name": "SapBwDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "SapBwLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```



### <a name="azure-blob-output-dataset"></a>Conjunto de dados de saída do Blob do Azure
Esse conjunto de DataSet define o conjunto de resultados de saída do blob do Azure. A propriedade Type é definida como AzureBlob. A seção typeproperties fornece o local em que os dados copiados da instância de SAP BW são armazenados. Os dados são gravados em um novo BLOB a cada hora (frequência: hora, intervalo: 1). O caminho da pasta para o blob é avaliado dinamicamente com base na hora de início da fatia que está sendo processada. O caminho da pasta usa as partes ano, mês, dia e horas da hora de início.

```json
{
    "name": "AzureBlobDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/sapbw/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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


### <a name="pipeline-with-copy-activity"></a>Pipeline com atividade de cópia
O pipeline contém uma atividade de cópia configurada para usar os conjuntos de dados de entrada e saída e está agendada para ser executada a cada hora. Na definição de JSON do pipeline, o tipo de **origem** é definido como **RelationalSource** (para SAP BW origem) e o tipo de **coletor** está definido como **BlobSink**. A consulta especificada para a propriedade de **consulta** seleciona os dados na última hora a serem copiados.

```json
{
    "name": "CopySapBwToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "RelationalSource",
                        "query": "<MDX query for SAP BW>"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "SapBwDataset"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobDataSet"
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
                "name": "SapBwToBlob"
            }
        ],
        "start": "2017-03-01T18:00:00Z",
        "end": "2017-03-01T19:00:00Z"
    }
}
```



### <a name="type-mapping-for-sap-bw"></a>Mapeamento de tipo para SAP BW
Conforme mencionado no artigo sobre as [atividades de movimentação de dados](data-factory-data-movement-activities.md) , a atividade de cópia executa conversões automáticas dos tipos de origem nos tipos de coletor com a seguinte abordagem de duas etapas:

1. Converter de tipos de origem nativos em tipo .NET
2. Converter do tipo .NET para o tipo de coletor nativo

Ao mover dados do SAP BW, os seguintes mapeamentos são usados de tipos de SAP BW para tipos .NET.

Tipo de dados no dicionário ABAP | Tipo de dados .NET
-------------------------------- | --------------
ACCP |  inteiro
º | String
CLNT | String
CURR | Vírgula
CUKY | String
DEZ | Vírgula
FLTP | Clique
INT1 | Minuciosa
INT2 | Int16
INT4 | inteiro
Lima | String
LCHR | String
LRAW | Byte []
PREC | Int16
QUAN | Vírgula
RECEBEM | Byte []
RAWSTRING | Byte []
Strings | String
UNIDADE | String
DATS | String
NUMC | String
TIMS | String

> [!NOTE]
> Para mapear colunas do conjunto de fonte de origem para colunas do conjunto de coleta, consulte [mapeando colunas do conjunto de linhas no Azure data Factory](data-factory-map-columns.md).


## <a name="map-source-to-sink-columns"></a>Mapear origem para colunas do coletor
Para saber mais sobre como mapear colunas no conjunto de informações de origem para colunas no conjunto de informações do coletor, confira [mapeando colunas Azure data Factory do conjunto de](data-factory-map-columns.md)informações

## <a name="repeatable-read-from-relational-sources"></a>Leitura repetida de fontes relacionais
Ao copiar dados de armazenamentos de dados relacionais, tenha em mente a capacidade de repetição para evitar resultados indesejados. No Azure Data Factory, você pode executar novamente uma fatia manualmente. Você também pode configurar a política de repetição para um conjunto de uma para que uma fatia seja executada novamente quando ocorrer uma falha. Quando uma fatia é executada novamente de qualquer forma, você precisa garantir que os mesmos dados sejam lidos, independentemente de quantas vezes uma fatia é executada. Consulte [leitura repetida de fontes relacionais](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources)

## <a name="performance-and-tuning"></a>Desempenho e ajuste
Consulte [Guia de ajuste do desempenho de atividade de cópia &](data-factory-copy-activity-performance.md) para saber mais sobre os principais fatores que afetam o desempenho da movimentação de dados (atividade de cópia) no Azure data Factory e várias maneiras de otimizá-lo.
