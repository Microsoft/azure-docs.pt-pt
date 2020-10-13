---
title: Encriptação no Azure Backup
description: Saiba como as funcionalidades de encriptação no Azure Backup ajudam a proteger os seus dados de backup e a satisfazer as necessidades de segurança do seu negócio.
ms.topic: conceptual
ms.date: 08/04/2020
ms.custom: references_regions
ms.openlocfilehash: f55deba58cd7b725bd030409296794e5de911c09
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89017911"
---
# <a name="encryption-in-azure-backup"></a>Encriptação no Azure Backup

Todos os seus dados de back-up são automaticamente encriptados quando armazenados na nuvem usando encriptação de Armazenamento Azure, o que o ajuda a cumprir os seus compromissos de segurança e conformidade. Estes dados em repouso são encriptados utilizando encriptação AES de 256 bits, uma das cifras de blocos mais fortes disponíveis, e é compatível com FIPS 140-2. Além da encriptação em repouso, todos os seus dados de backup em trânsito são transferidos para HTTPS. Permanece sempre na rede de espinha dorsal do Azure.

## <a name="levels-of-encryption-in-azure-backup"></a>Níveis de encriptação no Azure Backup

O Azure Backup inclui encriptação em dois níveis:

- **Encriptação de dados no cofre dos Serviços de Recuperação**
  - **Utilizando teclas geridas pela plataforma**: Por padrão, todos os seus dados são encriptados utilizando teclas geridas pela plataforma. Não precisa de tomar nenhuma ação explícita do seu lado para ativar esta encriptação. Aplica-se a todas as cargas de trabalho que estão a ser apoiadas até ao cofre dos Serviços de Recuperação.
  - **Utilizando teclas geridas pelo cliente**: Ao fazer o backup das suas Máquinas Virtuais Azure, pode optar por encriptar os seus dados utilizando chaves de encriptação possuídas e geridas por si. O Azure Backup permite-lhe utilizar as suas teclas RSA armazenadas no Cofre da Chave Azure para encriptar as suas cópias de segurança. A chave de encriptação utilizada para encriptar cópias de segurança pode ser diferente da utilizada para a origem. Os dados estão protegidos utilizando uma chave de encriptação de dados baseada em AES 256 (DEK), que está, por sua vez, protegida usando as suas chaves. Isto dá-lhe controlo total sobre os dados e as chaves. Para permitir a encriptação, é necessário que conceda ao cofre dos Serviços de Recuperação o acesso à chave de encriptação no Cofre da Chave Azure. Pode desativar a chave ou revogar o acesso sempre que necessário. No entanto, tem de ativar a encriptação utilizando as suas chaves antes de tentar proteger quaisquer itens no cofre. [Saiba mais aqui.](encryption-at-rest-with-cmk.md)
  - **Encriptação ao nível da infraestrutura**: Além de encriptar os seus dados no cofre dos Serviços de Recuperação utilizando chaves geridas pelo cliente, também pode optar por ter uma camada adicional de encriptação configurada na infraestrutura de armazenamento. Esta encriptação de infraestrutura é gerida pela plataforma. Juntamente com a encriptação em repouso utilizando as teclas geridas pelo cliente, permite a encriptação de duas camadas dos seus dados de backup. A encriptação da infraestrutura só pode ser configurada se optar pela primeira vez por utilizar as suas próprias chaves para encriptação em repouso. A encriptação da infraestrutura utiliza chaves geridas pela plataforma para encriptar dados.
- **Encriptação específica para a carga de trabalho que está sendo apoiada**  
  - **Backup da máquina virtual Azure**: O Azure Backup suporta a cópia de segurança dos VMs com discos encriptados utilizando [teclas geridas pela plataforma,](https://docs.microsoft.com/azure/virtual-machines/windows/disk-encryption#platform-managed-keys)bem como [chaves geridas](https://docs.microsoft.com/azure/virtual-machines/windows/disk-encryption#customer-managed-keys) pelo cliente, propriedade e gerida por si. Além disso, também pode fazer o back up das suas máquinas Azure Virtual que têm o seu SISTEMA ou discos de dados encriptados utilizando [a encriptação do disco Azure](backup-azure-vms-encryption.md#encryption-support-using-ade). O ADE utiliza o BitLocker para VMs do Windows e DM-Crypt para os VMs Linux, para executar encriptação no hóspede.

>[!NOTE]
>A encriptação da infraestrutura está atualmente em pré-visualização limitada e está disponível apenas nas regiões norte-americanas east, EUA West2, US South Central, US Gov Arizona e EUA GOV Virginia apenas. Se desejar utilizar a funcionalidade em qualquer uma destas regiões, preencha [este formulário](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0H3_nezt2RNkpBCUTbWEapUN0VHNEpJS0ZUWklUNVdJSTEzR0hIOVRMVC4u) e envie-nos um e-mail para [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com) .

## <a name="next-steps"></a>Passos seguintes

- [Azure Storage encryption for data at rest](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) (Encriptação do Armazenamento do Azure para dados inativos)
- [Azure Backup FAQ](backup-azure-backup-faq.md#encryption) para quaisquer perguntas que possa ter sobre encriptação
