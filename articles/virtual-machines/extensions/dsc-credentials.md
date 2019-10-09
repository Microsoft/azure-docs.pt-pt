---
title: Passe as credenciais para o Azure usando a configuração de estado desejado
description: Saiba como transmitir credenciais com segurança para máquinas virtuais do Azure usando a DSC (configuração de estado desejado) do PowerShell.
services: virtual-machines-windows
documentationcenter: ''
author: bobbytreed
manager: carmonm
editor: ''
tags: azure-resource-manager
keywords: dsc
ms.assetid: ea76b7e8-b576-445a-8107-88ea2f3876b9
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: na
ms.date: 05/02/2018
ms.author: robreed
ms.openlocfilehash: b0625e3ac14e28cb4d43313bf1d9bc8b7afa08b1
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2019
ms.locfileid: "72176700"
---
# <a name="pass-credentials-to-the-azure-dscextension-handler"></a>Passar credenciais para o manipulador de pssar do Azure

Este artigo aborda a extensão de configuração de estado desejado (DSC) para o Azure. Para obter uma visão geral do manipulador de extensão de DSC, consulte [introdução ao manipulador de extensão de configuração de estado desejado do Azure](dsc-overview.md).

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="pass-in-credentials"></a>Credenciais de passagem

Como parte do processo de configuração, talvez seja necessário configurar contas de usuário, acessar serviços ou instalar um programa em um contexto de usuário. Para fazer essas coisas, você precisa fornecer credenciais.

Você pode usar a DSC para definir configurações com parâmetros. Em uma configuração com parâmetros, as credenciais são passadas para a configuração e armazenadas com segurança em arquivos. mof. O manipulador de extensão do Azure simplifica o gerenciamento de credenciais, fornecendo gerenciamento automático de certificados.

O script de configuração DSC a seguir cria uma conta de usuário local com a senha especificada:

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

É importante incluir o **nó localhost** como parte da configuração. O manipulador de extensão procura especificamente a instrução do **nó localhost** . Se essa instrução estiver ausente, as etapas a seguir não funcionarão. Também é importante incluir o conversão **[PsCredential]** . Esse tipo específico dispara a extensão para criptografar a credencial.

Para publicar esse script no armazenamento de BLOBs do Azure:

`Publish-AzVMDscConfiguration -ConfigurationPath .\user_configuration.ps1`

Para definir a extensão de DSC do Azure e fornecer a credencial:

```powershell
$configurationName = 'Main'
$configurationArguments = @{ Credential = Get-Credential }
$configurationArchive = 'user_configuration.ps1.zip'
$vm = Get-AzVM -Name 'example-1'

$vm = Set-AzVMDscExtension -VMName $vm -ConfigurationArchive $configurationArchive -ConfigurationName $configurationName -ConfigurationArgument @configurationArguments

$vm | Update-AzVM
```

## <a name="how-a-credential-is-secured"></a>Como uma credencial é protegida

A execução desse código solicita uma credencial. Depois que a credencial é fornecida, ela é armazenada brevemente na memória. Quando a credencial é publicada usando o cmdlet **set-AzVMDscExtension** , a credencial é transmitida por HTTPS para a VM. Na VM, o Azure armazena a credencial criptografada no disco usando o certificado de VM local. A credencial é descriptografada brevemente na memória e, em seguida, é criptografada novamente para passá-la para o DSC.

Esse processo é diferente de [usar configurações seguras sem o manipulador de extensão](/powershell/scripting/dsc/pull-server/securemof). O ambiente do Azure oferece uma maneira de transmitir dados de configuração com segurança por meio de certificados. Ao usar o manipulador de extensão de DSC, você não precisa fornecer **$CertificatePath** ou uma entrada **$CertificateID**/  **$Thumbprint** em **ConfigurationData**.

## <a name="next-steps"></a>Passos seguintes

- Obtenha uma [introdução ao manipulador de extensões de DSC do Azure](dsc-overview.md).
- Examine o [modelo de Azure Resource Manager para a extensão de DSC](dsc-template.md).
- Para obter mais informações sobre o DSC do PowerShell, acesse o [centro de documentação do PowerShell](/powershell/scripting/dsc/overview/overview).
- Para obter mais funcionalidade que você pode gerenciar usando a DSC do PowerShell e para obter mais recursos de DSC, procure a [Galeria do PowerShell](https://www.powershellgallery.com/packages?q=DscResource&x=0&y=0).