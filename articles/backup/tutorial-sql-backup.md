---
title: Fazer backup de bancos de dados SQL Server no Azure | Microsoft Docs
description: Este tutorial explica como fazer backup de SQL Server no Azure.
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: tutorial
ms.date: 06/18/2019
ms.author: dacurwin
ms.openlocfilehash: 99e1e8194594d204a5080287794362dbe028a1f5
ms.sourcegitcommit: d585cdda2afcf729ed943cfd170b0b361e615fae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/31/2019
ms.locfileid: "68688425"
---
# <a name="back-up-sql-server-databases-in-azure-vms"></a>Fazer cópias de segurança de bases de dados do SQL Server em VMs do Azure



Este artigo mostra como fazer backup de um banco de dados SQL Server em execução em uma VM do Azure para um cofre dos serviços de recuperação de backup do Azure. Neste artigo, vai aprender a:

> [!div class="checklist"]
> * Criar e configurar um cofre.
> * Descobrir bancos de dados e configurar backups.
> * Configure a proteção automática para bancos de dados.
> * Execute um backup ad hoc.


## <a name="prerequisites"></a>Pré-requisitos

Antes de fazer backup do banco de dados do SQL Server, verifique as seguintes condições:

1. Identifique ou [crie](backup-sql-server-database-azure-vms.md#create-a-recovery-services-vault) um cofre dos serviços de recuperação na mesma região ou localidade que a VM que hospeda a instância de SQL Server.
2. [Verifique as permissões de VM](backup-azure-sql-database.md#set-vm-permissions) necessárias para fazer backup dos bancos de dados SQL.
3. Verifique se a VM tem [conectividade de rede](backup-sql-server-database-azure-vms.md#establish-network-connectivity).
4. Verifique se os bancos de dados do SQL Server são nomeados de acordo com as [diretrizes de nomenclatura](#verify-database-naming-guidelines-for-azure-backup) para o backup do Azure.
5. Verifique se você não tem outras soluções de backup habilitadas para o banco de dados. Desabilite todos os outros backups de SQL Server antes de configurar este cenário. Você pode habilitar o backup do Azure para uma VM do Azure junto com o backup do Azure para um banco de dados SQL Server em execução na VM sem nenhum conflito.


### <a name="establish-network-connectivity"></a>Estabelecer conectividade de rede

Para todas as operações, a máquina virtual VM SQL Server precisa de conectividade com os endereços IP públicos do Azure. Operações de VM (descoberta de banco de dados, configurar backups, agendar backups, restaurar pontos de recuperação e assim por diante) falham sem conectividade com os endereços IP públicos. Estabeleça a conectividade com uma destas opções:

- **Permitir os intervalos de IP do datacenter do Azure**: Permitir [intervalos de IP](https://www.microsoft.com/download/details.aspx?id=41653) no download. Para acessar o NSG (grupo de segurança de rede), use o cmdlet **set-AzureNetworkSecurityRule** .
- **Implantar um servidor proxy http para rotear o tráfego**: Quando você faz backup de um banco de dados SQL Server em uma VM do Azure, a extensão de backup na VM usa as APIs HTTPS para enviar comandos de gerenciamento para o backup do Azure e dados para o armazenamento do Azure. A extensão de backup também usa Azure Active Directory (Azure AD) para autenticação. Encaminhe o tráfego de extensão de backup para esses três serviços por meio do proxy HTTP. As extensões são o único componente que está configurado para acesso à Internet pública.

Cada opção tem vantagens e desvantagens

**Opção** | **Principais** | **Desvantagens**
--- | --- | ---
Permitir intervalos de IP | Sem custos adicionais. | Complexo para gerenciar, pois os intervalos de endereços IP mudam ao longo do tempo. <br/><br/> Fornece acesso ao todo o Azure, não apenas ao armazenamento do Azure.
Usar um proxy HTTP   | O controle granular no proxy sobre as URLs de armazenamento é permitido. <br/><br/> Ponto único de acesso à Internet para VMs. <br/><br/> Não está sujeito às alterações de endereço IP do Azure. | Custos adicionais para executar uma VM com o software proxy.

### <a name="set-vm-permissions"></a>Definir permissões de VM

O backup do Azure faz várias coisas quando você configura o backup para um banco de dados SQL Server:

- Adiciona a extensão **AzureBackupWindowsWorkload** .
- Para descobrir bancos de dados na máquina virtual, o backup do Azure cria a conta **NT SERVICE\AzureWLBackupPluginSvc**. Essa conta é usada para backup e restauração e requer permissões de sysadmin do SQL.
- O backup do Azure aproveita a conta **NT AUTHORITY\SYSTEM** para a consulta/descoberta de banco de dados, portanto, essa conta precisa ser um logon público no SQL.

Se você não criou a VM SQL Server do Azure Marketplace, você pode receber um erro **UserErrorSQLNoSysadminMembership**. Se isso ocorrer, [siga estas instruções](backup-azure-sql-database.md#set-vm-permissions).

### <a name="verify-database-naming-guidelines-for-azure-backup"></a>Verificar as diretrizes de nomenclatura de banco de dados para o backup do Azure

Evite os nomes de banco de dados abaixo:

  * Espaços à direita/à esquerda
  * '! ' À direita
  * Fechar colchete '] '
  * Nomes de bancos de dados começando com ' F:\ '

Temos aliases para caracteres sem suporte da tabela do Azure, mas é recomendável evitá-los. [Saiba mais](https://docs.microsoft.com/rest/api/storageservices/Understanding-the-Table-Service-Data-Model?redirectedfrom=MSDN).


[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-sql-server-databases"></a>Descobrir bancos de dados SQL Server

Descobrir bancos de dados em execução na VM.

1. No [portal do Azure](https://portal.azure.com), abra o cofre dos serviços de recuperação que você usa para fazer backup do banco de dados.

2. No painel do **cofre dos serviços de recuperação** , selecione **backup**.

   ![Selecione backup para abrir o menu meta de backup](./media/backup-azure-sql-database/open-backup-menu.png)

3. Em **meta de backup**, defina **onde a carga de trabalho é executada** no **Azure** (o padrão).

4. Em **o que você deseja fazer backup**, selecione **SQL Server na VM do Azure**.

    ![Selecione SQL Server na VM do Azure para o backup](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

5. Em **meta** > de backup**descobrir bancos de inicialização em VMs**, selecione **Iniciar descoberta** para procurar VMs desprotegidas na assinatura. Pode demorar um pouco, dependendo do número de máquinas virtuais desprotegidas na assinatura.

   - As VMs desprotegidas devem aparecer na lista após a descoberta, listada por nome e grupo de recursos.
   - Se uma VM não estiver listada como esperado, verifique se já foi feito o backup em um cofre.
   - Várias VMs podem ter o mesmo nome, mas elas pertencerão a grupos de recursos diferentes.

     ![O backup está pendente durante a pesquisa de bancos de os em VMs](./media/backup-azure-sql-database/discovering-sql-databases.png)

6. Na lista VM, selecione a VM que está executando o banco de dados SQL Server > **descobrir bancos**.

7. Acompanhe a descoberta de banco de dados na área de **notificações** . Pode levar algum tempo para que o trabalho seja concluído, dependendo de quantos bancos de dados estão na VM. Quando os bancos de dados selecionados são descobertos, uma mensagem de êxito é exibida.

    ![Mensagem de êxito na implantação](./media/backup-azure-sql-database/notifications-db-discovered.png)

8. O backup do Azure descobre todos os bancos de dados SQL Server na VM. Durante a descoberta, o seguinte ocorre em segundo plano:

    - O backup do Azure registra a VM com o cofre para backup de carga de trabalho. Só é possível fazer backup de todos os bancos de dados na VM registrada nesse cofre.
    - O backup do Azure instala a extensão **AzureBackupWindowsWorkload** na VM. Nenhum agente está instalado no banco de dados SQL.
    - O backup do Azure cria a conta de serviço **NT Service\AzureWLBackupPluginSvc** na VM.
      - Todas as operações de backup e restauração usam a conta de serviço.
      - **NT Service\AzureWLBackupPluginSvc** precisa de permissões de sysadmin do SQL. Todas as VMs SQL Server criadas no Azure Marketplace vêm com o **SqlIaaSExtension** instalado. A extensão **AzureBackupWindowsWorkload** usa o **SQLIaaSExtension** para obter automaticamente as permissões necessárias.
    - Se você não criou a VM do Marketplace, a VM não tem o **SqlIaaSExtension** instalado e a operação de descoberta falha com a mensagem de erro **UserErrorSQLNoSysAdminMembership**. Siga as [instruções](backup-azure-sql-database.md#set-vm-permissions) para corrigir esse problema.

        ![Selecione a VM e o banco de dados](./media/backup-azure-sql-database/registration-errors.png)

## <a name="configure-backup"></a>Configurar a cópia de segurança  

Configure o backup da seguinte maneira:

1. Em **meta de backup** , selecione **Configurar backup**.

   ![Selecione configurar backup](./media/backup-azure-sql-database/backup-goal-configure-backup.png)

2. Clique em **Configurar backup**, a folha **selecionar itens para backup** é exibida. Isso lista todos os grupos de disponibilidade registrados e os SQL Servers autônomos. Expanda a divisa à esquerda da linha para ver todos os bancos de dados desprotegidos nessa instância ou Always on AG.  

    ![Exibindo todas as instâncias de SQL Server com bancos de dados autônomos](./media/backup-azure-sql-database/list-of-sql-databases.png)

3. Selecione todos os bancos de dados que você deseja proteger > **OK**.

   ![Protegendo o banco de dados](./media/backup-azure-sql-database/select-database-to-protect.png)

   Para otimizar as cargas de backup, o backup do Azure define um número máximo de bancos de dados em um trabalho de backup para 50.

    
     * Como alternativa, você pode habilitar a proteção automática em toda a instância ou Always On grupo de disponibilidade selecionando a opção **ativado** no menu suspenso correspondente na coluna AutoProtect. O recurso de proteção automática não só permite a proteção em todos os bancos de dados existentes em um só lugar, mas também protege automaticamente quaisquer novos bancos de dados que serão adicionados a essa instância ou ao grupo de disponibilidade no futuro.  

4. Clique em **OK** para abrir a folha **política de backup** .

    ![Habilitar a proteção automática no grupo de disponibilidade Always On](./media/backup-azure-sql-database/enable-auto-protection.png)

5. Em **escolher política de backup**, selecione uma política e clique em **OK**.

   - Selecione a política padrão: HourlyLogBackup.
   - Escolha uma política de backup existente criada anteriormente para SQL.
   - Defina uma nova política com base no RPO e no período de retenção.

     ![Selecionar política de backup](./media/backup-azure-sql-database/select-backup-policy.png)

6. No menu **backup** , selecione **habilitar backup**.

    ![Habilitar a política de backup escolhida](./media/backup-azure-sql-database/enable-backup-button.png)

7. Acompanhe o progresso da configuração na área **notificações** do Portal.

    ![Área de notificação](./media/backup-azure-sql-database/notifications-area.png)

### <a name="create-a-backup-policy"></a>Criar uma política de backup

Uma política de backup define quando os backups são feitos e por quanto tempo eles são retidos.

- Uma política é criada no nível do cofre.
- Vários cofres podem usar a mesma política de backup, mas você deve aplicar a política de backup a cada cofre.
- Quando você cria uma política de backup, um backup completo diário é o padrão.
- Você pode adicionar um backup diferencial, mas somente se configurar backups completos para ocorrer semanalmente.
- [Saiba mais sobre](backup-architecture.md#sql-server-backup-types) os diferentes tipos de políticas de backup.

Para criar uma política de backup:

1. No cofre, clique em **políticas** > de backup**Adicionar**.
2. No menu **Adicionar** , clique em **SQL Server na VM do Azure**. Para definir o tipo de política.

   ![Escolha um tipo de política para a nova política de backup](./media/backup-azure-sql-database/policy-type-details.png)

3. Em **nome da política**, insira um nome para a nova política.
4. Em **política de backup completo**, selecione uma **frequência de backup**, escolha **diária** ou **semanal**.

   - Para **diário**, selecione a hora e o fuso horário em que o trabalho de backup começa.
   - Você deve executar um backup completo, pois não é possível desativar a opção **backup completo** .
   - Clique em **backup completo** para exibir a política.
   - Não é possível criar backups diferenciais para backups diários completos.
   - Para **semana**, selecione o dia da semana, hora e fuso horário em que o trabalho de backup começa.

     ![Novos campos de política de backup](./media/backup-azure-sql-database/full-backup-policy.png)  

5. Para o **período de retenção**, por padrão, todas as opções são selecionadas. Desmarque os limites de período de retenção indesejados que você não quer usar e defina os intervalos a serem usados.

    - O período de retenção mínimo para qualquer tipo de backup (completo/diferencial/log) é de 7 dias.
    - Os pontos de recuperação são marcados para retenção com base em seu período de retenção. Por exemplo, se você selecionar um backup completo diário, apenas um backup completo será disparado todos os dias.
    - O backup de um dia específico é marcado e retido com base no período de retenção semanal e na configuração de retenção semanal.
    - Os intervalos de retenção mensais e anuais se comportam de maneira semelhante.

   ![Configurações de intervalo do intervalo de retenção](./media/backup-azure-sql-database/retention-range-interval.png)

6. No menu **política de backup completo** , selecione **OK** para aceitar as configurações.
7. Para adicionar uma política de backup diferencial, selecione **backup diferencial**.

   ![](./media/backup-azure-sql-database/retention-range-interval.png)
   Configurações![de intervalo de retenção de intervalo abrir o menu de política de backup diferencial](./media/backup-azure-sql-database/backup-policy-menu-choices.png)

8. Em **política de backup diferencial**, selecione **habilitar** para abrir os controles de frequência e retenção.

    - No máximo, você pode disparar um backup diferencial por dia.
    - Backups diferenciais podem ser retidos por um máximo de 180 dias. Se precisar de maior retenção, você deverá usar backups completos.

9. Selecione **OK** para salvar a política e retornar para o menu principal da **política de backup** .

10. Para adicionar uma política de backup de log transacional, selecione **backup de log**.
11. Em **backup de log**, selecione **habilitar**e defina a frequência e os controles de retenção. Os backups de log podem ocorrer com frequência a cada 15 minutos e podem ser retidos por até 35 dias.
12. Selecione **OK** para salvar a política e retornar para o menu principal da **política de backup** .

    ![Editar a política de backup de log](./media/backup-azure-sql-database/log-backup-policy-editor.png)

13. No menu **política de backup** , escolha se deseja habilitar a compactação de **backup do SQL**.
    - A compactação é desabilitada por padrão.
    - No back-end, o backup do Azure usa a compactação de backup nativo do SQL.

14. Depois de concluir as edições na política de backup, selecione **OK**.

## <a name="run-an-ad-hoc-backup"></a>Executar um backup ad hoc

1. No cofre dos serviços de recuperação, escolha itens de backup.
2. Clique em "SQL na VM do Azure".
3. Clique com o botão direito do mouse em um banco de dados e escolha "fazer backup agora".
4. Escolha o tipo de backup (completo/diferencial/log/cópia somente completa) e compactação (habilitar/desabilitar)
5. Selecione OK para iniciar o backup.
6. Monitore o trabalho de backup acessando o cofre dos serviços de recuperação e escolhendo "trabalhos de backup".


## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, utilizou o portal do Azure para:

> [!div class="checklist"]
> * Criar e configurar um cofre.
> * Descobrir bancos de dados e configurar backups.
> * Configure a proteção automática para bancos de dados.
> * Execute um backup ad hoc.

Avance para o próximo tutorial para restaurar uma máquina virtual do Azure a partir do disco.

> [!div class="nextstepaction"]
> [Restaurar bancos de dados SQL Server em VMs do Azure](./restore-sql-database-azure-vm.md)
 

