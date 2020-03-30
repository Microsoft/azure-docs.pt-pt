---
title: 'Azure ExpressRoute: Configure MACsec'
description: Este artigo ajuda-o a configurar o MACsec para proteger as ligações entre os routers de borda e os routers de borda da Microsoft.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: cherylmc
ms.openlocfilehash: 626302845dfb4b19deb921675601818b35ab8edb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74083553"
---
# <a name="configure-macsec-on-expressroute-direct-ports"></a>Configure MACsec nas portas ExpressRoute Direct

Este artigo ajuda-o a configurar o MACsec para proteger as ligações entre os routers de borda e os routers de borda da Microsoft utilizando o PowerShell.

## <a name="before-you-begin"></a>Antes de começar

Antes de iniciar a configuração, confirme o seguinte:

* Compreende os fluxos de trabalho de [fornecimento direto expressRoute Direct](expressroute-erdirect-about.md).
* Criou um [recurso portuário ExpressRoute Direct.](expressroute-howto-erdirect.md)
* Se pretender executar o PowerShell localmente, verifique se a versão mais recente do Azure PowerShell está instalada no seu computador.

### <a name="working-with-azure-powershell"></a>Trabalhar com a Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

### <a name="sign-in-and-select-the-right-subscription"></a>Iniciar sessão e selecionar a subscrição certa

Para iniciar a configuração, inicie o sessão na sua conta Azure e selecione a subscrição que pretende utilizar.

   [!INCLUDE [sign in](../../includes/expressroute-cloud-shell-connect.md)]

## <a name="1-create-azure-key-vault-macsec-secrets-and-user-identity"></a>1. Criar cofre chave Azure, segredos MACsec e identidade do utilizador

1. Crie uma instância key vault para armazenar segredos mACsec num novo grupo de recursos.

    ```azurepowershell-interactive
    New-AzResourceGroup -Name "your_resource_group" -Location "resource_location"
    $keyVault = New-AzKeyVault -Name "your_key_vault_name" -ResourceGroupName "your_resource_group" -Location "resource_location" -EnableSoftDelete 
    ```

    Se já tem um cofre chave ou um grupo de recursos, pode reutilizá-los. No entanto, é fundamental que ative a funcionalidade [ **de eliminação suave** ](../key-vault/key-vault-ovw-soft-delete.md) no seu cofre chave existente. Se o soft-delete não estiver ativado, pode utilizar os seguintes comandos para o ativar:

    ```azurepowershell-interactive
    ($resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "your_existing_keyvault").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"
    Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
    ```
2. Criar uma identidade de utilizador.

    ```azurepowershell-interactive
    $identity = New-AzUserAssignedIdentity  -Name "identity_name" -Location "resource_location" -ResourceGroupName "your_resource_group"
    ```

    Se a Identidade Não For reconhecida como uma cmdlet PowerShell válida, instale o seguinte módulo (no modo Administrador) e reexecutar o comando acima.

    ```azurepowershell-interactive
    Install-Module -Name Az.ManagedServiceIdentity
    ```
3. Crie uma chave de associação de conectividade (CAK) e um nome-chave da associação de conectividade (CKN) e guarde-os no cofre chave.

    ```azurepowershell-interactive
    $CAK = ConvertTo-SecureString "your_key" -AsPlainText -Force
    $CKN = ConvertTo-SecureString "your_key_name" -AsPlainText -Force
    $MACsecCAKSecret = Set-AzKeyVaultSecret -VaultName "your_key_vault_name" -Name "CAK_name" -SecretValue $CAK
    $MACsecCKNSecret = Set-AzKeyVaultSecret -VaultName "your_key_vault_name" -Name "CKN_name" -SecretValue $CKN
    ```
4. Atribuir a permissão GET à identidade do utilizador.

    ```azurepowershell-interactive
    Set-AzKeyVaultAccessPolicy -VaultName "your_key_vault_name" -PermissionsToSecrets get -ObjectId $identity.PrincipalId
    ```

   Agora esta identidade pode obter os segredos, por exemplo CAK e CKN, do cofre chave.
5. Detete esta identidade de utilizador para ser utilizada pela ExpressRoute.

    ```azurepowershell-interactive
    $erIdentity = New-AzExpressRoutePortIdentity -UserAssignedIdentityId $identity.Id
    ```
 
## <a name="2-configure-macsec-on-expressroute-direct-ports"></a>2. Configure MACsec nas portas diretas expressRoute

### <a name="to-enable-macsec"></a>Para ativar o MACsec

Cada instância ExpressRoute Direct tem duas portas físicas. Pode optar por ativar o MACsec em ambas as portas ao mesmo tempo ou ativar o MACsec numa das portas de cada vez. Fazê-lo uma porta de cada vez (mudando o tráfego para uma porta ativa enquanto está a servir a outra porta) pode ajudar a minimizar a interrupção se o seu ExpressRoute Direct já estiver em serviço.

1. Desconte os segredos da MACsec e cifra e associe a identidade do utilizador à porta para que o código de gestão ExpressRoute possa aceder aos segredos do MACsec, se necessário.

    ```azurepowershell-interactive
    $erDirect = Get-AzExpressRoutePort -ResourceGroupName "your_resource_group" -Name "your_direct_port_name"
    $erDirect.Links[0]. MacSecConfig.CknSecretIdentifier = $MacSecCKNSecret.Id
    $erDirect.Links[0]. MacSecConfig.CakSecretIdentifier = $MacSecCAKSecret.Id
    $erDirect.Links[0]. MacSecConfig.Cipher = "GcmAes256"
    $erDirect.Links[1]. MacSecConfig.CknSecretIdentifier = $MacSecCKNSecret.Id
    $erDirect.Links[1]. MacSecConfig.CakSecretIdentifier = $MacSecCAKSecret.Id
    $erDirect.Links[1]. MacSecConfig.Cipher = "GcmAes256"
    $erDirect.identity = $erIdentity
    Set-AzExpressRoutePort -ExpressRoutePort $erDirect
    ```
2. (Opcional) Se os portos estiverem em Estado de Down Administrativo, pode executar os seguintes comandos para trazer as portas.

    ```azurepowershell-interactive
    $erDirect = Get-AzExpressRoutePort -ResourceGroupName "your_resource_group" -Name "your_direct_port_name"
    $erDirect.Links[0].AdminState = "Enabled"
    $erDirect.Links[1].AdminState = "Enabled"
    Set-AzExpressRoutePort -ExpressRoutePort $erDirect
    ```

    Neste ponto, o MACsec está ativado nas portas ExpressRoute Direct do lado da Microsoft. Se ainda não o configurou nos seus dispositivos de borda, pode proceder a configurá-los com os mesmos segredos e cifra sinuosos.

### <a name="to-disable-macsec"></a>Para desativar o MACsec

Se o MACsec já não for desejado na sua instância ExpressRoute Direct, pode executar os seguintes comandos para desativá-lo.

```azurepowershell-interactive
$erDirect = Get-AzExpressRoutePort -ResourceGroupName "your_resource_group" -Name "your_direct_port_name"
$erDirect.Links[0]. MacSecConfig.CknSecretIdentifier = $null
$erDirect.Links[0]. MacSecConfig.CakSecretIdentifier = $null
$erDirect.Links[1]. MacSecConfig.CknSecretIdentifier = $null
$erDirect.Links[1]. MacSecConfig.CakSecretIdentifier = $null
$erDirect.identity = $null
Set-AzExpressRoutePort -ExpressRoutePort $erDirect
```

Neste ponto, o MACsec é desativado nas portas ExpressRoute Direct do lado da Microsoft.

### <a name="test-connectivity"></a>Testar conectividade
Depois de configurar o MACsec (incluindo a atualização da chave MACsec) nas portas ExpressRoute Direct, [verifique](expressroute-troubleshooting-expressroute-overview.md) se as sessões de BGP dos circuitos estão a funcionar. Se ainda não tiver nenhum circuito nas portas, por favor crie um primeiro e instale o Azure Private Peering ou o Microsoft Peering do circuito. Se o MACsec estiver mal configurado, incluindo o desfasamento da chave MACsec, entre os dispositivos de rede e os dispositivos de rede da Microsoft, não verá a resolução ARP na camada 2 e o estabelecimento bGP na camada 3. Se tudo estiver configurado corretamente, deverá ver as rotas bGP publicitadas corretamente em ambas as direções e os dados da sua aplicação fluem em conformidade com o ExpressRoute.

## <a name="next-steps"></a>Passos seguintes
1. [Criar um circuito ExpressRoute no ExpressRoute Direct](expressroute-howto-erdirect.md)
2. [Ligue um circuito ExpressRoute a uma rede virtual Azure](expressroute-howto-linkvnet-arm.md)
3. [Verifique a conectividade ExpressRoute](expressroute-troubleshooting-expressroute-overview.md)
