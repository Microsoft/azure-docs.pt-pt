---
title: O Azure Backup para cargas de trabalho do SQL Server com o Azure Backup Server
description: Uma introdução ao backup bancos de dados do SQL Server com o Azure Backup Server
services: backup
author: kasinh
manager: vvithal
ms.service: backup
ms.topic: conceptual
ms.date: 03/24/2017
ms.author: kasinh
ms.openlocfilehash: 72b2368979f0c9e546e1c7ef7fc462bf1d64c947
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60782077"
---
# <a name="back-up-sql-server-to-azure-with-azure-backup-server"></a>Criar cópias de segurança do SQL Server para o Azure com o Azure Backup Server
Este artigo orienta-o através dos passos de configuração para cópia de segurança de bases de dados do SQL Server com o Microsoft Azure Backup Server (MABS).

A gestão de cópia de segurança do SQL Server da base de dados para o Azure e a recuperação do Azure envolve três etapas:

1. Crie uma política de cópia de segurança para proteger bases de dados do SQL Server para o Azure.
2. Crie cópias de segurança a pedido para o Azure.
3. Recupere a base de dados do Azure.

## <a name="before-you-start"></a>Antes de começar
Antes de começar, certifique-se de que tenha [instalado e preparar o servidor de cópia de segurança do Azure](backup-azure-microsoft-azure-backup.md).

## <a name="create-a-backup-policy-to-protect-sql-server-databases-to-azure"></a>Criar uma política de cópia de segurança para proteger bases de dados do SQL Server para o Azure
1. Na IU de servidor de cópia de segurança do Azure, clique nas **proteção** área de trabalho.
2. No Friso, clique em **New** para criar um novo grupo de proteção.

    ![Criar grupo de proteção](./media/backup-azure-backup-sql/protection-group.png)
3. MABS mostra o ecrã de início com a documentação de orientação sobre como criar uma **grupo de proteção**. Clique em **Seguinte**.
4. Selecione **servidores**.

    ![Selecionar tipo de grupo de proteção - "Servidores"](./media/backup-azure-backup-sql/pg-servers.png)
5. Expanda o computador de SQL Server onde as bases de dados a cópia de segurança estão presentes. MABS mostra várias origens de dados que podem ser submetidas a backup desse servidor. Expanda a **todas as partilhas de SQL** e selecione as bases de dados (neste caso selecionamos ReportServer$ MSDPM2012 e ReportServer$ MSDPM2012TempDB) a cópia de segurança. Clique em **Seguinte**.

    ![Selecione a BD SQL](./media/backup-azure-backup-sql/pg-databases.png)
6. Forneça um nome para o grupo de proteção e selecione o **pretendo proteção online** caixa de verificação.

    ![Método de proteção de dados - disco de curto prazo e Online do Azure](./media/backup-azure-backup-sql/pg-name.png)
7. Na **especificar objetivos a curto prazo** ecrã, incluem as entradas necessárias para criar pontos de cópia de segurança em disco.

    Aqui, Vemos que **período de retenção** está definida como *5 dias*, **frequência de sincronização** está definido como uma vez cada *15 minutos* que é o frequência com que é feita a cópia de segurança. **Cópia de segurança completa rápida** está definido como *8:00 P.M*.

    ![Objetivos de curto prazo](./media/backup-azure-backup-sql/pg-shortterm.png)

   > [!NOTE]
   > Às 20:00: 00 (de acordo com a entrada de tela) é criado um ponto de cópia de segurança todos os dias, transferindo os dados que tem sido modificados de ponto de cópia de segurança das 20:00 do dia anterior. Este processo é denominado **Express cópia de segurança completa**. Durante a transação registos são sincronizados a cada 15 minutos, se for necessário para recuperar a base de dados até 21 horas –, em seguida, o ponto é criado ao reproduzir os registos dos últimos expressar o ponto de cópia de segurança completa (8 horas neste caso).
   >
   >

8. Clique em **Seguinte**

    MABS mostra o espaço de armazenamento geral disponível e a utilização do espaço em disco potencial.

    ![Atribuição do disco](./media/backup-azure-backup-sql/pg-storage.png)

    Por predefinição, o MABS cria um volume por origem de dados (banco de dados do SQL Server) que é utilizado para a cópia de segurança inicial. Com essa abordagem, o Gestor de discos lógicos (LDM) limita a proteção do MABS para origens de dados de 300 (bases de dados do SQL Server). Para contornar esta limitação, selecione o **colocalizar dados no agrupamento de armazenamento do DPM**, opção. Se utilizar esta opção, MABS utiliza um único volume de várias origens de dados, que permite o MABS proteger até 2000 bases de dados do SQL.

    Se **aumentar automaticamente os volumes** opção estiver selecionada, MABS podem contribuir para o maior volume de cópia de segurança à medida que aumenta os dados de produção. Se **aumentar automaticamente os volumes** opção não estiver selecionada, MABS limita o armazenamento de cópia de segurança utilizado para as origens de dados no grupo de proteção.
9. Os administradores recebem a opção de transferência esta cópia de segurança inicial manualmente (desativado rede) para evitar o congestionamento de largura de banda ou através da rede. Pode também configurar o tempo em que a transferência inicial pode acontecer. Clique em **Seguinte**.

    ![Método de replicação inicial](./media/backup-azure-backup-sql/pg-manual.png)

    A cópia de segurança inicial requer transferência de origem de dados completa (base de dados do SQL Server) de servidor de produção (máquina do SQL Server) para o MABS. Estes dados podem ser grandes e transferência de dados através da rede poderá exceder a largura de banda. Por esse motivo, os administradores podem optar transferir a cópia de segurança inicial: **Manualmente** (usando mídia removível) para evitar o congestionamento de largura de banda, ou **automaticamente através da rede** (numa hora específica).

    Quando a cópia de segurança inicial estiver concluída, o restante das cópias de segurança são cópias de segurança incrementais em cópia de segurança inicial. Cópias de segurança incrementais tendem a ser pequeno e são facilmente transferidas através da rede.
10. Escolha quando pretende que a verificação de consistência para executar e clique em **seguinte**.

    ![Verificação de consistência](./media/backup-azure-backup-sql/pg-consistent.png)

    MABS pode realizar uma consistência verificação para verificação da integridade do ponto de cópia de segurança. Ele calcula a soma de verificação do arquivo de backup no servidor de produção (máquina do SQL Server neste cenário) e os dados de cópia de segurança para esse ficheiro no MABS. No caso de conflito, é assumido que o ficheiro de cópia de segurança do MABS está danificado. MABS rectifies os dados de cópia de segurança, enviando os blocos correspondente para o erro de correspondência de soma de verificação. Como a verificação de consistência é uma operação intensiva do desempenho, os administradores têm a opção de agendamento a verificação de consistência ou executá-lo automaticamente.
11. Para especificar a proteção online das origens de dados, selecione as bases de dados a ser protegido para o Azure e clique em **seguinte**.

    ![Selecione as origens de dados](./media/backup-azure-backup-sql/pg-sqldatabases.png)
12. Os administradores podem optar agendas de cópia de segurança e políticas de retenção que se adequam às suas políticas de organização.

    ![Agendamento e retenção](./media/backup-azure-backup-sql/pg-schedule.png)

    Neste exemplo, as cópias de segurança são executadas uma vez por dia às 12:00 e 8 PM (parte inferior do ecrã)

    > [!NOTE]
    > É recomendável ter alguns pontos de recuperação de curta duração no disco, para uma recuperação rápida. Esses pontos de recuperação são utilizados para "recuperação operacional". O Azure funciona como uma bom fora do local com SLAs superior e disponibilidade garantida.
    >
    >

    **Melhor prática**: Certifique-se de que estão agendadas de cópias de segurança do Azure após a conclusão das cópias de segurança de disco local com o DPM. Isto permite que a última cópia de segurança do disco seja copiado para o Azure.

13. Escolha o agendamento de política de retenção. Os detalhes sobre como funciona a política de retenção são fornecidos no [utilização do Azure Backup para substituir o seu artigo de infraestrutura da banda](backup-azure-backup-cloud-as-tape.md).

    ![Política de retenção](./media/backup-azure-backup-sql/pg-retentionschedule.png)

    Neste exemplo:

    * As cópias de segurança são efetuadas uma vez por dia às 12:00 e 8 PM (parte inferior do ecrã) e são mantidas durante 180 dias.
    * A cópia de segurança no sábados às 12:00h são retidos durante semanas 104
    * A cópia de segurança no último sábados às 12:00h são retidos durante 60 meses
    * A cópia de segurança no último Sábado de Março, às 12:00h são retidos durante 10 anos
14. Clique em **seguinte** e selecione a opção adequada para transferir a cópia de segurança inicial para o Azure. Pode escolher **automaticamente através da rede** ou **cópia de segurança Offline**.

    * **Automaticamente através da rede** transfere os dados de cópia de segurança para o Azure de acordo com o agendamento escolhido para cópia de segurança.
    * Como **cópia de segurança Offline** funciona é explicada na [fluxo de trabalho de cópia de segurança Offline na cópia de segurança do Azure](backup-azure-backup-import-export.md).

    Escolha o mecanismo de transferência relevantes para enviar a cópia de segurança inicial para o Azure e clique em **seguinte**.
15. Depois de rever os detalhes da política no **resumo** ecrã, clique nas **criar grupo** botão para concluir o fluxo de trabalho. Pode clicar a **fechar** botão e monitorizar o progresso da tarefa na área de trabalho de monitorização.

    ![Criação de grupo de proteção em curso](./media/backup-azure-backup-sql/pg-summary.png)

## <a name="on-demand-backup-of-a-sql-server-database"></a>Cópia de segurança a pedido de uma base de dados do SQL Server
Enquanto os passos anteriores criaram uma política de cópia de segurança, é criado um "ponto de recuperação" apenas quando ocorre a primeira cópia de segurança. Em vez de esperar o agendador de participar, os passos abaixo acionador a criação de uma recuperação de ponto de manualmente.

1. Aguarde até que o estado do grupo de proteção é apresentado **OK** para a base de dados antes de criar o ponto de recuperação.

    ![Membros do grupo de proteção](./media/backup-azure-backup-sql/sqlbackup-recoverypoint.png)
2. Com o botão direito na base de dados e selecione **criar ponto de recuperação**.

    ![Criar ponto de recuperação Online](./media/backup-azure-backup-sql/sqlbackup-createrp.png)
3. Escolher **proteção Online** no menu pendente e clique **OK**. Esta ação inicia a criação de um ponto de recuperação no Azure.

    ![Criar ponto de recuperação](./media/backup-azure-backup-sql/sqlbackup-azure.png)
4. Pode ver o progresso da tarefa no **monitorização** onde encontrará em curso de área de trabalho da tarefa, como aquele descrito na figura seguinte.

    ![Consola de monitorização](./media/backup-azure-backup-sql/sqlbackup-monitoring.png)

## <a name="recover-a-sql-server-database-from-azure"></a>Recuperar uma base de dados do SQL Server a partir do Azure
Os seguintes passos são necessários para recuperar uma entidade protegida (base de dados do SQL Server) a partir do Azure.

1. Abra o Console de gerenciamento de servidor do DPM. Navegue para **recuperação** área de trabalho onde pode ver os servidores de uma cópia de segurança pelo DPM. Procure essa base de dados (neste caso ReportServer$ MSDPM2012). Selecione um **recuperação a partir de** tempo que termina com **Online**.

    ![Selecione o ponto de recuperação](./media/backup-azure-backup-sql/sqlbackup-restorepoint.png)
2. O nome de base de dados com o botão direito e clique em **recuperar**.

    ![Recuperar a partir do Azure](./media/backup-azure-backup-sql/sqlbackup-recover.png)
3. O DPM mostra os detalhes dos pontos de recuperação. Clique em **Seguinte**. Para substituir a base de dados, selecione o tipo de recuperação **recuperar na instância original do SQL Server**. Clique em **Seguinte**.

    ![Recuperar para a localização Original](./media/backup-azure-backup-sql/sqlbackup-recoveroriginal.png)

    Neste exemplo, o DPM permite recuperação da base de dados para outra instância do SQL Server ou para uma pasta de rede autónomo.
4. Na **opções especificar recuperação** ecrã, pode selecionar as opções de recuperação, como a limitação da utilização de largura de banda de rede para limitar a largura de banda utilizada pela recuperação. Clique em **Seguinte**.
5. Na **resumo** ecrã, verá todas as configurações de recuperação até ao momento fornecidas. Clique em **recuperar**.

    O estado de recuperação mostra a base de dados a ser recuperado. Pode clicar em **feche** para fechar o assistente e ver o progresso no **monitorização** área de trabalho.

    ![Iniciar o processo de recuperação](./media/backup-azure-backup-sql/sqlbackup-recoverying.png)

    Quando a recuperação estiver concluída, a base de dados restaurada é consistente com a aplicação.

### <a name="next-steps"></a>Passos Seguintes:
• [FAQ sobre cópia de segurança do azure](backup-azure-backup-faq.md)
