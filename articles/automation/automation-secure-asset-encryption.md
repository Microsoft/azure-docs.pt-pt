---
title: Criptografia de ativos seguros na automação
description: A automação do Azure protege ativos seguros usando vários níveis de criptografia. Por padrão, a criptografia é feita usando chaves gerenciadas pela Microsoft. Os clientes podem configurar suas contas de automação para usar chaves gerenciadas pelo cliente para criptografia. Este artigo descreve os detalhes dos dois modos de criptografia e como você pode alternar entre os dois.
services: automation
ms.service: automation
ms.subservice: process-automation
author: snehithm
ms.author: snmuvva
ms.date: 01/11/2020
ms.topic: conceptual
manager: kmadnani
ms.openlocfilehash: fa8ea40d827807565e71d1e790c8c52986b85ec8
ms.sourcegitcommit: d48afd9a09f850b230709826d4a5cd46e57d19fa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/12/2020
ms.locfileid: "75904957"
---
# <a name="secure-assets-in-azure-automation"></a>Proteger ativos na automação do Azure

Os ativos seguros na automação do Azure incluem credenciais, certificados, conexões e variáveis criptografadas. Esses ativos são protegidos na automação do Azure usando vários níveis de criptografia. Com base na chave de nível superior usada para a criptografia, há dois modelos de criptografia:
-   Usando chaves gerenciadas pela Microsoft
-   Usando chaves gerenciadas pelo cliente

### <a name="microsoft-managed-keys"></a>Chaves gerenciadas pela Microsoft

Por padrão, sua conta de automação do Azure usa chaves gerenciadas pela Microsoft.

Cada ativo seguro é criptografado e armazenado na automação do Azure usando uma chave exclusiva (chave de criptografia de dados) que é gerada para cada conta de automação. Essas chaves são criptografadas e armazenadas na automação do Azure usando ainda outra chave exclusiva gerada para cada conta chamada de AEK (chave de criptografia de conta). Essas chaves de criptografia de conta são criptografadas e armazenadas na automação do Azure usando chaves gerenciadas pela Microsoft. 

### <a name="customer-managed-keys-with-key-vault-preview"></a>Chaves gerenciadas pelo cliente com Key Vault (versão prévia)

Você pode gerenciar a criptografia de ativos seguros na automação do Azure no nível de uma conta de automação com suas próprias chaves. Quando você especifica uma chave gerenciada pelo cliente no nível da conta de automação, essa chave é usada para proteger e controlar o acesso à chave de criptografia da conta para a conta de automação, que, por sua vez, é usada para criptografar e descriptografar todos os ativos seguros. Chaves gerenciadas pelo cliente oferecem maior flexibilidade para criar, girar, desabilitar e revogar controles de acesso. Você também pode auditar as chaves de criptografia usadas para proteger seus ativos seguros. 

Você deve usar Azure Key Vault para armazenar chaves gerenciadas pelo cliente. Você pode criar suas próprias chaves e armazená-las em um cofre de chaves ou pode usar as APIs de Azure Key Vault para gerar chaves.  Para obter mais informações sobre Azure Key Vault, consulte [o que é Azure Key Vault?](../key-vault/key-vault-overview.md)

## <a name="enable-customer-managed-keys-for-an-automation-account"></a>Habilitar chaves gerenciadas pelo cliente para uma conta de automação

Quando você habilita a criptografia com chaves gerenciadas pelo cliente para uma conta de automação, a automação do Azure encapsula a chave de criptografia da conta com a chave gerenciada pelo cliente no cofre de chaves associado. A habilitação de chaves gerenciadas pelo cliente não afeta o desempenho e a conta é criptografada com a nova chave imediatamente, sem nenhum atraso de tempo.

Uma nova conta de automação é sempre criptografada usando chaves gerenciadas pela Microsoft. Não é possível habilitar chaves gerenciadas pelo cliente no momento em que a conta é criada. As chaves gerenciadas pelo cliente são armazenadas em Azure Key Vault, e o cofre de chaves deve ser provisionado com políticas de acesso que concedem permissões de chave para a identidade gerenciada associada à conta de automação. A identidade gerenciada está disponível somente depois que a conta de armazenamento é criada.

Quando você modifica a chave que está sendo usada para a criptografia de ativo seguro da automação do Azure habilitando ou desabilitando chaves gerenciadas pelo cliente, atualizando a versão da chave ou especificando uma chave diferente, a criptografia da chave raiz é alterada, mas os ativos seguros no Azure A conta de automação não precisa ser criptografada novamente.

As três seções a seguir descrevem a mecânica de habilitação de chaves gerenciadas pelo cliente para uma conta de automação. 

> [!NOTE] 
> Para habilitar as chaves gerenciadas pelo cliente, no momento, você precisará fazer as APIs REST da automação do Azure usando a versão de API 2020-01-13-Preview

### <a name="pre-requisites-for-using-customer-managed-keys-in-azure-automation"></a>Pré-requisitos para usar chaves gerenciadas pelo cliente na automação do Azure

Antes de habilitar chaves gerenciadas pelo cliente para uma conta de automação, você deve garantir que os pré-requisitos a seguir sejam atendidos

 - A chave gerenciada pelo cliente é armazenada em um Azure Key Vault. 
 - Você deve habilitar a **exclusão reversível** e **não limpar** as propriedades no cofre de chaves. Esses recursos são necessários para permitir a recuperação de chaves em caso de exclusão acidental.
 - Somente as chaves RSA têm suporte com a criptografia de automação do Azure. Para obter mais informações sobre chaves, consulte [sobre Azure Key Vault chaves, segredos e certificados](../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys).
- A conta de automação e o cofre de chaves podem estar em assinaturas diferentes, mas precisam estar no mesmo locatário de Azure Active Directory.

### <a name="assign-an-identity-to-the-automation-account"></a>Atribuir uma identidade à conta de automação

Para usar chaves gerenciadas pelo cliente com uma conta de automação, sua conta de automação precisa se autenticar no keyvault que armazena chaves gerenciadas pelo cliente. A automação do Azure usa identidades gerenciadas atribuídas pelo sistema para autenticar a conta com Key Vault. Para obter mais informações sobre identidades gerenciadas, consulte [o que são identidades gerenciadas para recursos do Azure?](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)

Configurar uma identidade gerenciada atribuída pelo sistema à conta de automação usando a seguinte chamada à API REST

```http
PATCH https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name?api-version=2020-01-13-preview
```
Corpo do pedido
```json
{ 
 "identity": 
 { 
  "type": "SystemAssigned" 
  } 
}
```    

A identidade atribuída pelo sistema para a conta de automação é retornada na resposta

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

### <a name="configure-the-key-vault-access-policy"></a>Configurar a política de acesso de Key Vault

Depois que uma identidade gerenciada é atribuída à conta de automação, você configura o acesso ao Key Vault armazenar chaves gerenciadas pelo cliente. A automação do Azure requer **Get**, **Recover**, **wrapKey**, **UnwrapKey** nas chaves gerenciadas pelo cliente.

Essa política de acesso pode ser definida usando a seguinte chamada à API REST.

```http
PUT https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/sample-group/providers/Microsoft.KeyVault/vaults/sample-vault/accessPolicies/add?api-version=2018-02-14
```
Corpo do pedido

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
> Os campos tenantid e objectId devem ser fornecidos com os valores de Identity. tenantid e Identity. PrincipalId da resposta da identidade gerenciada para a conta de automação.

### <a name="change-the-configuration-of-automation-account-to-use-customer-managed-key"></a>Alterar a configuração da conta de automação para usar a chave gerenciada pelo cliente

Por fim, você pode alternar sua conta de automação de chaves gerenciadas por Microsft para chaves gerenciadas pelo cliente, usando a seguinte chamada à API REST.

```http
PATCH https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name?api-version=2020-01-13-preview
```
Corpo do pedido

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

## <a name="manage-customer-managed-keys-lifecycle"></a>Gerenciar ciclo de vida de chaves gerenciadas pelo cliente

### <a name="rotate-customer-managed-keys"></a>Girar chaves gerenciadas pelo cliente

Você pode girar uma chave gerenciada pelo cliente em Azure Key Vault de acordo com suas políticas de conformidade. Quando a chave é girada, você deve atualizar a conta de automação para usar o novo URI de chave. 

A rotação da chave não aciona a nova criptografia dos dados na conta de armazenamento. Não há nenhuma ação adicional necessária do usuário.

## <a name="revoke-access-to-customer-managed-keys"></a>Revogar o acesso a chaves gerenciadas pelo cliente

Para revogar o acesso às chaves gerenciadas pelo cliente, use o PowerShell ou CLI do Azure. Para obter mais informações, consulte [Azure Key Vault PowerShell](https://docs.microsoft.com/powershell/module/az.keyvault/) ou [Azure Key Vault CLI](https://docs.microsoft.com/cli/azure/keyvault). Revogar o acesso efetivamente bloqueia o acesso a todos os dados na conta de armazenamento, pois a chave de criptografia é inacessível pelo armazenamento do Azure.

## <a name="next-steps"></a>Passos seguintes

- [O que é o Azure Key Vault?](../key-vault/key-vault-overview.md) 
- [Recursos de certificados na Automatização do Azure](shared-resources/certificates.md)
- [Recursos de credenciais na Automatização do Azure](shared-resources/credentials.md)
- [Recursos de variáveis na Automatização do Azure](shared-resources/variables.md)
