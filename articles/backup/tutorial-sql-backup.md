---
title: Tutorial - Back up SQL Server bases de dados para Azure
description: Neste tutorial, aprenda a fazer backup de uma base de dados do SQL Server em funcionamento num Azure VM para um cofre dos Serviços de Recuperação de Backup Azure.
ms.topic: tutorial
ms.date: 06/18/2019
ms.openlocfilehash: 3216404b04647e2fb5f15beb09b9a4ccd6648bba
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "74171892"
---
# <a name="back-up-a-sql-server-database-in-an-azure-vm"></a>Volte a fazer uma base de dados do SQL Server num VM Azure

Este tutorial mostra-lhe como fazer backup numa base de dados do SQL Server em execução num Azure VM para um cofre dos Serviços de Recuperação de Backup Azure. Neste artigo, vai aprender a:

> [!div class="checklist"]
>
> * Crie e configure um cofre.
> * Descubra bases de dados e instale cópias de segurança.
> * Criar proteção automática para bases de dados.
> * Faça um reforço a pedido.

## <a name="prerequisites"></a>Pré-requisitos

Antes de fazer o check-up na base de dados do SQL Server, verifique as seguintes condições:

1. Identifique ou [crie](backup-sql-server-database-azure-vms.md#create-a-recovery-services-vault) um cofre de Serviços de Recuperação na mesma região ou local que o VM que acolhe a instância Do Servidor SQL.
2. [Verifique as permissões VM necessárias](backup-azure-sql-database.md#set-vm-permissions) para fazer o back-up das bases de dados SQL.
3. Verifique se o VM tem conectividade de [rede.](backup-sql-server-database-azure-vms.md#establish-network-connectivity)
4. Verifique se as bases de dados do SQL Server estão nomeadas de acordo com [as diretrizes](#verify-database-naming-guidelines-for-azure-backup) de nomeação para o Azure Backup.
5. Verifique se não tem outras soluções de backup ativadas para a base de dados. Desative todas as outras cópias de segurança do SQL Server antes de configurar este cenário. Pode ativar o Backup Azure para um VM Azure juntamente com o Azure Backup para uma base de dados do SQL Server em execução no VM sem qualquer conflito.

### <a name="establish-network-connectivity"></a>Estabelecer conectividade de rede

Para todas as operações, a máquina virtual SQL Server VM necessita de conectividade com endereços IP públicos do Azure. As operações vM (descoberta de bases de dados, configurar backups, agendar backups, restaurar pontos de recuperação, e assim por diante) falham sem conectividade com os endereços IP públicos. Estabelecer conectividade com uma destas opções:

* **Permitir as gamas IP do centro**de dados Azure : Permitir as [gamas IP](https://www.microsoft.com/download/details.aspx?id=41653) no download. Para aceder ao grupo de segurança da rede (NSG), utilize o **cmdlet Set-AzureNetworkSecurityRule.**
* **Implemente um servidor proxy HTTP para encaminhar**o tráfego : Quando fizer o backup de uma base de dados do SQL Server num VM Azure, a extensão de backup no VM utiliza as APIs HTTPS para enviar comandos de gestão para o Azure Backup e dados para o Armazenamento Azure. A extensão de backup também utiliza o Azure Ative Directory (Azure AD) para autenticação. Encaminhe o tráfego de extensão de reserva para estes três serviços através do proxy HTTP. As extensões são o único componente configurado para o acesso à internet pública.

Cada opção tem vantagens e desvantagens

**Opção** | **Vantagens** | **Desvantagens**
--- | --- | ---
Permitir gamas IP | Sem custos adicionais. | Complexo de gerir porque os intervalos de endereçoIP mudam ao longo do tempo. <br/><br/> Dá acesso a todo o Azure, e não apenas ao Armazenamento Azure.
Use um proxy HTTP   | É permitido o controlo granular no proxy sobre os URLs de armazenamento. <br/><br/> Ponto único de acesso à Internet a VMs. <br/><br/> Não sujeito a alterações de endereço IP Do Azure. | Custos adicionais para executar um VM com o software proxy.

### <a name="set-vm-permissions"></a>Definir permissões VM

O Azure Backup faz uma série de coisas quando configura a cópia de segurança para uma base de dados do Servidor SQL:

* Adiciona a extensão **AzureBackupWindowsWorkload.**
* Para descobrir bases de dados na máquina virtual, o Azure Backup cria a conta **NT SERVICE\AzureWLBackupPluginSvc**. Esta conta é usada para cópia de segurança e restauro, e requer permissões de sysadmina SQL.
* A Azure Backup aproveita a conta **NT AUTHORITY\SYSTEM** para a descoberta/investigação da base de dados, pelo que esta conta tem de ser um login público no SQL.

Se não criou o VM do Servidor SQL a partir do Mercado Azure, poderá receber um erro **UserErrorSQLNoSysadminMembership**. Se isto ocorrer [siga estas instruções](backup-azure-sql-database.md#set-vm-permissions).

### <a name="verify-database-naming-guidelines-for-azure-backup"></a>Verifique as diretrizes de nomeação da base de dados para o Backup Azure

Evite o seguinte nome para os nomes da base de dados:

* Espaços de trailing/líder
* A seguir '!
* Suporte quadrado próximo ']'
* Nomes de bases de dados a partir de 'F:\'

Temos pseudónimos para personagens não suportados da tabela Azure, mas recomendamos evitá-los. [Saiba mais](https://docs.microsoft.com/rest/api/storageservices/Understanding-the-Table-Service-Data-Model?redirectedfrom=MSDN).

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-sql-server-databases"></a>Descubra as bases de dados do Servidor SQL

Descubra as bases de dados em execução no VM.

1. No [portal Azure,](https://portal.azure.com)abra o cofre dos Serviços de Recuperação que usa para fazer o backup da base de dados.

2. No painel de **abóbadas** dos Serviços de Recuperação, selecione **Backup**.

   ![Selecione Backup para abrir o menu 'Backup Goal'](./media/backup-azure-sql-database/open-backup-menu.png)

3. In **Backup Goal**, set Onde está a sua carga de trabalho a **correr** para **Azure** (o padrão).

4. No **que pretende fazer backup,** selecione **SQL Server em Azure VM**.

    ![Selecione SQL Server em Azure VM para a cópia de segurança](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

5. Em **Backup Goal** > **Discover DBs em VMs**, selecione **Start Discovery** para procurar VMs desprotegidos na subscrição. Pode demorar algum tempo, dependendo do número de máquinas virtuais desprotegidas na subscrição.

   * VMs desprotegidos devem aparecer na lista após descoberta, listadas pelo nome e pelo grupo de recursos.
   * Se um VM não está listado como espera, verifique se já está num cofre.
   * Vários VMs podem ter o mesmo nome, mas pertencerão a diferentes grupos de recursos.

     ![Backup está pendente durante pesquisa de DBs em VMs](./media/backup-azure-sql-database/discovering-sql-databases.png)

6. Na lista VM, selecione o VM que executa a base de dados do Servidor SQL > **Discover DBs**.

7. Rastreie a descoberta da base de dados na área de **Notificações.** Pode levar algum tempo para o trabalho ser concluído, dependendo de quantas bases de dados estão no VM. Quando as bases de dados selecionadas são descobertas, aparece uma mensagem de sucesso.

    ![Mensagem de sucesso de implantação](./media/backup-azure-sql-database/notifications-db-discovered.png)

8. O Azure Backup descobre todas as bases de dados do SQL Server no VM. Durante a descoberta ocorre em segundo plano:

    * O Azure Backup registe o VM com o cofre para obter reforços de carga de trabalho. Todas as bases de dados do VM registado só podem ser apoiadas neste cofre.
    * A Cópia de Segurança Azure instala a extensão **AzureBackupWindowsWorkload** no VM. Nenhum agente está instalado na base de dados SQL.
    * A Azure Backup cria a conta de serviço **NT Service\AzureWLBackupPluginSvc** no VM.
      * Todas as operações de backup e restauro utilizam a conta de serviço.
      * **Serviço NT\AzureWLBackupPluginSvc** necessita de permissões de sysadmins SQL. Todos os VMs do Servidor SQL criados no Mercado Azure vêm com a **Extensão SqlIaS** instalada. A extensão **AzureBackupWindowsWorkload** utiliza a **Extensão SQLIaS** para obter automaticamente as permissões necessárias.
    * Se não criou o VM a partir do mercado, então o VM não tem a **Extensão SqlIaS** instalada, e a operação de descoberta falha com a mensagem de erro **UserErrorSQLNoSysAdminMembership**. Siga as [instruções](backup-azure-sql-database.md#set-vm-permissions) para corrigir este problema.

        ![Selecione o VM e a base de dados](./media/backup-azure-sql-database/registration-errors.png)

## <a name="configure-backup"></a>Configurar a cópia de segurança  

Configure a cópia de segurança da seguinte forma:

1. No **Objetivo de Backup,** selecione **Configurar cópia de segurança**.

   ![Selecione Configurar cópia de segurança](./media/backup-azure-sql-database/backup-goal-configure-backup.png)

2. Clique em **Configurar a Cópia de Segurança,** aparece o Select itens para a lâmina **de reserva.** Isto lista todos os grupos de disponibilidade registados e servidores SQL autónomos. Expanda o chevron para a esquerda da fila para ver todas as bases de dados desprotegidas nesse caso ou sempre em AG.  

    ![Exibindo todas as instâncias do SQL Server com bases de dados autónomas](./media/backup-azure-sql-database/list-of-sql-databases.png)

3. Selecione todas as bases de dados que pretende proteger > **OK**.

   ![Proteger a base de dados](./media/backup-azure-sql-database/select-database-to-protect.png)

   Para otimizar as cargas de backup, o Azure Backup define um número máximo de bases de dados num trabalho de backup para 50.

     * Em alternativa, pode ativar a proteção automática em toda a instância ou no grupo Always On Availability selecionando a opção **ON** na queda correspondente na coluna **AUTOPROTECT.** A função de auto-protecção não só permite a proteção em todas as bases de dados existentes de uma só vez, como também protege automaticamente quaisquer novas bases de dados que serão adicionadas a essa instância ou ao grupo de disponibilidade no futuro.  

4. Clique **em OK** para abrir a lâmina da política de **backup.**

    ![Ativar a proteção automática no grupo Always On](./media/backup-azure-sql-database/enable-auto-protection.png)

5. Em Escolha a política de **backup,** selecione uma política e, em seguida, clique em **OK**.

   * Selecione a política predefinida: Hora do LogBackup.
   * Escolha uma política de backup existente anteriormente criada para o SQL.
   * Defina uma nova política com base no seu intervalo de RPO e retenção.

     ![Selecione política de backup](./media/backup-azure-sql-database/select-backup-policy.png)

6. No menu **Backup,** **selecione 'Apoiar 'backup'.**

    ![Ativar a política de backup escolhida](./media/backup-azure-sql-database/enable-backup-button.png)

7. Acompanhe o progresso da configuração na área de **Notificações** do portal.

    ![Área de notificação](./media/backup-azure-sql-database/notifications-area.png)

### <a name="create-a-backup-policy"></a>Criar uma política de backup

Uma política de reserva define quando os backups são tomados e quanto tempo são retidos.

* Uma apólice é criada ao nível do cofre.
* Vários cofres podem usar a mesma política de reserva, mas tens de aplicar a política de reserva a cada cofre.
* Quando se cria uma política de backup, um backup completo diário é o padrão.
* Pode adicionar uma cópia de segurança diferencial, mas apenas se configurar cópias de segurança completas para ocorrer semanalmente.
* [Conheça](backup-architecture.md#sql-server-backup-types) diferentes tipos de políticas de backup.

Para criar uma política de backup:

1. No cofre, clique em **políticas** > de backup**Adicionar**.
2. No menu **Adicionar,** clique **no SQL Server em Azure VM** para definir o tipo de política.

   ![Escolha um tipo de política para a nova política de backup](./media/backup-azure-sql-database/policy-type-details.png)

3. Em **nome de Política,** insira um nome para a nova política.
4. Na **política de backup completo,** selecione uma Frequência de **Backup,** escolha **Diariamente** ou **Semanalmente**.

   * Para **o Daily,** selecione a hora e o fuso horário quando o trabalho de reserva começar.
   * Tem de executar uma cópia de segurança completa, pois não pode desligar a opção **Full Backup.**
   * Clique em **Backup Completo** para ver a política.
   * Não se pode criar backups diferenciais para cópias de segurança diárias completas.
   * Para **weekly**, selecione o dia da semana, hora e fuso horário quando o trabalho de reserva começar.

     ![Novos campos de política de backup](./media/backup-azure-sql-database/full-backup-policy.png)  

5. Para **o Range de Retenção,** por padrão todas as opções são selecionadas. Limpe os limites de alcance de retenção indesejados que não queira utilizar e detetete os intervalos a utilizar.

    * O período mínimo de retenção para qualquer tipo de cópia de segurança (full/diferencial/registo) é de sete dias.
    * Os pontos de recuperação estão marcados para retenção com base na sua gama de retenção. Por exemplo, se selecionar uma cópia de segurança completa diária, apenas um backup completo é acionado todos os dias.
    * O backup para um dia específico é marcado e retido com base na gama de retenção semanal e na sua definição semanal de retenção.
    * As gamas de retenção mensal e anual comportam-se de forma semelhante.

   ![Definições de intervalo de intervalo de retenção](./media/backup-azure-sql-database/retention-range-interval.png)

6. No menu de **política Full Backup,** selecione **OK** para aceitar as definições.
7. Para adicionar uma política de backup diferencial, selecione **Backup Diferencial**.

   ![Definições de](./media/backup-azure-sql-database/retention-range-interval.png)
   ![intervalo de intervalo de retenção Abrir o menu de política de backup diferencial](./media/backup-azure-sql-database/backup-policy-menu-choices.png)

8. Na **política de backup diferencial,** selecione **Ativar** para abrir os controlos de frequência e retenção.

    * No máximo, pode desencadear uma cópia de segurança diferencial por dia.
    * As cópias de segurança diferenciais podem ser retidas por um máximo de 180 dias. Se necessitar de retenção mais longa, deve utilizar cópias de segurança completas.

9. Selecione **OK** para salvar a apólice e volte ao menu principal de política de **backup.**

10. Para adicionar uma política de backup de registo transacional, selecione **Log Backup**.
11. No **Log Backup**, selecione **Enable**e, em seguida, detete os controlos de frequência e retenção. As cópias de segurança de log podem ocorrer com a frequência de cada 15 minutos, e podem ser retidas até 35 dias.
12. Selecione **OK** para salvar a apólice e volte ao menu principal de política de **backup.**

    ![Editar a política de backup de registo](./media/backup-azure-sql-database/log-backup-policy-editor.png)

13. No menu de **política de backup,** escolha se permite a compressão de **backup SQL**.
    * A compressão é desativada por defeito.
    * Na parte de trás, o Azure Backup utiliza a compressão de backup nativa SQL.

14. Depois de completar as edimas para a política de backup, selecione **OK**.

## <a name="run-an-on-demand-backup"></a>Faça um backup a pedido

1. No cofre dos Serviços de Recuperação, escolha itens de backup.
2. Clique em "SQL in Azure VM".
3. Clique numa base de dados e escolha "Backup now".
4. Escolha o Tipo de Backup (Completo/Diferencial/Registo/Cópia Apenas Completo) e Compressão (Ativar/Desativar)
5. Selecione OK para iniciar a cópia de segurança.
6. Monitorize o trabalho de reserva indo ao seu cofre de Serviços de Recuperação e escolhendo "Backup Jobs".

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, utilizou o portal do Azure para:

> [!div class="checklist"]
>
> * Crie e configure um cofre.
> * Descubra bases de dados e instale cópias de segurança.
> * Criar proteção automática para bases de dados.
> * Faça um reforço a pedido.

Avance para o próximo tutorial para restaurar uma máquina virtual do Azure a partir do disco.

> [!div class="nextstepaction"]
> [Restaurar as bases de dados do Servidor SQL em VMs Azure](./restore-sql-database-azure-vm.md)
