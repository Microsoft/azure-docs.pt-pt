---
title: Configure Azure Key Vault firewalls e redes virtuais - Azure Key Vault
description: Instruções passo a passo para configurar firewalls key vault e redes virtuais
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 04/22/2020
ms.author: sudbalas
ms.custom: devx-track-azurecli
ms.openlocfilehash: 8617b0b71e58d22ccd2cf753e4ddc862932f68da
ms.sourcegitcommit: c52e50ea04dfb8d4da0e18735477b80cafccc2cf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/08/2020
ms.locfileid: "89536060"
---
# <a name="configure-azure-key-vault-firewalls-and-virtual-networks"></a>Configurar firewalls de cofre de chaves Azure e redes virtuais

Este artigo fornece instruções passo a passo para configurar firewalls E redes virtuais do Azure Key Vault para restringir o acesso ao cofre das chaves. Os [pontos finais do serviço de rede virtual para o Key Vault](overview-vnet-service-endpoints.md) permitem-lhe restringir o acesso a uma rede virtual especificada e a um conjunto de intervalos de endereços IPv4 (versão 4 do protocolo de internet).

> [!IMPORTANT]
> Após as regras de firewall estarem em vigor, os utilizadores só podem realizar operações [de plano de dados](secure-your-key-vault.md#data-plane-access-control) key Vault quando os seus pedidos são originários de redes virtuais permitidas ou intervalos de endereços IPv4. Isto também se aplica ao acesso ao Cofre de Chaves a partir do portal Azure. Embora os utilizadores possam navegar para um cofre chave a partir do portal Azure, podem não ser capazes de listar chaves, segredos ou certificados se a sua máquina cliente não estiver na lista permitida. Isto também afeta o Key Vault Picker por outros serviços Azure. Os utilizadores podem ser capazes de ver a lista de cofres chave, mas não listar chaves, se as regras de firewall impedirem a sua máquina de clientes.

> [!NOTE]
> Esteja atento às seguintes limitações de configuração:
> * São permitidas regras de rede virtual no máximo 127 e 127 regras IPv4. 
> * Não são suportadas pequenas gamas de endereços que utilizem os tamanhos do prefixo "/31" ou "/32". Em vez disso, configura estas gamas utilizando regras de endereço IP individuais.
> * As regras da rede IP só são permitidas para endereços IP públicos. Os intervalos de endereços IP reservados para redes privadas (tal como definido no RFC 1918) não são permitidos nas regras de IP. As redes privadas incluem endereços **172.16-31**que começam com **10.** **192.168.** 
> * Apenas os endereços IPv4 são suportados neste momento.

## <a name="use-the-azure-portal"></a>Utilizar o portal do Azure

Eis como configurar firewalls key vault e redes virtuais utilizando o portal Azure:

1. Navegue pelo cofre que deseja proteger.
2. Selecione **Networking**e, em seguida, selecione o separador **Firewalls e redes virtuais.**
3. Ao **Permitir o acesso a redes** **selecionadas.**
4. Para adicionar redes virtuais existentes a firewalls e regras de rede virtuais, selecione **+ Adicione as redes virtuais existentes**.
5. Na nova lâmina que abre, selecione a subscrição, redes virtuais e sub-redes que pretende permitir o acesso a este cofre de chaves. Se as redes virtuais e sub-redes selecionadas não tiverem pontos finais de serviço ativados, confirme que pretende ativar os pontos finais do serviço e selecione **Enable**. Pode levar até 15 minutos para fazer efeito.
6. Nas **redes IP**, adicione intervalos de endereços IPv4 digitando gamas de endereços IPv4 na [notação CIDR (Encaminhamento inter-domínio sem classe)](https://tools.ietf.org/html/rfc4632) ou endereços IP individuais.
7. Se pretender permitir que o Microsoft Trusted Services ignore a Firewall do Cofre de Chaves, selecione 'Sim'. Para obter uma lista completa dos atuais Serviços Fidedignos do Cofre de Chaves, consulte o seguinte link. [Azure Key Vault Serviços fidedignos](https://docs.microsoft.com/azure/key-vault/general/overview-vnet-service-endpoints#trusted-services)
7. Selecione **Guardar**.

Também pode adicionar novas redes virtuais e sub-redes e, em seguida, ativar pontos finais de serviço para as redes e sub-redes virtuais recém-criadas, selecionando **+ Adicionar nova rede virtual**. Em seguida, siga as instruções.

## <a name="use-the-azure-cli"></a>Utilizar a CLI do Azure 

Aqui está como configurar firewalls key vault e redes virtuais usando o Azure CLI

1. [Instale o Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) e [inscreva-se .](https://docs.microsoft.com/cli/azure/authenticate-azure-cli)

2. Listar as regras de rede virtual disponíveis. Se ainda não estabeleceu regras para este cofre, a lista estará vazia.
   ```azurecli
   az keyvault network-rule list --resource-group myresourcegroup --name mykeyvault
   ```

3. Ativar um ponto final de serviço para o Key Vault numa rede virtual e numa sub-rede existentes.
   ```azurecli
   az network vnet subnet update --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --service-endpoints "Microsoft.KeyVault"
   ```

4. Adicione uma regra de rede para uma rede virtual e sub-rede.
   ```azurecli
   subnetid=$(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
   az keyvault network-rule add --resource-group "demo9311" --name "demo9311premium" --subnet $subnetid
   ```

5. Adicione uma gama de endereços IP a partir do qual permitir o tráfego.
   ```azurecli
   az keyvault network-rule add --resource-group "myresourcegroup" --name "mykeyvault" --ip-address "191.10.18.0/24"
   ```

6. Se este cofre de chaves deve ser acessível por quaisquer serviços fidedignos, definido `bypass` para `AzureServices` .
   ```azurecli
   az keyvault update --resource-group "myresourcegroup" --name "mykeyvault" --bypass AzureServices
   ```

7. Ligue as regras da rede definindo a ação por defeito para `Deny` .
   ```azurecli
   az keyvault update --resource-group "myresourcegroup" --name "mekeyvault" --default-action Deny
   ```

## <a name="use-azure-powershell"></a>Utilizar o Azure PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Eis como configurar firewalls key vault e redes virtuais usando PowerShell:

1. Instale o mais recente [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps)e [inscreva-se .](https://docs.microsoft.com/powershell/azure/authenticate-azureps)

2. Listar as regras de rede virtual disponíveis. Se não estabeleceu nenhuma regra para este cofre, a lista estará vazia.
   ```powershell
   (Get-AzKeyVault -VaultName "mykeyvault").NetworkAcls
   ```

3. Ativar o ponto final de serviço do Key Vault numa rede virtual e numa sub-rede existentes.
   ```powershell
   Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Set-AzVirtualNetworkSubnetConfig -Name "mysubnet" -AddressPrefix "10.1.1.0/24" -ServiceEndpoint "Microsoft.KeyVault" | Set-AzVirtualNetwork
   ```

4. Adicione uma regra de rede para uma rede virtual e sub-rede.
   ```powershell
   $subnet = Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"
   Add-AzKeyVaultNetworkRule -VaultName "mykeyvault" -VirtualNetworkResourceId $subnet.Id
   ```

5. Adicione uma gama de endereços IP a partir do qual permitir o tráfego.
   ```powershell
   Add-AzKeyVaultNetworkRule -VaultName "mykeyvault" -IpAddressRange "16.17.18.0/24"
   ```

6. Se este cofre de chaves deve ser acessível por quaisquer serviços fidedignos, definido `bypass` para `AzureServices` .
   ```powershell
   Update-AzKeyVaultNetworkRuleSet -VaultName "mykeyvault" -Bypass AzureServices
   ```

7. Ligue as regras da rede definindo a ação por defeito para `Deny` .
   ```powershell
   Update-AzKeyVaultNetworkRuleSet -VaultName "mykeyvault" -DefaultAction Deny
   ```

## <a name="references"></a>Referências
* Referência do modelo do arm: [referência do modelo do braço do cofre da chave](https://docs.microsoft.com/azure/templates/Microsoft.KeyVault/vaults) azul
* Comandos Azure CLI: [regra da rede az keyvault](https://docs.microsoft.com/cli/azure/keyvault/network-rule?view=azure-cli-latest)
* Cmdlets Azure PowerShell: [Get-AzKeyVault](https://docs.microsoft.com/powershell/module/az.keyvault/get-azkeyvault), [Add-AzKeyVaultNetworkRule](https://docs.microsoft.com/powershell/module/az.KeyVault/Add-azKeyVaultNetworkRule), [Remove-AzKeyVaultNetworkRule](https://docs.microsoft.com/powershell/module/az.KeyVault/Remove-azKeyVaultNetworkRule), [Update-AzKeyVaultNetworkRuleSet](https://docs.microsoft.com/powershell/module/az.KeyVault/Update-azKeyVaultNetworkRuleSet)

## <a name="next-steps"></a>Passos seguintes

* [Pontos finais de serviço de rede virtual para Key Vault](overview-vnet-service-endpoints.md)
* [Proteja o cofre da chave](secure-your-key-vault.md)
