---
title: Copiar dados de e para o Dynamics CRM ou o Dynamics 365 (Common Data Service) usando Azure Data Factory | Microsoft Docs
description: Saiba como copiar dados do Microsoft Dynamics CRM ou do Microsoft Dynamics 365 (Common Data Service) para armazenamentos de dados de coletor com suporte ou de armazenamentos de dados de origem com suporte para Dynamics CRM ou Dynamics 365, usando uma atividade de cópia em um pipeline de data factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: jingwang
ms.openlocfilehash: 18fdb14430eee97ff2780d963abf3e5ceafe1126
ms.sourcegitcommit: a819209a7c293078ff5377dee266fa76fd20902c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/16/2019
ms.locfileid: "71009403"
---
# <a name="copy-data-from-and-to-dynamics-365-common-data-service-or-dynamics-crm-by-using-azure-data-factory"></a>Copiar dados de e para o Dynamics 365 (Common Data Service) ou o Dynamics CRM usando Azure Data Factory

Este artigo descreve como usar a atividade de cópia em Azure Data Factory para copiar dados de e para o Microsoft Dynamics 365 ou o Microsoft Dynamics CRM. Ele se baseia no [descrição geral da atividade de cópia](copy-activity-overview.md) artigo apresenta uma visão geral da atividade de cópia.

## <a name="supported-capabilities"></a>Capacidades suportadas

Este conector tem suporte para as seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com [matriz de coletor/origem com suporte](copy-activity-overview.md)
- [Atividade de Pesquisa](control-flow-lookup-activity.md)

Você pode copiar dados do Dynamics 365 (Common Data Service) ou Dynamics CRM para qualquer armazenamento de dados de coletor com suporte. Você também pode copiar dados de qualquer armazenamento de dados de origem com suporte para o Dynamics 365 (Common Data Service) ou o Dynamics CRM. Para obter uma lista de armazenamentos de dados com suporte como fontes ou coletores pela atividade de cópia, consulte a tabela [armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats) .

Este conector do Dynamics dá suporte ao Dynamics versão 7. x a 9. x para online ou local. Mais especificamente,

- A versão 7. x é mapeada para o Dynamics CRM 2015
- A versão 8. x é mapeada para o Dynamics CRM 2016 e para a versão anterior do Dynamics 365
- A versão 9. x é mapeada para a versão mais recente do Dynamics 365

Consulte a tabela a seguir sobre os tipos de autenticação e configurações com suporte para as respectivas versões/produtos do Dynamics. (A IFD é curta para a implantação para a Internet.)

| Versões do Dynamics | Tipos de autenticação | Exemplos de serviço vinculado |
|:--- |:--- |:--- |
| Dynamics 365 online <br> Dynamics CRM Online | Office365 | [Dynamics online + autenticação do Office365](#dynamics-365-and-dynamics-crm-online) |
| Dynamics 365 local com IFD <br> Dynamics CRM 2016 local com IFD <br> Dynamics CRM 2015 local com IFD | IFD | [Dynamics local com IFD + autenticação de IFD](#dynamics-365-and-dynamics-crm-on-premises-with-ifd) |

Para o Dynamics 365 especificamente, há suporte para os seguintes tipos de aplicativo:

- Dynamics 365 para vendas
- Dynamics 365 para atendimento ao cliente
- Dynamics 365 for Field Service
- Dynamics 365 para automação do serviço de projeto
- Dynamics 365 para marketing

Outros tipos de aplicativos, por exemplo, finanças e operações, talento, etc., não têm suporte desse conector.

Esse conector do Dynamics é criado sobre as [Ferramentas do Dynamics xrm](https://docs.microsoft.com/dynamics365/customer-engagement/developer/build-windows-client-applications-xrm-tools).

>[!TIP]
>Para copiar dados de **Finanças e operações do dynamics 365**, você pode usar o [conector do Dynamics AX](connector-dynamics-ax.md).

## <a name="get-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções a seguir fornecem detalhes sobre as propriedades que são usadas para definir Data Factory entidades específicas ao Dynamics.

## <a name="linked-service-properties"></a>Propriedades do serviço ligado

As propriedades a seguir têm suporte para o serviço vinculado do Dynamics.

### <a name="dynamics-365-and-dynamics-crm-online"></a>Dynamics 365 e Dynamics CRM Online

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | A propriedade Type deve ser definida como **Dynamics**. | Sim |
| deploymentType | O tipo de implantação da instância do Dynamics. Ele deve ser **"online"** para o Dynamics online. | Sim |
| serviceUri | A URL de serviço da sua instância do Dynamics, `https://adfdynamics.crm.dynamics.com`por exemplo,. | Sim |
| authenticationType | O tipo de autenticação para se conectar a um servidor do Dynamics. Especifique **"Office365"** para o Dynamics online. | Sim |
| username | Especifique o nome de usuário para se conectar ao Dynamics. | Sim |
| password | Especifique a senha da conta de usuário que você especificou para username. Marcar esse campo como uma SecureString armazena de forma segura na fábrica de dados, ou [referenciar um segredo armazenado no Azure Key Vault](store-credentials-in-key-vault.md). | Sim |
| connectVia | O [runtime de integração](concepts-integration-runtime.md) a ser utilizado para ligar ao arquivo de dados. Se não for especificado, ele usa o padrão do Runtime de integração do Azure. | Não para fonte, sim para o coletor se o serviço vinculado de origem não tiver um tempo de execução de integração |

>[!NOTE]
>O conector do Dynamics usado para usar a propriedade "OrganizationName" opcional para identificar sua instância do Dynamics CRM/365 online. Enquanto ele continua funcionando, é recomendável especificar a nova propriedade "serviceUri" para obter um melhor desempenho para a descoberta de instância.

**Exemplo: Dynamics online usando a autenticação do Office365**

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

### <a name="dynamics-365-and-dynamics-crm-on-premises-with-ifd"></a>Dynamics 365 e Dynamics CRM local com IFD

*Propriedades adicionais que se comparam ao Dynamics online são "hostName" e "Port".*

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | A propriedade Type deve ser definida como **Dynamics**. | Sim |
| deploymentType | O tipo de implantação da instância do Dynamics. Deve ser **"OnPremisesWithIfd"** para o Dynamics local com IFD.| Sim |
| hostName | O nome do host do servidor do Dynamics local. | Sim |
| port | A porta do servidor do Dynamics local. | Não, o padrão é 443 |
| organizationName | O nome da organização da instância do Dynamics. | Sim |
| authenticationType | O tipo de autenticação para se conectar ao servidor do Dynamics. Especifique **"IFD"** para o Dynamics local com IFD. | Sim |
| username | Especifique o nome de usuário para se conectar ao Dynamics. | Sim |
| password | Especifique a senha da conta de usuário que você especificou para username. Você pode optar por marcar esse campo como uma SecureString para armazená-lo com segurança no ADF, ou armazenar a senha em Azure Key Vault e deixar a atividade de cópia efetuar pull a partir daí ao executar a cópia de dados-Saiba mais em [armazenar credenciais no Key Vault](store-credentials-in-key-vault.md). | Sim |
| connectVia | O [runtime de integração](concepts-integration-runtime.md) a ser utilizado para ligar ao arquivo de dados. Se não for especificado, ele usa o padrão do Runtime de integração do Azure. | Não para a origem, Sim para o sink |

**Exemplo: Dynamics local com IFD usando a autenticação IFD**

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

Para obter uma lista completa das secções e propriedades disponíveis para definir conjuntos de dados, consulte a [conjuntos de dados](concepts-datasets-linked-services.md) artigo. Esta seção fornece uma lista das propriedades com suporte pelo DataSet do Dynamics.

Para copiar dados de e para o Dynamics, defina a propriedade Type do conjunto como **DynamicsEntity**. São suportadas as seguintes propriedades.

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | A propriedade Type do conjunto de conjuntos deve ser definida como **DynamicsEntity**. |Sim |
| entityName | O nome lógico da entidade a ser recuperada. | Não para origem (se "consulta" na origem da atividade for especificada), sim para o coletor |

> [!IMPORTANT]
>- Quando você copia dados do Dynamics, a seção "estrutura" é opcional, mas altamente recomandoada no conjunto de dados do Dynamics para garantir um resultado de cópia determinística. Ele define o nome da coluna e o tipo de dados para os dados do Dynamics que você deseja copiar. Para saber mais, consulte [estrutura do conjunto](concepts-datasets-linked-services.md#dataset-structure-or-schema) de [dados e mapeamento de tipo de dado para o Dynamics](#data-type-mapping-for-dynamics).
>- Ao importar o esquema na interface do usuário de criação, o ADF infere o esquema por meio da amostragem das principais linhas do resultado da consulta do Dynamics para inicializar a construção da estrutura; nesse caso, as colunas sem valores serão omitidas. O mesmo comportamento se aplica a execuções de cópia se não houver nenhuma definição de estrutura explícita. Você pode revisar e adicionar mais colunas ao esquema/estrutura do conjunto de dados do Dynamics, conforme necessário, que serão respeitadas durante o tempo de execução da cópia.
>- Quando você copia dados para o Dynamics, a seção "estrutura" é opcional no DataSet do Dynamics. As colunas a serem copiadas são determinadas pelo esquema de dados de origem. Se sua origem for um arquivo CSV sem um cabeçalho, no conjunto de dados de entrada, especifique a "estrutura" com o nome da coluna e o tipo de dado. Eles são mapeados para campos no arquivo CSV, um a um na ordem.

**Example:**

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

Para obter uma lista completa das secções e propriedades disponíveis para a definição de atividades, consulte a [Pipelines](concepts-pipelines-activities.md) artigo. Esta seção fornece uma lista das propriedades com suporte pelos tipos de origem e de coletor do Dynamics.

### <a name="dynamics-as-a-source-type"></a>Dynamics como um tipo de origem

Para copiar dados do Dynamics, defina o tipo de origem na atividade de cópia como **Dynamics**Source. As seguintes propriedades são suportadas na atividade de cópia **origem** secção.

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | A propriedade Type da fonte da atividade de cópia deve ser definida como **Dynamics**Source. | Sim |
| query | FetchXML é uma linguagem de consulta proprietária que é usada no Dynamics (online e local). Veja o seguinte exemplo. Para saber mais, consulte [criar consultas com FetchXML](https://msdn.microsoft.com/library/gg328332.aspx). | Não (se "EntityName" no DataSet for especificado) |

>[!NOTE]
>A coluna CP sempre será copiada, mesmo que a projeção de coluna configurada na consulta FetchXML não a contenha.

**Example:**

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

### <a name="sample-fetchxml-query"></a>Consulta FetchXML de exemplo

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

### <a name="dynamics-as-a-sink-type"></a>Dynamics como um tipo de coletor

Para copiar dados para o Dynamics, defina o tipo de coletor na atividade de cópia como **DynamicsSink**. As propriedades a seguir têm suporte na seção **coletor** de atividade de cópia.

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | A propriedade Type do coletor da atividade de cópia deve ser definida como **DynamicsSink**. | Sim |
| writeBehavior | O comportamento de gravação da operação.<br/>O valor permitido é **"Upsert"** . | Sim |
| writeBatchSize | A contagem de linhas de dados gravados no Dynamics em cada lote. | Não (o padrão é 10) |
| ignoreNullValues | Indica se os valores nulos devem ser ignorados dos dados de entrada (exceto os campos de chave) durante uma operação de gravação.<br/>Os valores permitidos são **true** e **false**.<br>- **True**: Deixe os dados no objeto de destino inalterados quando você fizer uma operação de Upsert/atualização. Insira um valor padrão definido quando você fizer uma operação de inserção.<br/>- **Falso**: Atualize os dados no objeto de destino para NULL quando você fizer uma operação de Upsert/atualização. Insira um valor nulo ao fazer uma operação de inserção. | Não (o padrão é false) |

>[!NOTE]
>O valor padrão do coletor "**writeBatchSize**" e a atividade de cópia " **[parallelCopies](copy-activity-performance.md#parallel-copy)** " para o coletor do Dynamics são ambos 10. Portanto, os registros 100 são enviados ao Dynamics simultaneamente.

Para o Dynamics 365 online, há um limite de [2 chamadas simultâneas em lote por organização](https://msdn.microsoft.com/library/jj863631.aspx#Run-time%20limitations). Se esse limite for excedido, uma falha de "servidor ocupado" será lançada antes que a primeira solicitação seja executada. Manter "writeBatchSize" menor ou igual a 10 evitaria essa limitação de chamadas simultâneas.

A combinação ideal de "**writeBatchSize**" e "**parallelCopies**" depende do esquema da sua entidade, por exemplo, número de colunas, tamanho da linha, número de plug-ins/fluxos de trabalho/atividades de fluxo de trabalho conectados a essas chamadas etc. A configuração padrão de 10 writeBatchSize * 10 parallelCopies é a recomendação de acordo com o serviço do Dynamics, o que funcionaria para a maioria das entidades do Dynamics, embora possa não ser o melhor desempenho. Você pode ajustar o desempenho ajustando a combinação nas configurações da atividade de cópia.

**Example:**

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

## <a name="data-type-mapping-for-dynamics"></a>Mapeamento de tipo de dados para Dynamics

Quando você copia dados do Dynamics, os seguintes mapeamentos são usados de tipos de dados do Dynamics para Data Factory tipos de dados provisórios. Para saber como a atividade de cópia mapeia o esquema de origem e o tipo de dados para o coletor, consulte [mapeamentos de tipo de dados e esquema](copy-activity-schema-and-type-mapping.md).

Configure o tipo de dados Data Factory correspondente em uma estrutura de conjunto de dados com base no tipo de fonte Dynamics de origem usando a seguinte tabela de mapeamento.

| Tipo de dados do Dynamics | Tipo de dados intermediárias de fábrica de dados | Com suporte como fonte | Com suporte como coletor |
|:--- |:--- |:--- |:--- |
| AttributeTypeCode.BigInt | Longo | ✓ | ✓ |
| AttributeTypeCode.Boolean | Booleano | ✓ | ✓ |
| AttributeType.Customer | Guid | ✓ | |
| AttributeType.DateTime | Datetime | ✓ | ✓ |
| AttributeType.Decimal | Decimal | ✓ | ✓ |
| AttributeType. Double | Double | ✓ | ✓ |
| AttributeType.EntityName | Cadeia | ✓ | ✓ |
| AttributeType.Integer | Int32 | ✓ | ✓ |
| AttributeType.Lookup | Guid | ✓ | ✓ (com destino único associado) |
| AttributeType.ManagedProperty | Booleano | ✓ | |
| AttributeType.Memo | Cadeia | ✓ | ✓ |
| AttributeType.Money | Decimal | ✓ | ✓ |
| AttributeType. proprietário | Guid | ✓ | |
| AttributeType.Picklist | Int32 | ✓ | ✓ |
| AttributeType.Uniqueidentifier | Guid | ✓ | ✓ |
| AttributeType.String | Cadeia | ✓ | ✓ |
| AttributeType.State | Int32 | ✓ | ✓ |
| AttributeType.Status | Int32 | ✓ | ✓ |

> [!NOTE]
> Não há suporte para os tipos de dados do Dynamics AttributeType. CalendarRules, AttributeType. MultiSelectPicklist e AttributeType. Partylist.

## <a name="lookup-activity-properties"></a>Propriedades da atividade de pesquisa

Para obter detalhes sobre as propriedades, verifique a [atividade de pesquisa](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Passos seguintes
Para obter uma lista dos arquivos de dados suportados como origens e sinks, a atividade de cópia no Data Factory, veja [arquivos de dados suportados](copy-activity-overview.md#supported-data-stores-and-formats).
