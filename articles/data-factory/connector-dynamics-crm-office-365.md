---
title: Copiar dados em Dinâmica (Serviço Comum de Dados)
description: Saiba como copiar dados do Microsoft Dynamics CRM ou microsoft Dynamics 365 (Common Data Service) para lojas de dados de sink suportadas, ou de lojas de dados de origem suportadas para Dynamics CRM ou Dynamics 365, utilizando uma atividade de cópia num pipeline de fábrica de dados.
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
ms.date: 11/20/2019
ms.openlocfilehash: c891cb4eca2c286b3ac636e5995714accd591772
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81417350"
---
# <a name="copy-data-from-and-to-dynamics-365-common-data-service-or-dynamics-crm-by-using-azure-data-factory"></a>Copiar dados de e para a Dynamics 365 (Serviço Comum de Dados) ou O CRM da Dynamics utilizando a Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve como utilizar a Atividade de Cópia na Fábrica de Dados Azure para copiar dados de e para o Microsoft Dynamics 365 ou microsoft Dynamics CRM. Baseia-se no artigo de visão geral da [Copy Activity](copy-activity-overview.md) que apresenta uma visão geral da Atividade de Cópia.

## <a name="supported-capabilities"></a>Capacidades suportadas

Este conector é suportado para as seguintes atividades:

- [Copiar atividade](copy-activity-overview.md) com matriz de [origem/pia suportada](copy-activity-overview.md)
- [Atividade de procura](control-flow-lookup-activity.md)

Pode copiar dados da Dynamics 365 (Common Data Service) ou da Dynamics CRM para qualquer loja de dados de sink suportado. Também pode copiar dados de qualquer loja de dados de origem suportada para Dynamics 365 (Common Data Service) ou Dynamics CRM. Para obter uma lista de lojas de dados suportadas como fontes ou pias pela atividade de cópia, consulte a tabela de lojas de [dados suportadas.](copy-activity-overview.md#supported-data-stores-and-formats)

Este conector Dynamics suporta a versão Dinâmica 7.x a 9.x tanto online como no local. Mais especificamente,

- Versão 7.x mapas para Dynamics CRM 2015
- Versão 8.x maps to Dynamics CRM 2016 and the early version of Dynamics 365
- Versão 9.x maps para a versão posterior da Dynamics 365

Consulte a tabela seguinte sobre os tipos e configurações de autenticação suportadas para as respetivas versões/produtos Dynamics. (IfD é abreviado para implementação virada para a Internet.)

| Versões dinâmicas | Tipos de autenticação | Amostras de serviço ligadas |
|:--- |:--- |:--- |
| Common Data Service <br> Dinâmica 365 online <br> Dynamics CRM Online | Diretor de serviço AAD <br> Escritório365 | [Dinâmica online + Diretor de serviço AAD ou Office365 auth](#dynamics-365-and-dynamics-crm-online) |
| Dinâmica 365 no local com IFD <br> Dinâmica CRM 2016 no local com IFD <br> Dinâmica CRM 2015 no local com IFD | IFD | [Dinâmica no local com ifd + auth IFD](#dynamics-365-and-dynamics-crm-on-premises-with-ifd) |

Para a Dynamics 365 especificamente, são suportados os seguintes tipos de aplicações:

- Dynamics 365 for Sales
- Dynamics 365 for Customer Service
- Dynamics 365 for Field Service
- Dynamics 365 para Automatização do Serviço do Projeto
- Dynamics 365 for Marketing

Outros tipos de aplicações, por exemplo, Finanças e Operações, Talento, etc. não são suportados por este conector.

Este conector Dynamics é construído em cima da [ferramenta Dynamics XRM](https://docs.microsoft.com/dynamics365/customer-engagement/developer/build-windows-client-applications-xrm-tools).

>[!TIP]
>Para copiar dados da **Dynamics 365 Finance and Operations,** pode utilizar o [conector Dynamics AX](connector-dynamics-ax.md).

## <a name="get-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seguintes secções fornecem detalhes sobre propriedades que são usadas para definir entidades data Factory específicas da Dynamics.

## <a name="linked-service-properties"></a>Propriedades de serviço seletos

As seguintes propriedades são suportadas para o serviço ligado à Dinâmica.

### <a name="dynamics-365-and-dynamics-crm-online"></a>Dinâmica 365 e Dynamics CRM Online

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade do tipo deve ser definida para **Dynamics,** **DynamicsCrm,** ou **CommonDataServiceForApps**. | Sim |
| implementaçãoType | O tipo de implantação da instância Dynamics. Deve ser **"Online"** para a Dynamics online. | Sim |
| serviceUri | O URL de serviço da sua `https://adfdynamics.crm.dynamics.com`instância Dinâmica, por exemplo. | Sim |
| authenticationType | O tipo de autenticação para ligar a um servidor Dynamics. Os valores permitidos são: **AADServicePrincipal** ou **"Office365".** | Sim |
| serviçoPrincipalId | Especifique o ID do cliente do cliente da aplicação Azure Ative Diretório. | Sim quando `AADServicePrincipal` se usa a autenticação |
| serviçoPrincipalCredentialType | Especifique o tipo credencial a utilizar para autenticação do comitente do serviço. Os valores permitidos são: **ServicePrincipalKey** ou **ServicePrincipalCert**. | Sim quando `AADServicePrincipal` se usa a autenticação |
| serviçoPrincipalCredential | Especifique a credencial principal do serviço. <br>Ao `ServicePrincipalKey` utilizar como tipo `servicePrincipalCredential` credencial, pode ser uma cadeia (a ADF irá engrafá-la após a implementação de serviços ligados) ou uma referência a um segredo no AKV. <br>Ao `ServicePrincipalCert` utilizar como credencial, `servicePrincipalCredential` deve ser uma referência a um certificado no AKV. | Sim quando `AADServicePrincipal` se usa a autenticação | 
| o nome de utilizador | Especifique o nome do utilizador para se ligar à Dynamics. | Sim quando `Office365` se usa a autenticação |
| palavra-passe | Especifique a palavra-passe para a conta de utilizador especificada para o nome de utilizador. Marque este campo como um SecureString para o armazenar de forma segura na Data Factory, ou [refira um segredo armazenado no Cofre de Chaves Azure](store-credentials-in-key-vault.md). | Sim quando `Office365` se usa a autenticação |
| connectVia | O tempo de [integração](concepts-integration-runtime.md) a ser utilizado para se ligar à loja de dados. Se não especificado, utiliza o tempo de funcionar de integração azure padrão. | Não para fonte, Sim para afundar se o serviço ligado à fonte não tiver um tempo de funcionação de integração |

>[!NOTE]
>O conector Dynamics usado para usar a propriedade opcional "organizationName" para identificar a sua instância Online Dynamics CRM/365. Enquanto continua a funcionar, é-lhe sugerido especificar a nova propriedade "serviceUri" em vez de obter um melhor desempenho, por exemplo, descoberta.

**Exemplo: Dinâmica online utilizando o principal de serviço AAD + autenticação chave**

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
**Exemplo: Dinâmica online utilizando o principal de serviço AAD + autenticação de certificado**

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

**Exemplo: Dinâmica online utilizando autenticação office365**

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

### <a name="dynamics-365-and-dynamics-crm-on-premises-with-ifd"></a>Dinâmica 365 e Dynamics CRM no local com IFD

*Propriedades adicionais que comparam com Dynamics online são "hostName" e "port".*

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade do tipo deve ser definida para **Dynamics,** **DynamicsCrm,** ou **CommonDataServiceForApps**. | Sim |
| implementaçãoType | O tipo de implantação da instância Dynamics. Deve ser **"OnPremisesWithIfd"** para a Dynamics no local com ifd.| Sim |
| nome anfitrião | O nome anfitrião do servidor Dynamics no local. | Sim |
| porta | A porta do servidor Dynamics no local. | Não, o padrão é 443. |
| organizaçãoNome | O nome da organização da instância Dynamics. | Sim |
| authenticationType | O tipo de autenticação para ligar ao servidor Dynamics. Especifique **"Ifd"** para a Dynamics no local com ifd. | Sim |
| o nome de utilizador | Especifique o nome do utilizador para se ligar à Dynamics. | Sim |
| palavra-passe | Especifique a palavra-passe para a conta de utilizador especificada para o nome de utilizador. Pode optar por marcar este campo como um SecureString para o armazenar de forma segura em ADF, ou armazenar senha no Cofre de Chaves Azure e deixar a atividade de cópia puxar a partir daí ao executar cópia de dados - saiba mais com [as credenciais da Loja no Cofre chave](store-credentials-in-key-vault.md). | Sim |
| connectVia | O tempo de [integração](concepts-integration-runtime.md) a ser utilizado para se ligar à loja de dados. Se não especificado, utiliza o tempo de funcionar de integração azure padrão. | Não para a fonte, sim para afundar |

**Exemplo: Dinâmica no local com IFD utilizando autenticação IFD**

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

Para copiar dados de e para a Dynamics, são suportadas as seguintes propriedades.

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade do tipo do conjunto de dados deve ser definida para **DynamicsEntity,** **DynamicsCrmEntity**, ou **CommonDataServiceForAppsEntity**. |Sim |
| entidadeNome | O nome lógico da entidade para recuperar. | Não para a fonte (se for especificada "consulta" na fonte de atividade), Sim para pia |

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

Para obter uma lista completa de secções e imóveis disponíveis para definir atividades, consulte o artigo [Pipelines.](concepts-pipelines-activities.md) Esta secção fornece uma lista de propriedades suportadas por tipos de fonte e pia da Dynamics.

### <a name="dynamics-as-a-source-type"></a>Dinâmica como tipo de fonte

Para copiar dados da Dynamics, as seguintes propriedades são suportadas na secção **de origem** da atividade de cópia.

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade do tipo da fonte de atividade da cópia deve ser definida para **DynamicsSource,** **DynamicsCrmSource**, ou **CommonDataServiceForAppsSource**. | Sim |
| consulta | FetchXML é uma linguagem de consulta proprietária que é usada na Dynamics (online e no local). Veja o seguinte exemplo. Para saber mais, consulte [Construir consultas com fetchXML](https://msdn.microsoft.com/library/gg328332.aspx). | Não (se for especificado "entityName" no conjunto de dados) |

>[!NOTE]
>A coluna PK será sempre copiada mesmo que a projeção da coluna que configurana consulta FetchXML não a contenha.

> [!IMPORTANT]
>- Quando copia dados da Dynamics, o mapeamento explícito da coluna da Dynamics para afundar é opcional, mas altamente reordenado para garantir um resultado de cópia determinista.
>- Ao importar esquema na autoria da UI, a ADF infere o esquema através da amostragem das linhas superiores da consulta Dynamics para inicializar a lista de colunas de origem, caso em que as colunas sem valores nas linhas superiores serão omitidas. O mesmo comportamento aplica-se a execuções de cópia sintetizadas se não houver mapeamento explícito. Você pode rever e adicionar mais colunas no mapeamento, que será homenageado durante o tempo de execução da cópia.

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

### <a name="sample-fetchxml-query"></a>Consulta de FetchXML da amostra

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

### <a name="dynamics-as-a-sink-type"></a>Dinâmica como um tipo de pia

Para copiar dados para a Dynamics, as seguintes propriedades são suportadas na secção de **sink da** atividade de cópia.

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade tipo do sumidouro da atividade de cópia deve ser definida para **DynamicsSink,** **DynamicsCrmSink**, ou **CommonDataServiceForAppsSink**. | Sim |
| escreverComportamento | O comportamento de escrita da operação.<br/>O valor permitido é **"Upsert".** | Sim |
| nome chave alternativo | Especifique o nome chave alternativo definido na sua entidade para executar "Upsert". | Não |
| escreverBatchSize | A contagem de linhas de dados escritos à Dynamics em cada lote. | Não (o padrão é 10) |
| ignorar Valores Nulos | Indica se deve ignorar valores nulos dos dados de entrada (exceto os campos-chave) durante uma operação de escrita.<br/>Os valores permitidos são **verdadeiros** e **falsos.**<br>- **Verdade**: Deixe os dados no objeto de destino inalterados quando fizer uma operação de atualização/atualização. Insira um valor predefinido definido quando fizer uma operação de inserção.<br/>- **Falso**: Atualize os dados no objeto de destino para NULL quando fizer uma operação de atualização/atualização. Insira um valor NULO quando fizer uma operação de inserção. | Não (o padrão é falso) |

>[!NOTE]
>O valor padrão do lavatório "**writeBatchSize**" e a atividade de cópia "**[parallelCopy](copy-activity-performance-features.md#parallel-copy)**" para o lavatório Dynamics são ambos 10. Portanto, 100 registos são submetidos à Dynamics simultaneamente.

Para a Dynamics 365 online, existe um limite de [2 chamadas de lote simultâneopor organização.](https://msdn.microsoft.com/library/jj863631.aspx#Run-time%20limitations) Se esse limite for ultrapassado, uma falha "Server Busy" é lançada antes de o primeiro pedido ser executado. Manter "writeBatchSize" menos ou igual a 10 evitaria tal estrangulamento de chamadas simultâneas.

A combinação ideal de "**writeBatchSize**" e "**parallelCopy**" depende do esquema da sua entidade, por exemplo, número de colunas, tamanho da linha, número de plugins/fluxos de trabalho/atividades de fluxos de trabalho ligados a essas chamadas, etc. A definição padrão de 10 writeBatchSize * 10 paraleloCópias é a recomendação de acordo com o serviço Dynamics, que funcionaria para a maioria das entidades Da Dinâmica, embora possa não ser o melhor desempenho. Pode afinar o desempenho ajustando a combinação nas definições de atividade da cópia.

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

## <a name="data-type-mapping-for-dynamics"></a>Mapeamento de tipo de dados para Dinâmica

Quando copia dados da Dynamics, os seguintes mapeamentos são utilizados desde tipos de dados dynamics para tipos de dados provisórios da Data Factory. Para saber como a atividade da cópia mapeia o esquema de origem e o tipo de dados para a pia, consulte [schema e mapeamentos de tipo](copy-activity-schema-and-type-mapping.md)de dados .

Configure o tipo de dados correspondente da Data Factory numa estrutura de conjunto de dados com base no tipo de dados da dinâmica de origem utilizando a seguinte tabela de mapeamento.

| Tipo de dados de dinâmica | Data Factory tipo de dados provisórios | Apoiado como fonte | Suportado como pia |
|:--- |:--- |:--- |:--- |
| AttributeTypeCode.BigInt | Longo | ✓ | ✓ |
| AttributeTypeCode.Boolean | Booleano | ✓ | ✓ |
| AtributoType.Cliente | GUID | ✓ | |
| AtributoType.DateTime | Datetime | ✓ | ✓ |
| AttributeType.Decimal | Decimal | ✓ | ✓ |
| AtributoType.Double | Double | ✓ | ✓ |
| AttributeType.EntityName | String | ✓ | ✓ |
| AttributeType.Integer | Int32 | ✓ | ✓ |
| AttributeType.Lookup | GUID | ✓ | ✓ (com um único alvo associado) |
| AttributeType.ManagedProperty | Booleano | ✓ | |
| AttributeType.Memo | String | ✓ | ✓ |
| AttributeType.Money | Decimal | ✓ | ✓ |
| AttributeType.Proprietário | GUID | ✓ | |
| AttributeType.Picklist | Int32 | ✓ | ✓ |
| AttributeType.Uniqueidentifier | GUID | ✓ | ✓ |
| AttributeType.String | String | ✓ | ✓ |
| AtributoType.State | Int32 | ✓ | ✓ |
| AttributeType.Status | Int32 | ✓ | ✓ |

> [!NOTE]
> Os tipos de dados da Dinâmica AtributoType.CalendarRules, AttributeType.MultiSelectPicklist e AttributeType.PartyList não são suportados.

## <a name="lookup-activity-properties"></a>Propriedades de atividade de procura

Para saber mais detalhes sobre as propriedades, consulte a [atividade de Lookup.](control-flow-lookup-activity.md)

## <a name="next-steps"></a>Passos seguintes
Para obter uma lista de lojas de dados suportadas como fontes e pias pela atividade de cópia na Data Factory, consulte as lojas de [dados suportadas](copy-activity-overview.md#supported-data-stores-and-formats).
