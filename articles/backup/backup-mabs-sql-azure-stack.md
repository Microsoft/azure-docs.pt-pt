---
title: Apoie as cargas de trabalho do SQL Server na Azure Stack
description: Neste artigo, aprenda a configurar o Microsoft Azure Backup Server (MABS) para proteger as bases de dados do SQL Server no Azure Stack.
ms.topic: conceptual
ms.date: 06/08/2018
ms.openlocfilehash: 80de7913b010fca69c3703e423109f2ede653590
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "91332819"
---
# <a name="back-up-sql-server-on-azure-stack"></a>Apoiar o SQL Server na Azure Stack

Utilize este artigo para configurar o Microsoft Azure Backup Server (MABS) para proteger as bases de dados do SQL Server no Azure Stack.

A gestão da base de dados do SQL Server para a Azure e a recuperação do Azure envolve três etapas:

1. Criar uma política de backup para proteger bases de dados do SQL Server
2. Criar cópias de backup a pedido
3. Recuperar a base de dados dos Discos, e do Azure

## <a name="prerequisites-and-limitations"></a>Pré-requisitos e limitações

* Se tiver uma base de dados com ficheiros numa partilha de ficheiros remota, a proteção vai falhar com o Erro ID 104. O MABS não suporta a proteção dos dados do SQL Server numa partilha remota de ficheiros.
* O MABS não pode proteger bases de dados que são armazenadas em ações remotas de SMB.
* Certifique-se de que as [réplicas do grupo de disponibilidade são configuradas apenas para leitura](/sql/database-engine/availability-groups/windows/configure-read-only-access-on-an-availability-replica-sql-server).
* Tem de adicionar explicitamente a conta de sistema **NTAuthority\System** ao grupo Sysadmin no SQL Server.
* Quando efetuar uma recuperação de localização alternativa para uma base de dados parcialmente contida, deve certificar-se de que a instância SQL alvo tem a funcionalidade [De Base de Dados Contida](/sql/relational-databases/databases/migrate-to-a-partially-contained-database#enable) ativada.
* Quando efetuar uma recuperação de localização alternativa para uma base de dados de fluxo de ficheiros, deve certificar-se de que a instância SQL alvo tem a [função de base de dados de fluxo de ficheiros](/sql/relational-databases/blob/enable-and-configure-filestream) ativada.
* Proteção para o SQL Server AlwaysOn:
  * O MABS deteta grupos de disponibilidade ao realizar inquéritos na criação de grupos de proteção.
  * O MABS deteta uma falha e continua a proteger a base de dados.
  * O MABS suporta configurações de clusters de vários locais para uma instância do SQL Server.
* Quando protege bases de dados que utilizam a funcionalidade AlwaysOn, o MABS tem as seguintes limitações:
  * O MABS honrará a política de backup para grupos de disponibilidade definidos no SQL Server com base nas preferências de backup, da seguinte forma:
    * Preferir secundária – as cópias de segurança devem ocorrer numa réplica secundária, exceto se a réplica primária for a única online. Se houver várias réplicas secundárias disponíveis, então o nó com a maior prioridade de backup será selecionado para cópia de segurança. Se apenas a réplica primária estiver disponível, então a cópia de segurança deve ocorrer na réplica primária.
    * Apenas secundária – a cópia de segurança não deve ser criada na réplica primária. Se a réplica primária for a única online, a cópia de segurança não deve ser criada.
    * Primária – as cópias de segurança devem ser sempre criadas na réplica primária.
    * Qualquer réplica – as cópias de segurança podem ser efetuadas em qualquer uma das réplicas de disponibilidade do grupo de disponibilidade. O nó a partir do qual será efetuada a cópia de segurança dependerá das prioridades de cópia de segurança de cada um dos nós.
  * Tenha em atenção o seguinte:
    * Cópias de segurança podem acontecer a partir de qualquer réplica legível - isto é, primário, secundário sincronizado, assíncronos secundário.
    * Se qualquer réplica for excluída da cópia de segurança, por exemplo, **a Réplica excluída** está ativada ou está marcada como não legível, então essa réplica não será selecionada para cópia de segurança em nenhuma das opções.
    * Se várias réplicas estiverem disponíveis e legíveis, então o nó com a maior prioridade de backup será selecionado para cópia de segurança.
    * Se a cópia de segurança falhar no nó selecionado, a operação de backup falha.
    * A recuperação para o local original não é suportada.
* Problemas de backup do SQL Server 2014 ou acima:
  * O sql server 2014 adicionou uma nova funcionalidade para criar uma [base de dados para o SQL Server no Windows Azure Blob .](/sql/relational-databases/databases/sql-server-data-files-in-microsoft-azure) O MABS não pode ser usado para proteger esta configuração.
  * Existem alguns problemas conhecidos com a preferência de backup "Prefer secondary" para a opção SQL AlwaysOn. O MABS recebe sempre um apoio do secundário. Se não for possível encontrar secundário, o reforço falha.

## <a name="before-you-start"></a>Antes de começar

[Instale e prepare o Servidor de Backup Azure](backup-mabs-install-azure-stack.md).

## <a name="create-a-backup-policy-to-protect-sql-server-databases-to-azure"></a>Criar uma política de backup para proteger as bases de dados do SQL Server para o Azure

1. No UI do Servidor de Backup Azure, selecione o espaço de trabalho **de Proteção.**

2. Na fita da ferramenta, selecione **New** para criar um novo grupo de proteção.

    ![Criar Grupo de Proteção](./media/backup-azure-backup-sql/protection-group.png)

    O Azure Backup Server inicia o assistente do Grupo de Proteção, que o leva através da criação de um **Grupo de Proteção**. Selecione **Seguinte**.

3. No ecrã **Select Protection Group Type,** selecione **Servidores**.

    ![Selecione Tipo de Grupo de Proteção - 'Servidores'](./media/backup-azure-backup-sql/pg-servers.png)

4. No ecrã **Select Group Members,** a lista de membros disponíveis apresenta as várias fontes de dados. Selecione **+** para expandir uma pasta e revelar as sub-dobradeiras. Selecione a caixa de verificação para selecionar um item.

    ![Selecione SQL DB](./media/backup-azure-backup-sql/pg-databases.png)

    Todos os itens selecionados aparecem na lista de membros selecionados. Depois de selecionar os servidores ou bases de dados que pretende proteger, selecione **Next**.

5. No ecrã **Select Data Protection Method,** forneça um nome para o grupo de proteção e selecione a caixa de **verificação de proteção online I want.**

    ![Método de Proteção de Dados - & de disco de curto prazo Azure Online](./media/backup-azure-backup-sql/pg-name.png)

6. No ecrã **'Especificar Short-Term** Objetivos', inclua as entradas necessárias para criar pontos de backup para o disco e selecione **Next**.

    No exemplo, a **gama de retenção** é **de 5 dias,** **a frequência de sincronização** é uma vez a cada **15 minutos**, que é a frequência de reserva. **O Express Full Backup** está programado para **as 20:00 P.M**.

    ![Objetivos de curto prazo](./media/backup-azure-backup-sql/pg-shortterm.png)

   > [!NOTE]
   > No exemplo mostrado, às 20:00 horas de dia é criado um ponto de backup transferindo os dados modificados do ponto de backup das 20:00 do dia anterior. Este processo chama-se **Express Full Backup**. Os registos de transações são sincronizados a cada 15 minutos. Se precisar de recuperar a base de dados às 21:00, o ponto é criado a partir dos registos do último ponto de backup completo expresso (20h00 deste caso).
   >
   >

7. No ecrã **de atribuição do disco Review,** verifique o espaço de armazenamento geral disponível e o potencial espaço do disco. Selecione **Seguinte**.

8. No **Método de Criação de Réplicas,** escolha como criar o seu primeiro ponto de recuperação. Pode transferir a cópia de segurança inicial manualmente (fora da rede) para evitar o congestionamento da largura de banda ou sobre a rede. Se optar por esperar para transferir a primeira cópia de segurança, pode especificar a hora da transferência inicial. Selecione **Seguinte**.

    ![Método de replicação inicial](./media/backup-azure-backup-sql/pg-manual.png)

    A cópia de cópia de segurança inicial requer a transferência de toda a fonte de dados (base de dados SQL Server) do servidor de produção (sql server) para o Azure Backup Server. Estes dados podem ser grandes, e a transferência dos dados sobre a rede pode exceder a largura de banda. Por esta razão, pode optar por transferir a cópia de segurança inicial: **Manualmente** (utilizando suportes amovíveis) para evitar o congestionamento da largura de banda, ou **automaticamente através da rede** (num determinado momento).

    Uma vez que a cópia de segurança inicial esteja completa, o resto das cópias de segurança são cópias de segurança incrementais na cópia inicial de backup. As cópias de segurança incrementais tendem a ser pequenas e são facilmente transferidas através da rede.

9. Escolha quando pretender que o controlo de consistência seja executado e selecione **Next**.

    ![Verificação de consistência](./media/backup-azure-backup-sql/pg-consistent.png)

    O Azure Backup Server executa uma verificação de consistência sobre a integridade do ponto de backup. O Azure Backup Server calcula a parte de verificação do ficheiro de cópia de segurança no servidor de produção (computador SQL Server neste cenário) e os dados de backup para esse ficheiro. Se houver um conflito, presume-se que o ficheiro de reserva no Azure Backup Server é corrupto. O Azure Backup Server corrige os dados de backup enviando os blocos correspondentes ao desfasamento do sistema de verificação. Como os controlos de consistência são intensivos de desempenho, pode agendar a verificação de consistência ou executá-la automaticamente.

10. Para especificar a proteção online das fontes de dados, selecione as bases de dados a proteger para a Azure e selecione **Next**.

    ![Selecione fontes de dados](./media/backup-azure-backup-sql/pg-sqldatabases.png)

11. Escolha horários de backup e políticas de retenção que se assemessem às políticas da organização.

    ![Horário e Retenção](./media/backup-azure-backup-sql/pg-schedule.png)

    Neste exemplo, as cópias de segurança são tomadas uma vez por dia às 12:00 e 20:00 (parte inferior do ecrã)

    > [!NOTE]
    > É uma boa prática ter alguns pontos de recuperação de curto prazo no disco, para uma rápida recuperação. Estes pontos de recuperação são utilizados para a recuperação operacional. O Azure serve como uma boa localização fora do local com SLAs mais elevados e disponibilidade garantida.
    >
    >

    **Boas Práticas**: Se agendar cópias de segurança para a Azure para começar depois de concluídas as cópias de segurança do disco local, as cópias de segurança mais recentes do disco são sempre copiadas para o Azure.

12. Escolha o calendário da política de retenção. Os detalhes sobre como a política de retenção funciona são fornecidos na [Use Azure Backup para substituir o seu artigo de infraestrutura de fita](backup-azure-backup-cloud-as-tape.md).

    ![Política de retenção](./media/backup-azure-backup-sql/pg-retentionschedule.png)

    Neste exemplo:

    * As cópias de segurança são tomadas uma vez por dia às 12:00 e 20:00 (parte inferior do ecrã) e são mantidas durante 180 dias.
    * O reforço no sábado às 12:00 p.M. é retido por 104 semanas
    * O reforço no sábado passado às 12:00 p.M. é retido por 60 meses
    * O reforço no último sábado de março às 12:00 p.M. é mantido por 10 anos
13. Selecione **Next** e selecione a opção adequada para transferir a cópia de cópia inicial para Azure. Pode escolher **automaticamente sobre a rede**

14. Assim que rever os detalhes da política no ecrã **Resumo,** selecione **Criar grupo** para completar o fluxo de trabalho. Pode selecionar **Close** e monitorizar o progresso do trabalho no espaço de trabalho de monitorização.

    ![Criação de grupo de proteção In-Progress](./media/backup-azure-backup-sql/pg-summary.png)

## <a name="on-demand-backup-of-a-sql-server-database"></a>Backup on-demand de uma base de dados do SQL Server

Embora os passos anteriores tenham criado uma política de backup, um "ponto de recuperação" só é criado quando ocorre a primeira cópia de segurança. Em vez de esperar que o programador entre em vigor, os passos abaixo desencadeiam a criação de um ponto de recuperação manualmente.

1. Aguarde até que o estado do grupo de proteção mostre **OK** para a base de dados antes de criar o ponto de recuperação.

    ![Membros do Grupo de Proteção](./media/backup-azure-backup-sql/sqlbackup-recoverypoint.png)
2. Clique com o botão direito na base de dados e **selecione Criar Ponto de Recuperação**.

    ![Criar ponto de recuperação online](./media/backup-azure-backup-sql/sqlbackup-createrp.png)
3. Escolha **a Proteção Online** no menu suspenso e selecione **OK** para iniciar a criação de um ponto de recuperação em Azure.

    ![Criar ponto de recuperação](./media/backup-azure-backup-sql/sqlbackup-azure.png)
4. Veja o progresso do trabalho no espaço de trabalho **de monitorização.**

    ![Consola de monitorização](./media/backup-azure-backup-sql/sqlbackup-monitoring.png)

## <a name="recover-a-sql-server-database-from-azure"></a>Recuperar uma base de dados do SQL Server do Azure

São necessários os seguintes passos para recuperar uma entidade protegida (base de dados SQL Server) do Azure.

1. Abra a consola de gestão do servidor de backup Azure. Navegue para o espaço de trabalho **recovery** onde pode ver os servidores protegidos. Pesquise a base de dados requerida (neste caso, ReportServer$MSDPM2012). Selecione uma **Recuperação a partir do** tempo especificado como um ponto **Online.**

    ![Selecione ponto de recuperação](./media/backup-azure-backup-sql/sqlbackup-restorepoint.png)
2. Clique com o botão direito no nome da base de dados e selecione **Recuperar.**

    ![Recuperar de Azure](./media/backup-azure-backup-sql/sqlbackup-recover.png)
3. O MABS mostra os detalhes do ponto de recuperação. Selecione **Seguinte**. Para substituir a base de dados, selecione o tipo de recuperação **Recuperar para a instância original do SQL Server**. Selecione **Seguinte**.

    ![Recuperar para localização original](./media/backup-azure-backup-sql/sqlbackup-recoveroriginal.png)

    Neste exemplo, o MABS recupera a base de dados para outra instância do SQL Server ou para uma pasta de rede autónoma.

4. No ecrã de **opções de Recuperação de Especificação,** pode selecionar as opções de recuperação como o acelerador de utilização da largura de banda da rede para acelerar a largura de banda utilizada pela recuperação. Selecione **Seguinte**.

5. No ecrã **Resumo,** vê todas as configurações de recuperação fornecidas até agora. Selecione **Recuperar**.

    O estado de recuperação mostra a base de dados a ser recuperada. Pode selecionar **Perto** para fechar o assistente e ver o progresso no espaço de trabalho **de monitorização.**

    ![Iniciar processo de recuperação](./media/backup-azure-backup-sql/sqlbackup-recoverying.png)

    Uma vez concluída a recuperação, a base de dados restaurada é consistente com a aplicação.

## <a name="next-steps"></a>Passos seguintes

Consulte os ficheiros de cópia de segurança e o artigo [de aplicação.](backup-mabs-files-applications-azure-stack.md)
Veja o Backup SharePoint no artigo [Azure Stack.](backup-mabs-sharepoint-azure-stack.md)
