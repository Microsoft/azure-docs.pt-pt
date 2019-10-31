---
title: 'Configurar o MACsec-ExpressRoute: Azure | Microsoft Docs'
description: Este artigo ajuda você a configurar o MACsec para proteger as conexões entre os roteadores de borda e os roteadores de borda da Microsoft.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: cherylmc
ms.openlocfilehash: 36cd1bfebe4e5379a1c8cfc551c8e003459ebf5c
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73162443"
---
# <a name="configure-macsec-on-expressroute-direct-ports"></a>Configurar o MACsec em portas diretas do ExpressRoute

Este artigo ajuda você a configurar o MACsec para proteger as conexões entre os roteadores de borda e os roteadores de borda da Microsoft usando o PowerShell.

## <a name="before-you-begin"></a>Antes de começar

Antes de iniciar a configuração, confirme o seguinte:

* Você compreende os [fluxos de trabalho de provisionamento direto do ExpressRoute](expressroute-erdirect-about.md).
* Você criou um [recurso de porta direta do ExpressRoute](expressroute-howto-erdirect.md).
* Se você quiser executar o PowerShell localmente, verifique se a versão mais recente do Azure PowerShell está instalada em seu computador.

### <a name="working-with-azure-powershell"></a>Trabalhando com Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

### <a name="sign-in-and-select-the-right-subscription"></a>Entre e selecione a assinatura correta

Para iniciar a configuração, entre em sua conta do Azure e selecione a assinatura que você deseja usar.

   [!INCLUDE [sign in](../../includes/expressroute-cloud-shell-connect.md)]

## <a name="1-create-azure-key-vault-macsec-secrets-and-user-identity"></a>1. criar Azure Key Vault, segredos MACsec e identidade do usuário

1. Crie uma instância de Key Vault para armazenar segredos do MACsec em um novo grupo de recursos.

    ```azurepowershell-interactive
    New-AzResourceGroup -Name "your_resource_group" -Location "resource_location"
    $keyVault = New-AzKeyVault -Name "your_key_vault_name" -ResourceGroupName "your_resource_group" -Location "resource_location" -EnableSoftDelete 
    ```

    Se você já tiver um cofre de chaves ou um grupo de recursos, poderá reutilizá-los. No entanto, é essencial que você habilite o recurso de [ **exclusão reversível** ](https://docs.microsoft.com/en-us/azure/key-vault/key-vault-ovw-soft-delete) em seu cofre de chaves existente. Se a exclusão reversível não estiver habilitada, você poderá usar os seguintes comandos para habilitá-la:

    ```azurepowershell-interactive
    ($resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "your_existing_keyvault").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"
    Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
    ```
2. Crie uma identidade de usuário.

    ```azurepowershell-interactive
    $identity = New-AzUserAssignedIdentity  -Name "identity_name" -Location "resource_location" -ResourceGroupName "your_resource_group"
    ```

    Se New-AzUserAssignedIdentity não for reconhecido como um cmdlet válido do PowerShell, instale o módulo a seguir (no modo de administrador) e execute novamente o comando acima.

    ```azurepowershell-interactive
    Install-Module -Name Az.ManagedServiceIdentity
    ```
3. Crie uma CAK (chave de associação de conectividade) e um CKN (nome da chave de associação de conectividade) e armazene-as no cofre de chaves.

    ```azurepowershell-interactive
    $CAK = ConvertTo-SecureString "your_key" -AsPlainText -Force
    $CKN = ConvertTo-SecureString "your_key_name" -AsPlainText -Force
    $MACsecCAKSecret = Set-AzKeyVaultSecret -VaultName "your_key_vault_name" -Name "CAK_name" -SecretValue $CAK
    $MACsecCKNSecret = Set-AzKeyVaultSecret -VaultName "your_key_vault_name" -Name "CKN_name" -SecretValue $CKN
    ```
4. Atribua a permissão GET à identidade do usuário.

    ```azurepowershell-interactive
    Set-AzKeyVaultAccessPolicy -VaultName "your_key_vault_name" -PermissionsToSecrets get -ObjectId $identity.PrincipalId
    ```

   Agora essa identidade pode obter os segredos, por exemplo, CAK e CKN, do cofre de chaves.
5. Defina essa identidade de usuário para ser usada pelo ExpressRoute.

    ```azurepowershell-interactive
    $erIdentity = New-AzExpressRoutePortIdentity -UserAssignedIdentityId $identity.Id
    ```
 
## <a name="2-configure-macsec-on-expressroute-direct-ports"></a>2. configurar o MACsec nas portas diretas do ExpressRoute

### <a name="to-enable-macsec"></a>Para habilitar MACsec

Cada instância direta do ExpressRoute tem duas portas físicas. Você pode optar por habilitar o MACsec em ambas as portas ao mesmo tempo ou habilitar o MACsec em uma porta por vez. Fazer uma porta no tempo (alternando o tráfego para uma porta ativa durante a manutenção da outra porta) pode ajudar a minimizar a interrupção se o ExpressRoute Direct já estiver em serviço.

1. Defina os segredos e a codificação do MACsec e associe a identidade do usuário à porta para que o código de gerenciamento do ExpressRoute possa acessar os segredos do MACsec, se necessário.

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
2. Adicional Se as portas estiverem no estado de administração inativo, você poderá executar os comandos a seguir para abrir as portas.

    ```azurepowershell-interactive
    $erDirect = Get-AzExpressRoutePort -ResourceGroupName "your_resource_group" -Name "your_direct_port_name"
    $erDirect.Links[0].AdminState = "Enabled"
    $erDirect.Links[1].AdminState = "Enabled"
    Set-AzExpressRoutePort -ExpressRoutePort $erDirect
    ```

    Neste ponto, o MACsec está habilitado nas portas diretas do ExpressRoute no lado da Microsoft. Se você ainda não o tiver configurado em seus dispositivos de borda, poderá continuar a configurá-los com os mesmos segredos e codificação do MACsec.

### <a name="to-disable-macsec"></a>Para desabilitar o MACsec

Se MACsec não for mais desejado em sua instância do ExpressRoute Direct, você poderá executar os comandos a seguir para desabilitá-lo.

```azurepowershell-interactive
$erDirect = Get-AzExpressRoutePort -ResourceGroupName "your_resource_group" -Name "your_direct_port_name"
$erDirect.Links[0]. MacSecConfig.CknSecretIdentifier = $null
$erDirect.Links[0]. MacSecConfig.CakSecretIdentifier = $null
$erDirect.Links[1]. MacSecConfig.CknSecretIdentifier = $null
$erDirect.Links[1]. MacSecConfig.CakSecretIdentifier = $null
$erDirect.identity = $null
Set-AzExpressRoutePort -ExpressRoutePort $erDirect
```

Neste ponto, o MACsec está desabilitado nas portas diretas do ExpressRoute no lado da Microsoft.

### <a name="test-connectivity"></a>Testar conectividade
Depois de configurar o MACsec (incluindo a atualização de chave do MACsec) nas portas do ExpressRoute Direct, [Verifique](expressroute-troubleshooting-expressroute-overview.md) se as sessões BGP dos circuitos estão ativas e em execução. Se você ainda não tiver nenhum circuito nas portas, crie um primeiro e configure o emparelhamento privado do Azure ou o emparelhamento da Microsoft do circuito. Se MACsec estiver configurado incorretamente, incluindo incompatibilidade de chave MACsec, entre os dispositivos de rede e os dispositivos de rede da Microsoft, você não verá a resolução ARP na camada 2 e no estabelecimento de BGP na camada 3. Se tudo estiver configurado corretamente, você deverá ver as rotas BGP anunciadas corretamente em ambas as direções e o fluxo de dados do aplicativo de acordo com o ExpressRoute.

## <a name="next-steps"></a>Passos seguintes
1. [Criar um circuito do ExpressRoute no ExpressRoute Direct](expressroute-howto-erdirect.md)
2. [Vincular um circuito do ExpressRoute a uma rede virtual do Azure](expressroute-howto-linkvnet-arm.md)
3. [Verificar a conectividade do ExpressRoute](expressroute-troubleshooting-expressroute-overview.md)
