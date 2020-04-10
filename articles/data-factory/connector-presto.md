---
title: Copiar dados de Presto utilizando a Fábrica de Dados Azure (Pré-visualização)
description: Saiba como copiar dados de Presto para lojas de dados de sink suportadas utilizando uma atividade de cópia num pipeline azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: jingwang
ms.openlocfilehash: 8364468277123205d967871ab7bf2d048db64a82
ms.sourcegitcommit: a53fe6e9e4a4c153e9ac1a93e9335f8cf762c604
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/09/2020
ms.locfileid: "80991795"
---
# <a name="copy-data-from-presto-using-azure-data-factory-preview"></a>Copiar dados de Presto utilizando a Fábrica de Dados Azure (Pré-visualização)

Este artigo descreve como usar a Atividade de Cópia na Fábrica de Dados Azure para copiar dados de Presto. Baseia-se no artigo de visão geral da [atividade de cópia](copy-activity-overview.md) que apresenta uma visão geral da atividade de cópia.

> [!IMPORTANT]
> Este conector encontra-se atualmente em pré-visualização. Pode sacá-lo e dar-nos feedback. Se quiser realizar uma dependência em conectores de pré-visualização na sua solução, contacte o [Suporte do Azure](https://azure.microsoft.com/support/).

## <a name="supported-capabilities"></a>Capacidades suportadas

Este conector Presto é suportado para as seguintes atividades:

- [Copiar atividade](copy-activity-overview.md) com matriz de [origem/pia suportada](copy-activity-overview.md)
- [Atividade de procura](control-flow-lookup-activity.md)

Pode copiar dados do Presto para qualquer loja de dados suportada. Para obter uma lista de lojas de dados que são suportadas como fontes/pias pela atividade de cópia, consulte a tabela de lojas de [dados suportadas.](copy-activity-overview.md#supported-data-stores-and-formats)

A Azure Data Factory fornece um controlador incorporado para permitir a conectividade, pelo que não necessita de instalar manualmente qualquer controlador utilizando este conector.

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seguintes secções fornecem detalhes sobre propriedades que são usadas para definir entidades da Fábrica de Dados específicas do conector Presto.

## <a name="linked-service-properties"></a>Propriedades de serviço seletos

As seguintes propriedades são suportadas para o serviço ligado presto:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade tipo deve ser definida para: **Presto** | Sim |
| anfitrião | O endereço IP ou o nome do anfitrião do servidor Presto. (ou assim, 192.168.222.160)  | Sim |
| servidorVersão | A versão do servidor Presto. (ou é, 0.148-t)  | Sim |
| catálogo | O contexto do catálogo para todos os pedidos contra o servidor.  | Sim |
| porta | A porta TCP que o servidor Presto utiliza para ouvir as ligações do cliente. O valor padrão é 8080.  | Não |
| authenticationType | O mecanismo de autenticação utilizado para ligar ao servidor Presto. <br/>Os valores permitidos são: **Anónimo,** **LDAP** | Sim |
| o nome de utilizador | O nome de utilizador usado para ligar ao servidor Presto.  | Não |
| palavra-passe | A palavra-passe correspondente ao nome de utilizador. Marque este campo como um SecureString para o armazenar de forma segura na Data Factory, ou [refira um segredo armazenado no Cofre de Chaves Azure](store-credentials-in-key-vault.md). | Não |
| enableSsl | Especifica se as ligações ao servidor estão encriptadas utilizando TLS. O valor predefinido é false.  | Não |
| trustedCertPath | O caminho completo do ficheiro .pem contendo certificados CA fidedignos para verificar o servidor ao ligar-se através de TLS. Esta propriedade só pode ser definida quando se utiliza TLS em IR auto-hospedado. O valor predefinido é o ficheiro cacerts.pem instalado com o IR.  | Não |
| useSystemTrustStore | Especifica se deve utilizar um certificado CA a partir da loja fiduciário do sistema ou de um ficheiro PEM especificado. O valor predefinido é false.  | Não |
| permitirHostNameCNMismatch | Especifica se deve exigir um nome de certificado TLS/SSL emitido pela CA para corresponder ao nome de anfitrião do servidor ao ligar-se ao TLS. O valor predefinido é false.  | Não |
| permitir AutoSignedServerCert | Especifica se permite certificados auto-assinados a partir do servidor. O valor predefinido é false.  | Não |
| timeZoneID | O fuso horário local utilizado pela ligação. Valores válidos para esta opção são especificados na Base de Dados do Fuso Horário IANA. O valor padrão é o fuso horário do sistema.  | Não |

**Exemplo:**

```json
{
    "name": "PrestoLinkedService",
    "properties": {
        "type": "Presto",
        "typeProperties": {
            "host" : "<host>",
            "serverVersion" : "0.148-t",
            "catalog" : "<catalog>",
            "port" : "<port>",
            "authenticationType" : "LDAP",
            "username" : "<username>",
            "password": {
                 "type": "SecureString",
                 "value": "<password>"
            },
            "timeZoneID" : "Europe/Berlin"
        }
    }
}
```

## <a name="dataset-properties"></a>Dataset properties (Propriedades do conjunto de dados)

Para obter uma lista completa de secções e propriedades disponíveis para definir conjuntos de dados, consulte o artigo conjuntos de [dados.](concepts-datasets-linked-services.md) Esta secção fornece uma lista de propriedades suportadas pelo conjunto de dados presto.

Para copiar dados de Presto, detete a propriedade do tipo do conjunto de dados para **PrestoObject**. As seguintes propriedades são suportadas:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade tipo do conjunto de dados deve ser definida para: **PrestoObject** | Sim |
| schema | Nome do esquema. |Não (se for especificada a "consulta" na fonte de atividade)  |
| tabela | Nome da mesa. |Não (se for especificada a "consulta" na fonte de atividade)  |
| tableName | Nome da mesa com esquema. Esta propriedade é suportada para retrocompatibilidade. Uso `schema` `table` e para nova carga de trabalho. | Não (se for especificada a "consulta" na fonte de atividade) |

**Exemplo**

```json
{
    "name": "PrestoDataset",
    "properties": {
        "type": "PrestoObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Presto linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy

Para obter uma lista completa de secções e imóveis disponíveis para definir atividades, consulte o artigo [Pipelines.](concepts-pipelines-activities.md) Esta secção fornece uma lista de propriedades suportadas pela fonte presto.

### <a name="presto-as-source"></a>Presto como fonte

Para copiar dados de Presto, delineie o tipo de origem na atividade de cópia para **PrestoSource**. As seguintes propriedades são suportadas na secção de **origem** da atividade de cópia:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade do tipo da fonte de atividade de cópia deve ser definida para: **PrestoSource** | Sim |
| consulta | Utilize a consulta SQL personalizada para ler dados. Por exemplo: `"SELECT * FROM MyTable"`. | Não (se for especificado "tableName" no conjunto de dados) |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromPresto",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Presto input dataset name>",
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
                "type": "PrestoSource",
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
