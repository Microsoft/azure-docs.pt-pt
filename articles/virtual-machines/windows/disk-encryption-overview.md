---
title: Ativar encriptação de disco Azure para VMs do Windows
description: Este artigo fornece instruções sobre a ativação da encriptação do disco Microsoft Azure para VMs do Windows.
author: msmbaldwin
ms.service: virtual-machines-windows
ms.subservice: security
ms.topic: article
ms.author: mbaldwin
ms.date: 10/05/2019
ms.custom: seodec18
ms.openlocfilehash: 8bed34e816207c9f0bd0565abab6af4adbaeb7fd
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82081647"
---
# <a name="azure-disk-encryption-for-windows-vms"></a>Encriptação de disco azure para VMs do Windows 

A Azure Disk Encryption ajuda a proteger e a salvaguardar os seus dados para cumprir as obrigações de conformidade e segurança da sua organização. Utiliza a funcionalidade [Bitlocker](https://en.wikipedia.org/wiki/BitLocker) do Windows para fornecer encriptação de volume para o OS e discos de dados de máquinas virtuais Azure (VMs), e está integrado com o [Azure Key Vault](../../key-vault/index.yml) para o ajudar a controlar e gerir as chaves e segredos de encriptação do disco. 

Se utilizar o Centro de [Segurança Azure,](../../security-center/index.yml)é alertado se tiver VMs que não estejam encriptados. Os alertas mostram como Alta Severidade e a recomendação é encriptar estes VMs.

![Alerta de encriptação do disco do Azure Security Center](../media/disk-encryption/security-center-disk-encryption-fig1.png)

> [!WARNING]
> - Se já utilizou anteriormente a Encriptação do Disco Azure com a AD Azure para encriptar um VM, tem de continuar a utilizar esta opção para encriptar o seu VM. Consulte a Encriptação do [Disco Azure com AD Azure (versão anterior)](disk-encryption-overview-aad.md) para obter mais detalhes. 
> - Certas recomendações podem aumentar a utilização de dados, rede ou computação de recursos, resultando em custos adicionais de licença ou subscrição. Deve ter uma subscrição azure ativa válida para criar recursos em Azure nas regiões apoiadas.

Pode aprender os fundamentos da encriptação do Disco Azure para windows em poucos minutos com o [Create e encriptar um VM Windows com quickstart Azure CLI](disk-encryption-cli-quickstart.md) ou o [Create e encriptar um VM Windows com quickstart Azure Powershell](disk-encryption-powershell-quickstart.md).

## <a name="supported-vms-and-operating-systems"></a>VMs e sistemas operativos suportados

### <a name="supported-vms"></a>VMs suportados

Os VMs do Windows estão disponíveis numa [gama de tamanhos.](sizes-general.md) A Encriptação do Disco Azure não está disponível em [VMs básicos, sérieA,](https://azure.microsoft.com/pricing/details/virtual-machines/series/)ou em máquinas virtuais com menos de 2 GB de memória.

A Encriptação azure Disk também está disponível para VMs com armazenamento premium.

A encriptação do disco Azure não está disponível nas [VMs da Geração 2)](generation-2.md#generation-1-vs-generation-2-capabilities)e nas [séries Lsv2.](../lsv2-series.md) Para mais exceções, consulte [encriptação do disco Azure: cenários não suportados](disk-encryption-windows.md#unsupported-scenarios).

### <a name="supported-operating-systems"></a>Sistemas operativos suportados

- Cliente windows: Windows 8 e mais tarde.
- Windows Server: Windows Server 2008 R2 e posteriormente.  
 
> [!NOTE]
> O Windows Server 2008 R2 requer que a .NET Framework 4.5 seja instalada para encriptação; instale-o a partir do Windows Update com a atualização opcional Microsoft .NET Framework 4.5.2 para sistemas baseados no Windows Server 2008 R2 x64[(KB2901983](https://www.catalog.update.microsoft.com/Search.aspx?q=KB2901983)).  
>  
> O Núcleo De Núcleo E Windows Server 2012 R2 Core e Windows Server 2016 O Núcleo de Bdehdcfg requer que o componente bdehdcfg seja instalado no VM para encriptação.


## <a name="networking-requirements"></a>Requisitos de networking
Para ativar a encriptação do disco Azure, os VMs devem satisfazer os seguintes requisitos de configuração do ponto final da rede:
  - Para obter um sinal para ligar ao seu cofre chave, o Windows VM deve ser \[\]capaz de ligar a um ponto final do Diretório Ativo Azure, login.microsoftonline.com .
  - Para escrever as chaves de encriptação do seu cofre de chaves, o Windows VM deve ser capaz de se ligar ao ponto final do cofre chave.
  - O Windows VM deve ser capaz de se ligar a um ponto final de armazenamento Azure que acolhe o repositório de extensão Azure e uma conta de armazenamento Azure que acolhe os ficheiros VHD.
  -  Se a sua política de segurança limitar o acesso dos VMs Azure à Internet, pode resolver o URI anterior e configurar uma regra específica para permitir a conectividade de saída aos IPs. Para mais informações, consulte [o Cofre chave Azure atrás de uma firewall](../../key-vault/general/access-behind-firewall.md).    


## <a name="group-policy-requirements"></a>Requisitos de Política de Grupo

A encriptação do disco Azure utiliza o protetor de teclas externo BitLocker para VMs do Windows. Para os VMs de domínio, não empurre nenhuma política de grupo que aplique protetores TPM. Para obter informações sobre a política do grupo para "Permitir bitLocker sem um TPM compatível", consulte [BitLocker Group Policy Reference](/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings#bkmk-unlockpol1).

A política bitLocker no domínio aderiu a máquinas virtuais com a política de grupo personalizado deve incluir a seguinte definição: [Configure o armazenamento do utilizador de informações de recuperação BitLocker - > Permitir a chave de recuperação de 256 bits](/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings). A encriptação do disco Azure falhará quando as definições de política de grupo personalizadas para o BitLocker forem incompatíveis. Nas máquinas que não tinham a definição correta da política, apliquem a nova política, forcem a nova política a atualizar (gpupdate.exe/force) e, em seguida, o reinício pode ser necessário.

A encriptação do disco azure falhará se a política do grupo de nível de domínio bloquear o algoritmo AES-CBC, que é usado pelo BitLocker.

## <a name="encryption-key-storage-requirements"></a>Requisitos de armazenamento de chaves de encriptação  

A encriptação do disco Azure requer um Cofre de Chave Azure para controlar e gerir chaves e segredos de encriptação de disco. O seu cofre chave e VMs devem residir na mesma região azure e subscrição.

Para mais detalhes, consulte [Criar e configurar um cofre chave para encriptação](disk-encryption-key-vault.md)de disco azure .

## <a name="terminology"></a>Terminologia
A tabela que se segue define alguns dos termos comuns utilizados na documentação de encriptação do disco Azure:

| Terminologia | Definição |
| --- | --- |
| Azure Key Vault | Key Vault é um serviço de gestão criptográfico e chave que é baseado em módulos de segurança de hardware validados (FIPS) federal Information Processing Standards (FIPS). Estes padrões ajudam a salvaguardar as suas chaves criptográficas e segredos sensíveis. Para mais informações, consulte a documentação do [Cofre de Chaves Azure](https://azure.microsoft.com/services/key-vault/) e [criar e configurar um cofre chave para encriptação](disk-encryption-key-vault.md)de disco azure . |
| CLI do Azure | [O Azure CLI](/cli/azure/install-azure-cli) está otimizado para gerir e administrar recursos Azure a partir da linha de comando.|
| BitLocker |[O BitLocker](https://technet.microsoft.com/library/hh831713.aspx) é uma tecnologia de encriptação de volume do Windows reconhecida pela indústria que é usada para permitir a encriptação do disco nos VMs do Windows. |
| Chave de encriptação (KEK) | A chave assimétrica (RSA 2048) que pode usar para proteger ou embrulhar o segredo. Pode fornecer uma chave protegida por hardware (HSM) ou uma chave protegida por software. Para mais informações, consulte a documentação do [Cofre de Chaves Azure](https://azure.microsoft.com/services/key-vault/) e [criar e configurar um cofre chave para encriptação](disk-encryption-key-vault.md)de disco azure . |
| Cmdlets do PowerShell | Para mais informações, consulte [os cmdlets Azure PowerShell](/powershell/azure/overview). |


## <a name="next-steps"></a>Passos seguintes

- [Quickstart - Criar e encriptar um VM Windows com o Azure CLI](disk-encryption-cli-quickstart.md)
- [Quickstart - Crie e criptografe um VM windows com A Powershell Azure](disk-encryption-powershell-quickstart.md)
- [Cenários do Azure Disk Encryption em VMs do Windows](disk-encryption-windows.md)
- [Encriptação do disco azure pré-requisitos script CLI](https://github.com/ejarvi/ade-cli-getting-started)
- [Encriptação de disco azure pré-requisitos PowerShell script](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
- [Criar e configurar um cofre de chaves para o Azure Disk Encryption](disk-encryption-key-vault.md)


