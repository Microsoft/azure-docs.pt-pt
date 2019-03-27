---
ms.assetid: ''
title: Configurar o Azure Key Vault firewalls e redes virtuais - Azure Key Vault
description: Instruções passo a passo para configurar o Cofre de chaves firewalls e redes virtuais
services: key-vault
author: amitbapat
manager: barbkess
ms.service: key-vault
ms.topic: conceptual
ms.workload: identity
ms.date: 01/02/2019
ms.author: ambapat
ms.openlocfilehash: c54b78a24068758fabb0918cfeb7d6516fd1bce5
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2019
ms.locfileid: "58487248"
---
# <a name="configure-azure-key-vault-firewalls-and-virtual-networks"></a>Configurar o Azure Key Vault firewalls e redes virtuais

Este artigo fornece instruções passo a passo para configurar o Azure Key Vault firewalls e redes virtuais para restringir o acesso ao seu Cofre de chaves. O [pontos finais de serviço de rede virtual para o Key Vault](key-vault-overview-vnet-service-endpoints.md) permitem-lhe restringir o acesso a uma rede virtual especificada e um conjunto de intervalos de endereços IPv4 (protocolo IP versão 4).

> [!IMPORTANT]
> Depois de regras de firewall estão em vigor, os utilizadores apenas podem executar o Key Vault [plano de dados](../key-vault/key-vault-secure-your-key-vault.md#data-plane-access-control) permitidas de operações quando os pedidos têm origem nos intervalos de endereços IPv4 ou as redes virtuais. Isso também se aplica a aceder ao Cofre de chaves do portal do Azure. Embora os usuários podem navegar para um cofre de chaves do portal do Azure, eles poderão não conseguir listar chaves, segredos ou certificados se os respetivos computadores cliente não está na lista de permitidos. Isso também afeta o Seletor de Cofre de chave por outros serviços do Azure. Os utilizadores poderão ver a lista de cofres de chaves, mas não listar as chaves, se as regras de firewall impedem os respetivos computadores cliente.

## <a name="use-the-azure-portal"></a>Utilizar o portal do Azure

Eis como configurar o Cofre de chaves firewalls e redes virtuais com o portal do Azure:

1. Navegue para o Cofre de chaves que pretende proteger.
2. Selecione **Firewalls e redes virtuais**.
3. Sob **autorizar o acesso**, selecione **redes selecionadas**.
4. Para adicionar redes virtuais existentes para firewalls e regras de rede virtual, selecione **+ adicionar redes virtuais existentes**.
5. No novo painel apresentado, selecione a subscrição, redes virtuais e sub-redes que pretende permitir o acesso a este Cofre de chaves. Se a redes virtuais e sub-redes que selecionou não tiverem pontos finais de serviço ativados, confirme que pretende ativar pontos finais de serviço e selecione **ativar**. Poderá demorar até 15 minutos para entrar em vigor.
6. Sob **redes IP**, adicionar intervalos de endereços IPv4, escrevendo intervalos de endereços IPv4 no [notação CIDR (Classless Inter-domain Routing)](https://tools.ietf.org/html/rfc4632) ou endereços IP individuais.
7. Selecione **Guardar**.

Pode também adicionar novas redes virtuais e sub-redes e, em seguida, ativar pontos finais de serviço para o acabada de criar redes virtuais e sub-redes, selecionando **+ Adicionar nova rede virtual**. Em seguida, siga as instruções.

## <a name="use-the-azure-cli"></a>Utilizar a CLI do Azure 

Eis como configurar o Cofre de chaves firewalls e redes virtuais com a CLI do Azure

1. [Instalar a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) e [iniciar sessão](https://docs.microsoft.com/cli/azure/authenticate-azure-cli).

2. Listar regras de rede virtual disponível. Se ainda não definir quaisquer regras para este Cofre de chaves, a lista estará vazia.
   ```azurecli
   az keyvault network-rule list --resource-group myresourcegroup --name mykeyvault
   ```

3. Ative um ponto de extremidade de serviço para o Cofre de chaves numa rede virtual existente e a sub-rede.
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

6. Se este Cofre de chaves deve ser acessível a todos os serviços confiáveis, defina `bypass` para `AzureServices`.
   ```azurecli
   az keyvault update --resource-group "myresourcegroup" --name "mykeyvault" --bypass AzureServices
   ```

7. Ativar as regras de rede, definindo a ação predefinida `Deny`.
   ```azurecli
   az keyvault update --resource-group "myresourcegroup" --name "mekeyvault" --default-action Deny
   ```

## <a name="use-azure-powershell"></a>Utilizar o Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Eis como configurar o Cofre de chaves firewalls e redes virtuais com o PowerShell:

1. Instalar a versão mais recente [do Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps), e [iniciar sessão](https://docs.microsoft.com/powershell/azure/authenticate-azureps).

2. Listar regras de rede virtual disponível. Se não tiver definido a quaisquer regras para este Cofre de chaves, a lista estará vazia.
   ```powershell
   (Get-AzKeyVault -VaultName "mykeyvault").NetworkAcls
   ```

3. Ative o ponto final de serviço para o Key Vault numa rede virtual existente e a sub-rede.
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

6. Se este Cofre de chaves deve ser acessível a todos os serviços confiáveis, defina `bypass` para `AzureServices`.
   ```powershell
   Update-AzKeyVaultNetworkRuleSet -VaultName "mykeyvault" -Bypass AzureServices
   ```

7. Ativar as regras de rede, definindo a ação predefinida `Deny`.
   ```powershell
   Update-AzKeyVaultNetworkRuleSet -VaultName "mykeyvault" -DefaultAction Deny
   ```

## <a name="references"></a>Referências

* Os comandos da CLI do Azure: [az keyvault-regra de rede](https://docs.microsoft.com/cli/azure/keyvault/network-rule?view=azure-cli-latest)
* Cmdlets do Azure PowerShell: [Get-AzKeyVault](https://docs.microsoft.com/powershell/module/az.keyvault/get-azkeyvault), [Add-AzKeyVaultNetworkRule](https://docs.microsoft.com/powershell/module/az.KeyVault/Add-azKeyVaultNetworkRule), [Remove-AzKeyVaultNetworkRule](https://docs.microsoft.com/powershell/module/az.KeyVault/Remove-azKeyVaultNetworkRule), [Update-AzKeyVaultNetworkRuleSet](https://docs.microsoft.com/powershell/module/az.KeyVault/Update-azKeyVaultNetworkRuleSet)

## <a name="next-steps"></a>Passos Seguintes

* [Pontos finais de serviço de rede virtual para o Key Vault](key-vault-overview-vnet-service-endpoints.md)
* [Proteger o seu cofre de chaves](key-vault-secure-your-key-vault.md)
