---
title: Copiar dados de Presto usando Azure Data Factory (Pré-visualização)
description: Saiba como copiar dados da Presto para lojas de dados de sumidouros suportados utilizando uma atividade de cópia num oleoduto Azure Data Factory.
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
ms.openlocfilehash: 261bdedee56bb4de2dfbbef27358fae5ae8fdc3e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "81416732"
---
# <a name="copy-data-from-presto-using-azure-data-factory-preview"></a>Copiar dados de Presto usando Azure Data Factory (Pré-visualização)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve como utilizar a Atividade de Cópia na Fábrica de Dados Azure para copiar dados de Presto. Baseia-se no artigo [de visão geral](copy-activity-overview.md) da atividade de cópia que apresenta uma visão geral da atividade da cópia.

> [!IMPORTANT]
> Este conector encontra-se atualmente em pré-visualização. Pode experimentar e dar-nos feedback. Se quiser realizar uma dependência em conectores de pré-visualização na sua solução, contacte o [Suporte do Azure](https://azure.microsoft.com/support/).

## <a name="supported-capabilities"></a>Capacidades suportadas

Este conector Presto é suportado para as seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com [matriz de fonte/pia suportada](copy-activity-overview.md)
- [Atividade de procura](control-flow-lookup-activity.md)

Pode copiar dados do Presto para qualquer loja de dados de lavatórios suportados. Para obter uma lista de lojas de dados suportadas como fontes/pias pela atividade de cópia, consulte a tabela [de lojas de dados suportadas.](copy-activity-overview.md#supported-data-stores-and-formats)

A Azure Data Factory fornece um controlador incorporado para permitir a conectividade, pelo que não é necessário instalar manualmente qualquer controlador utilizando este conector.

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As secções seguintes fornecem detalhes sobre propriedades que são usadas para definir entidades da Data Factory específicas do conector Presto.

## <a name="linked-service-properties"></a>Propriedades de serviço ligadas

As seguintes propriedades são suportadas para o serviço relacionado presto:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade tipo deve ser definida para: **Presto** | Yes |
| anfitrião | O endereço IP ou o nome de anfitrião do servidor Presto. (ou seja, 192.168.222.160)  | Yes |
| servidorVer | A versão do servidor Presto. (ou seja, 0.148-t)  | Yes |
| catálogo | O contexto do catálogo para todos os pedidos contra o servidor.  | Yes |
| porta | A porta TCP que o servidor Presto utiliza para ouvir as ligações do cliente. O valor predefinido é 8080.  | No |
| authenticationType | O mecanismo de autenticação utilizado para ligar ao servidor Presto. <br/>Os valores permitidos são: **Anónimo,** **LDAP** | Yes |
| o nome de utilizador | O nome de utilizador usado para ligar ao servidor Presto.  | No |
| palavra-passe | A palavra-passe correspondente ao nome de utilizador. Marque este campo como um SecureString para armazená-lo de forma segura na Data Factory, ou [fazer referência a um segredo armazenado no Cofre da Chave Azure](store-credentials-in-key-vault.md). | No |
| ativarSl | Especifica se as ligações ao servidor são encriptadas utilizando O S.TLS. O valor predefinido é false.  | No |
| trustedCertPath | O percurso completo do ficheiro .pem que contém certificados ca fidedignos para verificar o servidor ao ligar através de TLS. Esta propriedade só pode ser definida quando se utiliza TLS em IR auto-hospedado. O valor predefinido é o ficheiro cacerts.pem instalado com o IR.  | No |
| useSystemTrustStore | Especifica se deve utilizar um certificado de CA da loja de fidedignidade do sistema ou de um ficheiro PEM especificado. O valor predefinido é false.  | No |
| permitirHostNameCNMismatch | Especifica se deve exigir um nome de certificado TLS/SSL emitido pela CA para corresponder ao nome de anfitrião do servidor ao ligar o TLS. O valor predefinido é false.  | No |
| permitirSelfSignedServerCert | Especifica se permite certificados auto-assinados a partir do servidor. O valor predefinido é false.  | No |
| timeZoneID | O fuso horário local usado pela ligação. Os valores válidos para esta opção estão especificados na Base de Dados do Fuso Horário IANA. O valor predefinido é o fuso horário do sistema.  | No |

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

Para obter uma lista completa de secções e propriedades disponíveis para definir conjuntos de dados, consulte o artigo [conjuntos de dados.](concepts-datasets-linked-services.md) Esta secção fornece uma lista de propriedades suportadas pelo Conjunto de Dados Presto.

Para copiar dados da Presto, defina a propriedade tipo do conjunto de dados para **o PrestoObject**. As seguintes propriedades são suportadas:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade tipo do conjunto de dados deve ser definida para: **PrestoObject** | Yes |
| esquema | O nome do esquema. |Não (se for especificada "consulta" na fonte de atividade)  |
| table | O nome da mesa. |Não (se for especificada "consulta" na fonte de atividade)  |
| tableName | Nome da mesa com esquema. Esta propriedade é suportada para retrocompatibilidade. Uso `schema` e para nova carga de `table` trabalho. | Não (se for especificada "consulta" na fonte de atividade) |

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

Para obter uma lista completa de secções e propriedades disponíveis para definir atividades, consulte o artigo [Pipelines.](concepts-pipelines-activities.md) Esta secção fornece uma lista de propriedades suportadas por fonte presto.

### <a name="presto-as-source"></a>Presto como fonte

Para copiar dados da Presto, desagrafe o tipo de origem na atividade de cópia para **o PrestoSource**. As seguintes propriedades são suportadas na secção fonte de **origem** da atividade de cópia:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade tipo da fonte de atividade de cópia deve ser definida para: **PrestoSource** | Yes |
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

Para obter detalhes sobre as propriedades, consulte [a atividade de Lookup](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Passos seguintes
Para obter uma lista de lojas de dados suportadas como fontes e sumidouros pela atividade de cópia na Azure Data Factory, consulte lojas de [dados suportadas.](copy-activity-overview.md#supported-data-stores-and-formats)
