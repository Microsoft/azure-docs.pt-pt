---
title: Back up SQL Server para Azure como uma carga de trabalho DPM
description: Uma introdução para apoiar as bases de dados do SQL Server utilizando o serviço de backup Azure
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: 8cbb8c833bc2933afac300bcc848fd50861011d0
ms.sourcegitcommit: 934776a860e4944f1a0e5e24763bfe3855bc6b60
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/20/2020
ms.locfileid: "77505929"
---
# <a name="back-up-sql-server-to-azure-as-a-dpm-workload"></a>Back up SQL Server para Azure como uma carga de trabalho DPM

Este artigo leva-o através dos passos de configuração para fazer backup nas bases de dados do SQL Server utilizando o Azure Backup.

Para fazer o apoio às bases de dados do SQL Server para o Azure, precisa de uma conta Azure. Se não tiver uma, pode criar uma conta gratuita em poucos minutos. Para mais informações, consulte [Criar a sua conta Azure gratuitamente.](https://azure.microsoft.com/pricing/free-trial/)

Para fazer o apoio a uma base de dados do SQL Server para o Azure e recuperá-la do Azure:

1. Crie uma política de backup para proteger as bases de dados do SQL Server em Azure.
1. Crie cópias de reserva a pedido em Azure.
1. Recupere a base de dados do Azure.

## <a name="before-you-start"></a>Antes de começar

Antes de começar, certifique-se de que cumpriu os [pré-requisitos](backup-azure-dpm-introduction.md#prerequisites-and-limitations) para a utilização de Backup Azure para proteger as cargas de trabalho. Aqui estão algumas das tarefas pré-requisitos: 
* Crie um cofre reserva.
* Descarregue as credenciais do cofre. 
* Instale o agente de reserva Azure.
* Registe o servidor com o cofre.

## <a name="create-a-backup-policy"></a>Criar uma política de backup 

Para proteger as bases de dados do SQL Server em Azure, primeiro crie uma política de backup:

1. No servidor Do Gestor de Proteção de Dados (DPM), selecione o espaço de trabalho **de Proteção.**
1. Selecione **New** para criar um grupo de proteção.

    ![Criar um grupo de proteção](./media/backup-azure-backup-sql/protection-group.png)
1. Na página inicial, reveja as orientações sobre a criação de um grupo de proteção. Em seguida, selecione **Seguinte**.
1. Selecione **Servidores**.

    ![Selecione o tipo de grupo de proteção de Servidores](./media/backup-azure-backup-sql/pg-servers.png)
1. Expanda a máquina SQL Server onde estão localizadas as bases de dados que pretende fazer. Vê as fontes de dados que podem ser apoiadas a partir desse servidor. Expandir **todas as ações Da SQL** e, em seguida, selecionar as bases de dados que pretende fazer o seu apoio. Neste exemplo, selecionamos reportServer$MSDPM2012 e ReportServer$MSDPM2012TempDB. Em seguida, selecione **Seguinte**.

    ![Selecione uma base de dados do Servidor SQL](./media/backup-azure-backup-sql/pg-databases.png)
1. Nomeie o grupo de proteção e, em seguida, selecione **quero proteção on-line**.

    ![Escolha um método de proteção de dados - proteção de discos de curto prazo ou proteção azure on-line](./media/backup-azure-backup-sql/pg-name.png)
1. Na página **De especificar Objetivos de Curto Prazo,** inclua as inputs necessárias para criar pontos de backup para o disco.

    Neste exemplo, a gama de **retenção** é definida para *5 dias*. A frequência de **sincronização** de reserva está definida para uma vez a cada *15 minutos*. **Express Full Backup** está marcado para *as 20:00.*

    ![Estabelecer metas de curto prazo para a proteção de backup](./media/backup-azure-backup-sql/pg-shortterm.png)

   > [!NOTE]
   > Neste exemplo, um ponto de apoio é criado às 20:00 todos os dias. Os dados que foram modificados desde o ponto de backup das 20:00 do dia anterior são transferidos. Este processo chama-se **Express Full Backup**. Embora os registos de transações sejam sincronizados a cada 15 minutos, se precisarmos de recuperar a base de dados às 21:00, então o ponto é criado reproduzindo os registos do último ponto de backup expresso completo, que é 20:00 h neste exemplo.
   >
   >

1. Selecione **Seguinte**. DPM mostra o espaço de armazenamento total disponível. Também mostra a potencial utilização do espaço do disco.

    ![Configurar a atribuição de disco](./media/backup-azure-backup-sql/pg-storage.png)

    Por padrão, o DPM cria um volume por fonte de dados (base de dados do Servidor SQL). O volume é utilizado para a cópia inicial de cópia de reserva. Nesta configuração, o Gestor lógico de Discos (LDM) limita a proteção dPM a 300 fontes de dados (bases de dados do Servidor SQL). Para contornar esta limitação, selecione **Co-localizar dados no DPM Storage Pool**. Se utilizar esta opção, o DPM utiliza um único volume para várias fontes de dados. Esta configuração permite que o DPM proteja até 2.000 bases de dados do Servidor SQL.

    Se selecionar **Automaticamente o crescimento dos volumes,** então o DPM pode explicar o aumento do volume de cópia de segurança à medida que os dados de produção crescem. Se não selecionar **Automaticamente o aumento dos volumes,** então o DPM limita o armazenamento de cópia de segurança às fontes de dados do grupo de proteção.

1. Se for administrador, pode optar por transferir esta cópia de segurança inicial **automaticamente sobre a rede** e escolher o tempo de transferência. Ou escolha transferir **manualmente** a cópia de segurança. Em seguida, selecione **Seguinte**.

    ![Escolha um método de criação de réplicas](./media/backup-azure-backup-sql/pg-manual.png)

    A cópia inicial de cópia de cópia de cópia de cópia de segurança requer a transferência de toda a fonte de dados (base de dados do Servidor SQL). Os dados de backup movem-se do servidor de produção (máquina SQL Server) para o servidor DPM. Se esta cópia de segurança for grande, a transferência dos dados sobre a rede pode causar congestionamento de largura de banda. Por esta razão, os administradores podem optar por utilizar meios amovíveis para transferir **manualmente**a cópia de segurança inicial . Ou podem transferir os dados **automaticamente sobre a rede** num momento determinado.

    Após os acabamentos iniciais de backup, as cópias de backup continuam incrementalmente na cópia inicial de cópia de cópia de cópia de cópia de reserva. As cópias de segurança incrementais tendem a ser pequenas e são facilmente transferidas através da rede.
    
1. Escolha quando fazer uma verificação de consistência. Em seguida, selecione **Seguinte**.

    ![Escolha quando fazer uma verificação de consistência](./media/backup-azure-backup-sql/pg-consistent.png)

    O DPM pode verificar a consistência da integridade do ponto de reserva. Calcula a verificação do ficheiro de backup no servidor de produção (a máquina SQL Server neste exemplo) e os dados de backup desse ficheiro em DPM. Se o cheque encontrar um conflito, então o ficheiro de apoio no DPM é assumido como corrupto. O DPM corrige os dados de back-up enviando os blocos que correspondem à incompatibilidade de verificação. Como a verificação de consistência é uma operação intensiva de desempenho, os administradores podem optar por agendar a verificação de consistência ou executá-la automaticamente.

1. Selecione as fontes de dados para proteger em Azure. Em seguida, selecione **Seguinte**.

    ![Selecione fontes de dados para proteger em Azure](./media/backup-azure-backup-sql/pg-sqldatabases.png)
1. Se for administrador, pode escolher horários de reserva e políticas de retenção que se adequam às políticas da sua organização.

    ![Escolha horários e políticas de retenção](./media/backup-azure-backup-sql/pg-schedule.png)

    Neste exemplo, os backups são tomados diariamente às 12:00 e 20:00.

    > [!TIP]
    > Para uma rápida recuperação, mantenha alguns pontos de recuperação de curto prazo no seu disco. Estes pontos de recuperação são utilizados para a recuperação operacional. O Azure serve como uma boa localização fora do local, proporcionando SLAs mais elevado e disponibilidade garantida.
    >
    > Utilize o DPM para agendar backups Azure após o acabamento das cópias de segurança do disco local. Quando segue esta prática, a última cópia de segurança do disco é copiada para O Azure.
    >

1. Escolha o calendário da política de retenção. Para obter mais informações sobre como funciona a política de retenção, consulte [use O Backup Azure para substituir a sua infraestrutura](backup-azure-backup-cloud-as-tape.md)de fita.

    ![Escolha uma política de retenção](./media/backup-azure-backup-sql/pg-retentionschedule.png)

    Neste exemplo:

    * Os reforços são feitos diariamente às 12:00 e às 20:00. Ficam conservados por 180 dias.
    * O reforço no sábado às 12:00 é mantido por 104 semanas.
    * O reforço do último sábado do mês às 12:00 é mantido por 60 meses.
    * O reforço do último sábado de março às 12:00 é mantido por 10 anos.
    
    Depois de escolher uma política de retenção, selecione **Next**.

1. Escolha como transferir a cópia de cópia inicial de cópia de reserva para o Azure.

    * A opção **de rede automaticamente segue o** seu calendário de cópia seleções para transferir os dados para o Azure.
    * Para mais informações sobre **backup offline,** consulte [a visão geral da Cópia de Segurança Offline](offline-backup-overview.md).

    Depois de escolher um mecanismo de transferência, selecione **Next**.

1. Na página **resumo,** reveja os detalhes da política. Em seguida, selecione **Criar grupo**. Pode selecionar **Close** e assistir ao progresso do trabalho no espaço de trabalho **de Monitorização.**

    ![O progresso da criação do grupo de proteção](./media/backup-azure-backup-sql/pg-summary.png)

## <a name="create-on-demand-backup-copies-of-a-sql-server-database"></a>Criar cópias de backup a pedido de uma base de dados do SQL Server

Um ponto de recuperação é criado quando ocorre o primeiro backup. Em vez de esperar que o horário seja executado, pode desencadear manualmente a criação de um ponto de recuperação:

1. No grupo de proteção, certifique-se de que o estado da base de dados está **ok**.

    ![Um grupo de proteção, mostrando o estado da base de dados](./media/backup-azure-backup-sql/sqlbackup-recoverypoint.png)
1. Clique na base de dados e, em seguida, selecione Criar ponto de **recuperação**.

    ![Opte por criar um ponto de recuperação online](./media/backup-azure-backup-sql/sqlbackup-createrp.png)
1. No menu suspenso, selecione **proteção Online**. Em seguida, selecione **OK** para iniciar a criação de um ponto de recuperação em Azure.

    ![Comece a criar um ponto de recuperação em Azure](./media/backup-azure-backup-sql/sqlbackup-azure.png)
1. Pode ver o progresso do trabalho no espaço de trabalho **de monitorização.**

    ![Ver o progresso do trabalho na consola de monitorização](./media/backup-azure-backup-sql/sqlbackup-monitoring.png)

## <a name="recover-a-sql-server-database-from-azure"></a>Recuperar uma base de dados do SQL Server do Azure

Para recuperar uma entidade protegida, como uma base de dados do SQL Server, do Azure:

1. Abra a consola de gestão de servidores DPM. Vá ao espaço de trabalho de **recuperação** para ver os servidores que o DPM faz. Selecione a base de dados (neste exemplo, ReportServer$MSDPM2012). Selecione um tempo de **recuperação** que termine com **Online**.

    ![Selecione um ponto de recuperação](./media/backup-azure-backup-sql/sqlbackup-restorepoint.png)
1. Clique no nome da base de dados e selecione **Recuperar**.

    ![Recuperar uma base de dados do Azure](./media/backup-azure-backup-sql/sqlbackup-recover.png)
1. DPM mostra os detalhes do ponto de recuperação. Selecione **Seguinte**. Para substituir a base de dados, selecione o tipo de recuperação **Recuperar para a instância original do Servidor SQL**. Em seguida, selecione **Seguinte**.

    ![Recuperar uma base de dados da sua localização original](./media/backup-azure-backup-sql/sqlbackup-recoveroriginal.png)

    Neste exemplo, o DPM permite que a base de dados seja recuperada para outra instância do SQL Server ou para uma pasta de rede autónoma.
1. Na página **'Opções de Recuperação Especificação',** pode selecionar as opções de recuperação. Por exemplo, pode escolher o estrangulamento da largura de **banda da Rede** para acelerar a largura de banda que a recuperação utiliza. Em seguida, selecione **Seguinte**.
1. Na página **resumo,** vê a configuração de recuperação atual. Selecione **Recuperar**.

    O estado de recuperação mostra que a base de dados está a ser recuperada. Pode selecionar **Perto** para fechar o assistente e ver o progresso no espaço de trabalho **de Monitorização.**

    ![Iniciar o processo de recuperação](./media/backup-azure-backup-sql/sqlbackup-recoverying.png)

    Quando a recuperação estiver concluída, a base de dados restaurada é consistente com a aplicação.

## <a name="next-steps"></a>Passos Seguintes

Para mais informações, consulte [o Azure Backup FAQ](backup-azure-backup-faq.md).
