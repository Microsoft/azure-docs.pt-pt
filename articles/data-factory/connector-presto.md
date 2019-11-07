---
title: Copiar dados do presto usando o Azure Data Factory (versão prévia)
description: Saiba como copiar dados do presto para armazenamentos de dados de coletor com suporte usando uma atividade de cópia em um pipeline de Azure Data Factory.
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
ms.openlocfilehash: aeb1e8b2bd4491a8124d95907308f014b7a36656
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73680389"
---
# <a name="copy-data-from-presto-using-azure-data-factory-preview"></a>Copiar dados do presto usando o Azure Data Factory (versão prévia)

Este artigo descreve como usar a atividade de cópia em Azure Data Factory para copiar dados do presto. Ele se baseia no artigo [visão geral da atividade de cópia](copy-activity-overview.md) que apresenta uma visão geral da atividade de cópia.

> [!IMPORTANT]
> Este conector está atualmente em visualização. Você pode experimentá-lo e nos enviar comentários. Se quiser realizar uma dependência em conectores de pré-visualização na sua solução, contacte o [Suporte do Azure](https://azure.microsoft.com/support/).

## <a name="supported-capabilities"></a>Recursos com suporte

Este conector do presto tem suporte para as seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com [matriz de coletor/origem com suporte](copy-activity-overview.md)
- [Atividade de Pesquisa](control-flow-lookup-activity.md)

Você pode copiar dados do presto para qualquer armazenamento de dados de coletor com suporte. Para obter uma lista de armazenamentos de dados com suporte como fontes/coletores pela atividade de cópia, consulte a tabela [armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats) .

O Azure Data Factory fornece um driver interno para habilitar a conectividade, portanto, você não precisa instalar manualmente nenhum driver usando esse conector.

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções a seguir fornecem detalhes sobre as propriedades que são usadas para definir Data Factory entidades específicas ao conector do presto.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

As propriedades a seguir têm suporte para o serviço vinculado do presto:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade Type deve ser definida como: **presto** | Sim |
| hospedeira | O endereço IP ou nome do host do servidor presto. (ou seja, 192.168.222.160)  | Sim |
| serverVersion | A versão do servidor presto. (ou seja, 0,148-t)  | Sim |
| Catalog | O contexto do catálogo para todas as solicitações no servidor.  | Sim |
| porta | A porta TCP que o servidor presto usa para escutar conexões de cliente. O valor padrão é 8080.  | Não |
| authenticationType | O mecanismo de autenticação usado para se conectar ao servidor presto. <br/>Os valores permitidos são: **Anonymous**, **LDAP** | Sim |
| o nome de utilizador | O nome de usuário usado para se conectar ao servidor presto.  | Não |
| palavra-passe | A senha correspondente ao nome de usuário. Marque este campo como uma SecureString para armazená-lo com segurança no Data Factory ou [faça referência a um segredo armazenado em Azure Key Vault](store-credentials-in-key-vault.md). | Não |
| enableSsl | Especifica se as conexões com o servidor são criptografadas usando SSL. O valor padrão é false.  | Não |
| trustedCertPath | O caminho completo do arquivo. pem que contém certificados de AC confiáveis para verificar o servidor ao se conectar via SSL. Essa propriedade só pode ser definida ao usar SSL no IR auto-hospedado. O valor padrão é o arquivo cacerts. pem instalado com o IR.  | Não |
| useSystemTrustStore | Especifica se um certificado de autoridade de certificação deve ser usado do repositório de confiança do sistema ou de um arquivo PEM especificado. O valor padrão é false.  | Não |
| allowHostNameCNMismatch | Especifica se deve ser necessário um nome de certificado SSL emitido pela autoridade de certificação para corresponder ao nome de host do servidor ao se conectar via SSL. O valor padrão é false.  | Não |
| allowSelfSignedServerCert | Especifica se os certificados autoassinados devem ser permitidos do servidor. O valor padrão é false.  | Não |
| timefusoid | O fuso horário local usado pela conexão. Os valores válidos para essa opção são especificados no banco de dados de fuso horário IANA. O valor padrão é o fuso horário do sistema.  | Não |

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

## <a name="dataset-properties"></a>Propriedades de DataSet

Para obter uma lista completa das seções e propriedades disponíveis para definir conjuntos de os, consulte o artigo [conjuntos de valores](concepts-datasets-linked-services.md) . Esta seção fornece uma lista de propriedades suportadas pelo conjunto de dados Presto.

Para copiar dados do presto, defina a propriedade Type do conjunto de dado como **PrestoObject**. As propriedades a seguir têm suporte:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade Type do conjunto de conjuntos deve ser definida como: **PrestoObject** | Sim |
| schema | Nome do esquema. |Não (se for especificada "query" na origem de atividade)  |
| tabela | Nome da tabela. |Não (se for especificada "query" na origem de atividade)  |
| tableName | Nome da tabela com esquema. Essa propriedade tem suporte para compatibilidade com versões anteriores. Use `schema` e `table` para uma nova carga de trabalho. | Não (se for especificada "query" na origem de atividade) |

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

Para obter uma lista completa de seções e propriedades disponíveis para definir atividades, consulte o artigo [pipelines](concepts-pipelines-activities.md) . Esta seção fornece uma lista das propriedades com suporte pela origem do presto.

### <a name="presto-as-source"></a>Presto como fonte

Para copiar dados do presto, defina o tipo de origem na atividade de cópia como **PrestoSource**. As propriedades a seguir têm suporte na seção **origem** da atividade de cópia:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade Type da fonte da atividade de cópia deve ser definida como: **PrestoSource** | Sim |
| consulta | Use a consulta SQL personalizada para ler os dados. Por exemplo: `"SELECT * FROM MyTable"`. | Não (se "TableName" no DataSet for especificado) |

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

## <a name="lookup-activity-properties"></a>Propriedades da atividade de pesquisa

Para obter detalhes sobre as propriedades, verifique a [atividade de pesquisa](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Passos seguintes
Para obter uma lista de armazenamentos de dados com suporte como fontes e coletores pela atividade de cópia no Azure Data Factory, consulte [armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).
