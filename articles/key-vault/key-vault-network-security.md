---
title: Configurar Azure Key Vault firewalls e redes virtuais-Azure Key Vault
description: Instruções detalhadas para configurar Key Vault firewalls e redes virtuais
services: key-vault
author: amitbapat
manager: rkarlin
ms.service: key-vault
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: ambapat
ms.openlocfilehash: 60378632a55fe4578bb376a3a00de5efffc5d275
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68976946"
---
# <a name="configure-azure-key-vault-firewalls-and-virtual-networks"></a>Configurar Azure Key Vault firewalls e redes virtuais

Este artigo fornece instruções passo a passo para configurar Azure Key Vault firewalls e redes virtuais para restringir o acesso ao cofre de chaves. Os [pontos de extremidade de serviço de rede virtual para Key Vault](key-vault-overview-vnet-service-endpoints.md) permitem restringir o acesso a uma rede virtual especificada e a um conjunto de intervalos de endereços IPv4 (protocolo IP versão 4).

> [!IMPORTANT]
> Depois que as regras de firewall estiverem em vigor, os usuários só poderão executar Key Vault operações do [plano de dados](../key-vault/key-vault-secure-your-key-vault.md#data-plane-access-control) quando suas solicitações se originarem de redes virtuais permitidas ou intervalos de endereços IPv4. Isso também se aplica ao acesso a Key Vault do portal do Azure. Embora os usuários possam navegar para um cofre de chaves do portal do Azure, eles podem não ser capazes de listar chaves, segredos ou certificados se o computador cliente não estiver na lista de permissões. Isso também afeta o seletor de Key Vault por outros serviços do Azure. Os usuários podem ver a lista de cofres de chaves, mas não as chaves de lista, se as regras de firewall impedirem seu computador cliente.

## <a name="use-the-azure-portal"></a>Utilizar o portal do Azure

Veja como configurar Key Vault firewalls e redes virtuais usando o portal do Azure:

1. Navegue até o cofre de chaves que você deseja proteger.
2. Selecione **firewalls e redes virtuais**.
3. Em **permitir acesso de**, selecione **redes selecionadas**.
4. Para adicionar redes virtuais existentes a firewalls e regras de rede virtual, selecione **+ Adicionar redes virtuais existentes**.
5. Na nova folha que é aberta, selecione a assinatura, as redes virtuais e as sub-redes que você deseja permitir o acesso a esse cofre de chaves. Se as redes virtuais e sub-redes selecionadas não tiverem pontos de extremidade de serviço habilitados, confirme se você deseja habilitar os pontos de extremidade de serviço e selecione **habilitar**. Pode levar até 15 minutos para entrar em vigor.
6. Em **redes IP**, adicione intervalos de endereços IPv4 digitando intervalos de endereços IPv4 na [notação CIDR (roteamento entre domínios sem classificação)](https://tools.ietf.org/html/rfc4632) ou endereços IP individuais.
7. Selecione **Guardar**.

Você também pode adicionar novas redes virtuais e sub-redes e, em seguida, habilitar pontos de extremidade de serviço para as redes virtuais e sub-redes recém-criadas, selecionando **+ Adicionar nova rede virtual**. Em seguida, siga os prompts.

## <a name="use-the-azure-cli"></a>Utilizar a CLI do Azure 

Veja como configurar Key Vault firewalls e redes virtuais usando o CLI do Azure

1. [Instale CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) e [entre](https://docs.microsoft.com/cli/azure/authenticate-azure-cli).

2. Listar regras de rede virtual disponíveis. Se você não tiver definido nenhuma regra para esse cofre de chaves, a lista estará vazia.
   ```azurecli
   az keyvault network-rule list --resource-group myresourcegroup --name mykeyvault
   ```

3. Habilite um ponto de extremidade de serviço para Key Vault em uma rede virtual e sub-rede existentes.
   ```azurecli
   az network vnet subnet update --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --service-endpoints "Microsoft.KeyVault"
   ```

4. Adicione uma regra de rede para uma rede virtual e uma sub-rede.
   ```azurecli
   subnetid=$(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
   az keyvault network-rule add --resource-group "demo9311" --name "demo9311premium" --subnet $subnetid
   ```

5. Adicione um intervalo de endereços IP do qual permitir o tráfego.
   ```azurecli
   az keyvault network-rule add --resource-group "myresourcegroup" --name "mykeyvault" --ip-address "191.10.18.0/24"
   ```

6. Se esse cofre de chaves deve ser acessível por qualquer serviço confiável, `bypass` defina `AzureServices`como.
   ```azurecli
   az keyvault update --resource-group "myresourcegroup" --name "mykeyvault" --bypass AzureServices
   ```

7. Ative as regras de rede ao definir a ação padrão como `Deny`.
   ```azurecli
   az keyvault update --resource-group "myresourcegroup" --name "mekeyvault" --default-action Deny
   ```

## <a name="use-azure-powershell"></a>Utilizar o Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Veja como configurar Key Vault firewalls e redes virtuais usando o PowerShell:

1. Instale a [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps)mais recente e [entre](https://docs.microsoft.com/powershell/azure/authenticate-azureps).

2. Listar regras de rede virtual disponíveis. Se você não tiver definido nenhuma regra para esse cofre de chaves, a lista estará vazia.
   ```powershell
   (Get-AzKeyVault -VaultName "mykeyvault").NetworkAcls
   ```

3. Habilite o ponto de extremidade de serviço para Key Vault em uma rede virtual e sub-rede existentes.
   ```powershell
   Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Set-AzVirtualNetworkSubnetConfig -Name "mysubnet" -AddressPrefix "10.1.1.0/24" -ServiceEndpoint "Microsoft.KeyVault" | Set-AzVirtualNetwork
   ```

4. Adicione uma regra de rede para uma rede virtual e uma sub-rede.
   ```powershell
   $subnet = Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"
   Add-AzKeyVaultNetworkRule -VaultName "mykeyvault" -VirtualNetworkResourceId $subnet.Id
   ```

5. Adicione um intervalo de endereços IP do qual permitir o tráfego.
   ```powershell
   Add-AzKeyVaultNetworkRule -VaultName "mykeyvault" -IpAddressRange "16.17.18.0/24"
   ```

6. Se esse cofre de chaves deve ser acessível por qualquer serviço confiável, `bypass` defina `AzureServices`como.
   ```powershell
   Update-AzKeyVaultNetworkRuleSet -VaultName "mykeyvault" -Bypass AzureServices
   ```

7. Ative as regras de rede ao definir a ação padrão como `Deny`.
   ```powershell
   Update-AzKeyVaultNetworkRuleSet -VaultName "mykeyvault" -DefaultAction Deny
   ```

## <a name="references"></a>Referências

* Comandos de CLI do Azure: [AZ keyvault Network – Rule](https://docs.microsoft.com/cli/azure/keyvault/network-rule?view=azure-cli-latest)
* Azure PowerShell cmdlets: [Get-AzKeyVault](https://docs.microsoft.com/powershell/module/az.keyvault/get-azkeyvault), [Add-AzKeyVaultNetworkRule](https://docs.microsoft.com/powershell/module/az.KeyVault/Add-azKeyVaultNetworkRule), [Remove-AzKeyVaultNetworkRule](https://docs.microsoft.com/powershell/module/az.KeyVault/Remove-azKeyVaultNetworkRule), [Update-AzKeyVaultNetworkRuleSet](https://docs.microsoft.com/powershell/module/az.KeyVault/Update-azKeyVaultNetworkRuleSet)

## <a name="next-steps"></a>Passos Seguintes

* [Pontos de extremidade de serviço de rede virtual para Key Vault](key-vault-overview-vnet-service-endpoints.md)
* [Proteger o seu cofre de chaves](key-vault-secure-your-key-vault.md)
