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
ms.date: 12/07/2018
ms.author: jingwang
ms.openlocfilehash: f86931aad4eab697e4a0d2dfc47a6d4ff5bfc256
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61401217"
---
# <a name="copy-data-from-impala-by-using-azure-data-factory-preview"></a>Copiar dados do Impala com o Azure Data Factory (pré-visualização)

Este artigo descreve como utilizar a atividade de cópia no Azure Data Factory para copiar dados de Impala. Ele se baseia no [descrição geral da atividade de cópia](copy-activity-overview.md) artigo apresenta uma visão geral da atividade de cópia.

> [!IMPORTANT]
> Este conector está atualmente em pré-visualização. Pode experimentá-lo e fornecer comentários. Se quiser realizar uma dependência em conectores de pré-visualização na sua solução, contacte o [Suporte do Azure](https://azure.microsoft.com/support/).

## <a name="supported-capabilities"></a>Capacidades suportadas

Pode copiar dados de Impala para qualquer arquivo de dados de sink suportados. Para obter uma lista dos arquivos de dados que são suportados como origens ou sinks a atividade de cópia, consulte a [arquivos de dados suportados](copy-activity-overview.md#supported-data-stores-and-formats) tabela.

 Data Factory fornece um driver incorporado para permitir a conectividade. Portanto, não precisa de instalar manualmente um driver para utilizar este conector.

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
| connectVia | O [runtime de integração](concepts-integration-runtime.md) a ser utilizado para ligar ao arquivo de dados. Pode utilizar o Runtime de integração autoalojado ou Runtime de integração do Azure (se o seu armazenamento de dados está acessível ao público). Se não for especificado, ele usa o padrão do Runtime de integração do Azure. |Não |

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
| type | A propriedade de tipo do conjunto de dados deve ser definida como: **ImpalaObject** | Sim |
| tableName | Nome da tabela. | Não (se for especificada "consulta" na origem de atividade) |

**Exemplo**

```json
{
    "name": "ImpalaDataset",
    "properties": {
        "type": "ImpalaObject",
        "linkedServiceName": {
            "referenceName": "<Impala linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
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
