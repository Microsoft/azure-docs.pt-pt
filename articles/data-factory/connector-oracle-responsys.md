---
title: Copiar dados da Oracle Responsys (Pré-visualização)
description: Saiba como copiar dados da Oracle Responsys para lojas de dados de sink suportadas utilizando uma atividade de cópia num pipeline azure Data Factory.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/01/2019
ms.openlocfilehash: cde0e2d118490801aeee3874e220d298fea24598
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416843"
---
# <a name="copy-data-from-oracle-responsys-using-azure-data-factory-preview"></a>Copiar dados da Oracle Responsys utilizando a Azure Data Factory (Pré-visualização)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve como usar a Atividade de Cópia na Fábrica de Dados Azure para copiar dados da Oracle Responsys. Baseia-se no artigo de visão geral da [atividade de cópia](copy-activity-overview.md) que apresenta uma visão geral da atividade de cópia.

> [!IMPORTANT]
> Este conector encontra-se atualmente em pré-visualização. Pode sacá-lo e dar-nos feedback. Se quiser realizar uma dependência em conectores de pré-visualização na sua solução, contacte o [Suporte do Azure](https://azure.microsoft.com/support/).

## <a name="supported-capabilities"></a>Capacidades suportadas

Este conector Oracle Responsys é suportado para as seguintes atividades:

- [Copiar atividade](copy-activity-overview.md) com matriz de [origem/pia suportada](copy-activity-overview.md)
- [Atividade de procura](control-flow-lookup-activity.md)

Pode copiar dados da Oracle Responsys para qualquer loja de dados suportada. Para obter uma lista de lojas de dados que são suportadas como fontes/pias pela atividade de cópia, consulte a tabela de lojas de [dados suportadas.](copy-activity-overview.md#supported-data-stores-and-formats)

A Azure Data Factory fornece um controlador incorporado para permitir a conectividade, pelo que não necessita de instalar manualmente qualquer controlador utilizando este conector.

## <a name="getting-started"></a>Introdução

Pode criar um pipeline com a atividade de cópia utilizando o modelo .NET SDK, Python SDK, Azure PowerShell, REST API ou Azure Resource Manager. Consulte o tutorial de [atividade de cópia](quickstart-create-data-factory-dot-net.md) para obter instruções passo a passo para criar um pipeline com uma atividade de cópia.

As seguintes secções fornecem detalhes sobre propriedades que são usadas para definir entidades da Fábrica de Dados específicas do conector Oracle Responsys.

## <a name="linked-service-properties"></a>Propriedades de serviço seletos

As seguintes propriedades são suportadas para o serviço ligado à Oracle Responsys:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade tipo deve ser definida para: **Responsys** | Sim |
| endpoint | O ponto final do servidor Respopnsys  | Sim |
| clientId | O ID do cliente associado à aplicação Responsys.  | Sim |
| clienteSecret | O segredo do cliente associado à aplicação Responsys. Pode optar por marcar este campo como um SecureString para o armazenar de forma segura em ADF, ou armazenar senha no Cofre de Chaves Azure e deixar a atividade de cópia da ADF puxar a partir daí ao executar cópia de dados - saiba mais com [as credenciais da Loja no Cofre chave](store-credentials-in-key-vault.md). | Sim |
| useEncryptedEndpoints | Especifica se os pontos finais da fonte de dados são encriptados usando HTTPS. O valor predefinido é true.  | Não |
| utilizaçãoVerificação de anfitriões | Especifica se deve exigir o nome de anfitrião no certificado do servidor para corresponder ao nome de anfitrião do servidor ao ligar-se ao TLS. O valor predefinido é true.  | Não |
| utilizaçãoPeerVerification | Especifica se deve verificar a identidade do servidor ao ligar-se através do TLS. O valor predefinido é true.  | Não |

**Exemplo:**

```json
{
    "name": "OracleResponsysLinkedService",
    "properties": {
        "type": "Responsys",
        "typeProperties": {
            "endpoint" : "<endpoint>",
            "clientId" : "<clientId>",
            "clientSecret": {
                 "type": "SecureString",
                 "value": "<clientSecret>"
            },
            "useEncryptedEndpoints" : true,
            "useHostVerification" : true,
            "usePeerVerification" : true
        }
    }
}

```

## <a name="dataset-properties"></a>Dataset properties (Propriedades do conjunto de dados)

Para obter uma lista completa de secções e propriedades disponíveis para definir conjuntos de dados, consulte o artigo conjuntos de [dados.](concepts-datasets-linked-services.md) Esta secção fornece uma lista de propriedades suportadas pelo conjunto de dados Da Oracle Responsys.

Para copiar dados da Oracle Responsys, detete a propriedade tipo do conjunto de dados para **ResponsysObject**. As seguintes propriedades são suportadas:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade tipo do conjunto de dados deve ser definida para: **ResponsysObject** | Sim |
| tableName | Nome da mesa. | Não (se for especificada a "consulta" na fonte de atividade) |

**Exemplo**

```json
{
    "name": "OracleResponsysDataset",
    "properties": {
        "type": "ResponsysObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Oracle Responsys linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}

```

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy

Para obter uma lista completa de secções e imóveis disponíveis para definir atividades, consulte o artigo [Pipelines.](concepts-pipelines-activities.md) Esta secção fornece uma lista de propriedades suportadas pela fonte da Oracle Responsys.

### <a name="oracle-responsys-as-source"></a>Oráculo Responsys como fonte

Para copiar dados da Oracle Responsys, delineie o tipo de origem na atividade de cópia para **ResponsysSource**. As seguintes propriedades são suportadas na secção de **origem** da atividade de cópia:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade tipo da fonte de atividade de cópia deve ser definida para: **ResponsysSource** | Sim |
| consulta | Utilize a consulta SQL personalizada para ler dados. Por exemplo: `"SELECT * FROM MyTable"`. | Não (se for especificado "tableName" no conjunto de dados) |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromOracleResponsys",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Oracle Responsys input dataset name>",
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
                "type": "ResponsysSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="lookup-activity-properties"></a>Propriedades de atividade de procura

Para saber mais detalhes sobre as propriedades, consulte a [atividade de Lookup.](control-flow-lookup-activity.md)


## <a name="next-steps"></a>Passos seguintes
Para obter uma lista de lojas de dados suportadas como fontes e pias pela atividade de cópia na Azure Data Factory, consulte as lojas de [dados suportadas](copy-activity-overview.md#supported-data-stores-and-formats).
