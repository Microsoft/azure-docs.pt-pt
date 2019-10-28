---
title: Copiar dados do Impala usando o Azure Data Factory | Microsoft Docs
description: Saiba como copiar dados do Impala para armazenamentos de dados de coletor com suporte usando uma atividade de cópia em um pipeline de data factory.
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
ms.openlocfilehash: 54f46c09cfab64d53e8f5f503ca46004289f18c2
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72935573"
---
# <a name="copy-data-from-impala-by-using-azure-data-factory"></a>Copiar dados do Impala usando Azure Data Factory

Este artigo descreve como usar a atividade de cópia no Azure Data Factory para copiar dados do Impala. Ele se baseia no artigo [visão geral da atividade de cópia](copy-activity-overview.md) que apresenta uma visão geral da atividade de cópia.

## <a name="supported-capabilities"></a>Recursos com suporte

Este conector do Impala tem suporte para as seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com [matriz de coletor/origem com suporte](copy-activity-overview.md)
- [Atividade de Pesquisa](control-flow-lookup-activity.md)

Você pode copiar dados do Impala para qualquer armazenamento de dados de coletor com suporte. Para obter uma lista de armazenamentos de dados com suporte como fontes ou coletores pela atividade de cópia, consulte a tabela [armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats) .

O Data Factory fornece um driver interno para habilitar a conectividade. Portanto, você não precisa instalar manualmente um driver para usar esse conector.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções a seguir fornecem detalhes sobre as propriedades que são usadas para definir Data Factory entidades específicas para o conector do Impala.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

As propriedades a seguir têm suporte para o serviço vinculado do Impala.

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade Type deve ser definida como **Impala**. | Sim |
| hospedeira | O endereço IP ou nome do host do servidor Impala (ou seja, 192.168.222.160).  | Sim |
| porta | A porta TCP que o servidor Impala usa para escutar conexões de cliente. O valor padrão é 21050.  | Não |
| authenticationType | O tipo de autenticação a ser usado. <br/>Os valores permitidos são **Anonymous**, **SASLUsername**e **UsernameAndPassword**. | Sim |
| o nome de utilizador | O nome de usuário usado para acessar o servidor Impala. O valor padrão é anônimo quando você usa SASLUsername.  | Não |
| palavra-passe | A senha que corresponde ao nome de usuário quando você usa UsernameAndPassword. Marque este campo como uma SecureString para armazená-lo com segurança no Data Factory ou [faça referência a um segredo armazenado em Azure Key Vault](store-credentials-in-key-vault.md). | Não |
| enableSsl | Especifica se as conexões com o servidor são criptografadas usando SSL. O valor padrão é **false**.  | Não |
| trustedCertPath | O caminho completo do arquivo. pem que contém certificados de AC confiáveis usados para verificar o servidor quando você se conecta via SSL. Essa propriedade só pode ser definida quando você usa SSL em Integration Runtime hospedados internamente. O valor padrão é o arquivo cacerts. pem instalado com o Integration Runtime.  | Não |
| useSystemTrustStore | Especifica se um certificado de autoridade de certificação deve ser usado do repositório de confiança do sistema ou de um arquivo PEM especificado. O valor padrão é **false**.  | Não |
| allowHostNameCNMismatch | Especifica se deve ser necessário um nome de certificado SSL emitido pela autoridade de certificação para corresponder ao nome de host do servidor quando você se conectar por SSL. O valor padrão é **false**.  | Não |
| allowSelfSignedServerCert | Especifica se os certificados autoassinados devem ser permitidos do servidor. O valor padrão é **false**.  | Não |
| connectVia | O [Integration Runtime](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. Saiba mais na seção de [pré-requisitos](#prerequisites) . Se não for especificado, ele usará o Azure Integration Runtime padrão. |Não |

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

## <a name="dataset-properties"></a>Propriedades de DataSet

Para obter uma lista completa das seções e propriedades disponíveis para definir conjuntos de os, consulte o artigo [conjuntos de valores](concepts-datasets-linked-services.md) . Esta seção fornece uma lista das propriedades com suporte pelo conjunto de Impala.

Para copiar dados do Impala, defina a propriedade Type do conjunto de dado como **apacheimpalaobject**. As propriedades a seguir têm suporte:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade Type do conjunto de conjuntos deve ser definida como: **apacheimpalaobject** | Sim |
| Esquema | Nome do esquema. |Não (se "Query" na origem da atividade for especificada)  |
| Tabela | Nome da tabela. |Não (se "Query" na origem da atividade for especificada)  |
| tableName | Nome da tabela com esquema. Essa propriedade tem suporte para compatibilidade com versões anteriores. Use `schema` e `table` para uma nova carga de trabalho. | Não (se "Query" na origem da atividade for especificada) |

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

Para obter uma lista completa de seções e propriedades disponíveis para definir atividades, consulte o artigo [pipelines](concepts-pipelines-activities.md) . Esta seção fornece uma lista das propriedades com suporte pelo tipo de origem Impala.

### <a name="impala-as-a-source-type"></a>Impala como um tipo de origem

Para copiar dados do Impala, defina o tipo de origem na atividade de cópia como **ImpalaSource**. As propriedades a seguir têm suporte na seção **origem** da atividade de cópia.

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade Type da fonte da atividade de cópia deve ser definida como **ImpalaSource**. | Sim |
| consulta | Use a consulta SQL personalizada para ler os dados. Um exemplo é `"SELECT * FROM MyTable"`. | Não (se "TableName" no DataSet for especificado) |

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

## <a name="lookup-activity-properties"></a>Propriedades da atividade de pesquisa

Para obter detalhes sobre as propriedades, verifique a [atividade de pesquisa](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Passos seguintes
Para obter uma lista de armazenamentos de dados com suporte como fontes e coletores pela atividade de cópia no Data Factory, consulte [armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).
