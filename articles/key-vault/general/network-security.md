---
title: Configure Firewalls e redes virtuais do Cofre de Chaves Do Azure - Cofre chave Azure
description: Instruções passo a passo para configurar firewalls chave vault e redes virtuais
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 04/22/2020
ms.author: sudbalas
ms.openlocfilehash: 0438f573c33c71e0f30b7db1909e3649b21010a7
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82086594"
---
# <a name="configure-azure-key-vault-firewalls-and-virtual-networks"></a>Configure firewalls e redes virtuais do Cofre de Chaves

Este artigo fornece instruções passo a passo para configurar firewalls e redes virtuais do Cofre de Chaves Azure para restringir o acesso ao seu cofre chave. Os [pontos finais](overview-vnet-service-endpoints.md)do serviço de rede virtual para key vault ) permitem-lhe restringir o acesso a uma rede virtual especificada e ao conjunto de gamas de endereços IPv4 (versão 4 do protocolo de internet).

> [!IMPORTANT]
> Após a aplicação das regras de firewall, os utilizadores só podem realizar operações de plano de [dados](secure-your-key-vault.md#data-plane-access-control) key Vault quando os seus pedidos têm origem em redes virtuais permitidas ou em intervalos de endereços IPv4. Isto também se aplica ao acesso ao Cofre chave do portal Azure. Embora os utilizadores possam navegar para um cofre chave a partir do portal Azure, eles podem não ser capazes de listar chaves, segredos ou certificados se a sua máquina cliente não estiver na lista permitida. Isto também afeta o Key Vault Picker por outros serviços Azure. Os utilizadores podem ser capazes de ver a lista de cofres chave, mas não listar chaves, se as regras de firewall impedirem a sua máquina de clientes.

## <a name="use-the-azure-portal"></a>Utilizar o portal do Azure

Aqui está como configurar firewalls key vault e redes virtuais usando o portal Azure:

1. Navegue até ao cofre da chave que pretende proteger.
2. Selecione **Networking**e, em seguida, selecione o separador **Firewalls e redes virtuais.**
3. Em **permitir o acesso a partir de**, selecione redes **selecionadas**.
4. Para adicionar redes virtuais existentes a firewalls e regras de rede virtuais, selecione **+ Adicione as redes virtuais existentes**.
5. Na nova lâmina que abre, selecione a subscrição, redes virtuais e subredes que pretende permitir o acesso a este cofre chave. Se as redes virtuais e subredes selecionadas não tiverem pontos finais de serviço ativados, confirme que pretende ativar pontos finais de serviço e selecione **Enable**. Pode levar até 15 minutos para fazer efeito.
6. Em **redes IP**, adicione gamas de endereços IPv4 digitando gamas de endereços IPv4 em [notação CIDR (Encaminhamento inter-domínio sem classe)](https://tools.ietf.org/html/rfc4632) ou endereços IP individuais.
7. Selecione **Guardar**.

Também pode adicionar novas redes virtuais e subredes e, em seguida, ativar pontos finais de serviço para as redes virtuais e subnets recém-criadas, selecionando **+ Adicionar nova rede virtual**. Em seguida, siga as instruções.

## <a name="use-the-azure-cli"></a>Utilizar a CLI do Azure 

Aqui está como configurar firewalls key vault e redes virtuais usando o Azure CLI

1. [Instale o Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) e [inscreva-se](https://docs.microsoft.com/cli/azure/authenticate-azure-cli).

2. Lista de regras de rede virtual disponíveis. Se não estabeleceu nenhuma regra para este cofre, a lista estará vazia.
   ```azurecli
   az keyvault network-rule list --resource-group myresourcegroup --name mykeyvault
   ```

3. Ative um ponto final de serviço para o Key Vault numa rede virtual existente e subnet.
   ```azurecli
   az network vnet subnet update --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --service-endpoints "Microsoft.KeyVault"
   ```

4. Adicione uma regra de rede para uma rede virtual e subnet.
   ```azurecli
   subnetid=$(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
   az keyvault network-rule add --resource-group "demo9311" --name "demo9311premium" --subnet $subnetid
   ```

5. Adicione um intervalo de endereço IP a partir do qual permitir o tráfego.
   ```azurecli
   az keyvault network-rule add --resource-group "myresourcegroup" --name "mykeyvault" --ip-address "191.10.18.0/24"
   ```

6. Se este cofre-chave for acessível `bypass` por `AzureServices`quaisquer serviços fidedignos, definido para .
   ```azurecli
   az keyvault update --resource-group "myresourcegroup" --name "mykeyvault" --bypass AzureServices
   ```

7. Ligue as regras da rede definindo a ação predefinida para `Deny`.
   ```azurecli
   az keyvault update --resource-group "myresourcegroup" --name "mekeyvault" --default-action Deny
   ```

## <a name="use-azure-powershell"></a>Utilizar o Azure PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Aqui está como configurar firewalls key vault e redes virtuais usando powerShell:

1. Instale o mais recente [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps), e [inscreva-se](https://docs.microsoft.com/powershell/azure/authenticate-azureps).

2. Lista de regras de rede virtual disponíveis. Se não estabeleceu nenhuma regra para este cofre, a lista estará vazia.
   ```powershell
   (Get-AzKeyVault -VaultName "mykeyvault").NetworkAcls
   ```

3. Ative o ponto final de serviço para o Key Vault numa rede virtual existente e subnet.
   ```powershell
   Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Set-AzVirtualNetworkSubnetConfig -Name "mysubnet" -AddressPrefix "10.1.1.0/24" -ServiceEndpoint "Microsoft.KeyVault" | Set-AzVirtualNetwork
   ```

4. Adicione uma regra de rede para uma rede virtual e subnet.
   ```powershell
   $subnet = Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"
   Add-AzKeyVaultNetworkRule -VaultName "mykeyvault" -VirtualNetworkResourceId $subnet.Id
   ```

5. Adicione um intervalo de endereço IP a partir do qual permitir o tráfego.
   ```powershell
   Add-AzKeyVaultNetworkRule -VaultName "mykeyvault" -IpAddressRange "16.17.18.0/24"
   ```

6. Se este cofre-chave for acessível `bypass` por `AzureServices`quaisquer serviços fidedignos, definido para .
   ```powershell
   Update-AzKeyVaultNetworkRuleSet -VaultName "mykeyvault" -Bypass AzureServices
   ```

7. Ligue as regras da rede definindo a ação predefinida para `Deny`.
   ```powershell
   Update-AzKeyVaultNetworkRuleSet -VaultName "mykeyvault" -DefaultAction Deny
   ```

## <a name="references"></a>Referências

* Comandos Azure CLI: [regra da rede az keyvault](https://docs.microsoft.com/cli/azure/keyvault/network-rule?view=azure-cli-latest)
* Cmdlets Azure PowerShell: [Get-AzKeyVault](https://docs.microsoft.com/powershell/module/az.keyvault/get-azkeyvault), [Add-AzKeyVaultNetworkRule](https://docs.microsoft.com/powershell/module/az.KeyVault/Add-azKeyVaultNetworkRule), [Remove-AzKeyVaultNetworkRule,](https://docs.microsoft.com/powershell/module/az.KeyVault/Remove-azKeyVaultNetworkRule) [Update-AzKeyVaultNetworkRuleSet](https://docs.microsoft.com/powershell/module/az.KeyVault/Update-azKeyVaultNetworkRuleSet)

## <a name="next-steps"></a>Passos seguintes

* [Pontos finais](overview-vnet-service-endpoints.md)de serviço de rede virtual para Key Vault )
* [Proteja o seu cofre de chaves)](secure-your-key-vault.md)
