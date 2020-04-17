---
title: Encriptação de disco azure com AD Azure (versão anterior)
description: Este artigo fornece pré-requisitos para a utilização da encriptação do disco Microsoft Azure para VMs IaaS.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18
ms.openlocfilehash: 9cd668fcac3751715fbe91c9aeff98583c9d03d5
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81458918"
---
# <a name="azure-disk-encryption-with-azure-ad-previous-release"></a>Encriptação de disco azure com AD Azure (versão anterior)

**O novo lançamento da Encriptação do Disco Azure elimina a exigência de fornecer um parâmetro de aplicação Azure AD para permitir a encriptação do disco VM. Com o novo lançamento, já não é necessário fornecer credenciais de AD Azure durante a fase de encriptação ativa. Todos os novos VMs devem ser encriptados sem os parâmetros da aplicação Azure AD utilizando o novo lançamento. Para visualizar instruções para ativar a encriptação do disco VM utilizando a nova versão, consulte a encriptação do [disco Azure para VMs do Windows](disk-encryption-overview.md). Os VMs que já estavam encriptados com parâmetros de aplicação Azure AD ainda são suportados e devem continuar a ser mantidos com a sintaxe AAD.**

Este artigo complementa a encriptação do [disco Azure para VMs do Windows](disk-encryption-overview.md) com requisitos e pré-requisitos adicionais para encriptação de disco azure com AD Azure (versão anterior). A secção de [VMs suportados e sistemas operativos](disk-encryption-overview.md#supported-vms-and-operating-systems) continua a ser a mesma.

## <a name="networking-and-group-policy"></a>Política de Networking e Grupo

**Para ativar a funcionalidade de encriptação do disco Azure utilizando a sintaxe de parâmetros AAD mais antiga, os VMs IaaS devem satisfazer os seguintes requisitos de configuração do ponto final da rede:** 
  - Para obter um símbolo para ligar ao seu cofre chave, o VM IaaS deve ser \[\]capaz de ligar a um ponto final do Diretório Ativo Azure, login.microsoftonline.com .
  - Para escrever as chaves de encriptação do seu cofre chave, o VM IaaS deve ser capaz de ligar ao ponto final do cofre chave.
  - O IaaS VM deve ser capaz de ligar a um ponto final de armazenamento Azure que acolhe o repositório de extensão Azure e uma conta de armazenamento Azure que acolhe os ficheiros VHD.
  -  Se a sua política de segurança limitar o acesso dos VMs Azure à Internet, pode resolver o URI anterior e configurar uma regra específica para permitir a conectividade de saída aos IPs. Para mais informações, consulte [o Cofre chave Azure atrás de uma firewall](../../key-vault/key-vault-access-behind-firewall.md).
  - O VM a encriptar deve ser configurado para utilizar o TLS 1.2 como protocolo predefinido. Se o TLS 1.0 tiver sido explicitamente desativado e a versão .NET não tiver sido atualizada para 4.6 ou superior, a seguinte alteração de registo permitirá à ADE selecionar a versão TLS mais recente:

```console
[HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319]
"SystemDefaultTlsVersions"=dword:00000001
"SchUseStrongCrypto"=dword:00000001

[HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319]
"SystemDefaultTlsVersions"=dword:00000001
"SchUseStrongCrypto"=dword:00000001` 
```

**Política de Grupo:**
 - A solução de encriptação do disco Azure utiliza o protetor de teclas externo BitLocker para VMs Windows IaaS. Para os VMs de domínio, não empurre nenhuma política de grupo que aplique protetores TPM. Para obter informações sobre a política do grupo para "Permitir bitLocker sem um TPM compatível", consulte [BitLocker Group Policy Reference](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings#bkmk-unlockpol1).

-  A política bitLocker no domínio aderiu a máquinas virtuais com a política de grupo personalizado deve incluir a seguinte definição: [Configure o armazenamento do utilizador de informações de recuperação BitLocker - > Permitir a chave de recuperação de 256 bits](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings). A encriptação do disco Azure falhará quando as definições de política de grupo personalizadas para o BitLocker forem incompatíveis. Nas máquinas que não tinham a definição correta da política, apliquem a nova política, forcem a nova política a atualizar (gpupdate.exe/force) e, em seguida, o reinício pode ser necessário.  

## <a name="encryption-key-storage-requirements"></a>Requisitos de armazenamento de chaves de encriptação  

A encriptação do disco Azure requer um Cofre de Chave Azure para controlar e gerir chaves e segredos de encriptação de disco. O seu cofre chave e VMs devem residir na mesma região azure e subscrição.

Para mais detalhes, consulte [Criar e configurar um cofre chave para encriptação de disco azure com AD Azure (versão anterior)](disk-encryption-key-vault-aad.md).
 
## <a name="next-steps"></a>Passos seguintes

- [Criação e configuração de um cofre chave para encriptação de disco azure com AD Azure (versão anterior)](disk-encryption-key-vault-aad.md)
- [Ativar encriptação do disco Azure com AD Azure em VMs do Windows (versão anterior)](disk-encryption-windows-aad.md)
- [Encriptação do disco azure pré-requisitos script CLI](https://github.com/ejarvi/ade-cli-getting-started)
- [Encriptação de disco azure pré-requisitos PowerShell script](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
