---
title: Copiar dados do SAP ECC
description: Saiba como copiar dados do SAP ECC para armazenamentos de dados de coletor com suporte usando uma atividade de cópia em um pipeline de Azure Data Factory.
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
ms.openlocfilehash: f875d8f4603a8f51b8b8fed2438e6f3a30c87aeb
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74931178"
---
# <a name="copy-data-from-sap-ecc-by-using-azure-data-factory"></a>Copiar dados do SAP ECC usando o Azure Data Factory

Este artigo descreve como usar a atividade de cópia no Azure Data Factory para copiar dados do SAP Enterprise central Component (ECC). Para obter mais informações, consulte [visão geral da atividade de cópia](copy-activity-overview.md).

>[!TIP]
>Para aprender o suporte geral do ADF no cenário de integração de dados do SAP, consulte [integração de dados SAP usando Azure data Factory White Paper](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf) com introdução, análise e diretrizes detalhadas.

## <a name="supported-capabilities"></a>Capacidades suportadas

Este conector SAP ECC tem suporte para as seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com [matriz de coletor/origem com suporte](copy-activity-overview.md)
- [Atividade de Pesquisa](control-flow-lookup-activity.md)

Você pode copiar dados do SAP ECC para qualquer armazenamento de dados de coletor com suporte. Para obter uma lista dos arquivos de dados que são suportados como origens ou sinks a atividade de cópia, consulte a [arquivos de dados suportados](copy-activity-overview.md#supported-data-stores-and-formats) tabela.

Especificamente, esse conector SAP ECC dá suporte a:

- Copiando dados do SAP ECC no SAP NetWeaver versão 7,0 e posterior.
- Copiar dados de qualquer objeto exposto pelos serviços do SAP ECC OData, como:

  - Tabelas ou exibições SAP.
  - Objetos de interface de programação de aplicativo de negócios [BAPI].
  - Extratores de dados.
  - Dados ou documentos intermediários (IDOCs) enviados à integração de processos SAP (PI) que podem ser recebidos como OData por meio de adaptadores relativos.

- Copiar dados usando a autenticação básica.

>[!TIP]
>Para copiar dados do SAP ECC por meio de uma tabela ou exibição do SAP, use o conector de [tabela do SAP](connector-sap-table.md) , que é mais rápido e mais escalonável.

## <a name="prerequisites"></a>Pré-requisitos

Geralmente, o SAP ECC expõe entidades por meio de serviços OData por meio do gateway SAP. Para usar esse conector SAP ECC, você precisa:

- **Configurar o gateway do SAP**. Para servidores com versões do SAP NetWeaver posteriores a 7,4, o gateway do SAP já está instalado. Para versões anteriores, você deve instalar o gateway SAP incorporado ou o sistema de Hub de gateway do SAP antes de expor dados de ECC do SAP por meio de serviços OData. Para configurar o gateway SAP, consulte o [Guia de instalação](https://help.sap.com/saphelp_gateway20sp12/helpdata/en/c3/424a2657aa4cf58df949578a56ba80/frameset.htm).

- **Ative e configure o serviço do SAP OData**. Você pode ativar o serviço OData por meio de TCODE SICF em segundos. Você também pode configurar quais objetos precisam ser expostos. Para obter mais informações, consulte as diretrizes passo a [passo](https://blogs.sap.com/2012/10/26/step-by-step-guide-to-build-an-odata-service-based-on-rfcs-part-1/).

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções a seguir fornecem detalhes sobre as propriedades que são usadas para definir as entidades de Data Factory específicas para o conector do SAP ECC.

## <a name="linked-service-properties"></a>Propriedades do serviço ligado

As propriedades a seguir têm suporte para o serviço vinculado do SAP ECC:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| `type` | A propriedade `type` deve ser definida como `SapEcc`. | Sim |
| `url` | A URL do serviço de OData do SAP ECC. | Sim |
| `username` | O nome de usuário usado para se conectar ao SAP ECC. | Não |
| `password` | A senha de texto sem formatação usada para se conectar ao SAP ECC. | Não |
| `connectVia` | O [runtime de integração](concepts-integration-runtime.md) a ser utilizado para ligar ao arquivo de dados. Saiba mais na seção de [pré-requisitos](#prerequisites) . Se você não especificar um tempo de execução, o tempo de execução de integração do Azure padrão será usado. | Não |

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

Para obter uma lista completa das seções e propriedades disponíveis para definir conjuntos de os, consulte [DataSets](concepts-datasets-linked-services.md). A seção a seguir fornece uma lista das propriedades com suporte pelo conjunto de de SAP ECC.

Para copiar dados do SAP ECC, defina a propriedade `type` do conjunto de dados como `SapEccResource`.

São suportadas as seguintes propriedades:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| `path` | Caminho da entidade do SAP ECC OData. | Sim |

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

Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, consulte [pipelines](concepts-pipelines-activities.md). A seção a seguir fornece uma lista das propriedades com suporte pela origem do SAP ECC.

### <a name="sap-ecc-as-a-source"></a>SAP ECC como uma fonte

Para copiar dados do SAP ECC, defina a propriedade `type` na seção `source` da atividade de cópia como `SapEccSource`.

As propriedades a seguir têm suporte na seção `source` da atividade de cópia:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| `type` | A propriedade `type` da seção `source` da atividade de cópia deve ser definida como `SapEccSource`. | Sim |
| `query` | As opções de consulta OData para filtrar os dados. Por exemplo:<br/><br/>`"$select=Name,Description&$top=10"`<br/><br/>O conector SAP ECC copia dados da URL combinada:<br/><br/>`<URL specified in the linked service>/<path specified in the dataset>?<query specified in the copy activity's source section>`<br/><br/>Para obter mais informações, consulte [componentes de URL do OData](https://www.odata.org/documentation/odata-version-3-0/url-conventions/). | Não |

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

Quando você está copiando dados do SAP ECC, os seguintes mapeamentos são usados de tipos de dados OData para dados do SAP ECC para Azure Data Factory tipos de dados provisórios. Para saber como a atividade de cópia mapeia o esquema de origem e o tipo de dados para o coletor, consulte [mapeamentos de tipo de dados e esquema](copy-activity-schema-and-type-mapping.md).

| Tipo de dados OData | Tipo de dados intermediárias de fábrica de dados |
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
> Atualmente, não há suporte para tipos de dados complexos.

## <a name="lookup-activity-properties"></a>Propriedades da atividade de pesquisa

Para obter detalhes sobre as propriedades, verifique a [atividade de pesquisa](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Passos seguintes

Para obter uma lista dos armazenamentos de dados com suporte como fontes e coletores pela atividade de cópia no Azure Data Factory, consulte [armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).
