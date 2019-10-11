---
title: Habilitar Azure Disk Encryption para VMs do Windows
description: Este artigo fornece instruções sobre como habilitar a criptografia de disco Microsoft Azure para VMs do Windows.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 10/05/2019
ms.custom: seodec18
ms.openlocfilehash: e38a58d076f6a8b7b2b07e468c61835ed06f55a4
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/10/2019
ms.locfileid: "72255601"
---
# <a name="azure-disk-encryption-for-windows-vms"></a>Azure Disk Encryption para VMs do Windows 

Azure Disk Encryption ajuda a proteger e proteger seus dados para atender aos compromissos de segurança e conformidade da organização. Ele usa o recurso [BitLocker](https://en.wikipedia.org/wiki/BitLocker) do Windows para fornecer criptografia de volume para o sistema operacional e os discos de dados das VMs (máquinas virtuais) do Azure e é integrado com o [Azure Key Vault](../../key-vault/index.yml) para ajudá-lo a controlar e gerenciar as chaves e os segredos de criptografia de disco. 

Se você usar a [central de segurança do Azure](../../security-center/index.yml), você será alertado se tiver VMs que não estão criptografadas. Os alertas são mostrados como severidade alta e a recomendação é criptografar essas VMs.

![Alerta de criptografia de disco da central de segurança do Azure](../media/disk-encryption/security-center-disk-encryption-fig1.png)

> [!WARNING]
> - Se você tiver usado anteriormente Azure Disk Encryption com o Azure AD para criptografar uma VM, você deve continuar usando essa opção para criptografar sua VM. Consulte [Azure Disk Encryption com o Azure AD (versão anterior)](disk-encryption-overview-aad.md) para obter detalhes. 
> - Algumas recomendações podem aumentar o uso de recursos de dados, rede ou computação, resultando em custos adicionais de licença ou assinatura. Você deve ter uma assinatura ativa válida do Azure para criar recursos no Azure nas regiões com suporte.

Você pode aprender os conceitos básicos do Azure Disk Encryption para Windows em apenas alguns minutos com a [criação e a criptografia de uma VM do Windows com CLI do Azure início rápido](disk-encryption-cli-quickstart.md) ou a [criação e a criptografia de uma VM do Windows com o início rápido do Azure PowerShell](disk-encryption-powershell-quickstart.md).

## <a name="supported-vms-and-operating-systems"></a>VMs e sistemas operacionais com suporte

### <a name="supported-vm-sizes"></a>Tamanhos de VM suportados

As VMs do Windows estão disponíveis em [vários tamanhos](sizes-general.md). O Azure Disk Encryption não está disponível em [VMs básicas da série A](/pricing/details/virtual-machines/series/)ou em máquinas virtuais com menos de 2 GB de memória.

Azure Disk Encryption também está disponível para VMs com armazenamento Premium.

### <a name="supported-operating-systems"></a>Sistemas operativos suportados

- Cliente Windows: Windows 8 e posterior.
- Windows Server: Windows Server 2008 R2 e posterior.  
 
> [!NOTE]
> O Windows Server 2008 R2 requer que o .NET Framework 4,5 seja instalado para criptografia; Instale-o de Windows Update com a atualização opcional Microsoft .NET Framework 4.5.2 para sistemas baseados em Windows Server 2008 R2 x64 ([KB2901983](https://www.catalog.update.microsoft.com/Search.aspx?q=KB2901983)).  
>  
> O Windows Server 2012 R2 Core e o Windows Server 2016 Core exigem que o componente BdeHdCfg seja instalado na VM para criptografia.


## <a name="networking-requirements"></a>Requisitos de rede
Para habilitar Azure Disk Encryption, as VMs devem atender aos seguintes requisitos de configuração de ponto de extremidade de rede:
  - Para obter um token para se conectar ao cofre de chaves, a VM do Windows deve ser capaz de se conectar a um ponto de extremidade Azure Active Directory, @no__t -0login. microsoftonline. com @ no__t-1.
  - Para gravar as chaves de criptografia no cofre de chaves, a VM do Windows deve ser capaz de se conectar ao ponto de extremidade do cofre de chaves.
  - A VM do Windows deve ser capaz de se conectar a um ponto de extremidade de armazenamento do Azure que hospeda o repositório de extensões do Azure e uma conta de armazenamento do Azure que hospeda os arquivos VHD.
  -  Se sua política de segurança limitar o acesso de VMs do Azure à Internet, você poderá resolver o URI anterior e configurar uma regra específica para permitir a conectividade de saída com os IPs. Para obter mais informações, consulte [Azure Key Vault atrás de um firewall](../../key-vault/key-vault-access-behind-firewall.md).    


## <a name="group-policy-requirements"></a>Requisitos de Política de Grupo

Azure Disk Encryption usa o protetor de chave externa do BitLocker para VMs do Windows. Para VMs ingressadas no domínio, não envie nenhuma política de grupo que imponha protetores de TPM. Para obter informações sobre a política de grupo para "permitir BitLocker sem um TPM compatível", consulte [referência do bitlocker política de grupo](/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings#bkmk-unlockpol1).

A política do BitLocker em máquinas virtuais ingressadas no domínio com a política de grupo personalizada deve incluir a seguinte configuração: [Configurar o armazenamento do usuário das informações de recuperação do BitLocker-> permitir chave de recuperação de 256 bits](/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings). Azure Disk Encryption falhará quando as configurações de política de grupo personalizadas para o BitLocker forem incompatíveis. Em computadores que não têm a configuração de política correta, aplique a nova política, force a nova política a ser atualizada (gpupdate. exe/Force) e, em seguida, a reinicialização pode ser necessária.

Azure Disk Encryption falhará se a diretiva de grupo no nível de domínio bloquear o algoritmo AES-CBC, que é usado pelo BitLocker.

## <a name="encryption-key-storage-requirements"></a>Requisitos de armazenamento de chave de criptografia  

Azure Disk Encryption requer uma Azure Key Vault para controlar e gerenciar chaves e segredos de criptografia de disco. O cofre de chaves e as VMs devem residir na mesma região e assinatura do Azure.

Para obter detalhes, consulte [criando e configurando um cofre de chaves para Azure Disk Encryption](disk-encryption-key-vault.md).

## <a name="terminology"></a>Terminologia
A tabela a seguir define alguns dos termos comuns usados na documentação de criptografia de disco do Azure:

| Terminologia | Definição |
| --- | --- |
| Azure Key Vault | O Key Vault é um serviço de gerenciamento de chaves, criptografado, baseado em módulos de segurança de hardware validados por FIPS (Federal Information Processing Standards). Esses padrões ajudam a proteger suas chaves de criptografia e segredos confidenciais. Para obter mais informações, consulte a documentação do [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) e [criando e configurando um cofre de chaves para Azure Disk Encryption](disk-encryption-key-vault.md). |
| CLI do Azure | [O CLI do Azure](/cli/azure/install-azure-cli) é otimizado para gerenciar e administrar os recursos do Azure na linha de comando.|
| BitLocker |O [BitLocker](https://technet.microsoft.com/library/hh831713.aspx) é uma tecnologia de criptografia de volume do Windows reconhecida pela indústria que é usada para habilitar a criptografia de disco em VMs do Windows. |
| Chave de criptografia de chave (KEK) | A chave assimétrica (RSA 2048) que você pode usar para proteger ou encapsular o segredo. Você pode fornecer uma chave protegida por HSM (módulo de segurança de hardware) ou chave protegida por software. Para obter mais informações, consulte a documentação do [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) e [criando e configurando um cofre de chaves para Azure Disk Encryption](disk-encryption-key-vault.md). |
| Cmdlets Powershell | Para obter mais informações, consulte [cmdlets Azure PowerShell](/powershell/azure/overview). |


## <a name="next-steps"></a>Passos seguintes

- [Início rápido-criar e criptografar uma VM do Windows com CLI do Azure](disk-encryption-cli-quickstart.md)
- [Início rápido-criar e criptografar uma VM do Windows com o Azure PowerShell](disk-encryption-powershell-quickstart.md)
- [Cenários de Azure Disk Encryption em VMs do Windows](disk-encryption-windows.md)
- [Script da CLI de pré-requisitos Azure Disk Encryption](https://github.com/ejarvi/ade-cli-getting-started)
- [Script do PowerShell de Azure Disk Encryption pré-requisitos](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
- [Criando e configurando um cofre de chaves para Azure Disk Encryption](disk-encryption-key-vault.md)


