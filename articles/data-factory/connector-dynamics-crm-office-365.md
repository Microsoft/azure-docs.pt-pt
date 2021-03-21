---
title: Copiar dados em Dinâmica (Serviço Comum de Dados)
description: Saiba como copiar dados do Microsoft Dynamics CRM ou do Microsoft Dynamics 365 (Common Data Service/Microsoft Dataverse) para lojas de dados de sumidouros suportados ou de lojas de dados de origem suportadas para Dynamics CRM ou Dynamics 365 utilizando uma atividade de cópia num oleoduto de fábrica de dados.
ms.service: data-factory
ms.topic: conceptual
ms.author: jingwang
author: linda33wj
ms.custom: seo-lt-2019
ms.date: 03/17/2021
ms.openlocfilehash: ec24fa1bde21c70aa95fc33c92048aebc9f6659c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104597390"
---
# <a name="copy-data-from-and-to-dynamics-365-common-data-servicemicrosoft-dataverse-or-dynamics-crm-by-using-azure-data-factory"></a>Copiar dados de e para a Dynamics 365 (Common Data Service/Microsoft Dataverse) ou Dynamics CRM utilizando a Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve como utilizar uma atividade de cópia na Azure Data Factory para copiar dados de e para o Microsoft Dynamics 365 e Microsoft Dynamics CRM. Baseia-se no artigo [de visão geral](copy-activity-overview.md) da atividade de cópia que apresenta uma visão geral de uma atividade de cópia.

## <a name="supported-capabilities"></a>Capacidades suportadas

Este conector é suportado para as seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com [matriz de origem e pia suportada](copy-activity-overview.md)
- [Atividade de procura](control-flow-lookup-activity.md)

Pode copiar dados da Dynamics 365 (Common Data Service/Microsoft Dataverse) ou do Dynamics CRM para qualquer loja de dados de sumidouro suportado. Também pode copiar dados de qualquer loja de dados de origem suportada para a Dynamics 365 (Common Data Service) ou Dynamics CRM. Para obter uma lista de lojas de dados que uma atividade de cópia suporta como fontes e pias, consulte a tabela [de lojas de dados suportadas.](copy-activity-overview.md#supported-data-stores-and-formats)

Este conector Dynamics suporta as versões Dynamics 7 a 9 para o online e para o local. Mais especificamente:

- Versão 7 mapeia para Dynamics CRM 2015.
- A versão 8 mapeia para Dynamics CRM 2016 e a versão inicial da Dynamics 365.
- A versão 9 mapeia para a versão posterior da Dynamics 365.

Consulte a seguinte tabela de tipos e configurações de autenticação suportada para versões e produtos Dinâmicos.

| Versões dinâmicas | Tipos de autenticação | Amostras de serviço ligadas |
|:--- |:--- |:--- |
| Common Data Service <br/><br/> Dinâmica 365 online <br/><br/> Dynamics CRM Online | Diretor ativo Azure (Azure AD) <br/><br/> Office 365 | [Dinâmica online e Azure AD serviço-principal ou autenticação do Office 365](#dynamics-365-and-dynamics-crm-online) |
| Dinâmica 365 no local com implantação virada para a Internet (IFD) <br/><br/> Dynamics CRM 2016 no local com IFD <br/><br/> Dynamics CRM 2015 no local com IFD | IFD | [Dinâmica no local com autenticação IFD e IFD](#dynamics-365-and-dynamics-crm-on-premises-with-ifd) |

Especificamente para a Dynamics 365, são suportados os seguintes tipos de aplicação:

- Dynamics 365 for Sales
- Dynamics 365 for Customer Service
- Dynamics 365 for Field Service
- Dynamics 365 para Automatização do Serviço do Projeto
- Dynamics 365 for Marketing

Este conector não suporta outros tipos de aplicações como Finanças, Operações e Talento.

>[!TIP]
>Para copiar dados da Dynamics 365 Finanças e Operações, pode utilizar o [conector Dynamics AX](connector-dynamics-ax.md).

Este conector Dynamics é construído em cima da [ferramenta Dynamics XRM](/dynamics365/customer-engagement/developer/build-windows-client-applications-xrm-tools).

## <a name="prerequisites"></a>Pré-requisitos

Para utilizar este conector com a autenticação principal do serviço Azure AD, tem de configurar a autenticação servidor-a-servidor (S2S) no Common Data Service ou Dynamics. Consulte [este artigo](/powerapps/developer/common-data-service/build-web-applications-server-server-s2s-authentication) para obter etapas detalhadas.

## <a name="get-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seguintes secções fornecem detalhes sobre propriedades que são usadas para definir entidades da Data Factory específicas da Dynamics.

## <a name="linked-service-properties"></a>Propriedades de serviço ligadas

As seguintes propriedades são suportadas para o serviço ligado à Dynamics.

### <a name="dynamics-365-and-dynamics-crm-online"></a>Dinâmica 365 e Dinâmica CRM online

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | O tipo de propriedade deve ser definido como "Dynamics", "DynamicsCrm", ou "CommonDataServiceForApps". | Yes |
| tipo de implantação | O tipo de implantação da instância Dynamics. O valor deve ser "Online" para a Dynamics online. | Yes |
| serviceUri | O URL de serviço da sua instância Dynamics, o mesmo que você acede a partir do navegador. Um exemplo é "https:// \<organization-name> .crm[x].dynamics.com". | Yes |
| authenticationType | O tipo de autenticação para ligar a um servidor Dynamics. Os valores válidos são "AADServicePrincipal" e "Office365". | Yes |
| servicePrincipalId | A identificação do cliente da aplicação AD Azure. | Sim, quando a autenticação é "AADServicePrincipal" |
| ServiçoPrincipalCredentialType | O tipo de credencial a utilizar para a autenticação principal de serviço. Os valores válidos são "ServicePrincipalKey" e "ServicePrincipalCert". | Sim, quando a autenticação é "AADServicePrincipal" |
| serviçoPrincipalCredential | A credencial principal de serviço. <br/><br/>Quando utiliza o "ServicePrincipalKey" como tipo credencial, `servicePrincipalCredential` pode ser uma cadeia que a Azure Data Factory encripta após a implementação do serviço ligado. Ou pode ser uma referência a um segredo em Azure Key Vault. <br/><br/>Quando utilizar o "ServicePrincipalCert" como credencial, `servicePrincipalCredential` deve ser uma referência a um certificado no Cofre da Chave Azure. | Sim, quando a autenticação é "AADServicePrincipal" |
| nome de utilizador | O nome de utilizador para ligar à Dynamics. | Sim, quando a autenticação é "Office365" |
| palavra-passe | A palavra-passe para a conta de utilizador que especificou como nome de utilizador. Marque este campo com "SecureString" para armazená-lo de forma segura na Data Factory, ou [fazer referência a um segredo armazenado no Cofre da Chave Azure](store-credentials-in-key-vault.md). | Sim, quando a autenticação é "Office365" |
| connectVia | O [tempo de integração](concepts-integration-runtime.md) a ser utilizado para ligar à loja de dados. Se nenhum valor for especificado, a propriedade utiliza o tempo de execução de integração Azure padrão. | No |

>[!NOTE]
>O conector Dynamics usou anteriormente a propriedade opcional **da organizaçãoName** para identificar a sua instância online Dynamics CRM ou Dynamics 365. Enquanto essa propriedade ainda funciona, sugerimos que especifique a nova propriedade **de ServiceUri** em vez de obter um melhor desempenho, por exemplo, a descoberta.

#### <a name="example-dynamics-online-using-azure-ad-service-principal-and-key-authentication"></a>Exemplo: Dinâmica on-line usando a Azure AD service-principal e autenticação chave

```json
{  
    "name": "DynamicsLinkedService",  
    "properties": {  
        "type": "Dynamics",  
        "typeProperties": {  
            "deploymentType": "Online",  
            "serviceUri": "https://<organization-name>.crm[x].dynamics.com",  
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
#### <a name="example-dynamics-online-using-azure-ad-service-principal-and-certificate-authentication"></a>Exemplo: Dinâmica online utilizando a autenticação principal e autenticação de certificados Azure AD

```json
{ 
    "name": "DynamicsLinkedService", 
    "properties": { 
        "type": "Dynamics", 
        "typeProperties": { 
            "deploymentType": "Online", 
            "serviceUri": "https://<organization-name>.crm[x].dynamics.com", 
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

#### <a name="example-dynamics-online-using-office-365-authentication"></a>Exemplo: Dinâmica online usando a autenticação do Office 365

```json
{
    "name": "DynamicsLinkedService",
    "properties": {
        "type": "Dynamics",
        "typeProperties": {
            "deploymentType": "Online",
            "serviceUri": "https://<organization-name>.crm[x].dynamics.com",
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

Propriedades adicionais que se comparam à Dynamics online são **o hostName** e **o porto.**

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | O tipo de propriedade deve ser definido como "Dynamics", "DynamicsCrm", ou "CommonDataServiceForApps". | Sim. |
| tipo de implantação | O tipo de implantação da instância Dynamics. O valor deve ser "OnPremisesWithIfd" para a Dynamics no local com IFD.| Sim. |
| nome hospedeiro | O nome de anfitrião do servidor Dynamics no local. | Sim. |
| porta | A porta do servidor Dynamics no local. | N.º O valor predefinido é 443. |
| organização Nome | O nome da organização do caso Dynamics. | Sim. |
| authenticationType | O tipo de autenticação para ligar ao servidor Dynamics. Especificar "Ifd" para Dinâmicas no local com IFD. | Sim. |
| nome de utilizador | O nome de utilizador para ligar à Dynamics. | Sim. |
| palavra-passe | A palavra-passe para a conta de utilizador que especificou para o nome de utilizador. Pode marcar este campo com "SecureString" para armazená-lo de forma segura na Data Factory. Ou pode armazenar uma palavra-passe no Key Vault e deixar a atividade de cópia sair daí quando fizer cópia de dados. Saiba mais com as credenciais da [Loja no Key Vault.](store-credentials-in-key-vault.md) | Sim. |
| connectVia | O [tempo de integração](concepts-integration-runtime.md) a ser utilizado para ligar à loja de dados. Se nenhum valor for especificado, a propriedade utiliza o tempo de execução de integração Azure padrão. | No |

#### <a name="example-dynamics-on-premises-with-ifd-using-ifd-authentication"></a>Exemplo: Dinâmica nas instalações com IFD utilizando a autenticação IFD

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

Para copiar dados de e para a Dynamics, suportam-se as seguintes propriedades:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade tipo do conjunto de dados deve ser definida como "DynamicsEntity", "DynamicsCrmEntity", ou "CommonDataServiceForAppsEntity". |Yes |
| entidade Nome | O nome lógico da entidade para recuperar. | Não para a fonte se a fonte de atividade for especificada como "consulta" e sim para pia |

#### <a name="example"></a>Exemplo

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

Para copiar dados da Dynamics, a secção **de origem** da atividade de cópia suporta as seguintes propriedades:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade tipo da fonte de atividade de cópia deve ser definida como "DynamicsSource", "DynamicsCrmSource", ou "CommonDataServiceForAppsSource". | Yes |
| consulta | FetchXML é uma linguagem de consulta proprietária que é usada em Dynamics on-line e no local. Veja o seguinte exemplo. Para saber mais, consulte [Criar consultas com a FetchXML.](/previous-versions/dynamicscrm-2016/developers-guide/gg328332(v=crm.8)) | Não se `entityName` no conjunto de dados for especificado |

>[!NOTE]
>A coluna PK será sempre copiada mesmo que a projeção da coluna que configurar na consulta FetchXML não a contenha.

> [!IMPORTANT]
>- Quando copia dados da Dynamics, o mapeamento de colunas explícitas da Dynamics para afundar é opcional. Mas recomendamos vivamente o mapeamento para garantir um resultado de cópia determinista.
>- Quando a Data Factory importa um esquema na UI autora, infere o esquema. Fá-lo através da amostragem das linhas superiores da consulta Dynamics para inicializar a lista de colunas de origem. Nesse caso, são omitidas colunas sem valores nas linhas superiores. O mesmo comportamento aplica-se a execuções de cópias se não houver mapeamento explícito. Pode rever e adicionar mais colunas no mapeamento, que são honradas durante o tempo de funcionação da cópia.

#### <a name="example"></a>Exemplo

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

Para copiar dados para a Dynamics, a secção **de lavatório** de atividade de cópia suporta as seguintes propriedades:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade tipo do lavatório de atividade de cópia deve ser definida como "DynamicsSink", "DynamicsCrmSink", ou "CommonDataServiceForAppsSink". | Sim. |
| escrever Comportamento | O comportamento de escrita da operação. O valor deve ser "Upsert". | Yes |
| nome alternativoKeyName | O nome de chave alternativa definido na sua entidade para fazer um upsert. | N.º |
| escreverBatchSize | A contagem de dados escrita para a Dynamics em cada lote. | N.º O valor predefinido é 10. |
| ignoreNullValues | Se ignorar valores nulos de dados de entrada que não sejam campos-chave durante uma operação de escrita.<br/><br/>Valores válidos são **VERDADEIROs** e **FALSOS:**<ul><li>**VERDADE**: Deixe os dados no objeto de destino inalterados quando fizer uma operação de atualização ou atualização. Insira um valor predefinido definido quando fizer uma operação de inserção.</li><li>**FALSO:** Atualize os dados no objeto de destino para um valor nulo quando fizer uma operação de atualização ou atualização. Insira um valor nulo quando fizer uma operação de inserção.</li></ul> | N.º O valor **predefinido** é FALSO . |
| maxConcurrentConnections |O limite superior das ligações simultâneas estabelecidas na loja de dados durante a atividade. Especifique um valor apenas quando pretende limitar ligações simultâneas.| No |

>[!NOTE]
>O valor predefinido tanto para o **sink writeBatchSize** como para a atividade de cópia **[paralelosCopias](copy-activity-performance-features.md#parallel-copy)** para o lavatório Dynamics é 10. Portanto, 100 registos são submetidos simultaneamente por defeito à Dynamics.

Para a Dynamics 365 online, há um limite de [duas chamadas de lote simultâneas por organização.](/previous-versions/dynamicscrm-2016/developers-guide/jj863631(v=crm.8)#Run-time%20limitations) Se esse limite for ultrapassado, é lançada uma exceção "Server Busy" antes de ser executado o primeiro pedido. Continue **a escreverBatchSize** a 10 ou menos para evitar tal estrangulamento de chamadas simultâneas.

A combinação ideal de **writeBatchSize** e **parallelCopies** depende do esquema da sua entidade. Os elementos de esquema incluem o número de colunas, tamanho da linha e número de plug-ins, fluxos de trabalho ou atividades de fluxo de trabalho ligados a essas chamadas. A definição predefinição do **writeBatchSize** (10) &times; **parallelCopies** (10) é a recomendação de acordo com o serviço Dynamics. Este valor funciona para a maioria das entidades da Dynamics, embora possa não dar o melhor desempenho. Pode sintonizar o desempenho ajustando a combinação nas definições de atividade da sua cópia.

#### <a name="example"></a>Exemplo

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

## <a name="retrieving-data-from-views"></a>Recuperar dados de pontos de vista

Para obter dados a partir de pontos de vista dynamics, você precisa obter a consulta guardada da vista, e usar a consulta para obter os dados.

Existem duas entidades que armazenam diferentes tipos de visualização: a visualização do sistema de lojas "save-questionry" e a visão do utilizador das lojas "user-questionry". Para obter a informação dos pontos de vista, consulte a seguinte consulta FetchXML e substitua a "TARGETENTITY" com `savedquery` ou `userquery` . Cada tipo de entidade tem mais atributos disponíveis que pode adicionar à consulta com base na sua necessidade. Saiba mais sobre [a entidade savequery](https://docs.microsoft.com/dynamics365/customer-engagement/web-api/savedquery) e [a entidade userquery](https://docs.microsoft.com/dynamics365/customer-engagement/web-api/userquery).

```xml
<fetch top="5000" >
  <entity name="<TARGETENTITY>">
    <attribute name="name" />
    <attribute name="fetchxml" />
    <attribute name="returnedtypecode" />
    <attribute name="querytype" />
  </entity>
</fetch>
```

Também pode adicionar filtros para filtrar as vistas. Por exemplo, adicione o seguinte filtro para obter uma vista chamada "As Minhas Contas Ativas" na entidade de conta.

```xml
<filter type="and" >
    <condition attribute="returnedtypecode" operator="eq" value="1" />
    <condition attribute="name" operator="eq" value="My Active Accounts" />
</filter>
```

## <a name="data-type-mapping-for-dynamics"></a>Mapeamento do tipo de dados para dinâmicas

Ao copiar dados da Dynamics, a tabela seguinte mostra mapeamentos de tipos de dados da Dynamics para tipos de dados provisórios da Data Factory. Para saber como uma atividade de cópia mapeia para um esquema de origem e um tipo de dados mapeia para uma pia, consulte [o Schema e os mapeamentos do tipo de dados](copy-activity-schema-and-type-mapping.md).

Configure o tipo de dados correspondente data factory numa estrutura de conjunto de dados que se baseia no seu tipo de dados Source Dynamics utilizando o seguinte quadro de mapeamento:

| Tipo de dados dinâmicos | Tipo de dados provisórios da Data Factory | Suportado como fonte | Suportado como pia |
|:--- |:--- |:--- |:--- |
| AtributoTypeCode.BigInt | Longo | ✓ | ✓ |
| AtributoTypeCode.Boolean | Booleano | ✓ | ✓ |
| AtributoType.Cliente | GUID | ✓ | ✓ (Ver [orientação)](#writing-data-to-a-lookup-field) |
| AtributoType.DateTime | Datetime | ✓ | ✓ |
| AtributoType.Decimal | Decimal | ✓ | ✓ |
| AtributoType.Double | Double (Duplo) | ✓ | ✓ |
| Nome de AtributoType.EntityName | String | ✓ | ✓ |
| AtributoType.Inteiro | Int32 | ✓ | ✓ |
| AtributoType.Lookup | GUID | ✓ | ✓ (Ver [orientação)](#writing-data-to-a-lookup-field) |
| AtributoType.ManagedProperty | Booleano | ✓ | |
| AtributoType.Memo | String | ✓ | ✓ |
| AtributoType.Dinheiro | Decimal | ✓ | ✓ |
| AtributoType.Proprietário | GUID | ✓ | ✓ (Ver [orientação)](#writing-data-to-a-lookup-field) |
| AtributoType.Picklist | Int32 | ✓ | ✓ |
| AtributoType.Uniqueidentifier | GUID | ✓ | ✓ |
| AtributoType.String | String | ✓ | ✓ |
| AtributoType.State | Int32 | ✓ | ✓ |
| AtributoType.Status | Int32 | ✓ | ✓ |

> [!NOTE]
> Os tipos de dados Dinâmicos **AttributeType.CalendarRules**, **AttributeType.MultiSelectPicklist** e **AttributeType.PartyList** não são suportados.

## <a name="writing-data-to-a-lookup-field"></a>Escrever dados para um campo de procura

Para escrever dados num campo de procura com vários alvos como Cliente e Proprietário, siga esta orientação e o exemplo:

1. Faça com que a sua fonte contenha tanto o valor de campo como o nome da entidade-alvo correspondente.
   - Se todos os registos traçarem o mapa para a mesma entidade-alvo, assegure-se de uma das seguintes condições:
      - Os seus dados de origem têm uma coluna que armazena o nome da entidade-alvo.
      - Adicionou uma coluna adicional na fonte de atividade de cópia para definir a entidade-alvo.
   - Se diferentes registos mapearem para diferentes entidades-alvo, certifique-se de que os seus dados de origem têm uma coluna que armazena o nome da entidade-alvo correspondente.

1. Mapear tanto as colunas de valor como de referência de entidades de origem para afundar. A coluna de referência da entidade deve ser mapeada para uma coluna virtual com o padrão especial de nomeação `{lookup_field_name}@EntityReference` . A coluna não existe na Dynamics. É usado para indicar que esta coluna é a coluna de metadados do campo de procura multitarget.

Por exemplo, assuma que a fonte tem estas duas colunas:

- **Coluna CustomerField** do tipo **GUID,** que é o valor chave primário da entidade-alvo em Dynamics.
- **Coluna-alvo** do tipo **String**, que é o nome lógico da entidade-alvo.

Assuma também que pretende copiar esses dados para o campo da entidade da Sink Dynamics **CustomerField** do tipo **Cliente.**

No mapeamento de colunas de cópia, mapear as duas colunas da seguinte forma:

- **CustomerField** para **CustomerField**. Este mapeamento é o mapeamento normal do campo.
- **Destino** para **CustomerField \@ EntityReference**. A coluna da pia é uma coluna virtual que representa a referência da entidade. Insira tais nomes de campo num mapeamento, já que não aparecem importando esquemas.

![Mapeamento de coluna de campo de procuração dinâmica](./media/connector-dynamics-crm-office-365/connector-dynamics-lookup-field-column-mapping.png)

Se todos os seus registos de origem mapear para a mesma entidade-alvo e os seus dados de origem não contiverem o nome da entidade-alvo, aqui está um atalho: na fonte de atividade de cópia, adicione uma coluna adicional. Nomeie a nova coluna utilizando o `{lookup_field_name}@EntityReference` padrão, desaponte o valor para o nome da entidade-alvo e, em seguida, proceda ao mapeamento da coluna como de costume. Se os nomes das colunas de origem e pia forem idênticos, também pode saltar o mapeamento de colunas explícitas porque a atividade de cópia por colunas de mapas predefinidos pelo nome.

![Campo de procura dinâmica adicionando uma coluna de referência de entidade](./media/connector-dynamics-crm-office-365/connector-dynamics-add-entity-reference-column.png)

## <a name="lookup-activity-properties"></a>Propriedades de atividade de procura

Para obter detalhes sobre as propriedades, consulte [a atividade da Lookup.](control-flow-lookup-activity.md)

## <a name="next-steps"></a>Passos seguintes
Para obter uma lista de lojas de dados, a atividade de cópia na Data Factory suporta como fontes e sumidouros, consulte [lojas de dados suportadas.](copy-activity-overview.md#supported-data-stores-and-formats)