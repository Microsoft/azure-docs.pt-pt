---
title: Copiar dados do Office 365 utilizando a Azure Data Factory
description: Saiba como copiar dados do Office 365 para lojas de dados de sink suportadas utilizando a atividade de cópia num pipeline azure Data Factory.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74912410"
---
# <a name="copy-data-from-office-365-into-azure-using-azure-data-factory"></a>Copiar dados do Office 365 para O Azure utilizando a Azure Data Factory

A Azure Data Factory integra-se com a ligação de dados do [Microsoft Graph,](https://docs.microsoft.com/graph/data-connect-concept-overview)permitindo-lhe trazer os dados organizacionais ricos do seu Office 365 tenant para o Azure de uma forma escalável e construir aplicações de análise e extrair insights com base nestes valiosos ativos de dados. A Integração com a Privileged Access Management fornece um controlo de acesso garantido para os valiosos dados curados no Office 365.  Consulte [este link](https://docs.microsoft.com/graph/data-connect-concept-overview) para uma visão geral da ligação de dados do Microsoft Graph e consulte [este link](https://docs.microsoft.com/graph/data-connect-policies#licensing) para obter informações de licenciamento.

Este artigo descreve como utilizar a Atividade de Cópia na Fábrica de Dados Azure para copiar dados do Office 365. Baseia-se no artigo de visão geral da [atividade de cópia](copy-activity-overview.md) que apresenta uma visão geral da atividade de cópia.

## <a name="supported-capabilities"></a>Capacidades suportadas
O conector ADF Office 365 e a ligação de dados do Microsoft Graph permitem a ingestão de diferentes tipos de conjuntos de dados a partir de caixas de correio ativadas pelo Exchange Email, incluindo contactos de livros de endereços, eventos de calendário, mensagens de correio eletrónico, informações do utilizador, definições de caixa de correio e assim por diante.  Consulte [aqui](https://docs.microsoft.com/graph/data-connect-datasets) a lista completa de conjuntos de dados disponíveis.

Por enquanto, dentro de uma única atividade de cópia só pode **copiar dados do Office 365 para o Armazenamento de [Blob Azure,](connector-azure-blob-storage.md) [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md)e [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) em formato JSON** (conjunto de tipoOfObjects). Se pretender carregar o Office 365 noutros tipos de lojas de dados ou noutros formatos, pode acorrentar a primeira atividade de cópia com uma atividade de cópia subsequente para carregar mais dados em qualquer uma das lojas de [destino ADF suportadas](copy-activity-overview.md#supported-data-stores-and-formats) (consulte a coluna "suportada como um lavatório" na tabela "Lojas de dados e formatos suportados").

>[!IMPORTANT]
>- A assinatura Azure que contenha a fábrica de dados e a loja de dados do lavatório deve estar sob o mesmo inquilino azure Ative Directory (Azure AD) como inquilino do Office 365.
>- Certifique-se de que a região de Runtime de Integração Azure utilizada para a atividade de cópia, bem como o destino, se encontra na mesma região onde está localizada a caixa de correio dos inquilinos do Office 365. Consulte [aqui](concepts-integration-runtime.md#integration-runtime-location) para entender como a localização do Ir Azure é determinada. Consulte a [tabela aqui](https://docs.microsoft.com/graph/data-connect-datasets#regions) para a lista de regiões apoiadas do Office e regiões de Azure correspondentes.
>- A autenticação principal do serviço é o único mecanismo de autenticação suportado para o armazenamento de Blob Azure, Azure Data Lake Storage Gen1 e Azure Data Lake Storage Gen2 como lojas de destino.

## <a name="prerequisites"></a>Pré-requisitos

Para copiar os dados do Office 365 para o Azure, é necessário completar as seguintes etapas pré-requisitos:

- O administrador do seu Gabinete 365 inquilino deve concluir as ações de embarque, conforme descrito [aqui.](https://docs.microsoft.com/graph/data-connect-get-started)
- Crie e configure uma aplicação web Azure AD no Diretório Ativo Azure.  Para obter instruções, consulte [Criar uma aplicação Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application).
- Tome nota dos seguintes valores, que utilizará para definir o serviço vinculado para o Office 365:
    - Identificação do inquilino. Para obter instruções, consulte [Obter identificação do inquilino](../active-directory/develop/howto-create-service-principal-portal.md#get-values-for-signing-in).
    - Id de aplicação e chave de aplicação.  Para obter instruções, consulte [Obter id da aplicação e chave de autenticação](../active-directory/develop/howto-create-service-principal-portal.md#get-values-for-signing-in).
- Adicione a identidade do utilizador que irá fazer o pedido de acesso a dados como proprietário da aplicação web Azure AD (a partir da aplicação web Da Azure AD > Definições > Proprietários > Adicionar proprietário). 
    - A identidade do utilizador deve estar na organização do Office 365 de onde está a obter dados e não deve ser um utilizador convidado.

## <a name="approving-new-data-access-requests"></a>Aprovação de novos pedidos de acesso a dados

Se esta for a primeira vez que solicita dados para este contexto (uma combinação da qual a tabela de dados está a ser acedida, qual a conta de destino que está a ser carregada, e qual a identidade do utilizador que está a fazer o pedido de acesso de dados), verá o estado da atividade da cópia como "Em Curso", e só quando clicar no [link "Detalhes" em Ações](copy-activity-overview.md#monitoring) verá o estado como "RequestingConsent".  Um membro do grupo de aprovadores de acesso a dados precisa de aprovar o pedido na Gestão de Acesso Privilegiado antes que a extração de dados possa prosseguir.

Consulte [aqui](https://docs.microsoft.com/graph/data-connect-tips#approve-pam-requests-via-office-365-admin-portal) como o aprovador pode aprovar o pedido de acesso a dados, e consulte [aqui](https://docs.microsoft.com/graph/data-connect-pam) uma explicação sobre a integração global com a Privileged Access Management, incluindo como configurar o grupo de aprovadores de acesso a dados.

## <a name="policy-validation"></a>Validação de políticas

Se a ADF for criada como parte de uma app gerida e as atribuições de políticas do Azure forem feitas em recursos dentro do grupo de recursos de gestão, então para cada execução de atividade de cópia, a ADF verificará para garantir que as atribuições de políticas são executadas. Consulte [aqui](https://docs.microsoft.com/graph/data-connect-policies#policies) uma lista de políticas apoiadas.

## <a name="getting-started"></a>Introdução

>[!TIP]
>Para uma passagem pela utilização do conector office 365, consulte os dados de carga do artigo [do Office 365.](load-office-365-data.md)

Pode criar um pipeline com a atividade da cópia utilizando uma das seguintes ferramentas ou SDKs. Selecione um link para ir a um tutorial com instruções passo a passo para criar um pipeline com uma atividade de cópia. 

- [Portal Azure](quickstart-create-data-factory-portal.md)
- [.NET SDK](quickstart-create-data-factory-dot-net.md)
- [Python SDK](quickstart-create-data-factory-python.md)
- [Azure PowerShell](quickstart-create-data-factory-powershell.md)
- [REST API](quickstart-create-data-factory-rest-api.md)
- Modelo de [Gestor de Recursos Azure](quickstart-create-data-factory-resource-manager-template.md). 

As seguintes secções fornecem detalhes sobre propriedades que são usadas para definir entidades data Factory específicas do conector do Office 365.

## <a name="linked-service-properties"></a>Propriedades de serviço seletos

As seguintes propriedades são suportadas para o serviço ligado ao Office 365:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade tipo deve ser definida para: **Office365** | Sim |
| office365TenantId | Id do inquilino azure a que pertence a conta 365 do Escritório. | Sim |
| serviçoPrincipalTenantId | Especifique as informações do arrendatário segundo as quais reside a sua aplicação web Azure AD. | Sim |
| serviçoPrincipalId | Especifique a identificação do cliente do pedido. | Sim |
| serviçoPrincipalKey | Especifique a chave da aplicação. Marque este campo como um SecureString para o armazenar de forma segura na Data Factory. | Sim |
| connectVia | O Tempo de Integração a utilizar para se ligar à loja de dados.  Se não especificado, utiliza o tempo de funcionar de integração azure padrão. | Não |

>[!NOTE]
> A diferença entre **o office365TenantId** e o **serviçoPrincipalTenantId** e o valor correspondente a fornecer:
>- Se você é um desenvolvedor de empresas desenvolvendo uma aplicação contra os dados do Office 365 para o uso da sua própria organização, então você deve fornecer o mesmo ID de inquilino para ambas as propriedades, que é o ID de inquilino AAD da sua organização.
>- Se você é um desenvolvedor ISV desenvolvendo uma aplicação para os seus clientes, então office365TenantId será o ID de inquilino aad do seu cliente (instalador de aplicações) AAD inquilino e serviçoPrincipalTenantId será o ID de inquilino AAD da sua empresa.

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

Para obter uma lista completa de secções e propriedades disponíveis para definir conjuntos de dados, consulte o artigo conjuntos de [dados.](concepts-datasets-linked-services.md) Esta secção fornece uma lista de propriedades suportadas pelo Conjunto de dados do Office 365.

Para copiar dados do Office 365, são suportadas as seguintes propriedades:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade tipo do conjunto de dados deve ser definida para: **Office365Table** | Sim |
| tableName | Nome do conjunto de dados para extrair do Office 365. Consulte [aqui](https://docs.microsoft.com/graph/data-connect-datasets#datasets) a lista dos conjuntos de dados do Office 365 disponíveis para extração. | Sim |

Se estava `dateFilterColumn`a `startTime` `endTime`definir `userScopeFilterUri` , e no conjunto de dados, ainda é suportado como está, enquanto é sugerido que utilize o novo modelo na fonte de atividade.

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

Para obter uma lista completa de secções e imóveis disponíveis para definir atividades, consulte o artigo [Pipelines.](concepts-pipelines-activities.md) Esta secção fornece uma lista de propriedades suportadas pela fonte do Office 365.

### <a name="office-365-as-source"></a>Escritório 365 como fonte

Para copiar dados do Office 365, as seguintes propriedades são suportadas na secção **de origem** da atividade de cópia:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade tipo da fonte de atividade de cópia deve ser definida para: **Office365Source** | Sim |
| grupos permitidos | Predicado de seleção de grupos.  Utilize esta propriedade para selecionar até 10 grupos de utilizadores para os quais os dados serão recuperados.  Se não forem especificados grupos, os dados serão devolvidos para toda a organização. | Não |
| userScopeFilterUri | Quando `allowedGroups` a propriedade não é especificada, você pode usar uma expressão predicada que é aplicada em todo o inquilino para filtrar as linhas específicas para extrair do Office 365. O formato predicado deve corresponder ao formato de consulta `https://graph.microsoft.com/v1.0/users?$filter=Department eq 'Finance'`das APIs do Microsoft Graph, por exemplo. | Não |
| dataFilterColuna | Nome da coluna do filtro DateTime. Utilize esta propriedade para limitar o intervalo de tempo para o qual os dados do Office 365 são extraídos. | Sim, se o conjunto de dados tiver uma ou mais colunas DateTime. Consulte [aqui](https://docs.microsoft.com/graph/data-connect-filtering#filtering) a lista de conjuntos de dados que requerem este filtro DateTime. |
| startTime | Iniciar o valor datatempo para filtrar. | Sim, `dateFilterColumn` se for especificado |
| endTime | Fim do valor data tempo para filtrar. | Sim, `dateFilterColumn` se for especificado |
| outputColunas | Conjunto de colunas para copiar para afundar. | Não |

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
Para obter uma lista de lojas de dados suportadas como fontes e pias pela atividade de cópia na Azure Data Factory, consulte as lojas de [dados suportadas](copy-activity-overview.md#supported-data-stores-and-formats).
