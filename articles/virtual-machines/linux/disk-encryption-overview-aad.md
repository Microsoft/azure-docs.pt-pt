---
title: Encriptação de disco azure com pré-requisitos da aplicação Azure AD (versão prévia)
description: Este artigo fornece pré-requisitos para a utilização da encriptação do disco Microsoft Azure para VMs IaaS.
author: msmbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18
ms.openlocfilehash: 029743dbb44ab0ebb6ab8f695c0c966137c45107
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81459802"
---
# <a name="azure-disk-encryption-with-azure-ad-previous-release"></a>Encriptação de disco azure com AD Azure (versão anterior)

O novo lançamento da Encriptação do Disco Azure elimina a exigência de fornecer um parâmetro de aplicação Azure Ative Directory (Azure AD) para permitir a encriptação do disco VM. Com o novo lançamento, já não é obrigado a fornecer credenciais de AD Azure durante a fase de encriptação ativa. Todos os novos VMs devem ser encriptados sem os parâmetros da aplicação Azure AD utilizando o novo lançamento. Para obter instruções sobre como ativar a encriptação do disco VM utilizando o novo lançamento, consulte a [encriptação do disco Azure para VMs Linux](disk-encryption-overview.md). Os VMs que já estavam encriptados com parâmetros de aplicação Azure AD ainda são suportados e devem continuar a ser mantidos com a sintaxe AAD.

Este artigo fornece suplementos à Encriptação do [Disco Azure para VMs Linux](disk-encryption-overview.md) com requisitos e pré-requisitos adicionais para encriptação de disco azure com AD Azure (versão anterior).

A informação nestas secções permanece a mesma:

- [VMs e sistemas operativos suportados](disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Requisitos adicionais de VM](disk-encryption-overview.md#additional-vm-requirements)


 

## <a name="networking-and-group-policy"></a>Política de Networking e Grupo

Para ativar a funcionalidade de encriptação do disco Azure utilizando a sintaxe de parâmetros AAD mais antiga, a infraestrutura como um serviço (IaaS) VMs deve satisfazer os seguintes requisitos de configuração do ponto final da rede: 
  - Para obter um símbolo para ligar ao seu cofre chave, o VM IaaS deve \[ser\]capaz de ligar a um ponto final da AD Azure, login.microsoftonline.com .
  - Para escrever as chaves de encriptação do seu cofre chave, o VM IaaS deve ser capaz de ligar ao ponto final do cofre chave.
  - O IaaS VM deve ser capaz de ligar a um ponto final de armazenamento Azure que acolhe o repositório de extensão Azure e uma conta de armazenamento Azure que acolhe os ficheiros VHD.
  -  Se a sua política de segurança limitar o acesso dos VMs Azure à internet, pode resolver o URI anterior e configurar uma regra específica para permitir a conectividade de saída aos IPs. Para mais informações, consulte [o Cofre chave Azure atrás de uma firewall](../../key-vault/general/access-behind-firewall.md).
  - No Windows, se o TLS 1.0 estiver explicitamente desativado e a versão .NET não for atualizada para 4.6 ou superior, a seguinte alteração de registo permite à Encriptação do Disco Azure selecionar a versão TLS mais recente:
    
            [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319]
            "SystemDefaultTlsVersions"=dword:00000001
            "SchUseStrongCrypto"=dword:00000001
    
            [HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319]
            "SystemDefaultTlsVersions"=dword:00000001
            "SchUseStrongCrypto"=dword:00000001` 
         
    
### <a name="group-policy"></a>Política de Grupo
 - A solução de encriptação do disco Azure utiliza o protetor de teclas externo BitLocker para VMs Windows IaaS. Para VMs unidos pelo domínio, não empurre nenhuma Política de Grupo que aplique protetores TPM. Para obter informações sobre a Política de Grupo para a opção **Permitir BitLocker sem um TPM compatível,** consulte a referência da [Política do Grupo BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings#bkmk-unlockpol1).

- A política bitLocker em máquinas virtuais unidas ao domínio com uma Política de Grupo personalizada deve incluir a seguinte definição: [Configure o armazenamento do utilizador de informações de recuperação BitLocker -> Permitir uma chave de recuperação de 256 bits](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings). A encriptação do disco Azure falha quando as definições personalizadas de Política de Grupo para BitLocker são incompatíveis. Nas máquinas que não têm a definição correta da política, apliquem a nova política, forcem a nova política a atualizar (gpupdate.exe/force) e, em seguida, reiniciem se for necessário. 

## <a name="encryption-key-storage-requirements"></a>Requisitos de armazenamento de chaves de encriptação 

A encriptação do disco Azure requer que o Cofre de Chaves Azure controle e gere as chaves e segredos de encriptação do disco. O seu cofre chave e VMs devem residir na mesma região azure e subscrição.

Para mais informações, consulte [Criar e configurar um cofre chave para encriptação de disco azure com AD Azure (versão anterior)](disk-encryption-key-vault-aad.md).
 
## <a name="next-steps"></a>Passos seguintes

- [Criação e configuração de um cofre chave para encriptação de disco azure com AD Azure (versão anterior)](disk-encryption-key-vault-aad.md)
- [Ativar encriptação de disco Azure com AD Azure em VMs Linux (versão anterior)](disk-encryption-linux-aad.md)
- [Encriptação do disco azure pré-requisitos script CLI](https://github.com/ejarvi/ade-cli-getting-started)
- [Encriptação de disco azure pré-requisitos PowerShell script](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)