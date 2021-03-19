---
title: Configurar preferência de encaminhamento de rede
titleSuffix: Azure Storage
description: Configure a preferência de encaminhamento de rede para a sua conta de armazenamento Azure para especificar como o tráfego de rede é encaminhado para a sua conta a partir de clientes através da Internet.
services: storage
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 03/17/2021
ms.author: normesta
ms.reviewer: santoshc
ms.subservice: common
ms.openlocfilehash: 0738f7e427c2ff094c9b6df7539ba67dff80d095
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104589859"
---
# <a name="configure-network-routing-preference-for-azure-storage"></a>Configure preferência de encaminhamento de rede para armazenamento Azure

Este artigo descreve como pode configurar a preferência de encaminhamento de rede e pontos finais específicos da rota para a sua conta de armazenamento. 

A preferência de encaminhamento de rede especifica como o tráfego de rede é encaminhado para a sua conta a partir de clientes através da internet. Os pontos finais específicos da rota são novos pontos finais que o Azure Storage cria para a sua conta de armazenamento. Estes pontos finais encaminham o tráfego por um caminho desejado sem alterar a sua preferência de encaminhamento padrão. Para saber mais, consulte [a preferência de encaminhamento de rede para armazenamento Azure](network-routing-preference.md).

## <a name="configure-the-routing-preference-for-the-default-public-endpoint"></a>Configure a preferência de encaminhamento para o ponto final público predefinido

Por padrão, a preferência de encaminhamento para o ponto final público da conta de armazenamento é definida para a rede global da Microsoft. Pode escolher entre a rede global da Microsoft e o encaminhamento da Internet como a preferência por padrão de encaminhamento para o ponto final público da sua conta de armazenamento. Para saber mais sobre a diferença entre estes dois tipos de encaminhamento, consulte [a preferência de encaminhamento de rede para armazenamento Azure](network-routing-preference.md). 

### <a name="portal"></a>[Portal](#tab/azure-portal)

Para alterar a sua preferência de encaminhamento para o encaminhamento da Internet:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

2. Navegue para a sua conta de armazenamento no portal.

3. Em **Definições**, escolha **Rede**.

    > [!div class="mx-imgBorder"]
    > ![Opção de menu de rede](./media/configure-network-routing-preference/networking-option.png)

4.  No separador **Firewalls e redes virtuais,** no **âmbito do Network Encaminhamento,** altere a definição **de preferência de encaminhamento** para o **encaminhamento da Internet**.

5.  Clique em **Guardar**.

    > [!div class="mx-imgBorder"]
    > ![opção de encaminhamento de internet](./media/configure-network-routing-preference/internet-routing-option.png)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Inscreva-se na sua subscrição Azure com o `Connect-AzAccount` comando e siga as instruções no ecrã para autenticar.

   ```powershell
   Connect-AzAccount
   ```

2. Se a sua identidade estiver associada a mais de uma subscrição, então desa estalem a sua subscrição ativa para a subscrição da conta de armazenamento que irá hospedar o seu website estático.

   ```powershell
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

   Substitua o `<subscription-id>` valor do espaço reservado pelo ID da sua subscrição.

3. Para alterar a sua preferência de encaminhamento para o encaminhamento da Internet, utilize o comando [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) e desa um `--routing-choice` parâmetro para `InternetRouting` .

   ```powershell
   Set-AzStorageAccount -ResourceGroupName <resource-group-name> `
    -AccountName <storage-account-name> `
    -RoutingChoice InternetRouting
   ```

   Substitua o `<resource-group-name>` valor do espaço reservado pelo nome do grupo de recursos que contém a conta de armazenamento.

   Substitua o `<storage-account-name>` valor do espaço reservado pelo nome da conta de armazenamento.

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

1. Inscreva-se na sua assinatura Azure.

   - Para lançar a Azure Cloud Shell, inscreva-se no [portal Azure](https://portal.azure.com).

   - Para iniciar sessão na instalação local do CLI, execute o comando [de login az:](/cli/azure/reference-index#az-login)

     ```azurecli
     az login
     ```
2. Se a sua identidade estiver associada a mais de uma subscrição, então desa estalem a sua subscrição ativa para a subscrição da conta de armazenamento que irá hospedar o seu website estático.

   ```azurecli
   az account set --subscription <subscription-id>
   ```

   Substitua o `<subscription-id>` valor do espaço reservado pelo ID da sua subscrição.

3. Para alterar a sua preferência de encaminhamento de encaminhamento para internet, utilize o comando de atualização da [conta de armazenamento az](/cli/azure/storage/account#az_storage_account_update) e desave o parâmetro para `--routing-choice` `InternetRouting` .

   ```azurecli
   az storage account update --name <storage-account-name> --routing-choice InternetRouting
   ```

   Substitua o `<storage-account-name>` valor do espaço reservado pelo nome da sua conta de armazenamento.

---

## <a name="configure-a-route-specific-endpoint"></a>Configure um ponto final específico da rota

Também pode configurar um ponto final específico da rota. Por exemplo, pode definir a preferência de encaminhamento para o ponto final padrão para o *encaminhamento da Internet*, e depois publicar um ponto final específico de rota que permite que o tráfego entre clientes na internet e a sua conta de armazenamento seja encaminhado através da rede global da Microsoft.

Esta preferência afeta apenas o ponto final específico da rota. Esta preferência não afeta a sua preferência de encaminhamento padrão.  

### <a name="portal"></a>[Portal](#tab/azure-portal)

1.  Navegue para a sua conta de armazenamento no portal.

2.  Em **Definições**, escolha **Rede**.

3.  No separador **Firewalls e redes virtuais,** em **Pontos finais específicos da rota,** escolha a preferência de encaminhamento do seu ponto final específico da rota e, em seguida, clique em **Guardar**.

    A imagem a seguir mostra a opção **de encaminhamento de rede** da Microsoft selecionada.

    > [!div class="mx-imgBorder"]
    > ![Opção de encaminhamento de rede microsoft](./media/configure-network-routing-preference/microsoft-network-routing-option.png)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Para configurar um ponto final específico da rota, utilize o comando [Set-AzStorageAccount.](/powershell/module/az.storage/set-azstorageaccount) 

   - Para criar um ponto final específico de rota que utilize a preferência de encaminhamento da rede Microsoft, desaponte o `-PublishMicrosoftEndpoint` parâmetro para `true` . 

   - Para criar um ponto final específico de rota que utilize a preferência de encaminhamento da Internet, desaponte o `-PublishInternetEndpointTo` parâmetro para `true` .  

   O exemplo a seguir cria um ponto final específico de rota que utiliza a preferência de encaminhamento de rede da Microsoft.

   ```powershell
   Set-AzStorageAccount -ResourceGroupName <resource-group-name> `
    -AccountName <storage-account-name> `
    -PublishMicrosoftEndpoint $true
   ```

   Substitua o `<resource-group-name>` valor do espaço reservado pelo nome do grupo de recursos que contém a conta de armazenamento.

   Substitua o `<storage-account-name>` valor do espaço reservado pelo nome da conta de armazenamento.

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

1. Para configurar um ponto final específico da rota, utilize o comando de atualização da [conta de armazenamento az.](/azure/storage/account#az-storage-account-update) 

   - Para criar um ponto final específico de rota que utilize a preferência de encaminhamento da rede Microsoft, desaponte o `--publish-microsoft-endpoints` parâmetro para `true` . 

   - Para criar um ponto final específico de rota que utilize a preferência de encaminhamento da Internet, desaponte o `--publish-internet-endpoints` parâmetro para `true` .  

   O exemplo a seguir cria um ponto final específico de rota que utiliza a preferência de encaminhamento de rede da Microsoft.

   ```azurecli
   az storage account update --name <storage-account-name> --publish-microsoft-endpoints true
   ```

   Substitua o `<storage-account-name>` valor do espaço reservado pelo nome da conta de armazenamento.

---

## <a name="find-the-endpoint-name-for-a-route-specific-endpoint"></a>Encontre o nome do ponto final para um ponto final específico da rota

Se configurar um ponto final específico da rota, pode encontrar o ponto final nas propriedades da sua conta de armazenamento.

### <a name="portal"></a>[Portal](#tab/azure-portal)

1.  Em **Definições,** escolha **Propriedades.**

    > [!div class="mx-imgBorder"]
    > ![opção de menu de propriedades](./media/configure-network-routing-preference/properties.png)

2.  O ponto final **de encaminhamento de rede da Microsoft** é mostrado para cada serviço que suporta preferências de encaminhamento. Esta imagem mostra o ponto final para os serviços de blob e ficheiros.

    > [!div class="mx-imgBorder"]
    > ![Opção de encaminhamento de rede da Microsoft para pontos finais específicos da rota](./media/configure-network-routing-preference/routing-url.png)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Para imprimir os pontos finais na consola, utilize a `PrimaryEndpoints` propriedade do objeto da conta de armazenamento.

   ```powershell
   Get-AzStorageAccount -ResourceGroupName <resource-group-name> -Name <storage-account-name>
   write-Output $StorageAccount.PrimaryEndpoints
   ```

   Substitua o `<resource-group-name>` valor do espaço reservado pelo nome do grupo de recursos que contém a conta de armazenamento.

   Substitua o `<storage-account-name>` valor do espaço reservado pelo nome da conta de armazenamento.

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

1. Para imprimir os pontos finais na consola, utilize a [conta de armazenamento az mostrar](/cli/azure/storage/account#az_storage_account_show) a propriedade do objeto da conta de armazenamento.

   ```azurecli
   az storage account show -g <resource-group-name> -n <storage-account-name>
   ```

   Substitua o `<resource-group-name>` valor do espaço reservado pelo nome do grupo de recursos que contém a conta de armazenamento.

   Substitua o `<storage-account-name>` valor do espaço reservado pelo nome da conta de armazenamento.

---

## <a name="see-also"></a>Ver também

- [Preferência de encaminhamento de rede](network-routing-preference.md)
- [Configurar as firewalls e as redes virtuais do Armazenamento do Microsoft Azure](storage-network-security.md)
- [Recomendações de segurança para armazenamento blob](../blobs/security-recommendations.md)
