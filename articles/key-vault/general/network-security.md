---
title: Configure Azure Key Vault firewalls e redes virtuais - Azure Key Vault
description: Instruções passo a passo para configurar firewalls key vault e redes virtuais
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 10/01/2020
ms.author: mbaldwin
ms.custom: devx-track-azurecli
ms.openlocfilehash: 13ead70f278f12866dbe00b53c487aebcc43304f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101742641"
---
# <a name="configure-azure-key-vault-firewalls-and-virtual-networks"></a>Configurar firewalls de cofre de chaves Azure e redes virtuais

Este artigo irá fornecer-lhe orientações sobre como configurar a firewall Azure Key Vault. Este documento cobrirá detalhadamente as diferentes configurações para a firewall do Cofre de Chaves e fornecerá instruções passo a passo sobre como configurar o Cofre de Chaves Azure para trabalhar com outras aplicações e serviços Azure.

Para obter mais informações, consulte [os pontos finais do serviço de rede virtual para Azure Key Vault](overview-vnet-service-endpoints.md).

## <a name="firewall-settings"></a>Definições de firewall

Esta secção cobrirá as diferentes formas de configurar a firewall Azure Key Vault.

### <a name="key-vault-firewall-disabled-default"></a>Firewall de cofre de chave desativado (Padrão)

Por defeito, quando criar um novo cofre de chaves, a firewall Azure Key Vault está desativada. Todas as aplicações e serviços da Azure podem aceder ao cofre da chave e enviar pedidos para o cofre da chave. Nota: esta configuração não significa que qualquer utilizador possa realizar operações no seu cofre de chaves. O cofre-chave ainda se restringe a segredos, chaves e certificados armazenados no cofre de chaves, exigindo permissões de autenticação e de acesso ao Azure Ative Directory. Para compreender a autenticação do cofre chave em mais detalhes, consulte o documento de fundamentação da autenticação do cofre [aqui.](./authentication-fundamentals.md)

### <a name="key-vault-firewall-enabled-trusted-services-only"></a>Firewall de cofre de chaves ativado (apenas serviços fidedignos)

Quando ativar o Firewall do Cofre de Chaves, será-lhe dada a opção de "Permitir que os Serviços Microsoft Fidedignos contornem esta firewall" A lista de serviços fidedignos não cobre todos os serviços Azure. Por exemplo, a Azure DevOps não está na lista de serviços fidedignos. **Isto não implica que os serviços que não aparecem na lista de serviços fidedignos não são de confiança ou inseguros.** A lista de serviços fidedignos engloba serviços onde a Microsoft controla todo o código que funciona no serviço. Uma vez que os utilizadores podem escrever código personalizado em serviços Azure, como o Azure DevOps, a Microsoft não oferece a opção de criar uma aprovação geral para o serviço. Além disso, só porque um serviço aparece na lista de serviços fidedignos, não significa que seja permitido para todos os cenários.

Para determinar se um serviço que está a tentar utilizar está na lista de serviços fidedignos, consulte [aqui](./overview-vnet-service-endpoints.md#trusted-services)o seguinte documento .

### <a name="key-vault-firewall-enabled-ipv4-addresses-and-ranges---static-ips"></a>Firewall de cofre de chaves ativado (endereços e intervalos IPv4 - IPs estáticos)

Se você quiser autorizar um determinado serviço para aceder ao cofre de chaves através da Firewall Key Vault, pode adicionar o seu endereço IP à lista de autorização de firewall do cofre de chaves. Esta configuração é melhor para serviços que usam endereços IP estáticos ou gamas bem conhecidas. Há um limite de 1000 gamas CIDR para este caso.

Para permitir um endereço IP ou alcance de um recurso Azure, como uma Web App ou Uma App Lógica, execute os seguintes passos.

1. Iniciar sessão no portal do Azure
1. Selecione o recurso (instância específica do serviço)
1. Clique na lâmina 'Propriedades' em 'Definições'
1. Procure o campo "Endereço IP".
1. Copie este valor ou alcance e introduza-o na lista de autorizações de firewall do cofre de chaves.

Para permitir um serviço Azure inteiro, através da firewall Key Vault, utilize a lista de endereços IP do centro de dados documentados publicamente para a Azure [aqui](https://www.microsoft.com/download/details.aspx?id=41653). Encontre os endereços IP associados ao serviço que deseja na região que deseja e adicione esses endereços IP à firewall do cofre de chaves utilizando os passos acima.

### <a name="key-vault-firewall-enabled-virtual-networks---dynamic-ips"></a>Firewall de cofre de chaves ativado (Redes Virtuais - IPs dinâmicos)

Se estiver a tentar permitir um recurso Azure, como uma máquina virtual através do cofre de chaves, poderá não ser capaz de utilizar endereços IP estáticos, e poderá não querer permitir que todos os endereços IP das Máquinas Virtuais Azure acedam ao cofre das chaves.

Neste caso, deve criar o recurso dentro de uma rede virtual e, em seguida, permitir que o tráfego a partir da rede virtual específica e sub-rede aceda ao seu cofre-chave. Para isso, execute os seguintes passos.

1. Iniciar sessão no portal do Azure
1. Selecione o cofre de chaves que deseja configurar
1. Selecione a lâmina 'Networking'
1. Selecione '+ Adicionar rede virtual existente'
1. Selecione a rede virtual e a sub-rede que pretende permitir através da firewall do cofre da chave.

### <a name="key-vault-firewall-enabled-private-link"></a>Firewall de cofre de chave ativado (Ligação Privada)

Para entender como configurar uma ligação de ligação privada no seu cofre de chaves, consulte o documento [aqui](./private-link-service.md).

> [!IMPORTANT]
> Após as regras de firewall estarem em vigor, os utilizadores só podem realizar operações [de plano de dados](secure-your-key-vault.md#data-plane-access-control) key Vault quando os seus pedidos são originários de redes virtuais permitidas ou intervalos de endereços IPv4. Isto também se aplica ao acesso ao Cofre de Chaves a partir do portal Azure. Embora os utilizadores possam navegar para um cofre chave a partir do portal Azure, podem não ser capazes de listar chaves, segredos ou certificados se a sua máquina cliente não estiver na lista permitida. Isto também afeta o Key Vault Picker por outros serviços Azure. Os utilizadores podem ser capazes de ver a lista de cofres chave, mas não listar chaves, se as regras de firewall impedirem a sua máquina de clientes.

> [!NOTE]
> Esteja atento às seguintes limitações de configuração:
> * São permitidas regras de rede virtual no máximo 127 e 127 regras IPv4. 
> * As regras da rede IP só são permitidas para endereços IP públicos. Os intervalos de endereços IP reservados para redes privadas (tal como definido no RFC 1918) não são permitidos nas regras de IP. As redes privadas incluem endereços que começam com **10.**  
> * Apenas os endereços IPv4 são suportados neste momento.

## <a name="use-the-azure-portal"></a>Utilizar o portal do Azure

Eis como configurar firewalls key vault e redes virtuais utilizando o portal Azure:

1. Navegue pelo cofre que deseja proteger.
2. Selecione **Networking** e, em seguida, selecione o separador **Firewalls e redes virtuais.**
3. Ao **Permitir o acesso a redes** **selecionadas.**
4. Para adicionar redes virtuais existentes a firewalls e regras de rede virtuais, selecione **+ Adicione as redes virtuais existentes**.
5. Na nova lâmina que abre, selecione a subscrição, redes virtuais e sub-redes que pretende permitir o acesso a este cofre de chaves. Se as redes virtuais e sub-redes selecionadas não tiverem pontos finais de serviço ativados, confirme que pretende ativar os pontos finais do serviço e selecione **Enable**. Pode levar até 15 minutos para fazer efeito.
6. Nas **redes IP**, adicione intervalos de endereços IPv4 digitando gamas de endereços IPv4 na [notação CIDR (Encaminhamento inter-domínio sem classe)](https://tools.ietf.org/html/rfc4632) ou endereços IP individuais.
7. Se pretender permitir que o Microsoft Trusted Services ignore a Firewall do Cofre de Chaves, selecione 'Sim'. Para obter uma lista completa dos atuais Serviços Fidedignos do Cofre de Chaves, consulte o seguinte link. [Azure Key Vault Serviços fidedignos](./overview-vnet-service-endpoints.md#trusted-services)
7. Selecione **Guardar**.

Também pode adicionar novas redes virtuais e sub-redes e, em seguida, ativar pontos finais de serviço para as redes e sub-redes virtuais recém-criadas, selecionando **+ Adicionar nova rede virtual**. Em seguida, siga as instruções.

## <a name="use-the-azure-cli"></a>Utilizar a CLI do Azure 

Aqui está como configurar firewalls key vault e redes virtuais usando o Azure CLI

1. [Instale o Azure CLI](/cli/azure/install-azure-cli) e [inscreva-se .](/cli/azure/authenticate-azure-cli)

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

1. Instale o mais recente [Azure PowerShell](/powershell/azure/install-az-ps)e [inscreva-se .](/powershell/azure/authenticate-azureps)

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
* Referência do modelo do arm: [referência do modelo do braço do cofre da chave](/azure/templates/Microsoft.KeyVault/vaults) azul
* Comandos Azure CLI: [regra da rede az keyvault](/cli/azure/keyvault/network-rule)
* Cmdlets Azure PowerShell: [Get-AzKeyVault](/powershell/module/az.keyvault/get-azkeyvault), [Add-AzKeyVaultNetworkRule](/powershell/module/az.KeyVault/Add-azKeyVaultNetworkRule), [Remove-AzKeyVaultNetworkRule](/powershell/module/az.KeyVault/Remove-azKeyVaultNetworkRule), [Update-AzKeyVaultNetworkRuleSet](/powershell/module/az.KeyVault/Update-azKeyVaultNetworkRuleSet)

## <a name="next-steps"></a>Passos seguintes

* [Pontos finais de serviço de rede virtual para Key Vault](overview-vnet-service-endpoints.md)
* [Visão geral da segurança do Cofre de Chaves Azure](security-overview.md)
