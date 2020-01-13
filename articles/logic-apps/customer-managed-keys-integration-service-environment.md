---
title: Configurar chaves gerenciadas pelo cliente para criptografar dados em repouso no ISEs
description: Crie e gerencie suas próprias chaves de criptografia para proteger dados em repouso para ambientes de serviço de integração (ISEs) em aplicativos lógicos do Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, rarayudu, logicappspm
ms.topic: conceptual
ms.date: 01/14/2020
ms.openlocfilehash: 6f4e0744aad5f053cdda0a52b382ad3c86982c2f
ms.sourcegitcommit: d48afd9a09f850b230709826d4a5cd46e57d19fa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/12/2020
ms.locfileid: "75904983"
---
# <a name="set-up-customer-managed-keys-to-encrypt-data-at-rest-for-integration-service-environments-ises-in-azure-logic-apps"></a>Configurar chaves gerenciadas pelo cliente para criptografar dados em repouso para ambientes de serviço de integração (ISEs) em aplicativos lógicos do Azure

Os aplicativos lógicos do Azure dependem do armazenamento do Azure para armazenar e [criptografar automaticamente os dados em repouso](../storage/common/storage-service-encryption.md). Essa criptografia protege seus dados e ajuda a atender aos compromissos de segurança e conformidade da organização. Por padrão, o armazenamento do Azure usa chaves gerenciadas pela Microsoft para criptografar seus dados. Para obter mais informações sobre como funciona a criptografia de armazenamento do Azure, consulte [criptografia de armazenamento do Azure para dados em repouso](../storage/common/storage-service-encryption.md) e [criptografia de dados do Azure em repouso](../security/fundamentals/encryption-atrest.md).

Quando você cria um [ambiente do serviço de integração (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) para hospedar seus aplicativos lógicos e deseja obter mais controle sobre as chaves de criptografia usadas pelo armazenamento do Azure, você pode configurar, usar e gerenciar sua própria chave usando [Azure Key Vault](../key-vault/key-vault-overview.md). Esse recurso também é conhecido como "Bring Your Own Key" (BYOK) e sua chave é chamada de "chave gerenciada pelo cliente".

Este tópico mostra como configurar e especificar sua própria chave de criptografia a ser usada ao criar o ISE. 

## <a name="considerations"></a>Considerações

* Neste momento, o suporte de chave gerenciada pelo cliente para um ISE está disponível somente nestas regiões do Azure: oeste dos EUA 2, leste dos EUA e Sul EUA Central

* Você pode especificar uma chave gerenciada pelo cliente *somente quando criar o ISE*, não depois. Não é possível desabilitar essa chave após a criação do ISE. No momento, não existe suporte para a rotação de uma chave gerenciada pelo cliente para um ISE.

* Para dar suporte a chaves gerenciadas pelo cliente, seu ISE requer que o tenha sua [identidade gerenciada atribuída pelo sistema](../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-work) habilitada. Essa identidade permite que o ISE autentique o acesso a recursos em outros locatários Azure Active Directory (Azure AD) para que você não precise entrar com suas credenciais.

* Atualmente, para criar um ISE que dê suporte a chaves gerenciadas pelo cliente e tenha sua identidade atribuída pelo sistema habilitada, você precisa chamar a API REST dos aplicativos lógicos usando uma solicitação HTTPS PUT.

* Em *30 minutos* depois de enviar a solicitação HTTPS Put que cria o ISE, você deve [fornecer acesso ao cofre de chaves para a identidade atribuída pelo sistema do ISE](#identity-access-to-key-vault). Caso contrário, a criação do ISE falhará e lançará um erro de permissões.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/).

* Um cofre de chaves do Azure que tem as propriedades **exclusão reversível** e **não limpar** habilitadas

  Para obter mais informações sobre como habilitar essas propriedades, consulte [visão geral Azure Key Vault exclusão reversível](../key-vault/key-vault-ovw-soft-delete.md) e [Configurar chaves gerenciadas pelo cliente com Azure Key Vault](../storage/common/storage-encryption-keys-portal.md). Se você for novo no Azure Key Vault, saiba [como criar um cofre de chaves](../key-vault/quick-create-portal.md#create-a-vault) usando o portal do Azure ou usando o comando Azure PowerShell, [New-AzKeyVault](https://docs.microsoft.com/powershell/module/az.keyvault/new-azkeyvault).

* No cofre de chaves, uma chave criada com esses valores de propriedade:

  | Propriedade | Valor |
  |----------|-------|
  | **Tipo de chave** | RSA |
  | **Tamanho da chave RSA** | 2048 |
  | **Ativado** | Sim |
  |||

  ![Criar sua chave de criptografia gerenciada pelo cliente](./media/customer-managed-keys-integration-service-environment/create-customer-managed-key-for-encryption.png)

  Para obter mais informações, consulte [Configurar chaves gerenciadas pelo cliente com Azure Key Vault](../storage/common/storage-encryption-keys-portal.md) ou o comando Azure PowerShell, [Add-AzKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyvault/Add-AzKeyVaultKey).

* Uma ferramenta que você pode usar para criar seu ISE chamando a API REST de aplicativos lógicos com uma solicitação HTTPS PUT. Por exemplo, você pode usar o [postmaster](https://www.getpostman.com/downloads/)ou pode criar um aplicativo lógico que executa essa tarefa.

<a name="enable-support-key-system-identity"></a>

## <a name="create-ise-with-key-vault-and-managed-identity-support"></a>Criar ISE com o cofre de chaves e o suporte de identidade gerenciada

Para criar seu ISE chamando a API REST dos aplicativos lógicos, faça esta solicitação HTTPS PUT:

`PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/integrationServiceEnvironments/{integrationServiceEnvironmentName}?api-version=2019-05-01`

> [!IMPORTANT]
> A versão 2019-05-01 da API REST dos aplicativos lógicos requer que você faça sua própria solicitação HTTP PUT para conectores do ISE.

### <a name="request-header"></a>Cabeçalho do pedido

No cabeçalho da solicitação, inclua estas propriedades:

* `Content-type`: defina esse valor de propriedade como `application/json`.

* `Authorization`: defina esse valor de propriedade para o token de portador para o cliente que tem acesso à assinatura do Azure ou ao grupo de recursos que você deseja usar.

### <a name="request-body"></a>Corpo do pedido

No corpo da solicitação, habilite o suporte para esses itens adicionais fornecendo suas informações em sua definição de ISE:

* A identidade gerenciada atribuída pelo sistema que seu ISE usa para acessar o cofre de chaves
* O cofre de chaves e a chave gerenciada pelo cliente que você deseja usar

#### <a name="request-body-syntax"></a>Sintaxe do corpo da solicitação

Aqui está a sintaxe do corpo da solicitação, que descreve as propriedades a serem usadas ao criar o ISE:

```json
{
   "id": "/subscriptions/{Azure-subscription-ID/resourceGroups/{Azure-resource-group}/providers/Microsoft.Logic/integrationServiceEnvironments/{ISE-name}",
   "name": "{ISE-name}",
   "type": "Microsoft.Logic/integrationServiceEnvironments",
   "location": "{Azure-region}",
   "sku": {
      "name": "Premium",
      "capacity": 1
   },
   "identity": {
      "type": "SystemAssigned"
   },
   "properties": {
      "networkConfiguration": {
         "accessEndpoint": {
            // Your ISE can use the "External" or "Internal" endpoint. This example uses "External".
            "type": "External"
         },
         "subnets": [
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-1}",
            },
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-2}",
            },
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-3}",
            },
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-4}",
            }
         ]
      },
      "encryptionConfiguration": {
         "encryptionKeyReference": {
            "keyVault": {
               "id": "subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.KeyVault/vaults/{key-vault-name}",
            },
            "keyName": "{customer-managed-key-name}",
            "keyVersion": "{key-version-number}"
         }
      }
   }
}
```

#### <a name="request-body-example"></a>Exemplo de corpo de solicitação

Este exemplo de corpo de solicitação mostra os valores de exemplo:

```json
{
   "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Logic/integrationServiceEnvironments/Fabrikam-ISE",
   "name": "Fabrikam-ISE",
   "type": "Microsoft.Logic/integrationServiceEnvironments",
   "location": "WestUS2",
   "identity": {
      "type": "SystemAssigned"
   },
   "sku": {
      "name": "Premium",
      "capacity": 1
   },
   "properties": {
      "networkConfiguration": {
         "accessEndpoint": {
            // Your ISE can use the "External" or "Internal" endpoint. This example uses "External".
            "type": "External"
         },
         "subnets": [
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-1",
            },
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-2",
            },
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-3",
            },
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-4",
            }
         ]
      },
      "encryptionConfiguration": {
         "encryptionKeyReference": {
            "keyVault": {
               "id": "subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.KeyVault/vaults/FabrikamKeyVault",
            },
            "keyName": "Fabrikam-Encryption-Key",
            "keyVersion": "********************"
         }
      }
   }
}
```

<a name="identity-access-to-key-vault"></a>

## <a name="grant-access-to-your-key-vault"></a>Conceder acesso ao cofre de chaves

Em *30 minutos* depois de enviar a solicitação HTTP PUT para criar o ISE, você deve adicionar uma política de acesso ao cofre de chaves para a identidade atribuída pelo sistema do ISE. Caso contrário, a criação de seu ISE falhará e você receberá um erro de permissões. 

Para essa tarefa, você pode usar o comando Azure PowerShell [set-AzKeyVaultAccessPolicy](https://docs.microsoft.com/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) ou pode seguir estas etapas na portal do Azure:

1. No [portal do Azure](https://portal.azure.com), abra o cofre de chaves do Azure.

1. No menu do cofre de chaves, selecione **políticas de acesso** > **Adicionar política de acesso**, por exemplo:

   ![Adicionar política de acesso para identidade gerenciada atribuída pelo sistema](./media/customer-managed-keys-integration-service-environment/add-ise-access-policy-key-vault.png)

1. Depois que o painel **Adicionar política de acesso** for aberto, siga estas etapas:

   1. Selecione estas opções:

      | Definição | Valores |
      |---------|--------|
      | **Configurar a partir do modelo (opcional) lista** | Gerenciamento de chaves |
      | **Permissões de chave** | - **operações de gerenciamento de chaves**: obter, listar <p><p>- **operações de criptografia**: desencapsular chave, encapsular chave |
      |||

      ![Selecione "gerenciamento de chaves" > "permissões de chave"](./media/customer-managed-keys-integration-service-environment/select-key-permissions.png)

   1. Para **selecionar entidade de segurança**, selecione **nenhum selecionado**. Depois que o painel **principal** for aberto, na caixa de pesquisa, localize e selecione o ISE. Quando terminar, escolha **selecionar** > **Adicionar**.

      ![Selecione o ISE para usar como o principal](./media/customer-managed-keys-integration-service-environment/select-service-principal-ise.png)

   1. Quando tiver terminado com o painel **políticas de acesso** , selecione **salvar**.

Para obter mais informações, consulte [fornecer Key Vault autenticação com uma identidade gerenciada](../key-vault/managed-identity.md#grant-your-app-access-to-key-vault).

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre o [Azure Key Vault](../key-vault/key-vault-overview.md)