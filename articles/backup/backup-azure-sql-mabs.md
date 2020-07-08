---
title: Faça backup no SQL Server utilizando o Servidor de Backup Azure
description: Neste artigo, aprenda a configuração para fazer backup das bases de dados do SQL Server utilizando o Microsoft Azure Backup Server (MABS).
ms.topic: conceptual
ms.date: 03/24/2017
ms.openlocfilehash: 2bb172ca36f3f932fdaaf5b71e8fa183c04d1510
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84194189"
---
# <a name="back-up-sql-server-to-azure-by-using-azure-backup-server"></a>Faça backup no SQL Server para Azure utilizando o Servidor de Backup Azure

Este artigo ajuda-o a configurar cópias de segurança das bases de dados do SQL Server utilizando o Microsoft Azure Backup Server (MABS).

Para fazer uma base de dados do SQL Server e recuperá-la do Azure:

1. Crie uma política de backup para proteger as bases de dados do SQL Server no Azure.
1. Crie cópias de backup a pedido no Azure.
1. Recupere a base de dados em Azure.

## <a name="before-you-start"></a>Antes de começar

Antes de começar, certifique-se de que [instalou e preparou o Servidor de Backup Azure](backup-azure-microsoft-azure-backup.md).

## <a name="create-a-backup-policy"></a>Criar uma política de backup

Para proteger as bases de dados do SQL Server em Azure, primeiro crie uma política de backup:

1. No Servidor de Backup Azure, selecione o espaço de trabalho **de Proteção.**
1. Selecione **Novo** para criar um grupo de proteção.

    ![Criar um grupo de proteção no Servidor de Backup Azure](./media/backup-azure-backup-sql/protection-group.png)
1. Na página inicial, reveja as orientações sobre a criação de um grupo de proteção. Em seguida, selecione **Seguinte**.
1. Para o tipo de grupo de proteção, selecione **Servidores**.

    ![Selecione o tipo de grupo de proteção servers](./media/backup-azure-backup-sql/pg-servers.png)
1. Expanda a instância do SQL Server onde estão localizadas as bases de dados que pretende fazer. Vê as fontes de dados que podem ser monitorizadas a partir desse servidor. Expanda **todas as Ações SQL** e, em seguida, selecione as bases de dados que pretende fazer. Neste exemplo, selecionamos ReportServer$MSDPM2012 e ReportServer$MSDPM2012TempDB. Selecione **Seguinte**.

    ![Selecione uma base de dados do SQL Server](./media/backup-azure-backup-sql/pg-databases.png)
1. Nomeie o grupo de proteção e, em seguida, **selecione Eu quero proteção on-line**.

    ![Escolha um método de proteção de dados - proteção de curto prazo do disco ou proteção on-line do Azure](./media/backup-azure-backup-sql/pg-name.png)
1. Na página **'Especificar Objetivos de Curto Prazo',** inclua as entradas necessárias para criar pontos de backup para o disco.

    Neste exemplo, **a gama de retenção** é definida para *5 dias.* A frequência de **sincronização** de backup é definida para uma vez a cada *15 minutos*. **O Express Full Backup** está programado para *as 20:00*.

    ![Estabelecer metas de curto prazo para a proteção de backup](./media/backup-azure-backup-sql/pg-shortterm.png)

   > [!NOTE]
   > Neste exemplo, um ponto de backup é criado às 20:00 todos os dias. Os dados que foram modificados desde o ponto de reserva das 20:00 do dia anterior são transferidos. Este processo chama-se **Express Full Backup**. Embora os registos de transação sejam sincronizados a cada 15 minutos, se precisarmos de recuperar a base de dados às 21:00, então o ponto é criado reproduzindo os registos do último ponto de backup completo expresso, que é 20:00 neste exemplo.
   >
   >

1. Selecione **Seguinte**. O MABS mostra o espaço de armazenamento geral disponível. Também mostra a potencial utilização do espaço do disco.

    ![Configurar alocação de discos em MABS](./media/backup-azure-backup-sql/pg-storage.png)

    Por predefinição, o MABS cria um volume por fonte de dados (base de dados SQL Server). O volume é utilizado para a cópia de reserva inicial. Nesta configuração, o Logical Disk Manager (LDM) limita a proteção MABS a 300 fontes de dados (bases de dados do SQL Server). Para contornar esta limitação, selecione **Co-localizar dados no DPM Storage Pool**. Se utilizar esta opção, o MABS utiliza um único volume para várias fontes de dados. Esta configuração permite que o MABS proteja até 2.000 bases de dados do SQL Server.

    Se selecionar **automaticamente o crescimento dos volumes,** então o MABS pode explicar o aumento do volume de backup à medida que os dados de produção crescem. Se não selecionar **automaticamente o crescimento dos volumes,** o MABS limita o armazenamento de backup às fontes de dados do grupo de proteção.
1. Se for administrador, pode optar por transferir esta cópia de segurança inicial **automaticamente pela rede** e escolher a hora da transferência. Ou opte por transferir **manualmente** a cópia de segurança. Em seguida, selecione **Seguinte**.

    ![Escolha um método de criação de réplica no MABS](./media/backup-azure-backup-sql/pg-manual.png)

    A cópia de cópia de segurança inicial requer a transferência de toda a fonte de dados (base de dados SQL Server). Os dados de cópia de segurança movem-se do servidor de produção (computador SQL Server) para MABS. Se esta cópia de segurança for grande, então transferir os dados para a rede pode causar congestionamento da largura de banda. Por esta razão, os administradores podem optar por utilizar suportes amovíveis para transferir **manualmente**a cópia de segurança inicial . Ou podem transferir os dados **automaticamente pela rede** num momento especificado.

    Após os acabamentos de backup iniciais, as cópias de backup continuam incrementalmente na cópia inicial de backup. As cópias de segurança incrementais tendem a ser pequenas e são facilmente transferidas através da rede.
1. Escolha quando fazer uma verificação de consistência. Em seguida, selecione **Seguinte**.

    ![Escolha quando fazer uma verificação de consistência](./media/backup-azure-backup-sql/pg-consistent.png)

    O MABS pode verificar a integridade do ponto de reserva. Calcula a parte de verificação do ficheiro de cópia de segurança no servidor de produção (o computador SQL Server neste exemplo) e os dados de backup para esse ficheiro no MABS. Se o cheque encontrar um conflito, então o ficheiro de apoio no MABS é assumido como corrupto. O MABS corrige os dados de back-up enviando os blocos que correspondem ao desfasamento da parte de verificação. Como a verificação de consistência é uma operação intensiva de desempenho, os administradores podem optar por agendar a verificação de consistência ou executá-la automaticamente.
1. Selecione as fontes de dados para proteger em Azure. Em seguida, selecione **Seguinte**.

    ![Selecione fontes de dados para proteger em Azure](./media/backup-azure-backup-sql/pg-sqldatabases.png)
1. Se for administrador, pode escolher horários de backup e políticas de retenção que se adequam às políticas da sua organização.

    ![Escolha horários e políticas de retenção](./media/backup-azure-backup-sql/pg-schedule.png)

    Neste exemplo, as cópias de segurança são tomadas diariamente às 12:00 e 20:00.

    > [!TIP]
    > Para uma rápida recuperação, mantenha alguns pontos de recuperação de curto prazo no seu disco. Estes pontos de recuperação são utilizados para a recuperação operacional. O Azure serve como uma boa localização fora do local, proporcionando SLAs mais elevados e disponibilidade garantida.
    >
    > Utilize o Gestor de Proteção de Dados (DPM) para agendar backups do Azure depois de terminarem as cópias de segurança do disco local. Quando segue esta prática, a cópia de segurança mais recente do disco é copiada para o Azure.
    >

1. Escolha o calendário da política de retenção. Para obter mais informações sobre o funcionamento da política de retenção, consulte [use Azure Backup para substituir a sua infraestrutura de fita](backup-azure-backup-cloud-as-tape.md).

    ![Escolha uma política de retenção no MABS](./media/backup-azure-backup-sql/pg-retentionschedule.png)

    Neste exemplo:

    * Os reforços são feitos diariamente às 12:00 e às 20:00. Ficam parada por 180 dias.
    * O reforço no sábado às 12:00 é mantido por 104 semanas.
    * O reforço do último sábado do mês às 12:00 é mantido por 60 meses.
    * O reforço do último sábado de março às 12:00 está guardado por 10 anos.

    Depois de escolher uma política de retenção, selecione **Seguinte**.
1. Escolha como transferir a cópia de reserva inicial para Azure.

    * **A opção De rede automaticamente** segue o seu horário de backup para transferir os dados para a Azure.
    * Para obter mais informações sobre a Cópia de **Segurança Offline,** consulte [a visão geral da cópia de segurança offline](offline-backup-overview.md).

    Depois de escolher um mecanismo de transferência, selecione **Seguinte**.
1. Na página **Resumo,** reveja os detalhes da apólice. Em seguida, **selecione Criar grupo**. Pode selecionar **Close** e ver o progresso do trabalho no espaço de trabalho **de monitorização.**

    ![O progresso da criação do grupo de proteção](./media/backup-azure-backup-sql/pg-summary.png)

## <a name="create-on-demand-backup-copies-of-a-sql-server-database"></a>Criar cópias de backup a pedido de uma base de dados do SQL Server

Um ponto de recuperação é criado quando ocorre a primeira cópia de segurança. Em vez de esperar que o horário se esgotasse, pode desencadear manualmente a criação de um ponto de recuperação:

1. No grupo de proteção, certifique-se de que o estado da base de **dados**está OK .

    ![Um grupo de proteção, mostrando o estado da base de dados](./media/backup-azure-backup-sql/sqlbackup-recoverypoint.png)
1. Clique com o botão direito na base de dados e, em seguida, **selecione Criar ponto de recuperação**.

    ![Opte por criar um ponto de recuperação online](./media/backup-azure-backup-sql/sqlbackup-createrp.png)
1. No menu suspenso, selecione **Proteção Online**. Em seguida, selecione **OK** para iniciar a criação de um ponto de recuperação em Azure.

    ![Comece a criar um ponto de recuperação em Azure](./media/backup-azure-backup-sql/sqlbackup-azure.png)
1. Pode ver o progresso do trabalho no espaço de trabalho **de monitorização.**

    ![Ver o progresso do trabalho na consola de monitorização](./media/backup-azure-backup-sql/sqlbackup-monitoring.png)

## <a name="recover-a-sql-server-database-from-azure"></a>Recuperar uma base de dados do SQL Server do Azure

Para recuperar uma entidade protegida, como uma base de dados SQL Server, a partir de Azure:

1. Abra a consola de gestão do servidor DPM. Vá ao espaço de trabalho **recovery** para ver os servidores que o DPM faz recuar. Selecione a base de dados (neste exemplo, ReportServer$MSDPM2012). Selecione um **tempo de recuperação** que termina com **Online**.

    ![Selecione um ponto de recuperação](./media/backup-azure-backup-sql/sqlbackup-restorepoint.png)
1. Clique com o botão direito no nome da base de dados e selecione **Recuperar.**

    ![Recuperar uma base de dados do Azure](./media/backup-azure-backup-sql/sqlbackup-recover.png)
1. O DPM mostra os detalhes do ponto de recuperação. Selecione **Seguinte**. Para substituir a base de dados, selecione o tipo de recuperação **Recuperar para a instância original do SQL Server**. Em seguida, selecione **Seguinte**.

    ![Recuperar uma base de dados para a sua localização original](./media/backup-azure-backup-sql/sqlbackup-recoveroriginal.png)

    Neste exemplo, o DPM permite a recuperação da base de dados para outra instância do SQL Server ou para uma pasta de rede autónoma.
1. Na página **'Especificar Opções de Recuperação',** pode selecionar as opções de recuperação. Por exemplo, pode escolher **o uso da largura de banda da rede** para acelerar a largura de banda que a recuperação utiliza. Em seguida, selecione **Seguinte**.
1. Na página **Resumo,** vê a configuração atual de recuperação. Selecione **Recuperar**.

    O estado de recuperação mostra a base de dados a ser recuperada. Pode selecionar **Perto** para fechar o assistente e ver o progresso no espaço de trabalho **de monitorização.**

    ![Inicie o processo de recuperação](./media/backup-azure-backup-sql/sqlbackup-recoverying.png)

    Quando a recuperação estiver concluída, a base de dados restaurada é consistente com a aplicação.

### <a name="next-steps"></a>Próximos passos

Para mais informações, consulte [a Azure Backup FAQ](backup-azure-backup-faq.md).
