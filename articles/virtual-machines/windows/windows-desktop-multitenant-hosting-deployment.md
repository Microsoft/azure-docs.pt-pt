---
title: Como implementar o Windows 10 em Azure com direitos de hospedagem multiarrendatário
description: Saiba como maximizar os benefícios da Garantia de Software do Windows para levar licenças no local para o Azure
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "70101492"
---
# <a name="how-to-deploy-windows-10-on-azure-with-multitenant-hosting-rights"></a>Como implementar o Windows 10 em Azure com direitos de hospedagem multiarrendatário 
Para clientes com Windows 10 Enterprise E3/E5 por utilizador ou Windows Virtual Desktop Access por utilizador (Licenças de subscrição de utilizador ou licenças de subscrição de utilizadores adicionais), os direitos de hospedagem multiarrendatários para o Windows 10 permitem-lhe trazer as suas Licenças Windows 10 para a nuvem e executar máquinas virtuais do Windows 10 no Azure sem pagar outra licença. Para mais informações, consulte [o Multitenant Hosting para o Windows 10](https://www.microsoft.com/en-us/CloudandHosting/licensing_sca.aspx).

> [!NOTE]
> Este artigo mostra-lhe a implementação do benefício de licenciamento para imagens do Windows 10 Pro Desktop no Azure Marketplace.
> - Para imagens do Windows 7, 8.1, 10 Enterprise (x64) no Azure Marketplace para subscrições msdN, consulte o [cliente windows em Azure para cenários de dev/teste](client-images.md)
> - Para obter benefícios de licenciamento do Windows Server, consulte os benefícios de [utilização do Azure Hybrid para imagens do Windows Server](hybrid-use-benefit-licensing.md).
>

## <a name="deploying-windows-10-image-from-azure-marketplace"></a>Implementação da imagem do Windows 10 do Azure Marketplace 
Para implementações de modelos Powershell, CLI e Azure Resource Manager, a imagem do Windows 10 pode ser encontrada com o seguinte nome de editor, oferta, sku.

| SO  |      Nome do Editor      |  Oferta | Sku |
|:----------|:-------------:|:------|:------|
| Windows 10 Pro    | MicrosoftWindowsDesktop | Windows-10  | RS2-Pro   |
| Windows 10 Pro N  | MicrosoftWindowsDesktop | Windows-10  | RS2-ProN  |
| Windows 10 Pro    | MicrosoftWindowsDesktop | Windows-10  | RS3-Pro   |
| Windows 10 Pro N  | MicrosoftWindowsDesktop | Windows-10  | RS3-ProN  |

## <a name="uploading-windows-10-vhd-to-azure"></a>Upload do Windows 10 VHD para Azure
se estiver a carregar um VHD do Windows 10 generalizado, por favor note que o Windows 10 não possui uma conta de administrador incorporada ativada por padrão. Para ativar a conta de administrador incorporado, inclua o seguinte comando como parte da extensão do Script Personalizado.

```powershell
Net user <username> /active:yes
```

O seguinte corte de powershell é marcar todas as contas do administrador como ativas, incluindo o administrador incorporado. Este exemplo é útil se o nome de utilizador do administrador incorporado for desconhecido.
```powershell
$adminAccount = Get-WmiObject Win32_UserAccount -filter "LocalAccount=True" | ? {$_.SID -Like "S-1-5-21-*-500"}
if($adminAccount.Disabled)
{
    $adminAccount.Disabled = $false
    $adminAccount.Put()
}
```
Para obter mais informações: 
* [Como enviar VHD para Azure](upload-generalized-managed.md)
* [Como preparar um VHD windows para fazer o upload para o Azure](prepare-for-upload-vhd-image.md)


## <a name="deploying-windows-10-with-multitenant-hosting-rights"></a>Implementação do Windows 10 com direitos de hospedagem multiarrendatários
Certifique-se de que [instalou e configurou o mais recente Azure PowerShell](/powershell/azure/overview). Depois de ter preparado o seu VHD, carregue o VHD para a sua conta de Armazenamento Azure utilizando o `Add-AzVhd` cmdlet da seguinte forma:

```powershell
Add-AzVhd -ResourceGroupName "myResourceGroup" -LocalFilePath "C:\Path\To\myvhd.vhd" `
    -Destination "https://mystorageaccount.blob.core.windows.net/vhds/myvhd.vhd"
```


**Implementar usando a implementação** do modelo de gestor de recursos azure Dentro dos seus modelos de Gestor `licenseType` de Recursos, pode especificar-se um parâmetro adicional. Pode ler mais sobre a autoria de modelos do Gestor de [Recursos Azure](../../resource-group-authoring-templates.md). Assim que tiver o seu VHD carregado para o Azure, edite o modelo de Gestor de Recursos para incluir o tipo de licença como parte do provedor de computação e implementar o seu modelo normalmente:
```json
"properties": {
    "licenseType": "Windows_Client",
    "hardwareProfile": {
        "vmSize": "[variables('vmSize')]"
    }
```

**Implantação via PowerShell** Ao implementar o vM do Windows Server via PowerShell, tem um parâmetro adicional para `-LicenseType`. Assim que tiver o seu VHD enviado para o `New-AzVM` Azure, cria um VM utilizando e especifica o tipo de licenciamento da seguinte forma:
```powershell
New-AzVM -ResourceGroupName "myResourceGroup" -Location "West US" -VM $vm -LicenseType "Windows_Client"
```

## <a name="verify-your-vm-is-utilizing-the-licensing-benefit"></a>Verifique se o seu VM está a utilizar o benefício de licenciamento
Depois de ter implantado o seu VM através do método de implementação powerShell ou do Resource Manager, verifique o tipo de licença com `Get-AzVM` o seguinte:
```powershell
Get-AzVM -ResourceGroup "myResourceGroup" -Name "myVM"
```

A saída é semelhante ao seguinte exemplo para o Windows 10 com o tipo de licença correto:

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : Windows_Client
```

Esta saída contrasta com o seguinte VM implantado sem licenciamento azure hybrid use benefit, como um VM implantado diretamente da Galeria Azure:

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              :
```

## <a name="additional-information-about-joining-azure-ad"></a>Informações adicionais sobre a adesão à Azure AD
>[!NOTE]
>O Azure disponibiliza todos os VMs do Windows com conta de administrador incorporado, que não pode ser usado para aderir à AAD. Por exemplo, definições > conta > Trabalho de *Acesso ou > Escolar +Connect* não funcionará. Tem de criar e iniciar sessão como uma segunda conta de administrador para se juntar manualmente à Azure AD. Também pode configurar o Azure AD utilizando um pacote de provisionamento, utilizar o link é a secção *Next Steps* para saber mais.
>
>

## <a name="next-steps"></a>Passos Seguintes
- Saiba mais sobre [configurar o VDA para o Windows 10](https://docs.microsoft.com/windows/deployment/vda-subscription-activation)
- Saiba mais sobre [o Alojamento Multitenant para o Windows 10](https://www.microsoft.com/en-us/CloudandHosting/licensing_sca.aspx)


