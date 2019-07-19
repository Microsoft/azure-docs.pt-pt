---
title: O que é Azure Disk Encryption?
description: Este artigo fornece uma visão geral de Azure Disk Encryption
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 07/17/2019
ms.custom: seodec18
ms.openlocfilehash: a67f19f0823827dad74e7aba15a92d696fbf580b
ms.sourcegitcommit: 770b060438122f090ab90d81e3ff2f023455213b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/17/2019
ms.locfileid: "68304273"
---
# <a name="azure-disk-encryption-overview"></a>Visão geral de Azure Disk Encryption

Azure Disk Encryption ajuda a proteger e proteger seus dados para atender aos compromissos de segurança e conformidade da organização. Ele usa o recurso [BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) do Windows e o recurso [DM-cript](https://en.wikipedia.org/wiki/Dm-crypt) do Linux para fornecer criptografia de volume para o sistema operacional e os discos de dados das VMs (máquinas virtuais) do Azure. Ele também é integrado com [Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) para ajudá-lo a controlar e gerenciar as chaves e os segredos de criptografia de disco e garante que todos os dados nos discos de VM sejam criptografados em repouso no armazenamento do Azure. Azure Disk Encryption para VMs Windows e Linux está em disponibilidade geral em todas as regiões públicas do Azure e regiões do Azure governamental para VMs padrão e VMs com o armazenamento Premium do Azure. 

Se utilizar o Centro de segurança do Azure, é alertado se tiver VMs que não estão encriptadas. Os alertas mostram como de gravidade alta e a recomendação é de encriptar estas VMs.

![Alerta de encriptação de disco do Centro de segurança do Azure](media/azure-security-disk-encryption/security-center-disk-encryption-fig1.png)

> [!NOTE]
> Algumas recomendações podem aumentar de dados, a rede, ou a utilização de recursos de computação e resultam em custos adicionais de licença ou subscrição.


## <a name="encryption-scenarios"></a>Cenários de encriptação

Com o Azure Disk Encryption, você pode atender aos requisitos de segurança e conformidade organizacionais protegendo suas VMs do Azure em repouso usando a tecnologia de criptografia padrão do setor. Você também pode configurar VMs para inicialização em chaves e políticas controladas pelo cliente (BYOK) e auditar o uso dessas chaves em seu cofre de chaves.

O Azure Disk Encryption dá suporte aos seguintes cenários de cliente:

* Habilitar e desabilitar a criptografia em novas VMs criadas com base nas imagens do Azure Gallery com suporte.
* Habilitando e desabilitando a criptografia em VMs existentes que são executadas no Azure.
* Habilitar e desabilitar a criptografia em novas VMs do Windows criadas com base em VHD e chaves de criptografia previamente criptografadas.
* Habilitando e desabilitando a criptografia em conjuntos de dimensionamento de máquinas virtuais do Windows.
* Habilitando e desabilitando a criptografia em unidades de dados para conjuntos de dimensionamento de máquinas virtuais do Linux.
* Habilitando e desabilitando a criptografia de VMs de disco gerenciado.
* Atualizando as configurações de criptografia de uma VM de armazenamento existente criptografada Premium e não Premium.
* Fazendo backup e restaurando VMs criptografadas.
* BYOE (traga sua própria criptografia) e cenários de traga sua própria chave (BYOK), em que os clientes usam suas próprias chaves de criptografia e as armazenam em um cofre de chaves do Azure.

Ele também dá suporte aos seguintes cenários para VMs quando eles estão habilitados no Microsoft Azure:

* Integração com o Cofre de chaves do Azure.
* As [VMs da camada Standard](https://azure.microsoft.com/pricing/details/virtual-machines/) que atendem ao [requisito mínimo de memória](azure-security-disk-encryption-prerequisites.md#supported-vm-sizes). 
* Habilitando a criptografia em VMs do Windows e Linux, disco gerenciado e VMs do conjunto de dimensionamento das imagens com suporte da galeria do Azure.
* Desabilitar a criptografia no sistema operacional e nas unidades de dados para VMs do Windows, VMs do conjunto de dimensionamento e VMs de disco gerenciado.
* Desabilitar a criptografia em unidades de dados para VMs do Linux, VMs do conjunto de dimensionamento e VMs de disco gerenciado.
* Habilitando a criptografia em VMs que executam o sistema operacional cliente Windows.
* Habilitando a criptografia em volumes com caminhos de montagem.
* Habilitando a criptografia em VMs Linux configuradas com a distribuição de disco (RAID) usando o mdadm.
* Habilitando a criptografia em VMs Linux que usam o LVM para discos de dados.
* Habilitando a criptografia no so da VM Linux e nos discos de dados.

   > [!NOTE]
   > Criptografia de unidade de sistema operacional para algumas distribuições do Linux não é suportada. Para obter mais informações, consulte [os Azure Disk Encryption sistemas operacionais com suporte: Linux](azure-security-disk-encryption-prerequisites.md#linux).
   
* Habilitando a criptografia em VMs configuradas com espaços de armazenamento do Windows a partir do Windows Server 2016. Espaços de Armazenamento Diretos (S2D) ainda não tem suporte.
* Backup e restauração de VMs criptografadas para KEK (chave de criptografia de chave) e cenários não KEK.

Azure Disk Encryption não funciona para os seguintes cenários, recursos e tecnologia:

* Criptografia de VM de camada básica ou VMs criadas por meio do método de criação de VM clássico.
* Desabilitar a criptografia em uma unidade do sistema operacional ou unidade de dados de uma VM do Linux quando a unidade do sistema operacional é criptografada.
* Criptografando a unidade do sistema operacional para conjuntos de dimensionamento de máquinas virtuais do Linux.
* Criptografia de VMs do Windows configuradas com sistemas RAID baseados em software.
* Criptografar imagens personalizadas em VMs do Linux.
* Integração com um sistema de gerenciamento de chaves local.
* Ficheiros do Azure (sistema de ficheiros partilhados).
* Sistema de ficheiros de rede (NFS).
* Volumes dinâmicos.

## <a name="encryption-features"></a>Recursos de criptografia

Ao habilitar e implantar Azure Disk Encryption para VMs do Azure, você pode configurar os seguintes recursos para serem habilitados:

* Criptografar o volume do sistema operacional para proteger o volume de inicialização em repouso no armazenamento.
* Criptografar volumes de dados para proteger os volumes de dados em repouso em seu armazenamento.
* Desabilitar a criptografia no sistema operacional e nas unidades de dados para VMs do Windows.
* Desabilitar a criptografia nas unidades de dados para VMs do Linux (somente quando a unidade do sistema operacional não está criptografada).
* Proteger as chaves de criptografia e os segredos em sua assinatura do Azure Key Vault.
* Relatar o status de criptografia da VM criptografada.
* Removendo as definições de configuração de criptografia de disco da VM.
* Fazer backup e restaurar as VMs criptografadas usando o serviço de backup do Azure.

O Azure Disk Encryption para VMs para Windows e Linux inclui:

* [A extensão de criptografia de disco para Windows](../virtual-machines/extensions/azure-disk-enc-windows.md).
* [A extensão de criptografia de disco para Linux](../virtual-machines/extensions/azure-disk-enc-linux.md).
* [Os cmdlets de criptografia de disco do PowerShell](/powershell/module/az.compute/set-azvmdiskencryptionextension?view=azps-2.2.0).
* [Os cmdlets de criptografia de disco CLI do Azure](/cli/azure/vm/encryption?view=azure-cli-latest).
* [Os modelos de criptografia de disco Azure Resource Manager](azure-security-disk-encryption-appendix.md#resource-manager-templates).

> [!NOTE]
> Não existe nenhum custo adicional para encriptar discos da VM com o Azure Disk Encryption. Standard [preços do Key Vault](https://azure.microsoft.com/pricing/details/key-vault/) aplica-se ao Cofre de chaves, que é utilizado para armazenar as chaves de encriptação. 

## <a name="encryption-workflow"></a>Fluxo de trabalho de encriptação

Para ativar a encriptação de disco para o Windows e VMs do Linux, siga os passos abaixo:

1. Optar por ativar a encriptação de disco através do modelo de encriptação de disco do Azure Resource Manager, cmdlets do PowerShell ou a CLI do Azure e especificar a configuração de encriptação.

   * Para o cenário VHD encriptados de cliente, carrega o VHD encriptado para sua conta de armazenamento e o material de chave de encriptação para o seu Cofre de chaves. Em seguida, forneça a configuração de criptografia para habilitar a criptografia em uma nova VM.
   * Para novas VMs criadas a partir de imagens da galeria com suporte e VMs existentes que já são executadas no Azure, forneça a configuração de criptografia para habilitar a criptografia na VM.

1. Conceda acesso à plataforma Azure para ler o material de chave de criptografia (chaves de criptografia BitLocker para sistemas Windows e frase secreta para Linux) do cofre de chaves para habilitar a criptografia na VM.

1. O Azure atualiza o modelo de serviço VM com a encriptação e a configuração de Cofre de chaves e configura a sua VM encriptada.

   ![Microsoft Antimalware no Azure](./media/azure-security-disk-encryption/disk-encryption-fig1.png)

## <a name="decryption-workflow"></a>Fluxo de trabalho de desencriptação
Para desabilitar a criptografia de disco para VMs, conclua as seguintes etapas de alto nível:

1. Opte por desabilitar a criptografia (descriptografia) em uma VM em execução no Azure e especifique a configuração de descriptografia. Pode desativar através do modelo de encriptação de disco do Azure Resource Manager, cmdlets do PowerShell ou a CLI do Azure.

   Esta etapa desabilita a criptografia do sistema operacional ou o volume de dados ou ambos na VM do Windows em execução. Conforme mencionado na secção anterior, a desativação da encriptação de disco de SO do Linux não é suportada. O passo de desencriptação só é permitido para unidades de dados em VMs do Linux, desde que o disco de SO não está encriptado.

1. O Azure atualiza o modelo de serviço de VM e a VM é marcada como descriptografada. O conteúdo da VM já não é encriptado em inatividade.

   > [!NOTE]
   > A operação de criptografia de Desativação não elimina o seu Cofre de chaves e o material de chave de encriptação (chaves de encriptação de disco BitLocker para sistemas Windows) ou a frase de acesso para Linux.
   >
   > A desativação da encriptação de disco de SO para Linux não é suportada. O passo de desencriptação só é permitido para unidades de dados em VMs do Linux.
   >
   > A desativação da encriptação de disco de dados para Linux não é suportada se a unidade do SO estiver encriptada.


## <a name="encryption-workflow-previous-release"></a>Fluxo de trabalho de encriptação (versão anterior)

A nova versão do Azure Disk Encryption elimina a necessidade de fornecer um parâmetro de aplicação do Azure Active Directory (Azure AD) para ativar a encriptação de disco da VM. Com a nova versão, já não tem de fornecer uma credencial do Azure AD durante o passo da criptografia de ativação. Todas as novas VMs tem de estar encriptadas sem os parâmetros da aplicação do Azure AD ao utilizar a nova versão. As VMs que já foram encriptadas com parâmetros de aplicação do Azure AD ainda são suportadas e devem continuar a ser mantido com a sintaxe do Azure AD. Para ativar a encriptação de disco para o Windows e VMs do Linux (versão anterior), siga os passos abaixo:

1. Escolha um cenário de encriptação dos cenários listados na [cenários de encriptação](#encryption-scenarios) secção.

1. Optar por ativar a encriptação de disco através do modelo de encriptação de disco do Azure Resource Manager, cmdlets do PowerShell ou a CLI do Azure e especificar a configuração de encriptação.

   * Para o cenário VHD encriptados de cliente, carrega o VHD encriptado para sua conta de armazenamento e o material de chave de encriptação para o seu Cofre de chaves. Em seguida, forneça a configuração de criptografia para habilitar a criptografia em uma nova VM.
   * Para novas VMs que são criadas a partir do Marketplace e VMs existentes que já são executadas no Azure, forneça a configuração de criptografia para habilitar a criptografia na VM.

1. Conceda acesso à plataforma Azure para ler o material de chave de criptografia (chaves de criptografia BitLocker para sistemas Windows e frase secreta para Linux) do cofre de chaves para habilitar a criptografia na VM.

1. Fornece a identidade da aplicação do Azure AD para escrever a chave de encriptação material para o seu Cofre de chaves. Esta etapa habilita a criptografia na VM para os cenários mencionados na etapa 2.

1. O Azure atualiza o modelo de serviço VM com a encriptação e a configuração de Cofre de chaves e configura a sua VM encriptada.


## <a name="terminology"></a>Terminologia
A tabela a seguir define alguns dos termos comuns usados na documentação de criptografia de disco do Azure:

| Terminologia | Definição |
| --- | --- |
| Azure AD | Uma [do Azure AD](https://azure.microsoft.com/documentation/services/active-directory/) conta é utilizada para autenticar, armazenar e obter segredos a partir de um cofre de chaves. |
| Azure Key Vault | Key Vault é um serviço de gestão chave criptográfica que tem com base no Federal Information Processing Standards (FIPS) módulos de segurança de hardware validado. Estas normas ajudam a salvaguardar as chaves criptográficas e segredos sensíveis. Para obter mais informações, consulte a [do Azure Key Vault](https://azure.microsoft.com/services/key-vault/) documentação. |
| BitLocker |O [BitLocker](https://technet.microsoft.com/library/hh831713.aspx) é uma tecnologia de criptografia de volume do Windows reconhecida pela indústria que é usada para habilitar a criptografia de disco em VMs do Windows. |
| BEK | Chaves de encriptação de disco BitLocker (BEK) são usadas para criptografar o volume de arranque do sistema operacional e os volumes de dados. BEKs são salvaguardadas num cofre de chaves como segredos. |
| CLI do Azure | [A CLI do Azure](/cli/azure/install-azure-cli) está otimizada para gerir e administrar recursos do Azure a partir da linha de comandos.|
| DM-Crypt |[DM-cript](https://gitlab.com/cryptsetup/cryptsetup/wikis/DMCrypt) é o subsistema de criptografia de disco transparente, baseado em Linux, usado para habilitar a criptografia de disco em VMs do Linux. |
| Chave de criptografia de chave (KEK) | A chave assimétrica (RSA 2048) que você pode usar para proteger ou encapsular o segredo. Pode fornecer um módulo de segurança de hardware (HSM)-chave ou chave protegida por software protegidos. Para obter mais informações, consulte a [do Azure Key Vault](https://azure.microsoft.com/services/key-vault/) documentação. |
| Cmdlets do PowerShell | Para obter mais informações, consulte [cmdlets do Azure PowerShell](/powershell/azure/overview). |

## <a name="next-steps"></a>Passos Seguintes

Para começar, consulte os [pré-requisitos de Azure Disk Encryption](azure-security-disk-encryption-prerequisites.md).

