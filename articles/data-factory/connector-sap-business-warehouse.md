---
title: Copiar dados da SAP BW
description: Saiba como copiar dados do SAP Business Warehouse para armazenar dados de sumidouros suportados utilizando uma atividade de cópia num oleoduto Azure Data Factory.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/04/2019
ms.openlocfilehash: 4a5eb7c8d729fc0c3b95a61a4b8dbc8266d1eec3
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100386667"
---
# <a name="copy-data-from-sap-business-warehouse-using-azure-data-factory"></a>Copiar dados do SAP Business Warehouse utilizando a Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que está a utilizar:"]
> * [Versão 1](v1/data-factory-sap-business-warehouse-connector.md)
> * [Versão atual](connector-sap-business-warehouse.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve como utilizar a Atividade de Cópia na Fábrica de Dados Azure para copiar dados de um Sap Business Warehouse (BW). Baseia-se no artigo [de visão geral](copy-activity-overview.md) da atividade de cópia que apresenta uma visão geral da atividade da cópia.

>[!TIP]
>Para obter o apoio global da ADF no cenário de integração de dados SAP, consulte [a integração de dados SAP utilizando o papel branco da Azure Data Factory](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf) com introdução detalhada em cada conector SAP, comparação e orientação.

## <a name="supported-capabilities"></a>Capacidades suportadas

Este conector SAP Business Warehouse é suportado para as seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com [matriz de fonte/pia suportada](copy-activity-overview.md)
- [Atividade de procura](control-flow-lookup-activity.md)

Pode copiar dados da SAP Business Warehouse para qualquer loja de dados de sumidouro suportado. Para obter uma lista de lojas de dados suportadas como fontes/pias pela atividade de cópia, consulte a tabela [de lojas de dados suportadas.](copy-activity-overview.md#supported-data-stores-and-formats)

Especificamente, este conector SAP Business Warehouse suporta:

- VERSÃO SAP Business Warehouse **7.x**.
- Copiar dados de **InfoCubes e Consultascubes** (incluindo consultas BEx) usando consultas MDX.
- Copiar dados utilizando a autenticação básica.

## <a name="prerequisites"></a>Pré-requisitos

Para utilizar este conector SAP Business Warehouse, é necessário:

- Crie um tempo de integração auto-hospedado. Consulte o artigo [de execução de integração auto-hospedado](create-self-hosted-integration-runtime.md) para obter detalhes.
- Instale a **biblioteca SAP NetWeaver** na máquina de tempo de execução de integração. Pode obter a biblioteca SAP Netweaver do seu administrador SAP ou diretamente do [Sap Software Download Center](https://support.sap.com/swdc). Procure o **#1025361 SAP Note** para obter o local de descarregamento para a versão mais recente. Certifique-se de que escolhe a biblioteca SAP NetWeaver **de 64 bits** que corresponde à sua instalação de tempo de execução de integração. Em seguida, instale todos os ficheiros incluídos no SAP NetWeaver RFC SDK de acordo com a Nota SAP. A biblioteca SAP NetWeaver também está incluída na instalação SAP Client Tools.

>[!TIP]
>Para resolver problemas de conectividade com a SAP BW, certifique-se de que:
>- Todas as bibliotecas de dependência extraídas do NetWeaver RFC SDK estão em vigor na pasta %windir%\system32. Normalmente tem icudt34.dll, icuin34.dll, icuuc34.dll, libicudecnumber.dll, librfc32.dll, libsapucum.dll, sapcrypto.dll, sapcryto_old.dll, sapnwrfc.dll.
>- As portas necessárias utilizadas para ligar ao SERVIDOR SAP estão ativadas na máquina de INFRAVERMELHOs auto-hospedada, que normalmente são as portas 3300 e 3201.

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seguintes secções fornecem detalhes sobre propriedades que são usadas para definir entidades da Data Factory específicas do conector SAP Business Warehouse.

## <a name="linked-service-properties"></a>Propriedades de serviço ligadas

As seguintes propriedades são suportadas para o serviço ligado ao SAP Business Warehouse (BW):

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade tipo deve ser definida para: **SapBw** | Yes |
| servidor | Nome do servidor em que reside a instância SAP BW. | Yes |
| sistemaNumbre | Número do sistema do sistema SAP BW.<br/>Valor permitido: número decimal de dois dígitos representado como uma corda. | Yes |
| clientId | Identificação do cliente do cliente no sistema SAP W.<br/>Valor permitido: número decimal de três dígitos representado como uma corda. | Yes |
| userName | Nome do utilizador que tem acesso ao servidor SAP. | Yes |
| palavra-passe | A palavra-passe do utilizador. Marque este campo como um SecureString para armazená-lo de forma segura na Data Factory, ou [fazer referência a um segredo armazenado no Cofre da Chave Azure](store-credentials-in-key-vault.md). | Yes |
| connectVia | O [tempo de execução de integração](concepts-integration-runtime.md) a ser utilizado para ligar à loja de dados. É necessário um tempo de integração auto-organizado, tal como mencionado nos [Pré-Requisitos](#prerequisites). |Yes |

**Exemplo:**

```json
{
    "name": "SapBwLinkedService",
    "properties": {
        "type": "SapBw",
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

## <a name="dataset-properties"></a>Dataset properties (Propriedades do conjunto de dados)

Para obter uma lista completa de secções e propriedades disponíveis para definir conjuntos de dados, consulte o artigo [conjuntos de dados.](concepts-datasets-linked-services.md) Esta secção fornece uma lista de propriedades suportadas pelo conjunto de dados SAP BW.

Para copiar dados da SAP BW, defina a propriedade tipo do conjunto de dados para **SapBwCube**. Embora não existam propriedades específicas do tipo suportadas para o conjunto de dados SAP BW do tipo RelationalTable.

**Exemplo:**

```json
{
    "name": "SAPBWDataset",
    "properties": {
        "type": "SapBwCube",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<SAP BW linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

Se estiver a utilizar `RelationalTable` conjunto de dados dactilografado, ainda é suportado como está, enquanto é sugerido que utilize o novo para a frente.

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy

Para obter uma lista completa de secções e propriedades disponíveis para definir atividades, consulte o artigo [Pipelines.](concepts-pipelines-activities.md) Esta secção fornece uma lista de propriedades suportadas por fonte SAP BW.

### <a name="sap-bw-as-source"></a>SAP BW como fonte

Para copiar dados da SAP BW, as seguintes propriedades são suportadas na secção **fonte de origem** da atividade da cópia:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade tipo da fonte de atividade de cópia deve ser definida para: **SapBwSource** | Yes |
| consulta | Especifica a consulta MDX para ler dados a partir da instância SAP BW. | Yes |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromSAPBW",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP BW input dataset name>",
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
                "type": "SapBwSource",
                "query": "<MDX query for SAP BW>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

Se estava a usar `RelationalSource` uma fonte dactilografada, ainda é suportada como está, enquanto é sugerido que utilize a nova que vai para a frente.

## <a name="data-type-mapping-for-sap-bw"></a>Mapeamento do tipo de dados para SAP BW

Ao copiar dados da SAP BW, os seguintes mapeamentos são usados desde os tipos de dados SAP BW até aos tipos de dados provisórios da Azure Data Factory. Consulte [os mapeamentos de schema e tipo de dados](copy-activity-schema-and-type-mapping.md) para saber como a atividade da cópia mapeia o esquema de origem e o tipo de dados para a pia.

| Tipo de dados SAP BW | Tipo de dados provisórios da fábrica de dados |
|:--- |:--- |
| ACCP | int |
| CHAR | String |
| CLNT | String |
| CURR | Decimal |
| RIO CUKY | String |
| DEC | Decimal |
| FLTP | Double (Duplo) |
| INT1 | Byte |
| INT2 | Int16 |
| INT4 | int |
| LANG | String |
| LCHR | String |
| RIO LRAW | Byte[] |
| PREC | Int16 |
| QUAN | Decimal |
| RAW | Byte[] |
| RAWSTRING | Byte[] |
| CORDA | String |
| UNIDADE | String |
| DATS | String |
| NUMC | String |
| TIMS | String |


## <a name="lookup-activity-properties"></a>Propriedades de atividade de procura

Para obter detalhes sobre as propriedades, consulte [a atividade de Lookup](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Passos seguintes
Para obter uma lista de lojas de dados suportadas como fontes e sumidouros pela atividade de cópia na Azure Data Factory, consulte lojas de [dados suportadas.](copy-activity-overview.md#supported-data-stores-and-formats)
