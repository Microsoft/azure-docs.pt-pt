---
title: Copiar dados do SAP ECC com o Azure Data Factory | Documentos da Microsoft
description: Saiba como copiar dados do SAP ECC para arquivos de dados de sink suportado através de uma atividade de cópia num pipeline do Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 07/02/2019
ms.author: jingwang
ms.openlocfilehash: 7ccd2e7a804c6495f6caf5e264b1f7c2a36cb02e
ms.sourcegitcommit: 441e59b8657a1eb1538c848b9b78c2e9e1b6cfd5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67827774"
---
# <a name="copy-data-from-sap-ecc-by-using-azure-data-factory"></a>Copiar dados do SAP ECC com o Azure Data Factory

Este artigo descreve como utilizar a atividade de cópia no Azure Data Factory para copiar dados do SAP Enterprise Central componente (ECC). Para obter mais informações, consulte [copiar descrição geral da atividade](copy-activity-overview.md).

## <a name="supported-capabilities"></a>Capacidades suportadas

Pode copiar dados de SAP ECC para qualquer arquivo de dados de sink suportados. Para obter uma lista dos arquivos de dados que são suportados como origens ou sinks a atividade de cópia, consulte a [arquivos de dados suportados](copy-activity-overview.md#supported-data-stores-and-formats) tabela.

Especificamente, este conector de SAP ECC suporta:

- Copiar dados do SAP ECC em SAP NetWeaver versão 7.0 e posterior.
- Copiar dados de todos os objetos expostos pelos serviços de SAP ECC OData, tais como:

  - SAP tabelas ou vistas.
  - Objetos de Application Programming Interface [BAPI] comerciais.
  - Extratores de dados.
  - Dados ou documentos intermédios (IDOCs) enviados ao SAP processo de integração (PI) que podem ser enviadas como OData através de adaptadores relativos.

- Copiar dados utilizando a autenticação básica.

>[!TIP]
>Para copiar dados do SAP ECC através de uma tabela SAP ou a vista, utilize o [tabela SAP](connector-sap-table.md) conector, que é mais rápido e mais escalonável.

## <a name="prerequisites"></a>Pré-requisitos

Em geral, o SAP ECC expõe entidades por meio de serviços de OData através do Gateway de SAP. Para utilizar este conector de SAP ECC, terá de:

- **Configurar o Gateway de SAP**. Para servidores com versões do SAP NetWeaver superior à versão 7.4, SAP Gateway já está instalado. Para versões anteriores, tem de instalar o Gateway de SAP incorporado ou o sistema de hub do Gateway de SAP antes de exposição de dados SAP ECC através dos serviços de OData. Para configurar o Gateway de SAP, consulte a [guia de instalação](https://help.sap.com/saphelp_gateway20sp12/helpdata/en/c3/424a2657aa4cf58df949578a56ba80/frameset.htm).

- **Ativar e configurar o serviço OData do SAP**. Pode ativar o serviço OData por meio de TCODE SICF em segundos. Também pode configurar os objetos que necessitam de ser expostos. Para obter mais informações, consulte a [orientações passo a passo](https://blogs.sap.com/2012/10/26/step-by-step-guide-to-build-an-odata-service-based-on-rfcs-part-1/).

## <a name="get-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As secções seguintes fornecem detalhes sobre as propriedades que são utilizadas para definir as entidades da fábrica de dados específicas para o conector de SAP ECC.

## <a name="linked-service-properties"></a>Propriedades do serviço ligado

As seguintes propriedades são suportadas para o serviço de SAP ECC ligado:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| `type` | O `type` propriedade deve ser definida `SapEcc`. | Sim |
| `url` | O URL do serviço OData do SAP ECC. | Sim |
| `username` | O nome de utilizador utilizada para ligar ao SAP ECC. | Não |
| `password` | A palavra-passe de texto sem formatação utilizada para ligar ao SAP ECC. | Não |
| `connectVia` | O [runtime de integração](concepts-integration-runtime.md) a ser utilizado para ligar ao arquivo de dados. Pode usar um runtime de integração autoalojado ou o runtime de integração do Azure (se o seu armazenamento de dados está acessível ao público). Se não especificar um tempo de execução, `connectVia` utiliza o runtime de integração do Azure padrão. | Não |

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

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Para obter uma lista completa das secções e propriedades disponíveis para definir conjuntos de dados, consulte [conjuntos de dados](concepts-datasets-linked-services.md). A secção seguinte fornece uma lista de propriedades suportadas pelo conjunto de dados SAP ECC.

Para copiar dados do SAP ECC, defina o `type` propriedade do conjunto de dados para `SapEccResource`.

São suportadas as seguintes propriedades:

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
        "linkedServiceName": {
            "referenceName": "<SAP ECC linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy

Para obter uma lista completa das secções e propriedades disponíveis para a definição de atividades, consulte [Pipelines](concepts-pipelines-activities.md). A secção seguinte fornece uma lista de propriedades suportado pela origem de SAP ECC.

### <a name="sap-ecc-as-a-source"></a>SAP ECC como uma origem

Para copiar dados do SAP ECC, defina o `type` propriedade no `source` secção a atividade de cópia para `SapEccSource`.

As seguintes propriedades são suportadas na atividade de cópia `source` secção:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| `type` | O `type` propriedade da atividade de cópia `source` secção tem de ser definida como `SapEccSource`. | Sim |
| `query` | As opções de consulta de OData para filtrar os dados. Por exemplo:<br/><br/>`"$select=Name,Description&$top=10"`<br/><br/>O conector de SAP ECC copia dados a partir do URL combinado:<br/><br/>`<URL specified in the linked service>/<path specified in the dataset>?<query specified in the copy activity's source section>`<br/><br/>Para obter mais informações, consulte [componentes do URL de OData](https://www.odata.org/documentation/odata-version-3-0/url-conventions/). | Não |

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

## <a name="data-type-mappings-for-sap-ecc"></a>Mapeamentos de tipo de dados para o SAP ECC

Quando estiver a copiar dados do SAP ECC, são utilizados os seguintes mapeamentos entre tipos de dados do OData para dados do SAP ECC para tipos de dados intermediárias do Azure Data Factory. Para saber como a atividade de cópia mapeia o tipo de esquema e os dados de origem para o sink, veja [mapeamentos de tipo de esquema e dados](copy-activity-schema-and-type-mapping.md).

| Tipo de dados do OData | Tipo de dados intermediárias de fábrica de dados |
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
> Tipos de dados complexos não são atualmente suportados.

## <a name="next-steps"></a>Passos Seguintes

Para obter uma lista de arquivos de dados suportados como origens e sinks, a atividade de cópia no Azure Data Factory, veja [arquivos de dados suportados](copy-activity-overview.md#supported-data-stores-and-formats).
