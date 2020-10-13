---
title: Copiar dados da Lista Online do SharePoint utilizando a Azure Data Factory
description: Saiba como copiar dados da Lista Online do SharePoint para lojas de dados de sumidouros suportados utilizando uma atividade de cópia num pipeline da Azure Data Factory.
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
ms.openlocfilehash: f560a01c4ec00649157a9c43aedf0ed6cfc2e050
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "83871921"
---
# <a name="copy-data-from-sharepoint-online-list-by-using-azure-data-factory"></a>Copiar dados da Lista Online do SharePoint utilizando a Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve como utilizar a Copy Activity na Azure Data Factory para copiar dados da SharePoint Online List. O artigo baseia-se [na Copy Activity in Azure Data Factory,](copy-activity-overview.md)que apresenta uma visão geral da Atividade de Cópia.

## <a name="supported-capabilities"></a>Capacidades suportadas

Este conector da Lista Online SharePoint é suportado para as seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com [matriz de fonte/pia suportada](copy-activity-overview.md)
- [Atividade de procura](control-flow-lookup-activity.md)

Pode copiar dados da Lista Online do SharePoint para qualquer loja de dados de lavatórios suportados. Para obter uma lista de lojas de dados que a Copy Activity suporta como fontes e sumidouros, consulte [lojas e formatos de dados suportados.](copy-activity-overview.md#supported-data-stores-and-formats)

Especificamente, este conector SharePoint List Online utiliza a autenticação principal do serviço e recupera dados através do protocolo OData.

> [!TIP]
> Este conector suporta a cópia de dados da Lista Online do **SharePoint,** mas não o ficheiro. Saiba como copiar ficheiro a partir do ficheiro Copy da secção [SharePoint Online.](#copy-file-from-sharepoint-online)

## <a name="prerequisites"></a>Pré-requisitos

O conector Online SharePoint List utiliza a autenticação principal do serviço para se ligar ao SharePoint. Siga estes passos para o configurar:

1. Registe uma entidade de candidatura no Azure Ative Directory (Azure AD) seguindo [o Registo da sua candidatura junto de um inquilino da AD Azure.](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant) Tome nota dos seguintes valores, que utiliza para definir o serviço ligado:

    - ID da Aplicação
    - Chave de aplicação
    - ID do inquilino

2. Grant SharePoint Permissão do site online para a sua aplicação registada: 

    > [!NOTE]
    > Esta operação requer permissão do proprietário do site SharePoint Online. Pode encontrar o proprietário indo à página inicial do site -> clique nos "X members" no canto direito -> verifique quem tem a função "Proprietário".

    1. Abra o link do site Do SharePoint Online, por exemplo `https://[your_site_url]/_layouts/15/appinv.aspx` (substitua o URL do site).
    2. Pesse no ID da aplicação que registou, preencha os campos vazios e clique em "Criar".

        - Domínio da aplicação: `localhost.com`
        - URL de redirecionamento: `https://www.localhost.com`
        - Pedido de Permissão XML:

        ```xml
        <AppPermissionRequests AllowAppOnlyPolicy="true">
            <AppPermissionRequest Scope="http://sharepoint/content/sitecollection/web" Right="Read"/>
        </AppPermissionRequests>
        ```

        ![autorização de concessão de ponto de partilha](media/connector-sharepoint-online-list/sharepoint-online-grant-permission.png)

    3. Clique em "Trust It" para esta aplicação.

## <a name="get-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As secções seguintes fornecem detalhes sobre propriedades que pode usar para definir entidades da Data Factory que são específicas do conector Da Lista Online SharePoint.

## <a name="linked-service-properties"></a>Propriedades de serviço ligadas

As seguintes propriedades são suportadas para um serviço ligado à Lista Online sharePoint:

| **Propriedade**        | **Descrição**                                              | **Necessário** |
| ------------------- | ------------------------------------------------------------ | ------------ |
| tipo                | A propriedade tipo deve ser definida para: **SharePointOnlineList**.  | Sim          |
| siteUrl             | O url do site SharePoint Online, por `https://contoso.sharepoint.com/sites/siteName` exemplo. | Sim          |
| servicePrincipalId  | O ID de Aplicação (cliente) da aplicação registada no Diretório Ativo Azure. | Sim          |
| servicePrincipalKey | A chave da inscrição. Marque este campo como um **SecureString** para armazená-lo de forma segura na Data Factory, ou [fazer referência a um segredo armazenado no Cofre da Chave Azure](store-credentials-in-key-vault.md). | Sim          |
| inquilinoId            | A identificação do inquilino sob a qual a sua candidatura reside.          | Sim          |
| connectVia          | O [Tempo de Execução de Integração](concepts-integration-runtime.md) para ligar à loja de dados. Saiba mais com [Pré-Requisitos](#prerequisites), mais cedo neste artigo. Se não for especificado, utiliza-se o tempo de execução de integração Azure predefinido. | Não           |

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

Para obter uma lista completa de secções e propriedades disponíveis para definir conjuntos de dados, consulte [Datasets e serviços ligados](concepts-datasets-linked-services.md). A secção seguinte fornece uma lista das propriedades suportadas pelo conjunto de dados da tabela SAP.

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade **tipo** do conjunto de dados deve ser definida para **SharePointOnlineLResource**. | Sim |
| listName | O nome da Lista Online SharePoint. | Sim |

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

## <a name="copy-activity-properties"></a>Propriedades de Atividade de Cópia

Para obter uma lista completa de secções e propriedades disponíveis para definir atividades, consulte [Pipelines](concepts-pipelines-activities.md).  A secção seguinte fornece uma lista dos imóveis suportados pela fonte da Lista Online SharePoint.

### <a name="sharepoint-online-list-as-source"></a>Lista Online do SharePoint como fonte

Para copiar dados da Lista Online do SharePoint, as seguintes propriedades são suportadas na secção **origem** da Atividade de Cópia:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade **tipo** da fonte de Atividade de Cópia deve ser definida para **SharePointOnlineListSource**. | Sim |
| consulta | Opções de consulta personalizadas OData para filtragem de dados. Exemplo: `"$top=10&$select=Title,Number"`. | Não |
| httpRequestTimeout | O tempo limite (em segundo) para o pedido HTTP para obter uma resposta. O padrão é de 300 (5 minutos). | Não |

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

## <a name="data-type-mapping-for-sharepoint-online-list"></a>Mapeamento do tipo de dados para a lista online do SharePoint

Ao copiar dados da Lista Online do SharePoint, são utilizados os seguintes mapeamentos entre os tipos de dados da Lista Online do SharePoint e os tipos de dados provisórios da Azure Data Factory. 

| **Tipo de dados SharePoint Online**                 | **Tipo de dados OData**                                  | **Tipo de dados provisórios da Azure Data Factory** |
| ----------------------------------------------- | ---------------------------------------------------- | ---------------------------------------- |
| Uma linha de texto                             | Edm.String                                           | Cadeia                                   |
| Várias linhas de texto                          | Edm.String                                           | Cadeia                                   |
| Escolha (menu para escolher)                    | Edm.String                                           | Cadeia                                   |
| Número (1, 1.0, 100)                            | Edm.Double                                           | Double (Duplo)                                   |
| Moeda ($, ≤, €)                              | Edm.Double                                           | Double (Duplo)                                   |
| Data e Hora                                   | Edm.DateTime                                         | DateTime                                 |
| Procura (informação já neste site)       | Edm.Int32                                            | Int32                                    |
| Sim/Não (caixa de verificação)                              | Edm.Boolean                                          | Booleano                                  |
| Pessoa ou Grupo                                 | Edm.Int32                                            | Int32                                    |
| Hiperligação ou Imagem                            | Edm.String                                           | Cadeia                                   |
| Calculado (cálculo com base noutras colunas) | Edm.String / Edm.Double / Edm.DateTime / Edm.Boolean | Corda / Duplo / DataTime / Boolean     |
| Anexo                                      | Não suportado                                        |                                          |
| Resultado da Tarefa                                    | Não suportado                                        |                                          |
| Dados Externos                                   | Não suportado                                        |                                          |
| Metadados Geridos                                | Não suportado                                        |                                          |

## <a name="copy-file-from-sharepoint-online"></a>Copy file from SharePoint Online

Pode copiar ficheiros do SharePoint Online utilizando a **atividade Web** para autenticar e captar o token de acesso do SPO, passando depois para a **atividade** de Copy subsequente para copiar dados com **o conector HTTP como fonte**.

![fluxo de ficheiro de cópia de sharepoint](media/connector-sharepoint-online-list/sharepoint-online-copy-file-flow.png)

1. Siga a secção [Pré-Requisitos](#prerequisites) para criar a aplicação AAD e conceder permissão ao SharePoint Online. 

2. Crie uma **Atividade Web** para obter o token de acesso do SharePoint Online:

    - **URL:** `https://accounts.accesscontrol.windows.net/[Tenant-ID]/tokens/OAuth/2` . Substitua a identificação do inquilino.
    - **Método**: POST
    - **Cabeçalhos:**
        - Tipo de conteúdo: aplicação/x-www-form-urlencoded
    - **Corpo:**  `grant_type=client_credentials&client_id=[Client-ID]@[Tenant-ID]&client_secret=[Client-Secret]&resource=00000003-0000-0ff1-ce00-000000000000/[Tenant-Name].sharepoint.com@[Tenant-ID]` . Substitua a identificação do cliente, o segredo do cliente, a identificação do inquilino e o nome do inquilino.

    > [!CAUTION]
    > Descreva a opção Saída Segura para ser verdadeira na atividade web para evitar que o valor simbólico seja registado em texto simples. Quaisquer outras atividades que consumam este valor devem ter a sua opção de Entrada Segura definida como verdadeira.

3. Cadeia com uma **atividade copy** com conector HTTP como fonte para copiar o conteúdo de ficheiros Do SharePoint Online:

    - Serviço ligado HTTP:
        - **URL de base:** `https://[site-url]/_api/web/GetFileByServerRelativeUrl('[relative-path-to-file]')/$value` . Substitua o URL do site e o caminho relativo para arquivar. Amostra rumo ao arquivo como `/sites/site2/Shared Documents/TestBook.xlsx` .
        - **Tipo de autenticação:** Anónimo *(para utilizar o símbolo do Portador configurado na fonte de atividade de cópia mais tarde)*
    - Conjunto de dados: escolha o formato que deseja. Para copiar o ficheiro tal como está, selecione o tipo "Binário".
    - Fonte de atividade de cópia:
        - **Método de pedido**: GET
        - **Cabeçalho adicional**: utilize a seguinte expressão `@{concat('Authorization: Bearer ', activity('<Web-activity-name>').output.access_token)}` , que utiliza o token Do Portador gerado pela atividade web a montante como cabeçalho de autorização. Substitua o nome de atividade web.
    - Configure a atividade da cópia, como de costume.

## <a name="lookup-activity-properties"></a>Propriedades de atividade de procura

Para obter detalhes sobre as propriedades, consulte [a atividade de Lookup](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Passos seguintes

Para obter uma lista de lojas de dados que a Copy Activity suporta como fontes e sumidouros na Azure Data Factory, consulte [lojas e formatos de dados suportados.](copy-activity-overview.md#supported-data-stores-and-formats)
