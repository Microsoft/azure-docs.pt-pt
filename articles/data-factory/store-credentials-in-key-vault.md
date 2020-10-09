---
title: Armazenar credenciais no Azure Key Vault
description: Saiba como armazenar credenciais para lojas de dados utilizadas num cofre de chaves Azure que a Azure Data Factory pode recuperar automaticamente no tempo de funcionamento.
services: data-factory
author: linda33wj
manager: shwang
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/13/2020
ms.author: jingwang
ms.openlocfilehash: 22ab4433d84db926733fd0b18035875e63322dda
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "81451691"
---
# <a name="store-credential-in-azure-key-vault"></a>Credencial de loja em Azure Key Vault

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Pode armazenar credenciais para lojas de dados e cálculos num [Cofre de Chaves Azure](../key-vault/general/overview.md). A Azure Data Factory recupera as credenciais ao executar uma atividade que utiliza a loja/computação de dados.

Atualmente, todos os tipos de atividade, exceto a atividade personalizada, suportam esta funcionalidade. Para obter especificamente a configuração do conector, consulte a secção "propriedades de serviço ligadas" em [cada tópico do conector](copy-activity-overview.md#supported-data-stores-and-formats) para obter mais detalhes.

## <a name="prerequisites"></a>Pré-requisitos

Esta funcionalidade baseia-se na identidade gerida pela fábrica de dados. Saiba como funciona a partir da [identidade gerida para a fábrica de dados](data-factory-service-identity.md) e certifique-se de que a sua fábrica de dados tem uma associada.

## <a name="steps"></a>Passos

Para fazer referência a uma credencial armazenada no Cofre da Chave Azure, é necessário:

1. **Recupere a identidade gerida** pela fábrica de dados copiando o valor do "ID do Objeto de Identidade Gerido" gerado juntamente com a sua fábrica. Se utilizar uI de autoria da ADF, o ID do objeto de identidade gerido será mostrado na janela de criação de serviço ligada a Azure Key Vault; também pode recuperá-lo do portal Azure, consulte a [identidade gerida pela fábrica de dados Recuperar.](data-factory-service-identity.md#retrieve-managed-identity)
2. **Conceda o acesso de identidade gerida ao seu Cofre de Chaves Azure.** Na sua chave de segurança -> Políticas de acesso -> Adicionar Política de Acesso, procure esta identidade gerida para **conceder** permissão em permissões secretas. Permite que esta fábrica designada aceda ao segredo no cofre das chaves.
3. **Crie um serviço ligado que indique o seu Cofre de Chaves Azure.** Consulte o [serviço ligado ao Cofre da Chave Azure](#azure-key-vault-linked-service).
4. **Crie um serviço ligado à loja de dados, no interior do qual se refere o segredo correspondente armazenado no cofre de chaves.** Consulte o [segredo de referência armazenado no cofre da chave](#reference-secret-stored-in-key-vault).

## <a name="azure-key-vault-linked-service"></a>Serviço ligado do Azure Key Vault

As seguintes propriedades são suportadas para o serviço ligado ao Cofre da Chave Azure:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade tipo deve ser definida para: **AzureKeyVault**. | Sim |
| baseUrl | Especifique o URL do cofre da chave Azure. | Sim |

**Utilização da UI de autor:**

Selecione **Ligações**  ->  **Serviços Ligados**  ->  **Novos**. No serviço new linked, procure e selecione "Azure Key Vault":

![Procure cofre de chave Azure](media/store-credentials-in-key-vault/search-akv.png)

Selecione o cofre de chave Azure, onde as suas credenciais estão armazenadas. Pode fazer **a Ligação de Teste** para se certificar de que a sua ligação AKV é válida. 

![Configurar o Azure Key Vault](media/store-credentials-in-key-vault/configure-akv.png)

**Exemplo JSON:**

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

## <a name="reference-secret-stored-in-key-vault"></a>Referência ao segredo armazenado no cofre de chaves

As seguintes propriedades são suportadas quando configura um campo em serviço ligado, referindo um segredo de cofre chave:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade tipo do campo deve ser definida para: **AzureKeyVaultSecret**. | Sim |
| nome secreto | O nome do segredo em Azure Key Vault. | Sim |
| versão secreta | A versão do segredo em Azure Key Vault.<br/>Se não for especificado, utiliza sempre a versão mais recente do segredo.<br/>Se especificado, então mantém-se na versão dada.| Não |
| Loja | Refere-se a um serviço ligado ao Cofre da Chave Azure que utiliza para armazenar a credencial. | Sim |

**Utilização da UI de autor:**

Selecione **Azure Key Vault** para campos secretos enquanto cria a ligação à sua loja/computação de dados. Selecione o serviço ligado ao cofre de chaves Azure e forneça o **nome secreto**. Pode opcionalmente fornecer uma versão secreta também. 

>[!TIP]
>Para os conectores que usam cadeias de ligação em serviço ligado como SQL Server, Blob storage, etc., pode optar por armazenar apenas o campo secreto, por exemplo, a palavra-passe em AKV, ou armazenar toda a cadeia de ligação em AKV. Pode encontrar ambas as opções na UI.

![Configurar segredo do Cofre da Chave Azure](media/store-credentials-in-key-vault/configure-akv-secret.png)

**Exemplo JSON: (ver a secção "password")**

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
Para obter uma lista de lojas de dados suportadas como fontes e sumidouros pela atividade de cópia na Azure Data Factory, consulte lojas de [dados suportadas.](copy-activity-overview.md#supported-data-stores-and-formats)
