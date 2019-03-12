---
title: Descrição geral - Azure Disk Encryption para IaaS VMs | Documentos da Microsoft
description: Este artigo fornece uma visão geral do Microsoft Azure Disk Encryption para VMs de IaaS.
author: mestew
ms.service: security
ms.topic: article
ms.author: mstewart
ms.date: 03/05/2019
ms.custom: seodec18
ms.openlocfilehash: e9b0653c8c42d207010b242d0d86985639560937
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57777376"
---
# <a name="azure-disk-encryption-for-iaas-vms"></a>Azure Disk Encryption para IaaS VMs

O Microsoft Azure é um compromisso de garantir que a sua privacidade de dados e soberania de dados. O Azure permite-lhe controlar os seus dados alojado no Azure através de uma variedade de tecnologias avançadas de encriptar, controlar e gerir chaves de encriptação e acesso de controlo e auditoria de dados. Esse controle fornece aos clientes Azure com a flexibilidade de escolher a solução mais adequada às suas necessidades de negócios. Este artigo apresenta a uma solução de tecnologia: "Encriptação de disco para Windows e Linux IaaS máquinas virtuais do azure (VMs)." Essa tecnologia ajuda a proteger e salvaguardar os seus dados para cumprir os seus compromissos de conformidade e segurança organizacionais. 

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]


## <a name="overview"></a>Descrição geral

O Azure Disk Encryption é um recurso que lhe permite criptografar seus discos de VM de IaaS de Linux e Windows. Encriptação de disco tira partido da norma da indústria [BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) recurso do Windows e o [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) funcionalidade do Linux para fornecer encriptação de volume para os discos de SO e dados. A solução está integrada [do Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) para o ajudar a controlar e gerir as chaves de encriptação de disco e segredos. A solução também garante que todos os dados nos discos de VM são encriptados em inatividade no armazenamento do Azure.

Encriptação de disco para Windows e VMs de IaaS Linux está em disponibilidade geral em todas as regiões públicas do Azure e regiões do Azure Government para VMs padrão e as VMs com armazenamento Premium do Azure. Quando aplica a solução de gestão de encriptação de disco, pode satisfazer as necessidades de negócio seguintes:

* VMs de IaaS são protegidas em repouso usando a tecnologia de encriptação de norma da indústria para abordar os requisitos de segurança e conformidade organizacionais.
* Arranque de VMs de IaaS em controlado pelo cliente chaves e políticas. É possível auditar a utilização das mesmas no seu Cofre de chaves.

Se utilizar o Centro de segurança do Azure, é alertado se tiver VMs que não estão encriptadas. Os alertas mostram como de gravidade alta e a recomendação é de encriptar estas VMs.

![Alerta de encriptação de disco do Centro de segurança do Azure](media/azure-security-disk-encryption/security-center-disk-encryption-fig1.png)

> [!NOTE]
> Algumas recomendações podem aumentar de dados, a rede, ou a utilização de recursos de computação e resultam em custos adicionais de licença ou subscrição.


## <a name="encryption-scenarios"></a>Cenários de encriptação

A solução de encriptação de disco suporta os seguintes cenários de cliente:

* Ative a encriptação em novas VMs de IaaS do Windows criados a partir de encriptadas VHD e chaves de encriptação.
* Ative a encriptação em novas VMs de IaaS, criado a partir de imagens de galeria do Azure suportadas.
* Ative a encriptação em VMs de IaaS existentes que executam no Azure.
* Ative a encriptação em conjuntos de dimensionamento de máquinas virtuais do Windows.
* Ative a encriptação em unidades de dados para conjuntos de dimensionamento de máquinas virtuais do Linux.
* Desative a encriptação em VMs de IaaS do Windows.
* Desative a encriptação em unidades de dados para as VMs de IaaS do Linux.
* Desative a encriptação em conjuntos de dimensionamento de máquinas virtuais do Windows.
* Desative a encriptação em unidades de dados para conjuntos de dimensionamento de máquinas virtuais do Linux.
* Ative a encriptação de disco gerido de VMs.
* Atualize definições de encriptação de um existente Premium encriptado e não - VM do Premium Storage.
* Criar cópias de segurança e restauro de VMs encriptadas.

A solução suporta os seguintes cenários para IaaS VMs quando estão ativadas no Microsoft Azure:

* Integração com o Cofre de chaves do Azure.
* Escalão Standard VMs: [A, D, DS, G, GS, F e assim por diante, VMs de IaaS de série](https://azure.microsoft.com/pricing/details/virtual-machines/). [VMs do Linux](azure-security-disk-encryption-faq.md#bkmk_LinuxOSSupport) dentro estas camadas têm de cumprir o requisito de memória mínima de 7 GB.
* Ativar a encriptação no Windows e VMs do Linux IaaS, disco gerido e dimensionamento de VMs do conjunto de imagens de galeria do Azure suportadas.
* Desativar a encriptação em unidades de SO e dados para VMs de IaaS do Windows, dimensionamento VMs do conjunto e gerido de VMs de disco.
* Desativar a encriptação em unidades de dados para VMs de IaaS Linux, dimensionamento VMs do conjunto e gerido de VMs de disco.
* Ative a encriptação em VMs de IaaS que executam o sistema operacional cliente Windows.
* Ative a encriptação em volumes com caminhos de montagem.
* Ative a encriptação em VMs do Linux que estão configurados com disco repartição (RAID) utilizando mdadm.
* Ative a encriptação em VMs do Linux utilizar LVM para discos de dados.
* Ative a encriptação nos discos de dados e SO de VM do Linux.

   > [!NOTE]
   > Criptografia de unidade de sistema operacional para algumas distribuições do Linux não é suportada. Para obter mais informações, consulte a [FAQ de encriptação de disco do Azure](azure-security-disk-encryption-faq.md#bkmk_LinuxOSSupport) artigo.
   
* Ative a encriptação em VMs que estão configurados com início de espaços de armazenamento do Windows no Windows Server 2016.
* Atualize definições de encriptação para uma Premium encriptado existente e não - VM do Premium Storage.
* Criar cópias de segurança e restauro de VMs encriptadas para a chave de encriptação de chaves (KEK) e cenários de não-KEK.
* Todos os públicos do Azure e o Azure Government regiões são suportadas.

A solução não suporta os seguintes cenários, recursos e tecnologia:

* VMs de IaaS de escalão básico.
* Desative a encriptação numa unidade do sistema operacional para VMs de IaaS Linux.
* Desative a encriptação numa unidade de dados quando a unidade do SO está encriptada para VMs de IaaS Linux.
* VMs de IaaS que são criadas com o método de criação de VM clássico.
* Ative a encriptação de imagens personalizadas do cliente em VMs do Linux IaaS.
* Integração com o seu sistema de gestão de chaves no local.
* Ficheiros do Azure (sistema de ficheiros partilhados).
* Sistema de ficheiros de rede (NFS).
* Volumes dinâmicos.
* VMs do Windows que estão configurados com sistemas RAID baseados em software.

## <a name="encryption-features"></a>Recursos de criptografia

Quando ativar e implementar a encriptação de disco para VMs IaaS do Azure, as seguintes funcionalidades estão ativadas consoante a configuração fornecida:

* Encriptação do volume de sistema operacional para proteger o volume de arranque em inatividade no seu armazenamento.
* Encriptação de volumes de dados para proteger os volumes de dados Inativos no seu armazenamento.
* Desative a encriptação nas unidades de SO e dados para VMs de IaaS do Windows.
* Desative a encriptação em unidades de dados para VMs de IaaS Linux (apenas quando a unidade do SO não está encriptada).
* Salvaguarde as chaves de encriptação e os segredos na sua subscrição do Azure Key Vault.
* Comunicar o estado de encriptação da VM de IaaS encriptada.
* Remova as definições de configuração de encriptação de disco da IaaS VM.
* Criar cópias de segurança e restaurar as VMs encriptadas com o serviço de cópia de segurança do Azure.

Inclui o Azure Disk Encryption para VMS de IaaS para a solução do Windows e Linux:

* A extensão de encriptação de disco para o Windows.
* A extensão de encriptação de disco para Linux.
* Os cmdlets de encriptação de disco do PowerShell.
* Os cmdlets de encriptação de disco de CLI do Azure.
* Os modelos de encriptação de disco do Azure Resource Manager.

A solução Azure Disk Encryption é suportada em VMs de IaaS que executam o Windows ou o SO Linux. Para obter mais informações sobre os sistemas operativos suportados, consulte a [pré-requisitos](azure-security-disk-encryption-prerequisites.md) artigo.

> [!NOTE]
> Não existe nenhum custo adicional para encriptar discos da VM com o Azure Disk Encryption. Standard [preços do Key Vault](https://azure.microsoft.com/pricing/details/key-vault/) aplica-se ao Cofre de chaves, que é utilizado para armazenar as chaves de encriptação. 


## <a name="encryption-workflow"></a>Fluxo de trabalho de encriptação

Para ativar a encriptação de disco para o Windows e VMs do Linux, siga os passos abaixo:

1. Escolha um cenário de encriptação dos cenários listados na [cenários de encriptação](#encryption-scenarios) secção.

1. Optar por ativar a encriptação de disco através do modelo de encriptação de disco do Azure Resource Manager, cmdlets do PowerShell ou a CLI do Azure e especificar a configuração de encriptação.

   * Para o cenário VHD encriptados de cliente, carrega o VHD encriptado para sua conta de armazenamento e o material de chave de encriptação para o seu Cofre de chaves. Em seguida, indique a configuração de criptografia para ativar a encriptação numa nova VM de IaaS.
   * Para novas VMs que são criadas a partir do Marketplace e as VMs existentes que já executam no Azure, indique a configuração de criptografia para ativar a encriptação na IaaS VM.

1. Conceder acesso, a plataforma do Azure para ler o material de chave de encriptação (chaves de encriptação de disco BitLocker para sistemas Windows) e a frase de acesso para Linux a partir do seu Cofre de chaves para ativar a encriptação na IaaS VM.

1. O Azure atualiza o modelo de serviço VM com a encriptação e a configuração de Cofre de chaves e configura a sua VM encriptada.

   ![Microsoft Antimalware no Azure](./media/azure-security-disk-encryption/disk-encryption-fig1.png)

## <a name="decryption-workflow"></a>Fluxo de trabalho de desencriptação
Para desativar a encriptação de disco para IaaS VMs, conclua os seguintes passos de alto nível:

1. Optar por desativar a encriptação (desencriptação) numa VM de IaaS em execução no Azure e especificar a configuração de desencriptação. Pode desativar através do modelo de encriptação de disco do Azure Resource Manager, cmdlets do PowerShell ou a CLI do Azure.

   Este passo desativa a encriptação de sistema operacional ou o volume de dados ou ambos na VM de IaaS do Windows em execução. Conforme mencionado na secção anterior, a desativação da encriptação de disco de SO do Linux não é suportada. O passo de desencriptação só é permitido para unidades de dados em VMs do Linux, desde que o disco de SO não está encriptado.

1. Atualizações do Azure, o modelo de serviço VM e a VM de IaaS é marcado como desencriptar. O conteúdo da VM já não é encriptado em inatividade.

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

   * Para o cenário VHD encriptados de cliente, carrega o VHD encriptado para sua conta de armazenamento e o material de chave de encriptação para o seu Cofre de chaves. Em seguida, indique a configuração de criptografia para ativar a encriptação numa nova VM de IaaS.
   * Para novas VMs que são criadas a partir do Marketplace e as VMs existentes que já executam no Azure, indique a configuração de criptografia para ativar a encriptação na IaaS VM.

1. Conceder acesso, a plataforma do Azure para ler o material de chave de encriptação (chaves de encriptação de disco BitLocker para sistemas Windows) e a frase de acesso para Linux a partir do seu Cofre de chaves para ativar a encriptação na IaaS VM.

1. Fornece a identidade da aplicação do Azure AD para escrever a chave de encriptação material para o seu Cofre de chaves. Este passo ativa a encriptação na VM IaaS para os cenários mencionados no passo 2.

1. O Azure atualiza o modelo de serviço VM com a encriptação e a configuração de Cofre de chaves e configura a sua VM encriptada.


## <a name="terminology"></a>Terminologia
A tabela seguinte define alguns dos termos comuns utilizados nessa tecnologia:

| Terminologia | Definição |
| --- | --- |
| Azure AD | Uma [do Azure AD](https://azure.microsoft.com/documentation/services/active-directory/) conta é utilizada para autenticar, armazenar e obter segredos a partir de um cofre de chaves. |
| Azure Key Vault | Key Vault é um serviço de gestão chave criptográfica que tem com base no Federal Information Processing Standards (FIPS) módulos de segurança de hardware validado. Estas normas ajudam a salvaguardar as chaves criptográficas e segredos sensíveis. Para obter mais informações, consulte a [do Azure Key Vault](https://azure.microsoft.com/services/key-vault/) documentação. |
| BitLocker |[BitLocker](https://technet.microsoft.com/library/hh831713.aspx) é uma reconhecidas no setor Windows volume tecnologia de encriptação que é utilizada para ativar a encriptação de disco em VMs de IaaS do Windows. |
| BEK | Chaves de encriptação de disco BitLocker (BEK) são usadas para criptografar o volume de arranque do sistema operacional e os volumes de dados. BEKs são salvaguardadas num cofre de chaves como segredos. |
| CLI do Azure | [A CLI do Azure](/cli/azure/install-azure-cli) está otimizada para gerir e administrar recursos do Azure a partir da linha de comandos.|
| DM-Crypt |[DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) é o subsistema de encriptação de disco baseado em Linux, transparente, que é utilizado para ativar a encriptação de disco em VMs do Linux IaaS. |
| KEK | Chave de encriptação de chaves (KEK) é a chave assimétrica (RSA 2048) que pode utilizar para proteger ou encapsular o segredo. Pode fornecer um módulo de segurança de hardware (HSM)-chave ou chave protegida por software protegidos. Para obter mais informações, consulte a [do Azure Key Vault](https://azure.microsoft.com/services/key-vault/) documentação. |
| Cmdlets do PowerShell | Para obter mais informações, consulte [cmdlets do Azure PowerShell](/powershell/azure/overview). |

## <a name="next-steps"></a>Passos Seguintes
> [!div class="nextstepaction"]
> [Pré-requisitos do Azure Disk Encryption](azure-security-disk-encryption-prerequisites.md)
