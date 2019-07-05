---
title: Copiar dados de tabela de SAP com o Azure Data Factory | Documentos da Microsoft
description: Saiba como copiar dados de tabela do SAP para arquivos de dados de sink suportado através de uma atividade de cópia num pipeline do Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/26/2018
ms.author: jingwang
ms.openlocfilehash: e54a69b6c2b48e50c089f8b6b7458cf91133dd85
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67443310"
---
# <a name="copy-data-from-sap-table-using-azure-data-factory"></a>Copiar dados de tabela de SAP com o Azure Data Factory

Este artigo descreve como utilizar a atividade de cópia no Azure Data Factory para copiar dados a partir de uma tabela de SAP. Ele se baseia no [copiar descrição geral da atividade](copy-activity-overview.md) artigo apresenta uma visão geral da atividade de cópia.

## <a name="supported-capabilities"></a>Capacidades suportadas

Pode copiar dados da tabela de SAP para qualquer arquivo de dados de sink suportados. Para obter uma lista dos arquivos de dados que são suportados como origens/sinks a atividade de cópia, consulte a [arquivos de dados suportados](copy-activity-overview.md#supported-data-stores-and-formats) tabela.

Especificamente, este conector de SAP tabela suporta:

- Copiar dados de tabela de SAP em:

    - **SAP ECC** com a versão 7.01 ou superior (numa recente SAP suporte pacote pilha lançada após o ano de 2015)
    - **SAP BW** com a versão 7.01 ou superior
    - **SAP S/4HANA**
    - **Outros produtos SAP Business Suite** com a versão 7.01 ou superior 

- Copiar dados a partir de ambos **tabela de transparente de SAP** e **vista**.
- Copiar dados utilizando **autenticação básica** ou **SNC** (proteger as comunicações de rede) se SNC estiver configurado.
- Ligar ao **servidor de aplicativos** ou **servidor de mensagens**.

## <a name="prerequisites"></a>Pré-requisitos

Para utilizar este conector de SAP tabela, terá de:

- Configure um Runtime de integração autoalojado com a versão 3.17 ou superior. Ver [Integration Runtime autoalojado](create-self-hosted-integration-runtime.md) artigo para obter detalhes.

- Transfira o **64-bit [3.0 de conector do SAP .NET](https://support.sap.com/en/product/connectors/msnet.html)**  partir do site do SAP e instalá-lo na máquina do Runtime de integração autoalojado. Quando instalar, na janela de passos de configuração opcionais, certifique-se de que seleciona os **instalar Assemblies ao GAC** opção. 

    ![Instalar o conector do SAP .NET](./media/connector-sap-business-warehouse-open-hub/install-sap-dotnet-connector.png)

- Utilizador SAP que está a ser utilizado no conector de tabela de SAP da fábrica de dados tem de ter as seguintes permissões: 

    - Autorização de RFC. 
    - Permissões para a atividade "Executar" do objeto de autorização "S_SDSAUTH".

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As secções seguintes fornecem detalhes sobre as propriedades que são utilizadas para definir entidades do Data Factory específicas para o conector de SAP tabela.

## <a name="linked-service-properties"></a>Propriedades do serviço ligado

As seguintes propriedades são suportadas para o serviço de SAP Business Warehouse aberto Hub ligado:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | A propriedade de tipo tem de ser definida como: **SapTable** | Sim |
| server | Nome do servidor no qual reside a instância do SAP.<br/>Aplicável se deseja se conectar **servidor de aplicações SAP**. | Não |
| systemNumber | Número de sistema do sistema SAP.<br/>Aplicável se deseja se conectar **servidor de aplicações SAP**.<br/>Valor permitido: número decimal de dois dígitos representado como uma cadeia de caracteres. | Não |
| messageServer | O nome de anfitrião do servidor de mensagem SAP.<br/>Aplicável se deseja se conectar **SAP Message Server**. | Não |
| messageServerService | O nome de serviço ou a porta número do servidor de mensagens.<br/>Aplicável se deseja se conectar **SAP Message Server**. | Não |
| systemId | SystemID do sistema SAP onde está localizada a tabela.<br/>Aplicável se deseja se conectar **SAP Message Server**. | Não |
| logonGroup | O grupo de início de sessão para o sistema SAP.<br/>Aplicável se deseja se conectar **SAP Message Server**. | Não |
| clientId | ID de cliente do cliente no sistema SAP.<br/>Valor permitido: número decimal de três dígitos representado como uma cadeia de caracteres. | Sim |
| language | Idioma utilizado pelo sistema SAP. | Não (valor predefinido é **EN**)|
| userName | Nome de utilizador que tem acesso ao servidor SAP. | Sim |
| password | A palavra-passe do utilizador. Marcar esse campo como uma SecureString armazena de forma segura na fábrica de dados, ou [referenciar um segredo armazenado no Azure Key Vault](store-credentials-in-key-vault.md). | Sim |
| sncMode | Indicador de ativação de SNC para aceder ao servidor SAP onde está localizada a tabela.<br/>Aplicável se pretender utilizar SNC para ligar ao servidor SAP.<br/>Valores permitidos são: **0** (desativado, padrão) ou **1** (no). | Não |
| sncMyName | Nome SNC do iniciador para aceder ao servidor SAP onde está localizada a tabela.<br/>Quando aplicável `sncMode` está ativada. | Não |
| sncPartnerName | Nome SNC do parceiro de comunicação para aceder ao servidor SAP onde está localizada a tabela.<br/>Quando aplicável `sncMode` está ativada. | Não |
| sncLibraryPath | Biblioteca do produto de segurança externas para aceder ao servidor SAP onde está localizada a tabela.<br/>Quando aplicável `sncMode` está ativada. | Não |
| sncQop | SNC qualidade de proteção.<br/>Quando aplicável `sncMode` está ativada. <br/>Valores permitidos são: **1** (autenticação), **2** (integridade), **3** (privacidade), **8** (predefinida), **9** (máximo). | Não |
| connectVia | O [Integration Runtime](concepts-integration-runtime.md) a ser utilizado para ligar ao arquivo de dados. Um Runtime de integração autoalojado é necessário, conforme mencionado na [pré-requisitos](#prerequisites). |Sim |

**Exemplo 1: ligar ao servidor de aplicativo de SAP**

```json
{
    "name": "SapTableLinkedService",
    "properties": {
        "type": "SapTable",
        "typeProperties": {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client id>",
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

**Exemplo 2: ligar ao servidor de mensagem de SAP**

```json
{
    "name": "SapTableLinkedService",
    "properties": {
        "type": "SapTable",
        "typeProperties": {
            "messageServer": "<message server name>",
            "messageServerService": "<service name or port>",
            "systemId": "<system id>",
            "logonGroup": "<logon group>",
            "clientId": "<client id>",
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

**Exemplo 3: ligar utilizando SNC**

```json
{
    "name": "SapTableLinkedService",
    "properties": {
        "type": "SapTable",
        "typeProperties": {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client id>",
            "userName": "<SAP user>",
            "password": {
                "type": "SecureString",
                "value": "<Password for SAP user>"
            },
            "sncMode": 1,
            "sncMyName": "snc myname",
            "sncPartnerName": "snc partner name",
            "sncLibraryPath": "snc library path",
            "sncQop": "8"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Para obter uma lista completa das secções e propriedades disponíveis para definir conjuntos de dados, consulte a [conjuntos de dados](concepts-datasets-linked-services.md) artigo. Esta secção fornece uma lista das propriedades compatíveis com o conjunto de dados de tabela de SAP.

Para copiar dados de e para o Hub aberto do SAP BW, são suportadas as seguintes propriedades.

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | A propriedade de tipo deve ser definida como **SapTableResource**. | Sim |
| tableName | O nome da tabela para copiar dados de SAP. | Sim |

**Exemplo:**

```json
{
    "name": "SAPTableDataset",
    "properties": {
        "type": "SapTableResource",
        "linkedServiceName": {
            "referenceName": "<SAP Table linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "<SAP table name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy

Para obter uma lista completa das secções e propriedades disponíveis para a definição de atividades, consulte a [Pipelines](concepts-pipelines-activities.md) artigo. Esta seção fornece uma lista de propriedades suportadas pela origem de tabela de SAP.

### <a name="sap-table-as-source"></a>Tabela SAP como origem

Para copiar dados de tabela do SAP, são suportadas as seguintes propriedades.

| Propriedade                         | Descrição                                                  | Necessário |
| :------------------------------- | :----------------------------------------------------------- | :------- |
| type                             | A propriedade de tipo deve ser definida como **SapTableSource**.         | Sim      |
| rowCount                         | Número de linhas a serem obtidas.                              | Não       |
| rfcTableFields                   | Campos para copiar a partir da tabela SAP. Por exemplo, `column0, column1`. | Não       |
| rfcTableOptions                  | Opções para filtrar as linhas da tabela de SAP. Por exemplo, `COLUMN0 EQ 'SOMEVALUE'`. Veja uma descrição mais abaixo desta tabela. | Não       |
| customRfcReadTableFunctionModule | RFC função módulo personalizado que pode ser utilizado para ler dados da tabela de SAP.<br>Pode utilizar o módulo de função RFC personalizado para definir a forma como os dados são recuperados do seu sistema SAP e retornado ao ADF. Embora, tenha em atenção de que o módulo de função personalizada tem de ter semelhante interface implementada (importar, exportar, tabelas), semelhante a como/SAPDS/RFC_READ_TABLE2 que está por predefinição utilizada pelo ADF. | Não       |
| partitionOption                  | O mecanismo de partição para ler da tabela SAP. As opções suportadas incluem: <br/>- **Nenhum**<br/>- **PartitionOnInt** (inteiro normal ou valores de número inteiro com zero preenchimento à esquerda, como 0000012345)<br/>- **PartitionOnCalendarYear** (4 dígitos no formato "YYYY")<br/>- **PartitionOnCalendarMonth** (6 dígitos no formato "YYYYMM")<br/>- **PartitionOnCalendarDate** (8 dígitos no formato "AAAAMMDD") | Não       |
| partitionColumnName              | O nome da coluna para particionar os dados.                | Não       |
| partitionUpperBound              | O valor máximo da coluna especificada no `partitionColumnName` que será utilizado para criação de partições de continuar. | Não       |
| partitionLowerBound              | O valor mínimo da coluna especificada no `partitionColumnName` que será utilizado para criação de partições de continuar. | Não       |
| maxPartitionsNumber              | O número máximo de partições para dividir os dados.     | Não       |

>[!TIP]
>- Se a sua tabela SAP tem grande volume de dados, como vários milhares de milhões de linhas, utilize `partitionOption` e `partitionSetting` para dividir os dados em partições pequenas, caso em que os dados são lidos por partições e cada partição de dados é obtido a partir do seu servidor SAP através de um único Chamada RFC.<br/>
>- Levando `partitionOption` como `partitionOnInt` por exemplo, o número de linhas em cada partição é calculado ao (total de linhas entre *partitionUpperBound* e *partitionLowerBound*) /*maxPartitionsNumber*.<br/>
>- Se quiser executar ainda mais partições em paralelo para acelerar a cópia, é vivamente recomendado para tornar `maxPartitionsNumber` como um múltiplo do valor de `parallelCopies` (saber mais no [cópia paralela](copy-activity-performance.md#parallel-copy)).

No `rfcTableOptions`, pode usar por exemplo, os seguintes comuns SAP operadores de consulta para filtrar as linhas: 

| Operador | Descrição |
| :------- | :------- |
| EQ | Igual a |
| NE | Não é igual a |
| LT | Menor que |
| LE | Menor que ou igual a |
| GT | Maior que |
| GE | Maior que ou igual a |
| COMO | Como em como "Emma %" |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromSAPTable",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP Table input dataset name>",
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

## <a name="data-type-mapping-for-sap-table"></a>Mapeamento de tipo de dados para a tabela de SAP

Ao copiar dados da tabela de SAP, os seguintes mapeamentos são utilizados entre tipos de dados de tabela do SAP para tipos de dados intermediárias do Azure Data Factory. Ver [mapeamentos de tipo de esquema e dados](copy-activity-schema-and-type-mapping.md) para saber mais sobre como atividade de cópia mapeia o tipo de esquema e os dados de origem para o sink.

| Tipo ABAP SAP | Tipo de dados intermediárias de fábrica de dados |
|:--- |:--- |
| C (cadeia) | String |
| Eu (número inteiro) | Int32 |
| F (número de vírgula flutuante) | Double |
| 1!d (data) | String |
| T (hora) | String |
| P (BCD incluídos, moeda, Decimal,. de defeitos) | Decimal |
| N (numérico) | String |
| X (binário e não processados) | String |

## <a name="next-steps"></a>Passos Seguintes
Para obter uma lista dos arquivos de dados suportados como origens e sinks, a atividade de cópia no Azure Data Factory, veja [arquivos de dados suportados](copy-activity-overview.md#supported-data-stores-and-formats).
