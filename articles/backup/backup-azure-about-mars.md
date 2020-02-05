---
title: Sobre o agente MARS
description: Saiba como o agente MARS dá suporte aos cenários de backup
ms.reviewer: srinathv
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 348980b840b814c09ce46627f286489d4caa9f8f
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77023961"
---
# <a name="about-the-microsoft-azure-recovery-services-mars-agent"></a>Sobre o agente de Serviços de Recuperação do Microsoft Azure (MARS)

Este artigo descreve como o serviço de backup do Azure usa o agente de Serviços de Recuperação do Microsoft Azure (MARS) para fazer backup e restaurar arquivos, pastas e o volume ou o estado do sistema de um computador local para o Azure.

O agente MARS dá suporte aos seguintes cenários de backup:

![Cenários de backup MARS](./media/backup-try-azure-backup-in-10-mins/backup-scenarios.png)

- **Arquivos e pastas**: proteger seletivamente arquivos e pastas do Windows.
- **Nível de volume**: Proteja todo o volume do Windows de seu computador.
- **Nível do sistema**: proteger todo o estado do sistema do Windows.

O agente MARS dá suporte aos seguintes cenários de restauração:

![Cenários de recuperação MARS](./media/backup-try-azure-backup-in-10-mins/restore-scenarios.png)

-   **Mesmo servidor**: o servidor no qual o backup foi criado originalmente.
    -    **Arquivos e pastas**: escolha os arquivos e as pastas individuais que você deseja restaurar.
    -    **Nível de volume**: escolha o volume e o ponto de recuperação que deseja restaurar e, em seguida, restaure-o no mesmo local ou em um local alternativo no mesmo computador.  Crie uma cópia dos arquivos existentes, substitua os arquivos existentes ou ignore a recuperação de arquivos existentes.
    -    **Nível do sistema**: escolha o estado do sistema e o ponto de recuperação para restaurar no mesmo computador em um local especificado.


-   **Servidor alternativo**: um servidor que não seja o servidor onde o backup foi feito.
    -    **Arquivos e pastas**: escolha os arquivos e as pastas individuais cujo ponto de recuperação você deseja restaurar em um computador de destino.
    -    **Nível de volume**: escolha o volume e o ponto de recuperação que você deseja restaurar para outro local. Crie uma cópia dos arquivos existentes, substitua os arquivos existentes ou ignore a recuperação de arquivos existentes.
    -    **Nível do sistema**: escolha o estado do sistema e o ponto de recuperação para restaurar como um arquivo de estado do sistema para um computador alternativo.

## <a name="backup-process"></a>Processo de backup

1. No portal do Azure, crie um [cofre dos serviços de recuperação](https://docs.microsoft.com/azure/backup/backup-configure-vault#create-a-recovery-services-vault)e escolha arquivos, pastas e o estado do sistema de metas de backup.
2. [Baixe as credenciais do cofre dos serviços de recuperação e o instalador do agente](https://docs.microsoft.com/azure/backup/backup-configure-vault#download-the-mars-agent) em um computador local. 

    Para proteger o computador local selecionando a opção de backup, escolha arquivos, pastas e o estado do sistema e, em seguida, baixe o agente MARS.

3. Preparar a infraestrutura:

    a. Execute o instalador para [instalar o agente](https://docs.microsoft.com/azure/backup/backup-configure-vault#install-and-register-the-agent).

    b. Use as credenciais do cofre baixado para registrar o computador no cofre dos serviços de recuperação.
4. No console do agente no cliente, [Configure o backup](https://docs.microsoft.com/azure/backup/backup-configure-vault#create-a-backup-policy). Especifique a política de retenção de seus dados de backup para começar a protegê-lo.

![Diagrama do agente de backup do Azure](./media/backup-try-azure-backup-in-10-mins/backup-process.png)


### <a name="additional-scenarios"></a>Cenários adicionais
-   **Fazer backup de arquivos e pastas específicos em máquinas virtuais do Azure**: o método principal para fazer backup de VMs (máquinas virtuais) do Azure é usar uma extensão de backup do Azure na VM. A extensão faz o backup de toda a VM. Se desejar fazer backup de arquivos e pastas específicos em uma VM, você poderá instalar o agente MARS nas VMs do Azure. Para obter mais informações, consulte [arquitetura: backup de VM do Azure interno](https://docs.microsoft.com/azure/backup/backup-architecture#architecture-built-in-azure-vm-backup).

-   **Propagação offline**: backups completos iniciais de dados no Azure normalmente transferem grandes quantidades de dados e exigem mais largura de banda de rede. Os backups subsequentes transferem apenas o Delta, ou incrementais, a quantidade de dados. O backup do Azure compacta os backups iniciais. Por meio do processo de *propagação offline*, o backup do Azure pode usar discos para carregar os dados de backup inicial compactados offline no Azure. Para mais informações, consulte [o Backup Azure backup offline utilizando](offline-backup-azure-data-box.md)a Caixa de Dados Azure .

## <a name="next-steps"></a>Passos seguintes
[Matriz de suporte do agente MARS](https://docs.microsoft.com/azure/backup/backup-support-matrix-mars-agent)

[FAQ do agente MARS](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq)
