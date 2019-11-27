---
title: Azure Disk Encryption com os pré-requisitos de aplicativo do Azure AD (versão anterior)
description: Este artigo fornece pré-requisitos para utilizar o Microsoft Azure Disk Encryption para VMs de IaaS.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18
ms.openlocfilehash: e1b9df750886af050163a85e2c6a3539bd63c733
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/24/2019
ms.locfileid: "74457194"
---
# <a name="azure-disk-encryption-with-azure-ad-previous-release"></a>Azure Disk Encryption com o Azure AD (versão anterior)

A nova versão do Azure Disk Encryption elimina a necessidade de fornecer um parâmetro de aplicativo Azure Active Directory (AD do Azure) para habilitar a criptografia de disco de VM. Com a nova versão, não é mais necessário fornecer as credenciais do Azure AD durante a etapa habilitar criptografia. Todas as novas VMs devem ser criptografadas sem os parâmetros do aplicativo do Azure AD usando a nova versão. Para obter instruções sobre como habilitar a criptografia de disco de VM usando a nova versão, consulte [Azure Disk Encryption para VMs do Linux](disk-encryption-overview.md). As VMs que já foram criptografadas com parâmetros de aplicativo do Azure AD ainda têm suporte e devem continuar a ser mantidas com a sintaxe do AAD.

Este artigo fornece suplementos para [Azure Disk Encryption para VMs Linux](disk-encryption-overview.md) com requisitos e pré-requisitos adicionais para Azure Disk Encryption com o Azure AD (versão anterior).

As informações contidas nessas seções permanecem as mesmas:

- [VMs e sistemas operacionais com suporte](disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Requisitos adicionais de VM](disk-encryption-overview.md#additional-vm-requirements)


 

## <a name="networking-and-group-policy"></a>Rede e Política de Grupo

Para habilitar o recurso de Azure Disk Encryption usando a sintaxe de parâmetro AAD mais antiga, as VMs de IaaS (infraestrutura como serviço) devem atender aos seguintes requisitos de configuração de ponto de extremidade de rede: 
  - Para obter um token para se conectar ao cofre de chaves, a VM IaaS deve ser capaz de se conectar a um ponto de extremidade do Azure AD, \[\]login.microsoftonline.com.
  - Para escrever as chaves de encriptação para o seu Cofre de chaves, a VM de IaaS tem de conseguir ligar ao ponto final do Cofre de chaves.
  - A VM de IaaS tem de ser capaz de ligar a um ponto de extremidade de armazenamento do Azure que aloja o repositório de extensão do Azure e uma conta de armazenamento do Azure que aloja os ficheiros VHD.
  -  Se sua política de segurança limitar o acesso de VMs do Azure à Internet, você poderá resolver o URI anterior e configurar uma regra específica para permitir a conectividade de saída com os IPs. Para obter mais informações, consulte [Azure Key Vault atrás de um firewall](../../key-vault/key-vault-access-behind-firewall.md).
  - No Windows, se o TLS 1,0 estiver explicitamente desabilitado e a versão do .NET não for atualizada para 4,6 ou superior, a seguinte alteração de registro permitirá que Azure Disk Encryption selecione a versão mais recente do TLS:
    
            [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319]
            "SystemDefaultTlsVersions"=dword:00000001
            "SchUseStrongCrypto"=dword:00000001
    
            [HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319]
            "SystemDefaultTlsVersions"=dword:00000001
            "SchUseStrongCrypto"=dword:00000001` 
         
    
### <a name="group-policy"></a>Política de Grupo
 - A solução Azure Disk Encryption utiliza o protetor de chave externo do BitLocker para VMs de IaaS do Windows. Para VMs ingressadas no domínio, não envie nenhuma política de grupo que imponha protetores de TPM. Para obter informações sobre o Política de Grupo para a opção **permitir BitLocker sem um TPM compatível**, consulte [referência de política de grupo do BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings#bkmk-unlockpol1).

- A política do BitLocker em máquinas virtuais ingressadas no domínio com um Política de Grupo personalizado deve incluir a seguinte configuração: [Configurar o armazenamento do usuário das informações de recuperação do BitLocker-> permitir a chave de recuperação de 256 bits](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings). Azure Disk Encryption falha quando as configurações de Política de Grupo personalizadas para o BitLocker são incompatíveis. Em computadores que não têm a configuração de política correta, aplique a nova política, force a nova política a ser atualizada (gpupdate. exe/Force) e, em seguida, reinicie se for necessário. 

## <a name="encryption-key-storage-requirements"></a>Requisitos de armazenamento de chave de criptografia 

Azure Disk Encryption requer Azure Key Vault para controlar e gerenciar chaves de criptografia de disco e segredos. O cofre de chaves e as VMs devem residir na mesma região e assinatura do Azure.

Para obter mais informações, consulte [criando e configurando um cofre de chaves para Azure Disk Encryption com o Azure AD (versão anterior)](disk-encryption-key-vault-aad.md).
 
## <a name="next-steps"></a>Passos Seguintes

- [Criando e configurando um cofre de chaves para Azure Disk Encryption com o Azure AD (versão anterior)](disk-encryption-key-vault-aad.md)
- [Habilitar Azure Disk Encryption com o Azure AD em VMs do Linux (versão anterior)](disk-encryption-linux-aad.md)
- [Script da CLI de pré-requisitos Azure Disk Encryption](https://github.com/ejarvi/ade-cli-getting-started)
- [Script do PowerShell de Azure Disk Encryption pré-requisitos](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)