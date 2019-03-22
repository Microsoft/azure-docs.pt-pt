---
title: FAQ - Azure Disk Encryption para IaaS VMs | Documentos da Microsoft
description: Este artigo fornece respostas para perguntas freqüentes sobre o Microsoft Azure disco encriptação para Windows e VMs de IaaS Linux.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18
ms.openlocfilehash: 9323db9faff162488f5f56c03ac976aa47bb6f97
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58339504"
---
# <a name="azure-disk-encryption-for-iaas-vms-faq"></a>Azure Disk Encryption para VMs de IaaS FAQ

Este artigo fornece respostas às perguntas mais frequentes (FAQ) sobre a encriptação de disco do Azure para Windows e VMs de IaaS Linux. Para obter mais informações sobre este serviço, consulte [encriptação de disco do Azure para Windows e VMs de IaaS Linux](azure-security-disk-encryption-overview.md).

## <a name="where-is-azure-disk-encryption-in-general-availability-ga"></a>Onde está o Azure Disk Encryption em disponibilidade geral (GA)?

Encriptação de disco para Windows e Linux VMs de IaaS do Azure está em disponibilidade geral em todas as regiões públicas do Azure.

## <a name="what-user-experiences-are-available-with-azure-disk-encryption"></a>Ocorre com o qual o utilizador que estão disponíveis com o Azure Disk Encryption?

Disponibilidade geral de encriptação de disco do Azure oferece suporte a modelos do Azure Resource Manager, do Azure PowerShell e CLI do Azure. As experiências de utilizador diferente dão-lhe flexibilidade. Tem três opções diferentes para ativar a encriptação de disco para as VMs de IaaS. Para obter mais informações sobre a experiência de utilizador e a orientação passo a passo disponível no Azure Disk Encryption, consulte [ativar o Azure disco encriptação para Windows](azure-security-disk-encryption-windows.md) e [ativar o Azure Disk Encryption para Linux](azure-security-disk-encryption-linux.md).

## <a name="how-much-does-azure-disk-encryption-cost"></a>Quanto custa o Azure Disk Encryption?

Não existe nenhum custo para encriptar discos da VM com o Azure Disk Encryption, mas existem encargos associados à utilização do Cofre de chaves do Azure. Para obter mais informações sobre os custos do Azure Key Vault, consulte a [preços do Key Vault](https://azure.microsoft.com/pricing/details/key-vault/) página.


## <a name="which-virtual-machine-tiers-does-azure-disk-encryption-support"></a>As camadas de máquina virtual suporta o Azure Disk Encryption?

O Azure Disk Encryption está disponível em VMs de escalão standard, incluindo [, D, DS, G, GS e F](https://azure.microsoft.com/pricing/details/virtual-machines/) série VMs de IaaS. Também está disponível para VMs com o armazenamento premium. Não está disponível no escalão básico VMs.

## <a name="bkmk_LinuxOSSupport"></a> Distribuições do Linux que suporta o Azure Disk Encryption?

O Azure Disk Encryption é suportado nas seguintes distribuições de servidor do Linux e versões:

| Distribuição do Linux | Versão | Tipo de volume suportado para a encriptação|
| --- | --- |--- |
| Ubuntu | 16.04| Disco de SO e dados |
| Ubuntu | 14.04.5</br>[com o Azure ajustado kernel atualizado para 4.15 ou posterior](azure-security-disk-encryption-tsg.md#bkmk_Ubuntu14) | Disco de SO e dados |
| RHEL | 7.6 | Disco de SO e dados * |
| RHEL | 7.5 | Disco de SO e dados * |
| RHEL | 7.4 | Disco de SO e dados * |
| RHEL | 7.3 | Disco de SO e dados * |
| RHEL | 7.2 | Disco de SO e dados * |
| RHEL | 6.8 | Disco de dados * |
| RHEL | 6.7 | Disco de dados * |
| CentOS | 7.5 | Disco de SO e dados |
| CentOS | 7.4 | Disco de SO e dados |
| CentOS | 7.3 | Disco de SO e dados |
| CentOS | 7.2N | Disco de SO e dados |
| CentOS | 6.8 | Disco de SO e dados |
| CentOS | 7.1 | Disco de dados |
| CentOS | 7.0 | Disco de dados |
| CentOS | 6.7 | Disco de dados |
| CentOS | 6.6 | Disco de dados |
| CentOS | 6.5 | Disco de dados |
| openSUSE | 42.3 | Disco de dados |
| SLES | 12-SP4 | Disco de dados |
| SLES | 12-SP3 | Disco de dados |

*__Nova implementação de ADE é suportada para RHEL SO e o disco de dados para imagens de pay as you go de RHEL7. ADE não é atualmente suportado para imagens RHEL traga-Your-Own-subscrição (BYOS). Consulte a [do Azure Disk Encryption para Linux](azure-security-disk-encryption-linux.md) artigo para obter mais informações.__

## <a name="how-can-i-start-using-azure-disk-encryption"></a>Como posso começar a utilizar o Azure Disk Encryption?

Para começar, leia os [descrição geral do Azure Disk Encryption](azure-security-disk-encryption-overview.md).

## <a name="can-i-encrypt-both-boot-and-data-volumes-with-azure-disk-encryption"></a>Pode criptografar volumes de arranque e dados com o Azure Disk Encryption?

Sim, pode criptografar volumes de arranque e de dados para VMs de IaaS de Linux e Windows. Para VMs do Windows, não é possível encriptar os dados sem primeiro criptografar o volume do sistema operacional. Para VMs do Linux, é possível criptografar o volume de dados sem ter de criptografar o volume do sistema operacional pela primeira vez. Depois de criptografia do volume do sistema operacional para Linux, desativar a encriptação num volume do sistema operacional para VMs de IaaS Linux não é suportada.

## <a name="can-i-encrypt-an-unmounted-volume-with-azure-disk-encryption"></a>Pode criptografar um volume desmontado com o Azure Disk Encryption?

Não, o Azure Disk Encryption encripta apenas volumes montados.

## <a name="how-do-i-rotate-secrets-or-encryption-keys"></a>Como rodar segredos ou chaves de encriptação?

Para rodar segredos, basta chamar o mesmo comando que utilizou originalmente para ativar a encriptação de disco, especificando um cofre de chaves diferentes. Rodar a chave de encriptação de chave, chame o mesmo comando que utilizou originalmente para ativar a encriptação de disco, especificando a encriptação de chave nova. 

## <a name="how-do-i-add-or-remove-a-key-encryption-key-if-i-didnt-originally-use-one"></a>Como adicionar ou remover uma chave de encriptação de chave, se eu originalmente não utilizar um?

Para adicionar uma chave de encriptação de chave, chame o comando de ativar novamente passando o parâmetro de chave de encriptação de chave. Para remover uma chave de encriptação de chave, chame o comando de ativar novamente sem o parâmetro de chave de encriptação de chave.

## <a name="does-azure-disk-encryption-allow-you-to-bring-your-own-key-byok"></a>O Azure Disk Encryption permite traga a sua própria chave (BYOK)?

Sim, pode fornecer suas próprias chaves de encriptação de chave. Estas chaves são salvaguardadas no Azure Key Vault, que é o armazenamento de chaves do Azure Disk Encryption. Para obter mais informações sobre as chaves de encriptação de chave oferecer suporte a cenários, consulte [pré-requisitos do Azure Disk Encryption](azure-security-disk-encryption-prerequisites.md).

## <a name="can-i-use-an-azure-created-key-encryption-key"></a>Pode utilizar uma chave de encriptação de chave criadas pelo Azure?

Sim, pode utilizar o Azure Key Vault para gerar uma chave de encriptação de chave para utilização de encriptação de disco do Azure. Estas chaves são salvaguardadas no Azure Key Vault, que é o armazenamento de chaves do Azure Disk Encryption. Para obter mais informações sobre a chave de encriptação de chave, consulte [pré-requisitos do Azure Disk Encryption](azure-security-disk-encryption-prerequisites.md).

## <a name="can-i-use-an-on-premises-key-management-service-or-hsm-to-safeguard-the-encryption-keys"></a>Posso utilizar um serviço de gestão de chaves no local ou o HSM para salvaguardar as chaves de encriptação?

Não é possível utilizar o serviço de gestão de chaves no local ou o HSM para salvaguardar as chaves de encriptação com o Azure Disk Encryption. Só pode utilizar o serviço Azure Key Vault para salvaguardar as chaves de encriptação. Para obter mais informações sobre os cenários de suporte de chave de encriptação de chave, consulte [pré-requisitos do Azure Disk Encryption](azure-security-disk-encryption-prerequisites.md).

## <a name="what-are-the-prerequisites-to-configure-azure-disk-encryption"></a>Quais são os pré-requisitos para configurar a encriptação de disco do Azure?

Existem pré-requisitos para a encriptação de disco do Azure. Consulte a [pré-requisitos do Azure Disk Encryption](azure-security-disk-encryption-prerequisites.md) artigo para criar um novo cofre de chaves ou configurar um cofre de chaves existente para o acesso de encriptação de disco ativar a encriptação e salvaguardar segredos e chaves. Para obter mais informações sobre os cenários de suporte de chave de encriptação de chave, consulte [descrição geral do Azure Disk Encryption](azure-security-disk-encryption-overview.md).

## <a name="what-are-the-prerequisites-to-configure-azure-disk-encryption-with-an-azure-ad-app-previous-release"></a>Quais são os pré-requisitos para configurar a encriptação de disco do Azure com uma aplicação do Azure AD (versão anterior)?

Existem pré-requisitos para a encriptação de disco do Azure. Consulte a [pré-requisitos do Azure Disk Encryption](azure-security-disk-encryption-prerequisites-aad.md) artigo para criar uma aplicação do Azure Active Directory, criar um novo cofre de chaves, ou configurar um cofre de chaves existente para o acesso de encriptação de disco ativar a encriptação e salvaguardar segredos e chaves. Para obter mais informações sobre os cenários de suporte de chave de encriptação de chave, consulte [descrição geral do Azure Disk Encryption](azure-security-disk-encryption-overview.md).

## <a name="is-azure-disk-encryption-using-an-azure-ad-app-previous-release-still-supported"></a>Azure Disk Encryption está a utilizar uma aplicação do Azure AD (versão anterior), continua a ser suportada?
Sim. Encriptação de disco a utilizar uma aplicação do Azure AD ainda é suportada. No entanto, quando novas VMs a encriptar é recomendado que utilize o novo método, em vez de encriptar com uma aplicação do Azure AD. 

## <a name="can-i-migrate-vms-that-were-encrypted-with-an-azure-ad-app-to-encryption-without-an-azure-ad-app"></a>Pode migrar as VMs que eram criptografadas com uma aplicação do Azure AD para encriptação sem uma aplicação do Azure AD?
  Atualmente, não um caminho de migração direta de máquinas que era criptografado com uma aplicação do Azure AD para encriptação sem uma aplicação do Azure AD. Além disso, não um caminho direto de criptografia sem uma aplicação do Azure AD para a encriptação com uma aplicação do AD. 

## <a name="what-version-of-azure-powershell-does-azure-disk-encryption-support"></a>Qual a versão do Azure PowerShell suporta o Azure Disk Encryption?

Utilize a versão mais recente do SDK do PowerShell do Azure para configurar o Azure Disk Encryption. Baixe a versão mais recente do [do Azure PowerShell](https://github.com/Azure/azure-powershell/releases). É o Azure Disk Encryption *não* suportados pelo SDK do Azure versão 1.1.0.

> [!NOTE]
> A extensão de pré-visualização de encriptação de disco do Linux do Azure foi preterida. Para obter detalhes, consulte [extensão de pré-visualização do Azure descontinuar disco encryption para VMs de IaaS Linux](https://blogs.msdn.microsoft.com/azuresecurity/2017/07/12/deprecating-azure-disk-encryption-preview-extension-for-linux-iaas-vms/).

## <a name="can-i-apply-azure-disk-encryption-on-my-custom-linux-image"></a>Pode aplicar encriptação de disco do Azure na minha imagem personalizada do Linux?

Não é possível aplicar o Azure Disk Encryption em sua imagem personalizada do Linux. São suportadas apenas as imagens de Linux de galeria para as distribuições suportadas descritas anteriormente. Imagens do Linux personalizadas não são atualmente suportadas.

## <a name="can-i-apply-updates-to-a-linux-red-hat-vm-that-uses-the-yum-update"></a>Pode aplicar atualizações para uma VM do Linux Red Hat que utiliza a atualização de yum?

Sim, pode efetuar uma atualização ou uma VM do Red Hat Linux de patches. Para obter mais informações, consulte [aplicar atualizações ao encriptados VM do Azure IaaS Red Hat, utilizando a atualização de yum](https://blogs.msdn.microsoft.com/azuresecurity/2017/07/13/applying-updates-to-a-encrypted-azure-iaas-red-hat-vm-using-yum-update/).

## <a name="what-is-the-recommended-azure-disk-encryption-workflow-for-linux"></a>O que é o fluxo de trabalho de encriptação de disco do Azure recomendado para o Linux?

O seguinte fluxo de trabalho é recomendado ter os melhores resultados no Linux:
* Começar a partir da imagem de galeria sem modificações de stock correspondente para o SO distro e a versão necessária
* Cópia de segurança de quaisquer unidades montadas que serão encriptadas.  Isto volta até permite recuperação se houver uma falha, por exemplo se a VM é reiniciada antes de encriptação foi concluída.
* Encriptar (pode demorar várias horas ou até mesmo dias dependendo do tamanho de quaisquer discos de dados anexados e de características da VM)
* Personalizar e adicione software à imagem, conforme necessário.

Se este fluxo de trabalho não for possível, contando [Storage Service Encryption](../storage/common/storage-service-encryption.md) (SSE) no armazenamento de plataforma camada da conta pode ser uma alternativa para encriptação de disco completa com dm-crypt.

## <a name="what-is-the-disk-bek-volume-or-mntazurebekdisk"></a>O que é o disco "Bek Volume" ou "/ mnt/azure_bek_disk"?

"Bek volume" para o Windows ou "/ mnt/azure_bek_disk" para Linux é um volume de dados local que armazena em segurança as chaves de encriptação para as VMs de IaaS do Azure encriptadas.
> [!NOTE]
> Não elimine ou edite qualquer conteúdo neste disco. Não desmonte o disco, uma vez que a presença de chaves de encriptação é necessária para quaisquer operações de encriptação na IaaS VM.


## <a name="what-encryption-method-does-azure-disk-encryption-use"></a>Que método de encriptação utilizar o Azure Disk Encryption?

No Windows, ADE utiliza o método de encriptação BitLocker AES256 (AES256WithDiffuser em versões anteriores ao Windows Server 2012). No Linux, ADE utiliza a predefinição de desencriptação de xts-aes-plain64 com uma chave mestra de volume de 256 bits.

## <a name="if-i-use-encryptformatall-and-specify-all-volume-types-will-it-erase-the-data-on-the-data-drives-that-we-already-encrypted"></a>Se eu utilizar EncryptFormatAll e especifique todos os tipos de volume, irá apagar os dados em unidades de dados que nós já de ser encriptados?
Não, os dados não ser apagados da unidades de dados que já são encriptadas com o Azure Disk Encryption. Semelhante a como EncryptFormatAll não voltar a encriptar a unidade do SO, ele não criptografa novamente a unidade de dados já encriptados. Para obter mais informações, consulte a [EncryptFormatAll critérios](azure-security-disk-encryption-linux.md#bkmk_EFACriteria).        

## <a name="is-xfs-filesystem-supported"></a>Sistema de ficheiros XFS é suportado?
Os volumes XFS são suportados para a encriptação de disco de dados apenas com o EncryptFormalAll. Isso reformatará o volume, apagar todos os dados anteriormente lá. Para obter mais informações, consulte a [EncryptFormatAll critérios](azure-security-disk-encryption-linux.md#bkmk_EFACriteria).

## <a name="can-i-backup-and-restore-an-encrypted-vm"></a>Pode criar cópias de segurança e restaurar uma VM encriptada? 

Cópia de segurança do Azure fornece um mecanismo de cópia de segurança e restaurar encriptada da VM na mesma subscrição e região.  Para obter instruções, consulte [fazer backup e restaurar máquinas virtuais encriptadas com o Azure Backup](https://docs.microsoft.com/en-us/azure/backup/backup-azure-vms-encryption).  Atualmente, o restauro de uma VM encriptada para uma região diferente não é suportado.  

## <a name="where-can-i-go-to-ask-questions-or-provide-feedback"></a>Onde posso ir para colocar questões ou comentários?

Pode fazer perguntas ou enviar comentários sobre o [fórum do Azure Disk Encryption](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureDiskEncryption).

## <a name="next-steps"></a>Passos Seguintes
Neste documento, ficou a saber mais sobre as perguntas mais frequentes relacionadas com o Azure Disk Encryption. Para obter mais informações sobre este serviço, consulte os artigos seguintes:

- [Descrição geral da encriptação de disco do Azure](azure-security-disk-encryption-overview.md)
- [Aplicar encriptação de disco no Centro de segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-apply-disk-encryption)
- [Encriptação de dados do Azure em repouso](https://docs.microsoft.com/azure/security/azure-security-encryption-atrest)
