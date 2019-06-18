---
title: Copiar dados do Office 365 com o Azure Data Factory | Documentos da Microsoft
description: Saiba como copiar dados do Office 365 para arquivos de dados de sink suportados através da atividade de cópia num pipeline do Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: jingwang
ms.openlocfilehash: 1a8d622aa280794d9a4d6fe7320ddcc21ac044f4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66475662"
---
# <a name="copy-data-from-office-365-into-azure-using-azure-data-factory"></a>Copiar dados do Office 365 no Azure com o Azure Data Factory

O Azure Data Factory se integra [ligar a dados do Microsoft Graph](https://docs.microsoft.com/graph/data-connect-concept-overview), permitindo-lhe colocar a avançada dados organizacionais no seu Office 365 de inquilino para o Azure de uma forma escalável e criem aplicações de análise e extrair informações com base em Estes recursos de dados valiosos. Integração com o Privileged Access Management fornece controlo de acesso protegido para os valiosos dados organizados no Office 365.  Consulte a [esta ligação](https://docs.microsoft.com/graph/data-connect-concept-overview) para uma descrição geral de dados do Microsoft Graph ligar e consultar [esta ligação](https://docs.microsoft.com/graph/data-connect-policies#licensing) para obter informações de licenciamento.

Este artigo descreve como utilizar a atividade de cópia no Azure Data Factory para copiar dados do Office 365. Ele se baseia no [copiar descrição geral da atividade](copy-activity-overview.md) artigo apresenta uma visão geral da atividade de cópia.

## <a name="supported-capabilities"></a>Capacidades suportadas
Conector do Office 365 do ADF e dados do Microsoft Graph estabelecer a ligação ativa na ingestão de dimensionamento de diferentes tipos de conjuntos de dados de caixas de correio de E-Mail do Exchange ativada, incluindo contatos do livro de endereços, eventos do calendário, mensagens de e-mail, informações de utilizador, as definições de caixa de correio, e assim por diante.  Consultar [aqui](https://docs.microsoft.com/graph/data-connect-datasets) para ver a lista completa de conjuntos de dados disponíveis.

Por agora, dentro de uma atividade de cópia única só é possível **copiar dados do Office 365 numa [armazenamento de Blobs do Azure](connector-azure-blob-storage.md), [Gen1 de armazenamento do Azure Data Lake](connector-azure-data-lake-store.md), e [Gen2 de armazenamento do Azure Data Lake ](connector-azure-data-lake-storage.md) no formato JSON** (digite setOfObjects). Se pretender carregar do Office 365 para outros tipos de arquivos de dados ou em outros formatos, pode encadear a primeira atividade de cópia com uma atividade de cópia subsequentes para ainda mais carregar dados para qualquer um da [arquivos de destino do ADF suportados](copy-activity-overview.md#supported-data-stores-and-formats) (consulte" suportado como sink"coluna na tabela"Suporte a arquivos de dados e formatos").

>[!IMPORTANT]
>- A subscrição do Azure que contém a fábrica de dados e o arquivo de dados de sink tem de estar no mesmo inquilino do Azure Active Directory (Azure AD) como inquilino do Office 365.
>- Certifique-se a região do Runtime de integração do Azure utilizada para atividade de cópia, bem como o destino está na mesma região onde está localizada a caixa de correio dos utilizadores de inquilino do Office 365. Consultar [aqui](concepts-integration-runtime.md#integration-runtime-location) para compreender como é determinada a localização do Runtime de integração do Azure. Consulte a [aqui de tabela](https://docs.microsoft.com/graph/data-connect-datasets#regions) para a lista de regiões suportadas do Office e regiões do Azure correspondentes.
>- A autenticação do Principal de serviço é o único mecanismo de autenticação suportado para o armazenamento de Blobs do Azure, Gen1 de armazenamento do Azure Data Lake e geração 2 de armazenamento do Azure Data Lake como arquivos de destino.

## <a name="prerequisites"></a>Pré-requisitos

Para copiar dados do Office 365 no Azure, tem de concluir os passos de pré-requisitos seguintes:

- O administrador do inquilino do Office 365 tem de concluir as ações de integração conforme descrito [aqui](https://docs.microsoft.com/graph/data-connect-get-started).
- Criar e configurar uma aplicação web do Azure AD no Azure Active Directory.  Para obter instruções, consulte [criar uma aplicação do Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application).
- Tome nota dos seguintes valores, que irá utilizar para definir o serviço ligado para o Office 365:
    - ID do inquilino. Para obter instruções, consulte [obter ID de inquilino](../active-directory/develop/howto-create-service-principal-portal.md#get-values-for-signing-in).
    - Chave de ID da aplicação e a aplicação.  Para obter instruções, consulte [chave de ID e a autenticação da aplicação Get](../active-directory/develop/howto-create-service-principal-portal.md#get-values-for-signing-in).
- Adicionar a identidade do utilizador que está a definir o pedido de acesso de dados como o proprietário do aplicativo web do Azure AD (do Azure AD da aplicação web > Definições > proprietários > Adicionar proprietário). 
    - A identidade do utilizador tem de ser na organização do Office 365 estão a obter dados a partir de e não pode ser um utilizador convidado.

## <a name="approving-new-data-access-requests"></a>Aos novos pedidos de acesso de dados

Se esta for a primeira vez que está a solicitar dados para este contexto (uma combinação de quais dados tabela está a ser acesso, o destino conta é os dados a ser carregados para, e que identidade de utilizador está a tornar os dados de pedido de acesso), verá que a atividade de cópia Estado como "Em curso", e somente quando clicar em [link "Detalhes" em ações](copy-activity-overview.md#monitoring) irá ver o estado como "RequestingConsent".  Um membro do grupo de aprovador de acesso de dados tem de aprovar o pedido de Privileged Access Management antes de pode continuar a extração de dados.

Consultar [aqui](https://docs.microsoft.com/graph/data-connect-tips#approve-pam-requests-via-office-365-admin-portal) sobre como o aprovador pode aprovar os dados de pedido de acesso e consulte [aqui](https://docs.microsoft.com/graph/data-connect-pam) para obter uma explicação sobre a integração geral com o Privileged Access Management, incluindo como configurar os dados grupo de aprovador de acesso.

## <a name="policy-validation"></a>Validação de diretivas

Se ADF é criado como parte de uma aplicação gerida e atribuições de políticas do Azure são feitas nos recursos no grupo de recursos de gerenciamento, em seguida, para cada atividade de cópia executa, ADF irá verificar para se certificar de que as atribuições de política são impostas. Consultar [aqui](https://docs.microsoft.com/graph/data-connect-policies#policies) para obter uma lista de políticas suportadas.

## <a name="getting-started"></a>Introdução

>[!TIP]
>Para obter instruções para utilizar o conector do Office 365, consulte [carregar dados a partir do Office 365](load-office-365-data.md) artigo.

Pode criar um pipeline com a atividade de cópia utilizando um dos seguintes ferramentas ou SDKs. Selecione uma ligação para ir para um tutorial com instruções passo a passo para criar um pipeline com uma atividade de cópia. 

- [Portal do Azure](quickstart-create-data-factory-portal.md)
- [SDK do .NET](quickstart-create-data-factory-dot-net.md)
- [Python SDK](quickstart-create-data-factory-python.md)
- [Azure PowerShell](quickstart-create-data-factory-powershell.md)
- [API REST](quickstart-create-data-factory-rest-api.md)
- [Modelo Azure Resource Manager](quickstart-create-data-factory-resource-manager-template.md). 

As secções seguintes fornecem detalhes sobre as propriedades que são utilizadas para definir entidades do Data Factory específicas para o conector do Office 365.

## <a name="linked-service-properties"></a>Propriedades do serviço ligado

As seguintes propriedades são suportadas para o serviço do Office 365 ligado:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | A propriedade de tipo tem de ser definida como: **Office365** | Sim |
| office365TenantId | ID de inquilino do Azure à qual pertence a conta do Office 365. | Sim |
| servicePrincipalTenantId | Especifique as informações de inquilino no qual reside o aplicativo de web do Azure AD. | Sim |
| servicePrincipalId | Especifique o ID de cliente. da aplicação | Sim |
| servicePrincipalKey | Especifique a chave da aplicação. Marca esse campo como uma SecureString armazena de forma segura no Data Factory. | Sim |
| connectVia | O Runtime de integração a ser utilizado para ligar ao arquivo de dados.  Se não for especificado, ele usa o padrão do Runtime de integração do Azure. | Não |

>[!NOTE]
> A diferença entre **office365TenantId** e **servicePrincipalTenantId** e o valor correspondente para fornecer:
>- Se for um programador empresarial desenvolver uma aplicação face a dados do Office 365 para a utilização da sua organização, em seguida, deve fornecer o mesmo inquilino ID para ambas as propriedades, que é o inquilino do AAD da sua organização ID.
>- Se for um desenvolvedor ISV desenvolvendo um aplicativo para os seus clientes office365TenantId será do seu cliente (instalador de aplicativo) o ID de inquilino do AAD e servicePrincipalTenantId serão ID de inquilino do AAD de. da sua empresa

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

Para obter uma lista completa das secções e propriedades disponíveis para definir conjuntos de dados, consulte a [conjuntos de dados](concepts-datasets-linked-services.md) artigo. Esta seção fornece uma lista de propriedades suportadas pelo conjunto de dados do Office 365.

Para copiar dados do Office 365, são suportadas as seguintes propriedades:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | A propriedade de tipo do conjunto de dados deve ser definida como: **Office365Table** | Sim |
| tableName | Nome do conjunto de dados para extrair a partir do Office 365. Consultar [aqui](https://docs.microsoft.com/graph/data-connect-datasets#datasets) para obter a lista de conjuntos de dados do Office 365 disponíveis para extração. | Sim |
| allowedGroups | Predicado de seleção de grupo.  Utilize esta propriedade para selecionar até 10 grupos de utilizadores aos quais os dados serão recuperados.  Se forem especificados não existem grupos, em seguida, os dados serão devolvidos para toda a organização. | Não |
| userScopeFilterUri | Quando `allowedGroups` propriedade não for especificada, pode usar uma expressão de predicado que é aplicada em todo o inquilino para filtrar as linhas específicas para extrair a partir do Office 365. O formato de predicado deve corresponder ao formato de consulta do Microsoft Graph APIs, por exemplo, `https://graph.microsoft.com/v1.0/users?$filter=Department eq 'Finance'`. | Não |
| dateFilterColumn | Nome da coluna de filtro de DateTime. Use essa propriedade para limitar o intervalo de tempo para o Office 365 os dados são extraídos. | Sim, se o conjunto de dados tem uma ou mais colunas de DateTime. Consultar [aqui](https://docs.microsoft.com/graph/data-connect-filtering#filtering) para obter a lista de conjuntos de dados que requerem este filtro de DateTime. |
| startTime | Inicie o valor de DateTime para filtrar. | Se Sim `dateFilterColumn` é especificado |
| endTime | Termina o valor de DateTime para filtrar. | Se Sim `dateFilterColumn` é especificado |

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
        "structure": [
            {
                "name": "Id",
                "type": "String",
                "description": "The unique identifier of the event."
            },
            {
                "name": "CreatedDateTime",
                "type": "DateTime",
                "description": "The date and time that the event was created."
            },
            {
                "name": "LastModifiedDateTime",
                "type": "DateTime",
                "description": "The date and time that the event was last modified."
            },
            {
                "name": "ChangeKey",
                "type": "String",
                "description": "Identifies the version of the event object. Every time the event is changed, ChangeKey changes as well. This allows Exchange to apply changes to the correct version of the object."
            },
            {
                "name": "Categories",
                "type": "String",
                "description": "The categories associated with the event. Format: ARRAY<STRING>"
            },
            {
                "name": "OriginalStartTimeZone",
                "type": "String",
                "description": "The start time zone that was set when the event was created. See DateTimeTimeZone for a list of valid time zones."
            },
            {
                "name": "OriginalEndTimeZone",
                "type": "String",
                "description": "The end time zone that was set when the event was created. See DateTimeTimeZone for a list of valid time zones."
            },
            {
                "name": "ResponseStatus",
                "type": "String",
                "description": "Indicates the type of response sent in response to an event message. Format: STRUCT<Response: STRING, Time: STRING>"
            },
            {
                "name": "iCalUId",
                "type": "String",
                "description": "A unique identifier that is shared by all instances of an event across different calendars."
            },
            {
                "name": "ReminderMinutesBeforeStart",
                "type": "Int32",
                "description": "The number of minutes before the event start time that the reminder alert occurs."
            },
            {
                "name": "IsReminderOn",
                "type": "Boolean",
                "description": "Set to true if an alert is set to remind the user of the event."
            },
            {
                "name": "HasAttachments",
                "type": "Boolean",
                "description": "Set to true if the event has attachments."
            },
            {
                "name": "Subject",
                "type": "String",
                "description": "The text of the event's subject line."
            },
            {
                "name": "Body",
                "type": "String",
                "description": "The body of the message associated with the event.Format: STRUCT<ContentType: STRING, Content: STRING>"
            },
            {
                "name": "Importance",
                "type": "String",
                "description": "The importance of the event: Low, Normal, High."
            },
            {
                "name": "Sensitivity",
                "type": "String",
                "description": "Indicates the level of privacy for the event: Normal, Personal, Private, Confidential."
            },
            {
                "name": "Start",
                "type": "String",
                "description": "The start time of the event. Format: STRUCT<DateTime: STRING, TimeZone: STRING>"
            },
            {
                "name": "End",
                "type": "String",
                "description": "The date and time that the event ends. Format: STRUCT<DateTime: STRING, TimeZone: STRING>"
            },
            {
                "name": "Location",
                "type": "String",
                "description": "Location information of the event. Format: STRUCT<DisplayName: STRING, Address: STRUCT<Street: STRING, City: STRING, State: STRING, CountryOrRegion: STRING, PostalCode: STRING>, Coordinates: STRUCT<Altitude: DOUBLE, Latitude: DOUBLE, Longitude: DOUBLE, Accuracy: DOUBLE, AltitudeAccuracy: DOUBLE>>"
            },
            {
                "name": "IsAllDay",
                "type": "Boolean",
                "description": "Set to true if the event lasts all day. Adjusting this property requires adjusting the Start and End properties of the event as well."
            },
            {
                "name": "IsCancelled",
                "type": "Boolean",
                "description": "Set to true if the event has been canceled."
            },
            {
                "name": "IsOrganizer",
                "type": "Boolean",
                "description": "Set to true if the message sender is also the organizer."
            },
            {
                "name": "Recurrence",
                "type": "String",
                "description": "The recurrence pattern for the event. Format: STRUCT<Pattern: STRUCT<Type: STRING, `Interval`: INT, Month: INT, DayOfMonth: INT, DaysOfWeek: ARRAY<STRING>, FirstDayOfWeek: STRING, Index: STRING>, `Range`: STRUCT<Type: STRING, StartDate: STRING, EndDate: STRING, RecurrenceTimeZone: STRING, NumberOfOccurrences: INT>>"
            },
            {
                "name": "ResponseRequested",
                "type": "Boolean",
                "description": "Set to true if the sender would like a response when the event is accepted or declined."
            },
            {
                "name": "ShowAs",
                "type": "String",
                "description": "The status to show: Free, Tentative, Busy, Oof, WorkingElsewhere, Unknown."
            },
            {
                "name": "Type",
                "type": "String",
                "description": "The event type: SingleInstance, Occurrence, Exception, SeriesMaster."
            },
            {
                "name": "Attendees",
                "type": "String",
                "description": "The collection of attendees for the event. Format: ARRAY<STRUCT<EmailAddress: STRUCT<Name: STRING, Address: STRING>, Status: STRUCT<Response: STRING, Time: STRING>, Type: STRING>>"
            },
            {
                "name": "Organizer",
                "type": "String",
                "description": "The organizer of the event. Format: STRUCT<EmailAddress: STRUCT<Name: STRING, Address: STRING>>"
            },
            {
                "name": "WebLink",
                "type": "String",
                "description": "The URL to open the event in Outlook Web App."
            },
            {
                "name": "Attachments",
                "type": "String",
                "description": "The FileAttachment and ItemAttachment attachments for the message. Navigation property. Format: ARRAY<STRUCT<LastModifiedDateTime: STRING, Name: STRING, ContentType: STRING, Size: INT, IsInline: BOOLEAN, Id: STRING>>"
            },
            {
                "name": "BodyPreview",
                "type": "String",
                "description": "The preview of the message associated with the event. It is in text format."
            },
            {
                "name": "Locations",
                "type": "String",
                "description": "The locations where the event is held or attended from. The location and locations properties always correspond with each other. Format:  ARRAY<STRUCT<DisplayName: STRING, Address: STRUCT<Street: STRING, City: STRING, State: STRING, CountryOrRegion: STRING, PostalCode: STRING>, Coordinates: STRUCT<Altitude: DOUBLE, Latitude: DOUBLE, Longitude: DOUBLE, Accuracy: DOUBLE, AltitudeAccuracy: DOUBLE>, LocationEmailAddress: STRING, LocationUri: STRING, LocationType: STRING, UniqueId: STRING, UniqueIdType: STRING>>"
            },
            {
                "name": "OnlineMeetingUrl",
                "type": "String",
                "description": "A URL for an online meeting. The property is set only when an organizer specifies an event as an online meeting such as a Skype meeting"
            },
            {
                "name": "OriginalStart",
                "type": "DateTime",
                "description": "The start time that was set when the event was created in UTC time."
            },
            {
                "name": "SeriesMasterId",
                "type": "String",
                "description": "The ID for the recurring series master item, if this event is part of a recurring series."
            }
        ],
        "typeProperties": {
            "tableName": "BasicDataSet_v0.Event_v1",
            "dateFilterColumn": "CreatedDateTime",
            "startTime": "2019-04-28T16:00:00.000Z",
            "endTime": "2019-05-05T16:00:00.000Z",
            "userScopeFilterUri": "https://graph.microsoft.com/v1.0/users?$filter=Department eq 'Finance'"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy

Para obter uma lista completa das secções e propriedades disponíveis para a definição de atividades, consulte a [Pipelines](concepts-pipelines-activities.md) artigo. Esta seção fornece uma lista de propriedades suportadas por origem do Office 365.

### <a name="office-365-as-source"></a>Office 365 como origem

Para copiar dados do Office 365, defina o tipo de origem na atividade copy na atividade **Office365Source**. Não existem propriedades adicionais são suportadas na atividade de cópia **origem** secção.

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
                "type": "Office365Source"
            },
            "sink": {
                "type": "BlobSink"
            }
        }
    }
]
```

## <a name="next-steps"></a>Passos Seguintes
Para obter uma lista dos arquivos de dados suportados como origens e sinks, a atividade de cópia no Azure Data Factory, veja [arquivos de dados suportados](copy-activity-overview.md#supported-data-stores-and-formats).
