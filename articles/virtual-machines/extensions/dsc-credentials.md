---
title: Passar credenciais para o Azure com a Desired State Configuration do
description: Aprenda a ser transmitido em segurança as credenciais para máquinas virtuais do Azure com o PowerShell Desired State Configuration (DSC).
services: virtual-machines-windows
documentationcenter: ''
author: bobbytreed
manager: carmonm
editor: ''
tags: azure-resource-manager
keywords: dsc
ms.assetid: ea76b7e8-b576-445a-8107-88ea2f3876b9
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: na
ms.date: 05/02/2018
ms.author: robreed
ms.openlocfilehash: 723d0cfe6e292c4b8013de4da55779a6c675d610
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64705939"
---
# <a name="pass-credentials-to-the-azure-dscextension-handler"></a>Passar credenciais para o manipulador de DSCExtension do Azure

Este artigo aborda a extensão do Desired State Configuration (DSC) para o Azure. Para obter uma descrição geral do manipulador de extensão DSC, veja [introdução ao manipulador de extensão Azure Desired State Configuration](dsc-overview.md).

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="pass-in-credentials"></a>Passar credenciais

Como parte do processo de configuração, poderá precisar para configurar contas de utilizador, aceder aos serviços, ou instale um programa num contexto de utilizador. Para fazer essas coisas, terá de fornecer credenciais.

Pode utilizar o DSC para definir configurações parametrizadas. Numa configuração de parâmetros, as credenciais são passadas para a configuração e armazenadas em segurança em arquivos. MOF. O manipulador de extensão do Azure simplifica a gestão de credenciais ao fornecer uma gestão automática de certificados.

O seguinte script de configuração de DSC cria uma conta de utilizador local com a palavra-passe especificada:

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

É importante incluir **localhost do nó** como parte da configuração. O manipulador de extensão procura especificamente para o **localhost do nó** instrução. Se essa instrução está em falta, os seguintes passos não funcionam. Também é importante incluir a typecast **[PsCredential]** . Este tipo específico aciona a extensão para encriptar a credencial.

Para publicar este script para o armazenamento de Blobs do Azure:

`Publish-AzVMDscConfiguration -ConfigurationPath .\user_configuration.ps1`

Para definir a extensão DSC do Azure e forneça a credencial:

```powershell
$configurationName = 'Main'
$configurationArguments = @{ Credential = Get-Credential }
$configurationArchive = 'user_configuration.ps1.zip'
$vm = Get-AzVM -Name 'example-1'

$vm = Set-AzVMDscExtension -VMName $vm -ConfigurationArchive $configurationArchive -ConfigurationName $configurationName -ConfigurationArgument @configurationArguments

$vm | Update-AzVM
```

## <a name="how-a-credential-is-secured"></a>Como uma credencial está protegida

Execução desse código solicita uma credencial. Depois da credencial for fornecida, brevemente é armazenado na memória. Quando a credencial é publicada utilizando o **Set-AzVMDscExtension** cmdlet, a credencial é transmitido via HTTPS para a VM. Na VM, o Azure armazena a credencial encriptada no disco com o certificado VM local. A credencial é descriptografada brevemente na memória e, em seguida, ele será novamente encriptado passá-lo para DSC.

Este processo é diferente [utilizar configurações de seguras sem o manipulador de extensão](/powershell/dsc/securemof). O ambiente do Azure dá-lhe uma forma de transmitir dados de configuração de forma segura através de certificados. Quando utiliza o manipulador de extensão DSC, não precisa fornecer **$CertificatePath** ou uma **$CertificateID**/  **$Thumbprint** entrada no **ConfigurationData**.

## <a name="next-steps"></a>Passos Seguintes

- Obter um [introdução ao manipulador de extensão DSC do Azure](dsc-overview.md).
- Examine os [modelo Azure Resource Manager para a extensão DSC](dsc-template.md).
- Para obter mais informações sobre o DSC de PowerShell, vá para o [Centro de documentação do PowerShell](/powershell/dsc/overview).
- Para mais funcionalidades que pode gerir utilizando o PowerShell DSC e para mais recursos de DSC, procurar as [galeria do PowerShell](https://www.powershellgallery.com/packages?q=DscResource&x=0&y=0).