---
title: Configurar chaves geridas pelo cliente para encriptar dados em repouso nas ISEs
description: Crie e gere as suas próprias chaves de encriptação para proteger dados em repouso para ambientes de serviçode integração (ISEs) em Aplicações Lógicas Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, rarayudu, logicappspm
ms.topic: conceptual
ms.date: 03/11/2020
ms.openlocfilehash: fa39c8f65b00283044ef31dc7577a4668b3e634b
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2020
ms.locfileid: "79127643"
---
# <a name="set-up-customer-managed-keys-to-encrypt-data-at-rest-for-integration-service-environments-ises-in-azure-logic-apps"></a>Configurar chaves geridas pelo cliente para encriptar dados em repouso para ambientes de serviçode integração (ISEs) em Aplicações Lógicas Azure

As Aplicações Lógicas Azure contam com o Armazenamento Azure para armazenar e encriptar automaticamente [os dados em repouso.](../storage/common/storage-service-encryption.md) Esta encriptação protege os seus dados e ajuda-o a cumprir os seus compromissos de segurança organizacional e conformidade. Por padrão, o Azure Storage utiliza chaves geridas pela Microsoft para encriptar os seus dados. Para obter mais informações sobre como funciona a encriptação do Armazenamento Azure, consulte [a encriptação do Armazenamento Azure para obter dados em repouso](../storage/common/storage-service-encryption.md) e [encriptação de dados azure-at-rest](../security/fundamentals/encryption-atrest.md).

Quando cria um ambiente de serviço de [integração (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) para hospedar as suas aplicações lógicas, e quer mais controlo sobre as chaves de encriptação utilizadas pelo Azure Storage, pode configurar, utilizar e gerir a sua própria chave utilizando o [Cofre de Chaves Azure](../key-vault/key-vault-overview.md). Esta capacidade também é conhecida como "Bring Your Own Key" (BYOK), e a sua chave chama-se "chave gerida pelo cliente".

Este tópico mostra como configurar e especificar a sua própria chave de encriptação para usar quando criar o seu ISE utilizando as Aplicações Lógicas REST API. Para os passos gerais para criar um ISE através de Logic Apps REST API, consulte Criar um ambiente de serviço de [integração (ISE) utilizando as Aplicações Lógicas REST API](../logic-apps/create-integration-service-environment-rest-api.md).

## <a name="considerations"></a>Considerações

* Neste momento, o suporte-chave gerido pelo cliente para um ISE só está disponível nestas regiões de Azure: West US 2, East US e South Central US

* Só pode especificar uma chave gerida pelo cliente *quando criar o seu ISE,* não depois. Não pode desativar esta chave depois da criação do ISE. Atualmente, não existe qualquer suporte para rodar uma chave gerida pelo cliente para um ISE.

* Para suportar as chaves geridas pelo cliente, o ISE requer que a sua [identidade gerida seja ativada pelo sistema.](../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-work) Esta identidade permite ao ISE autenticar o acesso aos recursos de outros inquilinos do Azure Ative Directory (Azure AD) para que não tenha de assinar com as suas credenciais.

* Atualmente, para criar um ISE que suporte chaves geridas pelo cliente e tenha a sua identidade atribuída ao sistema ativada, tem de ligar para as Aplicações Lógicas REST API utilizando um pedido HTTPS PUT.

* No prazo de *30 minutos* após o envio do pedido HTTPS PUT que cria o seu ISE, deve dar acesso ao cofre chave à identidade atribuída ao [seu ISE](#identity-access-to-key-vault). Caso contrário, a criação do ISE falha e lança um erro de permissões.

## <a name="prerequisites"></a>Pré-requisitos

* Os [mesmos pré-requisitos](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#prerequisites) e [requisitos para permitir o acesso ao seu ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#enable-access) como quando cria um ISE no portal Azure

* Um cofre chave Azure que tem as propriedades **Soft Delete** e **Não Purgar**

  Para obter mais informações sobre a ativação destas propriedades, consulte a visão geral do [Cofre de Chaves Azure](../key-vault/key-vault-ovw-soft-delete.md) e [configure as chaves geridas pelo cliente com o Cofre de Chaves Azure](../storage/common/storage-encryption-keys-portal.md). Se você é novo no Cofre chave Azure, aprenda [a criar um cofre chave](../key-vault/quick-create-portal.md#create-a-vault) usando o portal Azure ou usando o comando Azure PowerShell, [New-AzKeyVault](https://docs.microsoft.com/powershell/module/az.keyvault/new-azkeyvault).

* No seu cofre chave, uma chave que é criada com estes valores de propriedade:

  | Propriedade | Valor |
  |----------|-------|
  | **Tipo chave** | RSA |
  | **Tamanho da chave RSA** | 2048 |
  | **Ativado** | Sim |
  |||

  ![Crie a sua chave de encriptação gerida pelo cliente](./media/customer-managed-keys-integration-service-environment/create-customer-managed-key-for-encryption.png)

  Para mais informações, consulte [as chaves geridas pelo cliente Configure com](../storage/common/storage-encryption-keys-portal.md) o Cofre de Chave Azure ou o comando Azure PowerShell, [Add-AzKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyvault/Add-AzKeyVaultKey).

* Uma ferramenta que pode utilizar para criar o seu ISE, chamando as Aplicações Lógicas REST API com um pedido HTTPS PUT. Por exemplo, pode usar [o Carteiro](https://www.getpostman.com/downloads/), ou pode construir uma aplicação lógica que execute esta tarefa.

<a name="enable-support-key-system-identity"></a>

## <a name="create-ise-with-key-vault-and-managed-identity-support"></a>Criar ise com cofre chave e suporte de identidade gerido

Para criar o seu ISE, chamando as Aplicações Lógicas REST API, faça este pedido HTTPS PUT:

`PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/integrationServiceEnvironments/{integrationServiceEnvironmentName}?api-version=2019-05-01`

> [!IMPORTANT]
> A versão Logic Apps REST API 2019-05-01 requer que faça o seu próprio pedido HTTP PUT para conectores ISE.

O destacamento geralmente leva dentro de duas horas para terminar. Ocasionalmente, o destacamento pode demorar até quatro horas. Para verificar o estado de implantação, no [portal Azure,](https://portal.azure.com)na sua barra de ferramentas Azure, selecione o ícone de notificações, que abre o painel de notificações.

> [!NOTE]
> Se a implementação falhar ou eliminar o ise, o Azure pode demorar até uma hora antes de libertar as suas subredes. Este atraso significa que pode ter de esperar antes de reutilizar as subredes noutro ISE.
>
> Se eliminar a sua rede virtual, o Azure geralmente demora até duas horas antes de libertar as suas subredes, mas esta operação pode demorar mais tempo. 
> Ao apagar redes virtuais, certifique-se de que não há recursos ainda ligados. 
> Ver [Eliminar rede virtual](../virtual-network/manage-virtual-network.md#delete-a-virtual-network).

### <a name="request-header"></a>Cabeçalho do pedido

No cabeçalho de pedido, inclua estas propriedades:

* `Content-type`: Detete este valor de propriedade para `application/json`.

* `Authorization`: Detete este valor de propriedade ao cliente que tenha acesso à subscrição do Azure ou ao grupo de recursos que pretende utilizar.

### <a name="request-body"></a>Corpo do pedido

No organismo de pedido, permita o suporte a estes itens adicionais fornecendo as suas informações na sua definição ISE:

* A identidade gerida atribuída pelo sistema que o seu ISE usa para aceder ao seu cofre chave
* O seu cofre chave e a chave gerida pelo cliente que pretende utilizar

#### <a name="request-body-syntax"></a>Solicitar sintaxe do corpo

Aqui está a sintaxe do corpo de pedidos, que descreve as propriedades a utilizar quando cria o seu ISE:

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

#### <a name="request-body-example"></a>Solicitar exemplo de corpo

Este organismo de pedido de exemplo mostra os valores da amostra:

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

## <a name="grant-access-to-your-key-vault"></a>Conceda acesso ao seu cofre chave

No prazo de *30 minutos* após o envio do pedido HTTP PUT para criar o seu ISE, deve adicionar uma política de acesso ao seu cofre chave para a identidade atribuída ao seu ise. Caso contrário, a criação para o seu ISE falha, e obtém um erro de permissões. 

Para esta tarefa, pode utilizar o comando Azure PowerShell [Set-AzKeyVaultAccessPolicy,](https://docs.microsoft.com/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) ou pode seguir estes passos no portal Azure:

1. No [portal Azure,](https://portal.azure.com)abra o seu cofre chave Azure.

1. No seu menu de cofre chave, selecione políticas de **acesso** > Adicionar Política de **Acesso,** por exemplo:

   ![Adicionar política de acesso para identidade gerida atribuída pelo sistema](./media/customer-managed-keys-integration-service-environment/add-ise-access-policy-key-vault.png)

1. Depois de abrir o painel de política de **acesso Add,** siga estes passos:

   1. Selecione estas opções:

      | Definição | Valores |
      |---------|--------|
      | **Configurar a partir da lista de modelos (opcional)** | Gestão chave |
      | **Principais permissões** | - **Operações de Gestão chave**: Obter, Lista <p><p>**Operações Criptográficas**- : Chave de desembrulhar, Chave de Embrulho |
      |||

      ![Selecione "Key Management" > "Key permissions"](./media/customer-managed-keys-integration-service-environment/select-key-permissions.png)

   1. Para **selecionar o principal,** selecione **Nenhum selecionado**. Depois do painel **principal** abrir, na caixa de pesquisa, encontre e selecione o seu ISE. Quando terminar, escolha **Selecione** > **Adicionar**.

      ![Selecione o seu ISE para usar como principal](./media/customer-managed-keys-integration-service-environment/select-service-principal-ise.png)

   1. Quando terminar o painel de políticas de **acesso,** selecione **Save**.

Para mais informações, consulte [Fornecer a autenticação do Cofre chave com uma identidade gerida](../key-vault/managed-identity.md#grant-your-app-access-to-key-vault).

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [o Cofre de Chaves Azure](../key-vault/key-vault-overview.md)