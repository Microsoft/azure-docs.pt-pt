---
title: Fazer cópias de segurança de cargas de trabalho do SQL Server no Azure Stack
description: Utilize o Azure Backup Server para proteger a carga de trabalho do SQL Server no Azure Stack.
services: backup
author: adigan
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 6/8/2018
ms.author: adigan
ms.openlocfilehash: fb064c39fa014515fb2a3f4ccc96ce216f2f7b2e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60254473"
---
# <a name="back-up-sql-server-on-stack"></a>Criar cópias de segurança do SQL Server na pilha
Utilize este artigo para configurar o Microsoft Azure Backup Server (MABS) para proteger bases de dados do SQL Server no Azure Stack.

A gestão de cópia de segurança do SQL Server da base de dados para o Azure e a recuperação do Azure envolve três etapas:

1. Criar uma política de cópia de segurança para proteger bases de dados do SQL Server
2. Criar cópias de segurança a pedido
3. Recuperar a base de dados de discos e do Azure

## <a name="before-you-start"></a>Antes de começar

[Instalar e preparar o servidor de cópia de segurança do Azure](backup-mabs-install-azure-stack.md).

## <a name="create-a-backup-policy-to-protect-sql-server-databases-to-azure"></a>Criar uma política de cópia de segurança para proteger bases de dados do SQL Server para o Azure
1. Na IU de servidor de cópia de segurança do Azure, clique nas **proteção** área de trabalho.

2. No Friso, clique em **New** para criar um novo grupo de proteção.

    ![Criar grupo de proteção](./media/backup-azure-backup-sql/protection-group.png)

    O Azure Backup Server é iniciado o Assistente de grupo de proteção, o que o orienta pela criação de um **grupo de proteção**. Clique em **Seguinte**.

3. Na **selecionar tipo de grupo de proteção** ecrã, selecione **servidores**.

    ![Selecionar tipo de grupo de proteção - "Servidores"](./media/backup-azure-backup-sql/pg-servers.png)

4. Na **selecionar membros do grupo** ecrã, a lista de membros disponíveis apresenta várias origens de dados. Clique em **+** para expandir uma pasta e revelar as subpastas. Clique na caixa de verificação para selecionar um item.

    ![Selecione a BD SQL](./media/backup-azure-backup-sql/pg-databases.png)

    Todos os itens selecionados aparecem na lista de membros selecionados. Depois de selecionar servidores ou bases de dados que pretende proteger, clique em **seguinte**.

5. Na **selecionar método de proteção de dados** ecrã, forneça um nome para o grupo de proteção e selecione o **pretendo proteção online** caixa de verificação.

    ![Método de proteção de dados - disco de curto prazo e Online do Azure](./media/backup-azure-backup-sql/pg-name.png)

6. Na **especificar objetivos a curto prazo** ecrã, incluem as entradas necessárias para criar pontos de cópia de segurança em disco e clique em **próxima**.

    No exemplo, **período de retenção** é **5 dias**, **frequência de sincronização** é uma vez cada **15 minutos**, que é a cópia de segurança frequência. **Cópia de segurança completa rápida** está definido como **8:00 P.M**.

    ![Objetivos de curto prazo](./media/backup-azure-backup-sql/pg-shortterm.png)

   > [!NOTE]
   > No exemplo mostrado, todos os dias às 20:00: 00 um ponto de cópia de segurança é criado transferindo os dados modificados de ponto de cópia de segurança das 20:00 do dia anterior. Este processo é denominado **Express cópia de segurança completa**. Registos de transações são sincronizados a cada 15 minutos. Se precisar de recuperar a base de dados em 21 horas, a criação do ponto dos registos dos últimos express ponto de cópia de segurança completa (8 horas neste caso).
   >
   >

7. Sobre o **rever atribuição do disco** ecrã, verifique se o espaço de armazenamento geral disponível e o espaço em disco potencial. Clique em **Seguinte**.

8. Na **Choose Replica Creation Method**, escolha como pretende criar o primeiro ponto de recuperação. Pode transferir a cópia de segurança inicial manualmente (desativado rede) para evitar o congestionamento de largura de banda ou através da rede. Se optar por esperar para transferir a primeira cópia de segurança, pode especificar o tempo para a transferência inicial. Clique em **Seguinte**.

    ![Método de replicação inicial](./media/backup-azure-backup-sql/pg-manual.png)

    A cópia de segurança inicial requer transferência a origem de dados inteiro (base de dados do SQL Server) do servidor de produção (máquina do SQL Server) para servidor de cópia de segurança do Azure. Estes dados podem ser grandes e transferência de dados através da rede poderá exceder a largura de banda. Por esse motivo, pode optar por transferir a cópia de segurança inicial: **Manualmente** (usando mídia removível) para evitar o congestionamento de largura de banda, ou **automaticamente através da rede** (numa hora específica).

    Quando a cópia de segurança inicial estiver concluída, o restante das cópias de segurança são cópias de segurança incrementais em cópia de segurança inicial. Cópias de segurança incrementais tendem a ser pequeno e são facilmente transferidas através da rede.

9. Escolha quando pretende que a verificação de consistência para executar e clique em **seguinte**.

    ![Verificação de consistência](./media/backup-azure-backup-sql/pg-consistent.png)

    O Azure Backup Server efetua uma verificação de consistência na integridade do ponto de cópia de segurança. Servidor de cópia de segurança do Azure calcula a soma de verificação do arquivo de backup no servidor de produção (máquina do SQL Server neste cenário) e os dados de cópia de segurança para esse ficheiro. Se houver um conflito, presume-se que o ficheiro de cópia de segurança no servidor de cópia de segurança do Azure está danificado. O Azure Backup Server rectifies os dados de cópia de segurança, enviando os blocos correspondente para o erro de correspondência de soma de verificação. Como verificações de consistência são com uso intensivo de desempenho, pode agendar a verificação de consistência ou executá-la automaticamente.

10. Para especificar a proteção online das origens de dados, selecione as bases de dados a ser protegido para o Azure e clique em **seguinte**.

    ![Selecione as origens de dados](./media/backup-azure-backup-sql/pg-sqldatabases.png)

11. Escolha as agendas de cópia de segurança e políticas de retenção que se adequam às políticas de organização.

    ![Agendamento e retenção](./media/backup-azure-backup-sql/pg-schedule.png)

    Neste exemplo, as cópias de segurança são executadas uma vez por dia às 12:00 e 8 PM (parte inferior do ecrã)

    > [!NOTE]
    > É recomendável ter alguns pontos de recuperação de curta duração no disco, para uma recuperação rápida. Esses pontos de recuperação são utilizados para recuperação operacional. O Azure funciona como uma bom fora do local com SLAs superior e disponibilidade garantida.
    >
    >

    **Melhor prática**: Se agendar cópias de segurança para o Azure para iniciar após concluir as cópias de segurança de disco local, as cópias de segurança de disco mais recente sempre são copiadas para o Azure.

12. Escolha o agendamento de política de retenção. Os detalhes sobre como funciona a política de retenção são fornecidos no [utilização do Azure Backup para substituir o seu artigo de infraestrutura da banda](backup-azure-backup-cloud-as-tape.md).

    ![Política de Retenção](./media/backup-azure-backup-sql/pg-retentionschedule.png)

    Neste exemplo:

    * As cópias de segurança são efetuadas uma vez por dia às 12:00 e 8 PM (parte inferior do ecrã) e são mantidas durante 180 dias.
    * A cópia de segurança no sábados às 12:00h são retidos durante semanas 104
    * A cópia de segurança no último sábados às 12:00h são retidos durante 60 meses
    * A cópia de segurança no último Sábado de Março, às 12:00h são retidos durante 10 anos
13. Clique em **seguinte** e selecione a opção adequada para transferir a cópia de segurança inicial para o Azure. Pode escolher **automaticamente através da rede**

14. Depois de rever os detalhes da política no **resumo** ecrã, clique em **criar grupo** para concluir o fluxo de trabalho. Pode clicar em **fechar** e monitorizar o progresso da tarefa na área de trabalho de monitorização.

    ![Criação de grupo de proteção em curso](./media/backup-azure-backup-sql/pg-summary.png)

## <a name="on-demand-backup-of-a-sql-server-database"></a>Cópia de segurança a pedido de uma base de dados do SQL Server
Enquanto os passos anteriores criaram uma política de cópia de segurança, é criado um "ponto de recuperação" apenas quando ocorre a primeira cópia de segurança. Em vez de esperar o agendador de participar, os passos abaixo acionador a criação de uma recuperação de ponto de manualmente.

1. Aguarde até que o estado do grupo de proteção é apresentado **OK** para a base de dados antes de criar o ponto de recuperação.

    ![Membros do grupo de proteção](./media/backup-azure-backup-sql/sqlbackup-recoverypoint.png)
2. Com o botão direito na base de dados e selecione **criar ponto de recuperação**.

    ![Criar ponto de recuperação Online](./media/backup-azure-backup-sql/sqlbackup-createrp.png)
3. Escolher **proteção Online** no menu pendente e clique **OK** para iniciar a criação de um ponto de recuperação no Azure.

    ![Criar ponto de recuperação](./media/backup-azure-backup-sql/sqlbackup-azure.png)
4. Ver o progresso da tarefa no **monitorização** área de trabalho.

    ![Consola de monitorização](./media/backup-azure-backup-sql/sqlbackup-monitoring.png)

## <a name="recover-a-sql-server-database-from-azure"></a>Recuperar uma base de dados do SQL Server a partir do Azure
Os seguintes passos são necessários para recuperar uma entidade protegida (base de dados do SQL Server) a partir do Azure.

1. Abra a consola de gestão do servidor de cópia de segurança do Azure. Navegue para **recuperação** área de trabalho onde pode ver os servidores protegidos. Procure essa base de dados (neste caso ReportServer$ MSDPM2012). Selecione um **recuperação a partir de** hora em que é especificada como um **Online** ponto.

    ![Selecione o ponto de recuperação](./media/backup-azure-backup-sql/sqlbackup-restorepoint.png)
2. O nome de base de dados com o botão direito e clique em **recuperar**.

    ![Recuperar a partir do Azure](./media/backup-azure-backup-sql/sqlbackup-recover.png)
3. MABS mostra os detalhes dos pontos de recuperação. Clique em **Seguinte**. Para substituir a base de dados, selecione o tipo de recuperação **recuperar na instância original do SQL Server**. Clique em **Seguinte**.

    ![Recuperar para a localização Original](./media/backup-azure-backup-sql/sqlbackup-recoveroriginal.png)

    Neste exemplo, o MABS recupera a base de dados para outra instância do SQL Server ou para uma pasta de rede autónomo.

4. Na **opções especificar recuperação** ecrã, pode selecionar as opções de recuperação, como a limitação da utilização de largura de banda de rede para limitar a largura de banda utilizada pela recuperação. Clique em **Seguinte**.

5. Na **resumo** ecrã, verá todas as configurações de recuperação até ao momento fornecidas. Clique em **recuperar**.

    O estado de recuperação mostra a base de dados a ser recuperado. Pode clicar em **feche** para fechar o assistente e ver o progresso no **monitorização** área de trabalho.

    ![Iniciar o processo de recuperação](./media/backup-azure-backup-sql/sqlbackup-recoverying.png)

    Quando a recuperação estiver concluída, a base de dados restaurada é consistente com a aplicação.

## <a name="next-steps"></a>Próximos Passos

Consulte a [cópia de segurança de ficheiros e aplicativo](backup-mabs-files-applications-azure-stack.md) artigo.
Consulte a [SharePoint de cópia de segurança no Azure Stack](backup-mabs-sharepoint-azure-stack.md) artigo.
