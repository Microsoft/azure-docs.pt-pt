---
title: Copiar dados em Dinâmica (Serviço Comum de Dados)
description: Aprenda a copiar dados do Microsoft Dynamics CRM ou do Microsoft Dynamics 365 (Common Data Service) para lojas de dados de sumidouros suportados, ou de lojas de dados de origem suportadas para Dynamics CRM ou Dynamics 365, utilizando uma atividade de cópia num oleoduto de fábrica de dados.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.custom: seo-lt-2019
ms.date: 06/10/2020
ms.openlocfilehash: 6aef73381b9294f6bbd83ff99c3fa99eafb4fb1f
ms.sourcegitcommit: f01c2142af7e90679f4c6b60d03ea16b4abf1b97
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/10/2020
ms.locfileid: "84678320"
---
# <a name="copy-data-from-and-to-dynamics-365-common-data-service-or-dynamics-crm-by-using-azure-data-factory"></a>Copiar dados de e para Dynamics 365 (Common Data Service) ou Dynamics CRM utilizando a Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve como utilizar a Copy Activity in Azure Data Factory para copiar dados de e para o Microsoft Dynamics 365 ou Microsoft Dynamics CRM. Baseia-se no artigo [de visão geral](copy-activity-overview.md) da Atividade de Cópia que apresenta uma visão geral da Atividade de Cópia.

## <a name="supported-capabilities"></a>Capacidades suportadas

Este conector é suportado para as seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com [matriz de fonte/pia suportada](copy-activity-overview.md)
- [Atividade de procura](control-flow-lookup-activity.md)

Pode copiar dados da Dynamics 365 (Common Data Service) ou Dynamics CRM para qualquer loja de dados de sumidouro suportado. Também pode copiar dados de qualquer loja de dados de origem suportada para a Dynamics 365 (Common Data Service) ou Dynamics CRM. Para obter uma lista de lojas de dados suportadas como fontes ou sumidouros pela atividade de cópia, consulte a tabela [de lojas de dados suportadas.](copy-activity-overview.md#supported-data-stores-and-formats)

Este conector Dynamics suporta a versão Dynamics 7.x a 9.x para o online ou no local. Mais especificamente,

- Versão 7.x mapas para Dynamics CRM 2015
- Versão 8.x mapas para Dynamics CRM 2016 e a versão inicial da Dynamics 365
- Versão 9.x mapas para a versão posterior da Dynamics 365

Consulte a tabela seguinte sobre os tipos e configurações de autenticação suportadas para as respetivas versões/produtos Dynamics. (O IFD é abreviado para a implementação virada para a Internet.)

| Versões dinâmicas | Tipos de autenticação | Amostras de serviço ligadas |
|:--- |:--- |:--- |
| Common Data Service <br> Dinâmica 365 online <br> Dynamics CRM Online | Diretor de serviços da AAD <br> Escritório365 | [Dynamics online + AAD service principal ou Office365 auth](#dynamics-365-and-dynamics-crm-online) |
| Dinâmica 365 no local com IFD <br> Dynamics CRM 2016 no local com IFD <br> Dynamics CRM 2015 no local com IFD | IFD | [Dinâmica no local com IFD + IFD auth](#dynamics-365-and-dynamics-crm-on-premises-with-ifd) |

Especificamente para a Dynamics 365, são suportados os seguintes tipos de aplicação:

- Dynamics 365 for Sales
- Dynamics 365 for Customer Service
- Dynamics 365 for Field Service
- Dynamics 365 para Automatização do Serviço do Projeto
- Dynamics 365 for Marketing

Outros tipos de aplicação, por exemplo, Finanças e Operações, Talento, etc. não são suportados por este conector.

Este conector Dynamics é construído em cima da [ferramenta Dynamics XRM](https://docs.microsoft.com/dynamics365/customer-engagement/developer/build-windows-client-applications-xrm-tools).

>[!TIP]
>Para copiar **dados da Dynamics 365 Finanças e Operações,** pode utilizar o [conector Dynamics AX](connector-dynamics-ax.md).

## <a name="prerequisites"></a>Pré-requisitos

Para utilizar este conector com a autenticação principal do serviço AAD, é necessário configurar a autenticação Server-to-Server (S2S) no Common Data Service ou Dynamics. Consulte [este artigo](https://docs.microsoft.com/powerapps/developer/common-data-service/build-web-applications-server-server-s2s-authentication) sobre etapas detalhadas.

## <a name="get-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seguintes secções fornecem detalhes sobre propriedades que são usadas para definir entidades da Data Factory específicas da Dynamics.

## <a name="linked-service-properties"></a>Propriedades de serviço ligadas

As seguintes propriedades são suportadas para o serviço ligado à Dynamics.

### <a name="dynamics-365-and-dynamics-crm-online"></a>Dinâmica 365 e Dinâmica CRM Online

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade tipo deve ser definida para **Dynamics,** **DynamicsCrm,** ou **CommonDataServiceForApps**. | Sim |
| tipo de implantação | O tipo de implantação da instância Dynamics. Deve ser **"Online"** para a Dynamics online. | Sim |
| serviceUri | O URL de serviço da sua instância Dynamics, por `https://adfdynamics.crm.dynamics.com` exemplo. | Sim |
| authenticationType | O tipo de autenticação para ligar a um servidor Dynamics. Os valores permitidos são: **AADServicePrincipal** ou **"Office365".** | Sim |
| servicePrincipalId | Especificar o ID do cliente do Azure Ative Directory. | Sim, ao utilizar `AADServicePrincipal` a autenticação |
| ServiçoPrincipalCredentialType | Especifique o tipo de credencial a utilizar para a autenticação principal do serviço. Os valores permitidos são: **ServicePrincipalKey** ou **ServicePrincipalCert**. | Sim, ao utilizar `AADServicePrincipal` a autenticação |
| serviçoPrincipalCredential | Especifique a credencial principal de serviço. <br>Ao usar `ServicePrincipalKey` como tipo credencial, `servicePrincipalCredential` pode ser uma cadeia (a ADF irá criptografá-la após a implementação do serviço ligado) ou uma referência a um segredo em AKV. <br>Ao utilizar `ServicePrincipalCert` como credencial, `servicePrincipalCredential` deve ser uma referência a um certificado em AKV. | Sim, ao utilizar `AADServicePrincipal` a autenticação |
| o nome de utilizador | Especifique o nome de utilizador para ligar à Dynamics. | Sim, ao utilizar `Office365` a autenticação |
| palavra-passe | Especifique a palavra-passe para a conta de utilizador especificada para o nome de utilizador. Marque este campo como um SecureString para armazená-lo de forma segura na Data Factory, ou [fazer referência a um segredo armazenado no Cofre da Chave Azure](store-credentials-in-key-vault.md). | Sim, ao utilizar `Office365` a autenticação |
| connectVia | O [tempo de integração](concepts-integration-runtime.md) a ser utilizado para ligar à loja de dados. Se não for especificado, utiliza o tempo de execução de integração Azure predefinido. | Não para fonte, Sim para afundar se o serviço ligado à fonte não tiver um tempo de integração |

>[!NOTE]
>O conector Dynamics usado para usar propriedade opcional "organizationName" para identificar a sua instância Online Dynamics CRM/365. Enquanto continua a funcionar, sugere-se que especifique a nova propriedade "serviceUri" em vez de obter um melhor desempenho, por exemplo, a descoberta.

**Exemplo: Dinâmica on-line usando o principal do serviço AAD + autenticação de chaves**

```json
{  
    "name": "DynamicsLinkedService",  
    "properties": {  
        "type": "Dynamics",  
        "typeProperties": {  
            "deploymentType": "Online",  
            "serviceUri": "https://adfdynamics.crm.dynamics.com",  
            "authenticationType": "AADServicePrincipal",  
            "servicePrincipalId": "<service principal id>",  
            "servicePrincipalCredentialType": "ServicePrincipalKey",  
            "servicePrincipalCredential": "<service principal key>"
        },  
        "connectVia": {  
            "referenceName": "<name of Integration Runtime>",  
            "type": "IntegrationRuntimeReference"  
        }  
    }  
}  
```
**Exemplo: Dinâmica online usando o principal do serviço AAD + autenticação de certificado**

```json
{ 
    "name": "DynamicsLinkedService", 
    "properties": { 
        "type": "Dynamics", 
        "typeProperties": { 
            "deploymentType": "Online", 
            "serviceUri": "https://adfdynamics.crm.dynamics.com", 
            "authenticationType": "AADServicePrincipal", 
            "servicePrincipalId": "<service principal id>", 
            "servicePrincipalCredentialType": "ServicePrincipalCert", 
            "servicePrincipalCredential": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<AKV reference>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<certificate name in AKV>" 
            } 
        }, 
        "connectVia": { 
            "referenceName": "<name of Integration Runtime>", 
            "type": "IntegrationRuntimeReference" 
        } 
    } 
} 
```

**Exemplo: Dinâmica online usando a autenticação do Office365**

```json
{
    "name": "DynamicsLinkedService",
    "properties": {
        "type": "Dynamics",
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

### <a name="dynamics-365-and-dynamics-crm-on-premises-with-ifd"></a>Dinâmica 365 e Dinamismo CRM no local com IFD

*Propriedades adicionais que se comparam à Dynamics online são "hostName" e "port".*

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade tipo deve ser definida para **Dynamics,** **DynamicsCrm,** ou **CommonDataServiceForApps**. | Sim |
| tipo de implantação | O tipo de implantação da instância Dynamics. Deve ser **"OnPremisesWithIfd"** para a Dynamics no local com IFD.| Sim |
| nome hospedeiro | O nome de anfitrião do servidor Dynamics no local. | Sim |
| porta | A porta do servidor Dynamics no local. | Não, o padrão é 443. |
| organização Nome | O nome da organização do caso Dynamics. | Sim |
| authenticationType | O tipo de autenticação para ligar ao servidor Dynamics. Especificar **"Ifd"** para Dinâmicas no local com IFD. | Sim |
| o nome de utilizador | Especifique o nome de utilizador para ligar à Dynamics. | Sim |
| palavra-passe | Especifique a palavra-passe para a conta de utilizador especificada para o nome de utilizador. Pode optar por marcar este campo como um SecureString para armazená-lo de forma segura em ADF, ou armazenar a palavra-passe no Cofre da Chave Azure e deixar a atividade de cópia sair daí ao executar a cópia de dados - saiba mais com [as credenciais](store-credentials-in-key-vault.md)da Loja no Key Vault . | Sim |
| connectVia | O [tempo de integração](concepts-integration-runtime.md) a ser utilizado para ligar à loja de dados. Se não for especificado, utiliza o tempo de execução de integração Azure predefinido. | Não para a fonte, sim para a pia |

**Exemplo: Dinâmica nas instalações com IFD utilizando a autenticação IFD**

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

## <a name="dataset-properties"></a>Dataset properties (Propriedades do conjunto de dados)

Para obter uma lista completa de secções e propriedades disponíveis para definir conjuntos de dados, consulte o artigo [Datasets.](concepts-datasets-linked-services.md) Esta secção fornece uma lista de propriedades suportadas pelo conjunto de dados Dynamics.

Para copiar dados de e para a Dynamics, as seguintes propriedades são suportadas.

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade tipo do conjunto de dados deve ser definida para **DynamicsEntity,** **DynamicsCrmEntity,** ou **CommonDataServiceForAppsEntity**. |Sim |
| entidade Nome | O nome lógico da entidade para recuperar. | Não para a fonte (se for especificada "consulta" na fonte de atividade), Sim para pia |

**Exemplo:**

```json
{
    "name": "DynamicsDataset",
    "properties": {
        "type": "DynamicsEntity",
        "schema": [],
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

Para obter uma lista completa de secções e propriedades disponíveis para definir atividades, consulte o artigo [Pipelines.](concepts-pipelines-activities.md) Esta secção fornece uma lista de propriedades suportadas por fontes dinâmicas e tipos de pia.

### <a name="dynamics-as-a-source-type"></a>Dinâmica como tipo de origem

Para copiar dados da Dynamics, as seguintes propriedades são suportadas na secção **fonte de origem** da atividade da cópia.

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade tipo da fonte de atividade de cópia deve ser definida para **DynamicsSource,** **DynamicsCrmSource**ou **CommonDataServiceForAppsSource**. | Sim |
| consulta | FetchXML é uma linguagem de consulta proprietária que é usada em Dynamics (online e no local). Veja o seguinte exemplo. Para saber mais, consulte [Criar consultas com a FetchXML.](https://msdn.microsoft.com/library/gg328332.aspx) | Não (se for especificado "nome de entidade" no conjunto de dados) |

>[!NOTE]
>A coluna PK será sempre copiada mesmo que a projeção da coluna que configurar na consulta FetchXML não a contenha.

> [!IMPORTANT]
>- Quando copia dados da Dynamics, o mapeamento de colunas explícitas da Dynamics para afundar é opcional, mas altamente recomundosado para garantir um resultado de cópia determinista.
>- Ao importar esquema na autoria da UI, a ADF infere o esquema através da amostragem das linhas superiores da consulta Dynamics para inicializar a lista de colunas de origem, caso em que serão omitidas colunas sem valores nas linhas superiores. O mesmo comportamento aplica-se a execuções de cópias se não houver mapeamento explícito. Pode rever e adicionar mais colunas no mapeamento, que será homenageado durante o tempo de funcionação da cópia.

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

### <a name="sample-fetchxml-query"></a>Consulta de Amostra FetchXML

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

### <a name="dynamics-as-a-sink-type"></a>Dinâmica como tipo de pia

Para copiar dados para a Dynamics, as seguintes propriedades são suportadas na secção de **lavatório** de atividade de cópia.

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade tipo do lavatório de atividade de cópia deve ser definida para **DynamicsSink,** **DynamicsCrmSink,** ou **CommonDataServiceForAppsSink**. | Sim |
| escrever Comportamento | O comportamento de escrita da operação.<br/>O valor permitido é **"Upsert".** | Sim |
| nome alternativoKeyName | Especifique o nome de chave alternativa definido na sua entidade para executar "Upsert". | Não |
| escreverBatchSize | A contagem de dados escrita para a Dynamics em cada lote. | Não (o padrão é 10) |
| ignoreNullValues | Indica se deve ignorar os valores nulos dos dados de entrada (exceto os campos-chave) durante uma operação de escrita.<br/>Os valores permitidos são **verdadeiros** e **falsos.**<br>- **Verdade**: Deixe os dados no objeto de destino inalterados quando fizer uma operação de atualização/atualização. Insira um valor predefinido definido quando fizer uma operação de inserção.<br/>- **Falso**: Atualize os dados no objeto de destino para NU QUANDO fizer uma operação de atualização/atualização. Insira um valor NULO quando fizer uma operação de inserção. | Não (o padrão é falso) |

>[!NOTE]
>O valor predefinido do lavatório "**writeBatchSize**" e a atividade de cópia "**[parallelCopies](copy-activity-performance-features.md#parallel-copy)**" para a pia Dynamics são ambos 10. Portanto, 100 registos são submetidos à Dynamics simultaneamente.

Para a Dynamics 365 online, existe um limite de [2 chamadas de lote simultâneas por organização.](https://msdn.microsoft.com/library/jj863631.aspx#Run-time%20limitations) Se esse limite for ultrapassado, uma falha "Server Busy" é lançada antes de o primeiro pedido ser executado. Manter "writeBatchSize" menos ou igual a 10 evitaria tal estrangulamento de chamadas simultâneas.

A combinação ideal de "**writeBatchSize**" e "**parallelCopies**" depende do esquema da sua entidade, por exemplo, número de colunas, tamanho da linha, número de plugins/workflows/workflows ligados a essas chamadas, etc. A definição padrão de 10 writeBatchSize * 10 parallelCopies é a recomendação de acordo com o serviço Dynamics, que funcionaria para a maioria das entidades dynamics embora possa não ser o melhor desempenho. Pode sintonizar o desempenho ajustando a combinação nas definições de atividade da sua cópia.

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

## <a name="data-type-mapping-for-dynamics"></a>Mapeamento do tipo de dados para dinâmicas

Quando copia dados da Dynamics, os seguintes mapeamentos são usados desde tipos de dados dynamics para tipos de dados provisórios da Data Factory. Para saber como a atividade da cópia mapeia o esquema de origem e o tipo de dados para a pia, consulte [o Schema e os mapeamentos do tipo de dados](copy-activity-schema-and-type-mapping.md).

Configure o tipo de dados correspondente da Data Factory numa estrutura de conjunto de dados com base no tipo de dados da sua fonte Dynamics utilizando a seguinte tabela de mapeamento.

| Tipo de dados dinâmicos | Tipo de dados provisórios da Data Factory | Suportado como fonte | Suportado como pia |
|:--- |:--- |:--- |:--- |
| AtributoTypeCode.BigInt | Longo | ✓ | ✓ |
| AtributoTypeCode.Boolean | Booleano | ✓ | ✓ |
| AtributoType.Cliente | GUID | ✓ | ✓ (ver [orientação)](#writing-data-to-lookup-field) |
| AtributoType.DateTime | Datetime | ✓ | ✓ |
| AtributoType.Decimal | Decimal | ✓ | ✓ |
| AtributoType.Double | Double | ✓ | ✓ |
| Nome de AtributoType.EntityName | String | ✓ | ✓ |
| AtributoType.Inteiro | Int32 | ✓ | ✓ |
| AtributoType.Lookup | GUID | ✓ | ✓ (ver [orientação)](#writing-data-to-lookup-field) |
| AtributoType.ManagedProperty | Booleano | ✓ | |
| AtributoType.Memo | String | ✓ | ✓ |
| AtributoType.Dinheiro | Decimal | ✓ | ✓ |
| AtributoType.Proprietário | GUID | ✓ | ✓ (ver [orientação)](#writing-data-to-lookup-field) |
| AtributoType.Picklist | Int32 | ✓ | ✓ |
| AtributoType.Uniqueidentifier | GUID | ✓ | ✓ |
| AtributoType.String | String | ✓ | ✓ |
| AtributoType.State | Int32 | ✓ | ✓ |
| AtributoType.Status | Int32 | ✓ | ✓ |

> [!NOTE]
> Os tipos de dados Dinâmicos AttributeType.CalendarRules, AttributeType.MultiSelectPicklist e AttributeType.PartyList não são suportados.

## <a name="writing-data-to-lookup-field"></a>Escrever dados para o campo de procura

Para escrever dados no campo de procura com vários alvos, por *exemplo, Cliente* e *Proprietário,* siga esta orientação e exemplo:

1. Faça a sua fonte contendo tanto o valor de campo como o nome da entidade-alvo correspondente.
   - Se todos os registos mapearem para a mesma entidade-alvo, certifique-se de que os seus dados de origem têm uma coluna que armazena o nome da entidade-alvo, ou para adicionar uma coluna adicional na fonte de atividade de cópia para definir a entidade-alvo.
   - Se diferentes registos mapearem para diferentes entidades-alvo, certifique-se de que os seus dados de origem têm uma coluna que armazena o nome da entidade-alvo correspondente.

2. Mapear tanto as colunas de valor como as colunas de referência da entidade de origem para afundar. A coluna de referência da entidade tem de ser mapeada para uma coluna virtual com um padrão especial de nomeação `{lookup_field_name}@EntityReference` . Na verdade, não existe na Dynamics, mas é usada para indicar que esta é a coluna de metadados do campo de procura multi-alvo.

 Por exemplo, a fonte tem duas colunas:

- `CustomerField`coluna do tipo *GUID,* que é o valor-chave primário da entidade-alvo em Dynamics.
- `Target`coluna do tipo *String*, que é o nome lógico da entidade-alvo. 

E pretende copiar esses dados para afundar o campo da entidade Dynamics `CustomerField` do tipo *Cliente.* 

No mapeamento de colunas de cópia, mapear as duas colunas da seguinte forma:

- `CustomerField` -> `CustomerField`: este é o mapeamento normal do campo.
- `Target` -> `CustomerField@EntityReference`: a coluna da pia é uma coluna virtual que representa a referência da entidade. Insira tal nome de campo no mapeamento, uma vez que não aparece importando esquemas.

![Mapeamento de coluna de campo de procuração dinâmica](./media/connector-dynamics-crm-office-365/connector-dynamics-lookup-field-column-mapping.png)

Se todos os seus registos de origem mapear para a mesma entidade-alvo e os seus dados de origem não contiver o nome da entidade-alvo, aqui está um atalho: na fonte de atividade de cópia, adicione uma coluna adicional. Pode especificar o nome seguindo o padrão e o `{lookup_field_name}@EntityReference` valor como o nome da entidade-alvo, caso em que o mapeamento explícito da coluna torna-se opcional porque a atividade de cópia por colunas de mapas predefinidos por nome.

![Campo de procura dinâmica adicionar referência de entidade](./media/connector-dynamics-crm-office-365/connector-dynamics-add-entity-reference-column.png)

## <a name="lookup-activity-properties"></a>Propriedades de atividade de procura

Para obter detalhes sobre as propriedades, consulte [a atividade de Lookup](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Próximos passos
Para obter uma lista de lojas de dados suportadas como fontes e sumidouros pela atividade de cópia na Data Factory, consulte lojas de [dados suportadas.](copy-activity-overview.md#supported-data-stores-and-formats)
