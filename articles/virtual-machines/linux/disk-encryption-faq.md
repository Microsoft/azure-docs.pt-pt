---
title: FAQ - Encriptação de disco azure para VMs Linux
description: Este artigo fornece respostas a perguntas frequentes sobre encriptação do disco Microsoft Azure para VMs Linux IaaS.
author: msmbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: article
ms.author: mbaldwin
ms.date: 06/05/2019
ms.custom: seodec18
ms.openlocfilehash: b285daa465c8d069b359e94c9203c1ffbea24c06
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2020
ms.locfileid: "78970674"
---
# <a name="azure-disk-encryption-for-iaas-vms-faq"></a>Encriptação de disco azure para IaaS VMs FAQ

Este artigo fornece respostas a perguntas frequentes (FAQ) sobre encriptação de disco azure para VMs Linux. Para mais informações sobre este serviço, consulte a visão geral da [Encriptação do Disco Azure](disk-encryption-overview.md).

## <a name="where-is-azure-disk-encryption-in-general-availability-ga"></a>Onde está o Azure Disk Encryption em disponibilidade geral (GA)?

A encriptação do disco azure para VMs Linux está em disponibilidade geral em todas as regiões públicas do Azure.

## <a name="what-user-experiences-are-available-with-azure-disk-encryption"></a>Ocorre com o qual o utilizador que estão disponíveis com o Azure Disk Encryption?

Disponibilidade geral de encriptação de disco do Azure oferece suporte a modelos do Azure Resource Manager, do Azure PowerShell e CLI do Azure. As experiências de utilizador diferente dão-lhe flexibilidade. Tem três opções diferentes para permitir a encriptação do disco para os seus VMs. Para obter mais informações sobre a experiência do utilizador e orientação passo a passo disponível na Encriptação do Disco Azure, consulte os cenários de encriptação do [Disco Azure para o Linux](disk-encryption-linux.md).

## <a name="how-much-does-azure-disk-encryption-cost"></a>Quanto custa o Azure Disk Encryption?

Não há nenhuma acusação para encriptar discos VM com encriptação de disco azure, mas há cargas associadas com o uso do Cofre de Chaves Azure. Para obter mais informações sobre os custos do Cofre de Chaves Do Azure, consulte a página de preços do [Cofre chave.](https://azure.microsoft.com/pricing/details/key-vault/)

## <a name="how-can-i-start-using-azure-disk-encryption"></a>Como posso começar a utilizar o Azure Disk Encryption?

Para começar, leia a visão geral da [Encriptação do Disco Azure.](disk-encryption-overview.md)

## <a name="what-vm-sizes-and-operating-systems-support-azure-disk-encryption"></a>Que tamanhos vm e sistemas operativos suportam encriptação de disco azure?

O artigo de encriptação de [disco azure](disk-encryption-overview.md) lista os [tamanhos VM](disk-encryption-overview.md#supported-vm-sizes) e [sistemas operativos VM](disk-encryption-overview.md#supported-operating-systems) que suportam a encriptação do disco Azure.

## <a name="can-i-encrypt-both-boot-and-data-volumes-with-azure-disk-encryption"></a>Pode criptografar volumes de arranque e dados com o Azure Disk Encryption?

Sim, pode encriptar tanto os volumes de arranque como os volumes de dados, ou pode encriptar o volume de dados sem ter de encriptar primeiro o volume de SO. 

Depois de ter encriptado o volume de S, a encriptação incapacitante no volume de SO não é suportada. Para os VMs Linux num conjunto de escala, apenas o volume de dados pode ser encriptado.

## <a name="can-i-encrypt-an-unmounted-volume-with-azure-disk-encryption"></a>Posso encriptar um volume não montado com encriptação de disco azure?

Não, a encriptação do disco Azure apenas encripta volumes montados.

## <a name="how-do-i-rotate-secrets-or-encryption-keys"></a>Como posso rodar segredos ou chaves de encriptação?

Para rodar segredos, basta ligar para o mesmo comando que usou originalmente para ativar a encriptação do disco, especificando um Cofre chave diferente. Para rodar a chave de encriptação da chave, ligue para o mesmo comando que usou originalmente para ativar a encriptação do disco, especificando a encriptação da nova chave. 

>[!WARNING]
> - Se já utilizou anteriormente a [Encriptação do Disco Azure com a aplicação Azure AD,](disk-encryption-linux-aad.md) especificando as credenciais de AD Azure para encriptar este VM, terá de continuar a utilizar esta opção para encriptar o seu VM. Não é possível utilizar a Encriptação do Disco Azure neste VM encriptado, uma vez que este não é um cenário suportado, o que significa que a mudança da aplicação AAD para este VM encriptado ainda não é suportada.

## <a name="how-do-i-add-or-remove-a-key-encryption-key-if-i-didnt-originally-use-one"></a>Como adiciono ou remove uma chave de encriptação se não usei originalmente uma?

Para adicionar uma chave de encriptação, ligue novamente para o comando de ativação, passando o parâmetro da chave de encriptação da chave. Para remover uma chave de encriptação, volte a ligar para o comando de ativação sem o parâmetro da chave de encriptação.

## <a name="does-azure-disk-encryption-allow-you-to-bring-your-own-key-byok"></a>O Azure Disk Encryption permite traga a sua própria chave (BYOK)?

Sim, pode fornecer suas próprias chaves de encriptação de chave. Estas chaves são salvaguardadas no Azure Key Vault, que é o armazenamento de chaves do Azure Disk Encryption. Para obter mais informações sobre os cenários de suporte das teclas de encriptação, consulte [Criar e configurar um cofre chave para encriptação](disk-encryption-key-vault.md)de disco azure .

## <a name="can-i-use-an-azure-created-key-encryption-key"></a>Pode utilizar uma chave de encriptação de chave criadas pelo Azure?

Sim, pode utilizar o Azure Key Vault para gerar uma chave de encriptação de chave para utilização de encriptação de disco do Azure. Estas chaves são salvaguardadas no Azure Key Vault, que é o armazenamento de chaves do Azure Disk Encryption. Para obter mais informações sobre a chave de encriptação, consulte [Criar e configurar um cofre chave para encriptação](disk-encryption-key-vault.md)de disco azure .

## <a name="can-i-use-an-on-premises-key-management-service-or-hsm-to-safeguard-the-encryption-keys"></a>Posso utilizar um serviço de gestão de chaves no local ou o HSM para salvaguardar as chaves de encriptação?

Não é possível utilizar o serviço de gestão de chaves no local ou o HSM para salvaguardar as chaves de encriptação com o Azure Disk Encryption. Só pode utilizar o serviço Azure Key Vault para salvaguardar as chaves de encriptação. Para obter mais informações sobre os cenários de suporte da chave de encriptação, consulte [Criar e configurar um cofre chave para encriptação](disk-encryption-key-vault.md)de disco azure .

## <a name="what-are-the-prerequisites-to-configure-azure-disk-encryption"></a>Quais são os pré-requisitos para configurar a encriptação de disco do Azure?

Existem pré-requisitos para a encriptação de disco do Azure. Consulte a [Criação e configuração](disk-encryption-key-vault.md) de um cofre chave para o artigo de encriptação do disco Azure para criar um novo cofre de chaves, ou configurar um cofre chave existente para acesso de encriptação de disco para permitir encriptação, e salvaguardar segredos e chaves. Para obter mais informações sobre os cenários de suporte da chave de encriptação, consulte [Criar e configurar um cofre chave para encriptação](disk-encryption-key-vault.md)de disco azure .

## <a name="what-are-the-prerequisites-to-configure-azure-disk-encryption-with-an-azure-ad-app-previous-release"></a>Quais são os pré-requisitos para configurar a encriptação de disco do Azure com uma aplicação do Azure AD (versão anterior)?

Existem pré-requisitos para a encriptação de disco do Azure. Consulte a Encriptação do Disco Azure com conteúdo [da AD Azure](disk-encryption-linux-aad.md) para criar uma aplicação Azure Ative Directory, criar um novo cofre chave ou criar um cofre chave existente para acesso à encriptação de discos para permitir encriptação e salvaguardar segredos e chaves. Para obter mais informações sobre os cenários de suporte da chave de encriptação, consulte [Criar e configurar um cofre chave para encriptação de disco azure com AD Azure](disk-encryption-key-vault-aad.md).

## <a name="is-azure-disk-encryption-using-an-azure-ad-app-previous-release-still-supported"></a>Azure Disk Encryption está a utilizar uma aplicação do Azure AD (versão anterior), continua a ser suportada?
Sim. Encriptação de disco a utilizar uma aplicação do Azure AD ainda é suportada. No entanto, quando novas VMs a encriptar é recomendado que utilize o novo método, em vez de encriptar com uma aplicação do Azure AD. 

## <a name="can-i-migrate-vms-that-were-encrypted-with-an-azure-ad-app-to-encryption-without-an-azure-ad-app"></a>Pode migrar as VMs que eram criptografadas com uma aplicação do Azure AD para encriptação sem uma aplicação do Azure AD?
  Atualmente, não um caminho de migração direta de máquinas que era criptografado com uma aplicação do Azure AD para encriptação sem uma aplicação do Azure AD. Além disso, não um caminho direto de criptografia sem uma aplicação do Azure AD para a encriptação com uma aplicação do AD. 

## <a name="what-version-of-azure-powershell-does-azure-disk-encryption-support"></a>Qual a versão do Azure PowerShell suporta o Azure Disk Encryption?

Utilize a versão mais recente do SDK do PowerShell do Azure para configurar o Azure Disk Encryption. Descarregue a versão mais recente do [Azure PowerShell.](https://github.com/Azure/azure-powershell/releases) A encriptação do disco Azure *não* é suportada pela versão 1.1.0 do Azure SDK.

> [!NOTE]
> A extensão de pré-visualização de encriptação do disco Linux Azure "Microsoft.OSTCExtension.AzureDiskCryptonForLinux" é depreciada. Esta extensão foi publicada para a versão de pré-visualização de encriptação do disco Azure. Não deve utilizar a versão de pré-visualização da extensão nos seus testes ou implantação de produção.

> Para cenários de implementação como o Azure Resource Manager (ARM), onde é necessário implementar a extensão de encriptação do disco Azure para o Linux VM para permitir a encriptação no seu VM Linux IaaS, deve utilizar a extensão suportada pela produção de encriptação do disco Azure " Microsoft.Azure.Security.AzureDiskCryptonForLinux".

## <a name="can-i-apply-azure-disk-encryption-on-my-custom-linux-image"></a>Pode aplicar encriptação de disco do Azure na minha imagem personalizada do Linux?

Não é possível aplicar o Azure Disk Encryption em sua imagem personalizada do Linux. São suportadas apenas as imagens de Linux de galeria para as distribuições suportadas descritas anteriormente. Imagens do Linux personalizadas não são atualmente suportadas.

## <a name="can-i-apply-updates-to-a-linux-red-hat-vm-that-uses-the-yum-update"></a>Pode aplicar atualizações para uma VM do Linux Red Hat que utiliza a atualização de yum?

Sim, pode sacar uma atualização de yum num VM De Chapéu Vermelho.  Para mais informações, consulte a [Encriptação do Disco Azure numa rede isolada](disk-encryption-isolated-network.md).

## <a name="what-is-the-recommended-azure-disk-encryption-workflow-for-linux"></a>O que é o fluxo de trabalho de encriptação de disco do Azure recomendado para o Linux?

O seguinte fluxo de trabalho é recomendado ter os melhores resultados no Linux:
* Começar a partir da imagem de galeria sem modificações de stock correspondente para o SO distro e a versão necessária
* Cópia de segurança de quaisquer unidades montadas que serão encriptadas.  Isto volta até permite recuperação se houver uma falha, por exemplo se a VM é reiniciada antes de encriptação foi concluída.
* Encriptar (pode demorar várias horas ou até mesmo dias dependendo do tamanho de quaisquer discos de dados anexados e de características da VM)
* Personalizar e adicione software à imagem, conforme necessário.

Se este fluxo de trabalho não for possível, confiar na [Encriptação](../../storage/common/storage-service-encryption.md) do Serviço de Armazenamento (SSE) na camada de conta de armazenamento da plataforma pode ser uma alternativa à encriptação completa do disco usando dm-criptografia.

## <a name="what-is-the-disk-bek-volume-or-mntazure_bek_disk"></a>O que é o disco "Bek Volume" ou "/ mnt/azure_bek_disk"?

O "volume Bek" é um volume de dados local que armazena de forma segura as chaves de encriptação para VMs Azure encriptados.
> [!NOTE]
> Não elimine ou edite qualquer conteúdo neste disco. Não desmonte o disco, uma vez que a presença de chaves de encriptação é necessária para quaisquer operações de encriptação na IaaS VM.


## <a name="what-encryption-method-does-azure-disk-encryption-use"></a>Que método de encriptação utilizar o Azure Disk Encryption?

A Encriptação do Disco Azure utiliza o padrão de desencriptação do aes-xts-plain64 com uma chave master de volume de 256 bits.

## <a name="if-i-use-encryptformatall-and-specify-all-volume-types-will-it-erase-the-data-on-the-data-drives-that-we-already-encrypted"></a>Se eu utilizar EncryptFormatAll e especifique todos os tipos de volume, irá apagar os dados em unidades de dados que nós já de ser encriptados?
Não, os dados não ser apagados da unidades de dados que já são encriptadas com o Azure Disk Encryption. Semelhante a como EncryptFormatAll não voltar a encriptar a unidade do SO, ele não criptografa novamente a unidade de dados já encriptados. Para mais informações, consulte os [critérios do EncryptFormatAll](disk-encryption-linux.md#use-encryptformatall-feature-for-data-disks-on-linux-vms).        

## <a name="is-xfs-filesystem-supported"></a>O sistema de ficheiros XFS é suportado?
Os volumes XFS são suportados para encriptação de discos de dados apenas com o EncryptFormatAll. Isto irá reformar o volume, apagando quaisquer dados anteriormente existentes. Para mais informações, consulte os [critérios do EncryptFormatAll](disk-encryption-linux.md#use-encryptformatall-feature-for-data-disks-on-linux-vms).

## <a name="can-i-backup-and-restore-an-encrypted-vm"></a>Posso fazer backup e restaurar um VM encriptado? 

O Azure Backup fornece um mecanismo para fazer backup e restaurar vm's encriptados dentro da mesma subscrição e região.  Para obter instruções, consulte [O Backup e restaure as máquinas virtuais encriptadas com cópia](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption)de segurança Azure .  Restaurar um VM encriptado para uma região diferente não é atualmente suportado.  

## <a name="where-can-i-go-to-ask-questions-or-provide-feedback"></a>Onde posso ir para colocar questões ou comentários?

Pode fazer perguntas ou fornecer feedback sobre o fórum de encriptação do [disco Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureDiskEncryption).

## <a name="next-steps"></a>Passos seguintes
Neste documento, ficou a saber mais sobre as perguntas mais frequentes relacionadas com o Azure Disk Encryption. Para obter mais informações sobre este serviço, consulte os artigos seguintes:

- [Descrição geral do Azure Disk Encryption](disk-encryption-overview.md)
- [Aplicar encriptação de disco no Centro de Segurança Azure](https://docs.microsoft.com/azure/security-center/security-center-apply-disk-encryption)
- [Encriptação de dados azure em repouso](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)
