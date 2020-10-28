---
title: Copiar dados do Office 365 utilizando a Azure Data Factory
description: Saiba como copiar dados do Office 365 para lojas de dados de sumidouros suportados utilizando a atividade de cópia num pipeline da Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/20/2019
ms.author: jingwang
ms.openlocfilehash: 365896fec555340c3932192aa82086d140d4db0c
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92632996"
---
# <a name="copy-data-from-office-365-into-azure-using-azure-data-factory"></a>Copiar dados do Office 365 para a Azure usando a Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A Azure Data Factory integra-se com a ligação de dados do [Microsoft Graph,](/graph/data-connect-concept-overview)permitindo-lhe trazer os dados organizacionais ricos do seu inquilino office 365 para a Azure de uma forma escalável e construir aplicações de análise e extrair insights com base nestes valiosos ativos de dados. A integração com a Gestão de Acesso Privilegiado proporciona um controlo de acesso seguro para os valiosos dados curados no Office 365.  Consulte [este link](/graph/data-connect-concept-overview) para obter uma visão geral dos dados do Microsoft Graph e consulte [este link](/graph/data-connect-policies#licensing) para obter informações sobre o licenciamento.

Este artigo descreve como utilizar a Atividade de Cópia na Fábrica de Dados Azure para copiar dados do Office 365. Baseia-se no artigo [de visão geral](copy-activity-overview.md) da atividade de cópia que apresenta uma visão geral da atividade da cópia.

## <a name="supported-capabilities"></a>Capacidades suportadas
A ligação de dados do ADF Office 365 e do Microsoft Graph permite, em escala, a ingestão de diferentes tipos de conjuntos de dados a partir de caixas de correio ativadas pelo Exchange Email, incluindo contactos de livros de endereços, eventos de calendário, mensagens de correio eletrónico, informações do utilizador, definições de caixa de correio, e assim por diante.  Consulte [aqui](/graph/data-connect-datasets) a lista completa de conjuntos de dados disponíveis.

Por enquanto, numa única atividade de cópia, só é possível **copiar dados do Office 365 para [o Azure Blob Storage,](connector-azure-blob-storage.md) [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md)e [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) em formato JSON** (conjunto de tipoOfObjects). Se pretender carregar o Office 365 noutros tipos de lojas de dados ou noutros formatos, pode acorrentar a primeira atividade de cópia com uma atividade de cópia subsequente para carregar mais dados em qualquer uma das lojas de [destino ADF suportadas](copy-activity-overview.md#supported-data-stores-and-formats) (consulte a coluna "suportada como pia" na tabela "Lojas de dados e formatos suportados").

>[!IMPORTANT]
>- A assinatura Azure que contém a fábrica de dados e a loja de dados da pia deve estar sob o mesmo inquilino do Azure Ative Directory (Azure AD) como inquilino do Office 365.
>- Certifique-se de que a região de Runtime de Integração Azure utilizada para a atividade de cópias, bem como o destino, está na mesma região onde está localizada a caixa de correio dos utilizadores inquilinos do Office 365. Consulte [aqui](concepts-integration-runtime.md#integration-runtime-location) para entender como é determinada a localização do IR Azure. Consulte [aqui](/graph/data-connect-datasets#regions) a tabela para a lista das regiões apoiadas do Office e das regiões Azure correspondentes.
>- A autenticação principal do serviço é o único mecanismo de autenticação suportado para o Azure Blob Storage, Azure Data Lake Storage Gen1 e Azure Data Lake Storage Gen2 como lojas de destino.

## <a name="prerequisites"></a>Pré-requisitos

Para copiar os dados do Office 365 para a Azure, é necessário completar os seguintes passos pré-requisitos:

- O seu escritório 365 administrador inquilino deve concluir ações de embarque como descrito [aqui](/graph/data-connect-get-started).
- Crie e configuure uma aplicação web Azure AD no Azure Ative Directory.  Para obter instruções, consulte [Criar uma aplicação AD Azure](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal).
- Tome nota dos seguintes valores, que utilizará para definir o serviço ligado para o Office 365:
    - Identificação do inquilino. Para obter instruções, consulte [a identificação do inquilino.](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)
    - ID de aplicação e chave de aplicação.  Para obter instruções, consulte [obter identificação de aplicação e chave de autenticação](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in).
- Adicione a identidade do utilizador que fará o pedido de acesso aos dados como proprietário da aplicação web AZure AD (a partir da aplicação web AD AD Azure > Definições > Proprietários > Adicionar proprietário). 
    - A identidade do utilizador deve estar na organização do Office 365 de que está a receber dados e não deve ser um utilizador convidado.

## <a name="approving-new-data-access-requests"></a>Aprovação de novos pedidos de acesso a dados

Se esta for a primeira vez que solicita dados para este contexto (uma combinação a que a tabela de dados está a ser acedido, qual a conta de destino em que são os dados que estão a ser carregados, e qual a identidade do utilizador que está a fazer o pedido de acesso aos dados), verá o estado da atividade da cópia como "Em Progresso", e só quando clicar no [link "Detalhes" em Ações](copy-activity-overview.md#monitoring) verá o estado como "Solicitação Deconsent".  Um membro do grupo de aprovadores de acesso a dados tem de aprovar o pedido na Gestão de Acesso Privilegiado antes que a extração de dados possa prosseguir.

Consulte [aqui](/graph/data-connect-tips#approve-pam-requests-via-office-365-admin-portal) como o aprovador pode aprovar o pedido de acesso aos dados e consulte [aqui](/graph/data-connect-pam) uma explicação sobre a integração global com a Gestão de Acesso Privilegiado, incluindo como configurar o grupo de aprovadores de acesso a dados.

## <a name="policy-validation"></a>Validação de políticas

Se a ADF for criada como parte de uma aplicação gerida e as atribuições de políticas Azure forem feitas em recursos dentro do grupo de recursos de gestão, então para cada atividade de cópia executada, a ADF verificará se as atribuições políticas são executadas. Consulte [aqui](/graph/data-connect-policies#policies) uma lista de políticas apoiadas.

## <a name="getting-started"></a>Introdução

>[!TIP]
>Para obter uma passagem de utilização do conector do Office 365, consulte os dados de carga do artigo [do Office 365.](load-office-365-data.md)

Pode criar um pipeline com a atividade da cópia utilizando uma das seguintes ferramentas ou SDKs. Selecione um link para ir a um tutorial com instruções passo a passo para criar um pipeline com uma atividade de cópia. 

- [Portal do Azure](quickstart-create-data-factory-portal.md)
- [SDK do .NET](quickstart-create-data-factory-dot-net.md)
- [Python SDK](quickstart-create-data-factory-python.md)
- [Azure PowerShell](quickstart-create-data-factory-powershell.md)
- [API REST](quickstart-create-data-factory-rest-api.md)
- [Modelo de Gestor de Recursos Azure](quickstart-create-data-factory-resource-manager-template.md). 

As seguintes secções fornecem detalhes sobre propriedades que são usadas para definir entidades da Data Factory específicas do conector do Office 365.

## <a name="linked-service-properties"></a>Propriedades de serviço ligadas

As seguintes propriedades são suportadas para o serviço ligado ao Office 365:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade tipo deve ser definida para: **Office365** | Sim |
| office365TenantId | Identificação do inquilino Azure à qual pertence a conta do Office 365. | Sim |
| serviçoPrincipalTenantId | Especifique as informações do inquilino sob as quais a sua aplicação web Azure AD reside. | Sim |
| servicePrincipalId | Especifique a identificação do cliente da aplicação. | Sim |
| servicePrincipalKey | Especifique a chave da aplicação. Marque este campo como um SecureString para armazená-lo de forma segura na Data Factory. | Sim |
| connectVia | O tempo de execução de integração a ser utilizado para ligar à loja de dados.  Se não for especificado, utiliza o tempo de execução de integração Azure predefinido. | Não |

>[!NOTE]
> A diferença entre **office365TenantId** e **servicePrincipalTenantId** e o valor correspondente para fornecer:
>- Se você é um desenvolvedor de empresa desenvolvendo um pedido contra dados do Office 365 para o uso da sua própria organização, então você deve fornecer o mesmo ID do inquilino para ambos os imóveis, que é o ID do inquilino da sua organização.
>- Se você é um desenvolvedor ISV desenvolvendo uma aplicação para seus clientes, então office365TenantId será o ID do inquilino do seu cliente (instalador de aplicações) E o servicePrincipalTenantId será o ID inquilino da sua empresa.

**Exemplo:**

```json
{
    "name": "Office365LinkedService",
    "properties": {
        "type": "Office365",
        "typeProperties": {
            "office365TenantId": "<Office 365 tenant id>",
            "servicePrincipalTenantId": "<AAD app service principal tenant id>",
            "servicePrincipalId": "<AAD app service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<AAD app service principal key>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Dataset properties (Propriedades do conjunto de dados)

Para obter uma lista completa de secções e propriedades disponíveis para definir conjuntos de dados, consulte o artigo [conjuntos de dados.](concepts-datasets-linked-services.md) Esta secção fornece uma lista de propriedades suportadas pelo Conjunto de Dados do Office 365.

Para copiar dados do Office 365, suportam-se as seguintes propriedades:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade do tipo do conjunto de dados deve ser definida para: **Office365Table** | Sim |
| tableName | Nome do conjunto de dados para extrair do Office 365. Consulte [aqui](/graph/data-connect-datasets#datasets) a lista dos conjuntos de dados do Office 365 disponíveis para extração. | Sim |

Se estiver a configurar `dateFilterColumn` , e em conjunto de `startTime` `endTime` `userScopeFilterUri` dados, ainda é suportado como está, enquanto é sugerido que utilize o novo modelo na fonte de atividade que vai para a frente.

**Exemplo**

```json
{
    "name": "DS_May2019_O365_Message",
    "properties": {
        "type": "Office365Table",
        "linkedServiceName": {
            "referenceName": "<Office 365 linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [],
        "typeProperties": {
            "tableName": "BasicDataSet_v0.Event_v1"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy

Para obter uma lista completa de secções e propriedades disponíveis para definir atividades, consulte o artigo [Pipelines.](concepts-pipelines-activities.md) Esta secção fornece uma lista de imóveis suportados pela fonte do Office 365.

### <a name="office-365-as-source"></a>Escritório 365 como fonte

Para copiar dados do Office 365, as seguintes propriedades são suportadas na secção **fonte de origem** da atividade da cópia:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade tipo da fonte de atividade de cópia deve ser definida para: **Office365Source** | Sim |
| permitidos Grupos | Predicado de seleção de grupo.  Utilize esta propriedade para selecionar até 10 grupos de utilizadores para os quais os dados serão recuperados.  Se nenhum grupo for especificado, então os dados serão devolvidos para toda a organização. | Não |
| userScopeFilterUri | Quando `allowedGroups` a propriedade não é especificada, você pode usar uma expressão predicado que é aplicada em todo o inquilino para filtrar as linhas específicas para extrair do Office 365. O formato predicado deve coincidir com o formato de consulta das APIs do Microsoft Graph, por `https://graph.microsoft.com/v1.0/users?$filter=Department eq 'Finance'` exemplo. | Não |
| dataFilterColumn | Nome da coluna do filtro DateTime. Utilize esta propriedade para limitar o intervalo de tempo para o qual os dados do Office 365 são extraídos. | Sim, se o conjunto de dados tiver uma ou mais colunas DateTime. Consulte [aqui](/graph/data-connect-filtering#filtering) a lista de conjuntos de dados que requerem este filtro DateTime. |
| startTime | Valor de início do Tempo de Data para filtrar. | Sim, se `dateFilterColumn` for especificado |
| endTime | Valor de fim de data para filtrar. | Sim, se `dateFilterColumn` for especificado |
| outputColumns | Matriz das colunas para copiar para afundar. | Não |

**Exemplo:**

```json
"activities": [
    {
        "name": "CopyFromO365ToBlob",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Office 365 input dataset name>",
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
                "type": "Office365Source",
                "dateFilterColumn": "CreatedDateTime",
                "startTime": "2019-04-28T16:00:00.000Z",
                "endTime": "2019-05-05T16:00:00.000Z",
                "userScopeFilterUri": "https://graph.microsoft.com/v1.0/users?$filter=Department eq 'Finance'",
                "outputColumns": [
                    {
                        "name": "Id"
                    },
                    {
                        "name": "CreatedDateTime"
                    },
                    {
                        "name": "LastModifiedDateTime"
                    },
                    {
                        "name": "ChangeKey"
                    },
                    {
                        "name": "Categories"
                    },
                    {
                        "name": "OriginalStartTimeZone"
                    },
                    {
                        "name": "OriginalEndTimeZone"
                    },
                    {
                        "name": "ResponseStatus"
                    },
                    {
                        "name": "iCalUId"
                    },
                    {
                        "name": "ReminderMinutesBeforeStart"
                    },
                    {
                        "name": "IsReminderOn"
                    },
                    {
                        "name": "HasAttachments"
                    },
                    {
                        "name": "Subject"
                    },
                    {
                        "name": "Body"
                    },
                    {
                        "name": "Importance"
                    },
                    {
                        "name": "Sensitivity"
                    },
                    {
                        "name": "Start"
                    },
                    {
                        "name": "End"
                    },
                    {
                        "name": "Location"
                    },
                    {
                        "name": "IsAllDay"
                    },
                    {
                        "name": "IsCancelled"
                    },
                    {
                        "name": "IsOrganizer"
                    },
                    {
                        "name": "Recurrence"
                    },
                    {
                        "name": "ResponseRequested"
                    },
                    {
                        "name": "ShowAs"
                    },
                    {
                        "name": "Type"
                    },
                    {
                        "name": "Attendees"
                    },
                    {
                        "name": "Organizer"
                    },
                    {
                        "name": "WebLink"
                    },
                    {
                        "name": "Attachments"
                    },
                    {
                        "name": "BodyPreview"
                    },
                    {
                        "name": "Locations"
                    },
                    {
                        "name": "OnlineMeetingUrl"
                    },
                    {
                        "name": "OriginalStart"
                    },
                    {
                        "name": "SeriesMasterId"
                    }
                ]
            },
            "sink": {
                "type": "BlobSink"
            }
        }
    }
]
```

## <a name="next-steps"></a>Passos seguintes
Para obter uma lista de lojas de dados suportadas como fontes e sumidouros pela atividade de cópia na Azure Data Factory, consulte lojas de [dados suportadas.](copy-activity-overview.md#supported-data-stores-and-formats)