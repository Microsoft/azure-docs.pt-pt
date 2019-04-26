---
title: Copiar dados do Office 365 com o Azure Data Factory (pré-visualização) | Documentos da Microsoft
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
ms.date: 01/29/2019
ms.author: jingwang
ms.openlocfilehash: 5d2d5948d817cbe80d00b74ef104ebaffcb511fb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60405975"
---
# <a name="copy-data-from-office-365-into-azure-using-azure-data-factory-preview"></a>Copiar dados do Office 365 no Azure com o Azure Data Factory (pré-visualização) 

O Azure Data Factory permite-lhe reunir a avançada dados organizacionais no seu Office 365 de inquilino para o Azure de uma forma escalável e criem aplicações de análise e extrair informações com base nestes ativos de dados valiosos. Integração com o Privileged Access Management fornece controlo de acesso protegido para os valiosos dados organizados no Office 365.  Para obter mais informações sobre os dados do Microsoft Graph ligar, veja [esta ligação](https://github.com/OfficeDev/ManagedAccessMSGraph/wiki).

Este artigo descreve como utilizar a atividade de cópia no Azure Data Factory para copiar dados do Office 365. Ele se baseia no [copiar descrição geral da atividade](copy-activity-overview.md) artigo apresenta uma visão geral da atividade de cópia.

## <a name="supported-capabilities"></a>Capacidades suportadas

Por agora, dentro de uma atividade de cópia única só é possível **copiar dados do Office 365 numa [armazenamento de Blobs do Azure](connector-azure-blob-storage.md), [Gen1 de armazenamento do Azure Data Lake](connector-azure-data-lake-store.md), e [Gen2 de armazenamento do Azure Data Lake ](connector-azure-data-lake-storage.md) no formato JSON** (digite setOfObjects). Se pretender carregar do Office 365 para outros tipos de arquivos de dados ou em outros formatos, pode encadear a primeira atividade de cópia com uma atividade de cópia subsequentes para ainda mais carregar dados para qualquer um da [arquivos de destino do ADF suportados](copy-activity-overview.md#supported-data-stores-and-formats) (consulte" suportado como sink"coluna na tabela"Suporte a arquivos de dados e formatos").

>[!IMPORTANT]
>- A subscrição do Azure que contém a fábrica de dados e o arquivo de dados de sink tem de estar no mesmo inquilino do Azure Active Directory (Azure AD) como inquilino do Office 365.
>- Certifique-se a região do Runtime de integração do Azure utilizada para atividade de cópia, bem como o destino está na mesma região onde está localizada a caixa de correio dos utilizadores de inquilino do Office 365. Consultar [aqui](concepts-integration-runtime.md#integration-runtime-location) para compreender como é determinada a localização do Runtime de integração do Azure. Consulte a [aqui de tabela](https://github.com/OfficeDev/ManagedAccessMSGraph/wiki/Capabilities#data-regions) para a lista de regiões suportadas do Office e regiões do Azure correspondentes.
>-  Se estiver a carregar dados do Office 365 em **armazenamento de Blobs do Azure** como destino, certifique-se de que está a utilizar **[autenticação do principal de serviço](connector-azure-blob-storage.md#service-principal-authentication)** ao definir o ligado Serviço para o armazenamento de Blobs do Azure e não a utilizar [chave da conta](connector-azure-blob-storage.md#account-key-authentication), [assinatura de acesso partilhado](connector-azure-blob-storage.md#shared-access-signature-authentication) ou [geridos identidades para recursos do Azure](connector-azure-blob-storage.md#managed-identity) autenticações.
>-  Se estiver a carregar dados do Office 365 em **Gen1 de armazenamento do Azure Data Lake** como destino, certifique-se de que está a utilizar [ **autenticação do principal de serviço** ](connector-azure-data-lake-store.md#use-service-principal-authentication) ao definir o Serviço ligado para geração 1 de armazenamento do Azure Data Lake e não a utilizar [geridos identidades para autenticação de recursos do Azure](connector-azure-data-lake-store.md#managed-identity).

## <a name="prerequisites"></a>Pré-requisitos

Para copiar dados do Office 365 no Azure, tem de concluir os passos de pré-requisitos seguintes:

- O administrador do inquilino do Office 365 tem de concluir as ações de integração conforme descrito [aqui](https://github.com/OfficeDev/ManagedAccessMSGraph/wiki/On-boarding).
- Criar e configurar uma aplicação web do Azure AD no Azure Active Directory.  Para obter instruções, consulte [criar uma aplicação do Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application).
- Tome nota dos seguintes valores, que irá utilizar para definir o serviço ligado para o Office 365:
    - ID do inquilino. Para obter instruções, consulte [obter ID de inquilino](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-id).
    - Chave de ID da aplicação e a aplicação.  Para obter instruções, consulte [chave de ID e a autenticação da aplicação Get](../active-directory/develop/howto-create-service-principal-portal.md#get-application-id-and-authentication-key).
- Adicionar a identidade do utilizador que está a definir o pedido de acesso de dados como o proprietário do aplicativo web do Azure AD (do Azure AD da aplicação web > Definições > proprietários > Adicionar proprietário). 
    - A identidade do utilizador tem de ser na organização do Office 365 estão a obter dados a partir de e não pode ser um utilizador convidado.

## <a name="approving-new-data-access-requests"></a>Aos novos pedidos de acesso de dados

Se esta for a primeira vez que está a solicitar dados para este contexto (uma combinação de quais dados tabela está a ser acesso, o destino conta é os dados a ser carregados para, e que identidade de utilizador está a tornar os dados de pedido de acesso), verá que a atividade de cópia Estado como "Em curso", e somente quando clicar em [link "Detalhes" em ações](copy-activity-overview.md#monitoring) irá ver o estado como "RequestingConsent".  Um membro do grupo de aprovador de acesso de dados tem de aprovar o pedido de Privileged Access Management antes de pode continuar a extração de dados.

Consultar [aqui](https://github.com/OfficeDev/MS-Graph-Data-Connect/wiki/Approving-data-access-requests) sobre como o aprovador pode aprovar os dados de pedido de acesso e consulte [aqui](https://github.com/OfficeDev/MS-Graph-Data-Connect/wiki/Capabilities#integration-with-privileged-access-management) para obter uma explicação sobre a integração geral com o Privileged Access Management, incluindo como configurar os dados grupo de aprovador de acesso.

## <a name="policy-validation"></a>Validação de diretivas

Se ADF é criado como parte de uma aplicação gerida e atribuições de políticas do Azure são feitas nos recursos no grupo de recursos de gerenciamento, em seguida, para cada atividade de cópia executa, ADF irá verificar para se certificar de que as atribuições de política são impostas. Consultar [aqui](https://github.com/OfficeDev/ManagedAccessMSGraph/wiki/Capabilities#policies) para obter uma lista de políticas suportadas.

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
| tableName | Nome do conjunto de dados para extrair a partir do Office 365. Consultar [aqui](https://github.com/OfficeDev/MS-Graph-Data-Connect/wiki/Capabilities#datasets) para obter a lista de conjuntos de dados do Office 365 disponíveis para extração. | Sim |
| Predicado | Uma expressão de predicado que pode ser utilizada para filtrar as linhas específicas para extrair a partir do Office 365.  Consultar [aqui](https://github.com/OfficeDev/MS-Graph-Data-Connect/wiki/Capabilities#filters) para descobrir as colunas que podem ser utilizadas para a filtragem de predicados para cada tabela e o formato de expressão de filtro. | Não<br>(Não se for fornecido nenhum predicado, a predefinição é extrair dados dos últimos 30 dias) |

**Exemplo**

```json
{
    "name": "Office365Table1",
    "properties": {
        "type": "Office365Table",
        "linkedServiceName": {
            "referenceName": "<Office 365 linked service name>",
            "type": "LinkedServiceReference"
        },
        "structure": [
            {
                "name": "ReceivedDateTime",
                "type": "datetime"
            },
            {
                "name": "SentDateTime",
                "type": "datetime"
            },
            {
                "name": "HasAttachments",
                "type": "boolean"
            },
            {
                "name": "InternetMessageId",
                "type": "string"
            },
            {
                "name": "Subject",
                "type": "string"
            },
            {
                "name": "Importance",
                "type": "string"
            },
            {
                "name": "ParentFolderId",
                "type": "string"
            },
            {
                "name": "Sender",
                "type": "string"
            },
            {
                "name": "From",
                "type": "string"
            },
            {
                "name": "ToRecipients",
                "type": "string"
            },
            {
                "name": "CcRecipients",
                "type": "string"
            },
            {
                "name": "BccRecipients",
                "type": "string"
            },
            {
                "name": "ReplyTo",
                "type": "string"
            },
            {
                "name": "ConversationId",
                "type": "string"
            },
            {
                "name": "UniqueBody",
                "type": "string"
            },
            {
                "name": "IsDeliveryReceiptRequested",
                "type": "boolean"
            },
            {
                "name": "IsReadReceiptRequested",
                "type": "boolean"
            },
            {
                "name": "IsRead",
                "type": "boolean"
            },
            {
                "name": "IsDraft",
                "type": "boolean"
            },
            {
                "name": "WebLink",
                "type": "string"
            },
            {
                "name": "CreatedDateTime",
                "type": "datetime"
            },
            {
                "name": "LastModifiedDateTime",
                "type": "datetime"
            },
            {
                "name": "ChangeKey",
                "type": "string"
            },
            {
                "name": "Categories",
                "type": "string"
            },
            {
                "name": "Id",
                "type": "string"
            },
            {
                "name": "Attachments",
                "type": "string"
            }
        ],
        "typeProperties": {
            "tableName": "BasicDataSet_v0.Contact_v0",
            "predicate": "CreatedDateTime >= 2018-07-11T16:00:00.000Z AND CreatedDateTime <= 2018-07-18T16:00:00.000Z"
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
