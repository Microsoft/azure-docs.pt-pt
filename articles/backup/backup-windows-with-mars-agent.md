---
title: Recue as máquinas do Windows utilizando o agente MARS
description: Utilize o agente Microsoft Azure Recovery Services (MARS) para fazer o back-up das máquinas Windows.
ms.topic: conceptual
ms.date: 03/03/2020
ms.openlocfilehash: 12463f33a6fa97b33e70b77fb2fcf6b0a27b5790
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79408917"
---
# <a name="back-up-windows-machines-by-using-the-azure-backup-mars-agent"></a>Faça backup das máquinas Windows utilizando o agente Azure Backup MARS

Este artigo explica como fazer backup das máquinas Windows utilizando o serviço [de backup Azure](backup-overview.md) e o agente microsoft Azure Recovery Services (MARS). Mars também é conhecido como o agente Azure Backup.

Neste artigo, aprenderá a:

> [!div class="checklist"]
>
> * Verificar os pré-requisitos
> * Crie uma política de backup e horário.
> * Faça um reforço a pedido.

## <a name="before-you-start"></a>Antes de começar

* Saiba como o [Azure Backup utiliza o agente MARS para fazer backup](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders)das máquinas windows .
* Conheça a [arquitetura de backup](backup-architecture.md#architecture-back-up-to-dpmmabs) que executa o agente MARS num servidor secundário de MABS ou Data Protection Manager.
* Reveja [o que é suportado e o que pode apoiar](backup-support-matrix-mars-agent.md) pelo agente mars.
* [Verifique](install-mars-agent.md#verify-internet-access) o acesso à Internet nas máquinas que pretende fazer apoio.
* Se o agente MARS não estiver instalado, aprenda a instalá-lo [aqui](install-mars-agent.md).

## <a name="create-a-backup-policy"></a>Criar uma política de backup

A política de backup especifica quando tirar fotos dos dados para criar pontos de recuperação. Especifica também quanto tempo para manter os pontos de recuperação. Usa o agente MARS para configurar uma política de backup.

A Backup Azure não tem automaticamente em conta o horário de verão (DST). Este padrão pode causar alguma discrepância entre o tempo real e o tempo de backup programado.

Para criar uma política de backup:

1. Depois de descarregar e registar o agente MARS, abra a consola do agente. Pode encontrá-lo ao pesquisar na máquina por **Cópia de Segurança do Microsoft Azure**.  

1. Em **Ações,** selecione **'Agenda'.**

    ![Agendar uma cópia de segurança do Windows Server](./media/backup-configure-vault/schedule-first-backup.png)
1. No Assistente de Reserva de Agenda, selecione **Iniciar-se** > **Seguinte**.
1. Em **itens selecionados para fazer back up,** selecione **Adicionar itens**.

    ![Adicione itens para fazer apoio](./media/backup-azure-manage-mars/select-item-to-backup.png)

1. Na caixa **'Selecionar itens',** selecione itens para fazer o back-up e, em seguida, selecione **OK**.

    ![Selecione itens para fazer back-up](./media/backup-azure-manage-mars/selected-items-to-backup.png)

1. Na página **Select Itens para Back Up,** selecione **Next**.
1. Na página **'Agenda', especifique** quando deve receber cópias de segurança diárias ou semanais. Em seguida, selecione **Seguinte**.

    * Um ponto de recuperação é criado quando um backup é tomado.
    * O número de pontos de recuperação criados no seu ambiente depende do seu horário de backup.
    * Pode agendar até três cópias de segurança diárias por dia. No exemplo seguinte, ocorrem dois backups diários, um à meia-noite e outro às 18:00.

        ![Configurar um horário diário de backup](./media/backup-configure-vault/day-schedule.png)

    * Também podes fazer backups semanais. No exemplo seguinte, os backups são tomados todos os domingos e quartas-feiras alternativos às 9:30 e 1:00 da manhã.

        ![Configurar um horário semanal de backup](./media/backup-configure-vault/week-schedule.png)

1. Na página **Select Retention Policy,** especifique como armazenar cópias históricas dos seus dados. Em seguida, selecione **Seguinte**.

    * As definições de retenção especificam quais os pontos de recuperação a armazenar e quanto tempo os armazenam.
    * Para uma definição diária de retenção, indica que no momento especificado para a retenção diária, o último ponto de recuperação será mantido durante o número de dias especificado. Ou pode especificar uma política mensal de retenção que indique que o ponto de recuperação criado no dia 30 de cada mês deve ser armazenado durante 12 meses.
    * A retenção para pontos de recuperação diários e semanais geralmente coincide com o calendário de backup. Assim, quando o horário aciona uma cópia de segurança, o ponto de recuperação que a cópia de segurança cria é armazenado durante a duração que a política de retenção diária ou semanal especifica.
    * No seguinte exemplo:

        * Os reforços diários à meia-noite e às 18:00 são mantidos por sete dias.
        * Os reforços feitos num sábado à meia-noite e às 18:00 são mantidos por quatro semanas.
        * Os reforços feitos no último sábado do mês à meia-noite e às 18:00 são mantidos por 12 meses.
        * Os reforços feitos no último sábado de março são mantidos por 10 anos.

        ![Exemplo de uma política de retenção](./media/backup-configure-vault/retention-example.png)

1. Na página **'Escolha', decida** se pretende utilizar a cópia de segurança inicial sobre a rede ou utilizar a cópia de segurança offline. Para passar a cópia de segurança inicial sobre a rede, selecione **automaticamente sobre a rede** > **Seguinte**.

    Para obter mais informações sobre backup offline, consulte [Use Azure Data Box para backup offline](offline-backup-azure-data-box.md).

    ![Escolha um tipo inicial de backup](./media/backup-azure-manage-mars/choose-initial-backup-type.png)

1. Na página **de Confirmação,** reveja as informações e, em seguida, selecione **Terminar**.

    ![Confirmar o tipo de cópia de segurança](./media/backup-azure-manage-mars/confirm-backup-type.png)

1. Depois de o assistente terminar a criação do horário de cópia de segurança, selecione **Close**.

    ![Veja o progresso do horário de backup](./media/backup-azure-manage-mars/confirm-modify-backup-process.png)

Crie uma política em cada máquina onde o agente está instalado.

### <a name="do-the-initial-backup-offline"></a>Faça a cópia de segurança inicial offline

Pode executar uma cópia de segurança inicial automaticamente sobre a rede, ou pode fazer backup offline. A sementeiroffline para uma cópia de segurança inicial é útil se tiver grandes quantidades de dados que exigirão muita largura de banda da rede para transferir.

Para fazer uma transferência offline:

1. Escreva os dados de cópia de segurança para um local de preparação.
1. Utilize a ferramenta AzureOfflineBackupDiskPrep para copiar os dados do local de paragem para um ou mais discos SATA.

    A ferramenta cria um trabalho de Importação Azure. Para mais informações, consulte [O que é o serviço de importação/exportação Azure.](https://docs.microsoft.com/azure/storage/common/storage-import-export-service)
1. Envie os discos SATA para um centro de dados Azure.

    No datacenter, os dados do disco são copiados para uma conta de armazenamento Azure. A Azure Backup copia os dados da conta de armazenamento para o cofre, e estão agendadas cópias de segurança incrementais.

Para obter mais informações sobre a sementeira offline, consulte [Use Azure Data Box para cópia de segurança offline](offline-backup-azure-data-box.md).

### <a name="enable-network-throttling"></a>Ativar o estrangulamento da rede

Pode controlar a forma como o agente MARS utiliza a largura de banda da rede, permitindo o estrangulamento da rede. O estrangulamento é útil se precisar de fazer backup de dados durante o horário de trabalho, mas pretende controlar a largura de banda que a backup e restaurar a atividade utiliza.

O estrangulamento da rede em Backup Azure utiliza [qualidade de serviço (QoS)](https://docs.microsoft.com/windows-server/networking/technologies/qos/qos-policy-top) no sistema operativo local.

A rede de aceleração para backups está disponível no Windows Server 2012 e posteriormente, e no Windows 8 e mais tarde. Os sistemas operativos devem estar a executar os mais recentes pacotes de serviços.

Para permitir o estrangulamento da rede:

1. No agente MARS, selecione **Change Properties**.
1. No separador **Throttling,** **selecione Ativar**o estrangulamento da largura de banda da Internet para operações de backup .

    ![Configurar a rede de estrangulamento para operações de backup](./media/backup-configure-vault/throttling-dialog.png)
1. Especifique a largura de banda permitida durante o horário de trabalho e o horário de trabalho. Os valores de largura de banda começam em 512 Kbps e sobem até 1.023 MBps. Em seguida, selecione **OK**.

## <a name="run-an-on-demand-backup"></a>Faça um backup a pedido

1. No agente MARS, selecione **Back Up Now**.

    ![Volte agora no Windows Server](./media/backup-configure-vault/backup-now.png)

1. Se a versão do agente MARS for 2.0.9169.0 ou mais recente, então pode definir uma data de retenção personalizada. Na secção **'Reter Cópia de Segurança',** escolha uma data do calendário.

   ![Use o calendário para personalizar uma data de retenção](./media/backup-configure-vault/mars-ondemand.png)

1. Na página **confirmação,** reveja as definições e selecione **Back Up**.
1. Selecione **Perto** para fechar o assistente. Se fechar o assistente antes de o backup terminar, o assistente continua a correr em segundo plano.

Após os acabamentos iniciais de backup, o estado concluído de **Trabalho** aparece na consola De reserva.

## <a name="set-up-on-demand-backup-policy-retention-behavior"></a>Criar comportamentos de retenção de políticas de backup a pedido

> [!NOTE]
> Esta informação aplica-se apenas às versões do agente MARS que tenham mais de 2.0.9169.0.
>

| Opção de horário de cópia de segurança | Duração da retenção de dados
| -- | --
| Dia | **Retenção por defeito**: Equivalente à "retenção em dias para cópias de segurança diárias". <br/><br/> **Exceção**: Se um backup diário programado que está definido para retenção a longo prazo (semanas, meses ou anos) falhar, um backup a pedido que é desencadeado logo após a falha é considerado para retenção a longo prazo. Caso contrário, o próximo backup programado é considerado para retenção a longo prazo.<br/><br/> **Cenário exemplo**: O backup agendado para quinta-feira às 8:00 da manhã falhou. Este backup era para ser considerado para retenção semanal, mensal ou anual. Assim, o primeiro backup a pedido desencadeado antes do próximo backup agendado na sexta-feira às 8:00 é automaticamente marcado para retenção semanal, mensal ou anual. Este reforço substitui a quinta-feira, 8:00.
| Week (Semana) | **Retenção por defeito**: Um dia. Os backups a pedido que são tomados para uma fonte de dados que tem uma política semanal de backup são eliminados no dia seguinte. São apagados mesmo que sejam os mais recentes reforços para a fonte de dados. <br/><br/> **Exceção**: Se um backup semanal programado que está definido para retenção a longo prazo (semanas, meses ou anos) falhar, um backup a pedido que é desencadeado logo após a falha é considerado para retenção a longo prazo. Caso contrário, o próximo backup programado é considerado para retenção a longo prazo. <br/><br/> **Cenário exemplo**: O backup agendado para quinta-feira às 8:00 da manhã falhou. Este backup era para ser considerado para retenção mensal ou anual. Assim, o primeiro backup a pedido que é desencadeado antes do próximo backup agendado na quinta-feira às 8:00 é automaticamente marcado para retenção mensal ou anual. Este reforço substitui a quinta-feira, 8:00.

Para mais informações, consulte [Criar uma política](#create-a-backup-policy)de backup .

## <a name="next-steps"></a>Passos seguintes

* Saiba como [restaurar ficheiros em Azure](backup-azure-restore-windows-server.md).
* Encontre [questões comuns sobre o backup de ficheiros e pastas](backup-azure-file-folder-backup-faq.md)

