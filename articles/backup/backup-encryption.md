---
title: Encriptação em Backup Azure
description: Saiba como as funcionalidades de encriptação no Azure Backup ajudam-no a proteger os seus dados de backup e a satisfazer as necessidades de segurança do seu negócio.
ms.topic: conceptual
ms.date: 04/30/2020
ms.openlocfilehash: 1f7e0f26df0f8bd70a10b36f658dcfebe897b475
ms.sourcegitcommit: 3beb067d5dc3d8895971b1bc18304e004b8a19b3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82765802"
---
# <a name="encryption-in-azure-backup"></a>Encriptação em Backup Azure

Todos os seus dados de reserva são automaticamente encriptados quando armazenados na nuvem utilizando encriptação de Armazenamento Azure, o que o ajuda a cumprir os seus compromissos de segurança e conformidade. Estes dados em repouso são encriptados utilizando encriptação AES de 256 bits, uma das cifras de blocomais fortes disponíveis, e é compatível com o FIPS 140-2.

Além da encriptação em repouso, todos os seus dados de backup em trânsito são transferidos em HTTPS. Permanece sempre na rede de espinha dorsal Azure.

Para mais informações, consulte [a encriptação do Armazenamento Azure para obter dados em repouso](https://docs.microsoft.com/azure/storage/common/storage-service-encryption). Consulte o [FaQ de Backup Azure](https://docs.microsoft.com/azure/backup/backup-azure-backup-faq#encryption) para responder a quaisquer perguntas que possa ter sobre encriptação.

## <a name="encryption-of-backup-data-using-platform-managed-keys"></a>Encriptação de dados de backup usando chaves geridas pela plataforma

Por predefinição, todos os seus dados são encriptados utilizando chaves geridas pela plataforma. Não precisa de tomar nenhuma ação explícita do seu lado para permitir esta encriptação e aplica-se a todas as cargas de trabalho que estão a ser apoiadas no seu cofre de Serviços de Recuperação.

## <a name="encryption-of-backup-data-using-customer-managed-keys"></a>Encriptação de dados de backup usando chaves geridas pelo cliente

Ao fazer backup das suas Máquinas Virtuais Azure, pode agora encriptar os seus dados utilizando chaves próprias e geridas por si. O Azure Backup permite-lhe utilizar as chaves RSA armazenadas no Cofre de Chaves Azure para encriptar as suas cópias de segurança. A chave de encriptação utilizada para encriptar cópias de segurança pode ser diferente da utilizada para a fonte. Os dados estão protegidos utilizando uma chave de encriptação de dados baseada em AES 256 (DEK), que está, por sua vez, protegida utilizando as suas chaves. Isto dá-lhe total controlo sobre os dados e as chaves. Para permitir a encriptação, é necessário que o cofre dos Serviços de Recuperação tenha acesso à chave de encriptação no Cofre de Chaves Azure. Pode desativar a chave ou revogar o acesso sempre que necessário. No entanto, deve ativar a encriptação utilizando as suas chaves antes de tentar proteger quaisquer itens para o cofre.

>[!NOTE]
>Esta funcionalidade encontra-se atualmente em disponibilidade limitada. Preencha [este inquérito](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0H3_nezt2RNkpBCUTbWEapURE9TTDRIUEUyNFhNT1lZS1BNVDdZVllHWi4u) e AskAzureBackupTeam@microsoft.com envie-nos um e-mail se pretender encriptar os seus dados de backup utilizando as chaves geridas pelo cliente. Note que a capacidade de utilização desta funcionalidade está sujeita à aprovação do serviço de backup Azure.

## <a name="backup-of-managed-disk-vms-encrypted-using-customer-managed-keys"></a>Backup de VMs de disco gerido encriptado usando chaves geridas pelo cliente

O Azure Backup também lhe permite fazer backup dos seus VMs Azure que utilizam a sua chave para encriptação do lado do servidor. A chave utilizada para encriptar os discos é armazenada no Cofre de Chaves Azure e gerida por si. A encriptação do lado do servidor utilizando chaves geridas pelo cliente difere da encriptação do Disco Azure, uma vez que a ADE aproveita o BitLocker (para Windows) e o DM-Crypt (para o Linux) para executar encriptação no hóspede, a SSE encripta dados no serviço de armazenamento, permitindo-lhe utilizar qualquer SISTEMA ou imagens para os seus VMs. Consulte a [Encriptação de discos geridos com chaves geridas](https://docs.microsoft.com/azure/virtual-machines/windows/disk-encryption#customer-managed-keys) pelo cliente para mais detalhes.

## <a name="backup-of-vms-encrypted-using-ade"></a>Backup de VMs encriptados usando ADE

Com o Azure Backup, também pode fazer backup das suas máquinas Azure Virtual que têm os seus OS ou discos de dados encriptados utilizando encriptação do disco Azure. O ADE utiliza o BitLocker para VMs windows e DM-Crypt para VMs Linux para executar encriptação no hóspede. Para mais detalhes, consulte [Back up e restaure as máquinas virtuais encriptadas com Backup Azure](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption).

## <a name="next-steps"></a>Passos seguintes

- [Volte e restaure um Azure VM encriptado](backup-azure-vms-encryption.md)
