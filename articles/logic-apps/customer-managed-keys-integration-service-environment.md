---
title: Configurar chaves geridas pelo cliente para encriptar dados em repouso nas ISEs
description: Crie e gere as suas próprias chaves de encriptação para proteger dados em repouso para ambientes de serviços de integração (ISEs) em Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, rarayudu, logicappspm
ms.topic: conceptual
ms.date: 03/11/2020
ms.openlocfilehash: 30b09d43cbe510318ac4f48e0655d5483491c215
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94682779"
---
# <a name="set-up-customer-managed-keys-to-encrypt-data-at-rest-for-integration-service-environments-ises-in-azure-logic-apps"></a>Confiem chaves geridas pelo cliente para encriptar dados em repouso para ambientes de serviço de integração (ISEs) em Azure Logic Apps

A Azure Logic Apps conta com o Azure Storage para armazenar e encriptar automaticamente [os dados em repouso](../storage/common/storage-service-encryption.md). Esta encriptação protege os seus dados e ajuda-o a cumprir os seus compromissos de segurança organizacional e de conformidade. Por predefinição, o Azure Storage utiliza chaves geridas pela Microsoft para encriptar os seus dados. Para obter mais informações sobre o funcionamento da encriptação do Azure Storage, consulte [a encriptação do Armazenamento Azure para obter dados em repouso](../storage/common/storage-service-encryption.md) e [Azure Data Encryption-at-Rest](../security/fundamentals/encryption-atrest.md).

Quando criar um [ambiente de serviço de integração (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) para hospedar as suas aplicações lógicas, e pretender mais controlo sobre as chaves de encriptação utilizadas pelo Azure Storage, pode configurar, utilizar e gerir a sua própria chave utilizando o [Azure Key Vault](../key-vault/general/overview.md). Esta capacidade também é conhecida como "Bring Your Own Key" (BYOK), e a sua chave é chamada de "chave gerida pelo cliente".

Este tópico mostra como configurar e especificar a sua própria chave de encriptação para usar quando cria o ISE utilizando a API de Apps Lógicas REST. Para que os passos gerais para criar um ISE através de Apps Lógicas REST API, consulte [Criar um ambiente de serviço de integração (ISE) utilizando a API de Apps Lógicas](../logic-apps/create-integration-service-environment-rest-api.md)REST .

## <a name="considerations"></a>Considerações

* Neste momento, o suporte chave gerido pelo cliente para um ISE está disponível apenas nestas regiões Azure: West US 2, East US, e South Central US

* Só pode especificar uma chave gerida pelo cliente *quando criar o seu ISE,* não depois. Não pode desativar esta chave depois da criação do ISE. Atualmente, não existe qualquer suporte para a rotação de uma chave gerida pelo cliente para um ISE.

* Para suportar as chaves geridas pelo cliente, o ISE requer ter a sua [identidade gerida atribuída ao sistema](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) ativada. Esta identidade permite ao ISE autenticar o acesso aos recursos em outros inquilinos do Azure Ative Directory (Azure AD) para que não tenha de assinar com as suas credenciais.

* Atualmente, para criar um ISE que suporte chaves geridas pelo cliente e tenha a sua identidade atribuída ao sistema ativada, tem de ligar para a API de Apps Lógicas, utilizando um pedido HTTPS PUT.

* No prazo de *30 minutos* após o envio do pedido HTTPS PUT que cria o seu ISE, deve [dar acesso ao cofre de chaves à identidade atribuída ao sistema do ise.](#identity-access-to-key-vault) Caso contrário, a criação ise falha e lança um erro de permissões.

## <a name="prerequisites"></a>Pré-requisitos

* Os [mesmos pré-requisitos](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#prerequisites) e [requisitos para permitir o acesso ao seu ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#enable-access) como quando cria um ISE no portal Azure

* Um cofre-chave Azure que tem as propriedades **Soft Delete** e **Não Purgar** ativadas

  Para obter mais informações sobre como ativar estas propriedades, consulte [a visão geral do Azure Key Vault](../key-vault/general/soft-delete-overview.md) e [configuure as chaves geridas pelo cliente com o Azure Key Vault](../storage/common/customer-managed-keys-configure-key-vault.md). Se você é novo no [Azure Key Vault,](../key-vault/general/overview.md)aprenda a criar um cofre-chave usando [o portal Azure,](../key-vault/general/quick-create-portal.md) [Azure CLI](../key-vault/general/quick-create-cli.md)ou [Azure PowerShell](../key-vault/general/quick-create-powershell.md).

* No seu cofre chave, uma chave que é criada com estes valores de propriedade:

  | Propriedade | Valor |
  |----------|-------|
  | **Tipo chave** | RSA |
  | **Tamanho da chave RSA** | 2048 |
  | **Ativado** | Sim |
  |||

  ![Crie a sua chave de encriptação gerida pelo cliente](./media/customer-managed-keys-integration-service-environment/create-customer-managed-key-for-encryption.png)

  Para obter mais informações, consulte [as chaves geridas pelo cliente com cofre de chaves Azure](../storage/common/customer-managed-keys-configure-key-vault.md) ou o comando Azure PowerShell, [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey).

* Uma ferramenta que pode utilizar para criar o seu ISE, chamando a API de Apps Lógicas REST com um pedido HTTPS PUT. Por exemplo, pode usar [o Carteiro,](https://www.getpostman.com/downloads/)ou pode construir uma aplicação lógica que executa esta tarefa.

<a name="enable-support-key-system-identity"></a>

## <a name="create-ise-with-key-vault-and-managed-identity-support"></a>Criar ISE com cofre chave e suporte de identidade gerido

Para criar o seu ISE chamando a API de Apps Lógicas, faça este pedido HTTPS PUT:

`PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/integrationServiceEnvironments/{integrationServiceEnvironmentName}?api-version=2019-05-01`

> [!IMPORTANT]
> A versão Logic Apps REST API 2019-05-01 requer que faça o seu próprio pedido HTTP PUT para conectores ISE.

A implantação geralmente demora dentro de duas horas para terminar. Ocasionalmente, a implantação pode demorar até quatro horas. Para verificar o estado de implementação, no [portal Azure,](https://portal.azure.com)na sua barra de ferramentas Azure, selecione o ícone de notificações, que abre o painel de notificações.

> [!NOTE]
> Se a implementação falhar ou eliminar o ISE, o Azure pode demorar até uma hora antes de lançar as suas sub-redes. Este atraso significa que pode ter de esperar antes de reutilizar as sub-redes noutra ISE.
>
> Se eliminar a sua rede virtual, o Azure geralmente demora até duas horas antes de lançar as suas sub-redes, mas esta operação pode demorar mais tempo. 
> Ao eliminar redes virtuais, certifique-se de que ainda não há recursos ligados. 
> Ver [Eliminar rede virtual](../virtual-network/manage-virtual-network.md#delete-a-virtual-network).

### <a name="request-header"></a>Cabeçalho do pedido

No cabeçalho de pedido, inclua estas propriedades:

* `Content-type`: Desa esta propriedade valor para `application/json` .

* `Authorization`: Desaprova este valor de propriedade ao símbolo do portador para o cliente que tenha acesso à subscrição ou grupo de recursos Azure que pretende utilizar.

### <a name="request-body"></a>Corpo do pedido

No órgão de pedido, permita o suporte a estes itens adicionais, fornecendo as suas informações na sua definição ISE:

* A identidade gerida atribuída pelo sistema que o seu ISE utiliza para aceder ao cofre de chaves
* O cofre e a chave gerida pelo cliente que pretende utilizar

#### <a name="request-body-syntax"></a>Solicitar sintaxe corporal

Aqui está a sintaxe corporal de pedido, que descreve as propriedades a utilizar quando cria o seu ISE:

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

#### <a name="request-body-example"></a>Solicitar exemplo corporal

Este corpo de pedido de exemplo mostra os valores da amostra:

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

## <a name="grant-access-to-your-key-vault"></a>Conceder acesso ao seu cofre chave

No prazo de *30 minutos* após o envio do pedido HTTP PUT para criar o seu ISE, tem de adicionar uma política de acesso ao cofre-chave para a identidade atribuída ao sistema do ISE. Caso contrário, a criação para o seu ISE falha e obtém um erro de permissões. 

Para esta tarefa, pode utilizar o comando Azure PowerShell [Set-AzKeyVaultAccessPolicy,](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) ou pode seguir estes passos no portal Azure:

1. No [portal Azure,](https://portal.azure.com)abra o cofre da chave Azure.

1. No menu do cofre de chaves, selecione **Políticas de acesso** Adicionar Política de  >  **Acesso,** por exemplo:

   ![Adicionar política de acesso à identidade gerida atribuída pelo sistema](./media/customer-managed-keys-integration-service-environment/add-ise-access-policy-key-vault.png)

1. Depois de abrir o painel de política de **acesso Add,** siga estes passos:

   1. Selecione estas opções:

      | Definição | Valores |
      |---------|--------|
      | **Configurar da lista de modelos (opcional)** | Gestão de Chaves |
      | **Permissões-chave** | - **Principais Operações de Gestão**: Obter, Lista <p><p>- **Operações criptográficas**: Desembrulhar chave, chave de embrulho |
      |||

      ![Selecione "Key Management" > "Permissões-chave"](./media/customer-managed-keys-integration-service-environment/select-key-permissions.png)

   1. Para **selecionar o principal**, selecione Nenhum **selecionado**. Depois de o **painel principal** abrir, na caixa de pesquisa, encontre e selecione o seu ISE. Quando terminar, escolha **Select**  >  **Add**.

      ![Selecione o seu ISE para usar como principal](./media/customer-managed-keys-integration-service-environment/select-service-principal-ise.png)

   1. Quando terminar o painel de **políticas de Acesso,** selecione **Save**.

Para obter mais informações, consulte [como autenticar o Cofre de Chaves](../key-vault/general/authentication.md) e atribuir uma política de acesso ao Cofre de [Chaves](../key-vault/general/assign-access-policy-portal.md).

## <a name="next-steps"></a>Próximos passos

* Saiba mais sobre [o Azure Key Vault](../key-vault/general/overview.md)