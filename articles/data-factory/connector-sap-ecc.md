---
title: Copiar dados do SAP ECC
description: Saiba como copiar dados do SAP ECC para lojas de dados de sumidouros suportados utilizando uma atividade de cópia num pipeline da Azure Data Factory.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 06/12/2020
ms.openlocfilehash: 4bdcb2b4008f54ff0d84594e6f3b5a7b76944e65
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84987013"
---
# <a name="copy-data-from-sap-ecc-by-using-azure-data-factory"></a>Copiar dados da SAP ECC utilizando a Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve como utilizar a atividade de cópia na Azure Data Factory para copiar dados da SAP Enterprise Central Component (ECC). Para obter mais informações, consulte [a visão geral da atividade da Cópia.](copy-activity-overview.md)

>[!TIP]
>Para obter o apoio global da ADF no cenário de integração de dados SAP, consulte [a integração de dados SAP utilizando o papel branco da Azure Data Factory](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf) com introdução, comparação e orientação detalhadas.

## <a name="supported-capabilities"></a>Capacidades suportadas

Este conector SAP ECC é suportado para as seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com [matriz de fonte/pia suportada](copy-activity-overview.md)
- [Atividade de procura](control-flow-lookup-activity.md)

Pode copiar dados do SAP ECC para qualquer loja de dados de sumidouro suportado. Para obter uma lista de lojas de dados que são suportadas como fontes ou sumidouros pela atividade de cópia, consulte a tabela [de lojas de dados suportadas.](copy-activity-overview.md#supported-data-stores-and-formats)

Especificamente, este conector SAP ECC suporta:

- Cópia de dados do SAP ECC na versão 7.0 da SAP NetWeaver e posteriormente.
- Copiar dados de quaisquer objetos expostos pelos serviços SAP ECC OData, tais como:

  - Mesas sap ou vistas.
  - Objetos de Programação de Aplicações empresariais [BAPI].
  - Extratores de dados.
  - Dados ou documentos intermédios (IDOCs) enviados para a Integração do Processo SAP (PI) que podem ser recebidos como OData através de adaptadores relativos.

- Copiar dados utilizando a autenticação básica.

>[!TIP]
>Para copiar dados do SAP ECC através de uma tabela SAP ou visualização, utilize o conector de [mesa SAP,](connector-sap-table.md) que é mais rápido e escalável.

## <a name="prerequisites"></a>Pré-requisitos

Geralmente, o SAP ECC expõe entidades através dos serviços OData através do SAP Gateway. Para utilizar este conector SAP ECC, é necessário:

- **Configurar o SAP Gateway.** Para servidores com versões SAP NetWeaver mais tarde que 7.4, o SAP Gateway já está instalado. Para versões anteriores, deve instalar o SAP Gateway incorporado ou o sistema de hub SAP Gateway antes de expor os dados do SAP ECC através dos serviços OData. Para configurar o SAP Gateway, consulte o [guia de instalação.](https://help.sap.com/saphelp_gateway20sp12/helpdata/en/c3/424a2657aa4cf58df949578a56ba80/frameset.htm)

- **Ativar e configurar o serviço SAP OData**. Pode ativar o serviço OData através do TCODE SICF em segundos. Também pode configurar quais os objetos que precisam de ser expostos. Para obter mais informações, consulte a [orientação passo a passo.](https://blogs.sap.com/2012/10/26/step-by-step-guide-to-build-an-odata-service-based-on-rfcs-part-1/)

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As secções seguintes fornecem detalhes sobre propriedades que são usadas para definir as entidades da Data Factory específicas do conector SAP ECC.

## <a name="linked-service-properties"></a>Propriedades de serviço ligadas

As seguintes propriedades são suportadas para o serviço ligado ao ECC SAP:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| `type` | A `type` propriedade deve ser definida para `SapEcc` . | Sim |
| `url` | O URL do serviço SAP ECC OData. | Sim |
| `username` | O nome de utilizador utilizado para ligar ao SAP ECC. | Não |
| `password` | A palavra-passe de texto simples usada para ligar ao SAP ECC. | Não |
| `connectVia` | O [tempo de integração](concepts-integration-runtime.md) a ser utilizado para ligar à loja de dados. Saiba mais na secção [Pré-Requisitos.](#prerequisites) Se não especificar um tempo de execução, utiliza-se o tempo de execução da integração Azure predefinido. | Não |

### <a name="example"></a>Exemplo

```json
{
    "name": "SapECCLinkedService",
    "properties": {
        "type": "SapEcc",
        "typeProperties": {
            "url": "<SAP ECC OData URL, e.g., http://eccsvrname:8000/sap/opu/odata/sap/zgw100_dd02l_so_srv/>",
            "username": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        }
    },
    "connectVia": {
        "referenceName": "<name of integration runtime>",
        "type": "IntegrationRuntimeReference"
    }
}
```

## <a name="dataset-properties"></a>Dataset properties (Propriedades do conjunto de dados)

Para obter uma lista completa das secções e propriedades disponíveis para definir conjuntos de dados, consulte [Datasets](concepts-datasets-linked-services.md). A secção seguinte fornece uma lista das propriedades suportadas pelo conjunto de dados SAP ECC.

Para copiar dados do SAP ECC, defina a `type` propriedade do conjunto de dados para `SapEccResource` .

As seguintes propriedades são suportadas:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| `path` | Caminho da entidade SAP ECC OData. | Sim |

### <a name="example"></a>Exemplo

```json
{
    "name": "SapEccDataset",
    "properties": {
        "type": "SapEccResource",
        "typeProperties": {
            "path": "<entity path, e.g., dd04tentitySet>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<SAP ECC linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy

Para obter uma lista completa das secções e propriedades disponíveis para definir [atividades,](concepts-pipelines-activities.md)consulte Pipelines . A secção seguinte fornece uma lista dos imóveis suportados pela fonte do ECC SAP.

### <a name="sap-ecc-as-a-source"></a>SAP ECC como fonte

Para copiar dados do SAP ECC, defina o `type` imóvel na `source` secção da atividade de cópia para `SapEccSource` .

As seguintes propriedades são suportadas na secção da atividade de `source` cópia:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| `type` | A `type` propriedade da secção da atividade de cópia deve ser definida para `source` `SapEccSource` . | Sim |
| `query` | As opções de consulta OData para filtrar os dados. Por exemplo:<br/><br/>`"$select=Name,Description&$top=10"`<br/><br/>O conector SAP ECC copia os dados do URL combinado:<br/><br/>`<URL specified in the linked service>/<path specified in the dataset>?<query specified in the copy activity's source section>`<br/><br/>Para obter mais informações, consulte [os componentes URL do OData](https://www.odata.org/documentation/odata-version-3-0/url-conventions/). | Não |
| `httpRequestTimeout` | O tempo limite (o valor **TimeSpan)** para o pedido HTTP obter uma resposta. Este valor é o tempo limite para obter uma resposta, não o tempo limite para ler dados de resposta. Se não for especificado, o valor predefinido é **00:30:00** (30 minutos). | Não |

### <a name="example"></a>Exemplo

```json
"activities":[
    {
        "name": "CopyFromSAPECC",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP ECC input dataset name>",
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
                "type": "SapEccSource",
                "query": "$top=10"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mappings-for-sap-ecc"></a>Mapeamentos de tipo de dados para SAP ECC

Quando está a copiar dados do SAP ECC, os seguintes mapeamentos são utilizados desde os tipos de dados OData para dados SAP ECC até tipos de dados provisórios da Azure Data Factory. Para saber como a atividade da cópia mapeia o esquema de origem e o tipo de dados para a pia, consulte [o Schema e os mapeamentos do tipo de dados](copy-activity-schema-and-type-mapping.md).

| Tipo de dados OData | Tipo de dados provisórios da Data Factory |
|:--- |:--- |
| `Edm.Binary` | `String` |
| `Edm.Boolean` | `Bool` |
| `Edm.Byte` | `String` |
| `Edm.DateTime` | `DateTime` |
| `Edm.Decimal` | `Decimal` |
| `Edm.Double` | `Double` |
| `Edm.Single` | `Single` |
| `Edm.Guid` | `String` |
| `Edm.Int16` | `Int16` |
| `Edm.Int32` | `Int32` |
| `Edm.Int64` | `Int64` |
| `Edm.SByte` | `Int16` |
| `Edm.String` | `String` |
| `Edm.Time` | `TimeSpan` |
| `Edm.DateTimeOffset` | `DateTimeOffset` |

> [!NOTE]
> Os tipos de dados complexos não são suportados atualmente.

## <a name="lookup-activity-properties"></a>Propriedades de atividade de procura

Para obter detalhes sobre as propriedades, consulte [a atividade de Lookup](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Próximos passos

Para obter uma lista das lojas de dados suportadas como fontes e sumidouros pela atividade de cópias na Azure Data Factory, consulte [lojas de dados suportadas](copy-activity-overview.md#supported-data-stores-and-formats).
