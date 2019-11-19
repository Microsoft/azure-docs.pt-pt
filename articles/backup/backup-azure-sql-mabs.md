---
title: SQL Server de backup usando Servidor de Backup do Azure
description: Neste artigo, conheça as etapas de configuração para o backup de bancos de dados SQL Server usando Backup do Microsoft Azure Server (MABS).
ms.topic: conceptual
ms.date: 03/24/2017
ms.openlocfilehash: d5e0c49e2cad77e42960fafec9311b31d4cf2601
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74172670"
---
# <a name="back-up-sql-server-to-azure-with-azure-backup-server"></a>Fazer backup de SQL Server no Azure com Servidor de Backup do Azure

Este artigo orienta você pelas etapas de configuração para o backup de bancos de dados SQL Server usando Backup do Microsoft Azure Server (MABS).

O gerenciamento do backup de SQL Server banco de dados no Azure e na recuperação do Azure envolve três etapas:

1. Crie uma política de backup para proteger SQL Server bancos de dados no Azure.
2. Crie cópias de backup sob demanda para o Azure.
3. Recupere o banco de dados do Azure.

## <a name="before-you-start"></a>Antes de começar

Antes de começar, verifique se você [instalou e preparou o servidor de backup do Azure](backup-azure-microsoft-azure-backup.md).

## <a name="create-a-backup-policy-to-protect-sql-server-databases-to-azure"></a>Criar uma política de backup para proteger SQL Server bancos de dados no Azure

1. Na interface do usuário do Servidor de Backup do Azure, clique no espaço de trabalho **proteção** .
2. Na faixa de ferramentas da ferramenta, clique em **novo** para criar um novo grupo de proteção.

    ![Criar grupo de proteção](./media/backup-azure-backup-sql/protection-group.png)
3. MABS mostra a tela inicial com a orientação sobre como criar um **grupo de proteção**. Clique em **Seguinte**.
4. Selecione **servidores**.

    ![Selecionar tipo de grupo de proteção-' servidores '](./media/backup-azure-backup-sql/pg-servers.png)
5. Expanda a máquina SQL Server em que os bancos de dados a serem incluídos no backup estão presentes. MABS mostra várias fontes de dados cujo backup pode ser feito desse servidor. Expanda **todos os compartilhamentos SQL** e selecione os bancos de dados (neste caso, selecionamos REPORTSERVER $ MSDPM2012 e ReportServer $ MSDPM2012TempDB) para fazer backup. Clique em **Seguinte**.

    ![Selecionar Banco de BD SQL](./media/backup-azure-backup-sql/pg-databases.png)
6. Forneça um nome para o grupo de proteção e marque a caixa de seleção **desejo proteção online** .

    ![Método de proteção de dados – disco de curto prazo & Azure online](./media/backup-azure-backup-sql/pg-name.png)
7. Na tela **especificar objetivos de curto prazo** , inclua as entradas necessárias para criar pontos de backup no disco.

    Aqui vemos que o **período de retenção** está definido *como 5 dias*, a **frequência de sincronização** é definida como uma vez a cada *15 minutos*, que é a frequência na qual o backup é feito. O **backup completo expresso** é definido como *8:00 P. M*.

    ![Objetivos de curto prazo](./media/backup-azure-backup-sql/pg-shortterm.png)

   > [!NOTE]
   > Às 8:00 PM (de acordo com a entrada da tela), um ponto de backup é criado todos os dias, transferindo os dados que foram modificados do ponto de backup de 8:00 PM do dia anterior. Esse processo é chamado de **backup completo expresso**. Enquanto os logs de transações são sincronizados a cada 15 minutos, se houver a necessidade de recuperar o banco de dados às 9:00, o ponto será criado por meio da repetição dos logs do último ponto de backup completo expresso (às 20:00, neste caso).
   >
   >

8. Clique em **Seguinte**

    MABS mostra o espaço de armazenamento geral disponível e a utilização de espaço em disco potencial.

    ![Alocação de disco](./media/backup-azure-backup-sql/pg-storage.png)

    Por padrão, o MABS cria um volume por fonte de dados (banco de dado SQL Server) que é usado para a cópia de backup inicial. Usando essa abordagem, o LDM (Gerenciador de discos lógicos) limita a proteção MABS a 300 fontes de dados (SQL Server bancos de dados). Para contornar essa limitação, selecione a opção **colocalizar dados no pool de armazenamento do DPM**. Se você usar essa opção, o MABS usará um único volume para várias fontes de dados, o que permitirá que o MABS Proteja até 2000 bancos de dado SQL.

    Se **a opção aumentar os volumes automaticamente** estiver selecionada, o mAbs poderá considerar o aumento do volume de backup à medida que os dados de produção aumentarem. Se **a opção aumentar os volumes automaticamente** não estiver selecionada, mAbs limitará o armazenamento de backup usado para as fontes de dados no grupo de proteção.
9. Os administradores recebem a opção de transferir esse backup inicial manualmente (fora da rede) para evitar o congestionamento da largura de banda ou pela rede. Eles também podem configurar a hora em que a transferência inicial pode ocorrer. Clique em **Seguinte**.

    ![Método de replicação inicial](./media/backup-azure-backup-sql/pg-manual.png)

    A cópia de backup inicial requer a transferência de toda a fonte de dados (SQL Server banco de dado) do servidor de produção (SQL Server máquina) para MABS. Esses dados podem ser grandes e a transferência dos dados pela rede pode exceder a largura de banda. Por esse motivo, os administradores podem optar por transferir o backup inicial: **manualmente** (usando mídia removível) para evitar o congestionamento da largura de banda ou **automaticamente pela rede** (em um horário especificado).

    Depois que o backup inicial for concluído, o restante dos backups será backups incrementais na cópia de backup inicial. Os backups incrementais tendem a ser pequenos e são facilmente transferidos pela rede.
10. Escolha quando deseja que a verificação de consistência seja executada e clique em **Avançar**.

    ![Verificação de consistência](./media/backup-azure-backup-sql/pg-consistent.png)

    O MABS pode executar uma verificação de consistência para verificar a integridade do ponto de backup. Ele calcula a soma de verificação do arquivo de backup no servidor de produção (SQL Server computador neste cenário) e os dados de backup para esse arquivo em MABS. No caso de um conflito, supõe-se que o arquivo de backup em MABS está corrompido. O MABS corrigiu os dados de backup enviando os blocos correspondentes à soma de verificação incompatível. Como a verificação de consistência é uma operação com uso intensivo de desempenho, os administradores têm a opção de agendar a verificação de consistência ou executá-la automaticamente.
11. Para especificar a proteção online das fontes de dados, selecione os bancos que serão protegidos no Azure e clique em **Avançar**.

    ![Selecionar fontes de fonte](./media/backup-azure-backup-sql/pg-sqldatabases.png)
12. Os administradores podem escolher agendamentos de backup e políticas de retenção que atendam às políticas da organização.

    ![Agendamento e retenção](./media/backup-azure-backup-sql/pg-schedule.png)

    Neste exemplo, os backups são feitos uma vez por dia às 12:00 e às 20:00 (parte inferior da tela)

    > [!NOTE]
    > É uma boa prática ter alguns pontos de recuperação de curto prazo em disco, para recuperação rápida. Esses pontos de recuperação são usados para "recuperação operacional". O Azure serve como um bom local externo com SLAs maiores e disponibilidade garantida.
    >
    >

    **Prática recomendada**: Verifique se os backups do Azure estão agendados após a conclusão de backups de disco local usando o DPM. Isso permite que o backup de disco mais recente seja copiado para o Azure.

13. Escolha o agendamento da política de retenção. Os detalhes sobre como a política de retenção funciona são fornecidos em [usar o backup do Azure para substituir o artigo de infraestrutura de fita](backup-azure-backup-cloud-as-tape.md).

    ![Política de retenção](./media/backup-azure-backup-sql/pg-retentionschedule.png)

    Neste exemplo:

    * Os backups são feitos uma vez por dia às 12:00 e às 20:00 (parte inferior da tela) e são mantidos por 180 dias.
    * O backup em sábado às 12:00 P.M. é retido por 104 semanas
    * O backup no último sábado às 12:00 P.M. é retido por 60 meses
    * O backup no último sábado de março às 12:00 P.M. é retido por 10 anos
14. Clique em **Avançar** e selecione a opção apropriada para transferir a cópia de backup inicial para o Azure. Você pode escolher **automaticamente pela rede** ou **backup offline**.

    * **Automaticamente, a rede** transfere os dados de backup para o Azure de acordo com o agendamento escolhido para backup.
    * O modo como o **backup offline** funciona é explicado em [fluxo de trabalho de backup offline no backup do Azure](backup-azure-backup-import-export.md).

    Escolha o mecanismo de transferência relevante para enviar a cópia de backup inicial para o Azure e clique em **Avançar**.
15. Depois de examinar os detalhes da política na tela **Resumo** , clique no botão **Criar grupo** para concluir o fluxo de trabalho. Você pode clicar no botão **fechar** e monitorar o progresso do trabalho no espaço de trabalho monitoramento.

    ![Criação de grupo de proteção em andamento](./media/backup-azure-backup-sql/pg-summary.png)

## <a name="on-demand-backup-of-a-sql-server-database"></a>Backup sob demanda de um banco de dados SQL Server

Embora as etapas anteriores tenham criado uma política de backup, um "ponto de recuperação" é criado somente quando ocorre o primeiro backup. Em vez de aguardar o início do Agendador, as etapas abaixo disparam a criação de um ponto de recuperação manualmente.

1. Aguarde até que o status do grupo de proteção mostre **OK** para o banco de dados antes de criar o ponto de recuperação.

    ![Membros do grupo de proteção](./media/backup-azure-backup-sql/sqlbackup-recoverypoint.png)
2. Clique com o botão direito do mouse no banco de dados e selecione **criar ponto de recuperação**.

    ![Criar ponto de recuperação online](./media/backup-azure-backup-sql/sqlbackup-createrp.png)
3. Escolha **proteção online** no menu suspenso e clique em **OK**. Isso inicia a criação de um ponto de recuperação no Azure.

    ![Criar ponto de recuperação](./media/backup-azure-backup-sql/sqlbackup-azure.png)
4. Você pode exibir o andamento do trabalho no espaço de trabalho de **monitoramento** onde encontrará um trabalho em andamento como aquele descrito na próxima figura.

    ![Console de monitoramento](./media/backup-azure-backup-sql/sqlbackup-monitoring.png)

## <a name="recover-a-sql-server-database-from-azure"></a>Recuperar um banco de dados SQL Server do Azure

As etapas a seguir são necessárias para recuperar uma entidade protegida (SQL Server banco de dados) do Azure.

1. Abra o console de gerenciamento do servidor DPM. Navegue até o espaço de trabalho de **recuperação** , no qual você pode ver os servidores submetidos a backup pelo DPM. Procure o banco de dados necessário (neste caso, ReportServer $ MSDPM2012). Selecione uma **recuperação de** tempo que termina com **online**.

    ![Selecionar ponto de recuperação](./media/backup-azure-backup-sql/sqlbackup-restorepoint.png)
2. Clique com o botão direito do mouse no nome do banco de dados e clique em **recuperar**.

    ![Recuperar do Azure](./media/backup-azure-backup-sql/sqlbackup-recover.png)
3. O DPM mostra os detalhes do ponto de recuperação. Clique em **Seguinte**. Para substituir o banco de dados, selecione o tipo de recuperação **recuperar na instância original do SQL Server**. Clique em **Seguinte**.

    ![Recuperar no local original](./media/backup-azure-backup-sql/sqlbackup-recoveroriginal.png)

    Neste exemplo, o DPM permite a recuperação do banco de dados para outra SQL Server instância ou para uma pasta de rede autônoma.
4. Na tela **especificar opções de recuperação** , você pode selecionar as opções de recuperação como limitação do uso de largura de banda para limitar a largura de banda usada pela recuperação. Clique em **Seguinte**.
5. Na tela **Resumo** , você verá todas as configurações de recuperação fornecidas até agora. Clique em **recuperar**.

    O status de recuperação mostra o banco de dados que está sendo recuperado. Você pode clicar em **fechar** para fechar o assistente e exibir o progresso no espaço de trabalho **monitoramento** .

    ![Iniciar processo de recuperação](./media/backup-azure-backup-sql/sqlbackup-recoverying.png)

    Depois que a recuperação for concluída, o banco de dados restaurado será consistente com o aplicativo.

### <a name="next-steps"></a>Passos seguintes

* [Perguntas frequentes do backup do Azure](backup-azure-backup-faq.md)
