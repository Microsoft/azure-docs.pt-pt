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
ms.date: 04/26/2018
ms.author: jingwang
ms.openlocfilehash: ba268806dccb7c2671825c794383396a2ff20299
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57548818"
---
# <a name="copy-data-from-sap-ecc-using-azure-data-factory"></a>Copiar dados do SAP ECC com o Azure Data Factory

Este artigo descreve como utilizar a atividade de cópia no Azure Data Factory para copiar dados do SAP ECC (componente de Central empresarial do SAP). Ele se baseia no [copiar descrição geral da atividade](copy-activity-overview.md) artigo apresenta uma visão geral da atividade de cópia.

## <a name="supported-capabilities"></a>Capacidades suportadas

Pode copiar dados de SAP ECC para qualquer arquivo de dados de sink suportados. Para obter uma lista dos arquivos de dados que são suportados como origens/sinks a atividade de cópia, consulte a [arquivos de dados suportados](copy-activity-overview.md#supported-data-stores-and-formats) tabela.

Especificamente, este conector de SAP ECC suporta:

- Copiar dados do SAP ECC no SAP NetWeaver versão 7.0 e superior. 
- Copiar dados a partir de todos os objetos expostos por serviços OData do SAP ECC (por exemplo, SAP/exibições de tabela, BAPI, Extratores de dados, etc.) ou dados/IDOCs enviados para o SAP PI, que podem ser enviadas como OData através de adaptadores relativos.
- Cópia de dados usando a autenticação básica.

## <a name="prerequisites"></a>Pré-requisitos

Em geral, o SAP ECC expõe entidades por meio de serviços de OData através do Gateway de SAP. Para utilizar este conector de SAP ECC, terá de:

- **Configurar o Gateway de SAP**. Para servidores com a versão do SAP NetWeaver superior 7.4, já está instalado o Gateway de SAP. Caso contrário, tem de instalar o Gateway ou Gateway imbedded hub antes de exposição de dados SAP ECC através dos serviços de OData. Saiba como configurar o Gateway de SAP [guia de instalação](https://help.sap.com/saphelp_gateway20sp12/helpdata/en/c3/424a2657aa4cf58df949578a56ba80/frameset.htm).

- **Ativar e configurar o serviço OData do SAP**. Pode ativar os serviços de OData por meio de TCODE SICF em segundos. Também pode configurar quais os objetos têm de ser expostos. Eis um exemplo [orientações passo a passo](https://blogs.sap.com/2012/10/26/step-by-step-guide-to-build-an-odata-service-based-on-rfcs-part-1/).

## <a name="getting-started"></a>Introdução

Pode criar um pipeline com a atividade de cópia com o SDK de .NET, o SDK de Python, o Azure PowerShell, o REST API ou o modelo Azure Resource Manager. Ver [tutorial da atividade de cópia](quickstart-create-data-factory-dot-net.md) para obter instruções passo a passo Criar um pipeline com uma atividade de cópia.

As secções seguintes fornecem detalhes sobre as propriedades que são utilizadas para definir entidades do Data Factory específicas para o conector de SAP ECC.

## <a name="linked-service-properties"></a>Propriedades do serviço ligado

As seguintes propriedades são suportadas para o serviço de SAP ECC ligado:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade de tipo tem de ser definida como: **SapEcc** | Sim |
| url | O url do serviço OData do SAP ECC. | Sim |
| o nome de utilizador | O nome de utilizador utilizada para ligar para o SAP ECC. | Não |
| palavra-passe | A palavra-passe de texto sem formatação utilizada para ligar para o SAP ECC. | Não |
| connectVia | O [Integration Runtime](concepts-integration-runtime.md) a ser utilizado para ligar ao arquivo de dados. Pode utilizar o Runtime de integração autoalojado ou Runtime de integração do Azure (se o seu armazenamento de dados está acessível ao público). Se não for especificado, ele usa o padrão do Runtime de integração do Azure. |Não |

**Exemplo:**

```json
{
    "name": "SapECCLinkedService",
    "properties": {
        "type": "SapEcc",
        "typeProperties": {
            "url": "<SAP ECC OData url e.g. http://eccsvrname:8000/sap/opu/odata/sap/zgw100_dd02l_so_srv/>",
            "username": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        }
    },
    "connectVia": {
        "referenceName": "<name of Integration Runtime>",
        "type": "IntegrationRuntimeReference"
    }
}
```

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Para obter uma lista completa das secções e propriedades disponíveis para definir conjuntos de dados, consulte a [conjuntos de dados](concepts-datasets-linked-services.md) artigo. Esta seção fornece uma lista de propriedades suportadas pelo conjunto de dados do SAP ECC.

Para copiar dados do SAP ECC, defina a propriedade de tipo de conjunto de dados para **SapEccResource**. São suportadas as seguintes propriedades:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| caminho | Caminho da entidade SAP ECC OData. | Sim |

**Exemplo**

```json
{
    "name": "SapEccDataset",
    "properties": {
        "type": "SapEccResource",
        "typeProperties": {
            "path": "<entity path e.g. dd04tentitySet>"
        },
        "linkedServiceName": {
            "referenceName": "<SAP ECC linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy

Para obter uma lista completa das secções e propriedades disponíveis para a definição de atividades, consulte a [Pipelines](concepts-pipelines-activities.md) artigo. Esta seção fornece uma lista de propriedades suportadas pela origem SAP ECC.

### <a name="sap-ecc-as-source"></a>SAP ECC como origem

Para copiar dados do SAP ECC, defina o tipo de origem na atividade de cópia para **SapEccSource**. As seguintes propriedades são suportadas na atividade de cópia **origem** secção:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade de tipo de origem de atividade de cópia tem de ser definida: **SapEccSource** | Sim |
| consulta | Opções de consulta de OData para filtrar dados. Exemplo: "$select = nome, descrição e $top = 10".<br/><br/>Conector de SAP ECC copia dados da URL combinado: (o url especificado no serviço ligado) / (o caminho especificado no conjunto de dados)? (consulta especificada na origem de atividade de cópia). Consulte a [componentes do URL de OData](https://www.odata.org/documentation/odata-version-3-0/url-conventions/). | Não |

**Exemplo:**

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

## <a name="data-type-mapping-for-sap-ecc"></a>Mapeamento de tipo de dados para o SAP ECC

Quando se copiam dados a partir do SAP ECC, são utilizados os seguintes mapeamentos entre tipos de dados do OData para dados do SAP ECC para tipos de dados intermediárias do Azure Data Factory. Ver [mapeamentos de tipo de esquema e dados](copy-activity-schema-and-type-mapping.md) para saber mais sobre como atividade de cópia mapeia o tipo de esquema e os dados de origem para o sink.

| Tipo de dados do OData | Tipo de dados intermediárias de fábrica de dados |
|:--- |:--- |:--- |
| Edm.Binary | String |
| Edm.Boolean | Bool |
| Edm.Byte | String |
| Edm.DateTime | DateTime |
| Edm.Decimal | Decimal |
| Edm.Double | Double |
| Edm.Single | Single |
| Edm.Guid | String |
| Edm.Int16 | Int16 |
| Edm.Int32 | Int32 |
| Edm.Int64 | Int64 |
| Edm.SByte | Int16 |
| Edm.String | String |
| Edm.Time | TimeSpan |
| Edm.DateTimeOffset | DateTimeOffset |

> [!NOTE]
> Agora, não são suportados tipos de dados complexos.

## <a name="next-steps"></a>Passos Seguintes
Para obter uma lista dos arquivos de dados suportados como origens e sinks, a atividade de cópia no Azure Data Factory, veja [arquivos de dados suportados](copy-activity-overview.md#supported-data-stores-and-formats).
