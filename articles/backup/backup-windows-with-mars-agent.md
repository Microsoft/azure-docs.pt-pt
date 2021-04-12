---
title: Fazer o back up das máquinas Do Windows utilizando o agente MARS
description: Utilize o agente Microsoft Azure Recovery Services (MARS) para fazer o back up das máquinas Windows.
ms.topic: conceptual
ms.date: 03/03/2020
ms.openlocfilehash: 54932192d61633da55657e2ba57adf4e30c4fbc7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98702773"
---
# <a name="back-up-windows-server-files-and-folders-to-azure"></a>Fazer o back up ficheiros e pastas do Windows Server para O Azure

Este artigo explica como fazer o backup das máquinas windows utilizando o serviço [Azure Backup](backup-overview.md) e o agente Microsoft Azure Recovery Services (MARS). Mars também é conhecido como o agente Azure Backup.

Neste artigo, vai aprender a:

> [!div class="checklist"]
>
> * Verificar os pré-requisitos
> * Crie uma política de backup e horário.
> * Faça uma cópia de segurança a pedido.

## <a name="before-you-start"></a>Antes de começar

* Saiba como o [Azure Backup utiliza o agente MARS para fazer backup de máquinas windows](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders).
* Saiba mais sobre a [arquitetura de backup](backup-architecture.md#architecture-back-up-to-dpmmabs) que executa o agente MARS num servidor secundário de MABS ou Gestor de Proteção de Dados.
* [Reveja o que é apoiado e o que pode apoiar](backup-support-matrix-mars-agent.md) pelo agente MARS.
* [Verifique](install-mars-agent.md#verify-internet-access) o acesso à Internet nas máquinas que pretende fazer.
* Se o agente MARS não estiver instalado, aprenda a instalá-lo [aqui.](install-mars-agent.md)

## <a name="create-a-backup-policy"></a>Criar uma política de cópias de segurança

A política de backup especifica quando tirar instantâneos dos dados para criar pontos de recuperação. Também especifica quanto tempo para manter os pontos de recuperação. Usas o agente MARS para configurar uma política de reserva.

O Azure Backup não leva automaticamente em conta o horário de verão (DST). Este padrão pode causar alguma discrepância entre o tempo real e o tempo de backup programado.

Para criar uma política de cópias de segurança:

1. Depois de descarregar e registar o agente MARS, abra a consola do agente. Pode encontrá-lo ao pesquisar na máquina por **Cópia de Segurança do Microsoft Azure**.  

1. Em **Ações**, **selecione AgendaMento Backup**.

    ![Agendar uma cópia de segurança do Windows Server](./media/backup-configure-vault/schedule-first-backup.png)
1. No Agendamento Do Assistente de Cópia de Segurança, selecione **Começar a**  >  **seguir**.
1. Em **Itens selecionados para fazer o back up,** selecione **Adicionar Itens**.

    ![Adicione itens para fazer back-up](./media/backup-azure-manage-mars/select-item-to-backup.png)

1. Na caixa **'Itens',** selecione itens para fazer o back up e, em seguida, selecione **OK**.

    ![Selecione itens para fazer back-up](./media/backup-azure-manage-mars/selected-items-to-backup.png)

1. Na **página 'Selecionar itens para fazer back up',** selecione **Seguinte**.
1. Na página **'Agendar Cópia de Segurança** Especificar', especifique quando deve fazer cópias de segurança diárias ou semanais. Em seguida, selecione **Seguinte**.

    * Um ponto de recuperação é criado quando um backup é tomado.
    * O número de pontos de recuperação criados no seu ambiente depende do seu horário de reserva.
    * Pode agendar até três backups diários por dia. No exemplo seguinte, ocorrem dois backups diários, um à meia-noite e outro às 18:00.

        ![Configurar um horário diário de backup](./media/backup-configure-vault/day-schedule.png)

    * Também podes fazer backups semanais. No exemplo seguinte, os backups são tomados todos os domingos e quartas-feiras alternados às 9:30 e 1:00 am.

        ![Estabeleça um horário de backup semanal](./media/backup-configure-vault/week-schedule.png)

1. Na página **'Política de Retenção Seletiva',** especifique como armazenar cópias históricas dos seus dados. Em seguida, selecione **Seguinte**.

    * As definições de retenção especificam quais os pontos de recuperação para armazenar e quanto tempo os armazenar.
    * Para uma definição de retenção diária, indica que no momento especificado para a retenção diária, o último ponto de recuperação será mantido para o número especificado de dias. Ou pode especificar uma política de retenção mensal para indicar que o ponto de recuperação criado no dia 30 de cada mês deve ser armazenado durante 12 meses.
    * A retenção para pontos de recuperação diários e semanais geralmente coincide com o horário de reserva. Assim, quando o horário aciona uma cópia de segurança, o ponto de recuperação que a cópia de segurança cria é armazenado durante a duração que a política de retenção diária ou semanal especifica.
    * No seguinte exemplo:

        * Os reforços diários à meia-noite e as 18:00 são mantidos durante sete dias.
        * Os reforços feitos num sábado à meia-noite e 18:00 são mantidos por quatro semanas.
        * Os reforços feitos no último sábado do mês à meia-noite e as 18:00 são mantidos por 12 meses.
        * Os reforços assumidos no último sábado de março são mantidos por 10 anos.

        ![Exemplo de uma política de retenção](./media/backup-configure-vault/retention-example.png)

1. Na página Escolha o **Tipo de Cópia de Segurança Inicial,** decida se deseja fazer a cópia de segurança inicial sobre a rede ou utilizar a cópia de segurança offline. Para obter a cópia de segurança inicial sobre a rede, selecione **Automaticamente sobre a rede**  >  **Next**.

    Para obter mais informações sobre a cópia de segurança offline, consulte [Use Azure Data Box para obter cópia de segurança offline](offline-backup-azure-data-box.md).

    ![Escolha um tipo inicial de backup](./media/backup-azure-manage-mars/choose-initial-backup-type.png)

1. Na página **Confirmação,** reveja as informações e, em seguida, **selecione Terminar**.

    ![Confirme o tipo de backup](./media/backup-azure-manage-mars/confirm-backup-type.png)

1. Depois de o assistente terminar de criar o horário de backup, selecione **Close**.

    ![Ver o progresso do calendário de backup](./media/backup-azure-manage-mars/confirm-modify-backup-process.png)

Crie uma política em cada máquina onde o agente está instalado.

### <a name="do-the-initial-backup-offline"></a>Faça a cópia de segurança inicial offline

Pode executar uma cópia de segurança inicial automaticamente pela rede, ou pode fazer backup offline. A sementeira offline para uma cópia de segurança inicial é útil se tiver grandes quantidades de dados que exigirão muita largura de banda de rede para transferir.

Para fazer uma transferência offline:

1. Escreva os dados de reserva para um local de preparação.
1. Utilize a ferramenta AzureOfflineBackupDiskPrep para copiar os dados da localização de localização para um ou mais discos SATA.

    A ferramenta cria um trabalho de Importação Azure. Para mais informações, consulte [o serviço Azure Import/Export](../import-export/storage-import-export-service.md).
1. Envie os discos SATA para um datacenter Azure.

    No datacenter, os dados do disco são copiados para uma conta de armazenamento Azure. A Azure Backup copia os dados da conta de armazenamento para o cofre, e estão agendadas cópias incrementais.

Para obter mais informações sobre a sementeira offline, consulte [Use Azure Data Box para obter cópia de segurança offline](offline-backup-azure-data-box.md).

### <a name="enable-network-throttling"></a>Permitir o estrangulamento da rede

Pode controlar a forma como o agente MARS utiliza a largura de banda da rede, permitindo o estrangulamento da rede. O estrangulamento é útil se precisar de fazer cópias de segurança durante o horário de trabalho, mas pretende controlar a largura de banda que a cópia de segurança e a atividade de restauração utiliza.

O estrangulamento da rede em Azure Backup utiliza [qualidade de serviço (QoS)](/windows-server/networking/technologies/qos/qos-policy-top) no sistema operativo local.

O estrangulamento da rede para cópias de segurança está disponível no Windows Server 2012 e mais tarde, e no Windows 8 e mais tarde. Os sistemas operativos devem estar a executar os mais recentes pacotes de serviços.

Para permitir o estrangulamento da rede:

1. No agente MARS, selecione **Change Properties**.
1. No **separador Throttling,** selecione **Ativar o estrangulamento da largura de banda da Internet para operações de backup**.

    ![Configurar estrangulamento de rede para operações de backup](./media/backup-configure-vault/throttling-dialog.png)
1. Especifique a largura de banda permitida durante o horário de trabalho e horas não laborais. Os valores de largura de banda começam nos 512 Kbps e sobem para 1.023 Mbps. Em seguida, selecione **OK**.

## <a name="run-an-on-demand-backup"></a>Executar uma cópia de segurança a pedido

1. No agente MARS, selecione **Back Up Now**.

    ![Volte a fazer o back up agora no Windows Server](./media/backup-configure-vault/backup-now.png)

1. Se a versão do agente MARS for 2.0.9169.0 ou mais recente, então pode definir uma data de retenção personalizada. Na secção **'Retenção de Reserva' Até,** escolha uma data do calendário.

   ![Use o calendário para personalizar uma data de retenção](./media/backup-configure-vault/mars-ondemand.png)

1. Na página **de Confirmação,** reveja as definições e selecione **Back Up**.
1. Selecione **Perto** para fechar o assistente. Se fechar o assistente antes que a cópia de segurança termine, o assistente continua a correr em segundo plano.

Após o acabamento inicial da cópia de segurança, o estado **preenchido do Job** aparece na consola de backup.

## <a name="set-up-on-demand-backup-policy-retention-behavior"></a>Configurar o comportamento de retenção de políticas de backup a pedido

> [!NOTE]
> Esta informação aplica-se apenas às versões do agente MARS com mais de 2.0.9169.0.
>

| Opção de agendamento de backup | Duração da conservação de dados
| -- | --
| Dia | **Retenção predefinida**: Equivalente à "retenção em dias para cópias de segurança diárias". <br/><br/> **Exceção**: Se uma cópia de segurança programada diária que está definida para a retenção a longo prazo (semanas, meses ou anos) falhar, um backup a pedido que é desencadeado logo após a falha é considerado para retenção a longo prazo. Caso contrário, o próximo backup programado é considerado para retenção a longo prazo.<br/><br/> **Cenário de exemplo**: O reforço agendado para quinta-feira às 8:00 da manhã falhou. Este apoio devia ser considerado para retenção semanal, mensal ou anual. Assim, o primeiro backup a pedido desencadeado antes do próximo backup agendado na sexta-feira às 8:00 é automaticamente marcado para retenção semanal, mensal ou anual. Este reforço substitui o reforço de quinta-feira 8:00 am.
| Semana | **Retenção padrão:** Um dia. As cópias de segurança a pedido que são tomadas para uma fonte de dados que tenha uma política de backup semanal são eliminadas no dia seguinte. São apagados mesmo que sejam os mais recentes backups para a fonte de dados. <br/><br/> **Exceção**: Se uma cópia de segurança programada semanal que está definida para a retenção a longo prazo (semanas, meses ou anos) falhar, um backup a pedido que é desencadeado logo após a falha é considerado para retenção a longo prazo. Caso contrário, o próximo backup programado é considerado para retenção a longo prazo. <br/><br/> **Cenário de exemplo**: O reforço agendado para quinta-feira às 8:00 da manhã falhou. Este apoio devia ser considerado para retenção mensal ou anual. Assim, o primeiro backup a pedido que é desencadeado antes do próximo backup agendado na quinta-feira às 8:00 é automaticamente marcado para retenção mensal ou anual. Este reforço substitui o reforço de quinta-feira 8:00 am.

Para obter mais informações, consulte [Criar uma política de backup](#create-a-backup-policy).

## <a name="next-steps"></a>Passos seguintes

* Saiba como [restaurar ficheiros em Azure](backup-azure-restore-windows-server.md).
* Encontre [questões comuns sobre o backup de ficheiros e pastas](backup-azure-file-folder-backup-faq.md)
