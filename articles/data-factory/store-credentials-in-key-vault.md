---
title: Store credenciais no Azure Key Vault | Documentos da Microsoft
description: Saiba como armazenar as credenciais para arquivos de dados utilizados um cofre de chaves do Azure do Azure Data Factory pode obter automaticamente no tempo de execução.
services: data-factory
author: linda33wj
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: jingwang
ms.openlocfilehash: a7d440509e2b823400cde83c1ac2ec054c37eb74
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57896216"
---
# <a name="store-credential-in-azure-key-vault"></a>Credencial de Store no Azure Key Vault

Pode armazenar as credenciais para arquivos de dados e computações numa [do Azure Key Vault](../key-vault/key-vault-whatis.md). O Azure Data Factory obtém as credenciais ao executar uma atividade que utiliza a arquivo de dados/computação.

Atualmente, todos os tipos de atividade, exceto a atividade personalizada suportam esta funcionalidade. Para a configuração do conector especificamente, verifique a seção "Propriedades do serviço ligado" na [cada tópico de conector](copy-activity-overview.md#supported-data-stores-and-formats) para obter detalhes.

## <a name="prerequisites"></a>Pré-requisitos

Esta funcionalidade baseia-se na identidade do geridos de fábrica de dados. Saiba como ele funciona da [identidade de fábrica de dados gerida](data-factory-service-identity.md) e certifique-se a fábrica de dados tem um associados.

## <a name="steps"></a>Passos

Para fazer referência a uma credencial armazenada no Azure Key Vault, terá de:

1. **Obter dados fábrica gerenciada identidade** ao copiar o valor de "Serviço de ID da IDENTITY APPLICATION" gerada juntamente com sua fábrica. Se usar o ADF criação da interface do Usuário, o ID da aplicação de identidade gerida será apresentado na janela de criação do serviço ligado do Azure Key Vault; Também pode recuperá-la no portal do Azure, consulte [identidade gerida de fábrica de dados de recuperação](data-factory-service-identity.md#retrieve-managed-identity).
2. **Conceda o acesso de identidade gerida ao Cofre de chaves do Azure.** No seu Cofre de chaves -> acesso políticas -> Adicionar novo -> pesquisa isso gerenciado ID da identity application para conceder **obter** permissão na lista pendente de permissões do segredo. Ele permite que esta fábrica designada aceder ao segredo no Cofre de chaves.
3. **Crie um serviço ligado que aponta para o seu Cofre de chaves do Azure.** Consulte a [serviço ligado do Azure Key Vault](#azure-key-vault-linked-service).
4. **Crie serviço ligado do arquivo de dados, dentro do qual referência a correspondente chave secreta do armazenados no cofre.** Consulte a [segredo de referência armazenado no Cofre de chaves](#reference-secret-stored-in-key-vault).

## <a name="azure-key-vault-linked-service"></a>Serviço ligado de Cofre de chaves do Azure

As seguintes propriedades são suportadas para o serviço ligado do Azure Key Vault:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade de tipo tem de ser definida como: **AzureKeyVault**. | Sim |
| baseUrl | Especifique o URL do Cofre de chaves do Azure. | Sim |

**Usando a interface do Usuário de criação:**

Clique em **conexões** -> **serviços ligados** -> **+ novo** -> procure "Azure Key Vault":

![Pesquisa AKV](media/store-credentials-in-key-vault/search-akv.png)

Selecione o Cofre de chaves do Azure aprovisionado em que as suas credenciais são armazenadas. Pode fazer **Testar ligação** para se certificar de que sua AKV ligação é válida. 

![Configurar AKV](media/store-credentials-in-key-vault/configure-akv.png)

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

## <a name="reference-secret-stored-in-key-vault"></a>Segredo de referência armazenado no Cofre de chaves

As seguintes propriedades são suportadas quando configura um campo no serviço ligado que referencia um segredo do Cofre de chaves:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade de tipo do campo tem de ser definida: **AzureKeyVaultSecret**. | Sim |
| secretName | O nome do segredo no Cofre de chaves do azure. | Sim |
| secretVersion | A versão do segredo no Cofre de chaves do azure.<br/>Se não for especificado, utiliza sempre a versão mais recente do segredo do.<br/>Se for especificado, em seguida, prende para a versão especificada.| Não |
| Store | Refere-se a um serviço ligado do Azure Key Vault que utiliza para armazenar a credencial. | Sim |

**Usando a interface do Usuário de criação:**

Selecione **do Azure Key Vault** para campos secretos ao criar a ligação ao seu arquivo de dados/computação. Selecione o aprovisionado chave de Cofre de serviço ligado do Azure e forneça o **nome do segredo**. Opcionalmente, pode fornecer uma versão do segredo também. 

>[!TIP]
>Para os conectores com a cadeia de ligação no serviço ligado como o SQL Server, armazenamento de BLOBs, etc., é possível armazenar apenas o campo secreto por exemplo, palavra-passe em AKV ou para armazenar a cadeia de ligação completo no AKV. Pode encontrar ambas as opções na interface do Usuário.

![Configurar o segredo AKV](media/store-credentials-in-key-vault/configure-akv-secret.png)

**Exemplo de JSON: (consulte a seção de "palavra-passe")**

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

## <a name="next-steps"></a>Passos Seguintes
Para obter uma lista dos arquivos de dados suportados como origens e sinks, a atividade de cópia no Azure Data Factory, veja [arquivos de dados suportados](copy-activity-overview.md#supported-data-stores-and-formats).
