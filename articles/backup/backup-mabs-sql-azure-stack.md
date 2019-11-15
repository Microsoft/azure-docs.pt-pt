---
title: Fazer backup de cargas de trabalho SQL Server em Azure Stack
description: Neste artigo, saiba como configurar o servidor de Backup do Microsoft Azure (MABS) para proteger SQL Server bancos de dados no Azure Stack.
ms.reviewer: adigan
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 06/08/2018
ms.author: dacurwin
ms.openlocfilehash: 397de7d1de9acc657fa0fd0cb93bd6e26e5606e2
ms.sourcegitcommit: a170b69b592e6e7e5cc816dabc0246f97897cb0c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74090897"
---
# <a name="back-up-sql-server-on-azure-stack"></a>Fazer backup de SQL Server em Azure Stack

Use este artigo para configurar o servidor de Backup do Microsoft Azure (MABS) para proteger SQL Server bancos de dados no Azure Stack.

O gerenciamento do backup de SQL Server banco de dados no Azure e na recuperação do Azure envolve três etapas:

1. Criar uma política de backup para proteger SQL Server bancos de dados
2. Criar cópias de backup sob demanda
3. Recuperar o banco de dados de discos e do Azure

## <a name="before-you-start"></a>Antes de começar

[Instale e prepare servidor de backup do Azure](backup-mabs-install-azure-stack.md).

## <a name="create-a-backup-policy-to-protect-sql-server-databases-to-azure"></a>Criar uma política de backup para proteger SQL Server bancos de dados no Azure

1. Na interface do usuário do Servidor de Backup do Azure, clique no espaço de trabalho **proteção** .

2. Na faixa de ferramentas da ferramenta, clique em **novo** para criar um novo grupo de proteção.

    ![Criar grupo de proteção](./media/backup-azure-backup-sql/protection-group.png)

    Servidor de Backup do Azure inicia o assistente de grupo de proteção, que o conduz pela criação de um **grupo de proteção**. Clique em **Seguinte**.

3. Na tela **Selecionar tipo de grupo de proteção** , selecione **servidores**.

    ![Selecionar tipo de grupo de proteção-' servidores '](./media/backup-azure-backup-sql/pg-servers.png)

4. Na tela **selecionar membros do grupo** , a lista Membros disponíveis exibe as várias fontes de dados. Clique em **+** para expandir uma pasta e revelar as subpastas. Clique na caixa de seleção para selecionar um item.

    ![Selecionar Banco de BD SQL](./media/backup-azure-backup-sql/pg-databases.png)

    Todos os itens selecionados aparecem na lista Membros selecionados. Depois de selecionar os servidores ou bancos de dados que você deseja proteger, clique em **Avançar**.

5. Na tela **selecionar método de proteção de dados** , forneça um nome para o grupo de proteção e marque a caixa de seleção **desejo proteção online** .

    ![Método de proteção de dados – disco de curto prazo & Azure online](./media/backup-azure-backup-sql/pg-name.png)

6. Na tela **especificar objetivos de curto prazo** , inclua as entradas necessárias para criar pontos de backup em disco e clique em **Avançar**.

    No exemplo, o **período de retenção** é de **5 dias**, a **frequência de sincronização** é uma vez a cada **15 minutos**, que é a frequência de backup. O **backup completo expresso** é definido como **8:00 P. M**.

    ![Objetivos de curto prazo](./media/backup-azure-backup-sql/pg-shortterm.png)

   > [!NOTE]
   > No exemplo mostrado, às 8:00 PM todos os dias, um ponto de backup é criado transferindo os dados modificados do ponto de backup de 8:00 PM do dia anterior. Esse processo é chamado de **backup completo expresso**. Os logs de transações são sincronizados a cada 15 minutos. Se você precisar recuperar o banco de dados às 9:00 PM, o ponto será criado a partir dos logs do último ponto de backup completo expresso (às 20:00, neste caso).
   >
   >

7. Na tela **rever alocação do disco** , verifique o espaço de armazenamento geral disponível e o espaço em disco potencial. Clique em **Seguinte**.

8. Em **escolher método de criação de réplica**, escolha como criar seu primeiro ponto de recuperação. Você pode transferir o backup inicial manualmente (fora da rede) para evitar o congestionamento da largura de banda ou pela rede. Se você optar por esperar para transferir o primeiro backup, poderá especificar o tempo para a transferência inicial. Clique em **Seguinte**.

    ![Método de replicação inicial](./media/backup-azure-backup-sql/pg-manual.png)

    A cópia de backup inicial requer a transferência de toda a fonte de dados (SQL Server banco de dado) do servidor de produção (SQL Server máquina) para Servidor de Backup do Azure. Esses dados podem ser grandes e a transferência dos dados pela rede pode exceder a largura de banda. Por esse motivo, você pode optar por transferir o backup inicial: **manualmente** (usando mídia removível) para evitar o congestionamento da largura de banda ou **automaticamente pela rede** (em um horário especificado).

    Depois que o backup inicial for concluído, o restante dos backups será backups incrementais na cópia de backup inicial. Os backups incrementais tendem a ser pequenos e são facilmente transferidos pela rede.

9. Escolha quando deseja que a verificação de consistência seja executada e clique em **Avançar**.

    ![Verificação de consistência](./media/backup-azure-backup-sql/pg-consistent.png)

    Servidor de Backup do Azure executa uma verificação de consistência na integridade do ponto de backup. Servidor de Backup do Azure calcula a soma de verificação do arquivo de backup no servidor de produção (SQL Server computador neste cenário) e os dados de backup para esse arquivo. Se houver um conflito, supõe-se que o arquivo de backup em Servidor de Backup do Azure está corrompido. Servidor de Backup do Azure retifica os dados de backup, enviando os blocos correspondentes à soma de verificação incompatível. Como as verificações de consistência são intensivas no desempenho, você pode agendar a verificação de consistência ou executá-la automaticamente.

10. Para especificar a proteção online das fontes de dados, selecione os bancos que serão protegidos no Azure e clique em **Avançar**.

    ![Selecionar fontes de fonte](./media/backup-azure-backup-sql/pg-sqldatabases.png)

11. Escolha os agendamentos de backup e as políticas de retenção que atendam às políticas da organização.

    ![Agendamento e retenção](./media/backup-azure-backup-sql/pg-schedule.png)

    Neste exemplo, os backups são feitos uma vez por dia às 12:00 e às 20:00 (parte inferior da tela)

    > [!NOTE]
    > É uma boa prática ter alguns pontos de recuperação de curto prazo em disco, para recuperação rápida. Esses pontos de recuperação são usados para recuperação operacional. O Azure serve como um bom local externo com SLAs maiores e disponibilidade garantida.
    >
    >

    **Prática recomendada**: se você agendar backups para o Azure para iniciar depois que os backups do disco local forem concluídos, os backups de disco mais recentes sempre serão copiados para o Azure.

12. Escolha o agendamento da política de retenção. Os detalhes sobre como a política de retenção funciona são fornecidos em [usar o backup do Azure para substituir o artigo de infraestrutura de fita](backup-azure-backup-cloud-as-tape.md).

    ![Política de Retenção](./media/backup-azure-backup-sql/pg-retentionschedule.png)

    Neste exemplo:

    * Os backups são feitos uma vez por dia às 12:00 e às 20:00 (parte inferior da tela) e são mantidos por 180 dias.
    * O backup em sábado às 12:00 P.M. é retido por 104 semanas
    * O backup no último sábado às 12:00 P.M. é retido por 60 meses
    * O backup no último sábado de março às 12:00 P.M. é retido por 10 anos
13. Clique em **Avançar** e selecione a opção apropriada para transferir a cópia de backup inicial para o Azure. Você pode escolher **automaticamente pela rede**

14. Depois de examinar os detalhes da política na tela **Resumo** , clique em **Criar grupo** para concluir o fluxo de trabalho. Você pode clicar em **fechar** e monitorar o progresso do trabalho no espaço de trabalho monitoramento.

    ![Criação de grupo de proteção em andamento](./media/backup-azure-backup-sql/pg-summary.png)

## <a name="on-demand-backup-of-a-sql-server-database"></a>Backup sob demanda de um banco de dados SQL Server

Embora as etapas anteriores tenham criado uma política de backup, um "ponto de recuperação" é criado somente quando ocorre o primeiro backup. Em vez de aguardar o início do Agendador, as etapas abaixo disparam a criação de um ponto de recuperação manualmente.

1. Aguarde até que o status do grupo de proteção mostre **OK** para o banco de dados antes de criar o ponto de recuperação.

    ![Membros do grupo de proteção](./media/backup-azure-backup-sql/sqlbackup-recoverypoint.png)
2. Clique com o botão direito do mouse no banco de dados e selecione **criar ponto de recuperação**.

    ![Criar ponto de recuperação online](./media/backup-azure-backup-sql/sqlbackup-createrp.png)
3. Escolha **proteção online** no menu suspenso e clique em **OK** para iniciar a criação de um ponto de recuperação no Azure.

    ![Criar ponto de recuperação](./media/backup-azure-backup-sql/sqlbackup-azure.png)
4. Exiba o andamento do trabalho no espaço de trabalho **monitoramento** .

    ![Console de monitoramento](./media/backup-azure-backup-sql/sqlbackup-monitoring.png)

## <a name="recover-a-sql-server-database-from-azure"></a>Recuperar um banco de dados SQL Server do Azure

As etapas a seguir são necessárias para recuperar uma entidade protegida (SQL Server banco de dados) do Azure.

1. Abra o console de gerenciamento do Servidor de Backup do Azure. Navegue até o espaço de trabalho de **recuperação** , no qual você pode ver os servidores protegidos. Procure o banco de dados necessário (neste caso, ReportServer $ MSDPM2012). Selecione uma **recuperação de** hora especificada como um ponto **online** .

    ![Selecionar ponto de recuperação](./media/backup-azure-backup-sql/sqlbackup-restorepoint.png)
2. Clique com o botão direito do mouse no nome do banco de dados e clique em **recuperar**.

    ![Recuperar do Azure](./media/backup-azure-backup-sql/sqlbackup-recover.png)
3. MABS mostra os detalhes do ponto de recuperação. Clique em **Seguinte**. Para substituir o banco de dados, selecione o tipo de recuperação **recuperar na instância original do SQL Server**. Clique em **Seguinte**.

    ![Recuperar no local original](./media/backup-azure-backup-sql/sqlbackup-recoveroriginal.png)

    Neste exemplo, o MABS recupera o banco de dados para outra instância do SQL Server ou para uma pasta de rede autônoma.

4. Na tela **especificar opções de recuperação** , você pode selecionar as opções de recuperação como limitação do uso de largura de banda para limitar a largura de banda usada pela recuperação. Clique em **Seguinte**.

5. Na tela **Resumo** , você verá todas as configurações de recuperação fornecidas até agora. Clique em **recuperar**.

    O status de recuperação mostra o banco de dados que está sendo recuperado. Você pode clicar em **fechar** para fechar o assistente e exibir o progresso no espaço de trabalho **monitoramento** .

    ![Iniciar processo de recuperação](./media/backup-azure-backup-sql/sqlbackup-recoverying.png)

    Depois que a recuperação for concluída, o banco de dados restaurado será consistente com o aplicativo.

## <a name="next-steps"></a>Passos seguintes

Consulte o artigo [arquivos e aplicativos de backup](backup-mabs-files-applications-azure-stack.md) .
Consulte o artigo [fazer backup do SharePoint no Azure Stack](backup-mabs-sharepoint-azure-stack.md) .
