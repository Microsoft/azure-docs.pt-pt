---
title: Perguntas frequentes-Azure Disk Encryption para VMs do Windows
description: Este artigo fornece respostas para perguntas frequentes sobre a criptografia de disco Microsoft Azure para VMs IaaS do Windows.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 11/01/2019
ms.custom: seodec18
ms.openlocfilehash: ea2a66a6b012664a9596a02ea32c1a0b677ee3ea
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74384270"
---
# <a name="azure-disk-encryption-for-windows-vms-faq"></a>Perguntas frequentes sobre o Azure Disk Encryption para VMs do Windows

Este artigo fornece respostas para perguntas frequentes sobre Azure Disk Encryption para VMs do Windows. Para obter mais informações sobre esse serviço, consulte [Azure Disk Encryption visão geral](disk-encryption-overview.md).

## <a name="where-is-azure-disk-encryption-in-general-availability-ga"></a>Onde está o Azure Disk Encryption em disponibilidade geral (GA)?

Azure Disk Encryption está em disponibilidade geral em todas as regiões públicas do Azure.

## <a name="what-user-experiences-are-available-with-azure-disk-encryption"></a>Ocorre com o qual o utilizador que estão disponíveis com o Azure Disk Encryption?

Disponibilidade geral de encriptação de disco do Azure oferece suporte a modelos do Azure Resource Manager, do Azure PowerShell e CLI do Azure. As experiências de utilizador diferente dão-lhe flexibilidade. Você tem três opções diferentes para habilitar a criptografia de disco para suas VMs. Para obter mais informações sobre a experiência do usuário e as diretrizes passo a passo disponíveis no Azure Disk Encryption, consulte [cenários de Azure Disk Encryption do Windows](disk-encryption-windows.md).

## <a name="how-much-does-azure-disk-encryption-cost"></a>Quanto custa o Azure Disk Encryption?

Não há nenhum custo para criptografar discos de VM com Azure Disk Encryption, mas há encargos associados ao uso de Azure Key Vault. Para obter mais informações sobre custos de Azure Key Vault, consulte a página de [preços de Key Vault](https://azure.microsoft.com/pricing/details/key-vault/) .

## <a name="how-can-i-start-using-azure-disk-encryption"></a>Como posso começar a utilizar o Azure Disk Encryption?

Para começar, leia a [visão geral de Azure Disk Encryption](disk-encryption-overview.md).

## <a name="what-vm-sizes-and-operating-systems-support-azure-disk-encryption"></a>Quais tamanhos de VM e sistemas operacionais dão suporte Azure Disk Encryption?

O artigo de [visão geral de Azure Disk Encryption](disk-encryption-overview.md) lista os [tamanhos de VM](disk-encryption-overview.md#supported-vm-sizes) e os [sistemas operacionais de vm](disk-encryption-overview.md#supported-operating-systems) que dão suporte a Azure Disk Encryption.

## <a name="can-i-encrypt-both-boot-and-data-volumes-with-azure-disk-encryption"></a>Pode criptografar volumes de arranque e dados com o Azure Disk Encryption?

Você pode criptografar volumes de dados e de inicialização, mas não pode criptografar os dados sem primeiro criptografar o volume do sistema operacional.

Depois de criptografar o volume do sistema operacional, não há suporte para desabilitar a criptografia no volume do sistema operacional.

## <a name="can-i-encrypt-an-unmounted-volume-with-azure-disk-encryption"></a>Posso criptografar um volume não montado com Azure Disk Encryption?

Não, Azure Disk Encryption apenas criptografa volumes montados.

## <a name="how-do-i-rotate-secrets-or-encryption-keys"></a>Como fazer girar segredos ou chaves de criptografia?

Para girar os segredos, basta chamar o mesmo comando usado originalmente para habilitar a criptografia de disco, especificando um Key Vault diferente. Para girar a chave de criptografia de chave, chame o mesmo comando usado originalmente para habilitar a criptografia de disco, especificando a nova criptografia de chave. 

>[!WARNING]
> - Se você tiver usado anteriormente [Azure Disk Encryption com o aplicativo do Azure ad](disk-encryption-windows-aad.md) especificando as credenciais do Azure ad para criptografar essa VM, será necessário continuar usando essa opção para criptografar sua VM. Você não pode usar Azure Disk Encryption nessa VM criptografada, pois esse não é um cenário com suporte, o que significa que não há suporte para a alternância do aplicativo AAD para essa VM criptografada ainda.

## <a name="how-do-i-add-or-remove-a-key-encryption-key-if-i-didnt-originally-use-one"></a>Como fazer adicionar ou remover uma chave de criptografia de chave se eu não utilizou originalmente uma?

Para adicionar uma chave de criptografia de chave, chame o comando Enable novamente passando o parâmetro Key Encryption Key. Para remover uma chave de criptografia de chave, chame o comando Enable novamente sem o parâmetro Key Encryption Key.

## <a name="does-azure-disk-encryption-allow-you-to-bring-your-own-key-byok"></a>O Azure Disk Encryption permite traga a sua própria chave (BYOK)?

Sim, pode fornecer suas próprias chaves de encriptação de chave. Estas chaves são salvaguardadas no Azure Key Vault, que é o armazenamento de chaves do Azure Disk Encryption. Para obter mais informações sobre os cenários de suporte a chaves de criptografia de chave, consulte [criando e configurando um cofre de chaves para Azure Disk Encryption](disk-encryption-key-vault.md).

## <a name="can-i-use-an-azure-created-key-encryption-key"></a>Pode utilizar uma chave de encriptação de chave criadas pelo Azure?

Sim, pode utilizar o Azure Key Vault para gerar uma chave de encriptação de chave para utilização de encriptação de disco do Azure. Estas chaves são salvaguardadas no Azure Key Vault, que é o armazenamento de chaves do Azure Disk Encryption. Para obter mais informações sobre a chave de criptografia de chave, consulte [criando e configurando um cofre de chaves para Azure Disk Encryption](disk-encryption-key-vault.md).

## <a name="can-i-use-an-on-premises-key-management-service-or-hsm-to-safeguard-the-encryption-keys"></a>Posso utilizar um serviço de gestão de chaves no local ou o HSM para salvaguardar as chaves de encriptação?

Não é possível utilizar o serviço de gestão de chaves no local ou o HSM para salvaguardar as chaves de encriptação com o Azure Disk Encryption. Só pode utilizar o serviço Azure Key Vault para salvaguardar as chaves de encriptação. Para obter mais informações sobre os principais cenários de suporte de chave de criptografia, consulte [criando e configurando um cofre de chaves para Azure Disk Encryption](disk-encryption-key-vault.md).

## <a name="what-are-the-prerequisites-to-configure-azure-disk-encryption"></a>Quais são os pré-requisitos para configurar a encriptação de disco do Azure?

Existem pré-requisitos para a encriptação de disco do Azure. Consulte o artigo [criando e configurando um cofre de chaves para Azure Disk Encryption](disk-encryption-key-vault.md) para criar um novo cofre de chaves ou configurar um cofre de chaves existente para acesso de criptografia de disco para habilitar a criptografia e proteger segredos e chaves. Para obter mais informações sobre os principais cenários de suporte de chave de criptografia, consulte [criando e configurando um cofre de chaves para Azure Disk Encryption](disk-encryption-key-vault.md).

## <a name="what-are-the-prerequisites-to-configure-azure-disk-encryption-with-an-azure-ad-app-previous-release"></a>Quais são os pré-requisitos para configurar a encriptação de disco do Azure com uma aplicação do Azure AD (versão anterior)?

Existem pré-requisitos para a encriptação de disco do Azure. Consulte o [Azure Disk Encryption com conteúdo do Azure ad](disk-encryption-windows-aad.md) para criar um aplicativo de Azure Active Directory, criar um novo cofre de chaves ou configurar um cofre de chaves existente para acesso de criptografia de disco para habilitar a criptografia e proteger segredos e chaves. Para obter mais informações sobre os principais cenários de suporte de chave de criptografia, consulte [criando e configurando um cofre de chaves para Azure Disk Encryption com o Azure ad](disk-encryption-key-vault-aad.md).

## <a name="is-azure-disk-encryption-using-an-azure-ad-app-previous-release-still-supported"></a>Azure Disk Encryption está a utilizar uma aplicação do Azure AD (versão anterior), continua a ser suportada?
Sim. Encriptação de disco a utilizar uma aplicação do Azure AD ainda é suportada. No entanto, quando novas VMs a encriptar é recomendado que utilize o novo método, em vez de encriptar com uma aplicação do Azure AD. 

## <a name="can-i-migrate-vms-that-were-encrypted-with-an-azure-ad-app-to-encryption-without-an-azure-ad-app"></a>Pode migrar as VMs que eram criptografadas com uma aplicação do Azure AD para encriptação sem uma aplicação do Azure AD?
  Atualmente, não um caminho de migração direta de máquinas que era criptografado com uma aplicação do Azure AD para encriptação sem uma aplicação do Azure AD. Além disso, não um caminho direto de criptografia sem uma aplicação do Azure AD para a encriptação com uma aplicação do AD. 

## <a name="what-version-of-azure-powershell-does-azure-disk-encryption-support"></a>Qual a versão do Azure PowerShell suporta o Azure Disk Encryption?

Utilize a versão mais recente do SDK do PowerShell do Azure para configurar o Azure Disk Encryption. Baixe a versão mais recente do [Azure PowerShell](https://github.com/Azure/azure-powershell/releases). *Não* há suporte para Azure Disk Encryption no SDK do Azure versão 1.1.0.

## <a name="what-is-the-disk-bek-volume-or-mntazure_bek_disk"></a>O que é o disco "Bek Volume" ou "/ mnt/azure_bek_disk"?

O "volume Bek" é um volume de dados local que armazena com segurança as chaves de criptografia para VMs do Azure criptografadas.

> [!NOTE]
> Não elimine ou edite qualquer conteúdo neste disco. Não desmonte o disco, uma vez que a presença de chaves de encriptação é necessária para quaisquer operações de encriptação na IaaS VM.

## <a name="what-encryption-method-does-azure-disk-encryption-use"></a>Que método de encriptação utilizar o Azure Disk Encryption?

Azure Disk Encryption seleciona o método de criptografia no BitLocker com base na versão do Windows da seguinte maneira:

| Versões do Windows                 | Versão | Método de criptografia        |
|----------------------------------|--------|--------------------------|
| Windows Server 2012, Windows 10 ou superior  | > = 1511 |XTS-AES 256 bits           |
| Windows Server 2012, Windows 8, 8,1, 10 | < 1511 |AES 256 bit *              |
| 2008R2 do Windows Server            |        |AES 256 bit com difusor |

\* o AES 256 bit com difusor não tem suporte no Windows 2012 e posterior.

Para determinar a versão do sistema operacional Windows, execute a ferramenta ' winver ' em sua máquina virtual.

## <a name="if-i-use-encryptformatall-and-specify-all-volume-types-will-it-erase-the-data-on-the-data-drives-that-we-already-encrypted"></a>Se eu utilizar EncryptFormatAll e especifique todos os tipos de volume, irá apagar os dados em unidades de dados que nós já de ser encriptados?
Não, os dados não ser apagados da unidades de dados que já são encriptadas com o Azure Disk Encryption. Semelhante a como EncryptFormatAll não voltar a encriptar a unidade do SO, ele não criptografa novamente a unidade de dados já encriptados. 

## <a name="can-i-backup-and-restore-an-encrypted-vm"></a>Posso fazer backup e restaurar uma VM criptografada? 

O backup do Azure fornece um mecanismo para fazer backup e restaurar VMs criptografadas na mesma assinatura e região.  Para obter instruções, consulte [fazer backup e restaurar máquinas virtuais criptografadas com o backup do Azure](../../backup/backup-azure-vms-encryption.md).  Atualmente, não há suporte para a restauração de uma VM criptografada em uma região diferente.  

## <a name="where-can-i-go-to-ask-questions-or-provide-feedback"></a>Onde posso ir para colocar questões ou comentários?

Você pode fazer perguntas ou fornecer comentários sobre o [Fórum de Azure Disk Encryption](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureDiskEncryption).

## <a name="next-steps"></a>Passos Seguintes
Neste documento, ficou a saber mais sobre as perguntas mais frequentes relacionadas com o Azure Disk Encryption. Para obter mais informações sobre este serviço, consulte os artigos seguintes:

- [Descrição geral do Azure Disk Encryption](disk-encryption-overview.md)
- [Aplicar a criptografia de disco na central de segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-apply-disk-encryption)
- [Criptografia de dados do Azure em repouso](../../security/fundamentals/encryption-atrest.md)
