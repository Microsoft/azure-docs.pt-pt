---
title: Copiar dados de uma tabela SAP
description: Saiba como copiar dados de uma tabela SAP para lojas de dados de sumidouros suportados utilizando uma atividade de cópia num pipeline da Azure Data Factory.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/01/2020
ms.openlocfilehash: 1970709dea67111bfd8b90f9fc315a3b044b2ab9
ms.sourcegitcommit: 693df7d78dfd5393a28bf1508e3e7487e2132293
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92900266"
---
# <a name="copy-data-from-an-sap-table-by-using-azure-data-factory"></a>Copiar dados de uma tabela SAP utilizando a Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve como utilizar a atividade de cópia na Azure Data Factory para copiar dados de uma tabela SAP. Para obter mais informações, consulte [a visão geral da atividade da Cópia.](copy-activity-overview.md)

>[!TIP]
>Para obter o apoio global da ADF no cenário de integração de dados SAP, consulte [a integração de dados SAP utilizando o papel branco da Azure Data Factory](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf) com introdução detalhada em cada conector SAP, comparação e orientação.

## <a name="supported-capabilities"></a>Capacidades suportadas

Este conector de mesa SAP é suportado para as seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com [matriz de fonte/pia suportada](copy-activity-overview.md)
- [Atividade de procura](control-flow-lookup-activity.md)

Pode copiar dados de uma tabela SAP para qualquer loja de dados de lavatórios suportados. Para obter uma lista das lojas de dados que são suportadas como fontes ou sumidouros pela atividade de cópia, consulte a tabela [de lojas de dados suportadas.](copy-activity-overview.md#supported-data-stores-and-formats)

Especificamente, este conector de mesa SAP suporta:

- Cópia de dados de uma tabela SAP em:

  - Cabo ERP Central Da versão 7.01 ou posterior (num recente Pacote de Pacotes de Suporte SAP lançado após 2015).
  - Versão 7.01 ou posterior (num recente Pacote de Pacotes de Suporte SAP lançado após 2015).
  - SAP S/4HANA.
  - Outros produtos na versão 7.01 ou mais tarde do SAP Business Suite (num recente Pacote de Suporte SAP Lançado após 2015).

- Copiando dados de uma mesa transparente SAP, uma mesa agrupada, uma mesa agrupada e uma vista.
- Copiar dados utilizando a autenticação básica ou as comunicações de rede seguras (SNC), se o SNC estiver configurado.
- Ligação a um servidor de aplicações SAP ou servidor de mensagens SAP.
- Recuperação de dados por padrão ou RFC personalizado.

A versão 7.01 ou posterior refere-se à versão SAP NetWeaver em vez da versão SAP ECC. Por exemplo, o SAP ECC 6.0 EHP 7 em geral tem a versão NetWeaver >=7.4. Caso não tenha a certeza sobre o seu ambiente, aqui estão os passos para confirmar a versão do seu sistema SAP:

1. Utilize o SAP GUI para ligar ao sistema SAP. 
2. Ir para o **Estado do**  ->  **Sistema** . 
3. Verifique a libertação do SAP_BASIS, certifique-se de que é igual ou superior a 701.  
      ![Verifique SAP_BASIS](./media/connector-sap-table/sap-basis.png)

## <a name="prerequisites"></a>Pré-requisitos

Para utilizar este conector de mesa SAP, tem de:

- Configurar um tempo de integração auto-hospedado (versão 3.17 ou posterior). Para obter mais informações, consulte [Criar e configurar um tempo de integração auto-organizado.](create-self-hosted-integration-runtime.md)

- Descarregue o Conector SAP de 64 [bits para o Microsoft .NET 3.0](https://support.sap.com/en/product/connectors/msnet.html) do site da SAP e instale-o na máquina de execução de integração auto-hospedada. Durante a instalação, certifique-se de que seleciona os **Conjuntos de Instalação para** a opção GAC na janela **de etapas de configuração opcionais.**

  ![Instale o conector SAP para .NET](./media/connector-sap-business-warehouse-open-hub/install-sap-dotnet-connector.png)

- O utilizador SAP que está a ser utilizado no conector de tabela DATA Factory SAP deve ter as seguintes permissões:

  - Autorização para a utilização de destinos de Chamada de Função Remota (RFC).
  - Permissões para a atividade executar do S_SDSAUTH objeto de autorização.

## <a name="get-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As secções seguintes fornecem detalhes sobre propriedades que são usadas para definir as entidades da Data Factory específicas do conector de mesa SAP.

## <a name="linked-service-properties"></a>Propriedades de serviço ligadas

As seguintes propriedades são suportadas para o serviço ligado ao SAP BW Open Hub:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| `type` | A `type` propriedade deve ser definida para `SapTable` . | Sim |
| `server` | O nome do servidor no qual se encontra a instância SAP.<br/>Utilize para ligar a um servidor de aplicações SAP. | No |
| `systemNumber` | O número do sistema do sistema SAP.<br/>Utilize para ligar a um servidor de aplicações SAP.<br/>Valor permitido: Um número decimal de dois dígitos representado como uma corda. | No |
| `messageServer` | O nome de anfitrião do servidor de mensagens SAP.<br/>Utilize para ligar a um servidor de mensagens SAP. | No |
| `messageServerService` | O nome de serviço ou o número da porta do servidor de mensagens.<br/>Utilize para ligar a um servidor de mensagens SAP. | No |
| `systemId` | A identificação do sistema SAP onde se encontra a mesa.<br/>Utilize para ligar a um servidor de mensagens SAP. | No |
| `logonGroup` | O grupo de início de são para o sistema SAP.<br/>Utilize para ligar a um servidor de mensagens SAP. | No |
| `clientId` | A identificação do cliente no sistema SAP.<br/>Valor permitido: Um número decimal de três dígitos representado como uma corda. | Sim |
| `language` | A linguagem que o sistema SAP usa.<br/>O valor predefinido é `EN`.| No |
| `userName` | O nome do utilizador que tem acesso ao servidor SAP. | Sim |
| `password` | A palavra-passe do utilizador. Marque este campo com o `SecureString` tipo de armazená-lo de forma segura na Data Factory, ou [fazer referência a um segredo armazenado no Cofre da Chave Azure](store-credentials-in-key-vault.md). | Sim |
| `sncMode` | O indicador de ativação SNC para aceder ao servidor SAP onde a tabela está localizada.<br/>Utilize se quiser utilizar o SNC para ligar ao servidor SAP.<br/>Os valores permitidos são `0` (desligados, o padrão) ou `1` (ligado). | No |
| `sncMyName` | O nome SNC do iniciador para aceder ao servidor SAP onde a tabela está localizada.<br/>Aplica-se quando `sncMode` está ligado. | No |
| `sncPartnerName` | O nome SNC do parceiro de comunicação para aceder ao servidor SAP onde a tabela está localizada.<br/>Aplica-se quando `sncMode` está ligado. | No |
| `sncLibraryPath` | A biblioteca do produto de segurança externa para aceder ao servidor SAP onde a mesa está localizada.<br/>Aplica-se quando `sncMode` está ligado. | No |
| `sncQop` | O nível de qualidade de proteção do SNC a aplicar.<br/>Aplica-se quando `sncMode` está ligado. <br/>Os valores permitidos são `1` `2` (Autenticação), `3` (Integridade), (Privacidade), `8` (Predefinição), `9` (Máximo). | No |
| `connectVia` | O [tempo de integração](concepts-integration-runtime.md) a ser utilizado para ligar à loja de dados. É necessário um tempo de integração auto-organizado, como mencionado anteriormente em [Pré-requisitos](#prerequisites). |Sim |

**Exemplo 1: Ligar-se a um servidor de aplicações SAP**

```json
{
    "name": "SapTableLinkedService",
    "properties": {
        "type": "SapTable",
        "typeProperties": {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client ID>",
            "userName": "<SAP user>",
            "password": {
                "type": "SecureString",
                "value": "<Password for SAP user>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="example-2-connect-to-an-sap-message-server"></a>Exemplo 2: Ligar-se a um servidor de mensagens SAP

```json
{
    "name": "SapTableLinkedService",
    "properties": {
        "type": "SapTable",
        "typeProperties": {
            "messageServer": "<message server name>",
            "messageServerService": "<service name or port>",
            "systemId": "<system ID>",
            "logonGroup": "<logon group>",
            "clientId": "<client ID>",
            "userName": "<SAP user>",
            "password": {
                "type": "SecureString",
                "value": "<Password for SAP user>"
            }
        },
        "connectVia": {
            "referenceName": "<name of integration runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="example-3-connect-by-using-snc"></a>Exemplo 3: Conecte-se utilizando o SNC

```json
{
    "name": "SapTableLinkedService",
    "properties": {
        "type": "SapTable",
        "typeProperties": {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client ID>",
            "userName": "<SAP user>",
            "password": {
                "type": "SecureString",
                "value": "<Password for SAP user>"
            },
            "sncMode": 1,
            "sncMyName": "<SNC myname>",
            "sncPartnerName": "<SNC partner name>",
            "sncLibraryPath": "<SNC library path>",
            "sncQop": "8"
        },
        "connectVia": {
            "referenceName": "<name of integration runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Dataset properties (Propriedades do conjunto de dados)

Para obter uma lista completa das secções e propriedades para a definição de conjuntos de dados, consulte [Datasets](concepts-datasets-linked-services.md). A secção seguinte fornece uma lista das propriedades suportadas pelo conjunto de dados da tabela SAP.

Para copiar dados de e para o serviço ligado ao SAP BW Open Hub, suportam-se as seguintes propriedades:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| `type` | A `type` propriedade deve ser definida para `SapTableResource` . | Sim |
| `tableName` | O nome da tabela SAP para copiar dados de. | Sim |

### <a name="example"></a>Exemplo

```json
{
    "name": "SAPTableDataset",
    "properties": {
        "type": "SapTableResource",
        "typeProperties": {
            "tableName": "<SAP table name>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<SAP table linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy

Para obter uma lista completa das secções e propriedades para a definição de [atividades,](concepts-pipelines-activities.md)consulte Pipelines . A secção seguinte fornece uma lista dos imóveis suportados pela fonte de tabela SAP.

### <a name="sap-table-as-source"></a>Tabela SAP como fonte

Para copiar dados de uma tabela SAP, suportam-se as seguintes propriedades:

| Propriedade                         | Descrição                                                  | Obrigatório |
| :------------------------------- | :----------------------------------------------------------- | :------- |
| `type`                             | A `type` propriedade deve ser definida para `SapTableSource` .         | Sim      |
| `rowCount`                         | O número de filas a recuperar.                              | No       |
| `rfcTableFields`                 | Os campos (colunas) para copiar a partir da tabela SAP. Por exemplo, `column0, column1`. | No       |
| `rfcTableOptions`                | As opções para filtrar as linhas numa mesa SAP. Por exemplo, `COLUMN0 EQ 'SOMEVALUE'`. Consulte também a tabela do operador de consulta SAP mais tarde neste artigo. | No       |
| `customRfcReadTableFunctionModule` | Um módulo de função RFC personalizado que pode ser usado para ler dados a partir de uma tabela SAP.<br>Pode utilizar um módulo de função RFC personalizado para definir como os dados são recuperados do seu sistema SAP e devolvidos à Data Factory. O módulo de função personalizada deve ter uma interface implementada (importação, exportação, tabelas) semelhante a `/SAPDS/RFC_READ_TABLE2` , que é a interface padrão usada pela Data Factory.<br>Data Factory | No       |
| `partitionOption`                  | O mecanismo de partição para ler a partir de uma tabela SAP. As opções suportadas são as seguintes: <ul><li>`None`</li><li>`PartitionOnInt` (valores inteiros ou inteiros normais com estofamento zero à esquerda, tais `0000012345` como)</li><li>`PartitionOnCalendarYear` (4 dígitos no formato "YYYY")</li><li>`PartitionOnCalendarMonth` (6 dígitos no formato "YYYYMM")</li><li>`PartitionOnCalendarDate` (8 dígitos no formato "YYYYMMDD")</li></ul> | No       |
| `partitionColumnName`              | O nome da coluna usada para dividir os dados.                | No       |
| `partitionUpperBound`              | O valor máximo da coluna especificada na `partitionColumnName` qual será utilizado para continuar com a partição. | No       |
| `partitionLowerBound`              | O valor mínimo da coluna especificada na `partitionColumnName` qual será utilizado para continuar com a partição. (Nota: `partitionLowerBound` não pode ser "0" quando a opção de partição é `PartitionOnInt` ) | No       |
| `maxPartitionsNumber`              | O número máximo de divisórias para dividir os dados.     | No       |
| `sapDataColumnDelimiter` | O único personagem que é usado como delimiter passou para o SAP RFC para dividir os dados de saída. | No |

>[!TIP]
>Se a sua tabela SAP tiver um grande volume de dados, como vários biliões de linhas, use `partitionOption` e `partitionSetting` divida os dados em divisórias menores. Neste caso, os dados são lidos por partição, e cada partição de dados é recuperada do seu servidor SAP através de uma única chamada RFC.<br/>
<br/>
>Tomando `partitionOption` `partitionOnInt` como exemplo, o número de linhas em cada partição é calculado com esta fórmula: (linhas totais caindo entre `partitionUpperBound` e `partitionLowerBound` )/ `maxPartitionsNumber` .<br/>
<br/>
>Para carregar as divisórias de dados paralelamente à cópia acelerada, o grau paralelo é controlado pela [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) definição da atividade da cópia. Por exemplo, se definir `parallelCopies` para quatro, data factory simultaneamente gera e executa quatro consultas com base na sua opção e configurações de partição especificadas, e cada consulta recupera uma parte dos dados da sua tabela SAP. Recomendamos vivamente fazer `maxPartitionsNumber` um múltiplo do valor do `parallelCopies` imóvel. Ao copiar dados para a loja de dados baseada em ficheiros, também é recomuedado para escrever para uma pasta como vários ficheiros (apenas especificar o nome da pasta), caso em que o desempenho é melhor do que escrever para um único ficheiro.

Em `rfcTableOptions` , pode utilizar os seguintes operadores de consulta SAP comuns para filtrar as linhas:

| Operador | Descrição |
| :------- | :------- |
| `EQ` | Igual a |
| `NE` | Diferente de |
| `LT` | Menor do que |
| `LE` | Menor ou igual a |
| `GT` | Maior do que |
| `GE` | Maior ou igual a |
| `IN` | Como em `TABCLASS IN ('TRANSP', 'INTTAB')` |
| `LIKE` | Como em `LIKE 'Emma%'` |

### <a name="example"></a>Exemplo

```json
"activities":[
    {
        "name": "CopyFromSAPTable",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP table input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SapTableSource",
                "partitionOption": "PartitionOnInt",
                "partitionSettings": {
                     "partitionColumnName": "<partition column name>",
                     "partitionUpperBound": "2000",
                     "partitionLowerBound": "1",
                     "maxPartitionsNumber": 500
                 }
            },
            "sink": {
                "type": "<sink type>"
            },
            "parallelCopies": 4
        }
    }
]
```

## <a name="data-type-mappings-for-an-sap-table"></a>Mapeamentos de tipo de dados para uma tabela SAP

Quando está a copiar dados de uma tabela SAP, os seguintes mapeamentos são usados desde os tipos de dados da tabela SAP até aos tipos de dados provisórios da Azure Data Factory. Para saber como a atividade da cópia mapeia o esquema de origem e o tipo de dados para a pia, consulte [o Schema e os mapeamentos do tipo de dados](copy-activity-schema-and-type-mapping.md).

| Tipo SAP ABAP | Tipo de dados provisórios da Data Factory |
|:--- |:--- |
| `C` (Corda) | `String` |
| `I` (Inteiro) | `Int32` |
| `F` (Flutuar) | `Double` |
| `D` (Data) | `String` |
| `T` (Tempo) | `String` |
| `P` (BCD Embalado, Moeda, Decimal, Qty) | `Decimal` |
| `N` (Numérico) | `String` |
| `X` (Binário e Cru) | `String` |

## <a name="lookup-activity-properties"></a>Propriedades de atividade de procura

Para obter detalhes sobre as propriedades, consulte [a atividade de Lookup](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Próximas etapas

Para obter uma lista das lojas de dados suportadas como fontes e sumidouros pela atividade de cópias na Azure Data Factory, consulte [lojas de dados suportadas](copy-activity-overview.md#supported-data-stores-and-formats).
