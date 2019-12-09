---
title: Armazenar credenciais no Azure Key Vault
description: Saiba como armazenar credenciais para armazenamentos de dados usados em um cofre de chaves do Azure que Azure Data Factory pode recuperar automaticamente no tempo de execução.
services: data-factory
author: linda33wj
manager: shwang
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: jingwang
ms.openlocfilehash: 32c4b9b8e6268aa648e3414b337e8b2b908589e8
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74928725"
---
# <a name="store-credential-in-azure-key-vault"></a>Armazenar credencial em Azure Key Vault

Você pode armazenar credenciais para armazenamentos de dados e computações em um [Azure Key Vault](../key-vault/key-vault-overview.md). Azure Data Factory recupera as credenciais ao executar uma atividade que usa o armazenamento de dados/computação.

Atualmente, todos os tipos de atividade, exceto a atividade personalizada, dão suporte a esse recurso. Para configuração de conector especificamente, marque a seção "Propriedades do serviço vinculado" em [cada tópico de conector](copy-activity-overview.md#supported-data-stores-and-formats) para obter detalhes.

## <a name="prerequisites"></a>Pré-requisitos

Esse recurso depende da identidade gerenciada data factory. Saiba como ele funciona da [identidade gerenciada para o data Factory](data-factory-service-identity.md) e certifique-se de que seu data Factory tenha um associado.

## <a name="steps"></a>Passos

Para fazer referência a uma credencial armazenada no Azure Key Vault, você precisa:

1. **Recupere data Factory identidade gerenciada** copiando o valor de "ID do aplicativo de identidade gerenciada" gerado junto com sua fábrica. Se você usar a interface do usuário de criação do ADF, a ID do aplicativo de identidade gerenciada será mostrada na janela de criação do serviço vinculado Azure Key Vault; Você também pode recuperá-lo de portal do Azure, consulte [recuperar data Factory identidade gerenciada](data-factory-service-identity.md#retrieve-managed-identity).
2. **Conceda o acesso de identidade gerenciada à sua Azure Key Vault.** Em seu cofre de chaves-políticas de acesso de >-> Adicionar New-> Pesquise essa ID de aplicativo de identidade gerenciada para conceder permissão **Get** no menu suspenso permissões secretas. Ele permite que essa fábrica designada acesse o segredo no Key Vault.
3. **Crie um serviço vinculado apontando para seu Azure Key Vault.** Consulte [Azure Key Vault serviço vinculado](#azure-key-vault-linked-service).
4. **Crie um serviço vinculado do repositório de dados, dentro do qual fazer referência ao segredo correspondente armazenado no cofre de chaves.** Consulte o [segredo de referência armazenado no cofre de chaves](#reference-secret-stored-in-key-vault).

## <a name="azure-key-vault-linked-service"></a>Azure Key Vault serviço vinculado

As propriedades a seguir têm suporte para Azure Key Vault serviço vinculado:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade Type deve ser definida como: **AzureKeyVault**. | Sim |
| baseUrl | Especifique a URL do Azure Key Vault. | Sim |

**Usando a interface do usuário de criação:**

Clique em **conexões** -> **serviços vinculados** ->  **+ New** -> Pesquisar por "Azure Key Vault":

![Pesquisar AKV](media/store-credentials-in-key-vault/search-akv.png)

Selecione os Azure Key Vault provisionados onde suas credenciais são armazenadas. Você pode **testar a conexão** para verificar se a conexão do akv é válida. 

![Configurar o AKV](media/store-credentials-in-key-vault/configure-akv.png)

**Exemplo de JSON:**

```json
{
    "name": "AzureKeyVaultLinkedService",
    "properties": {
        "type": "AzureKeyVault",
        "typeProperties": {
            "baseUrl": "https://<azureKeyVaultName>.vault.azure.net"
        }
    }
}
```

## <a name="reference-secret-stored-in-key-vault"></a>Segredo de referência armazenado no cofre de chaves

As propriedades a seguir têm suporte quando você configura um campo no serviço vinculado que faz referência a um segredo do cofre de chaves:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade Type do campo deve ser definida como: **AzureKeyVaultSecret**. | Sim |
| secretName | O nome do segredo no Azure Key Vault. | Sim |
| secretVersion | A versão do segredo no Azure Key Vault.<br/>Se não for especificado, ele sempre usará a versão mais recente do segredo.<br/>Se especificado, ele se enparará com a versão fornecida.| Não |
| store | Refere-se a um Azure Key Vault serviço vinculado que você usa para armazenar a credencial. | Sim |

**Usando a interface do usuário de criação:**

Selecione **Azure Key Vault** para campos secretos ao criar a conexão com o armazenamento de dados/computação. Selecione o serviço vinculado de Azure Key Vault provisionado e forneça o **nome do segredo**. Opcionalmente, você também pode fornecer uma versão secreta. 

>[!TIP]
>Para os conectores que usam a cadeia de conexão no serviço vinculado, como SQL Server, armazenamento de BLOBs, etc., você pode optar por armazenar apenas o campo secreto, por exemplo, senha em AKV, ou armazenar a cadeia de conexão inteira em AKV. Você pode encontrar ambas as opções na interface do usuário.

![Configurar o segredo AKV](media/store-credentials-in-key-vault/configure-akv-secret.png)

**Exemplo de JSON: (consulte a seção "senha")**

```json
{
    "name": "DynamicsLinkedService",
    "properties": {
        "type": "Dynamics",
        "typeProperties": {
            "deploymentType": "<>",
            "organizationName": "<>",
            "authenticationType": "<>",
            "username": "<>",
            "password": {
                "type": "AzureKeyVaultSecret",
                "secretName": "<secret name in AKV>",
                "store":{
                    "referenceName": "<Azure Key Vault linked service>",
                    "type": "LinkedServiceReference"
                }
            }
        }
    }
}
```

## <a name="next-steps"></a>Passos seguintes
Para obter uma lista dos arquivos de dados suportados como origens e sinks, a atividade de cópia no Azure Data Factory, veja [arquivos de dados suportados](copy-activity-overview.md#supported-data-stores-and-formats).
