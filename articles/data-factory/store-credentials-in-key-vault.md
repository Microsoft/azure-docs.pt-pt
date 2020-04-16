---
title: Armazenar credenciais no Azure Key Vault
description: Saiba como armazenar credenciais para lojas de dados usadas num cofre chave Azure que a Azure Data Factory pode recuperar automaticamente no tempo de funcionação.
services: data-factory
author: linda33wj
manager: shwang
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/13/2020
ms.author: jingwang
ms.openlocfilehash: b5a181625488a57de4b878d13c01a8c90bf8785a
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414670"
---
# <a name="store-credential-in-azure-key-vault"></a>Credencial de loja em Cofre chave Azure

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Pode armazenar credenciais para lojas de dados e cálculos num [Cofre de Chaves Azure.](../key-vault/key-vault-overview.md) A Azure Data Factory recupera as credenciais ao executar uma atividade que utiliza a loja de dados/computação.

Atualmente, todos os tipos de atividade, exceto atividade personalizada suportam esta funcionalidade. Para obter especificamente a configuração do conector, verifique especificamente a secção "propriedades de serviço ligadas" em cada tópico do [conector](copy-activity-overview.md#supported-data-stores-and-formats) para obter mais detalhes.

## <a name="prerequisites"></a>Pré-requisitos

Esta funcionalidade baseia-se na identidade gerida pela fábrica de dados. Saiba como funciona a partir da [identidade gerida para a fábrica](data-factory-service-identity.md) de dados e certifique-se de que a sua fábrica de dados tem uma associada.

## <a name="steps"></a>Passos

Para fazer referência a uma credencial armazenada no Cofre chave Azure, é necessário:

1. **Recupere** a identidade gerida pela fábrica de dados copiando o valor do "Id de Objeto de Identidade Gerido" gerado juntamente com a sua fábrica. Se utilizar a ADF a autoria de UI, o ID do objeto de identidade gerido será mostrado na janela de criação de serviço ligada ao Cofre chave Azure; também pode recuperá-lo do portal Azure, consulte a identidade gerida pela fábrica de [dados Retrieve.](data-factory-service-identity.md#retrieve-managed-identity)
2. **Conceda o acesso de identidade gerido ao seu Cofre chave Azure.** No seu cofre-chave - > políticas de acesso - > Adicionar Política de Acesso, procure esta identidade gerida para conceder permissão **obter** permissão em secretas. Permite que esta fábrica designada aceda a segredo no cofre.
3. **Crie um serviço ligado que indique o seu Cofre chave Azure.** Consulte o serviço ligado ao [Cofre de Chaves Azure](#azure-key-vault-linked-service).
4. **Crie um serviço ligado à loja de dados, no interior do qual referenciam o segredo correspondente armazenado no cofre chave.** Consulte o [segredo de referência armazenado no cofre da chave](#reference-secret-stored-in-key-vault).

## <a name="azure-key-vault-linked-service"></a>Serviço ligado do Azure Key Vault

As seguintes propriedades são suportadas para o serviço ligado ao Cofre chave Azure:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade tipo deve ser definida para: **AzureKeyVault**. | Sim |
| baseUrl | Especifique o URL do Cofre de Chaves Azure. | Sim |

**Utilizando a autoria da UI:**

Selecione **Ligações Serviços** -> **Ligados** -> **Novos**. No novo serviço ligado, procure e selecione "Azure Key Vault":

![Pesquisar cofre de chaves azure](media/store-credentials-in-key-vault/search-akv.png)

Selecione o cofre de chaves Azure aprovisionado onde as suas credenciais estão armazenadas. Pode fazer **a Ligação de Teste** para se certificar de que a sua ligação AKV é válida. 

![Configurar o Azure Key Vault](media/store-credentials-in-key-vault/configure-akv.png)

**Exemplo jSON:**

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

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade tipo do campo deve ser definida para: **AzureKeyVaultSecret**. | Sim |
| nome secreto | O nome do segredo no Cofre de Chaves Azure. | Sim |
| secretVersion | A versão do segredo no Cofre chave azure.<br/>Se não for especificado, utiliza sempre a versão mais recente do segredo.<br/>Se especificado, então cola-se à versão dada.| Não |
| Loja | Refere-se a um serviço ligado ao Cofre de Chaves Azure que utiliza para armazenar a credencial. | Sim |

**Utilizando a autoria da UI:**

Selecione **Azure Key Vault** para campos secretos enquanto cria a ligação à sua loja de dados/computação. Selecione o serviço ligado ao cofre de chaves Azure e forneça o **nome Secreto**. Também pode fornecer opcionalmente uma versão secreta. 

>[!TIP]
>Para conectores que utilizem cordas de ligação em serviço sitiato como O Servidor SQL, armazenamento Blob, etc., pode optar por armazenar apenas o campo secreto, por exemplo, palavra-passe no AKV, ou armazenar toda a cadeia de ligação em AKV. Pode encontrar ambas as opções na UI.

![Configure segredo do cofre de chaves azure](media/store-credentials-in-key-vault/configure-akv-secret.png)

**Exemplo jSON: (ver a secção "password")**

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
Para obter uma lista de lojas de dados suportadas como fontes e pias pela atividade de cópia na Azure Data Factory, consulte as lojas de [dados suportadas](copy-activity-overview.md#supported-data-stores-and-formats).
