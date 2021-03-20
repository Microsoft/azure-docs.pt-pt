---
title: Encriptação de recursos seguros na Automatização do Azure
description: Este artigo fornece conceitos para configurar a conta Automation para usar encriptação.
services: automation
ms.service: automation
ms.subservice: process-automation
author: snehithm
ms.author: snmuvva
ms.date: 01/11/2020
ms.topic: conceptual
manager: kmadnani
ms.openlocfilehash: 7d59ca60c7f90c227885927086511bd1f8ac7ca1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "86185845"
---
# <a name="encryption-of-secure-assets-in-azure-automation"></a>Encriptação de recursos seguros na Automatização do Azure

Os ativos seguros na Azure Automation incluem credenciais, certificados, conexões e variáveis encriptadas. Estes ativos estão protegidos na Azure Automation utilizando vários níveis de encriptação. Com base na chave de nível superior utilizada para a encriptação, existem dois modelos para encriptação:

- Usando as teclas geridas pela Microsoft
- Usando as chaves que gere

## <a name="microsoft-managed-keys"></a>Chaves geridas pela Microsoft

Por predefinição, a sua conta Azure Automation utiliza as teclas geridas pela Microsoft.

Cada ativo seguro é encriptado e armazenado na Azure Automation utilizando uma chave única (chave de encriptação de dados) que é gerada para cada conta de automação. Estas próprias chaves são encriptadas e armazenadas na Azure Automation utilizando mais uma chave única que é gerada para cada conta chamada Chave de Encriptação de Conta (AEK). Estas chaves de encriptação de conta encriptadas e armazenadas em Azure Automation utilizando Chaves geridas pela Microsoft. 

## <a name="keys-that-you-manage-with-key-vault-preview"></a>Chaves que gere com o Key Vault (pré-visualização)

Pode gerir a encriptação de ativos seguros para a sua conta Demôm automação com as suas próprias chaves. Quando especifica uma chave gerida pelo cliente ao nível da conta Automation, essa chave é utilizada para proteger e controlar o acesso à chave de encriptação da conta para a conta Automation. Isto, por sua vez, é usado para encriptar e desencriptar todos os ativos seguros. As chaves geridas pelo cliente oferecem uma maior flexibilidade para criar, rodar, desativar e revogar os controlos de acesso. Também pode auditar as chaves de encriptação utilizadas para proteger os seus ativos seguros.

Utilize o Cofre de Chaves Azure para armazenar chaves geridas pelo cliente. Pode criar as suas próprias chaves e armazená-las num cofre de chaves, ou pode usar as APIs do Cofre de Chaves Azure para gerar chaves.  Para mais informações sobre o Azure Key Vault, veja [o que é o Cofre da Chave Azure?](../key-vault/general/overview.md)

## <a name="use-of-customer-managed-keys-for-an-automation-account"></a>Utilização de chaves geridas pelo cliente para uma conta de Automação

Quando utiliza encriptação com chaves geridas pelo cliente para uma conta Automation, a Azure Automation embrulha a chave de encriptação da conta com a chave gerida pelo cliente no cofre de chaves associado. Ativar as chaves geridas pelo cliente não tem impacto no desempenho, e a conta é encriptada imediatamente com a nova chave, sem demora.

Uma nova conta De automação é sempre encriptada utilizando as teclas geridas pela Microsoft. Não é possível ativar chaves geridas pelo cliente no momento em que a conta é criada. As chaves geridas pelo cliente estão armazenadas no Cofre da Chave Azure, e o cofre-chave deve ser abastado com políticas de acesso que concedem permissões-chave à identidade gerida que está associada à conta Automation. A identidade gerida só está disponível após a criação da conta de armazenamento.

Quando modifica a chave utilizada para a encriptação segura do ativo da Azure Automation, ativando ou desativando as teclas geridas pelo cliente, atualizando a versão chave ou especificando uma chave diferente, a encriptação da chave de encriptação da conta muda, mas os ativos seguros na sua conta Azure Automation não precisam de ser reen encriptados.

> [!NOTE] 
> Para ativar as chaves geridas pelo cliente, é necessário fazer chamadas API API do Azure Automation REST utilizando a versão api 2020-01-13-pré-visualização

### <a name="prerequisites-for-using-customer-managed-keys-in-azure-automation"></a>Pré-requisitos para a utilização de chaves geridas pelo cliente na Azure Automation

Antes de ativar as chaves geridas pelo cliente para uma conta Demôm automação, deve certificar-se de que os seguintes pré-requisitos são cumpridos:

 - A chave manejada pelo cliente está guardada num Cofre de Chaves Azure. 
 - Ativar as propriedades **Soft Delete** e **Não Purgar** no cofre da chave. Estas características são necessárias para permitir a recuperação das chaves em caso de eliminação acidental.
 - Apenas as teclas RSA são suportadas com encriptação Azure Automation. Para obter mais informações sobre as chaves, consulte [as chaves, segredos e certificados do Azure Key Vault.](../key-vault/general/about-keys-secrets-certificates.md)
- A conta Automation e o cofre-chave podem estar em diferentes subscrições, mas precisam de estar no mesmo inquilino do Azure Ative Directory.

### <a name="assignment-of-an-identity-to-the-automation-account"></a>Atribuição de uma identidade à conta Automation

Para utilizar chaves geridas pelo cliente com uma conta Automation, a sua conta Automation precisa de autenticar contra o cofre-chave que armazena as chaves geridas pelo cliente. A Azure Automation utiliza identidades geridas do sistema para autenticar a conta com o Azure Key Vault. Para obter mais informações sobre identidades geridas, veja [quais são as identidades geridas para os recursos do Azure?](../active-directory/managed-identities-azure-resources/overview.md)

Configure um sistema atribuído à conta Automation' (2H00)

```http
PATCH https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name?api-version=2020-01-13-preview
```

Corpo do pedido:

```json
{ 
 "identity": 
 { 
  "type": "SystemAssigned" 
  } 
}
```

A identidade atribuída ao sistema para a conta Automation é devolvida numa resposta semelhante à seguinte:

```json
{
 "name": "automation-account-name",
 "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name",
 ..
 "identity": {
    "type": "SystemAssigned",
    "principalId": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
    "tenantId": "bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb"
 },
..
}
```

### <a name="configuration-of-the-key-vault-access-policy"></a>Configuração da política de acesso ao Cofre de Chaves

Uma vez atribuída uma identidade gerida à conta Automation, configura o acesso ao cofre de chaves que armazena as chaves geridas pelo cliente. A Azure Automation requer **obter,** **recuperar,** **embrulharKey,** **Desembrulhar** a Chave nas teclas geridas pelo cliente.

Tal política de acesso pode ser definida utilizando a seguinte chamada REST API:

```http
PUT https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/sample-group/providers/Microsoft.KeyVault/vaults/sample-vault/accessPolicies/add?api-version=2018-02-14
```

Corpo do pedido:

```json
{
  "properties": {
    "accessPolicies": [
      {
        "tenantId": "bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb",
        "objectId": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
        "permissions": {
          "keys": [
            "get",
            "recover",
            "wrapKey",
            "unwrapKey"
          ],
          "secrets": [],
          "certificates": []
        }
      }
    ]
  }
}
```

> [!NOTE]
> Os **campos de inquilinoId** e **objectId** devem ser doados com valores de **identidade.tenantId** e **identity.principalId** respectivamente a partir da resposta da identidade gerida para a conta Automation.

### <a name="change-the-configuration-of-automation-account-to-use-customer-managed-key"></a>Alterar a configuração da conta Dem automação para utilizar a chave gerida pelo cliente

Finalmente, pode mudar a sua conta de Automação das teclas geridas pela Microsoft para as teclas geridas pelo cliente, utilizando a seguinte chamada REST API:

```http
PATCH https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name?api-version=2020-01-13-preview
```

Corpo do pedido:

```json
 {
    "properties": {
      "encryption": {
        "keySource": "Microsoft.Keyvault",
        "keyvaultProperties": {
          "keyName": "sample-vault-key",
          "keyvaultUri": "https://sample-vault-key12.vault.azure.net",
          "keyVersion": "7c73556c521340209371eaf623cc099d"
        }
      }
    }
  }
```

Resposta de amostra

```json
{
  "name": "automation-account-name",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name",
  ..
  "properties": {
    ..
    "encryption": {
      "keyvaultProperties": {
         "keyName": "sample-vault-key",
          "keyvaultUri": "https://sample-vault-key12.vault.azure.net",
          "keyVersion": "7c73556c521340209371eaf623cc099d"
      },
      "keySource": "Microsoft.Keyvault"
    },
    ..
  }
}
```

## <a name="rotation-of-a-customer-managed-key"></a>Rotação de uma chave gerida pelo cliente

Pode rodar uma chave gerida pelo cliente no Cofre de Chaves Azure de acordo com as suas políticas de conformidade. Quando a tecla estiver rodada, tem de atualizar a conta Automation para utilizar a nova tecla URI.

Rodar a tecla não desencadeia a reencriminação de ativos seguros na conta Automation. Não é necessária mais nenhuma ação.

## <a name="revocation-of-access-to-a-customer-managed-key"></a>Revogação do acesso a uma chave gerida pelo cliente

Para revogar o acesso às chaves geridas pelo cliente, utilize o PowerShell ou o Azure CLI. Para obter mais informações, consulte [Azure Key Vault PowerShell](/powershell/module/az.keyvault/) ou [Azure Key Vault CLI](/cli/azure/keyvault). A revogação do acesso bloqueia eficazmente o acesso a todos os ativos seguros na conta Automation, uma vez que a chave de encriptação é inacessível pela Azure Automation.

## <a name="next-steps"></a>Passos seguintes

- Para entender o Azure Key Vault, veja [o que é Azure Key Vault?](../key-vault/general/overview.md)
- Para trabalhar com certificados, consulte [Gerir certificados na Azure Automation.](shared-resources/certificates.md)
- Para manusear credenciais, consulte [Gerir credenciais na Azure Automation](shared-resources/credentials.md).
- Para utilizar variáveis de automatização, [gerir variáveis na Azure Automation](shared-resources/variables.md).
- Para obter ajuda ao trabalhar com ligações, consulte [Gerir as ligações na Azure Automation.](automation-connections.md)
