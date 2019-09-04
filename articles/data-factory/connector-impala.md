---
title: Copiar dados do Impala com o Azure Data Factory (pré-visualização) | Documentos da Microsoft
description: Saiba como copiar dados de Impala para arquivos de dados de sink suportado através de uma atividade de cópia num pipeline de fábrica de dados.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: jingwang
ms.openlocfilehash: 01712508b15be947f4c3d7df401ad431d9f25455
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70276386"
---
# <a name="copy-data-from-impala-by-using-azure-data-factory-preview"></a>Copiar dados do Impala com o Azure Data Factory (pré-visualização)

Este artigo descreve como utilizar a atividade de cópia no Azure Data Factory para copiar dados de Impala. Ele se baseia no [descrição geral da atividade de cópia](copy-activity-overview.md) artigo apresenta uma visão geral da atividade de cópia.

> [!IMPORTANT]
> Este conector está atualmente em pré-visualização. Pode experimentá-lo e fornecer comentários. Se quiser realizar uma dependência em conectores de pré-visualização na sua solução, contacte o [Suporte do Azure](https://azure.microsoft.com/support/).

## <a name="supported-capabilities"></a>Capacidades suportadas

Pode copiar dados de Impala para qualquer arquivo de dados de sink suportados. Para obter uma lista dos arquivos de dados que são suportados como origens ou sinks a atividade de cópia, consulte a [arquivos de dados suportados](copy-activity-overview.md#supported-data-stores-and-formats) tabela.

Data Factory fornece um driver incorporado para permitir a conectividade. Portanto, não precisa de instalar manualmente um driver para utilizar este conector.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As secções seguintes fornecem detalhes sobre as propriedades que são utilizadas para definir entidades do Data Factory específicas para o conector Impala.

## <a name="linked-service-properties"></a>Propriedades do serviço ligado

As seguintes propriedades são suportadas para o serviço ligado de Impala.

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | A propriedade de tipo deve ser definida como **Impala**. | Sim |
| host | O nome ou endereço IP anfitrião do servidor Impala (ou seja, 192.168.222.160).  | Sim |
| port | A porta TCP que o servidor de Impala utiliza para escutar ligações de cliente. O valor predefinido é 21050.  | Não |
| authenticationType | O tipo de autenticação a utilizar. <br/>Valores permitidos são **anónimo**, **SASLUsername**, e **UsernameAndPassword**. | Sim |
| username | O nome de utilizador utilizado para aceder ao servidor de Impala. O valor predefinido é anônimo ao utilizar SASLUsername.  | Não |
| password | A palavra-passe que corresponde ao nome do utilizador quando utiliza UsernameAndPassword. Marcar esse campo como uma SecureString armazena de forma segura na fábrica de dados, ou [referenciar um segredo armazenado no Azure Key Vault](store-credentials-in-key-vault.md). | Não |
| enableSsl | Especifica se as ligações ao servidor são encriptadas com SSL. O valor predefinido é **false**.  | Não |
| trustedCertPath | O caminho completo do ficheiro. pem que contém os certificados de AC fidedigna, utilizados para verificar se o servidor quando se liga através de SSL. Esta propriedade pode ser definida apenas se utilizar o SSL no Integration Runtime autoalojado. O valor predefinido é o arquivo de cacerts.pem instalado com o runtime de integração.  | Não |
| useSystemTrustStore | Especifica se pretende utilizar um certificado de AC a partir da loja de confiança do sistema ou a partir de um ficheiro PEM especificado. O valor predefinido é **false**.  | Não |
| allowHostNameCNMismatch | Especifica se exige um nome de certificado SSL emitidos pela AC para corresponder ao nome do anfitrião do servidor, quando se liga através de SSL. O valor predefinido é **false**.  | Não |
| allowSelfSignedServerCert | Especifica se pretende permitir que os certificados autoassinados do servidor. O valor predefinido é **false**.  | Não |
| connectVia | O [runtime de integração](concepts-integration-runtime.md) a ser utilizado para ligar ao arquivo de dados. Saiba mais na seção de [pré-requisitos](#prerequisites) . Se não for especificado, ele usa o padrão do Runtime de integração do Azure. |Não |

**Exemplo:**

```json
{
    "name": "ImpalaLinkedService",
    "properties": {
        "type": "Impala",
        "typeProperties": {
            "host" : "<host>",
            "port" : "<port>",
            "authenticationType" : "UsernameAndPassword",
            "username" : "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Para obter uma lista completa das secções e propriedades disponíveis para definir conjuntos de dados, consulte a [conjuntos de dados](concepts-datasets-linked-services.md) artigo. Esta secção fornece uma lista das propriedades compatíveis com o conjunto de dados Impala.

Para copiar dados de Impala, defina a propriedade de tipo de conjunto de dados para **ImpalaObject**. São suportadas as seguintes propriedades:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | A propriedade Type do conjunto de conjuntos deve ser definida como: **ImpalaObject** | Sim |
| schema | Nome do esquema. |Não (se for especificada "query" na origem de atividade)  |
| table | Nome da tabela. |Não (se for especificada "query" na origem de atividade)  |
| tableName | Nome da tabela com esquema. Essa propriedade tem suporte para compatibilidade com versões anteriores. Use `schema` e`table` para uma nova carga de trabalho. | Não (se for especificada "query" na origem de atividade) |

**Exemplo**

```json
{
    "name": "ImpalaDataset",
    "properties": {
        "type": "ImpalaObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Impala linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy

Para obter uma lista completa das secções e propriedades disponíveis para a definição de atividades, consulte a [Pipelines](concepts-pipelines-activities.md) artigo. Esta secção fornece uma lista das propriedades compatíveis com o tipo de origem de Impala.

### <a name="impala-as-a-source-type"></a>Impala como um tipo de origem

Para copiar dados de Impala, definir o tipo de origem na atividade de cópia para **ImpalaSource**. As seguintes propriedades são suportadas na atividade de cópia **origem** secção.

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | A propriedade de tipo de origem de atividade de cópia tem de ser definida **ImpalaSource**. | Sim |
| query | Utilize a consulta SQL personalizada para ler os dados. Um exemplo é `"SELECT * FROM MyTable"`. | Não (se for especificado "tableName" no conjunto de dados) |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromImpala",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Impala input dataset name>",
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
                "type": "ImpalaSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="next-steps"></a>Passos Seguintes
Para obter uma lista dos arquivos de dados suportados como origens e sinks, a atividade de cópia no Data Factory, veja [arquivos de dados suportados](copy-activity-overview.md#supported-data-stores-and-formats).
