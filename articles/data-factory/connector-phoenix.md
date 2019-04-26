---
title: Copiar dados de Phoenix com o Azure Data Factory | Documentos da Microsoft
description: Saiba como copiar dados de Phoenix para arquivos de dados de sink suportado através de uma atividade de cópia num pipeline do Azure Data Factory.
services: data-factory
documentationcenter: ''
author: WenJason
manager: digimobile
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
origin.date: 12/07/2018
ms.date: 04/22/2019
ms.author: v-jay
ms.openlocfilehash: 012057c7d01924ab1998a010b6ea0c7d83651a4d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60405928"
---
# <a name="copy-data-from-phoenix-using-azure-data-factory"></a>Copiar dados de Phoenix com o Azure Data Factory 

Este artigo descreve como utilizar a atividade de cópia no Azure Data Factory para copiar dados de Phoenix. Ele se baseia no [copiar descrição geral da atividade](copy-activity-overview.md) artigo apresenta uma visão geral da atividade de cópia.

## <a name="supported-capabilities"></a>Capacidades suportadas

Pode copiar dados de Phoenix para qualquer arquivo de dados de sink suportados. Para obter uma lista dos arquivos de dados que são suportados como origens/sinks a atividade de cópia, consulte a [arquivos de dados suportados](copy-activity-overview.md#supported-data-stores-and-formats) tabela.

O Azure Data Factory fornece um driver incorporado para permitir a conectividade, portanto não precisa de instalar manualmente a qualquer driver utilizar este conector.

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As secções seguintes fornecem detalhes sobre as propriedades que são utilizadas para definir entidades do Data Factory específicas para o conector de Phoenix.

## <a name="linked-service-properties"></a>Propriedades do serviço ligado

As seguintes propriedades são suportadas para o serviço de Phoenix ligado:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | A propriedade de tipo tem de ser definida como: **Phoenix** | Sim |
| anfitrião | O nome ou endereço IP anfitrião do servidor Phoenix. (ou seja, 192.168.222.160)  | Sim |
| porta | A porta TCP que o servidor de Phoenix utiliza para escutar ligações de cliente. O valor predefinido é 8765. Se ligar ao Azure HDInsights, especifique a porta como 443. | Não |
| httpPath | O URL de parcial correspondente para o servidor de Phoenix. (ou seja, /gateway/sandbox/phoenix/version). Especificar `/hbasephoenix0` se utilizar o cluster do HDInsights.  | Não |
| authenticationType | O mecanismo de autenticação utilizado para ligar ao servidor de Phoenix. <br/>Valores permitidos são: **Anonymous**, **UsernameAndPassword**, **WindowsAzureHDInsightService** | Sim |
| o nome de utilizador | O nome de utilizador utilizado para ligar ao servidor de Phoenix.  | Não |
| palavra-passe | A palavra-passe correspondente ao nome do usuário. Marcar esse campo como uma SecureString armazena de forma segura na fábrica de dados, ou [referenciar um segredo armazenado no Azure Key Vault](store-credentials-in-key-vault.md). | Não |
| enableSsl | Especifica se as ligações ao servidor são encriptadas com SSL. O valor predefinido é false.  | Não |
| trustedCertPath | O caminho completo do ficheiro. pem que contém os certificados de AC fidedigna para verificar o servidor ao ligar-se através de SSL. Esta propriedade só pode ser definida ao utilizar o SSL em Ir autoalojado. O valor predefinido é o arquivo de cacerts.pem instalado com o IR.  | Não |
| useSystemTrustStore | Especifica se pretende utilizar um certificado de AC a partir da loja de confiança do sistema ou a partir de um ficheiro PEM especificado. O valor predefinido é false.  | Não |
| allowHostNameCNMismatch | Especifica se exige um nome de certificado SSL emitidos pela AC para coincidir com o nome de anfitrião do servidor ao ligar-se através de SSL. O valor predefinido é false.  | Não |
| allowSelfSignedServerCert | Especifica se pretende permitir que os certificados autoassinados do servidor. O valor predefinido é false.  | Não |
| connectVia | O [Integration Runtime](concepts-integration-runtime.md) a ser utilizado para ligar ao arquivo de dados. Pode utilizar o Runtime de integração autoalojado ou Runtime de integração do Azure (se o seu armazenamento de dados está acessível ao público). Se não for especificado, ele usa o padrão do Runtime de integração do Azure. |Não |

>[!NOTE]
>Se o seu cluster não suportar sessões adesivas por exemplo, o HDInsight, adicionar explicitamente o índice do nó no final da definição do caminho http, por exemplo, especifique `/hbasephoenix0` em vez de `/hbasephoenix`.

**Exemplo:**

```json
{
    "name": "PhoenixLinkedService",
    "properties": {
        "type": "Phoenix",
        "typeProperties": {
            "host" : "<cluster>.azurehdinsight.cn",
            "port" : "443",
            "httpPath" : "/hbasephoenix0",
            "authenticationType" : "WindowsAzureHDInsightService",
            "username" : "<username>",
            "password": {
                 "type": "SecureString",
                 "value": "<password>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Para obter uma lista completa das secções e propriedades disponíveis para definir conjuntos de dados, consulte a [conjuntos de dados](concepts-datasets-linked-services.md) artigo. Esta seção fornece uma lista de propriedades suportadas pelo conjunto de dados de Phoenix.

Para copiar dados de Phoenix, defina a propriedade de tipo de conjunto de dados para **PhoenixObject**. São suportadas as seguintes propriedades:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | A propriedade de tipo do conjunto de dados deve ser definida como: **PhoenixObject** | Sim |
| tableName | Nome da tabela. | Não (se for especificada "consulta" na origem de atividade) |

**Exemplo**

```json
{
    "name": "PhoenixDataset",
    "properties": {
        "type": "PhoenixObject",
        "linkedServiceName": {
            "referenceName": "<Phoenix linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy

Para obter uma lista completa das secções e propriedades disponíveis para a definição de atividades, consulte a [Pipelines](concepts-pipelines-activities.md) artigo. Esta seção fornece uma lista de propriedades suportadas pela origem de Phoenix.

### <a name="phoenix-as-source"></a>Phoenix como origem

Para copiar dados de Phoenix, definir o tipo de origem na atividade de cópia para **PhoenixSource**. As seguintes propriedades são suportadas na atividade de cópia **origem** secção:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | A propriedade de tipo de origem de atividade de cópia tem de ser definida: **PhoenixSource** | Sim |
| consulta | Utilize a consulta SQL personalizada para ler os dados. Por exemplo: `"SELECT * FROM MyTable"`. | Não (se for especificado "tableName" no conjunto de dados) |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromPhoenix",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Phoenix input dataset name>",
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
                "type": "PhoenixSource",
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
Para obter uma lista dos arquivos de dados suportados como origens e sinks, a atividade de cópia no Azure Data Factory, veja [arquivos de dados suportados](copy-activity-overview.md#supported-data-stores-and-formats).
