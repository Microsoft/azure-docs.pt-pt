---
title: Como implantar o Windows 10 no Azure com direitos de hospedagem multilocatário
description: Saiba como maximizar seus benefícios do Windows Software Assurance para trazer licenças locais para o Azure
services: virtual-machines-windows
documentationcenter: ''
author: xujing
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 1/24/2018
ms.author: xujing
ms.openlocfilehash: 9ff8cc64266375a2d439763b222870843136f67a
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70101492"
---
# <a name="how-to-deploy-windows-10-on-azure-with-multitenant-hosting-rights"></a>Como implantar o Windows 10 no Azure com direitos de hospedagem multilocatário 
Para clientes com o Windows 10 Enterprise E3/E5 por usuário ou para o acesso à área de trabalho virtual do Windows por usuário (licenças de assinatura de usuário ou licenças de assinatura de usuário de complemento), os direitos de hospedagem multilocatário para o Windows 10 permitem que você traga suas licenças do Windows 10 para a nuvem e executar máquinas virtuais do Windows 10 no Azure sem pagar por outra licença. Para obter mais informações, consulte [hospedagem multilocatário para o Windows 10](https://www.microsoft.com/en-us/CloudandHosting/licensing_sca.aspx).

> [!NOTE]
> Este artigo mostra a você como implementar o benefício de licenciamento para imagens do Windows 10 pro desktop no Azure Marketplace.
> - Para as imagens do Windows 7, 8,1, 10 Enterprise (x64) no Azure Marketplace para assinaturas do MSDN, consulte o [cliente do Windows no Azure para cenários de desenvolvimento/teste](client-images.md)
> - Para obter os benefícios de licenciamento do Windows Server, consulte [benefícios de uso híbrido do Azure para imagens do Windows Server](hybrid-use-benefit-licensing.md).
>

## <a name="deploying-windows-10-image-from-azure-marketplace"></a>Implantando a imagem do Windows 10 do Azure Marketplace 
Para implantações de modelo do PowerShell, CLI e Azure Resource Manager, a imagem do Windows 10 pode ser encontrada com o PublisherName, oferta, SKU a seguir.

| OS  |      Nome do Editor      |  Oferta | SKU |
|:----------|:-------------:|:------|:------|
| Windows 10 Pro    | MicrosoftWindowsDesktop | Windows-10  | RS2-Pro   |
| Windows 10 Pro N  | MicrosoftWindowsDesktop | Windows-10  | RS2-ProN  |
| Windows 10 Pro    | MicrosoftWindowsDesktop | Windows-10  | RS3-Pro   |
| Windows 10 Pro N  | MicrosoftWindowsDesktop | Windows-10  | RS3-ProN  |

## <a name="uploading-windows-10-vhd-to-azure"></a>Carregando o VHD do Windows 10 no Azure
Se você estiver carregando um VHD do Windows 10 generalizado, observe que o Windows 10 não tem uma conta de administrador interna habilitada por padrão. Para habilitar a conta de administrador interno, inclua o comando a seguir como parte da extensão de script personalizado.

```powershell
Net user <username> /active:yes
```

O trecho do PowerShell a seguir é marcar todas as contas de administrador como ativas, incluindo o administrador interno. Este exemplo será útil se o nome de usuário do administrador interno for desconhecido.
```powershell
$adminAccount = Get-WmiObject Win32_UserAccount -filter "LocalAccount=True" | ? {$_.SID -Like "S-1-5-21-*-500"}
if($adminAccount.Disabled)
{
    $adminAccount.Disabled = $false
    $adminAccount.Put()
}
```
Para obter mais informações: 
* [Como carregar o VHD no Azure](upload-generalized-managed.md)
* [Como preparar um VHD do Windows para carregar no Azure](prepare-for-upload-vhd-image.md)


## <a name="deploying-windows-10-with-multitenant-hosting-rights"></a>Implantando o Windows 10 com direitos de hospedagem multilocatário
Verifique se você [instalou e configurou as Azure PowerShell mais recentes](/powershell/azure/overview). Depois de preparar o VHD, carregue o VHD em sua conta de armazenamento do Azure usando `Add-AzVhd` o cmdlet da seguinte maneira:

```powershell
Add-AzVhd -ResourceGroupName "myResourceGroup" -LocalFilePath "C:\Path\To\myvhd.vhd" `
    -Destination "https://mystorageaccount.blob.core.windows.net/vhds/myvhd.vhd"
```


**Implantar usando a implantação de modelo do Azure Resource Manager** Em seus modelos do Resource Manager, um parâmetro adicional `licenseType` para pode ser especificado. Você pode ler mais sobre a [criação de modelos de Azure Resource Manager](../../resource-group-authoring-templates.md). Depois de carregar o VHD no Azure, edite o modelo do Resource Manager para incluir o tipo de licença como parte do provedor de computação e implante o modelo normalmente:
```json
"properties": {
    "licenseType": "Windows_Client",
    "hardwareProfile": {
        "vmSize": "[variables('vmSize')]"
    }
```

**Implantar por meio do PowerShell** Ao implantar a VM do Windows Server por meio do PowerShell, você tem um parâmetro `-LicenseType`adicional para. Depois de carregar o VHD no Azure, você cria uma VM usando `New-AzVM` e especifica o tipo de licenciamento da seguinte maneira:
```powershell
New-AzVM -ResourceGroupName "myResourceGroup" -Location "West US" -VM $vm -LicenseType "Windows_Client"
```

## <a name="verify-your-vm-is-utilizing-the-licensing-benefit"></a>Verifique se sua VM está utilizando o benefício de licenciamento
Depois de implantar sua VM por meio do método de implantação do PowerShell ou do Resource Manager, verifique o tipo `Get-AzVM` de licença com o seguinte:
```powershell
Get-AzVM -ResourceGroup "myResourceGroup" -Name "myVM"
```

A saída é semelhante ao seguinte exemplo para o Windows 10 com o tipo de licença correto:

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : Windows_Client
```

Essa saída contrasta com a seguinte VM implantada sem licenciamento do benefício de uso híbrido do Azure, como uma VM implantada diretamente da galeria do Azure:

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              :
```

## <a name="additional-information-about-joining-azure-ad"></a>Informações adicionais sobre como ingressar no Azure AD
>[!NOTE]
>O Azure provisiona todas as VMs do Windows com a conta de administrador interno, que não pode ser usada para ingressar no AAD. Por exemplo, *as configurações > conta > acessar o trabalho ou a escola > + Connect* não funcionará. Você deve criar e fazer logon como uma segunda conta de administrador para ingressar o Azure AD manualmente. Você também pode configurar o Azure AD usando um pacote de provisionamento, use o link para a seção *próximas etapas* para saber mais.
>
>

## <a name="next-steps"></a>Próximos Passos
- Saiba mais sobre como [Configurar o VDA para Windows 10](https://docs.microsoft.com/windows/deployment/vda-subscription-activation)
- Saiba mais sobre [hospedagem multilocatário para Windows 10](https://www.microsoft.com/en-us/CloudandHosting/licensing_sca.aspx)


