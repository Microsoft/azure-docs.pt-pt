---
title: Copiar dados do Office 365 usando Azure Data Factory
description: Saiba como copiar dados do Office 365 para armazenamentos de dados de coletor com suporte usando a atividade de cópia em um pipeline de Azure Data Factory.
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
ms.openlocfilehash: d97b3caccc92f0fdfeb229d94e30ee6499c26181
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74912410"
---
# <a name="copy-data-from-office-365-into-azure-using-azure-data-factory"></a>Copiar dados do Office 365 para o Azure usando Azure Data Factory

O Azure Data Factory integra- [se com o Microsoft Graph data Connect](https://docs.microsoft.com/graph/data-connect-concept-overview), permitindo que você coloque os dados organizacionais avançados em seu locatário do Office 365 no Azure de uma maneira escalonável e crie aplicativos de análise e extraia informações com base nesses valiosos ativos de dados. A integração com o Privileged Access Management fornece controle de acesso seguro para os valiosos dados organizados no Office 365.  Consulte [este link](https://docs.microsoft.com/graph/data-connect-concept-overview) para obter uma visão geral sobre Microsoft Graph data Connect e consulte [este link](https://docs.microsoft.com/graph/data-connect-policies#licensing) para obter informações de licenciamento.

Este artigo descreve como usar a atividade de cópia no Azure Data Factory para copiar dados do Office 365. Ele se baseia no [copiar descrição geral da atividade](copy-activity-overview.md) artigo apresenta uma visão geral da atividade de cópia.

## <a name="supported-capabilities"></a>Capacidades suportadas
O conector do ADF do Office 365 e o Microsoft Graph data Connect permite a ingestão de escala de diferentes tipos de conjuntos de dados de caixas de correio com email habilitado do Exchange, incluindo contatos do catálogo de endereços, eventos de calendário, mensagens de email, informações do usuário, configurações de caixa de correio e assim por diante.  Consulte [aqui](https://docs.microsoft.com/graph/data-connect-datasets) para ver a lista completa de conjuntos de valores disponíveis.

Por enquanto, em uma única atividade de cópia, você só pode **copiar dados do Office 365 para o [armazenamento de BLOBs do Azure](connector-azure-blob-storage.md), [Azure data Lake Storage Gen1](connector-azure-data-lake-store.md)e [Azure data Lake Storage Gen2](connector-azure-data-lake-storage.md) no formato JSON** (tipo setOfObjects). Se você quiser carregar o Office 365 em outros tipos de armazenamentos de dados ou em outros formatos, você poderá encadear a primeira atividade de cópia com uma atividade de cópia subsequente para carregar mais dados em qualquer um dos [repositórios de destino do ADF com suporte](copy-activity-overview.md#supported-data-stores-and-formats) (consulte a coluna "com suporte como um coletor" na tabela "armazenamentos de dados e formatos com suporte").

>[!IMPORTANT]
>- A assinatura do Azure que contém o data factory e o armazenamento de dados do coletor deve estar no mesmo locatário do Azure Active Directory (AD do Azure) que o locatário do Office 365.
>- Verifique se a região de Azure Integration Runtime usada para a atividade de cópia, bem como o destino, está na mesma região em que a caixa de correio dos usuários do locatário do Office 365 está localizada. Consulte [aqui](concepts-integration-runtime.md#integration-runtime-location) para entender como o local de Azure ir é determinado. Consulte a [tabela aqui](https://docs.microsoft.com/graph/data-connect-datasets#regions) para obter a lista de regiões do Office com suporte e regiões do Azure correspondentes.
>- A autenticação de entidade de serviço é o único mecanismo de autenticação com suporte para armazenamento de BLOBs do Azure, Azure Data Lake Storage Gen1 e Azure Data Lake Storage Gen2 como armazenamentos de destino.

## <a name="prerequisites"></a>Pré-requisitos

Para copiar dados do Office 365 para o Azure, você precisa concluir as seguintes etapas de pré-requisito:

- Seu administrador de locatários do Office 365 deve concluir ações de integração, conforme descrito [aqui](https://docs.microsoft.com/graph/data-connect-get-started).
- Criar e configurar um aplicativo Web do Azure AD no Azure Active Directory.  Para obter instruções, consulte [criar um aplicativo do Azure ad](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application).
- Anote os seguintes valores, que serão usados para definir o serviço vinculado para o Office 365:
    - ID do locatário. Para obter instruções, consulte [obter ID do locatário](../active-directory/develop/howto-create-service-principal-portal.md#get-values-for-signing-in).
    - ID do aplicativo e chave do aplicativo.  Para obter instruções, consulte [obter a ID do aplicativo e a chave de autenticação](../active-directory/develop/howto-create-service-principal-portal.md#get-values-for-signing-in).
- Adicione a identidade do usuário que fará a solicitação de acesso a dados como o proprietário do aplicativo Web do Azure AD (do aplicativo Web do Azure AD > Configurações > proprietários > Adicionar proprietário). 
    - A identidade do usuário deve estar na organização do Office 365 da qual você está obtendo dados e não deve ser um usuário convidado.

## <a name="approving-new-data-access-requests"></a>Aprovando novas solicitações de acesso a dados

Se esta for a primeira vez que você está solicitando dados para esse contexto (uma combinação de qual tabela de dados está sendo acessada, em qual conta de destino os dados estão sendo carregados e qual identidade de usuário está fazendo a solicitação de acesso a dados), você verá o status da atividade de cópia como "em andamento" e somente quando clicar no [link "detalhes" em ações](copy-activity-overview.md#monitoring) , você verá o status como "RequestingConsent".  Um membro do grupo Aprovador de acesso a dados precisa aprovar a solicitação no Privileged Access Management antes que a extração de dados possa continuar.

Consulte [aqui](https://docs.microsoft.com/graph/data-connect-tips#approve-pam-requests-via-office-365-admin-portal) como o aprovador pode aprovar a solicitação de acesso a dados e consulte [aqui](https://docs.microsoft.com/graph/data-connect-pam) uma explicação sobre a integração geral com o Privileged Access Management, incluindo como configurar o grupo de aprovador de acesso a dados.

## <a name="policy-validation"></a>Validação de políticas

Se o ADF for criado como parte de um aplicativo gerenciado e as atribuições de políticas do Azure forem feitas em recursos dentro do grupo de recursos de gerenciamento, para cada execução da atividade de cópia, o ADF verificará se as atribuições de política são impostas. Consulte [aqui](https://docs.microsoft.com/graph/data-connect-policies#policies) para obter uma lista de políticas com suporte.

## <a name="getting-started"></a>Introdução

>[!TIP]
>Para obter uma explicação sobre o uso do conector do Office 365, consulte [carregar dados do artigo do office 365](load-office-365-data.md) .

Você pode criar um pipeline com a atividade de cópia usando uma das ferramentas ou SDKs a seguir. Selecione um link para ir até um tutorial com as instruções detalhadas para criar um pipeline com uma atividade de cópia. 

- [Portal do Azure](quickstart-create-data-factory-portal.md)
- [SDK do .NET](quickstart-create-data-factory-dot-net.md)
- [Python SDK](quickstart-create-data-factory-python.md)
- [O Azure PowerShell](quickstart-create-data-factory-powershell.md)
- [API REST](quickstart-create-data-factory-rest-api.md)
- [Modelo de Azure Resource Manager](quickstart-create-data-factory-resource-manager-template.md). 

As seções a seguir fornecem detalhes sobre as propriedades que são usadas para definir Data Factory entidades específicas para o conector do Office 365.

## <a name="linked-service-properties"></a>Propriedades do serviço ligado

As propriedades a seguir têm suporte para o serviço vinculado do Office 365:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade Type deve ser definida como: **Office365** | Sim |
| office365TenantId | ID de locatário do Azure à qual a conta do Office 365 pertence. | Sim |
| servicePrincipalTenantId | Especifique as informações de locatário sob as quais o aplicativo Web do Azure AD reside. | Sim |
| servicePrincipalId | Especifique o ID de cliente. da aplicação | Sim |
| servicePrincipalKey | Especifique a chave da aplicação. Marque este campo como uma SecureString para armazená-lo com segurança em Data Factory. | Sim |
| connectVia | O Integration Runtime a ser usado para se conectar ao armazenamento de dados.  Se não for especificado, ele usa o padrão do Runtime de integração do Azure. | Não |

>[!NOTE]
> A diferença entre **office365TenantId** e **servicePrincipalTenantId** e o valor correspondente a fornecer:
>- Se você for um desenvolvedor empresarial desenvolvendo um aplicativo em relação aos dados do Office 365 para o uso de sua própria organização, deverá fornecer a mesma ID de locatário para ambas as propriedades, que é a ID de locatário do AAD da sua organização.
>- Se você for um desenvolvedor de ISV desenvolvendo um aplicativo para seus clientes, office365TenantId será a ID de locatário do AAD do seu cliente (instalador do aplicativo) e servicePrincipalTenantId será a ID de locatário do AAD da sua empresa.

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

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Para obter uma lista completa das secções e propriedades disponíveis para definir conjuntos de dados, consulte a [conjuntos de dados](concepts-datasets-linked-services.md) artigo. Esta seção fornece uma lista das propriedades com suporte pelo conjunto de acordos do Office 365.

Para copiar dados do Office 365, há suporte para as seguintes propriedades:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade Type do conjunto de conjuntos deve ser definida como: **Office365Table** | Sim |
| tableName | Nome do conjunto de DataSet a ser extraído do Office 365. Consulte [aqui](https://docs.microsoft.com/graph/data-connect-datasets#datasets) a lista de conjuntos de itens do Office 365 disponíveis para extração. | Sim |

Se você estivesse Configurando `dateFilterColumn`, `startTime`, `endTime`e `userScopeFilterUri` no conjunto de código, ainda há suporte no estado em que se encontra, enquanto você é sugerido para usar o novo modelo na origem da atividade no futuro.

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

Para obter uma lista completa das secções e propriedades disponíveis para a definição de atividades, consulte a [Pipelines](concepts-pipelines-activities.md) artigo. Esta seção fornece uma lista das propriedades com suporte pela origem do Office 365.

### <a name="office-365-as-source"></a>Office 365 como fonte

Para copiar dados do Office 365, há suporte para as seguintes propriedades na seção **origem** da atividade de cópia:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade Type da fonte da atividade de cópia deve ser definida como: **Office365Source** | Sim |
| allowedGroups | Predicado de seleção de grupo.  Use essa propriedade para selecionar até 10 grupos de usuários para os quais os dados serão recuperados.  Se nenhum grupo for especificado, os dados serão retornados para toda a organização. | Não |
| userScopeFilterUri | Quando `allowedGroups` propriedade não é especificada, você pode usar uma expressão de predicado que é aplicada em todo o locatário para filtrar as linhas específicas a serem extraídas do Office 365. O formato do predicado deve corresponder ao formato de consulta de APIs de Microsoft Graph, por exemplo, `https://graph.microsoft.com/v1.0/users?$filter=Department eq 'Finance'`. | Não |
| dateFilterColumn | Nome da coluna de filtro DateTime. Use essa propriedade para limitar o intervalo de tempo para o qual os dados do Office 365 são extraídos. | Sim se o conjunto de informações tiver uma ou mais colunas DateTime. Consulte [aqui](https://docs.microsoft.com/graph/data-connect-filtering#filtering) para obter a lista de conjuntos de valores que exigem este filtro de data e hora. |
| startTime | Inicie o valor DateTime para filtrar. | Sim se `dateFilterColumn` for especificado |
| endTime | Valor DateTime final para filtrar. | Sim se `dateFilterColumn` for especificado |
| outputColumns | Matriz das colunas a serem copiadas para o coletor. | Não |

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
Para obter uma lista dos arquivos de dados suportados como origens e sinks, a atividade de cópia no Azure Data Factory, veja [arquivos de dados suportados](copy-activity-overview.md#supported-data-stores-and-formats).
