---
title: Servidor SQL de backup usando o servidor de backup Azure
description: Neste artigo, aprenda os passos de configuração para a cópia de segurança das bases de dados do SQL Server utilizando o Microsoft Azure Backup Server (MABS).
ms.topic: conceptual
ms.date: 03/24/2017
ms.openlocfilehash: 461faa2c88b8db9c1e3b2f9af19783b7d6b7fa07
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024012"
---
# <a name="back-up-sql-server-to-azure-with-azure-backup-server"></a>Backup SQL Server para Azure Com servidor de backup Azure

Este artigo leva-o através dos passos de configuração para cópia de segurança das bases de dados do SQL Server utilizando o Microsoft Azure Backup Server (MABS).

A gestão da base de dados SQL Server de backup para Azure e recuperação do Azure envolve três etapas:

1. Crie uma política de backup para proteger as bases de dados do SQL Server para o Azure.
2. Crie cópias de reserva a pedido para o Azure.
3. Recupere a base de dados do Azure.

## <a name="before-you-start"></a>Antes de começar

Antes de começar, certifique-se de que [instalou e preparou o Servidor](backup-azure-microsoft-azure-backup.md)de Backup Azure .

## <a name="create-a-backup-policy-to-protect-sql-server-databases-to-azure"></a>Criar uma política de backup para proteger as bases de dados do SQL Server para o Azure

1. No Azure Backup Server UI, clique no espaço de trabalho **de Proteção.**
2. Na fita da ferramenta, clique em **New** para criar um novo grupo de proteção.

    ![Criar grupo de proteção](./media/backup-azure-backup-sql/protection-group.png)
3. O MABS mostra o ecrã de partida com a orientação para a criação de um Grupo de **Proteção**. Clique em **Seguinte**.
4. Selecione **Servidores**.

    ![Selecione Tipo de Grupo de Proteção - 'Servidores'](./media/backup-azure-backup-sql/pg-servers.png)
5. Expanda a máquina SQL Server onde estão presentes as bases de dados a serem apoiadas. O MABS mostra várias fontes de dados que podem ser apoiadas a partir desse servidor. Expanda as **Todas as Ações SQL** e selecione as bases de dados (neste caso selecionamos reportServer$MSDPM2012 e ReportServer$MSDPM2012TempDB) para ser apoiado. Clique em **Seguinte**.

    ![Selecione SQL DB](./media/backup-azure-backup-sql/pg-databases.png)
6. Forneça um nome para o grupo de proteção e selecione a caixa de verificação **de proteção online I want.**

    ![Método de Proteção de Dados - disco de curto prazo e Azure online](./media/backup-azure-backup-sql/pg-name.png)
7. No ecrã **'Especificar Objetivos de Curto Prazo',** inclua as inputs necessárias para criar pontos de backup para o disco.

    Aqui vemos que a gama de **retenção** está definida para *5 dias*, a frequência de **sincronização** está definida para uma vez a cada *15 minutos*, que é a frequência a que a cópia de segurança é tomada. **Express Full Backup** está marcado para as *20h00.*

    ![Objetivos de curto prazo](./media/backup-azure-backup-sql/pg-shortterm.png)

   > [!NOTE]
   > Às 20:00 (de acordo com a entrada do ecrã) é criado um ponto de backup todos os dias, transferindo os dados que foram modificados a partir do ponto de backup das 20:00 do dia anterior. Este processo chama-se **Express Full Backup**. Enquanto os registos de transação são sincronizados a cada 15 minutos, se houver necessidade de recuperar a base de dados às 21:00 – então o ponto é criado reproduzindo os registos do último ponto de backup expresso completo (20h neste caso).
   >
   >

8. Clique em **Seguinte**

    O MABS mostra o espaço de armazenamento global disponível e a potencial utilização do espaço do disco.

    ![Atribuição de disco](./media/backup-azure-backup-sql/pg-storage.png)

    Por padrão, o MABS cria um volume por fonte de dados (base de dados do SQL Server) que é utilizado para a cópia inicial de cópia de cópia de cópia de cópia. Utilizando esta abordagem, o Gestor lógico de Discos (LDM) limita a proteção MABS a 300 fontes de dados (bases de dados do Servidor SQL). Para contornar esta limitação, selecione os **dados co-localizar no DPM Storage Pool,** opção. Se utilizar esta opção, o MABS utiliza um único volume para várias fontes de dados, o que permite ao MABS proteger até 2000 bases de dados SQL.

    Se **crescer automaticamente a opção** de volumes é selecionada, o MABS pode explicar o aumento do volume de cópia de segurança à medida que os dados de produção crescem. Se **aumentar automaticamente a opção de volumes** não é selecionada, o MABS limita o armazenamento de backup utilizado para as fontes de dados do grupo de proteção.
9. Os administradores têm a opção de transferir manualmente esta cópia de segurança inicial (fora da rede) para evitar o congestionamento da largura de banda ou sobre a rede. Também podem configurar o tempo em que a transferência inicial pode acontecer. Clique em **Seguinte**.

    ![Método de replicação inicial](./media/backup-azure-backup-sql/pg-manual.png)

    A cópia inicial de cópia de cópia de cópia de cópia de segurança requer a transferência de toda a fonte de dados (base de dados do Servidor SQL) do servidor de produção (máquina SQL Server) para MABS. Estes dados podem ser grandes e a transferência dos dados para a rede pode exceder a largura de banda. Por esta razão, os administradores podem optar por transferir a cópia de segurança inicial: **Manualmente** (utilizando meios amovíveis) para evitar o congestionamento da largura de banda, ou **automaticamente sobre a rede** (num momento determinado).

    Uma vez concluída a cópia inicial de cópia de segurança, as restantes cópias de segurança são cópias incrementais na cópia inicial de cópia de cópia de cópia de cópia. As cópias de segurança incrementais tendem a ser pequenas e são facilmente transferidas através da rede.
10. Escolha quando deseja que a verificação de consistência seja executada e clique em **Next**.

    ![Verificação de consistência](./media/backup-azure-backup-sql/pg-consistent.png)

    O MABS pode efetuar uma verificação de consistência para verificar a integridade do ponto de reserva. Calcula a verificação do ficheiro de backup no servidor de produção (máquina SQL Server neste cenário) e os dados de backup desse ficheiro no MABS. Em caso de conflito, presume-se que o ficheiro de back-up na MABS é corrupto. A MABS retifica os dados de back-up enviando os blocos correspondentes à incompatibilidade de verificação. Como a verificação de consistência é uma operação intensiva de desempenho, os administradores têm a opção de agendar a verificação de consistência ou executá-la automaticamente.
11. Para especificar a proteção on-line das fontes de dados, selecione as bases de dados a proteger para O Azure e clique **em Next**.

    ![Selecione fontes de dados](./media/backup-azure-backup-sql/pg-sqldatabases.png)
12. Os administradores podem escolher horários de backup e políticas de retenção que se adequam às suas políticas de organização.

    ![Horário e Retenção](./media/backup-azure-backup-sql/pg-schedule.png)

    Neste exemplo, as cópias de segurança são tomadas uma vez por dia às 12:00 e 20:00 (parte inferior do ecrã)

    > [!NOTE]
    > É uma boa prática ter alguns pontos de recuperação a curto prazo no disco, para uma rápida recuperação. Estes pontos de recuperação são utilizados para a "recuperação operacional". O Azure serve como uma boa localização fora do local com SLAs mais elevado slas e disponibilidade garantida.
    >
    >

    **Boas Práticas**: Certifique-se de que as cópias de segurança do Azure estão programadas após a conclusão das cópias de segurança do disco local utilizando o DPM. Isto permite que a última cópia de cópia do disco para Azure.

13. Escolha o calendário da política de retenção. Os detalhes sobre como a política de retenção funciona são fornecidos na Use Azure Backup para substituir o seu artigo de [infraestrutura](backup-azure-backup-cloud-as-tape.md)de fita.

    ![Política de Retenção](./media/backup-azure-backup-sql/pg-retentionschedule.png)

    Neste exemplo:

    * As cópias de segurança são tomadas uma vez por dia às 12:00 e 20:00 (parte inferior do ecrã) e são retidas durante 180 dias.
    * O reforço no sábado, às 12:00. é retido por 104 semanas
    * O reforço no sábado passado às 12:00. é retido por 60 meses
    * O reforço no último sábado de março às 12:00 p.m. é retido por 10 anos
14. Clique em **Seguinte** e selecione a opção adequada para transferir a cópia de cópia inicial de cópia de reserva para o Azure. Pode escolher **automaticamente sobre a rede** ou a Cópia de Segurança **Offline**.

    * **Automaticamente, por cima da rede** transfere os dados de backup para o Azure de acordo com o calendário escolhido para backup.
    * Como funciona **o backup offline** é explicado na visão geral da cópia de segurança [offline](offline-backup-overview.md).

    Escolha o mecanismo de transferência relevante para enviar a cópia de cópia de cópia inicial para o Azure e clique **em Next**.
15. Assim que rever os detalhes da política no ecrã **Resumo,** clique no botão **de grupo Criar** para completar o fluxo de trabalho. Pode clicar no botão **Fechar** e monitorizar o progresso do trabalho no espaço de trabalho de monitorização.

    ![Criação do Grupo de Proteção Em Curso](./media/backup-azure-backup-sql/pg-summary.png)

## <a name="on-demand-backup-of-a-sql-server-database"></a>Backup a pedido de uma base de dados do SQL Server

Embora os passos anteriores tenham criado uma política de backup, um "ponto de recuperação" só é criado quando ocorre o primeiro backup. Em vez de esperar que o programador inicie o arranque, os passos abaixo desencadeiam a criação de um ponto de recuperação manualmente.

1. Aguarde até que o estado do grupo de proteção mostre **OK** para a base de dados antes de criar o ponto de recuperação.

    ![Membros do Grupo de Proteção](./media/backup-azure-backup-sql/sqlbackup-recoverypoint.png)
2. Clique à direita na base de dados e selecione **Create Recovery Point**.

    ![Criar ponto de recuperação on-line](./media/backup-azure-backup-sql/sqlbackup-createrp.png)
3. Escolha **a Proteção Online** no menu suspenso e clique em **OK**. Isto inicia a criação de um ponto de recuperação em Azure.

    ![Criar ponto de recuperação](./media/backup-azure-backup-sql/sqlbackup-azure.png)
4. Pode ver o progresso do trabalho no espaço de trabalho de **Monitorização,** onde encontrará um trabalho em andamento como o representado na próxima figura.

    ![Consola de monitorização](./media/backup-azure-backup-sql/sqlbackup-monitoring.png)

## <a name="recover-a-sql-server-database-from-azure"></a>Recuperar uma base de dados do SQL Server do Azure

São necessários os seguintes passos para recuperar uma entidade protegida (base de dados Do Servidor SQL) do Azure.

1. Abra a Consola de Gestão de Servidores DPM. Navegue para o espaço de trabalho **de recuperação** onde pode ver os servidores apoiados pelo DPM. Navegue na base de dados necessária (neste caso ReportServer$MSDPM2012). Selecione uma **Recuperação a partir do** tempo que termina com **Online**.

    ![Selecione ponto de recuperação](./media/backup-azure-backup-sql/sqlbackup-restorepoint.png)
2. Clique no nome da base de dados e clique em **Recuperar**.

    ![Recuperar de Azure](./media/backup-azure-backup-sql/sqlbackup-recover.png)
3. DPM mostra os detalhes do ponto de recuperação. Clique em **Seguinte**. Para substituir a base de dados, selecione o tipo de recuperação **Recuperar para a instância original do Servidor SQL**. Clique em **Seguinte**.

    ![Recuperar para localização original](./media/backup-azure-backup-sql/sqlbackup-recoveroriginal.png)

    Neste exemplo, o DPM permite a recuperação da base de dados para outra instância do SQL Server ou para uma pasta de rede autónoma.
4. No ecrã de opções de **recuperação especificada,** pode selecionar as opções de recuperação, como o estrangulamento da largura de banda da rede para acelerar a largura de banda utilizada pela recuperação. Clique em **Seguinte**.
5. No ecrã **Resumo,** vê-se todas as configurações de recuperação fornecidas até agora. Clique em **recuperar**.

    O estado de recuperação mostra que a base de dados está a ser recuperada. Pode clicar **perto** de fechar o assistente e ver o progresso no espaço de trabalho **de Monitorização.**

    ![Iniciar processo de recuperação](./media/backup-azure-backup-sql/sqlbackup-recoverying.png)

    Uma vez concluída a recuperação, a base de dados restaurada é consistente com a aplicação.

### <a name="next-steps"></a>Passos seguintes

* [Azure Backup FAQ](backup-azure-backup-faq.md)
