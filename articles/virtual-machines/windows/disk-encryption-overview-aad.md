---
title: Azure Disk Encryption com o Azure AD (versão anterior)
description: Este artigo fornece pré-requisitos para usar o Microsoft Azure a criptografia de disco para VMs IaaS.
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

**Para habilitar o recurso de Azure Disk Encryption usando a sintaxe de parâmetro AAD mais antiga, as VMs de IaaS devem atender aos seguintes requisitos de configuração de ponto de extremidade de rede:** 
  - Para obter um token para se conectar ao cofre de chaves, a VM IaaS deve ser capaz de se conectar a um ponto de extremidade Azure Active Directory, @no__t -0login. microsoftonline. com @ no__t-1.
  - Para gravar as chaves de criptografia em seu cofre de chaves, a VM IaaS deve ser capaz de se conectar ao ponto de extremidade do cofre de chaves.
  - A VM IaaS deve ser capaz de se conectar a um ponto de extremidade de armazenamento do Azure que hospeda o repositório de extensões do Azure e uma conta de armazenamento do Azure que hospeda os arquivos VHD.
  -  Se sua política de segurança limitar o acesso de VMs do Azure à Internet, você poderá resolver o URI anterior e configurar uma regra específica para permitir a conectividade de saída com os IPs. Para obter mais informações, consulte [Azure Key Vault atrás de um firewall](../../key-vault/key-vault-access-behind-firewall.md).
  - No Windows, se o TLS 1,0 tiver sido explicitamente desabilitado e a versão do .NET não tiver sido atualizada para 4,6 ou superior, a seguinte alteração no registro permitirá que o ADE selecione a versão mais recente do TLS:
    
        [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319]
        "SystemDefaultTlsVersions"=dword:00000001
        "SchUseStrongCrypto"=dword:00000001

        [HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319]
        "SystemDefaultTlsVersions"=dword:00000001
        "SchUseStrongCrypto"=dword:00000001` 
     

**Política de Grupo:**
 - A solução Azure Disk Encryption usa o protetor de chave externa do BitLocker para VMs IaaS do Windows. Para VMs ingressadas no domínio, não envie nenhuma política de grupo que imponha protetores de TPM. Para obter informações sobre a política de grupo para "permitir BitLocker sem um TPM compatível", consulte [referência do bitlocker política de grupo](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings#bkmk-unlockpol1).

-  A política do BitLocker em máquinas virtuais ingressadas no domínio com a política de grupo personalizada deve incluir a seguinte configuração: [Configurar o armazenamento do usuário das informações de recuperação do BitLocker-> permitir chave de recuperação de 256 bits](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings). Azure Disk Encryption falhará quando as configurações de política de grupo personalizadas para o BitLocker forem incompatíveis. Em computadores que não têm a configuração de política correta, aplique a nova política, force a nova política a ser atualizada (gpupdate. exe/Force) e, em seguida, a reinicialização pode ser necessária.  

## <a name="encryption-key-storage-requirements"></a>Requisitos de armazenamento de chave de criptografia  

Azure Disk Encryption requer uma Azure Key Vault para controlar e gerenciar chaves e segredos de criptografia de disco. O cofre de chaves e as VMs devem residir na mesma região e assinatura do Azure.

Para obter detalhes, consulte [criando e configurando um cofre de chaves para Azure Disk Encryption com o Azure AD (versão anterior)](disk-encryption-key-vault-aad.md).
 
## <a name="next-steps"></a>Passos seguintes

- [Criando e configurando um cofre de chaves para Azure Disk Encryption com o Azure AD (versão anterior)](disk-encryption-key-vault-aad.md)
- [Habilitar Azure Disk Encryption com o Azure AD em VMs do Windows (versão anterior)](disk-encryption-windows-aad.md)
- [Script da CLI de pré-requisitos Azure Disk Encryption](https://github.com/ejarvi/ade-cli-getting-started)
- [Script do PowerShell de Azure Disk Encryption pré-requisitos](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)