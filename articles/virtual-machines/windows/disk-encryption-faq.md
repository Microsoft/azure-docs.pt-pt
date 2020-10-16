---
title: FAQ - Encriptação do disco Azure para VMs windows
description: Este artigo fornece respostas a perguntas frequentes sobre encriptação do disco do Microsoft Azure para o Windows IaaS VMs.
author: msmbaldwin
ms.service: virtual-machines-windows
ms.subservice: security
ms.topic: conceptual
ms.author: mbaldwin
ms.date: 11/01/2019
ms.custom: seodec18
ms.openlocfilehash: 6fedefccf57364e690415542114030ba7ce12b49
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91974096"
---
# <a name="azure-disk-encryption-for-windows-virtual-machines-faq"></a>Encriptação do disco Azure para máquinas virtuais do Windows FAQ

Este artigo fornece respostas a perguntas frequentes (FAQ) sobre encriptação de disco Azure para VMs windows. Para obter mais informações sobre este serviço, consulte [a visão geral da encriptação do disco Azure](disk-encryption-overview.md).

## <a name="what-is-azure-disk-encryption-for-windows-vms"></a>O que é Azure Disk Encryption para VMs windows?

A encriptação do disco Azure para VMs do Windows utiliza a funcionalidade BitLocker do Windows para fornecer encriptação completa do disco de segurança e discos de dados. Além disso, fornece encriptação do disco temporário quando o [parâmetro VolumeType é Tudo](disk-encryption-windows.md#enable-encryption-on-a-newly-added-data-disk).  O conteúdo flui encriptado do VM para o backend de Armazenamento. Assim, fornecer encriptação de ponta a ponta com uma chave gerida pelo cliente.
 
Ver [VMs suportados e sistemas operativos](disk-encryption-overview.md#supported-vms-and-operating-systems).
 
## <a name="where-is-azure-disk-encryption-in-general-availability-ga"></a>Onde está a Encriptação do Disco Azure em disponibilidade geral (GA)?

A encriptação do disco Azure está em disponibilidade geral em todas as regiões públicas do Azure.

## <a name="what-user-experiences-are-available-with-azure-disk-encryption"></a>Que experiências do utilizador estão disponíveis com encriptação do disco Azure?

Azure Disk Encryption GA suporta modelos de Gestor de Recursos Azure, Azure PowerShell e Azure CLI. As diferentes experiências do utilizador dão-lhe flexibilidade. Tem três opções diferentes para ativar a encriptação do disco para os seus VMs. Para obter mais informações sobre a experiência do utilizador e orientação passo a passo disponível na Encriptação do Disco [Azure, consulte os cenários de encriptação do disco Azure para windows](disk-encryption-windows.md).

## <a name="how-much-does-azure-disk-encryption-cost"></a>Quanto custa a encriptação do disco Azure?

Não há nenhuma acusação para encriptar discos VM com encriptação de disco Azure, mas existem acusações associadas com o uso do Cofre de Chaves Azure. Para obter mais informações sobre os custos do Cofre chave Azure, consulte a página [de preços do Cofre chave.](https://azure.microsoft.com/pricing/details/key-vault/)

## <a name="how-can-i-start-using-azure-disk-encryption"></a>Como posso começar a usar encriptação do disco Azure?

Para começar, leia a visão geral da encriptação do [disco Azure](disk-encryption-overview.md).

## <a name="what-vm-sizes-and-operating-systems-support-azure-disk-encryption"></a>Que tamanhos de VM e sistemas operativos suportam encriptação do disco Azure?

O artigo de visão geral da encriptação do [disco Azure](disk-encryption-overview.md) lista os [tamanhos VM](disk-encryption-overview.md#supported-vms) e [os sistemas operativos VM](disk-encryption-overview.md#supported-operating-systems) que suportam a encriptação do disco Azure.

## <a name="can-i-encrypt-both-boot-and-data-volumes-with-azure-disk-encryption"></a>Posso encriptar tanto os volumes de arranque como os volumes de dados com encriptação do disco Azure?

Pode encriptar tanto os volumes de arranque como os volumes de dados, mas não pode encriptar os dados sem primeiro encriptar o volume de SO.

## <a name="can-i-encrypt-an-unmounted-volume-with-azure-disk-encryption"></a>Posso encriptar um volume não montado com encriptação do disco Azure?

Não, a encriptação do disco Azure apenas encripta volumes montados.

## <a name="what-is-storage-server-side-encryption"></a>O que é encriptação do lado do servidor de armazenamento?

Encriptação do lado do servidor de armazenamento encripta discos geridos pelo Azure no Azure Storage. Os discos geridos são encriptados por padrão com encriptação do lado do Servidor com uma chave gerida pela plataforma (a partir de 10 de junho de 2017). Pode gerir a encriptação de discos geridos com as suas próprias chaves especificando uma chave gerida pelo cliente. Para obter mais informações, consulte [a encriptação do lado do Servidor dos discos geridos pelo Azure](disk-encryption.md).
 
## <a name="how-is-azure-disk-encryption-different-from-storage-server-side-encryption-with-customer-managed-key-and-when-should-i-use-each-solution"></a>Como é que a encriptação do disco Azure é diferente da encriptação do lado do servidor de armazenamento com a chave gerida pelo cliente e quando devo usar cada solução?

A Azure Disk Encryption fornece encriptação de ponta a ponta para o disco DE, discos de dados e o disco temporário com uma chave gerida pelo cliente.

- Se os seus requisitos incluirem encriptar todas as encriptações acima e de ponta a ponta, utilize encriptação do disco Azure. 
- Se os seus requisitos incluirem encriptar apenas dados em repouso com a chave gerida pelo cliente, em seguida, use [encriptação do lado do Servidor com teclas geridas pelo cliente](disk-encryption.md). Não é possível encriptar um disco com encriptação do servidor Azure e encriptação do servidor de armazenamento com teclas geridas pelo cliente.
- Se estiver a utilizar um cenário chamado em [cenários não suportados para o Windows](disk-encryption-windows.md#unsupported-scenarios), considere [a encriptação do lado do Servidor com as teclas geridas pelo cliente](disk-encryption.md). 
- Se a política da sua organização permitir que você encripte o conteúdo em repouso com uma chave gerida pelo Azure, então não é necessária nenhuma ação - o conteúdo é encriptado por padrão. Para discos geridos, o conteúdo dentro do armazenamento é encriptado por padrão com encriptação do lado do Servidor com chave gerida pela plataforma. A chave é gerida pelo serviço de armazenamento Azure. 

## <a name="how-do-i-rotate-secrets-or-encryption-keys"></a>Como posso rodar segredos ou chaves de encriptação?

Para rodar segredos, basta ligar para o mesmo comando que usou originalmente para ativar a encriptação do disco, especificando um cofre de chaves diferente. Para rodar a chave de encriptação, ligue para o mesmo comando que usou originalmente para ativar a encriptação do disco, especificando a nova encriptação da chave. 

>[!WARNING]
> - Se já utilizou a [encriptação do disco Azure com a aplicação AZure AD](disk-encryption-windows-aad.md) especificando credenciais AZure AD para encriptar este VM, terá de continuar a utilizar esta opção para encriptar o seu VM. Não é possível utilizar a Encriptação do Disco Azure neste VM encriptado, uma vez que este não é um cenário suportado, o que significa que mudar a aplicação AAD para este VM encriptado ainda não está suportado.

## <a name="how-do-i-add-or-remove-a-key-encryption-key-if-i-didnt-originally-use-one"></a>Como posso adicionar ou remover uma chave de encriptação se não usei uma originalmente?

Para adicionar uma chave de encriptação, ligue para o comando de ativação novamente passando o parâmetro chave de encriptação da chave. Para remover uma chave de encriptação, ligue novamente para o comando de ativação sem o parâmetro chave de encriptação da chave.

## <a name="does-azure-disk-encryption-allow-you-to-bring-your-own-key-byok"></a>A encriptação do disco Azure permite-lhe trazer a sua própria chave (BYOK)?

Sim, pode fornecer as suas próprias chaves de encriptação. Estas chaves estão protegidas no Cofre da Chave Azure, que é a loja chave para a encriptação do disco Azure. Para obter mais informações sobre os cenários de suporte das chaves de encriptação chave, consulte [criar e configurar um cofre chave para encriptação do disco Azure](disk-encryption-key-vault.md).

## <a name="can-i-use-an-azure-created-key-encryption-key"></a>Posso usar uma chave de encriptação criada pelo Azure?

Sim, pode utilizar o Azure Key Vault para gerar uma chave de encriptação chave para o uso de encriptação do disco Azure. Estas chaves estão protegidas no Cofre da Chave Azure, que é a loja chave para a encriptação do disco Azure. Para obter mais informações sobre a chave de encriptação, consulte [criar e configurar um cofre chave para encriptação do disco Azure](disk-encryption-key-vault.md).

## <a name="can-i-use-an-on-premises-key-management-service-or-hsm-to-safeguard-the-encryption-keys"></a>Posso utilizar um serviço de gestão de chaves no local ou hSM para proteger as chaves de encriptação?

Não é possível utilizar o serviço de gestão de chaves no local ou o HSM para proteger as chaves de encriptação com encriptação do disco Azure. Só pode utilizar o serviço Azure Key Vault para proteger as chaves de encriptação. Para obter mais informações sobre os principais cenários de suporte à encriptação, consulte [criar e configurar um cofre-chave para encriptação do disco Azure](disk-encryption-key-vault.md).

## <a name="what-are-the-prerequisites-to-configure-azure-disk-encryption"></a>Quais são os pré-requisitos para configurar a encriptação do disco Azure?

Existem pré-requisitos para encriptação do disco Azure. Consulte a Criação e configuração de um cofre chave para o artigo [de encriptação do disco Azure](disk-encryption-key-vault.md) para criar um novo cofre de chaves, ou crie um cofre de chaves existente para o acesso à encriptação do disco para permitir a encriptação e salvaguardar segredos e chaves. Para obter mais informações sobre os principais cenários de suporte à encriptação, consulte [criar e configurar um cofre-chave para encriptação do disco Azure](disk-encryption-key-vault.md).

## <a name="what-are-the-prerequisites-to-configure-azure-disk-encryption-with-an-azure-ad-app-previous-release"></a>Quais são os pré-requisitos para configurar a Encriptação do Disco Azure com uma aplicação AD AZure (versão anterior)?

Existem pré-requisitos para encriptação do disco Azure. Consulte a Encriptação do Disco Azure com o conteúdo [Azure AD](disk-encryption-windows-aad.md) para criar uma aplicação Azure Ative Directory, criar um novo cofre de chaves ou configurar um cofre-chave existente para acesso à encriptação do disco para permitir a encriptação e salvaguardar segredos e chaves. Para obter mais informações sobre os principais cenários de suporte à chave de encriptação, consulte [criar e configurar um cofre-chave para encriptação de disco Azure com Azure AD](disk-encryption-key-vault-aad.md).

## <a name="is-azure-disk-encryption-using-an-azure-ad-app-previous-release-still-supported"></a>A Encriptação do Disco Azure está a utilizar uma aplicação AD AZure (versão anterior) ainda suportada?
Yes. A encriptação do disco utilizando uma aplicação AD Azure ainda é suportada. No entanto, ao encriptar novos VMs é recomendado que use o novo método em vez de encriptar com uma aplicação AD Azure. 

## <a name="can-i-migrate-vms-that-were-encrypted-with-an-azure-ad-app-to-encryption-without-an-azure-ad-app"></a>Posso migrar VMs que foram encriptados com uma aplicação AD AD Azure para encriptação sem uma aplicação AD AZure?
  Atualmente, não existe um caminho de migração direta para máquinas que foram encriptadas com uma aplicação AD AD Azure para encriptar sem uma aplicação AD AZure. Além disso, não existe um caminho direto desde a encriptação sem uma aplicação AD AZure até encriptação com uma aplicação AD. 

## <a name="what-version-of-azure-powershell-does-azure-disk-encryption-support"></a>Que versão do Azure PowerShell suporta a encriptação do disco Azure?

Utilize a versão mais recente do Azure PowerShell SDK para configurar a Encriptação do Disco Azure. Descarregue a versão mais recente do [Azure PowerShell](https://github.com/Azure/azure-powershell/releases). A encriptação do disco Azure *não* é suportada pela versão 1.1.0 do Azure SDK.

## <a name="what-is-the-disk-bek-volume-or-mntazure_bek_disk"></a>O que é o disco "Bek Volume" ou "/mnt/azure_bek_disk"?

O "volume Bek" é um volume de dados local que armazena de forma segura as chaves de encriptação dos VMs Azure Encriptados.

> [!NOTE]
> Não elimine nem edite quaisquer conteúdos neste disco. Não desmonte o disco uma vez que a presença da chave de encriptação é necessária para quaisquer operações de encriptação no IaaS VM.

## <a name="what-encryption-method-does-azure-disk-encryption-use"></a>Que método de encriptação utiliza a encriptação do disco Azure?

A Azure Disk Encryption seleciona o método de encriptação no BitLocker com base na versão do Windows da seguinte forma:

| Versões windows                 | Versão | Método de encriptação        |
|----------------------------------|--------|--------------------------|
| Windows Server 2012, Windows 10 ou maior  | >=1511 |XTS-AES 256 bit           |
| Windows Server 2012, Windows 8, 8.1, 10 | < 1511 |AES 256 bit *              |
| Windows Server 2008R2            |        |AES 256 bit com Difusor |

\* A aES 256 bit com Diffuser não é suportada no Windows 2012 e mais tarde.

Para determinar a versão Do Windows OS, execute a ferramenta 'winver' na sua máquina virtual.

## <a name="can-i-backup-and-restore-an-encrypted-vm"></a>Posso fazer cópias de segurança e restaurar um VM encriptado? 

O Azure Backup fornece um mecanismo para fazer backup e restaurar vMs encriptados dentro da mesma subscrição e região.  Para obter instruções, consulte [o Back up e restaure as máquinas virtuais encriptadas com a Azure Backup](../../backup/backup-azure-vms-encryption.md).  Restaurar um VM encriptado para uma região diferente não é suportado atualmente.  

## <a name="where-can-i-go-to-ask-questions-or-provide-feedback"></a>Onde posso ir para fazer perguntas ou dar feedback?

Pode fazer perguntas ou fornecer feedback sobre a [página de perguntas do Microsoft Q&Uma página de perguntas para encriptação do disco Azure](/answers/topics/azure-disk-encryption.html).

## <a name="next-steps"></a>Passos seguintes
Neste documento, aprendeu mais sobre as questões mais frequentes relacionadas com a Encriptação do Disco Azure. Para obter mais informações sobre este serviço, consulte os seguintes artigos:

- [Descrição geral do Azure Disk Encryption](disk-encryption-overview.md)
- [Aplicar encriptação de disco no Azure Security Center](../../security-center/asset-inventory.md)
- [Encriptação de dados inativa do Azure](../../security/fundamentals/encryption-atrest.md)