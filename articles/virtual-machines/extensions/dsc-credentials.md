---
title: Passar credenciais para Azure usando a configuração do Estado desejado
description: Aprenda a passar credenciais de forma segura para máquinas virtuais Azure utilizando a Configuração do Estado Desejado pela PowerShell (DSC).
services: virtual-machines-windows
documentationcenter: ''
author: bobbytreed
manager: carmonm
editor: ''
tags: azure-resource-manager
keywords: dSC
ms.assetid: ea76b7e8-b576-445a-8107-88ea2f3876b9
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: na
ms.date: 05/02/2018
ms.author: robreed
ms.openlocfilehash: f7edbd0fd8791829a2d9ffaa4e7c0ee0e561cc5d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73748965"
---
# <a name="pass-credentials-to-the-azure-dscextension-handler"></a>Passar credenciais para o manipulador de extensão Azure DSCExtension

Este artigo cobre a extensão de Configuração do Estado Desejado (DSC) para o Azure. Para uma visão geral do manipulador de extensão DSC, consulte introdução ao manipulador de [extensão de configuração de configuração do Estado Pretendido .](dsc-overview.md)

 

## <a name="pass-in-credentials"></a>Passe em credenciais

Como parte do processo de configuração, poderá ser necessário configurar contas de utilizador, serviços de acesso ou instalar um programa num contexto de utilizador. Para fazer estas coisas, precisa fornecer credenciais.

Pode utilizar o DSC para configurar configurações parametrizadas. Numa configuração parametrizada, as credenciais são passadas para a configuração e armazenadas de forma segura em ficheiros .mof. O manipulador de extensão Azure simplifica a gestão da credencial através do fornecimento automático de certificados.

O seguinte script de configuração DSC cria uma conta de utilizador local com a senha especificada:

```powershell
configuration Main
{
    param(
        [Parameter(Mandatory=$true)]
        [ValidateNotNullorEmpty()]
        [PSCredential]
        $Credential
    )
    Node localhost {
        User LocalUserAccount
        {
            Username = $Credential.UserName
            Password = $Credential
            Disabled = $false
            Ensure = "Present"
            FullName = "Local User Account"
            Description = "Local User Account"
            PasswordNeverExpires = $true
        }
    }
}
```

É importante incluir o **anfitrião local** do nó como parte da configuração. O manipulador de extensão procura especificamente a declaração de **anfitrião local** do nó. Se esta declaração faltar, os seguintes passos não funcionam. Também é importante incluir a typecast **[PsCredential]**. Este tipo específico aciona a extensão para encriptar a credencial.

Para publicar este guião no armazenamento da Blob Azure:

`Publish-AzVMDscConfiguration -ConfigurationPath .\user_configuration.ps1`

Para definir a extensão Azure DSC e fornecer a credencial:

```powershell
$configurationName = 'Main'
$configurationArguments = @{ Credential = Get-Credential }
$configurationArchive = 'user_configuration.ps1.zip'
$vm = Get-AzVM -Name 'example-1'

$vm = Set-AzVMDscExtension -VMName $vm -ConfigurationArchive $configurationArchive -ConfigurationName $configurationName -ConfigurationArgument @configurationArguments

$vm | Update-AzVM
```

## <a name="how-a-credential-is-secured"></a>Como uma credencial é segura

Executar este código pede uma credencial. Após a credencial ser fornecida, é brevemente armazenada na memória. Quando a credencial é publicada utilizando o cmdlet **set-AzVMDscExtension,** a credencial é transmitida através de HTTPS para o VM. No VM, o Azure armazena a credencial encriptada no disco utilizando o certificado VM local. A credencial é brevemente desencriptada na memória, e depois é reencriptada para passá-la para dSC.

Este processo é diferente de [utilizar configurações seguras sem o manipulador de extensões](/powershell/scripting/dsc/pull-server/securemof). O ambiente Azure dá-lhe uma forma de transmitir dados de configuração de forma segura através de certificados. Quando utiliza o manipulador de extensões DSC, não necessita de fornecer **$CertificatePath** ou uma **entrada**/ **$Thumbprint** $CertificateID no **ConfigurationData**.

## <a name="next-steps"></a>Passos seguintes

- Obtenha uma introdução ao manipulador de [extensão Azure DSC](dsc-overview.md).
- Examine o modelo do Gestor de [Recursos Azure para a extensão DSC](dsc-template.md).
- Para mais informações sobre o PowerShell DSC, vá ao centro de [documentação PowerShell](/powershell/scripting/dsc/overview/overview).
- Para obter mais funcionalidades que possa gerir utilizando o PowerShell DSC e para obter mais recursos DSC, navegue na [galeria PowerShell.](https://www.powershellgallery.com/packages?q=DscResource&x=0&y=0)