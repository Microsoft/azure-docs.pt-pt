---
title: O que é o Azure Disk Encryption?
description: Este artigo fornece uma visão geral do Azure Disk Encryption
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 06/05/2019
ms.custom: seodec18
ms.openlocfilehash: 58c5c8321e505fe2c1c7d19c58fe0d031b75b3e4
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/20/2019
ms.locfileid: "67294869"
---
# <a name="azure-disk-encryption-overview"></a>Descrição geral da encriptação de disco do Azure

O Azure Disk Encryption ajuda a proteger e salvaguardar os seus dados para respeitar os compromissos de segurança e conformidade. Ele usa o [BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) recurso do Windows e o [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) funcionalidade do Linux para fornecer encriptação de volume para os discos de SO e dados de máquinas virtuais do Azure (VMs). Ele também está integrado [do Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) para o ajudar a controlar e gerir as chaves de encriptação de disco e segredos e garante que todos os dados nos discos de VM são encriptados em inatividade no armazenamento do Azure. Encriptação de disco para Windows e VMs do Linux do Azure está em disponibilidade geral em todas as regiões públicas do Azure e regiões do Azure Government para VMs padrão e as VMs com armazenamento Premium do Azure. 

Se utilizar o Centro de segurança do Azure, é alertado se tiver VMs que não estão encriptadas. Os alertas mostram como de gravidade alta e a recomendação é de encriptar estas VMs.

![Alerta de encriptação de disco do Centro de segurança do Azure](media/azure-security-disk-encryption/security-center-disk-encryption-fig1.png)

> [!NOTE]
> Algumas recomendações podem aumentar de dados, a rede, ou a utilização de recursos de computação e resultam em custos adicionais de licença ou subscrição.


## <a name="encryption-scenarios"></a>Cenários de encriptação

Com o Azure Disk Encryption, que pode abordar os requisitos de conformidade e segurança organizacionais, protegendo as VMs do Azure em descanso ao utilizar a tecnologia de encriptação de norma da indústria. Também pode configurar as VMs para efetuar o arranque em controlado pelo cliente chaves e políticas (BYOK) e auditar a utilização dessas chaves no seu Cofre de chaves.

O Azure Disk Encryption suporta os seguintes cenários de cliente:

* Ativar e desativar a encriptação em novas VMs criadas a partir de imagens de galeria do Azure suportadas.
* Ativar e desativar a encriptação em VMs existentes que são executadas no Azure.
* Ativar e desativar a encriptação em novas VMs do Windows criados a partir de VHD previamente encriptado e as chaves de encriptação.
* Ativar e desativar a encriptação em dimensionamento de máquinas virtuais do Windows define.
* Ativar e desativar a encriptação nos dados de unidades para conjuntos de dimensionamento de máquinas virtuais do Linux.
* Ativar e desativar a encriptação de disco gerido de VMs.
* A atualizar definições de encriptação de um existente Premium encriptado e não - VM do Premium Storage.
* Criar cópias de segurança e restaurar VMs encriptadas.
* Traga a sua própria encriptação (BYOE) e cenários bring your own chave (BYOK), no qual os clientes utilizam suas próprias chaves de encriptação e armazená-los num cofre de chave do Azure.

Também suporta os seguintes cenários para as VMs quando estão ativadas no Microsoft Azure:

* Integração com o Cofre de chaves do Azure.
* [Escalão Standard VMs](https://azure.microsoft.com/pricing/details/virtual-machines/) que cumpram os [requisito mínimo de memória](azure-security-disk-encryption-prerequisites.md#supported-vm-sizes). 
* Ativar a encriptação do Windows e VMs do Linux, disco gerido e dimensionar VMs do conjunto de imagens de galeria do Azure suportadas.
* A desativação da encriptação no SO e dados unidades para VMs do Windows, dimensionamento VMs do conjunto e geridas VMs de disco.
* Desativar encriptação em unidades de dados para VMs do Linux, dimensionamento VMs do conjunto e geridas VMs de disco.
* Ativar a encriptação em VMs que executam o sistema operacional cliente Windows.
* Ativar a encriptação em volumes com caminhos de montagem.
* Ativar a encriptação em VMs do Linux que estão configurados com disco repartição (RAID) utilizando mdadm.
* Ativar a encriptação em VMs do Linux utilizar LVM para discos de dados.
* Ativar a encriptação nos discos de dados e SO de VM do Linux.

   > [!NOTE]
   > Criptografia de unidade de sistema operacional para algumas distribuições do Linux não é suportada. Para obter mais informações, consulte o [sistemas operativos suportados do Azure Disk Encryption: Linux](azure-security-disk-encryption-prerequisites.md#linux).
   
* Ativar a encriptação em VMs que estão configurados com início de espaços de armazenamento do Windows no Windows Server 2016.
* Criar cópias de segurança e restauro de VMs encriptadas para a chave de encriptação de chaves (KEK) e cenários de não-KEK.

O Azure Disk Encryption não funciona para os seguintes cenários, recursos e tecnologia:

* Encriptar o escalão básico, VM ou VMs criadas com o método de criação de VM clássico.
* A desativação da encriptação numa unidade de sistema operacional ou unidade de dados de uma VM do Linux quando a unidade do SO for encriptada.
* Define a criptografia de unidade de SO para o dimensionamento de máquinas virtuais do Linux.
* Encriptar VMs do Windows configurada com sistemas RAID baseados em software.
* Encriptar imagens personalizadas em VMs do Linux.
* Integração com um sistema de gestão de chaves no local.
* Ficheiros do Azure (sistema de ficheiros partilhados).
* Sistema de ficheiros de rede (NFS).
* Volumes dinâmicos.

## <a name="encryption-features"></a>Recursos de criptografia

Quando ativa e implementa o Azure Disk Encryption para VMs do Azure, pode configurar as seguintes capacidades para ser ativada:

* Encriptar o volume do sistema operacional para proteger o volume de arranque em inatividade no seu armazenamento.
* Criptografar volumes de dados para proteger os volumes de dados Inativos no seu armazenamento.
* A desativação da encriptação nas unidades de SO e dados para as VMs do Windows.
* A desativação da encriptação nos dados de unidades para VMs do Linux (apenas quando a unidade do SO não está encriptada).
* Salvaguardar as chaves de encriptação e os segredos na sua subscrição do Azure Key Vault.
* Relatório de status de encriptação da VM encriptada.
* A remover as definições de configuração de encriptação de disco da VM.
* Backup e restauração de VMs encriptadas com o serviço de cópia de segurança do Azure.

Inclui o Azure Disk Encryption para VMs para Windows e Linux:

* [A extensão de encriptação de disco para o Windows](../virtual-machines/extensions/azure-disk-enc-windows.md).
* [A extensão de encriptação de disco para Linux](../virtual-machines/extensions/azure-disk-enc-linux.md).
* [Os cmdlets de encriptação de disco do PowerShell](/powershell/module/az.compute/set-azvmdiskencryptionextension?view=azps-2.2.0).
* [Os cmdlets de encriptação de disco de CLI do Azure](/cli/azure/vm/encryption?view=azure-cli-latest).
* [Os modelos de encriptação de disco do Azure Resource Manager](azure-security-disk-encryption-appendix.md#resource-manager-templates).

> [!NOTE]
> Não existe nenhum custo adicional para encriptar discos da VM com o Azure Disk Encryption. Standard [preços do Key Vault](https://azure.microsoft.com/pricing/details/key-vault/) aplica-se ao Cofre de chaves, que é utilizado para armazenar as chaves de encriptação. 

## <a name="encryption-workflow"></a>Fluxo de trabalho de encriptação

Para ativar a encriptação de disco para o Windows e VMs do Linux, siga os passos abaixo:

1. Optar por ativar a encriptação de disco através do modelo de encriptação de disco do Azure Resource Manager, cmdlets do PowerShell ou a CLI do Azure e especificar a configuração de encriptação.

   * Para o cenário VHD encriptados de cliente, carrega o VHD encriptado para sua conta de armazenamento e o material de chave de encriptação para o seu Cofre de chaves. Em seguida, indique a configuração de criptografia para ativar a encriptação numa nova VM.
   * Para novas VMs que são criadas a partir de imagens da Galeria suportadas e as VMs existentes que já executam no Azure, indique a configuração de criptografia para ativar a encriptação na VM.

1. Conceder acesso, a plataforma do Azure para ler o material de chave de encriptação (chaves de encriptação de disco BitLocker para sistemas Windows) e a frase de acesso para Linux a partir do seu Cofre de chaves para ativar a encriptação na VM.

1. O Azure atualiza o modelo de serviço VM com a encriptação e a configuração de Cofre de chaves e configura a sua VM encriptada.

   ![Microsoft Antimalware no Azure](./media/azure-security-disk-encryption/disk-encryption-fig1.png)

## <a name="decryption-workflow"></a>Fluxo de trabalho de desencriptação
Para desativar a encriptação de disco para VMs, conclua os seguintes passos de alto nível:

1. Optar por desativar a encriptação (desencriptação) numa VM em execução no Azure e especificar a configuração de desencriptação. Pode desativar através do modelo de encriptação de disco do Azure Resource Manager, cmdlets do PowerShell ou a CLI do Azure.

   Este passo desativa a encriptação de sistema operacional ou o volume de dados ou ambos na VM do Windows em execução. Conforme mencionado na secção anterior, a desativação da encriptação de disco de SO do Linux não é suportada. O passo de desencriptação só é permitido para unidades de dados em VMs do Linux, desde que o disco de SO não está encriptado.

1. Atualizações do Azure, o modelo de serviço VM e a VM está marcada como desencriptar. O conteúdo da VM já não é encriptado em inatividade.

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

   * Para o cenário VHD encriptados de cliente, carrega o VHD encriptado para sua conta de armazenamento e o material de chave de encriptação para o seu Cofre de chaves. Em seguida, indique a configuração de criptografia para ativar a encriptação numa nova VM.
   * Para novas VMs que são criadas a partir do Marketplace e as VMs existentes que já executam no Azure, indique a configuração de criptografia para ativar a encriptação na VM.

1. Conceder acesso, a plataforma do Azure para ler o material de chave de encriptação (chaves de encriptação de disco BitLocker para sistemas Windows) e a frase de acesso para Linux a partir do seu Cofre de chaves para ativar a encriptação na VM.

1. Fornece a identidade da aplicação do Azure AD para escrever a chave de encriptação material para o seu Cofre de chaves. Este passo ativa a encriptação na VM para os cenários mencionados no passo 2.

1. O Azure atualiza o modelo de serviço VM com a encriptação e a configuração de Cofre de chaves e configura a sua VM encriptada.


## <a name="terminology"></a>Terminologia
A tabela seguinte define alguns dos termos comuns utilizados na documentação de encriptação de disco do Azure:

| Terminologia | Definição |
| --- | --- |
| Azure AD | Uma [do Azure AD](https://azure.microsoft.com/documentation/services/active-directory/) conta é utilizada para autenticar, armazenar e obter segredos a partir de um cofre de chaves. |
| Azure Key Vault | Key Vault é um serviço de gestão chave criptográfica que tem com base no Federal Information Processing Standards (FIPS) módulos de segurança de hardware validado. Estas normas ajudam a salvaguardar as chaves criptográficas e segredos sensíveis. Para obter mais informações, consulte a [do Azure Key Vault](https://azure.microsoft.com/services/key-vault/) documentação. |
| BitLocker |[BitLocker](https://technet.microsoft.com/library/hh831713.aspx) é uma reconhecidas no setor Windows volume tecnologia de encriptação que é utilizada para ativar a encriptação de disco em VMs do Windows. |
| BEK | Chaves de encriptação de disco BitLocker (BEK) são usadas para criptografar o volume de arranque do sistema operacional e os volumes de dados. BEKs são salvaguardadas num cofre de chaves como segredos. |
| CLI do Azure | [A CLI do Azure](/cli/azure/install-azure-cli) está otimizada para gerir e administrar recursos do Azure a partir da linha de comandos.|
| DM-Crypt |[DM-Crypt](https://gitlab.com/cryptsetup/cryptsetup/wikis/DMCrypt) é o subsistema de encriptação de disco baseado em Linux, transparente, que é utilizado para ativar a encriptação de disco em VMs do Linux. |
| Chave de encriptação de chaves (KEK) | A chave assimétrica (RSA 2048) que pode utilizar para proteger ou encapsular o segredo. Pode fornecer um módulo de segurança de hardware (HSM)-chave ou chave protegida por software protegidos. Para obter mais informações, consulte a [do Azure Key Vault](https://azure.microsoft.com/services/key-vault/) documentação. |
| Cmdlets do PowerShell | Para obter mais informações, consulte [cmdlets do Azure PowerShell](/powershell/azure/overview). |

## <a name="next-steps"></a>Passos Seguintes

Para começar, consulte a [pré-requisitos do Azure Disk Encryption](azure-security-disk-encryption-prerequisites.md).

