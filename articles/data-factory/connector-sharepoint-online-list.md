---
title: Copiar dados da Lista Online SharePoint utilizando a Azure Data Factory
description: Saiba como copiar dados da SharePoint Online List para lojas de dados de sink suportadas utilizando uma atividade de cópia num pipeline azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: jingwang
ms.openlocfilehash: 02b88ae0fa0473ad3d11346f0443582d80e75f5d
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83691132"
---
# <a name="copy-data-from-sharepoint-online-list-by-using-azure-data-factory"></a>Copiar dados da Lista Online SharePoint utilizando a Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve como usar a Atividade copiadora na Fábrica de Dados Azure para copiar dados da SharePoint Online List. O artigo baseia-se [na Copy Activity na Azure Data Factory,](copy-activity-overview.md)que apresenta uma visão geral da Atividade de Cópia.

## <a name="supported-capabilities"></a>Capacidades suportadas

Este conector da Lista Online SharePoint é suportado para as seguintes atividades:

- [Copiar atividade](copy-activity-overview.md) com matriz de [origem/pia suportada](copy-activity-overview.md)
- [Atividade de procura](control-flow-lookup-activity.md)

Pode copiar dados da Lista Online sharePoint para qualquer loja de dados de sink suportado. Para obter uma lista de lojas de dados que a Copy Activity suporta como fontes e afunda, consulte [lojas e formatos de dados suportados.](copy-activity-overview.md#supported-data-stores-and-formats)

Especificamente, este conector SharePoint List Online utiliza a autenticação principal do serviço e recupera dados através do protocolo OData.

> [!TIP]
> Este conector suporta copiar dados da **Lista** Online sharePoint, mas não arquivar. Saiba como copiar ficheiro [sacar ficheiro sacar da secção SharePoint Online.](#copy-file-from-sharepoint-online)

## <a name="prerequisites"></a>Pré-requisitos

O conector SharePoint List Online utiliza a autenticação principal do serviço para ligar ao SharePoint. Siga estes passos para configurar:

1. Registe uma entidade de candidatura no Azure Ative Directory (Azure AD) seguindo o registo da sua candidatura junto de [um inquilino da Azure AD](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant). Tome nota dos seguintes valores, que utiliza para definir o serviço vinculado:

    - ID da Aplicação
    - Chave de aplicação
    - ID do inquilino

2. Grant SharePoint Online permissão de site para a sua aplicação registada: 

    > [!NOTE]
    > Esta operação requer permissão do proprietário do site SharePoint Online. Você pode encontrar o proprietário indo para a página inicial do site - > clique nos "x membros" no canto direito - > verificar quem tem o papel de "Proprietário".

    1. Open SharePoint Online link site por exemplo `https://[your_site_url]/_layouts/15/appinv.aspx` (substitua o nome do inquilino e do site).
    2. Pesquise o ID de aplicação que registou, preencha os campos vazios e clique em "Criar".

        - Domínio da aplicação: localhost.com
        - URL de redirecionamento:https://www.localhost.com
        - Pedido de Permissão XML:

        ```xml
        <AppPermissionRequests AllowAppOnlyPolicy="true">
            <AppPermissionRequest Scope="http://sharepoint/content/sitecollection/web" Right="Read"/>
        </AppPermissionRequests>
        ```

        ![autorização de concessão de sharepoint](media/connector-sharepoint-online-list/sharepoint-online-grant-permission.png)

    3. Clique em "Trust It" para esta aplicação.

## <a name="get-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seguintes secções fornecem detalhes sobre propriedades que pode utilizar para definir entidades da Fábrica de Dados específicas do conector da Lista Online SharePoint.

## <a name="linked-service-properties"></a>Propriedades de serviço seletos

As seguintes propriedades são suportadas para um serviço ligado à Lista Online SharePoint:

| **Propriedade**        | **Descrição**                                              | **Necessário** |
| ------------------- | ------------------------------------------------------------ | ------------ |
| tipo                | A propriedade do tipo deve ser definida para: **SharePointOnlineList**.  | Sim          |
| siteUrl             | O url do site Online SharePoint, por `https://contoso.sharepoint.com/sites/siteName` exemplo. | Sim          |
| serviçoPrincipalId  | O ID de Aplicação (cliente) da aplicação registada no Diretório Ativo azure. | Sim          |
| serviçoPrincipalKey | A chave da aplicação. Marque este campo como um **SecureString** para o armazenar de forma segura na Data Factory, ou [refira um segredo armazenado no Cofre de Chaves Azure](store-credentials-in-key-vault.md). | Sim          |
| inquilinoId            | A identificação do inquilino sob a qual o seu pedido reside.          | Sim          |
| connectVia          | O Tempo de [Integração](concepts-integration-runtime.md) para utilizar para ligar à loja de dados. Saiba mais com [os Pré-Requisitos,](#prerequisites)no início deste artigo. Se não especificado, é utilizado o tempo de execução de integração azure padrão. | Não           |

**Exemplo:**

```json
{
    "name": "SharePointOnlineList",
    "properties": {
        "type": "SharePointOnlineList",
        "typeProperties": {
            "siteUrl": "<site URL>",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenantId": "<tenant ID>"
        }
    }
}
```

## <a name="dataset-properties"></a>Dataset properties (Propriedades do conjunto de dados)

Para obter uma lista completa de secções e propriedades disponíveis para definir conjuntos de dados, consulte [Datasets e serviços ligados.](concepts-datasets-linked-services.md) A secção seguinte fornece uma lista das propriedades suportadas pelo conjunto de dados da tabela SAP.

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade do **tipo** do conjunto de dados deve ser definida para **SharePointOnlineLResource**. | Sim |
| nome da lista | O nome da Lista Online SharePoint. | Sim |

**Exemplo**

```json
{
    "name": "SharePointOnlineListDataset",
    "properties":
    {
        "type": "SharePointOnlineListResource",
        "linkedServiceName": {
            "referenceName": "<SharePoint Online List linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties":
        {
            "listName": "<name of the list>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades de Copy Activity

Para obter uma lista completa de secções e propriedades disponíveis para definir atividades, consulte [Pipelines](concepts-pipelines-activities.md).  A seguinte secção fornece uma lista das propriedades suportadas pela fonte da Lista Online SharePoint.

### <a name="sharepoint-online-list-as-source"></a>Lista Online SharePoint como fonte

Para copiar dados da Lista Online SharePoint, as seguintes propriedades são suportadas na secção fonte de **origem** da Copy Activity:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade do **tipo** da fonte de atividade de cópia deve ser definida para **SharePointOnlineListSource**. | Sim |
| consulta | Opções personalizadas de consulta OData para filtrar dados. Exemplo: `"$top=10&$select=Title,Number"`. | Não |
| httpRequestTimeout | O prazo (em segundo) para o pedido http para obter uma resposta. Predefinido é de 300 (5 minutos). | Não |

**Exemplo**

```json
"activities":[
    {
        "name": "CopyFromSharePointOnlineList",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SharePoint Online List input dataset name>",
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
            "source": {
                "type": "SharePointOnlineListSource",
                "query": "<OData query e.g. $top=10&$select=Title,Number>"
            }, 
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-sharepoint-online-list"></a>Mapeamento de tipo de dados para lista online sharePoint

Quando copia dados da Lista Online sharePoint, os seguintes mapeamentos são utilizados entre os tipos de dados da Lista Online SharePoint e os tipos de dados provisórios da Azure Data Factory. 

| **Tipo de dados online SharePoint**                 | **Tipo de dados OData**                                  | **Tipo de dados provisórios da Azure Data Factory** |
| ----------------------------------------------- | ---------------------------------------------------- | ---------------------------------------- |
| Uma linha de texto                             | Edm.String                                           | String                                   |
| Várias linhas de texto                          | Edm.String                                           | String                                   |
| Escolha (menu à escolha)                    | Edm.String                                           | String                                   |
| Número (1, 1.0, 100)                            | Edm.Double                                           | Double                                   |
| Moeda ($, ¥, €)                              | Edm.Double                                           | Double                                   |
| Data e Hora                                   | Edm.DateTime                                         | DateTime                                 |
| Procura (informação já neste site)       | Edm.Int32                                            | Int32                                    |
| Sim/Não (caixa de verificação)                              | Edm.Boolean                                          | Booleano                                  |
| Pessoa ou Grupo                                 | Edm.Int32                                            | Int32                                    |
| Hiperligação ou Imagem                            | Edm.String                                           | String                                   |
| Calculado (cálculo com base noutras colunas) | Edm.String / Edm.Double / Edm.DateTime / Edm.Boolean | Corda / Duplo / Data / Boolean     |
| Anexo                                      | Não suportado                                        |                                          |
| Resultado da Tarefa                                    | Não suportado                                        |                                          |
| Dados Externos                                   | Não suportado                                        |                                          |
| Metadados Geridos                                | Não suportado                                        |                                          |

## <a name="copy-file-from-sharepoint-online"></a>Copiar ficheiro do SharePoint Online

Pode copiar ficheiros do SharePoint Online utilizando a **atividade web** para autenticar e agarrar o sinal de acesso do SPO, passando depois para a **atividade** de Cópia subsequente para copiar dados com **o conector HTTP como fonte**.

![fluxo de ficheiro de cópia de sharepoint](media/connector-sharepoint-online-list/sharepoint-online-copy-file-flow.png)

1. Siga a secção [pré-requisitos](#prerequisites) para criar aplicação AAD e conceder permissão ao SharePoint Online. 

2. Criar uma **Atividade Web** para obter o token de acesso do SharePoint Online:

    - **URL**: `https://accounts.accesscontrol.windows.net/[Tenant-ID]/tokens/OAuth/2` . Substitua a identificação do inquilino.
    - **Método**: POST
    - **Cabeçalhos:**
        - Tipo de conteúdo: aplicação/x-www-form-urlencoded
    - **Corpo:** `grant_type=client_credentials&client_id=[Client-ID]@[Tenant-ID]&client_secret=[Client-Secret]&resource=00000003-0000-0ff1-ce00-000000000000/[Tenant-Name].sharepoint.com@[Tenant-ID]` . Substitua a identificação do cliente, o segredo do cliente, a identificação do inquilino e o nome do inquilino.

    > [!CAUTION]
    > Delineie a opção De Saída Segura como verdadeira na atividade web para evitar que o valor simbólico seja registado em texto simples. Quaisquer outras atividades que consumam este valor devem ter a sua opção De Entrada Segura definida como verdadeira.

3. Cadeia com uma **atividade de Cópia** com conector HTTP como fonte para copiar conteúdo de ficheiro SharePoint Online:

    - Serviço ligado http:
        - **URL base:** `https://[site-url]/_api/web/GetFileByServerRelativeUrl('[relative-path-to-file]')/$value` . Substitua o URL do site e o caminho relativo para arquivar. Prove o caminho relativo para arquivar como `/sites/site2/Shared Documents/TestBook.xlsx` .
        - **Tipo de autenticação:** Anónimo *(para usar o token Bearer configurado na fonte* de atividade de cópia mais tarde)
    - Conjunto de dados: escolha o formato que deseja. Para copiar o ficheiro como está, selecione o tipo "Binário".
    - Fonte de atividade de cópia:
        - **Método de pedido**: GET
        - **Cabeçalho adicional:** utilize a seguinte expressão `@{concat('Authorization: Bearer ', activity('<Web-activity-name>').output.access_token)}` , que utiliza o token Bearer gerado pela atividade web a montante como cabeçalho de autorização. Substitua o nome da atividade web.
    - Configure a atividade da cópia como de costume.

## <a name="lookup-activity-properties"></a>Propriedades de atividade de procura

Para saber mais detalhes sobre as propriedades, consulte a [atividade de Lookup.](control-flow-lookup-activity.md)

## <a name="next-steps"></a>Passos seguintes

Para obter uma lista de lojas de dados que a Copy Activity suporta como fontes e afunda na Azure Data Factory, consulte [lojas e formatos de dados suportados.](copy-activity-overview.md#supported-data-stores-and-formats)
