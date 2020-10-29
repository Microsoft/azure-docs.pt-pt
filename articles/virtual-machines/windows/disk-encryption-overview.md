---
title: Ativar encriptação do disco Azure para VMs windows
description: Este artigo fornece instruções para permitir a encriptação do disco do Microsoft Azure para VMs windows.
author: msmbaldwin
ms.service: virtual-machines-windows
ms.subservice: security
ms.topic: conceptual
ms.author: mbaldwin
ms.date: 10/05/2019
ms.custom: seodec18
ms.openlocfilehash: ea82724ec534aa896fa012332c10e28050caf825
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/29/2020
ms.locfileid: "92909391"
---
# <a name="azure-disk-encryption-for-windows-vms"></a>Encriptação do disco Azure para VMs windows 

A Azure Disk Encryption ajuda a proteger e a salvaguardar os seus dados para cumprir as obrigações de conformidade e segurança da sua organização. Utiliza a funcionalidade [Bitlocker](https://en.wikipedia.org/wiki/BitLocker) do Windows para fornecer encriptação de volume para o SISTEMA e discos de dados de máquinas virtuais Azure (VMs), e está integrado com [o Azure Key Vault](../../key-vault/index.yml) para o ajudar a controlar e gerir as chaves e segredos de encriptação do disco. 

Se utilizar [o Centro de Segurança Azure,](../../security-center/index.yml)é alertado se tiver VMs que não estejam encriptados. Os alertas mostram como Alta Severidade e a recomendação é encriptar estes VMs.

![Alerta de encriptação do disco do Azure Security Center](../media/disk-encryption/security-center-disk-encryption-fig1.png)

> [!WARNING]
> - Se já utilizou a Encriptação do Disco Azure com AZure AD para encriptar um VM, deve continuar a utilizar esta opção para encriptar o seu VM. Consulte [a encriptação do disco Azure com Azure AD (versão anterior)](disk-encryption-overview-aad.md) para obter mais detalhes. 
> - Certas recomendações podem aumentar o uso de dados, redes ou recursos computativos, resultando em custos adicionais de licença ou subscrição. Você deve ter uma subscrição ativa válida Azure para criar recursos em Azure nas regiões apoiadas.

Pode aprender os fundamentos da Encriptação do Disco Azure para windows em apenas alguns minutos com o [Create e encriptar um Windows VM com quickstart Azure CLI](disk-encryption-cli-quickstart.md) ou o [Create e encriptar um VM do Windows com quickstart Azure Powershell](disk-encryption-powershell-quickstart.md).

## <a name="supported-vms-and-operating-systems"></a>VMs e sistemas operativos suportados

### <a name="supported-vms"></a>VMs suportados

Os VMs do Windows estão disponíveis numa [gama de tamanhos.](../sizes-general.md) A Encriptação do Disco Azure não está disponível em [VMs básicos, série A,](https://azure.microsoft.com/pricing/details/virtual-machines/series/)ou em máquinas virtuais com menos de 2 GB de memória.

A Azure Disk Encryption também está disponível para VMs com armazenamento premium.

A encriptação do disco Azure não está disponível em [VMs da Geração 2](../generation-2.md#generation-1-vs-generation-2-capabilities). Para mais exceções, consulte [encriptação do disco Azure: cenários não suportados](disk-encryption-windows.md#unsupported-scenarios).

### <a name="supported-operating-systems"></a>Sistemas operativos suportados

- Cliente do Windows: Windows 8 e mais tarde.
- Windows Server: Windows Server 2008 R2 e mais tarde.  
 
> [!NOTE]
> O Windows Server 2008 R2 requer que o Quadro .NET 4.5 seja instalado para encriptação; instalá-lo a partir do Windows Update com a atualização opcional Microsoft .NET Framework 4.5.2 para sistemas baseados no Windows Server 2008 R2 x64[(KB2901983).](https://www.catalog.update.microsoft.com/Search.aspx?q=KB2901983)  
>  
> O Núcleo R2 do Windows Server 2012 e o Windows Server 2016 Core exigem que o componente bdehdcfg seja instalado no VM para encriptação.


## <a name="networking-requirements"></a>Requisitos de rede
Para ativar a encriptação do disco Azure, os VMs devem satisfazer os seguintes requisitos de configuração do ponto final da rede:
  - Para obter um token para ligar ao seu cofre chave, o Windows VM deve ser capaz de ligar a um ponto final do Azure Ative Directory, \[ login.microsoftonline.com \] .
  - Para escrever as chaves de encriptação do cofre da chave, o Windows VM deve ser capaz de ligar ao ponto final do cofre.
  - O Windows VM deve ser capaz de se ligar a um ponto final de armazenamento Azure que acolhe o repositório de extensão Azure e uma conta de armazenamento Azure que acolhe os ficheiros VHD.
  -  Se a sua política de segurança limitar o acesso dos VMs Azure à Internet, pode resolver o URI anterior e configurar uma regra específica para permitir a conectividade de saída com os IPs. Para obter mais informações, consulte [o Azure Key Vault atrás de uma firewall](../../key-vault/general/access-behind-firewall.md).    


## <a name="group-policy-requirements"></a>Requisitos de Política de Grupo

A encriptação do disco Azure utiliza o protetor de chave externo BitLocker para VMs windows. Para o domínio associado aos VMs, não pressione nenhuma política de grupo que aplique protetores TPM. Para obter informações sobre a política de grupo para "Permitir o BitLocker sem um TPM compatível", consulte [a Referência política do Grupo BitLocker](/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings#bkmk-unlockpol1).

A política bitLocker sobre máquinas virtuais de domínio com a política de grupo personalizado deve incluir a seguinte definição: Configurar o [armazenamento do utilizador de informações de recuperação bitLocker -> Permitir a chave de recuperação de 256 bits](/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings). A encriptação do disco Azure falhará quando as definições de política de grupo personalizadas para BitLocker são incompatíveis. Nas máquinas que não tenham a definição de política correta, aplique a nova política, force a nova política a atualizar (gpupdate.exe/força), e depois poderá ser necessário reiniciar.

A encriptação do disco Azure falhará se a política do grupo de nível de domínio bloquear o algoritmo AES-CBC, que é usado pelo BitLocker.

## <a name="encryption-key-storage-requirements"></a>Requisitos de armazenamento de chaves de encriptação  

A encriptação do disco Azure requer um Cofre de Chave Azure para controlar e gerir chaves e segredos de encriptação de discos. O seu cofre-chave e VMs devem residir na mesma região de Azure e subscrição.

Para mais detalhes, consulte [criar e configurar um cofre chave para encriptação do disco Azure](disk-encryption-key-vault.md).

## <a name="terminology"></a>Terminologia
A tabela a seguir define alguns dos termos comuns utilizados na documentação de encriptação do disco Azure:

| Terminologia | Definição |
| --- | --- |
| Azure Key Vault | Key Vault é um serviço de gestão criptográfico e chave que é baseado em módulos de segurança de hardware validados (FIPS) federais de processamento de informação. Estes padrões ajudam a salvaguardar as suas chaves criptográficas e segredos sensíveis. Para obter mais informações, consulte a documentação do [Cofre da Chave Azure](https://azure.microsoft.com/services/key-vault/) e [criando e configurando um cofre chave para encriptação do disco Azure](disk-encryption-key-vault.md). |
| CLI do Azure | [O CLI Azure](/cli/azure/install-azure-cli) está otimizado para gerir e administrar recursos Azure a partir da linha de comando.|
| BitLocker |[O BitLocker](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831713(v=ws.11)) é uma tecnologia de encriptação de volume do Windows reconhecida pela indústria que é usada para permitir a encriptação de discos em VMs do Windows. |
| Chave de encriptação (KEK) | A chave assimétrica (RSA 2048) que pode usar para proteger ou embrulhar o segredo. Pode fornecer uma chave protegida por hardware (HSM) ou uma chave protegida por software. Para obter mais informações, consulte a documentação do [Cofre da Chave Azure](https://azure.microsoft.com/services/key-vault/) e [criando e configurando um cofre chave para encriptação do disco Azure](disk-encryption-key-vault.md). |
| Cmdlets do PowerShell | Para obter mais informações, consulte [os cmdlets Azure PowerShell](/powershell/azure/). |


## <a name="next-steps"></a>Passos seguintes

- [Quickstart - Criar e encriptar um VM do Windows com O Azure CLI ](disk-encryption-cli-quickstart.md)
- [Quickstart - Criar e encriptar um VM do Windows com Azure Powershell](disk-encryption-powershell-quickstart.md)
- [Cenários do Azure Disk Encryption em VMs do Windows](disk-encryption-windows.md)
- [Encriptação de disco Azure pré-requisitos do script CLI](https://github.com/ejarvi/ade-cli-getting-started)
- [Encriptação de disco Azure pré-requisitos PowerShell script](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
- [Criar e configurar um cofre de chaves para o Azure Disk Encryption](disk-encryption-key-vault.md)