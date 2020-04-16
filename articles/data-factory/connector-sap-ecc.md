---
title: Copiar dados do SAP ECC
description: Saiba como copiar dados do SAP ECC para lojas de dados de sink suportadas utilizando uma atividade de cópia num pipeline azure Data Factory.
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
ms.openlocfilehash: ad26fca94527864af10bb0051336c372ea65b3e0
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81413803"
---
# <a name="copy-data-from-sap-ecc-by-using-azure-data-factory"></a>Copiar dados do SAP ECC utilizando a Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve como utilizar a atividade de cópia na Azure Data Factory para copiar dados da Componente Central da Empresa SAP (ECC). Para mais informações, consulte a [visão geral da atividade da Cópia](copy-activity-overview.md).

>[!TIP]
>Para conhecer o suporte global da ADF no cenário de integração de dados DoSAP, consulte a integração de [dados do SAP utilizando o livro branco da Azure Data Factory](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf) com introdução detalhada, comparação e orientação.

## <a name="supported-capabilities"></a>Capacidades suportadas

Este conector SAP ECC é suportado para as seguintes atividades:

- [Copiar atividade](copy-activity-overview.md) com matriz de [origem/pia suportada](copy-activity-overview.md)
- [Atividade de procura](control-flow-lookup-activity.md)

Pode copiar dados do SAP ECC para qualquer loja de dados de sink suportado. Para obter uma lista de lojas de dados que sejam suportadas como fontes ou afunda-se pela atividade de cópia, consulte a tabela de lojas de [dados suportadas.](copy-activity-overview.md#supported-data-stores-and-formats)

Especificamente, este conector SAP ECC suporta:

- Copiar dados do SAP ECC na versão 7.0 do SAP NetWeaver e posteriormente.
- Copiar dados de quaisquer objetos expostos pelos serviços SAP ECC OData, tais como:

  - Tabelas ou vistas sAP.
  - Interface de programação de aplicações empresariais [BAPI] objetos.
  - Extratores de dados.
  - Dados ou documentos intermédios (IDOCs) enviados para a Integração de Processos SAP (PI) que podem ser recebidos como OData através de adaptadores relativos.

- Copiar dados utilizando a autenticação básica.

>[!TIP]
>Para copiar dados do SAP ECC através de uma tabela ou vista SAP, utilize o conector de [mesa SAP,](connector-sap-table.md) que é mais rápido e escalável.

## <a name="prerequisites"></a>Pré-requisitos

Geralmente, a SAP ECC expõe entidades através de serviços OData através do SAP Gateway. Para utilizar este conector SAP ECC, é necessário:

- **Configurar o Portal SAP**. Para servidores com versões SAP NetWeaver mais tarde do que 7.4, o SAP Gateway já está instalado. Para versões anteriores, tem de instalar o SAP Gateway incorporado ou o sistema de hub SAP Gateway antes de expor os dados do SAP ECC através dos serviços OData. Para configurar o Portal SAP, consulte o guia de [instalação](https://help.sap.com/saphelp_gateway20sp12/helpdata/en/c3/424a2657aa4cf58df949578a56ba80/frameset.htm).

- **Ativar e configurar o serviço SAP OData**. Pode ativar o serviço OData através do TCODE SICF em segundos. Também pode configurar quais os objetos que precisam de ser expostos. Para mais informações, consulte a [orientação passo a passo](https://blogs.sap.com/2012/10/26/step-by-step-guide-to-build-an-odata-service-based-on-rfcs-part-1/).

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seguintes secções fornecem detalhes sobre propriedades que são usadas para definir as entidades data Factory específicas do conector SAP ECC.

## <a name="linked-service-properties"></a>Propriedades de serviço seletos

As seguintes propriedades são suportadas para o serviço ligado sap ECC:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| `type` | A `type` propriedade deve `SapEcc`ser definida para . | Sim |
| `url` | O URL do serviço SAP ECC OData. | Sim |
| `username` | O nome de utilizador utilizado para ligar ao SAP ECC. | Não |
| `password` | A palavra-passe de texto simples utilizada para ligar ao SAP ECC. | Não |
| `connectVia` | O tempo de [integração](concepts-integration-runtime.md) a ser utilizado para se ligar à loja de dados. Saiba mais na secção [Pré-Requisitos.](#prerequisites) Se não especificar um tempo de execução, o tempo de execução de integração do Azure padrão é utilizado. | Não |

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

Para copiar dados do SAP `type` ECC, detete a propriedade do conjunto de dados para `SapEccResource`.

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

Para obter uma lista completa das secções e propriedades disponíveis para definir atividades, consulte [Pipelines](concepts-pipelines-activities.md). A secção seguinte fornece uma lista dos imóveis suportados pela fonte do SAP ECC.

### <a name="sap-ecc-as-a-source"></a>SAP ECC como fonte

Para copiar dados do SAP `type` ECC, `source` coloque a propriedade `SapEccSource`na secção da atividade de cópia para .

As seguintes propriedades são suportadas `source` na secção da atividade da cópia:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| `type` | A `type` propriedade da secção `source` da atividade de `SapEccSource`cópia deve ser definida para . | Sim |
| `query` | As opções de consulta OData para filtrar os dados. Por exemplo:<br/><br/>`"$select=Name,Description&$top=10"`<br/><br/>O conector SAP ECC copia dados do URL combinado:<br/><br/>`<URL specified in the linked service>/<path specified in the dataset>?<query specified in the copy activity's source section>`<br/><br/>Para mais informações, consulte [os componentes url do OData](https://www.odata.org/documentation/odata-version-3-0/url-conventions/). | Não |

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

## <a name="data-type-mappings-for-sap-ecc"></a>Mapeamento de tipo de dados para SAP ECC

Quando está a copiar dados do SAP ECC, os seguintes mapeamentos são utilizados a partir de tipos de dados OData para dados SAP ECC para tipos de dados provisórios da Azure Data Factory. Para saber como a atividade da cópia mapeia o esquema de origem e o tipo de dados para a pia, consulte [schema e mapeamentos de tipo](copy-activity-schema-and-type-mapping.md)de dados .

| Tipo de dados OData | Data Factory tipo de dados provisórios |
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
> Os tipos de dados complexos não são atualmente suportados.

## <a name="lookup-activity-properties"></a>Propriedades de atividade de procura

Para saber mais detalhes sobre as propriedades, consulte a [atividade de Lookup.](control-flow-lookup-activity.md)

## <a name="next-steps"></a>Passos seguintes

Para obter uma lista das lojas de dados suportadas como fontes e pias pela atividade de cópia na Azure Data Factory, consulte as lojas de [dados suportadas](copy-activity-overview.md#supported-data-stores-and-formats).
