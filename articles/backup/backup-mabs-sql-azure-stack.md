---
title: Back up SQL Server cargas de trabalho em Azure Stack
description: Neste artigo, aprenda a configurar o Microsoft Azure Backup Server (MABS) para proteger as bases de dados do SQL Server no Azure Stack.
ms.topic: conceptual
ms.date: 06/08/2018
ms.openlocfilehash: 03211e1147f96429a8406c4c95654161ed2bf308
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74172317"
---
# <a name="back-up-sql-server-on-azure-stack"></a>Back up SQL Server em Azure Stack

Utilize este artigo para configurar o Microsoft Azure Backup Server (MABS) para proteger as bases de dados do Servidor SQL no Azure Stack.

A gestão da base de dados SQL Server de backup para Azure e recuperação do Azure envolve três etapas:

1. Criar uma política de backup para proteger as bases de dados do SQL Server
2. Criar cópias de backup a pedido
3. Recuperar a base de dados dos Discos, e do Azure

## <a name="before-you-start"></a>Antes de começar

[Instale e prepare](backup-mabs-install-azure-stack.md)o Servidor de Backup Azure .

## <a name="create-a-backup-policy-to-protect-sql-server-databases-to-azure"></a>Criar uma política de backup para proteger as bases de dados do SQL Server para o Azure

1. No Azure Backup Server UI, clique no espaço de trabalho **de Proteção.**

2. Na fita da ferramenta, clique em **New** para criar um novo grupo de proteção.

    ![Criar grupo de proteção](./media/backup-azure-backup-sql/protection-group.png)

    O Servidor de Backup Azure inicia o assistente do Grupo de Proteção, que o leva através da criação de um **Grupo de Proteção**. Clique em **Seguinte**.

3. No ecrã **Select Protection Group Type,** selecione **Servers**.

    ![Selecione Tipo de Grupo de Proteção - 'Servidores'](./media/backup-azure-backup-sql/pg-servers.png)

4. No ecrã **Select Group Members,** a lista de membros disponíveis apresenta as várias fontes de dados. Clique **+** para expandir uma pasta e revele as subpastas. Clique na caixa de verificação para selecionar um item.

    ![Selecione SQL DB](./media/backup-azure-backup-sql/pg-databases.png)

    Todos os itens selecionados aparecem na lista de membros selecionados. Depois de selecionar os servidores ou bases de dados que pretende proteger, clique em **Seguinte**.

5. No ecrã **Select Data Protection Method,** forneça um nome para o grupo de proteção e selecione a caixa de verificação **de proteção online I want.**

    ![Método de Proteção de Dados - disco de curto prazo & Azure Online](./media/backup-azure-backup-sql/pg-name.png)

6. No ecrã **'Especificar Objetivos de Curto Prazo',** inclua as inputs necessárias para criar pontos de backup para o disco e clique em **Next**.

    No exemplo, a gama de **retenção** é de **5 dias,** a frequência de **sincronização** é uma vez a cada **15 minutos,** que é a frequência de backup. **Express Full Backup** está marcado para as **20h00.**

    ![Objetivos de curto prazo](./media/backup-azure-backup-sql/pg-shortterm.png)

   > [!NOTE]
   > No exemplo mostrado, às 20:00 todos os dias é criado um ponto de backup transferindo os dados modificados do ponto de backup das 20:00 do dia anterior. Este processo chama-se **Express Full Backup**. Os registos de transações são sincronizados a cada 15 minutos. Se precisar de recuperar a base de dados às 21:00, o ponto é criado a partir dos registos do último ponto de backup expresso completo (20h neste caso).
   >
   >

7. No ecrã de atribuição de **discos De revisão,** verifique o espaço de armazenamento global disponível e o potencial espaço do disco. Clique em **Seguinte**.

8. No **Método escolha de criação de réplicas,** escolha como criar o seu primeiro ponto de recuperação. Pode transferir a cópia de segurança inicial manualmente (fora da rede) para evitar o congestionamento da largura de banda ou sobre a rede. Se optar por guardar para transferir a primeira cópia de segurança, pode especificar o tempo para a transferência inicial. Clique em **Seguinte**.

    ![Método de replicação inicial](./media/backup-azure-backup-sql/pg-manual.png)

    A cópia inicial de cópia de cópia de cópia de cópia de segurança requer a transferência de toda a fonte de dados (base de dados do Servidor SQL) do servidor de produção (máquina SQL Server) para o Servidor de Backup Azure. Estes dados podem ser grandes e a transferência dos dados para a rede pode exceder a largura de banda. Por esta razão, pode optar por transferir a cópia de segurança inicial: **Manualmente** (utilizando meios amovíveis) para evitar o congestionamento da largura de banda, ou **automaticamente sobre a rede** (num momento determinado).

    Uma vez concluída a cópia inicial de cópia de segurança, as restantes cópias de segurança são cópias incrementais na cópia inicial de cópia de cópia de cópia de cópia. As cópias de segurança incrementais tendem a ser pequenas e são facilmente transferidas através da rede.

9. Escolha quando deseja que a verificação de consistência seja executada e clique em **Next**.

    ![Verificação de consistência](./media/backup-azure-backup-sql/pg-consistent.png)

    O Azure Backup Server realiza uma verificação de consistência na integridade do ponto de backup. O Azure Backup Server calcula a verificação do ficheiro de backup no servidor de produção (máquina SQL Server neste cenário) e os dados de backup desse ficheiro. Se houver um conflito, assumiu-se que o ficheiro de reserva do Servidor de Backup do Azure é corrupto. O Azure Backup Server retifica os dados de backup enviando os blocos correspondentes à incompatibilidade de verificação. Como as verificações de consistência são intensivas em desempenho, pode agendar a verificação de consistência ou executá-la automaticamente.

10. Para especificar a proteção on-line das fontes de dados, selecione as bases de dados a proteger para O Azure e clique **em Next**.

    ![Selecione fontes de dados](./media/backup-azure-backup-sql/pg-sqldatabases.png)

11. Escolha horários de backup e políticas de retenção que se adequam às políticas da organização.

    ![Horário e Retenção](./media/backup-azure-backup-sql/pg-schedule.png)

    Neste exemplo, as cópias de segurança são tomadas uma vez por dia às 12:00 e 20:00 (parte inferior do ecrã)

    > [!NOTE]
    > É uma boa prática ter alguns pontos de recuperação a curto prazo no disco, para uma rápida recuperação. Estes pontos de recuperação são utilizados para a recuperação operacional. O Azure serve como uma boa localização fora do local com SLAs mais elevado slas e disponibilidade garantida.
    >
    >

    **Boas Práticas**: Se agendar backups para o Azure para começar depois de concluídas as cópias de segurança do disco local, as cópias mais recentes do disco são sempre copiadas para o Azure.

12. Escolha o calendário da política de retenção. Os detalhes sobre como a política de retenção funciona são fornecidos na Use Azure Backup para substituir o seu artigo de [infraestrutura](backup-azure-backup-cloud-as-tape.md)de fita.

    ![Política de retenção](./media/backup-azure-backup-sql/pg-retentionschedule.png)

    Neste exemplo:

    * As cópias de segurança são tomadas uma vez por dia às 12:00 e 20:00 (parte inferior do ecrã) e são retidas durante 180 dias.
    * O reforço no sábado, às 12:00. é retido por 104 semanas
    * O reforço no sábado passado às 12:00. é retido por 60 meses
    * O reforço no último sábado de março às 12:00 p.m. é retido por 10 anos
13. Clique em **Seguinte** e selecione a opção adequada para transferir a cópia de cópia inicial de cópia de reserva para o Azure. Pode escolher **automaticamente sobre a rede**

14. Assim que rever os detalhes da política no ecrã **Resumo,** clique em **Criar grupo** para completar o fluxo de trabalho. Pode clicar em **Fechar** e monitorizar o progresso do trabalho na Monitorização do espaço de trabalho.

    ![Criação do Grupo de Proteção Em Curso](./media/backup-azure-backup-sql/pg-summary.png)

## <a name="on-demand-backup-of-a-sql-server-database"></a>Backup a pedido de uma base de dados do SQL Server

Embora os passos anteriores tenham criado uma política de backup, um "ponto de recuperação" só é criado quando ocorre o primeiro backup. Em vez de esperar que o programador inicie o arranque, os passos abaixo desencadeiam a criação de um ponto de recuperação manualmente.

1. Aguarde até que o estado do grupo de proteção mostre **OK** para a base de dados antes de criar o ponto de recuperação.

    ![Membros do Grupo de Proteção](./media/backup-azure-backup-sql/sqlbackup-recoverypoint.png)
2. Clique à direita na base de dados e selecione **Create Recovery Point**.

    ![Criar ponto de recuperação on-line](./media/backup-azure-backup-sql/sqlbackup-createrp.png)
3. Escolha **a Proteção Online** no menu suspenso e clique em **OK** para iniciar a criação de um ponto de recuperação em Azure.

    ![Criar ponto de recuperação](./media/backup-azure-backup-sql/sqlbackup-azure.png)
4. Veja o progresso do trabalho no espaço de trabalho **de Monitorização.**

    ![Consola de monitorização](./media/backup-azure-backup-sql/sqlbackup-monitoring.png)

## <a name="recover-a-sql-server-database-from-azure"></a>Recuperar uma base de dados do SQL Server do Azure

São necessários os seguintes passos para recuperar uma entidade protegida (base de dados Do Servidor SQL) do Azure.

1. Abra a consola de gestão do servidor de backup Azure. Navegue para o espaço de trabalho **de recuperação** onde pode ver os servidores protegidos. Navegue na base de dados necessária (neste caso ReportServer$MSDPM2012). Selecione uma **Recuperação a partir do** tempo especificado como um ponto **Online.**

    ![Selecione ponto de recuperação](./media/backup-azure-backup-sql/sqlbackup-restorepoint.png)
2. Clique no nome da base de dados e clique em **Recuperar**.

    ![Recuperar de Azure](./media/backup-azure-backup-sql/sqlbackup-recover.png)
3. O MABS mostra os detalhes do ponto de recuperação. Clique em **Seguinte**. Para substituir a base de dados, selecione o tipo de recuperação **Recuperar para a instância original do Servidor SQL**. Clique em **Seguinte**.

    ![Recuperar para localização original](./media/backup-azure-backup-sql/sqlbackup-recoveroriginal.png)

    Neste exemplo, o MABS recupera a base de dados para outra instância do SQL Server, ou para uma pasta de rede autónoma.

4. No ecrã de opções de **recuperação especificada,** pode selecionar as opções de recuperação, como o estrangulamento da largura de banda da rede para acelerar a largura de banda utilizada pela recuperação. Clique em **Seguinte**.

5. No ecrã **Resumo,** vê-se todas as configurações de recuperação fornecidas até agora. Clique em **Recuperar**.

    O estado de recuperação mostra que a base de dados está a ser recuperada. Pode clicar **perto** de fechar o assistente e ver o progresso no espaço de trabalho **de Monitorização.**

    ![Iniciar processo de recuperação](./media/backup-azure-backup-sql/sqlbackup-recoverying.png)

    Uma vez concluída a recuperação, a base de dados restaurada é consistente com a aplicação.

## <a name="next-steps"></a>Passos seguintes

Consulte os ficheiros de cópia de segurança e o artigo de [aplicação.](backup-mabs-files-applications-azure-stack.md)
Consulte o [Artigo De Backup SharePoint no artigo Azure Stack.](backup-mabs-sharepoint-azure-stack.md)
