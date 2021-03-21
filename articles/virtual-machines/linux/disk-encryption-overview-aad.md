---
title: Encriptação do disco Azure com pré-requisitos da aplicação AD Azure (versão anterior)
description: Este artigo fornece suplementos à Encriptação do Disco Azure para Os VMs Linux com requisitos adicionais e pré-requisitos para encriptação de disco Azure com Azure AD.
author: msmbaldwin
ms.service: virtual-machines
ms.subservice: disks
ms.collection: linux
ms.topic: conceptual
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18
ms.openlocfilehash: e27eb64139c20ac2b8c776edc2d0840b80fddb62
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102558343"
---
# <a name="azure-disk-encryption-with-azure-active-directory-ad-previous-release"></a>Encriptação do disco Azure com Diretório Ativo Azure (AD) (versão anterior)

A nova versão da Encriptação do Disco Azure elimina o requisito para fornecer um parâmetro de aplicação Azure Ative Directory (Azure AD) para permitir a encriptação do disco VM. Com a nova versão, já não é obrigado a fornecer credenciais AZure AD durante o passo de encriptação ativa. Todos os novos VMs devem ser encriptados sem os parâmetros da aplicação AD Azure utilizando a nova versão. Para obter instruções sobre como ativar a encriptação do disco VM utilizando a nova versão, consulte [Azure Disk Encryption for Linux VMs](disk-encryption-overview.md). Os VMs que já estavam encriptados com os parâmetros da aplicação AD Azure ainda são suportados e devem continuar a ser mantidos com a sintaxe AAD.

Este artigo fornece suplementos à [Encriptação do Disco Azure para Os VMs Linux](disk-encryption-overview.md) com requisitos adicionais e pré-requisitos para encriptação de disco Azure com Azure AD (versão anterior).

A informação nestas secções permanece a mesma:

- [VMs e sistemas operativos suportados](disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Requisitos adicionais de VM](disk-encryption-overview.md#additional-vm-requirements)


## <a name="networking-and-group-policy"></a>Política de Networking e Grupo

Para ativar a funcionalidade de encriptação do disco Azure utilizando a sintaxe de parâmetro AAD mais antiga, a infraestrutura como um VMs de serviço (IaaS) deve satisfazer os seguintes requisitos de configuração do ponto final da rede: 
  - Para obter um token para ligar ao seu cofre chave, o IaaS VM deve ser capaz de ligar a um ponto final AZure AD, \[ login.microsoftonline.com \] .
  - Para escrever as chaves de encriptação do cofre, o IaaS VM deve ser capaz de ligar ao ponto final do cofre.
  - O IaaS VM deve ser capaz de se ligar a um ponto final de armazenamento Azure que acolhe o repositório de extensão Azure e uma conta de armazenamento Azure que acolhe os ficheiros VHD.
  -  Se a sua política de segurança limitar o acesso dos VMs Azure à internet, pode resolver o URI anterior e configurar uma regra específica para permitir a conectividade de saída com os IPs. Para obter mais informações, consulte [o Azure Key Vault atrás de uma firewall](../../key-vault/general/access-behind-firewall.md).
  - No Windows, se o TLS 1.0 for explicitamente desativado e a versão .NET não for atualizada para 4.6 ou superior, a seguinte alteração de registo permite que a Encriptação do Disco Azure selecione a versão TLS mais recente:

  ```config-registry
  [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319]
  "SystemDefaultTlsVersions"=dword:00000001
  "SchUseStrongCrypto"=dword:00000001
    
  [HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319]
  "SystemDefaultTlsVersions"=dword:00000001
  "SchUseStrongCrypto"=dword:00000001` 
  ```

### <a name="group-policy"></a>Política de Grupo
 - A solução de encriptação do disco Azure utiliza o protetor de chave externo BitLocker para Os VMs do Windows IaaS. Para VMs unidos por domínio, não pressione quaisquer Políticas de Grupo que imponham protetores TPM. Para obter informações sobre a Política de Grupo para a opção **Permitir bitLocker sem um TPM compatível,** consulte [a referência Política do Grupo BitLocker](/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings#bkmk-unlockpol1).

- A política do BitLocker sobre máquinas virtuais unidas a domínios com uma Política de Grupo personalizada deve incluir a seguinte definição: Configurar o [armazenamento do utilizador de informações de recuperação bitLocker -> Permitir a chave de recuperação de 256 bits](/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings). A encriptação do disco Azure falha quando as definições personalizadas da Política de Grupo para BitLocker são incompatíveis. Nas máquinas que não têm a definição de política correta, aplique a nova política, force a nova política a atualizar (gpupdate.exe/força) e, em seguida, reinicie se for necessário. 

## <a name="encryption-key-storage-requirements"></a>Requisitos de armazenamento de chaves de encriptação 

A encriptação do disco Azure requer a Azure Key Vault para controlar e gerir chaves e segredos de encriptação de discos. O seu cofre-chave e VMs devem residir na mesma região de Azure e subscrição.

Para obter mais informações, consulte [criar e configurar um cofre-chave para encriptação do disco Azure com Azure AD (versão anterior)](disk-encryption-key-vault-aad.md).
 
## <a name="next-steps"></a>Passos seguintes

- [Criar e configurar um cofre chave para encriptação de disco Azure com Azure AD (versão anterior)](disk-encryption-key-vault-aad.md)
- [Ativar encriptação do disco Azure com Azure AD em VMs Linux (versão anterior)](disk-encryption-linux-aad.md)
- [Encriptação de disco Azure pré-requisitos do script CLI](https://github.com/ejarvi/ade-cli-getting-started)
- [Encriptação de disco Azure pré-requisitos PowerShell script](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
