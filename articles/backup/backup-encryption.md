---
title: Encriptação no Backup Azure
description: Saiba como as funcionalidades de encriptação no Azure Backup ajudam a proteger os seus dados de backup e a satisfazer as necessidades de segurança do seu negócio.
ms.topic: conceptual
ms.date: 04/30/2020
ms.openlocfilehash: aafb9868dfb6a63ec9b6a3ae654b88b202a1a145
ms.sourcegitcommit: 1e6c13dc1917f85983772812a3c62c265150d1e7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/09/2020
ms.locfileid: "86171827"
---
# <a name="encryption-in-azure-backup"></a>Encriptação no Backup Azure

Todos os seus dados de back-up são automaticamente encriptados quando armazenados na nuvem usando encriptação de Armazenamento Azure, o que o ajuda a cumprir os seus compromissos de segurança e conformidade. Estes dados em repouso são encriptados utilizando encriptação AES de 256 bits, uma das cifras de blocos mais fortes disponíveis, e é compatível com FIPS 140-2.

Além da encriptação em repouso, todos os seus dados de backup em trânsito são transferidos para HTTPS. Permanece sempre na rede de espinha dorsal do Azure.

Para obter mais informações, consulte [a encriptação do Azure Storage para obter dados em repouso](https://docs.microsoft.com/azure/storage/common/storage-service-encryption). Consulte as FAQ de backup do [Azure](https://docs.microsoft.com/azure/backup/backup-azure-backup-faq#encryption) para responder a quaisquer perguntas que possa ter sobre encriptação.

## <a name="encryption-of-backup-data-using-platform-managed-keys"></a>Encriptação de dados de backup usando chaves geridas pela plataforma

Por predefinição, todos os seus dados são encriptados utilizando chaves geridas pela plataforma. Não precisa de tomar nenhuma ação explícita a partir da sua parte para ativar esta encriptação e aplica-se a todas as cargas de trabalho que estão a ser apoiadas até ao cofre dos Serviços de Recuperação.

## <a name="encryption-of-backup-data-using-customer-managed-keys"></a>Encriptação de dados de backup usando chaves geridas pelo cliente

Ao fazer o backup das suas Máquinas Virtuais Azure, pode agora encriptar os seus dados utilizando as teclas de propriedade e geridas por si. O Azure Backup permite-lhe utilizar as suas teclas RSA armazenadas no Cofre da Chave Azure para encriptar as suas cópias de segurança. A chave de encriptação utilizada para encriptar cópias de segurança pode ser diferente da utilizada para a origem. Os dados estão protegidos utilizando uma chave de encriptação de dados baseada em AES 256 (DEK), que está, por sua vez, protegida usando as suas chaves. Isto dá-lhe controlo total sobre os dados e as chaves. Para permitir a encriptação, é necessário que o cofre dos Serviços de Recuperação tenha acesso à chave de encriptação no Cofre da Chave Azure. Pode desativar a chave ou revogar o acesso sempre que necessário. No entanto, tem de ativar a encriptação utilizando as suas chaves antes de tentar proteger quaisquer itens no cofre.

Leia mais sobre como encriptar os seus dados de backup utilizando chaves geridas pelo cliente [aqui.](encryption-at-rest-with-cmk.md)

## <a name="backup-of-managed-disk-vms-encrypted-using-customer-managed-keys"></a>Backup de VMs de disco gerido encriptados usando chaves geridas pelo cliente

O Azure Backup também permite fazer backup dos seus VMs Azure que utilizam a sua chave para [encriptação do serviço de armazenamento.](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) A chave utilizada para encriptar os discos é armazenada no Cofre da Chave Azure e gerida por si. A Encriptação do Serviço de Armazenamento (SSE) utilizando chaves geridas pelo cliente difere da Encriptação do Disco Azure, uma vez que a ADE aproveita o BitLocker (para Windows) e o DM-Crypt (para Linux) para executar a encriptação no hóspede, a SSE encripta dados no serviço de armazenamento, permitindo-lhe utilizar qualquer SISTEMA ou imagens para os seus VMs. Consulte a [encriptação de discos geridos com chaves geridas pelo cliente](https://docs.microsoft.com/azure/virtual-machines/windows/disk-encryption#customer-managed-keys) para obter mais detalhes.

## <a name="infrastructure-level-encryption-for-backup-data"></a>Encriptação ao nível da infraestrutura para dados de backup

Além de encriptar os seus dados no cofre dos Serviços de Recuperação utilizando chaves geridas pelo cliente, também pode optar por ter uma camada adicional de encriptação configurada na infraestrutura de armazenamento. Esta encriptação de infraestrutura é gerida pela plataforma e, juntamente com a encriptação em repouso, utilizando chaves geridas pelo cliente, permite encriptação de duas camadas dos seus dados de backup. Note-se que a encriptação da infraestrutura só pode ser configurada se optar pela primeira vez por utilizar as suas próprias chaves para encriptação em repouso. A encriptação da infraestrutura utiliza chaves geridas pela plataforma para encriptar dados.

>[!NOTE]
>A encriptação da infraestrutura está atualmente em pré-visualização limitada e está disponível apenas nas regiões norte-americanas east, EUA West2, US South Central, US Gov Arizona e EUA GOV Virginia apenas. Se desejar utilizar a funcionalidade em qualquer uma destas regiões, preencha [este formulário](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0H3_nezt2RNkpBCUTbWEapUN0VHNEpJS0ZUWklUNVdJSTEzR0hIOVRMVC4u) e envie-nos um e-mail para [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com) .

## <a name="backup-of-vms-encrypted-using-ade"></a>Cópia de segurança dos VMs encriptados através do ADE

Com o Azure Backup, também pode fazer backup das suas máquinas Azure Virtual que têm o seu SISTEMA ou discos de dados encriptados através da Encriptação do Disco Azure. O ADE utiliza o BitLocker para VMs windows e DM-Crypt para os VMs do Linux para executar encriptação no hóspede. Para mais detalhes, consulte [Back up e restaure máquinas virtuais encriptadas com Azure Backup](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption).

## <a name="next-steps"></a>Passos seguintes

- [Recue e restaure um Azure VM encriptado](backup-azure-vms-encryption.md)
