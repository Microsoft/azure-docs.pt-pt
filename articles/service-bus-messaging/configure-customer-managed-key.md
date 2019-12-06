---
title: Configure sua própria chave para criptografar dados do barramento de serviço do Azure em repouso
description: Este artigo fornece informações sobre como configurar sua própria chave para criptografar dados REST do barramento de serviço do Azure.
services: service-bus-messaging
ms.service: service-bus
documentationcenter: ''
author: axisc
ms.topic: conceptual
ms.date: 11/15/2019
ms.author: aschhab
ms.openlocfilehash: 356f825524192c3b6cf7df7f0460975f23ea4f7c
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74852292"
---
# <a name="configure-customer-managed-keys-for-encrypting-azure-service-bus-data-at-rest-by-using-the-azure-portal-preview"></a>Configurar chaves gerenciadas pelo cliente para criptografar dados do barramento de serviço do Azure em repouso usando o portal do Azure (versão prévia)
O barramento de serviço Premium do Azure fornece criptografia de dados em repouso com Criptografia do Serviço de Armazenamento do Azure (Azure SSE). O barramento de serviço Premium depende do armazenamento do Azure para armazenar os dados e, por padrão, todos os dados armazenados com o armazenamento do Azure são criptografados usando chaves gerenciadas pela Microsoft. 

## <a name="overview"></a>Visão geral
O barramento de serviço do Azure agora dá suporte à opção de criptografar dados em repouso com chaves gerenciadas pela Microsoft ou chaves gerenciadas pelo cliente (Bring Your Own Key-BYOK). Esse recurso permite que você crie, gire, desabilite e revogue o acesso às chaves gerenciadas pelo cliente que são usadas para criptografar o barramento de serviço do Azure em repouso.

Habilitar o recurso BYOK é um processo de instalação única em seu namespace.

> [!NOTE]
> Há algumas advertências para a chave gerenciada pelo cliente para criptografia do lado do serviço. 
>   * Esse recurso é suportado pela camada [Premium do barramento de serviço do Azure](service-bus-premium-messaging.md) . Ele não pode ser habilitado para namespaces de barramento de serviço da camada Standard.
>   * A criptografia só pode ser habilitada para namespaces novos ou vazios. Se o namespace contiver dados, a operação de criptografia falhará.
>   * Se os [pontos de extremidade de serviço de rede virtual (VNet)](service-bus-service-endpoints.md) estiverem configurados em Azure Key Vault para o namespace do barramento de serviço, o BYOK não terá suporte. 

Você pode usar Azure Key Vault para gerenciar suas chaves e auditar o uso da chave. Você pode criar suas próprias chaves e armazená-las em um cofre de chaves ou pode usar as APIs de Azure Key Vault para gerar chaves. Para obter mais informações sobre Azure Key Vault, consulte [o que é Azure Key Vault?](../key-vault/key-vault-overview.md)

Este artigo mostra como configurar um cofre de chaves com chaves gerenciadas pelo cliente usando o portal do Azure. Para saber como criar um cofre de chaves usando o portal do Azure, consulte [início rápido: definir e recuperar um segredo de Azure Key Vault usando o portal do Azure](../key-vault/quick-create-portal.md).

> [!IMPORTANT]
> O uso de chaves gerenciadas pelo cliente com o barramento de serviço do Azure requer que o cofre de chaves tenha duas propriedades necessárias configuradas. Eles são: **exclusão reversível** e **não limpeza**. Essas propriedades são habilitadas por padrão quando você cria um novo cofre de chaves no portal do Azure. No entanto, se você precisar habilitar essas propriedades em um cofre de chaves existente, deverá usar o PowerShell ou CLI do Azure.

## <a name="enable-customer-managed-keys"></a>Habilitar chaves gerenciadas pelo cliente
Para habilitar as chaves gerenciadas pelo cliente no portal do Azure, siga estas etapas:

1. Navegue até o namespace Premium do barramento de serviço.
2. Na página **configurações** do namespace do barramento de serviço, selecione **criptografia (versão prévia)** .
3. Selecione a **criptografia de chave gerenciada pelo cliente em repouso** , conforme mostrado na imagem a seguir.

    ![Habilitar chave gerenciada pelo cliente](./media/configure-customer-managed-key/enable-customer-managed-key.png)


## <a name="set-up-a-key-vault-with-keys"></a>Configurar um cofre de chaves com chaves

Depois de habilitar as chaves gerenciadas pelo cliente, você precisa associar a chave gerenciada pelo cliente ao namespace do barramento de serviço do Azure. O barramento de serviço dá suporte apenas a Azure Key Vault. Se você habilitar a opção **criptografia com chave gerenciada pelo cliente** na seção anterior, precisará ter a chave importada para Azure Key Vault. Além disso, as chaves devem ter **exclusão reversível** e **não limpar** configuradas para a chave. Essas configurações podem ser configuradas usando o [PowerShell](../key-vault/key-vault-soft-delete-powershell.md) ou a [CLI](../key-vault/key-vault-soft-delete-cli.md#enabling-purge-protection).

1. Para criar um novo cofre de chaves, siga o guia de [início rápido](../key-vault/key-vault-overview.md)do Azure Key Vault. Para obter mais informações sobre como importar chaves existentes, consulte [sobre chaves, segredos e certificados](../key-vault/about-keys-secrets-and-certificates.md).
1. Para ativar a exclusão reversível e limpar a proteção ao criar um cofre, use o comando [AZ keyvault Create](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create) .

    ```azurecli-interactive
    az keyvault create --name contoso-SB-BYOK-keyvault --resource-group ContosoRG --location westus --enable-soft-delete true --enable-purge-protection true
    ```    
1. Para adicionar a proteção de limpeza a um cofre existente (que já tem a exclusão reversível habilitada), use o comando [AZ keyvault Update](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-update) .

    ```azurecli-interactive
    az keyvault update --name contoso-SB-BYOK-keyvault --resource-group ContosoRG --enable-purge-protection true
    ```
1. Crie chaves seguindo estas etapas:
    1. Para criar uma nova chave, selecione **gerar/importar** no menu **chaves** em **configurações**.
        
        ![Selecionar botão gerar/importar](./media/configure-customer-managed-key/select-generate-import.png)

    1. Defina **as opções** para **gerar** e dar um nome à chave.

        ![Criar uma chave](./media/configure-customer-managed-key/create-key.png) 

    1. Agora você pode selecionar essa chave para associar ao namespace do barramento de serviço para criptografia na lista suspensa. 

        ![Selecionar chave do Key Vault](./media/configure-customer-managed-key/select-key-from-key-vault.png)
        > [!NOTE]
        > Para redundância, você pode adicionar até três chaves. Caso uma das chaves tenha expirado ou não esteja acessível, as outras chaves serão usadas para criptografia.
        
    1. Preencha os detalhes da chave e clique em **selecionar**. Isso habilitará a criptografia de dados em repouso no namespace com uma chave gerenciada pelo cliente. 


> [!IMPORTANT]
> Se você pretende usar a chave gerenciada pelo cliente juntamente com a recuperação de desastres geográficos, leia o abaixo- 
>
> Para habilitar a criptografia em repouso com a chave gerenciada pelo cliente, uma [política de acesso](../key-vault/key-vault-secure-your-key-vault.md) é configurada para a identidade gerenciada do barramento de serviço no Azure keyvault especificado. Isso garante o acesso controlado ao Azure keyvault do namespace do barramento de serviço do Azure.
>
> Devido a isso:
> 
>   * Se a [recuperação de desastre geográfica](service-bus-geo-dr.md) já estiver habilitada para o namespace do barramento de serviço e você estiver procurando habilitar a chave gerenciada pelo cliente, então 
>     * Interromper o emparelhamento
>     * [Configure a política de acesso](../key-vault/managed-identity.md) para a identidade gerenciada para os namespaces primário e secundário para o cofre de chaves.
>     * Configure a criptografia no namespace primário.
>     * Emparelhe novamente os namespaces primário e secundário.
> 
>   * Se você estiver procurando habilitar a DR geográfica em um namespace do barramento de serviço em que a chave gerenciada pelo cliente já está configurada,
>     * [Configure a política de acesso](../key-vault/managed-identity.md) para a identidade gerenciada para o namespace secundário para o cofre de chaves.
>     * Emparelhe os namespaces primários e secundários.


## <a name="rotate-your-encryption-keys"></a>Girar suas chaves de criptografia

Você pode girar sua chave no cofre de chaves usando o mecanismo de rotação do Azure Key Vaults. Para obter mais informações, consulte [Configurar a rotação de chaves e a auditoria](../key-vault/key-vault-key-rotation-log-monitoring.md). As datas de ativação e expiração também podem ser definidas para automatizar a rotação de chaves. O serviço do barramento de serviço detectará novas versões de chave e começará a usá-las automaticamente.

## <a name="revoke-access-to-keys"></a>Revogar o acesso às chaves

Revogar o acesso às chaves de criptografia não limpará os dados do barramento de serviço. No entanto, os dados não podem ser acessados no namespace do barramento de serviço. Você pode revogar a chave de criptografia por meio da política de acesso ou excluindo a chave. Saiba mais sobre as políticas de acesso e proteger o cofre de chaves de [acesso seguro a um cofre de chaves](../key-vault/key-vault-secure-your-key-vault.md).

Depois que a chave de criptografia for revogada, o serviço do barramento de serviço no namespace criptografado se tornará inoperável. Se o acesso à chave estiver habilitado ou a chave excluída for restaurada, o serviço do barramento de serviço escolherá a chave para que você possa acessar os dados do namespace do barramento de serviço criptografado.

> [!NOTE]
> Se você excluir uma chave de criptografia existente do cofre de chaves e substituí-la por uma nova chave no namespace do barramento de serviço, como a chave de exclusão ainda é válida (pois ela é armazenada em cache) por até uma hora, seus dados antigos (que foram criptografados com a chave antiga) ainda podem estar acessíveis junto g com os novos dados, que agora são acessíveis apenas usando a nova chave. Esse comportamento é por design na versão de visualização do recurso. 

## <a name="next-steps"></a>Passos seguintes
Consulte os seguintes artigos:
- [Visão geral do barramento de serviço](service-bus-messaging-overview.md)
- [Visão geral de Key Vault](../key-vault/key-vault-overview.md)


