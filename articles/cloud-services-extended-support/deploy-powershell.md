---
title: Implementar um Serviço cloud (suporte alargado) - PowerShell
description: Implementar um Serviço de Cloud (suporte alargado) utilizando o PowerShell
ms.topic: tutorial
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: a7e4e76aa0619d78a91d766a9a43c0b1a02a48d3
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107717392"
---
# <a name="deploy-a-cloud-service-extended-support-using-azure-powershell"></a>Implementar um Serviço cloud (suporte alargado) utilizando a Azure PowerShell

Este artigo mostra como usar o `Az.CloudService` módulo PowerShell para implantar serviços cloud (suporte alargado) em Azure que tem múltiplas funções (WebRole e WorkerRole) e extensão remota do ambiente de trabalho. 

## <a name="before-you-begin"></a>Antes de começar

Reveja os [pré-requisitos](deploy-prerequisite.md) de implantação para serviços em nuvem (suporte alargado) e crie os recursos associados. 

## <a name="deploy-a-cloud-services-extended-support"></a>Implementar um Serviço de Cloud (suporte alargado)
1. Instale o módulo Az.CloudService PowerShell  

    ```powershell
    Install-Module -Name Az.CloudService 
    ```

2. Criar um novo grupo de recursos. Este passo é opcional se utilizar um grupo de recursos existente.   

    ```powershell
    New-AzResourceGroup -ResourceGroupName “ContosOrg” -Location “East US” 
    ```

3. Crie uma conta de armazenamento e um recipiente que será utilizado para armazenar os ficheiros cloud Service (.cspkg) e Service Configuration (.cscfg). Deve usar um nome único para o nome da conta de armazenamento. 

    ```powershell
    $storageAccount = New-AzStorageAccount -ResourceGroupName “ContosOrg” -Name “contosostorageaccount” -Location “East US” -SkuName “Standard_RAGRS” -Kind “StorageV2” 
    $container = New-AzStorageContainer -Name “contosocontainer” -Context $storageAccount.Context -Permission Blob 
    ```

4. Faça o upload do seu pacote cloud service (cspkg) para a conta de armazenamento.

    ```powershell
    $tokenStartTime = Get-Date 
    $tokenEndTime = $tokenStartTime.AddYears(1) 
    $cspkgBlob = Set-AzStorageBlobContent -File “./ContosoApp/ContosoApp.cspkg” -Container “contosocontainer” -Blob “ContosoApp.cspkg” -Context $storageAccount.Context 
    $cspkgToken = New-AzStorageBlobSASToken -Container “contosocontainer” -Blob $cspkgBlob.Name -Permission rwd -StartTime $tokenStartTime -ExpiryTime $tokenEndTime -Context $storageAccount.Context 
    $cspkgUrl = $cspkgBlob.ICloudBlob.Uri.AbsoluteUri + $cspkgToken 
    ```
 

5.  Faça o upload da configuração do serviço de nuvem (cscfg) para a conta de armazenamento. 

    ```powershell
    $cscfgBlob = Set-AzStorageBlobContent -File “./ContosoApp/ContosoApp.cscfg” -Container contosocontainer -Blob “ContosoApp.cscfg” -Context $storageAccount.Context 
    $cscfgToken = New-AzStorageBlobSASToken -Container “contosocontainer” -Blob $cscfgBlob.Name -Permission rwd -StartTime $tokenStartTime -ExpiryTime $tokenEndTime -Context $storageAccount.Context 
    $cscfgUrl = $cscfgBlob.ICloudBlob.Uri.AbsoluteUri + $cscfgToken 
    ```

6. Crie uma rede virtual e uma sub-rede. Este passo é opcional se utilizar uma rede e uma sub-rede existentes. Este exemplo utiliza uma única rede virtual e uma sub-rede para ambas as funções de serviço em nuvem (WebRole e WorkerRole). 

    ```powershell
    $subnet = New-AzVirtualNetworkSubnetConfig -Name "ContosoWebTier1" -AddressPrefix "10.0.0.0/24" -WarningAction SilentlyContinue 
    $virtualNetwork = New-AzVirtualNetwork -Name “ContosoVNet” -Location “East US” -ResourceGroupName “ContosOrg” -AddressPrefix "10.0.0.0/24" -Subnet $subnet 
    ```
 
7. Crie um endereço IP público e desaponija a propriedade da etiqueta DNS do endereço IP público. Os Serviços cloud (suporte alargado) só suportam endereços IP públicos [básicos](https://docs.microsoft.com/azure/virtual-network/public-ip-addresses#basic) do SKU. Os IPs públicos padrão da SKU não funcionam com os Serviços cloud.
Se estiver a utilizar um IP estático, precisa de o referir como um ficheiro IP reservado na configuração de serviço (.cscfg) 

    ```powershell
    $publicIp = New-AzPublicIpAddress -Name “ContosIp” -ResourceGroupName “ContosOrg” -Location “East US” -AllocationMethod Dynamic -IpAddressVersion IPv4 -DomainNameLabel “contosoappdns” -Sku Basic 
    ```

8. Crie um Objeto de Perfil de Rede e associe o endereço IP público à parte frontal do equilibrador de carga. A plataforma Azure cria automaticamente um recurso de balançador de carga SKU 'Classic' na mesma subscrição que o recurso de serviço de cloud. O recurso do balanceador de carga é um recurso apenas de leitura na ARM. Quaisquer atualizações ao recurso são suportadas apenas através dos ficheiros de implementação do serviço na nuvem (.cscfg & .csdef)

    ```powershell
    $publicIP = Get-AzPublicIpAddress -ResourceGroupName ContosOrg -Name ContosIp  
    $feIpConfig = New-AzCloudServiceLoadBalancerFrontendIPConfigurationObject -Name 'ContosoFe' -PublicIPAddressId $publicIP.Id 
    $loadBalancerConfig = New-AzCloudServiceLoadBalancerConfigurationObject -Name 'ContosoLB' -FrontendIPConfiguration $feIpConfig 
    $networkProfile = @{loadBalancerConfiguration = $loadBalancerConfig} 
    ```
 
9. Criar um Key Vault. Este Cofre chave será utilizado para armazenar certificados associados às funções de Cloud Service (suporte alargado). O Cofre-Chave deve estar localizado na mesma região e subscrição que o serviço de nuvem e ter um nome único. Para mais informações consulte [os certificados utilizar com a Azure Cloud Services (suporte alargado)](certificates-and-key-vault.md).

    ```powershell
    New-AzKeyVault -Name "ContosKeyVault” -ResourceGroupName “ContosOrg” -Location “East US” 
    ```

10. Atualize a política de acesso ao Cofre-Chave e conceda permissões de certificado à sua conta de utilizador. 

    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName 'ContosKeyVault' -ResourceGroupName 'ContosOrg' -EnabledForDeployment
    Set-AzKeyVaultAccessPolicy -VaultName 'ContosKeyVault' -ResourceGroupName 'ContosOrg' -UserPrincipalName 'user@domain.com' -PermissionsToCertificates create,get,list,delete 
    ```

    Em alternativa, desajeita a política de acesso via ObjectId (que pode ser obtida através da `Get-AzADUser` execução) 
    
    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName 'ContosKeyVault' -ResourceGroupName 'ContosOrg' -ObjectId 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx' -PermissionsToCertificates create,get,list,delete 
    ```
 

11. Para efeitos deste exemplo, adicionaremos um certificado auto-assinado a um Cofre-Chave. A impressão digital do certificado precisa de ser adicionada no ficheiro Cloud Service Configuration (.cscfg) para implantação em funções de serviço de cloud. 

    ```powershell
    $Policy = New-AzKeyVaultCertificatePolicy -SecretContentType "application/x-pkcs12" -SubjectName "CN=contoso.com" -IssuerName "Self" -ValidityInMonths 6 -ReuseKeyOnRenewal 
    Add-AzKeyVaultCertificate -VaultName "ContosKeyVault" -Name "ContosCert" -CertificatePolicy $Policy 
    ```
 
12. Crie um objeto de perfil de OS na memória. O perfil do OS especifica os certificados associados às funções de serviço na nuvem. Este será o mesmo certificado criado no passo anterior. 

    ```powershell
    $keyVault = Get-AzKeyVault -ResourceGroupName ContosOrg -VaultName ContosKeyVault 
    $certificate = Get-AzKeyVaultCertificate -VaultName ContosKeyVault -Name ContosCert 
    $secretGroup = New-AzCloudServiceVaultSecretGroupObject -Id $keyVault.ResourceId -CertificateUrl $certificate.SecretId 
    $osProfile = @{secret = @($secretGroup)} 
    ```

13. Crie um objeto de perfil de função na memória. O perfil de função define uma função de propriedades específicas como nome, capacidade e nível. Para este exemplo, definimos duas funções: frontendRole e backendRole. As informações de perfil de função devem coincidir com a configuração de função definida no ficheiro de configuração (cscfg) e na definição de serviço (csdef). 

    ```powershell
    $frontendRole = New-AzCloudServiceRoleProfilePropertiesObject -Name 'ContosoFrontend' -SkuName 'Standard_D1_v2' -SkuTier 'Standard' -SkuCapacity 2 
    $backendRole = New-AzCloudServiceRoleProfilePropertiesObject -Name 'ContosoBackend' -SkuName 'Standard_D1_v2' -SkuTier 'Standard' -SkuCapacity 2 
    $roleProfile = @{role = @($frontendRole, $backendRole)} 
    ```

14. (Opcional) Crie um objeto de memória de perfil de extensão que pretende adicionar ao seu serviço na nuvem. Para este exemplo, adicionaremos extensão RDP. 

    ```powershell
    $credential = Get-Credential 
    $expiration = (Get-Date).AddYears(1) 
    $rdpExtension = New-AzCloudServiceRemoteDesktopExtensionObject -Name 'RDPExtension' -Credential $credential -Expiration $expiration -TypeHandlerVersion '1.2.1' 

    $storageAccountKey = Get-AzStorageAccountKey -ResourceGroupName "ContosOrg" -Name "contosostorageaccount"
    $configFile = "<WAD public configuration file path>"
    $wadExtension = New-AzCloudServiceDiagnosticsExtension -Name "WADExtension" -ResourceGroupName "ContosOrg" -CloudServiceName "ContosCS" -StorageAccountName "contosostorageaccount" -StorageAccountKey $storageAccountKey[0].Value -DiagnosticsConfigurationPath $configFile -TypeHandlerVersion "1.5" -AutoUpgradeMinorVersion $true 
    $extensionProfile = @{extension = @($rdpExtension, $wadExtension)} 
    ```
    Note que o configFile deve ter apenas tags PublicConfig e deve conter um espaço de nome como seguinte:
    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
        ...............
    </PublicConfig>
    ```
15. (Opcional) Defina tags como tabela de haxixe PowerShell que pretende adicionar ao seu serviço de nuvem. 

    ```powershell
    $tag=@{"Owner" = "Contoso"} 
    ```

17. Criar implementação de Serviço de Nuvem utilizando objetos de perfil & URLs SAS.

    ```powershell
    $cloudService = New-AzCloudService ` 
    -Name “ContosoCS” ` 
    -ResourceGroupName “ContosOrg” ` 
    -Location “East US” ` 
    -PackageUrl $cspkgUrl ` 
    -ConfigurationUrl $cscfgUrl ` 
    -UpgradeMode 'Auto' ` 
    -RoleProfile $roleProfile ` 
    -NetworkProfile $networkProfile  ` 
    -ExtensionProfile $extensionProfile ` 
    -OSProfile $osProfile `
    -Tag $tag 
    ```

## <a name="next-steps"></a>Passos seguintes 
- Reveja [perguntas frequentes](faq.md) para serviços cloud (suporte alargado).
- Implementar um Serviço de Cloud (suporte alargado) utilizando o [portal Azure](deploy-portal.md), [PowerShell,](deploy-powershell.md) [Modelo](deploy-template.md) ou [Estúdio Visual](deploy-visual-studio.md).
- Visite o [repositório](https://github.com/Azure-Samples/cloud-services-extended-support) de amostras cloud services (suporte alargado)
