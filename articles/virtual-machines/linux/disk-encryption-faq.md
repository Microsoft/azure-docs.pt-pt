---
title: FAQ - Encriptação do disco Azure para VMs Linux
description: Este artigo fornece respostas a perguntas frequentes sobre encriptação do disco do Microsoft Azure para Os VMs do Linux IaaS.
author: msmbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: conceptual
ms.author: mbaldwin
ms.date: 06/05/2019
ms.custom: seodec18
ms.openlocfilehash: 232bc43440979a08da4a0e77d9b49bf56fdb0ae4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87374048"
---
# <a name="azure-disk-encryption-for-linux-virtual-machines-faq"></a>Encriptação do disco Azure para máquinas virtuais Linux FAQ

Este artigo fornece respostas a perguntas frequentes (FAQ) sobre encriptação de disco Azure para máquinas virtuais Linux (VMs). Para obter mais informações sobre este serviço, consulte [a visão geral da encriptação do disco Azure](disk-encryption-overview.md).

## <a name="what-is-azure-disk-encryption-for-linux-vms"></a>O que é Azure Disk Encryption para Os VMs Linux?

A encriptação do disco Azure para Os VMs Linux utiliza a funcionalidade dm-crypt do Linux para fornecer encriptação completa do disco de segurança do sistema operativo* e discos de dados. Além disso, fornece encriptação do disco temporário ao utilizar a [função EncryptFormatAll](disk-encryption-linux.md#use-encryptformatall-feature-for-data-disks-on-linux-vms). O conteúdo flui encriptado do VM para o backend de Armazenamento. Assim, fornecer encriptação de ponta a ponta com uma chave gerida pelo cliente.
 
Ver [VMs suportados e sistemas operativos](disk-encryption-overview.md#supported-vms-and-operating-systems).

## <a name="where-is-azure-disk-encryption-in-general-availability-ga"></a>Onde está a Encriptação do Disco Azure em disponibilidade geral (GA)?

A encriptação do disco Azure para Os VMs Linux está em disponibilidade geral em todas as regiões públicas do Azure.

## <a name="what-user-experiences-are-available-with-azure-disk-encryption"></a>Que experiências do utilizador estão disponíveis com encriptação do disco Azure?

Azure Disk Encryption GA suporta modelos de Gestor de Recursos Azure, Azure PowerShell e Azure CLI. As diferentes experiências do utilizador dão-lhe flexibilidade. Tem três opções diferentes para ativar a encriptação do disco para os seus VMs. Para obter mais informações sobre a experiência do utilizador e orientação passo a passo disponível na Encriptação do Disco [Azure, consulte os cenários de encriptação do disco Azure para o Linux](disk-encryption-linux.md).

## <a name="how-much-does-azure-disk-encryption-cost"></a>Quanto custa a encriptação do disco Azure?

Não há nenhuma acusação para encriptar discos VM com encriptação de disco Azure, mas existem acusações associadas com o uso do Cofre de Chaves Azure. Para obter mais informações sobre os custos do Cofre chave Azure, consulte a página [de preços do Cofre chave.](https://azure.microsoft.com/pricing/details/key-vault/)

## <a name="how-can-i-start-using-azure-disk-encryption"></a>Como posso começar a usar encriptação do disco Azure?

Para começar, leia a visão geral da encriptação do [disco Azure](disk-encryption-overview.md).

## <a name="what-vm-sizes-and-operating-systems-support-azure-disk-encryption"></a>Que tamanhos de VM e sistemas operativos suportam encriptação do disco Azure?

O artigo de visão geral da encriptação do [disco Azure](disk-encryption-overview.md) lista os [tamanhos VM](disk-encryption-overview.md#supported-vms) e [os sistemas operativos VM](disk-encryption-overview.md#supported-operating-systems) que suportam a encriptação do disco Azure.

## <a name="can-i-encrypt-both-boot-and-data-volumes-with-azure-disk-encryption"></a>Posso encriptar tanto os volumes de arranque como os volumes de dados com encriptação do disco Azure?

Sim, pode encriptar tanto os volumes de arranque como os volumes de dados, ou pode encriptar o volume de dados sem ter de encriptar primeiro o volume de SISTEMA. 

Depois de encriptar o volume de SO, a encriptação incapacitante no volume de SO não é suportada. Para os VMs Linux num conjunto de escala, apenas o volume de dados pode ser encriptado.

## <a name="can-i-encrypt-an-unmounted-volume-with-azure-disk-encryption"></a>Posso encriptar um volume não montado com encriptação do disco Azure?

Não, a encriptação do disco Azure apenas encripta volumes montados.

## <a name="what-is-storage-server-side-encryption"></a>O que é encriptação do lado do servidor de armazenamento?

Encriptação do lado do servidor de armazenamento encripta discos geridos pelo Azure no Azure Storage. Os discos geridos são encriptados por padrão com encriptação do lado do Servidor com uma chave gerida pela plataforma (a partir de 10 de junho de 2017). Pode gerir a encriptação de discos geridos com as suas próprias chaves especificando uma chave gerida pelo cliente. Para obter mais informações consulte: [Encriptação do lado do servidor dos discos geridos Azure](disk-encryption.md).
 
## <a name="how-is-azure-disk-encryption-different-from-storage-server-side-encryption-with-customer-managed-key-and-when-should-i-use-each-solution"></a>Como é que a encriptação do disco Azure é diferente da encriptação do lado do servidor de armazenamento com a chave gerida pelo cliente e quando devo usar cada solução?

A Azure Disk Encryption fornece encriptação de ponta a ponta para o disco DE, discos de dados e o disco temporário, utilizando uma chave gerida pelo cliente.
- Se os seus requisitos incluirem encriptar todas as encriptações acima e de ponta a ponta, utilize encriptação do disco Azure. 
- Se os seus requisitos incluirem encriptar apenas dados em repouso com a chave gerida pelo cliente, em seguida, use [encriptação do lado do Servidor com teclas geridas pelo cliente](disk-encryption.md). Não é possível encriptar um disco com encriptação do servidor Azure e encriptação do servidor de armazenamento com teclas geridas pelo cliente. 
- Se o seu distro Linux não estiver listado em [sistemas operativos suportados para encriptação de disco Azure](disk-encryption-overview.md#supported-operating-systems) ou estiver a utilizar um cenário chamado nos [cenários não suportados para](disk-encryption-linux.md#unsupported-scenarios)o Windows , considere [a encriptação do lado do Servidor com as teclas geridas pelo cliente](disk-encryption.md).
- Se a política da sua organização permitir que você encripte o conteúdo em repouso com uma chave gerida pelo Azure, então não é necessária nenhuma ação - o conteúdo é encriptado por padrão. Para discos geridos, o conteúdo dentro do armazenamento é encriptado por padrão com encriptação do lado do Servidor com chave gerida pela plataforma. A chave é gerida pelo serviço de armazenamento Azure. 



## <a name="how-do-i-rotate-secrets-or-encryption-keys"></a>Como posso rodar segredos ou chaves de encriptação?

Para rodar segredos, basta ligar para o mesmo comando que usou originalmente para ativar a encriptação do disco, especificando um cofre de chaves diferente. Para rodar a chave de encriptação, ligue para o mesmo comando que usou originalmente para ativar a encriptação do disco, especificando a nova encriptação da chave. 

>[!WARNING]
> - Se já utilizou a [encriptação do disco Azure com a aplicação AZure AD](disk-encryption-linux-aad.md) especificando credenciais AZure AD para encriptar este VM, terá de continuar a utilizar esta opção para encriptar o seu VM. Não é possível utilizar a Encriptação do Disco Azure neste VM encriptado, uma vez que este não é um cenário suportado, o que significa que mudar a aplicação AAD para este VM encriptado ainda não está suportado.

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

Existem pré-requisitos para encriptação do disco Azure. Consulte a Encriptação do Disco Azure com o conteúdo [Azure AD](disk-encryption-linux-aad.md) para criar uma aplicação Azure Ative Directory, criar um novo cofre de chaves ou configurar um cofre-chave existente para acesso à encriptação do disco para permitir a encriptação e salvaguardar segredos e chaves. Para obter mais informações sobre os principais cenários de suporte à chave de encriptação, consulte [criar e configurar um cofre-chave para encriptação de disco Azure com Azure AD](disk-encryption-key-vault-aad.md).

## <a name="is-azure-disk-encryption-using-an-azure-ad-app-previous-release-still-supported"></a>A Encriptação do Disco Azure está a utilizar uma aplicação AD AZure (versão anterior) ainda suportada?
Sim. A encriptação do disco utilizando uma aplicação AD Azure ainda é suportada. No entanto, ao encriptar novos VMs é recomendado que use o novo método em vez de encriptar com uma aplicação AD Azure. 

## <a name="can-i-migrate-vms-that-were-encrypted-with-an-azure-ad-app-to-encryption-without-an-azure-ad-app"></a>Posso migrar VMs que foram encriptados com uma aplicação AD AD Azure para encriptação sem uma aplicação AD AZure?
  Atualmente, não existe um caminho de migração direta para máquinas que foram encriptadas com uma aplicação AD AD Azure para encriptar sem uma aplicação AD AZure. Além disso, não existe um caminho direto desde a encriptação sem uma aplicação AD AZure até encriptação com uma aplicação AD. 

## <a name="what-version-of-azure-powershell-does-azure-disk-encryption-support"></a>Que versão do Azure PowerShell suporta a encriptação do disco Azure?

Utilize a versão mais recente do Azure PowerShell SDK para configurar a Encriptação do Disco Azure. Descarregue a versão mais recente do [Azure PowerShell](https://github.com/Azure/azure-powershell/releases). A encriptação do disco Azure *não* é suportada pela versão 1.1.0 do Azure SDK.

> [!NOTE]
> A extensão de pré-visualização da encriptação do disco Linux Azure "Microsoft.OSTCExtension.AzureDiskEncryptionForLinux" é depreciada. Esta extensão foi publicada para a versão de pré-visualização da encriptação do disco Azure. Não deve utilizar a versão de pré-visualização da extensão no seu teste ou implantação de produção.

> Para cenários de implementação como O Gestor de Recursos Azure (ARM), onde você tem a necessidade de implementar a extensão de encriptação do disco Azure para Linux VM para permitir encriptação no seu Linux IaaS VM, deve utilizar a extensão suportada pela produção de encriptação do disco Azure "Microsoft.Azure.Security.AzureDiskEncryptionForLinux".

## <a name="can-i-apply-azure-disk-encryption-on-my-custom-linux-image"></a>Posso aplicar a encriptação do disco Azure na minha imagem linux personalizada?

Não é possível aplicar encriptação de disco Azure na sua imagem Linux personalizada. Apenas as imagens da galeria Linux para as distribuições apoiadas anteriormente são apoiadas. As imagens linux personalizadas não são suportadas atualmente.

## <a name="can-i-apply-updates-to-a-linux-red-hat-vm-that-uses-the-yum-update"></a>Posso aplicar atualizações a um Linux Red Hat VM que utiliza a atualização yum?

Sim, podes fazer uma atualização yum num Red Hat Linux VM.  Para obter mais informações, consulte [a Encriptação do Disco Azure numa rede isolada.](disk-encryption-isolated-network.md)

## <a name="what-is-the-recommended-azure-disk-encryption-workflow-for-linux"></a>Qual é o fluxo de trabalho recomendado de encriptação do disco Azure para o Linux?

Recomenda-se o seguinte fluxo de trabalho para ter os melhores resultados no Linux:
* Comece a partir da imagem de galeria de stock não modificada correspondente ao necessário oss distro e versão
* Faça a reposição de quaisquer unidades montadas que sejam encriptadas.  Esta reversão permite a recuperação se houver uma falha, por exemplo, se o VM for reiniciado antes de a encriptação ter sido concluída.
* Criptografe (pode levar várias horas ou mesmo dias dependendo das características de VM e do tamanho de quaisquer discos de dados anexados)
* Personalize e adicione software à imagem conforme necessário.

Se este fluxo de trabalho não for possível, confiar na [Encriptação do Serviço de Armazenamento](../../storage/common/storage-service-encryption.md) (SSE) na camada de conta de armazenamento da plataforma pode ser uma alternativa à encriptação completa do disco utilizando a encriptação do dm-crypt.

## <a name="what-is-the-disk-bek-volume-or-mntazure_bek_disk"></a>O que é o disco "Bek Volume" ou "/mnt/azure_bek_disk"?

O "volume Bek" é um volume de dados local que armazena de forma segura as chaves de encriptação dos VMs Azure Encriptados.
> [!NOTE]
> Não elimine nem edite quaisquer conteúdos neste disco. Não desmonte o disco uma vez que a presença da chave de encriptação é necessária para quaisquer operações de encriptação no IaaS VM.


## <a name="what-encryption-method-does-azure-disk-encryption-use"></a>Que método de encriptação utiliza a encriptação do disco Azure?

A Azure Disk Encryption utiliza o padrão desencriptado de aes-xts-plain64 com uma chave master de volume de 256 bits.

## <a name="if-i-use-encryptformatall-and-specify-all-volume-types-will-it-erase-the-data-on-the-data-drives-that-we-already-encrypted"></a>Se eu utilizar o EncryptFormatAll e especificar todos os tipos de volume, irá apagar os dados nas unidades de dados que já criptografámos?
Não, os dados não serão apagados a partir de unidades de dados que já estão encriptadas usando a Encriptação do Disco Azure. Tal como o EncryptFormatAll não reencriptou a unidade de SISTEMA, não voltará a encriptar a unidade de dados já encriptada. Para obter mais informações, consulte os [critérios EncryptFormatAll](disk-encryption-linux.md#use-encryptformatall-feature-for-data-disks-on-linux-vms).        

## <a name="is-xfs-filesystem-supported"></a>O sistema de ficheiros XFS está suportado?
A encriptação dos discos XFS OS é suportada.

A encriptação dos discos de dados XFS só é suportada quando o parâmetro EncryptFormatAll é utilizado. Isto irá reformar o volume, apagando quaisquer dados anteriormente existentes. Para obter mais informações, consulte os [critérios EncryptFormatAll](disk-encryption-linux.md#use-encryptformatall-feature-for-data-disks-on-linux-vms).

## <a name="can-i-backup-and-restore-an-encrypted-vm"></a>Posso fazer cópias de segurança e restaurar um VM encriptado? 

O Azure Backup fornece um mecanismo para fazer backup e restaurar vMs encriptados dentro da mesma subscrição e região.  Para obter instruções, consulte [o Back up e restaure as máquinas virtuais encriptadas com a Azure Backup](../../backup/backup-azure-vms-encryption.md).  Restaurar um VM encriptado para uma região diferente não é suportado atualmente.  

## <a name="where-can-i-go-to-ask-questions-or-provide-feedback"></a>Onde posso ir para fazer perguntas ou dar feedback?

Pode fazer perguntas ou fornecer feedback sobre a [página de perguntas do Microsoft Q&Uma página de perguntas para encriptação do disco Azure](/answers/topics/azure-disk-encryption.html).

## <a name="next-steps"></a>Passos seguintes
Neste documento, aprendeu mais sobre as questões mais frequentes relacionadas com a Encriptação do Disco Azure. Para obter mais informações sobre este serviço, consulte os seguintes artigos:

- [Descrição geral do Azure Disk Encryption](disk-encryption-overview.md)
- [Aplicar encriptação de disco no Azure Security Center](../../security-center/security-center-virtual-machine-protection.md)
- [Encriptação de dados inativa do Azure](../../security/fundamentals/encryption-atrest.md)
