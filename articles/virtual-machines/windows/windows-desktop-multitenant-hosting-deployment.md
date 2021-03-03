---
title: Como implementar o Windows 10 no Azure com direitos de hospedagem multitenant
description: Saiba como maximizar os benefícios da garantia do software do Windows para trazer licenças no local para a Azure com direitos de hospedagem multitenant.
author: mimckitt
ms.service: virtual-machines-windows
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 2/2/2021
ms.author: mimckitt
ms.custom: rybaker, chmimckitt
ms.openlocfilehash: 6e6f6ced1cdba429abd914354a5eba861ab127ec
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101670048"
---
# <a name="how-to-deploy-windows-10-on-azure-with-multitenant-hosting-rights"></a>Como implementar o Windows 10 no Azure com direitos de hospedagem multitenant 
Para clientes com Windows 10 Enterprise E3/E5 por utilizador ou Windows Virtual Desktop Access por utilizador (Licenças de subscrição de utilizador ou licenças de subscrição de utilizadores adicionais), os direitos de hospedagem multitenant para o Windows 10 permitem-lhe trazer as suas Licenças do Windows 10 para a nuvem e executar máquinas virtuais do Windows 10 no Azure sem pagar outra licença. Os Direitos de Hospedagem Multitenant só estão disponíveis para o Windows 10 (versão 1703 ou posterior).

Para obter mais informações, consulte [o Multitenant Hosting para windows 10](https://www.microsoft.com/en-us/CloudandHosting/licensing_sca.aspx).

> [!NOTE]
> - Para utilizar imagens do Windows 7, 8.1 e 10 para desenvolvimento ou teste, consulte [o cliente do Windows em Azure para cenários dev/teste](client-images.md)
> - Para obter benefícios de licenciamento do Windows Server, consulte o [Azure Hybrid para obter benefícios para imagens do Windows Server](hybrid-use-benefit-licensing.md).

## <a name="subscription-licenses-that-qualify-for-multitenant-hosting-rights"></a>Licenças de subscrição que se qualificam para direitos de hospedagem multitenant

Utilizando o [centro de administração](/microsoft-365/admin/admin-overview/about-the-admin-center?preserve-view=true&view=o365-worldwide)da Microsoft, pode confirmar se foi atribuída a um utilizador uma licença suportada pelo Windows 10.

> [!IMPORTANT]
> Os utilizadores devem ter uma das licenças de subscrição abaixo para utilizar imagens do Windows 10 no Azure. Se não tiver uma destas licenças de subscrição, estas podem ser adquiridas através do seu [Parceiro de Serviço cloud](https://azure.microsoft.com/overview/choosing-a-cloud-service-provider/) ou diretamente através da [Microsoft.](https://www.microsoft.com/microsoft-365?rtc=1)

**Licenças de subscrição elegíveis:**

-   Microsoft 365 E3/E5 
-   Microsoft 365 F3 
-   Microsoft 365 A3/A5 
-   Empresa E3/E5 do Windows 10
-   Windows 10 Educação A3/A5 
-   Windows VDA E3/E5


## <a name="deploying-windows-10-image-from-azure-marketplace"></a>Implementação da imagem do Windows 10 a partir do Azure Marketplace 
Para as implementações do modelo PowerShell, CLI e Azure Resource Manager, as imagens do Windows 10 podem ser encontradas utilizando o `PublisherName: MicrosoftWindowsDesktop` e `Offer: Windows-10` . . A versão do Windows 10 Creators Update (1809) ou mais tarde é suportada por Direitos de Hospedagem Multitenant. 

```powershell
Get-AzVmImageSku -Location '$location' -PublisherName 'MicrosoftWindowsDesktop' -Offer 'Windows-10'

Skus                        Offer      PublisherName           Location 
----                        -----      -------------           -------- 
rs4-pro                     Windows-10 MicrosoftWindowsDesktop eastus   
rs4-pron                    Windows-10 MicrosoftWindowsDesktop eastus   
rs5-enterprise              Windows-10 MicrosoftWindowsDesktop eastus   
rs5-enterprisen             Windows-10 MicrosoftWindowsDesktop eastus   
rs5-pro                     Windows-10 MicrosoftWindowsDesktop eastus   
rs5-pron                    Windows-10 MicrosoftWindowsDesktop eastus  
```

Para obter mais informações sobre as imagens disponíveis, [consulte Localizar e utilizar imagens VM do Azure Marketplace com Azure PowerShell](./cli-ps-findimage.md)

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
O Azure prevê todos os VMs do Windows com conta de administrador incorporada, que não podem ser usados para aderir à AAD. Por exemplo, *As Definições > Conta > Trabalho de Acesso ou > Escolares não* funcionarão. Tem de criar e iniciar sessão como segunda conta de administrador para se juntar manualmente ao Azure AD. Também pode configurar a Azure AD usando um pacote de provisionamento, use o link na secção *Etapas Seguintes* para saber mais.

## <a name="next-steps"></a>Passos Seguintes
- Saiba mais sobre [configurar vDA para o Windows 10](/windows/deployment/vda-subscription-activation)
- Saiba mais sobre [o Multitenant Hosting para o Windows 10](https://www.microsoft.com/en-us/CloudandHosting/licensing_sca.aspx)