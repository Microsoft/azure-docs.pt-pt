---
title: Sobre o Agente MARS
description: Saiba como o Agente MARS suporta os cenários de backup
ms.reviewer: srinathv
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: d2cc8e32152f6930c9c250e2811668cc2c924616
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "78673281"
---
# <a name="about-the-microsoft-azure-recovery-services-mars-agent"></a>Sobre o agente microsoft Azure Recovery Services (MARS)

Este artigo descreve como o serviço de backup Azure utiliza o agente microsoft Azure Recovery Services (MARS) para fazer backup e restaurar ficheiros, pastas e o estado de volume ou sistema de um computador no local para o Azure.

O agente MARS suporta os seguintes cenários de backup:

![Cenários de backup mars](./media/backup-try-azure-backup-in-10-mins/backup-scenarios.png)

- **Ficheiros e Pastas**: Proteja seletivamente ficheiros e pastas do Windows.
- **Nível de Volume:** Proteja todo o volume do Windows da sua máquina.
- **Nível do sistema:** Proteja todo o estado do sistema Windows.

O agente MARS suporta os seguintes cenários de restauro:

![Cenários de recuperação de MARS](./media/backup-try-azure-backup-in-10-mins/restore-scenarios.png)

- **Mesmo Servidor**: O servidor no qual a cópia de segurança foi originalmente criada.
  - **Ficheiros e Pastas**: Escolha os ficheiros e pastas individuais que pretende restaurar.
  - **Nível de Volume:** Escolha o ponto de volume e recuperação que pretende restaurar e, em seguida, restaurá-lo para o mesmo local ou para uma localização alternativa na mesma máquina.  Crie uma cópia dos ficheiros existentes, sobrepor ficheiros existentes ou ignore a recuperação dos ficheiros existentes.
  - **Nível do sistema:** Escolha o estado do sistema e o ponto de recuperação para restaurar a mesma máquina num local determinado.

- **Servidor Alternativo**: Um servidor diferente do servidor onde a cópia de segurança foi tomada.
  - **Ficheiros e Pastas**: Escolha os ficheiros e pastas individuais cujo ponto de recuperação pretende restaurar para uma máquina-alvo.
  - **Nível de Volume:** Escolha o ponto de volume e recuperação que pretende restaurar para outro local. Crie uma cópia dos ficheiros existentes, sobrepor ficheiros existentes ou ignore a recuperação dos ficheiros existentes.
  - **Nível do sistema:** Escolha o estado do sistema e o ponto de recuperação para restaurar como ficheiro System State para uma máquina alternativa.

## <a name="backup-process"></a>Processo de cópia de segurança

1. A partir do portal Azure, crie um cofre de Serviços de [Recuperação](install-mars-agent.md#create-a-recovery-services-vault), e escolha ficheiros, pastas e o estado do sistema a partir dos objetivos de Backup.
2. [Descarregue as credenciais do cofre dos Serviços](https://docs.microsoft.com/azure/backup/install-mars-agent#download-the-mars-agent) de Recuperação e o instalador de agentes para uma máquina no local.

    Para proteger a máquina no local selecionando a opção 'Backup', escolha ficheiros, pastas e o estado do sistema e, em seguida, baixe o agente MARS.

3. Preparar a infraestrutura:

    a. Executar o instalador para [instalar o agente](https://docs.microsoft.com/azure/backup/install-mars-agent#install-and-register-the-agent).

    b. Use as suas credenciais de cofre descarregadas para registar a máquina no cofre dos Serviços de Recuperação.
4. A partir da consola do agente no cliente, [configure a cópia](https://docs.microsoft.com/azure/backup/backup-windows-with-mars-agent#create-a-backup-policy)de segurança . Especifique a política de retenção dos seus dados de backup para começar a protegê-los.

![Diagrama de agente de backup Azure](./media/backup-try-azure-backup-in-10-mins/backup-process.png)

### <a name="additional-scenarios"></a>Cenários adicionais

- **Backup de ficheiros e pastas específicos dentro de máquinas virtuais Azure**: O método principal para apoiar as máquinas virtuais Azure (VMs) é utilizar uma extensão de backup Azure no VM. A extensão apoia todo o VM. Se pretender fazer o back-up de ficheiros e pastas específicos dentro de um VM, pode instalar o agente MARS nos VMs Azure. Para mais informações, consulte Arquitetura: Backup [Azure VM incorporado](https://docs.microsoft.com/azure/backup/backup-architecture#architecture-built-in-azure-vm-backup).

- **Sementeira offline**: Cópias de segurança completas iniciais de dados para o Azure normalmente transferem grandes quantidades de dados e requerem mais largura de banda da rede. As cópias de segurança subsequentes transferem apenas a quantidade de dados delta, ou incremental. O Azure Backup comprime as cópias de segurança iniciais. Através do processo de *sementeira offline,* o Azure Backup pode utilizar discos para carregar os dados iniciais de backup comprimidos offline para o Azure. Para mais informações, consulte [o Backup Azure backup offline utilizando](offline-backup-azure-data-box.md)a Caixa de Dados Azure .

## <a name="next-steps"></a>Passos seguintes

[Matriz de suporte do agente MARS](https://docs.microsoft.com/azure/backup/backup-support-matrix-mars-agent)

[AGENTE MARS FAQ](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq)
