---
title: Copiar dados de uma tabela SAP
description: Saiba como copiar dados de uma tabela SAP para lojas de dados de sink suportadas utilizando uma atividade de cópia num pipeline azure Data Factory.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/02/2019
ms.openlocfilehash: 65e51258b3a24b14b5171968645e88420e92dd5a
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2020
ms.locfileid: "80421064"
---
# <a name="copy-data-from-an-sap-table-by-using-azure-data-factory"></a>Copiar dados de uma tabela SAP utilizando a Azure Data Factory

Este artigo descreve como utilizar a atividade de cópia na Azure Data Factory para copiar dados de uma tabela SAP. Para mais informações, consulte a [visão geral da atividade da Cópia](copy-activity-overview.md).

>[!TIP]
>Para conhecer o suporte global da ADF no cenário de integração de dados DoSAP, consulte a integração de [dados do SAP utilizando o livro branco da Azure Data Factory](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf) com introdução detalhada, comparação e orientação.

## <a name="supported-capabilities"></a>Capacidades suportadas

Este conector de mesa SAP é suportado para as seguintes atividades:

- [Copiar atividade](copy-activity-overview.md) com matriz de [origem/pia suportada](copy-activity-overview.md)
- [Atividade de procura](control-flow-lookup-activity.md)

Pode copiar dados de uma tabela SAP para qualquer loja de dados de sink suportado. Para obter uma lista das lojas de dados que são suportadas como fontes ou afunda-se pela atividade de cópia, consulte a tabela de lojas de [dados suportadas.](copy-activity-overview.md#supported-data-stores-and-formats)

Especificamente, este conector de mesa SAP suporta:

- Copiar dados de uma tabela SAP em:

  - SAP ERP Central Component (SAP ECC) versão 7.01 ou posterior (num recente Pacote de Suporte SAP Stack lançado após 2015).
  - SAP Business Warehouse (SAP BW) versão 7.01 ou posterior (num recente Pacote de Suporte SAP Stack lançado após 2015).
  - SAP S/4HANA.
  - Outros produtos na versão 7.01 ou posterior do SAP Business Suite (num recente Pacote de Suporte SAP Stack lançado após 2015).

- Copiar dados de uma tabela transparente SAP, uma mesa reunida, uma mesa agrupada e uma vista.
- Copiar dados utilizando a autenticação básica ou as Comunicações de Rede Segura (SNC), se o SNC estiver configurado.
- Conectando-se a um servidor de aplicação SAP ou servidor de mensagens SAP.

## <a name="prerequisites"></a>Pré-requisitos

Para utilizar este conector de mesa SAP, é necessário:

- Criar um tempo de execução de integração auto-hospedado (versão 3.17 ou posterior). Para mais informações, consulte [Criar e configurar um tempo de execução de integração auto-hospedado.](create-self-hosted-integration-runtime.md)

- Descarregue o [Conector SAP](https://support.sap.com/en/product/connectors/msnet.html) de 64 bits para microsoft .NET 3.0 a partir do site da SAP e instale-o na máquina de tempo de funcionação de integração auto-hospedada. Durante a instalação, certifique-se de que seleciona as **Assembléias** de Instalação para a opção GAC na janela de passos de **configuração opcional.**

  ![Instale o Conector SAP para .NET](./media/connector-sap-business-warehouse-open-hub/install-sap-dotnet-connector.png)

- O utilizador SAP que está a ser utilizado no conector de mesa SAP da Fábrica de Dados deve ter as seguintes permissões:

  - Autorização para utilizar destinos de Chamada de Função Remota (RFC).
  - Permissões à atividade executar do objeto de autorização S_SDSAUTH.

## <a name="get-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seguintes secções fornecem detalhes sobre propriedades que são usadas para definir as entidades data Factory específicas do conector de tabela SAP.

## <a name="linked-service-properties"></a>Propriedades de serviço seletos

As seguintes propriedades são suportadas para o serviço ligado ao SAP BW Open Hub:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| `type` | A `type` propriedade deve `SapTable`ser definida para . | Sim |
| `server` | O nome do servidor no qual se encontra a instância SAP.<br/>Utilize para ligar a um servidor de aplicações SAP. | Não |
| `systemNumber` | O número do sistema do sistema SAP.<br/>Utilize para ligar a um servidor de aplicações SAP.<br/>Valor permitido: Um número decimal de dois dígitos representado como uma corda. | Não |
| `messageServer` | O nome anfitrião do servidor de mensagens SAP.<br/>Utilize para ligar a um servidor de mensagens SAP. | Não |
| `messageServerService` | O nome de serviço ou o número de porta do servidor de mensagens.<br/>Utilize para ligar a um servidor de mensagens SAP. | Não |
| `systemId` | A identificação do sistema SAP onde a mesa está localizada.<br/>Utilize para ligar a um servidor de mensagens SAP. | Não |
| `logonGroup` | O grupo de logon para o sistema SAP.<br/>Utilize para ligar a um servidor de mensagens SAP. | Não |
| `clientId` | A identificação do cliente no sistema SAP.<br/>Valor permitido: Um número decimal de três dígitos representado como uma corda. | Sim |
| `language` | A linguagem que o sistema SAP usa.<br/>O valor `EN`predefinido é .| Não |
| `userName` | O nome do utilizador que tem acesso ao servidor SAP. | Sim |
| `password` | A palavra-passe do utilizador. Marque este `SecureString` campo com o tipo de armazená-lo de forma segura na Data Factory, ou [refira um segredo armazenado no Cofre de Chaves Azure](store-credentials-in-key-vault.md). | Sim |
| `sncMode` | O indicador de ativação SNC para aceder ao servidor SAP onde a tabela está localizada.<br/>Utilize se pretender utilizar o SNC para se ligar ao servidor SAP.<br/>Os valores permitidos são `0` `1` (desligados, o padrão) ou (on). | Não |
| `sncMyName` | O nome SNC do iniciador para aceder ao servidor SAP onde a mesa está localizada.<br/>Aplica-se `sncMode` quando está ligado. | Não |
| `sncPartnerName` | O nome SNC do parceiro de comunicação para aceder ao servidor SAP onde a tabela está localizada.<br/>Aplica-se `sncMode` quando está ligado. | Não |
| `sncLibraryPath` | A biblioteca do produto de segurança externa para aceder ao servidor SAP onde a mesa está localizada.<br/>Aplica-se `sncMode` quando está ligado. | Não |
| `sncQop` | O nível de qualidade de proteção do SNC a aplicar.<br/>Aplica-se `sncMode` quando está ligado. <br/>Os valores permitidos são `1` (Autenticação), `2` (Integridade), `3` (Privacidade), `8` (Padrão), `9` (Máximo). | Não |
| `connectVia` | O tempo de [integração](concepts-integration-runtime.md) a ser utilizado para se ligar à loja de dados. É necessário um tempo de execução de integração auto-hospedado, como mencionado anteriormente nos [pré-requisitos.](#prerequisites) |Sim |

**Exemplo 1: Ligar a um servidor de aplicações SAP**

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

### <a name="example-2-connect-to-an-sap-message-server"></a>Exemplo 2: Ligar a um servidor de mensagens SAP

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

### <a name="example-3-connect-by-using-snc"></a>Exemplo 3: Ligar utilizando o SNC

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

Para obter uma lista completa das secções e propriedades para definir conjuntos de dados, consulte [Conjuntos](concepts-datasets-linked-services.md)de Dados . A secção seguinte fornece uma lista das propriedades suportadas pelo conjunto de dados da tabela SAP.

Para copiar dados de e para o serviço ligado ao SAP BW Open Hub, são suportadas as seguintes propriedades:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| `type` | A `type` propriedade deve `SapTableResource`ser definida para . | Sim |
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

Para obter uma lista completa das secções e propriedades para definir atividades, consulte [Pipelines](concepts-pipelines-activities.md). A secção seguinte fornece uma lista das propriedades suportadas pela fonte da tabela SAP.

### <a name="sap-table-as-source"></a>Tabela SAP como fonte

Para copiar dados de uma tabela SAP, são suportadas as seguintes propriedades:

| Propriedade                         | Descrição                                                  | Necessário |
| :------------------------------- | :----------------------------------------------------------- | :------- |
| `type`                             | A `type` propriedade deve `SapTableSource`ser definida para .         | Sim      |
| `rowCount`                         | O número de filas a recuperar.                              | Não       |
| `rfcTableFields`                   | Os campos (colunas) para copiar a partir da tabela SAP. Por exemplo, `column0, column1`. | Não       |
| `rfcTableOptions`                  | As opções para filtrar as linhas numa tabela SAP. Por exemplo, `COLUMN0 EQ 'SOMEVALUE'`. Consulte também a tabela do operador de consulta SAP mais tarde neste artigo. | Não       |
| `customRfcReadTableFunctionModule` | Um módulo de função RFC personalizado que pode ser usado para ler dados a partir de uma tabela SAP.<br>Pode utilizar um módulo de função RFC personalizado para definir como os dados são recuperados do seu sistema SAP e devolvidos à Data Factory. O módulo de função personalizada deve ter uma interface implementada (importação, exportação, tabelas) semelhante a `/SAPDS/RFC_READ_TABLE2`, que é a interface predefinida utilizada pela Data Factory. | Não       |
| `partitionOption`                  | O mecanismo de partição para ler a partir de uma mesa SAP. As opções suportadas são as seguintes: <ul><li>`None`</li><li>`PartitionOnInt`(valores inteiros ou inteiros normais com preenchimento `0000012345`zero à esquerda, tais como)</li><li>`PartitionOnCalendarYear`(4 dígitos no formato "YYYY")</li><li>`PartitionOnCalendarMonth`(6 dígitos no formato "YYYYMM")</li><li>`PartitionOnCalendarDate`(8 dígitos no formato "YYYYMMDD")</li></ul> | Não       |
| `partitionColumnName`              | O nome da coluna usada para dividir os dados.                | Não       |
| `partitionUpperBound`              | O valor máximo da coluna `partitionColumnName` especificada na mesma será utilizada para continuar com a divisão. | Não       |
| `partitionLowerBound`              | O valor mínimo da coluna `partitionColumnName` especificada na medida em que será utilizado para continuar com a divisão. (Nota: `partitionLowerBound` não pode ser "0" quando a opção de partição é `PartitionOnInt`) | Não       |
| `maxPartitionsNumber`              | O número máximo de divisórias para dividir os dados.     | Não       |

>[!TIP]
>Se a sua tabela SAP tiver um grande volume de `partitionOption` `partitionSetting` dados, como vários biliões de linhas, use e divida os dados em divisórias menores. Neste caso, os dados são lidos por partição, e cada partição de dados é recuperada do seu servidor SAP através de uma única chamada RFC.<br/>
<br/>
>Tomando `partitionOption` `partitionOnInt` como exemplo, o número de linhas em cada partição é `partitionUpperBound` `partitionLowerBound`calculado`maxPartitionsNumber`com esta fórmula: (linhas totais que caem entre e )/ .<br/>
<br/>
>Para carregar divisórias de dados paralelas à cópia [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) de aceleração, o grau paralelo é controlado pela regulação da atividade da cópia. Por exemplo, se `parallelCopies` definir para quatro, data Factory gera simultaneamente e executa quatro consultas com base na sua opção e configurações especificadas de partição, e cada consulta recupera uma parte dos dados da sua tabela SAP. Recomendamos vivamente `maxPartitionsNumber` fazer um múltiplo do valor da `parallelCopies` propriedade. Ao copiar dados para uma loja de dados baseada em ficheiros, também é re-ordenado para escrever para uma pasta como múltiplos ficheiros (apenas especificar o nome da pasta), caso em que o desempenho é melhor do que escrever para um único ficheiro.

Em, `rfcTableOptions`pode utilizar os seguintes operadores comuns de consulta SAP para filtrar as linhas:

| Operador | Descrição |
| :------- | :------- |
| `EQ` | Igual a |
| `NE` | Diferente de |
| `LT` | Menor do que |
| `LE` | Menor ou igual a |
| `GT` | Maior que |
| `GE` | Maior que ou igual a |
| `LIKE` | Como em`LIKE 'Emma%'` |

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

Quando está a copiar dados de uma tabela SAP, os seguintes mapeamentos são utilizados desde os tipos de dados da tabela SAP para os tipos de dados provisórios da Azure Data Factory. Para saber como a atividade da cópia mapeia o esquema de origem e o tipo de dados para a pia, consulte [schema e mapeamentos de tipo](copy-activity-schema-and-type-mapping.md)de dados .

| Tipo SAP ABAP | Data Factory tipo de dados provisórios |
|:--- |:--- |
| `C`(Corda) | `String` |
| `I`(Inteiro) | `Int32` |
| `F`(Flutuar) | `Double` |
| `D`(Data) | `String` |
| `T`(Tempo) | `String` |
| `P`(BCD Embalado, Moeda, Decimal, Qty) | `Decimal` |
| `N`(Numérico) | `String` |
| `X`(Binário e Cru) | `String` |

## <a name="lookup-activity-properties"></a>Propriedades de atividade de procura

Para saber mais detalhes sobre as propriedades, consulte a [atividade de Lookup.](control-flow-lookup-activity.md)


## <a name="next-steps"></a>Passos seguintes

Para obter uma lista das lojas de dados suportadas como fontes e pias pela atividade de cópia na Azure Data Factory, consulte as lojas de [dados suportadas](copy-activity-overview.md#supported-data-stores-and-formats).
