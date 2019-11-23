---
title: Azure Disk Encryption com o Azure AD (versão anterior)
description: Este artigo fornece pré-requisitos para utilizar o Microsoft Azure Disk Encryption para VMs de IaaS.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18
ms.openlocfilehash: 33b257e9d344fc31df072509f105d2e8fd1bd29b
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/10/2019
ms.locfileid: "72245178"
---
# <a name="azure-disk-encryption-with-azure-ad-previous-release"></a>Azure Disk Encryption com o Azure AD (versão anterior)

**A nova versão do Azure Disk Encryption elimina a necessidade de fornecer um parâmetro de aplicativo do Azure AD para habilitar a criptografia de disco de VM. Com a nova versão, você não precisa mais fornecer as credenciais do Azure AD durante a etapa habilitar criptografia. Todas as novas VMs devem ser criptografadas sem os parâmetros de aplicativo do Azure AD usando a nova versão. Para exibir instruções para habilitar a criptografia de disco de VM usando a nova versão, consulte [Azure Disk Encryption para VMs do Windows](disk-encryption-overview.md). As VMs que já foram criptografadas com parâmetros de aplicativo do Azure AD ainda têm suporte e devem continuar a ser mantidas com a sintaxe do AAD.**

Este artigo complementa [Azure Disk Encryption para VMs do Windows](disk-encryption-overview.md) com requisitos e pré-requisitos adicionais para Azure Disk Encryption com o Azure AD (versão anterior). A seção [VMs e sistemas operacionais com suporte](disk-encryption-overview.md#supported-vms-and-operating-systems) permanece a mesma.

## <a name="networking-and-group-policy"></a>Rede e Política de Grupo

**Para ativar a funcionalidade de encriptação de disco do Azure com o AAD mais antigo sintaxe do parâmetro, as VMs de IaaS tem de cumprir os seguintes requisitos de configuração do ponto final de rede:** 
  - Para obter um token para ligar ao seu Cofre de chaves, a VM de IaaS tem de ser capaz de ligar a um ponto de extremidade do Azure Active Directory \[login.microsoftonline.com\].
  - Para escrever as chaves de encriptação para o seu Cofre de chaves, a VM de IaaS tem de conseguir ligar ao ponto final do Cofre de chaves.
  - A VM de IaaS tem de ser capaz de ligar a um ponto de extremidade de armazenamento do Azure que aloja o repositório de extensão do Azure e uma conta de armazenamento do Azure que aloja os ficheiros VHD.
  -  Se a política de segurança limita o acesso a partir de VMs do Azure para a Internet, pode resolver o URI anterior e configurar uma regra específica para permitir a conectividade de saída para os IPs. Para obter mais informações, consulte [do Azure Key Vault protegido por uma firewall](../../key-vault/key-vault-access-behind-firewall.md).
  - No Windows, se TLS 1.0 tem sido explicitamente desabilitado e a versão do .NET não foi atualizada para 4.6 ou superior, a alteração de registo seguinte irá permitir ADE selecionar a versão mais recente do TLS:
    
        [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319]
        "SystemDefaultTlsVersions"=dword:00000001
        "SchUseStrongCrypto"=dword:00000001

        [HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319]
        "SystemDefaultTlsVersions"=dword:00000001
        "SchUseStrongCrypto"=dword:00000001` 
     

**Política de grupo:**
 - A solução Azure Disk Encryption utiliza o protetor de chave externo do BitLocker para VMs de IaaS do Windows. Para VMs associados ao domínio, não enviar por push as políticas de grupo que impõem protetores TPM. Para informações sobre a política de grupo para "Permitir BitLocker sem um TPM compatível", consulte [referência de política de grupo de BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings#bkmk-unlockpol1).

-  A política do BitLocker em máquinas virtuais ingressadas no domínio com a política de grupo personalizada deve incluir a seguinte configuração: [Configurar o armazenamento do usuário das informações de recuperação do BitLocker-> permitir chave de recuperação de 256 bits](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings). Azure Disk Encryption falhará quando as configurações de política de grupo personalizadas para o BitLocker forem incompatíveis. Nas máquinas que não tinham a definição de política correta, aplicar a nova política, forçar a nova política de atualização (gpupdate.exe /force) e, em seguida, reiniciar poderá ser necessário.  

## <a name="encryption-key-storage-requirements"></a>Requisitos de armazenamento de chave de criptografia  

Azure Disk Encryption requer uma Azure Key Vault para controlar e gerenciar chaves e segredos de criptografia de disco. O cofre de chaves e as VMs devem residir na mesma região e assinatura do Azure.

Para obter detalhes, consulte [criando e configurando um cofre de chaves para Azure Disk Encryption com o Azure AD (versão anterior)](disk-encryption-key-vault-aad.md).
 
## <a name="next-steps"></a>Passos seguintes

- [Criando e configurando um cofre de chaves para Azure Disk Encryption com o Azure AD (versão anterior)](disk-encryption-key-vault-aad.md)
- [Habilitar Azure Disk Encryption com o Azure AD em VMs do Windows (versão anterior)](disk-encryption-windows-aad.md)
- [Script da CLI de pré-requisitos Azure Disk Encryption](https://github.com/ejarvi/ade-cli-getting-started)
- [Script do PowerShell de Azure Disk Encryption pré-requisitos](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)