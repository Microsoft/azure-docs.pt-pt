---
title: Copiar dados de e para o Dynamics CRM ou do Dynamics 365 (Common Data Service) com o Azure Data Factory | Documentos da Microsoft
description: Saiba como copiar dados do Microsoft Dynamics CRM ou Microsoft Dynamics 365 (Common Data Service) suportado arquivos de dados de sink, ou de suportado armazenamentos de dados de origem para o Dynamics CRM ou do Dynamics 365, utilizando uma atividade de cópia num pipeline de fábrica de dados.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: jingwang
ms.openlocfilehash: 481b19d0121e93c84d123579e91bcbfb9fb50815
ms.sourcegitcommit: 8e76be591034b618f5c11f4e66668f48c090ddfd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/29/2019
ms.locfileid: "66356964"
---
# <a name="copy-data-from-and-to-dynamics-365-common-data-service-or-dynamics-crm-by-using-azure-data-factory"></a>Copiar dados de e para o Dynamics 365 (Common Data Service) ou o Dynamics CRM com o Azure Data Factory

Este artigo descreve como utilizar a atividade de cópia no Azure Data Factory para copiar dados de e para o Microsoft Dynamics 365 ou do Microsoft Dynamics CRM. Ele se baseia no [descrição geral da atividade de cópia](copy-activity-overview.md) artigo apresenta uma visão geral da atividade de cópia.

## <a name="supported-capabilities"></a>Capacidades suportadas

Pode copiar dados a partir do Dynamics 365 (Common Data Service) ou o Dynamics CRM para qualquer arquivo de dados de sink suportados. Também pode copiar dados de qualquer arquivo de dados de origem suportada para o Dynamics 365 (Common Data Service) ou o Dynamics CRM. Para obter uma lista dos arquivos de dados suportados como origens ou sinks a atividade de cópia, consulte a [arquivos de dados suportados](copy-activity-overview.md#supported-data-stores-and-formats) tabela.

Este conector Dynamics suporta as seguintes versões do Dynamics e tipos de autenticação. (IFD é a abreviação de implantação de acesso à internet.)

| Versões do Dynamics | Tipos de autenticação | Exemplos de serviço ligado |
|:--- |:--- |:--- |
| Dynamics 365 online <br> Dynamics CRM Online | Office365 | [Dynamics online + autenticação do Office 365](#dynamics-365-and-dynamics-crm-online) |
| Dynamics 365 no local com IFD <br> Dynamics CRM 2016 no local com IFD <br> Dynamics CRM 2015 no local com IFD | IFD | [Dynamics no local com IFD + IFD auth](#dynamics-365-and-dynamics-crm-on-premises-with-ifd) |

Especificamente, para o Dynamics 365 são suportados os seguintes tipos de aplicação:

- Dynamics 365 para vendas
- Dynamics 365 para suporte ao cliente
- Dynamics 365 for Field Service
- Dynamics 365 para automatização de serviço do projeto
- Dynamics 365 para Marketing

Outros tipos de aplicação por exemplo, Finanças e operações, talento, etc. não são suportadas por este conector.

>[!TIP]
>Para copiar dados a partir **Dynamics 365 Finanças e operações**, pode utilizar os [conector do Dynamics AX](connector-dynamics-ax.md).

## <a name="get-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As secções seguintes fornecem detalhes sobre as propriedades que são utilizadas para definir entidades do Data Factory específicas ao Dynamics.

## <a name="linked-service-properties"></a>Propriedades do serviço ligado

As seguintes propriedades são suportadas para o serviço ligado do Dynamics.

### <a name="dynamics-365-and-dynamics-crm-online"></a>Dynamics 365 e Dynamics CRM Online

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | A propriedade de tipo deve ser definida como **Dynamics**. | Sim |
| deploymentType | O tipo de implementação da instância do Dynamics. Tem de ser **"Online"** do Dynamics online. | Sim |
| serviceUri | Por exemplo, o URL do serviço de seu Dynamics instância `https://adfdynamics.crm.dynamics.com`. | Sim |
| authenticationType | O tipo de autenticação para ligar a um servidor do Dynamics. Especifique **"Office 365"** do Dynamics online. | Sim |
| username | Especifique o nome de utilizador para ligar ao Dynamics. | Sim |
| password | Especifique a palavra-passe da conta de utilizador que especificou para o nome de utilizador. Marcar esse campo como uma SecureString armazena de forma segura na fábrica de dados, ou [referenciar um segredo armazenado no Azure Key Vault](store-credentials-in-key-vault.md). | Sim |
| connectVia | O [runtime de integração](concepts-integration-runtime.md) a ser utilizado para ligar ao arquivo de dados. Se não for especificado, ele usa o padrão do Runtime de integração do Azure. | Não para a origem, Sim para sink se associada a origem de serviço não tem um runtime de integração |

>[!NOTE]
>O conector do Dynamics utilizado para utilizar a propriedade de "organizationName" opcional para identificar a instância do Dynamics CRM/365 Online. Enquanto mantém trabalhar, são sugeridas para especificar a nova propriedade de "serviceUri" em vez disso, para obter um melhor desempenho por exemplo deteção.

**Exemplo: Dynamics online através da autenticação do Office 365**

```json
{
    "name": "DynamicsLinkedService",
    "properties": {
        "type": "Dynamics",
        "description": "Dynamics online linked service using Office365 authentication",
        "typeProperties": {
            "deploymentType": "Online",
            "serviceUri": "https://adfdynamics.crm.dynamics.com",
            "authenticationType": "Office365",
            "username": "test@contoso.onmicrosoft.com",
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

### <a name="dynamics-365-and-dynamics-crm-on-premises-with-ifd"></a>Dynamics 365 e Dynamics CRM no local com IFD

*Propriedades adicionais que comparam ao Dynamics online são "nome de anfitrião" e "porta".*

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | A propriedade de tipo deve ser definida como **Dynamics**. | Sim |
| deploymentType | O tipo de implementação da instância do Dynamics. Tem de ser **"OnPremisesWithIfd"** para Dynamics no local com IFD.| Sim |
| hostName | O nome de anfitrião do servidor de Dynamics no local. | Sim |
| port | A porta do servidor de Dynamics no local. | Não, a predefinição é 443 |
| organizationName | O nome da organização da instância do Dynamics. | Sim |
| authenticationType | O tipo de autenticação para ligar ao servidor do Dynamics. Especifique **"Ifd"** para Dynamics no local com IFD. | Sim |
| username | Especifique o nome de utilizador para ligar ao Dynamics. | Sim |
| password | Especifique a palavra-passe da conta de utilizador que especificou para o nome de utilizador. Pode escolher marcar este campo como uma SecureString armazena de forma segura no ADF ou armazenar a palavra-passe no Azure Key Vault e permitir que a atividade de cópia pull a partir daí, quando efetuar a cópia de dados - Saiba mais a partir da [Store credenciais no Key Vault](store-credentials-in-key-vault.md). | Sim |
| connectVia | O [runtime de integração](concepts-integration-runtime.md) a ser utilizado para ligar ao arquivo de dados. Se não for especificado, ele usa o padrão do Runtime de integração do Azure. | Não para a origem, Sim para o sink |

**Exemplo: Dynamics no local com IFD utilizando a autenticação de IFD**

```json
{
    "name": "DynamicsLinkedService",
    "properties": {
        "type": "Dynamics",
        "description": "Dynamics on-premises with IFD linked service using IFD authentication",
        "typeProperties": {
            "deploymentType": "OnPremisesWithIFD",
            "hostName": "contosodynamicsserver.contoso.com",
            "port": 443,
            "organizationName": "admsDynamicsTest",
            "authenticationType": "Ifd",
            "username": "test@contoso.onmicrosoft.com",
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

Para obter uma lista completa das secções e propriedades disponíveis para definir conjuntos de dados, consulte a [conjuntos de dados](concepts-datasets-linked-services.md) artigo. Esta seção fornece uma lista de propriedades suportadas pelo conjunto de dados do Dynamics.

Para copiar dados de e para o Dynamics, defina a propriedade de tipo de conjunto de dados para **DynamicsEntity**. São suportadas as seguintes propriedades.

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | A propriedade de tipo do conjunto de dados tem de ser definida **DynamicsEntity**. |Sim |
| entityName | O nome lógico da entidade para recuperar. | Não para a origem (se não for especificada "consulta" na origem de atividade), Sim para o sink |

> [!IMPORTANT]
>- Quando copiar dados do Dynamics, a seção de "estrutura" é opcional mas altamente recommanded do conjunto de dados do Dynamics para garantir um resultado de cópia determinística. Define o tipo de dados e de nome de coluna para dados do Dynamics que pretende copiar pela. Para obter mais informações, consulte [estrutura do conjunto de dados](concepts-datasets-linked-services.md#dataset-structure-or-schema) e [mapeamento de tipo de dados para o Dynamics](#data-type-mapping-for-dynamics).
>- Ao importar o esquema na criação da interface do Usuário, o ADF inferir o esquema, as primeiras linhas do resultado de consulta do Dynamics para inicializar a construção de estrutura, em que irão ser omitidas casos colunas com nenhum valor de amostragem. O mesmo comportamento aplica-se para copiar as execuções, se não existir nenhuma definição de estrutura explícita. Pode rever e adicionar mais colunas para a Dynamics esquema/estrutura de dataset conforme necessário, que serão cumpridas durante o tempo de execução de cópia.
>- Quando copiar dados para o Dynamics, a seção de "estrutura" é opcional no conjunto de dados do Dynamics. As colunas que pretende copiar para é determinado pelo esquema de dados de origem. Se a origem é um ficheiro CSV sem cabeçalho, o conjunto de dados de entrada, especifique a "estrutura" com o tipo de dados e de nome de coluna. Podem ser mapeados para os campos no ficheiro CSV individualmente por ordem.

**Exemplo:**

```json
{
    "name": "DynamicsDataset",
    "properties": {
        "type": "DynamicsEntity",
        "structure": [
            {
                "name": "accountid",
                "type": "Guid"
            },
            {
                "name": "name",
                "type": "String"
            },
            {
                "name": "marketingonly",
                "type": "Boolean"
            },
            {
                "name": "modifiedon",
                "type": "Datetime"
            }
        ],
        "typeProperties": {
            "entityName": "account"
        },
        "linkedServiceName": {
            "referenceName": "<Dynamics linked service name>",
            "type": "linkedservicereference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy

Para obter uma lista completa das secções e propriedades disponíveis para a definição de atividades, consulte a [Pipelines](concepts-pipelines-activities.md) artigo. Esta seção fornece uma lista de propriedades suportadas por tipos de origem e sink do Dynamics.

### <a name="dynamics-as-a-source-type"></a>Dynamics como um tipo de origem

Para copiar dados do Dynamics, defina o tipo de origem na atividade de cópia para **DynamicsSource**. As seguintes propriedades são suportadas na atividade de cópia **origem** secção.

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | A propriedade de tipo de origem de atividade de cópia tem de ser definida **DynamicsSource**. | Sim |
| query | FetchXML é uma linguagem de consulta de proprietários que é utilizada no Dynamics (online e no local). Veja o seguinte exemplo. Para obter mais informações, consulte [criar consultas com o FetchXML](https://msdn.microsoft.com/library/gg328332.aspx). | Não (se for especificado "entityName" no conjunto de dados) |

>[!NOTE]
>A coluna de PK sempre será copiada mesmo que não contém a projeção de coluna que configurou a consultas de FetchXML-lo.

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromDynamics",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Dynamics input dataset>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "DynamicsSource",
                "query": "<FetchXML Query>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="sample-fetchxml-query"></a>Consultas de FetchXML de exemplo

```xml
<fetch>
  <entity name="account">
    <attribute name="accountid" />
    <attribute name="name" />
    <attribute name="marketingonly" />
    <attribute name="modifiedon" />
    <order attribute="modifiedon" descending="false" />
    <filter type="and">
      <condition attribute ="modifiedon" operator="between">
        <value>2017-03-10 18:40:00z</value>
        <value>2017-03-12 20:40:00z</value>
      </condition>
    </filter>
  </entity>
</fetch>
```

### <a name="dynamics-as-a-sink-type"></a>Dynamics como um tipo de sink

Para copiar dados para o Dynamics, defina o tipo de sink na atividade de cópia para **DynamicsSink**. As seguintes propriedades são suportadas na atividade de cópia **sink** secção.

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | A propriedade de tipo de sink de atividade de cópia tem de ser definida **DynamicsSink**. | Sim |
| writeBehavior | O comportamento da operação de escrita.<br/>Permitido é de valor **"Upsert"** . | Sim |
| writeBatchSize | A contagem de linhas de dados escritos para o Dynamics em cada lote. | Não (a predefinição é 10) |
| ignoreNullValues | Indica se a ignorar valores nulos de dados de entrada (exceto os campos de chave) durante uma operação de escrita.<br/>Valores permitidos são **true** e **falso**.<br>- **Verdadeiro**: Deixe os dados no objeto de destino inalterado quando faz uma operação de upsert/atualização. Inserir um valor padrão definido quando o fizer uma operação de inserção.<br/>- **False**: Atualize os dados no objeto de destino como NULL quando o fizer uma operação de upsert/atualização. Inserir um valor nulo ao fazer uma operação de inserção. | Não (a predefinição é falso) |

>[!NOTE]
>O valor predefinido do coletor "**writeBatchSize**"e a atividade de cópia" **[parallelCopies](copy-activity-performance.md#parallel-copy)** " para o sink de Dynamics são ambos os 10. Por conseguinte, 100 registos são submetidos ao Dynamics e ao mesmo tempo.

Para online para o Dynamics 365, existe um limite de [2 chamadas de batch em simultâneo por organização](https://msdn.microsoft.com/library/jj863631.aspx#Run-time%20limitations). Se esse limite for excedido, uma falha de "Servidor ocupado" será gerada antes do primeiro pedido nunca é executado. Manter "writeBatchSize" inferior ou igual a 10 evitaria essa limitação de chamadas simultâneas.

A combinação ideal de "**writeBatchSize**"e"**parallelCopies**" depende o esquema da sua entidade por exemplo, número de colunas, o tamanho de linha, o número de atividades de plug-ins/fluxos de trabalho/fluxo de trabalho conectado para essas chamadas, etc. A predefinição de 10 writeBatchSize * 10 parallelCopies é a recomendação de acordo com o serviço de Dynamics, que funciona para a maior parte das entidades do Dynamics embora talvez não seja melhor desempenho. Pode otimizar o desempenho ao ajustar a combinação nas suas definições de atividade de cópia.

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyToDynamics",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Dynamics output dataset>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "DynamicsSink",
                "writeBehavior": "Upsert",
                "writeBatchSize": 10,
                "ignoreNullValues": true
            }
        }
    }
]
```

## <a name="data-type-mapping-for-dynamics"></a>Tipo de dados de mapeamento para o Dynamics

Quando copiar dados do Dynamics, os seguintes mapeamentos são utilizados entre tipos de dados do Dynamics para tipos de dados intermediárias do Data Factory. Para saber como a atividade de cópia mapeia o tipo de esquema e os dados de origem para o sink, veja [mapeamentos de tipo de esquema e dados](copy-activity-schema-and-type-mapping.md).

Configure o tipo de dados de fábrica de dados correspondente numa estrutura de conjunto de dados com base na sua origem de tipo de dados do Dynamics com a seguinte tabela de mapeamento.

| Tipo de dados do Dynamics | Tipo de dados intermediárias de fábrica de dados | Suportado como origem | Suportado como sink |
|:--- |:--- |:--- |:--- |
| AttributeTypeCode.BigInt | Longo | ✓ | ✓ |
| AttributeTypeCode.Boolean | Boolean | ✓ | ✓ |
| AttributeType.Customer | Guid | ✓ | |
| AttributeType.DateTime | Datetime | ✓ | ✓ |
| AttributeType.Decimal | Decimal | ✓ | ✓ |
| AttributeType.Double | Double | ✓ | ✓ |
| AttributeType.EntityName | String | ✓ | ✓ |
| AttributeType.Integer | Int32 | ✓ | ✓ |
| AttributeType.Lookup | Guid | ✓ | ✓ (com o único destino associado) |
| AttributeType.ManagedProperty | Boolean | ✓ | |
| AttributeType.Memo | String | ✓ | ✓ |
| AttributeType.Money | Decimal | ✓ | ✓ |
| AttributeType.Owner | Guid | ✓ | |
| AttributeType.Picklist | Int32 | ✓ | ✓ |
| AttributeType.Uniqueidentifier | Guid | ✓ | ✓ |
| AttributeType.String | String | ✓ | ✓ |
| AttributeType.State | Int32 | ✓ | ✓ |
| AttributeType.Status | Int32 | ✓ | ✓ |


> [!NOTE]
> Os tipos de dados do Dynamics AttributeType.CalendarRules e AttributeType.PartyList não são suportados.

## <a name="next-steps"></a>Passos Seguintes
Para obter uma lista dos arquivos de dados suportados como origens e sinks, a atividade de cópia no Data Factory, veja [arquivos de dados suportados](copy-activity-overview.md#supported-data-stores-and-formats).
