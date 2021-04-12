---
title: Passe credenciais para Azure usando a configuração do estado desejado
description: Saiba como passar com segurança credenciais para máquinas virtuais Azure utilizando a Configuração de Estado Desejada powerShell (DSC).
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: bobbytreed
ms.author: robreed
ms.collection: windows
ms.date: 05/02/2018
ms.openlocfilehash: 6817dd6baacd835b7d433177ff18af1238ee44a6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102560084"
---
# <a name="pass-credentials-to-the-azure-dscextension-handler"></a>Passe credenciais ao manipulador Azure DSCExtension

Este artigo cobre a extensão de Configuração do Estado Desejado (DSC) para Azure. Para obter uma visão geral do manipulador de extensão DSC, consulte [Introdução ao controlador de extensão de configuração do estado Azure Desired](dsc-overview.md).

 

## <a name="pass-in-credentials"></a>Passe em credenciais

Como parte do processo de configuração, poderá ser necessário configurar contas de utilizador, aceder a serviços ou instalar um programa num contexto de utilizador. Para fazer estas coisas, tens de fornecer credenciais.

Pode utilizar o DSC para configurar configurações parametrizadas. Numa configuração parametrizada, as credenciais são transmitidas para a configuração e armazenadas de forma segura em ficheiros .mof. O manipulador de extensões Azure simplifica a gestão credencial através da gestão automática dos certificados.

O seguinte script de configuração DSC cria uma conta de utilizador local com a palavra-passe especificada:

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

É importante incluir o **nó local como** parte da configuração. O manipulador de extensão procura especificamente a declaração **local de node.** Se esta declaração faltar, os seguintes passos não funcionam. Também é importante incluir a tipa **[PsCredential]**. Este tipo específico ativa a extensão para encriptar a credencial.

Para publicar este script no armazenamento da Azure Blob:

`Publish-AzVMDscConfiguration -ConfigurationPath .\user_configuration.ps1`

Para definir a extensão do Azure DSC e fornecer a credencial:

```powershell
$configurationName = 'Main'
$configurationArguments = @{ Credential = Get-Credential }
$configurationArchive = 'user_configuration.ps1.zip'
$vm = Get-AzVM -Name 'example-1'

$vm = Set-AzVMDscExtension -VMName $vm -ConfigurationArchive $configurationArchive -ConfigurationName $configurationName -ConfigurationArgument @configurationArguments

$vm | Update-AzVM
```

## <a name="how-a-credential-is-secured"></a>Como uma credencial é segura

Executar este código sugere uma credencial. Depois da credencial ser fornecida, é brevemente guardada na memória. Quando a credencial é publicada utilizando o **cmdlet Set-AzVMDscExtension,** a credencial é transmitida através de HTTPS para o VM. No VM, a Azure armazena a credencial encriptada no disco utilizando o certificado VM local. A credencial é brevemente desencriptada na memória, e depois é reencri encriptada para passá-la para dSC.

Este processo é diferente de [utilizar configurações seguras sem o manipulador de extensão](/powershell/scripting/dsc/pull-server/securemof). O ambiente Azure dá-lhe uma forma de transmitir os dados de configuração de forma segura através de certificados. Quando utilizar o controlador de extensão DSC, não precisa de fornecer **$CertificatePath** ou uma **entrada $Thumbprint** /  **$CertificateID** no **ConfigurationData**.

## <a name="next-steps"></a>Passos seguintes

- Obtenha uma [introdução ao controlador de extensão Azure DSC](dsc-overview.md).
- Examine o [modelo do Gestor de Recursos Azure para a extensão DSC](dsc-template.md).
- Para obter mais informações sobre o PowerShell DSC, aceda ao [centro de documentação PowerShell](/powershell/scripting/dsc/overview/overview).
- Para obter mais funcionalidades que possa gerir utilizando o PowerShell DSC e para obter mais recursos DSC, consulte a [galeria PowerShell](https://www.powershellgallery.com/packages?q=DscResource&x=0&y=0).