---
title: Como implementar o Windows 10 no Azure com direitos de hospedagem multitenant
description: Saiba como maximizar os benefícios da garantia do software do Windows para trazer licenças no local para a Azure com direitos de hospedagem multitenant.
author: xujing
ms.service: virtual-machines-windows
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 1/24/2018
ms.author: xujing
ms.openlocfilehash: 101f2cfe57624502764d145351a6343cfdd2a334
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2020
ms.locfileid: "96572869"
---
# <a name="how-to-deploy-windows-10-on-azure-with-multitenant-hosting-rights"></a>Como implementar o Windows 10 no Azure com direitos de hospedagem multitenant 
Para clientes com Windows 10 Enterprise E3/E5 por utilizador ou Windows Virtual Desktop Access por utilizador (Licenças de subscrição de utilizador ou licenças de subscrição de utilizadores adicionais), os direitos de hospedagem multitenant para o Windows 10 permitem-lhe trazer as suas Licenças do Windows 10 para a nuvem e executar máquinas virtuais do Windows 10 no Azure sem pagar outra licença. Para mais informações, consulte [o Multitenant Hosting para windows 10.](https://www.microsoft.com/en-us/CloudandHosting/licensing_sca.aspx)

> [!NOTE]
> Este artigo mostra-lhe para implementar o benefício de licenciamento para imagens do Windows 10 Pro Desktop no Azure Marketplace.
> - Para imagens do Windows 7, 8.1, 10 Enterprise (x64) no Azure Marketplace para subscrições MSDN, consulte o [cliente do Windows em Azure para cenários de dev/teste](client-images.md)
> - Para obter benefícios de licenciamento do Windows Server, consulte o [Azure Hybrid para obter benefícios para imagens do Windows Server](hybrid-use-benefit-licensing.md).
>

## <a name="deploying-windows-10-image-from-azure-marketplace"></a>Implementação da imagem do Windows 10 a partir do Azure Marketplace 
Para as implementações do modelo PowerShell, CLI e Azure Resource Manager, a imagem do Windows 10 pode ser encontrada com o seguinte nome de editor, oferta, sku.

| SO  |      Nome do Editor      |  Oferta | Sku |
|:----------|:-------------:|:------|:------|
| Windows 10 Pro    | MicrosoftWindowsDesktop | Windows-10  | RS2-Pro   |
| Windows 10 Pro N  | MicrosoftWindowsDesktop | Windows-10  | RS2-ProN  |
| Windows 10 Pro    | MicrosoftWindowsDesktop | Windows-10  | RS3-Pro   |
| Windows 10 Pro N  | MicrosoftWindowsDesktop | Windows-10  | RS3-ProN  |

## <a name="qualify-for-multi-tenant-hosting-rights"></a>Qualificar-se para direitos de acolhimento multi-inquilinos 
Para se qualificar para os direitos de hospedagem de vários inquilinos e para executar imagens do Windows 10 em Azure, os utilizadores devem ter uma das seguintes subscrições: 

-   Microsoft 365 E3/E5 
-   Microsoft 365 F3 
-   Microsoft 365 A3/A5 
-   Empresa E3/E5 do Windows 10
-   Windows 10 Educação A3/A5 
-   Windows VDA E3/E5


## <a name="uploading-windows-10-vhd-to-azure"></a>Upload do Windows 10 VHD para Azure
se estiver a carregar um VHD do Windows 10 generalizado, note que o Windows 10 não tem uma conta de administrador incorporada ativada por padrão. Para ativar a conta de administrador incorporada, inclua o seguinte comando como parte da extensão do Script Personalizado.

```powershell
Net user <username> /active:yes
```

O seguinte corte powerShell é marcar todas as contas do administrador como ativas, incluindo o administrador incorporado. Este exemplo é útil se o nome de utilizador do administrador incorporado for desconhecido.
```powershell
$adminAccount = Get-WmiObject Win32_UserAccount -filter "LocalAccount=True" | ? {$_.SID -Like "S-1-5-21-*-500"}
if($adminAccount.Disabled)
{
    $adminAccount.Disabled = $false
    $adminAccount.Put()
}
```
Para obter mais informações: 
* [Como carregar VHD para Azure](upload-generalized-managed.md)
* [Como preparar um VHD do Windows para fazer o upload para o Azure](prepare-for-upload-vhd-image.md)


## <a name="deploying-windows-10-with-multitenant-hosting-rights"></a>Implantação do Windows 10 com direitos de hospedagem multitenant
Certifique-se de que [instalou e configura o mais recente Azure PowerShell](/powershell/azure/). Depois de ter preparado o seu VHD, faça o upload do VHD para a sua conta de Armazenamento Azure utilizando o `Add-AzVhd` cmdlet da seguinte forma:

```powershell
Add-AzVhd -ResourceGroupName "myResourceGroup" -LocalFilePath "C:\Path\To\myvhd.vhd" `
    -Destination "https://mystorageaccount.blob.core.windows.net/vhds/myvhd.vhd"
```


**Implementar usando a implementação do modelo do gestor de recursos Azure** Dentro dos seus modelos de Gestor de Recursos, pode ser especificado um parâmetro `licenseType` adicional. Pode ler mais sobre [a autoria de modelos do Gestor de Recursos Azure.](../../azure-resource-manager/templates/template-syntax.md) Assim que tiver o seu VHD carregado para a Azure, edite o modelo de Gestor de Recursos para incluir o tipo de licença como parte do fornecedor de cálculo e implemente o seu modelo normalmente:
```json
"properties": {
    "licenseType": "Windows_Client",
    "hardwareProfile": {
        "vmSize": "[variables('vmSize')]"
    }
```

**Implementar via PowerShell** Ao implementar o seu VM do Servidor do Windows via PowerShell, tem um parâmetro adicional para `-LicenseType` . Uma vez que tenha o seu VHD carregado para Azure, cria um VM usando `New-AzVM` e especificar o tipo de licenciamento da seguinte forma:
```powershell
New-AzVM -ResourceGroupName "myResourceGroup" -Location "West US" -VM $vm -LicenseType "Windows_Client"
```

## <a name="verify-your-vm-is-utilizing-the-licensing-benefit"></a>Verifique se o seu VM está a utilizar o benefício do licenciamento
Depois de ter implantado o seu VM através do método de implementação powerShell ou Gestor de Recursos, verifique o tipo de licença com o `Get-AzVM` seguinte:
```powershell
Get-AzVM -ResourceGroup "myResourceGroup" -Name "myVM"
```

A saída é semelhante ao seguinte exemplo para o Windows 10 com o tipo de licença correto:

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : Windows_Client
```

Esta saída contrasta com o seguinte VM implantado sem o licenciamento Azure Hybrid Use Benefit, tal como um VM implantado diretamente da Galeria Azure:

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              :
```

## <a name="additional-information-about-joining-azure-ad"></a>Informações adicionais sobre a adesão do Azure AD
>[!NOTE]
>O Azure prevê todos os VMs do Windows com conta de administrador incorporada, que não podem ser usados para aderir à AAD. Por exemplo, *As Definições > Conta > Trabalho de Acesso ou > Escolares não* funcionarão. Tem de criar e iniciar sessão como segunda conta de administrador para se juntar manualmente ao Azure AD. Também pode configurar a Azure AD usando um pacote de provisionamento, use o link na secção *Etapas Seguintes* para saber mais.
>
>

## <a name="next-steps"></a>Passos Seguintes
- Saiba mais sobre [configurar vDA para o Windows 10](/windows/deployment/vda-subscription-activation)
- Saiba mais sobre [o Multitenant Hosting para o Windows 10](https://www.microsoft.com/en-us/CloudandHosting/licensing_sca.aspx)
