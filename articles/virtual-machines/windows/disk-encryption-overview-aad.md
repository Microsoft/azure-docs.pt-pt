---
title: Encriptação do disco Azure com Azure AD (versão anterior)
description: Este artigo fornece pré-requisitos para a utilização da Encriptação do Disco Azure do Microsoft para iaaS VMs.
author: msmbaldwin
ms.service: virtual-machines
ms.subservice: disks
ms.topic: conceptual
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18
ms.openlocfilehash: 8db9aad279c151073f8c674854d139276a84b064
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102555385"
---
# <a name="azure-disk-encryption-with-azure-ad-previous-release"></a>Encriptação do disco Azure com Azure AD (versão anterior)

**A nova versão da Encriptação do Disco Azure elimina o requisito para fornecer um parâmetro de aplicação AD Azure para permitir a encriptação do disco VM. Com a nova versão, já não é obrigado a fornecer credenciais AZure AD durante o passo de encriptação ativa. Todos os novos VMs devem ser encriptados sem os parâmetros da aplicação AD Azure utilizando a nova versão. Para visualizar instruções para ativar a encriptação do disco VM utilizando a nova versão, consulte [a encriptação do disco Azure para VMs do Windows](disk-encryption-overview.md). Os VMs que já estavam encriptados com os parâmetros da aplicação AD Azure ainda são suportados e devem continuar a ser mantidos com a sintaxe AAD.**

Este artigo complementa [a encriptação do disco Azure para VMs do Windows](disk-encryption-overview.md) com requisitos adicionais e pré-requisitos para encriptação de disco Azure com Azure AD (versão anterior). A [secção de VMs e sistemas operativos suportados](disk-encryption-overview.md#supported-vms-and-operating-systems) permanece a mesma.

## <a name="networking-and-group-policy"></a>Política de Networking e Grupo

**Para ativar a funcionalidade de encriptação do disco Azure utilizando a sintaxe de parâmetro AAD mais antiga, os VMs iaas devem satisfazer os seguintes requisitos de configuração do ponto final da rede:** 
  - Para obter um token para ligar ao seu cofre chave, o IaaS VM deve ser capaz de ligar a um ponto final do Azure Ative Directory, \[ login.microsoftonline.com \] .
  - Para escrever as chaves de encriptação do cofre, o IaaS VM deve ser capaz de ligar ao ponto final do cofre.
  - O IaaS VM deve ser capaz de se ligar a um ponto final de armazenamento Azure que acolhe o repositório de extensão Azure e uma conta de armazenamento Azure que acolhe os ficheiros VHD.
  -  Se a sua política de segurança limitar o acesso dos VMs Azure à Internet, pode resolver o URI anterior e configurar uma regra específica para permitir a conectividade de saída com os IPs. Para obter mais informações, consulte [o Azure Key Vault atrás de uma firewall](../../key-vault/general/access-behind-firewall.md).
  - O VM a ser encriptado deve ser configurado para utilizar o TLS 1.2 como protocolo predefinido. Se o TLS 1.0 tiver sido explicitamente desativado e a versão .NET não tiver sido atualizada para 4.6 ou superior, a seguinte alteração de registo permitirá à ADE selecionar a versão TLS mais recente:

```console
[HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319]
"SystemDefaultTlsVersions"=dword:00000001
"SchUseStrongCrypto"=dword:00000001

[HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319]
"SystemDefaultTlsVersions"=dword:00000001
"SchUseStrongCrypto"=dword:00000001` 
```

**Política de Grupo:**
 - A solução de encriptação do disco Azure utiliza o protetor de chave externo BitLocker para Os VMs do Windows IaaS. Para o domínio associado aos VMs, não pressione nenhuma política de grupo que aplique protetores TPM. Para obter informações sobre a política de grupo para "Permitir o BitLocker sem um TPM compatível", consulte [a Referência política do Grupo BitLocker](/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings#bkmk-unlockpol1).

-  A política bitLocker sobre máquinas virtuais de domínio com a política de grupo personalizado deve incluir a seguinte definição: Configurar o [armazenamento do utilizador de informações de recuperação bitLocker -> Permitir a chave de recuperação de 256 bits](/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings). A encriptação do disco Azure falhará quando as definições de política de grupo personalizadas para BitLocker são incompatíveis. Nas máquinas que não tenham a definição de política correta, aplique a nova política, force a nova política a atualizar (gpupdate.exe/força), e depois poderá ser necessário reiniciar.  

## <a name="encryption-key-storage-requirements"></a>Requisitos de armazenamento de chaves de encriptação  

A encriptação do disco Azure requer um Cofre de Chave Azure para controlar e gerir chaves e segredos de encriptação de discos. O seu cofre-chave e VMs devem residir na mesma região de Azure e subscrição.

Para mais detalhes, consulte [criar e configurar um cofre-chave para encriptação do disco Azure com Azure AD (versão anterior)](disk-encryption-key-vault-aad.md).
 
## <a name="next-steps"></a>Passos seguintes

- [Criar e configurar um cofre chave para encriptação de disco Azure com Azure AD (versão anterior)](disk-encryption-key-vault-aad.md)
- [Ativar encriptação do disco Azure com Azure AD em VMs do Windows (versão anterior)](disk-encryption-windows-aad.md)
- [Encriptação de disco Azure pré-requisitos do script CLI](https://github.com/ejarvi/ade-cli-getting-started)
- [Encriptação de disco Azure pré-requisitos PowerShell script](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
