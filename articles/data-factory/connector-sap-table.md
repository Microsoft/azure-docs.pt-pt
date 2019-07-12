---
title: Copiar dados a partir de uma tabela SAP através do Azure Data Factory | Documentos da Microsoft
description: Saiba como copiar dados de uma tabela do SAP para arquivos de dados de sink suportado com uma atividade de cópia num pipeline do Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 07/09/2018
ms.author: jingwang
ms.openlocfilehash: 9216f5c00cbdac273b562736abdd1c812d172237
ms.sourcegitcommit: 441e59b8657a1eb1538c848b9b78c2e9e1b6cfd5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67827761"
---
# <a name="copy-data-from-an-sap-table-by-using-azure-data-factory"></a>Copiar dados a partir de uma tabela SAP através do Azure Data Factory

Este artigo descreve como utilizar a atividade de cópia no Azure Data Factory para copiar dados a partir de uma tabela SAP. Para obter mais informações, consulte [copiar descrição geral da atividade](copy-activity-overview.md).

## <a name="supported-capabilities"></a>Capacidades suportadas

Pode copiar dados a partir de uma tabela SAP para qualquer arquivo de dados de sink suportados. Para obter uma lista de arquivos de dados que são suportados como origens ou sinks a atividade de cópia, consulte a [arquivos de dados suportados](copy-activity-overview.md#supported-data-stores-and-formats) tabela.

Especificamente, este conector do SAP tabela suporta:

- Copiar dados a partir de uma tabela SAP em:

  - SAP ERP a componente Central (SAP ECC) versão 7.01 ou posterior (numa recente SAP suporte pacote pilha lançada após 2015).
  - SAP Business Warehouse (SAP BW) versão 7.01 ou posterior.
  - SAP S/4HANA.
  - Outros produtos SAP Business Suite versão 7.01 ou posterior.

- Copiar dados a partir de uma tabela de transparente do SAP, uma tabela em pool, uma tabela em cluster e uma visualização.
- Copiar dados utilizando a autenticação básica ou proteger a rede comunicações (SNC), se SNC estiver configurado.
- A ligar a um servidor de aplicações SAP ou o servidor de mensagens SAP.

## <a name="prerequisites"></a>Pré-requisitos

Para utilizar este conector de tabela do SAP, tem de:

- Configure um runtime de integração autoalojado (versão 3.17 ou posterior). Para obter mais informações, consulte [criar e configurar um runtime de integração autoalojado](create-self-hosted-integration-runtime.md).

- Download de 64 bits [conector do SAP para o Microsoft .NET 3.0](https://support.sap.com/en/product/connectors/msnet.html) partir do site do SAP e instalá-lo no computador runtime de integração autoalojado. Durante a instalação, certifique-se de que seleciona os **instalar Assemblies ao GAC** opção a **passos de configuração opcionais** janela.

  ![Instale o conector do SAP para o .NET](./media/connector-sap-business-warehouse-open-hub/install-sap-dotnet-connector.png)

- O utilizador SAP que está a ser utilizado no conector de tabela do SAP de fábrica de dados tem de ter as seguintes permissões:

  - Autorização para a utilização de destinos de chamada de função remota (RFC).
  - Permissões para a atividade de execução do objeto de autorização S_SDSAUTH.

## <a name="get-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As secções seguintes fornecem detalhes sobre as propriedades que são utilizadas para definir as entidades da fábrica de dados específicas para o conector de tabela do SAP.

## <a name="linked-service-properties"></a>Propriedades do serviço ligado

As seguintes propriedades são suportadas para o serviço de SAP BW aberto Hub ligado:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| `type` | O `type` propriedade deve ser definida `SapTable`. | Sim |
| `server` | O nome do servidor onde está localizada a instância do SAP.<br/>Utilize para ligar a um servidor de aplicações SAP. | Não |
| `systemNumber` | O número de sistema do sistema SAP.<br/>Utilize para ligar a um servidor de aplicações SAP.<br/>Valor permitido: Número decimal de dois dígitos representado como uma cadeia de caracteres. | Não |
| `messageServer` | O nome de anfitrião do servidor de mensagem SAP.<br/>Utilize para ligar a um servidor de mensagem SAP. | Não |
| `messageServerService` | O nome de serviço ou a porta número do servidor de mensagens.<br/>Utilize para ligar a um servidor de mensagem SAP. | Não |
| `systemId` | O ID do sistema SAP onde está localizada a tabela.<br/>Utilize para ligar a um servidor de mensagem SAP. | Não |
| `logonGroup` | O grupo de início de sessão para o sistema SAP.<br/>Utilize para ligar a um servidor de mensagem SAP. | Não |
| `clientId` | O ID do cliente no sistema SAP.<br/>Valor permitido: Número decimal de três dígitos representado como uma cadeia de caracteres. | Sim |
| `language` | O idioma utilizado pelo sistema SAP.<br/>Valor predefinido é `EN`.| Não |
| `userName` | O nome de utilizador que tem acesso ao servidor SAP. | Sim |
| `password` | A palavra-passe do utilizador. Marcar este campo com o `SecureString` tipo armazena de forma segura na fábrica de dados, ou [referenciar um segredo armazenado no Azure Key Vault](store-credentials-in-key-vault.md). | Sim |
| `sncMode` | O indicador de ativação de SNC para aceder ao servidor SAP onde está localizada a tabela.<br/>Utilize se pretender utilizar SNC para ligar ao servidor SAP.<br/>Valores permitidos são `0` (desativado, a predefinição) ou `1` (no). | Não |
| `sncMyName` | Nome SNC o iniciador para aceder ao servidor SAP onde está localizada a tabela.<br/>Aplica-se quando `sncMode` está ativada. | Não |
| `sncPartnerName` | No comunicação nome do parceiro SNC para aceder ao servidor SAP onde está localizada a tabela.<br/>Aplica-se quando `sncMode` está ativada. | Não |
| `sncLibraryPath` | Biblioteca do produto de segurança externas para aceder ao servidor SAP onde está localizada a tabela.<br/>Aplica-se quando `sncMode` está ativada. | Não |
| `sncQop` | O nível de SNC qualidade de proteção a aplicar.<br/>Aplica-se quando `sncMode` está ativada. <br/>Valores permitidos são `1` (autenticação), `2` (integridade), `3` (privacidade), `8` (predefinida), `9` (máximo). | Não |
| `connectVia` | O [runtime de integração](concepts-integration-runtime.md) a ser utilizado para ligar ao arquivo de dados. Um runtime de integração autoalojado é necessário, conforme mencionado anteriormente na [pré-requisitos](#prerequisites). |Sim |

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

### <a name="example-2-connect-to-an-sap-message-server"></a>Exemplo 2: Ligar a um servidor de mensagem do SAP

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

### <a name="example-3-connect-by-using-snc"></a>Exemplo 3: Ligar com SNC

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

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Para obter uma lista completa das secções e propriedades para definir conjuntos de dados, consulte [conjuntos de dados](concepts-datasets-linked-services.md). A secção seguinte fornece uma lista de propriedades suportadas pelo conjunto de dados de tabela da SAP.

Para copiar dados de e para o serviço de SAP BW aberto Hub ligado, são suportadas as seguintes propriedades:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| `type` | O `type` propriedade deve ser definida `SapTableResource`. | Sim |
| `tableName` | O nome da tabela para copiar dados de SAP. | Sim |

### <a name="example"></a>Exemplo

```json
{
    "name": "SAPTableDataset",
    "properties": {
        "type": "SapTableResource",
        "linkedServiceName": {
            "referenceName": "<SAP table linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "<SAP table name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy

Para obter uma lista completa das secções e propriedades para a definição de atividades, consulte [Pipelines](concepts-pipelines-activities.md). A secção seguinte fornece uma lista de propriedades suportado pela origem de tabela de SAP.

### <a name="sap-table-as-a-source"></a>Tabela SAP como uma origem

Para copiar dados de uma tabela do SAP, são suportadas as seguintes propriedades:

| Propriedade                         | Descrição                                                  | Necessário |
| :------------------------------- | :----------------------------------------------------------- | :------- |
| `type`                             | O `type` propriedade deve ser definida `SapTableSource`.         | Sim      |
| `rowCount`                         | O número de linhas a serem obtidas.                              | Não       |
| `rfcTableFields`                   | Os campos (colunas) para copiar a partir da tabela SAP. Por exemplo, `column0, column1`. | Não       |
| `rfcTableOptions`                  | As opções para filtrar as linhas numa tabela do SAP. Por exemplo, `COLUMN0 EQ 'SOMEVALUE'`. Consulte também a tabela de operador de consulta SAP neste artigo. | Não       |
| `customRfcReadTableFunctionModule` | Um RFC função módulo personalizado que pode ser utilizado para ler dados a partir de uma tabela SAP.<br>Pode utilizar um módulo de função RFC personalizado para definir como os dados são obtidos a partir do seu sistema SAP e devolvidos à fábrica de dados. O módulo de função personalizada tem de ter uma interface implementada (importar, exportar, tabelas) que é semelhante ao `/SAPDS/RFC_READ_TABLE2`, que é a interface utilizada pela fábrica de dados. | Não       |
| `partitionOption`                  | O mecanismo de partição para ler a partir de uma tabela SAP. As opções suportadas incluem: <ul><li>`None`</li><li>`PartitionOnInt` (inteiro normal ou valores de número inteiro com zero preenchimento à esquerda, como `0000012345`)</li><li>`PartitionOnCalendarYear` (4 dígitos no formato "YYYY")</li><li>`PartitionOnCalendarMonth` (6 dígitos no formato "YYYYMM")</li><li>`PartitionOnCalendarDate` (8 dígitos no formato "AAAAMMDD")</li></ul> | Não       |
| `partitionColumnName`              | O nome da coluna utilizado para particionar os dados.                | Não       |
| `partitionUpperBound`              | O valor máximo da coluna especificada no `partitionColumnName` que será utilizado para continuar com a criação de partições. | Não       |
| `partitionLowerBound`              | O valor mínimo da coluna especificada no `partitionColumnName` que será utilizado para continuar com a criação de partições. | Não       |
| `maxPartitionsNumber`              | O número máximo de partições para dividir os dados.     | Não       |

>[!TIP]
>Se a sua tabela SAP tem um grande volume de dados, como várias linhas de mil milhões, utilize `partitionOption` e `partitionSetting` dividir os dados em partições mais pequenas. Neste caso, os dados são lidos por partição, e cada partição de dados é obtida a partir do seu servidor SAP através de uma única chamada RFC.<br/>
<br/>
>Levando `partitionOption` como `partitionOnInt` por exemplo, o número de linhas em cada partição é calculado com esta fórmula: (total de linhas entre `partitionUpperBound` e `partitionLowerBound`) /`maxPartitionsNumber`.<br/>
<br/>
>Para executar as partições em paralelo para acelerar a copiar, recomendamos vivamente a tomada `maxPartitionsNumber` um múltiplo do valor do `parallelCopies` propriedade. Para obter mais informações, consulte [cópia em paralelo](copy-activity-performance.md#parallel-copy).

No `rfcTableOptions`, pode utilizar os seguintes operadores de consulta SAP comuns para filtrar as linhas:

| Operador | Descrição |
| :------- | :------- |
| `EQ` | Igual a |
| `NE` | Não é igual a |
| `LT` | Menor que |
| `LE` | Menor que ou igual a |
| `GT` | Maior que |
| `GE` | Maior que ou igual a |
| `LIKE` | Como mostrado na `LIKE 'Emma%'` |

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
            }
        }
    }
]
```

## <a name="data-type-mappings-for-an-sap-table"></a>Mapeamentos de tipo de dados para uma tabela SAP

Quando estiver a copiar dados de uma tabela do SAP, os seguintes mapeamentos são utilizados entre os tipos de dados de tabela do SAP para os tipos de dados intermediárias do Azure Data Factory. Para saber como a atividade de cópia mapeia o tipo de esquema e os dados de origem para o sink, veja [mapeamentos de tipo de esquema e dados](copy-activity-schema-and-type-mapping.md).

| Tipo ABAP SAP | Tipo de dados intermediárias de fábrica de dados |
|:--- |:--- |
| `C` (String) | `String` |
| `I` (Número inteiro) | `Int32` |
| `F` (Número de vírgula flutuante) | `Double` |
| `D` (Data) | `String` |
| `T` (Hora) | `String` |
| `P` (BCD incluídos, moeda, Decimal,. de defeitos) | `Decimal` |
| `N` (Numérico) | `String` |
| `X` (Binário e não processados) | `String` |

## <a name="next-steps"></a>Passos Seguintes

Para obter uma lista de arquivos de dados suportados como origens e sinks, a atividade de cópia no Azure Data Factory, veja [arquivos de dados suportados](copy-activity-overview.md#supported-data-stores-and-formats).
