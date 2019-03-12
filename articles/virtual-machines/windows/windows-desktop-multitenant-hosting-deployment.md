---
title: Como implementar o Windows 10 no Azure com direitos de alojamento multi-inquilino
description: Saiba como maximizar os benefícios do Windows Software Assurance para colocar licenças no local para o Azure
services: virtual-machines-windows
documentationcenter: ''
author: xujing
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 1/24/2018
ms.author: xujing
ms.openlocfilehash: 7f43528c55cd22c2649ca0f1208da6f41695b98e
ms.sourcegitcommit: dd1a9f38c69954f15ff5c166e456fda37ae1cdf2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57569982"
---
# <a name="how-to-deploy-windows-10-on-azure-with-multitenant-hosting-rights"></a>Como implementar o Windows 10 no Azure com direitos de alojamento multi-inquilino 
Para os clientes com Windows 10 Enterprise E3/E5 por usuário ou o Windows Virtual Desktop Access por utilizador (licenças de subscrição de utilizador ou licenças de subscrição de utilizador do suplemento), o multi-inquilino de alojamento direitos para Windows 10 permite-lhe reunir as suas licenças do Windows 10 para a cloud e executar máquinas virtuais do Windows 10 no Azure sem ter de adquirir outra licença. Para obter mais informações, consulte [alojamento do multi-inquilino para o Windows 10](https://www.microsoft.com/en-us/CloudandHosting/licensing_sca.aspx).

> [!NOTE]
> Este artigo mostra-lhe implementar o benefício de licenciamento para imagens de Desktop do Windows 10 Pro no Azure Marketplace.
> - Para Windows 7, 8.1, 10 imagens de Enterprise (x64) no Azure Marketplace para as assinaturas do MSDN, consulte [cliente Windows no Azure para cenários de desenvolvimento/teste](client-images.md)
> - Para obter os benefícios de licenciamento do servidor Windows, consulte [híbrido do Azure utilizar os benefícios para imagens do Windows Server](hybrid-use-benefit-licensing.md).
>

## <a name="deploying-windows-10-image-from-azure-marketplace"></a>Implementar a imagem do Windows 10 no Azure Marketplace 
Para implementações de modelo do Powershell, CLI e do Azure Resource Manager, a imagem do Windows 10 pode ser encontrada com a seguinte publishername, oferta, sku.

| SO  |      Nome do Editor      |  Oferta | Sku |
|:----------|:-------------:|:------|:------|
| Windows 10 Pro    | MicrosoftWindowsDesktop | Windows-10  | RS2-Pro   |
| Windows 10 Pro N  | MicrosoftWindowsDesktop | Windows-10  | RS2-ProN  |
| Windows 10 Pro    | MicrosoftWindowsDesktop | Windows-10  | RS3-Pro   |
| Windows 10 Pro N  | MicrosoftWindowsDesktop | Windows-10  | RS3-ProN  |

## <a name="uploading-windows-10-vhd-to-azure"></a>A carregar o Windows 10 VHD para o Azure
Se estiver a carregar um VHD generalizado de 10 Windows, tenha em atenção de que Windows 10 não tem uma conta de administrador incorporada ativada por predefinição. Para ativar a conta de administrador incorporada, incluem o seguinte comando como parte da extensão de Script personalizado.

```powershell
Net user <username> /active:yes
```

O fragmento seguinte do powershell é marcar todas as contas de administrador como ativa, incluindo o administrador incorporado. Neste exemplo é útil se o nome de utilizador de administrador interno é desconhecido.
```powershell
$adminAccount = Get-WmiObject Win32_UserAccount -filter "LocalAccount=True" | ? {$_.SID -Like "S-1-5-21-*-500"}
if($adminAccount.Disabled)
{
    $adminAccount.Disabled = $false
    $adminAccount.Put()
}
```
Para mais informações: 
* [Como carregar o VHD para o Azure](upload-generalized-managed.md)
* [Como preparar um VHD do Windows para carregar para o Azure](prepare-for-upload-vhd-image.md)


## <a name="deploying-windows-10-with-multitenant-hosting-rights"></a>Implantando o Windows 10 com direitos de alojamento multi-inquilinos
Certifique-se de que tem [instalado e configurado o Azure PowerShell mais recente](/powershell/azure/overview). Depois de preparar o VHD, carregar o VHD para a sua conta de armazenamento do Azure com o `Add-AzVhd` cmdlet da seguinte forma:

```powershell
Add-AzVhd -ResourceGroupName "myResourceGroup" -LocalFilePath "C:\Path\To\myvhd.vhd" `
    -Destination "https://mystorageaccount.blob.core.windows.net/vhds/myvhd.vhd"
```


**Implementar com a implementação de modelo do Azure Resource Manager** dentro de modelos do Resource Manager, um parâmetro adicional para `licenseType` pode ser especificado. Pode ler mais sobre [criar modelos do Azure Resource Manager](../../resource-group-authoring-templates.md). Assim que tiver o seu VHD carregado para o Azure, edite modelo do Resource Manager para incluir o tipo de licença como parte do fornecedor de computação e implementar o modelo como normal:
```json
"properties": {
    "licenseType": "Windows_Client",
    "hardwareProfile": {
        "vmSize": "[variables('vmSize')]"
    }
```

**Implementar através do PowerShell** quando implementar a sua VM do Windows Server através do PowerShell, tem um parâmetro adicional para `-LicenseType`. Assim que tiver o seu VHD carregado para o Azure, criar uma VM com `New-AzVM` e especifique o tipo de licenciamento da seguinte forma:
```powershell
New-AzVM -ResourceGroupName "myResourceGroup" -Location "West US" -VM $vm -LicenseType "Windows_Client"
```

## <a name="verify-your-vm-is-utilizing-the-licensing-benefit"></a>Certifique-se de que a VM estiver a utilizar o benefício de licenciamento
Assim que tiver implementado a sua VM através de qualquer um dos métodos de implementação do PowerShell ou do Resource Manager, verifique se o tipo de licença com `Get-AzVM` da seguinte forma:
```powershell
Get-AzVM -ResourceGroup "myResourceGroup" -Name "myVM"
```

O resultado é semelhante ao seguinte exemplo para o Windows 10 com o tipo de licença correto:

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : Windows_Client
```

Esta saída contrasta com a VM seguinte implementada sem o Azure Hybrid Use Benefit licenciamento, por exemplo, uma VM implementada diretamente a partir da galeria do Azure:

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              :
```

## <a name="additional-information-about-joining-azure-ad"></a>Informações adicionais sobre a associação do Azure AD
>[!NOTE]
>O Azure Aprovisiona todas as VMs do Windows com a conta de administrador incorporado, que não pode ser utilizada para associar o AAD. Por exemplo, *definições > conta > acesso de trabalho ou escola > + Connect* não funcionará. Tem de criar e iniciar sessão como uma segunda conta de administrador para aderir ao Azure AD manualmente. Também pode configurar o Azure AD através de um pacote de aprovisionamento, utilize a ligação é o *passos seguintes* secção para saber mais.
>
>

## <a name="next-steps"></a>Próximos Passos
- Saiba mais sobre [configurar VDA para Windows 10](https://docs.microsoft.com/windows/deployment/vda-subscription-activation)
- Saiba mais sobre [de alojamento do multi-inquilino para o Windows 10](https://www.microsoft.com/en-us/CloudandHosting/licensing_sca.aspx)


