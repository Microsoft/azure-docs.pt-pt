---
title: Cópia de segurança de bases de dados do SQL Server para o Azure | Documentos da Microsoft
description: Este tutorial explica como criar cópias de segurança do SQL Server para o Azure. O artigo também explica a recuperação do SQL Server.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: tutorial
ms.date: 02/19/2018
ms.author: raynew
ms.openlocfilehash: b08d4e55deac4c9dd6a11fc9a278bdcfaa739627
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58224316"
---
# <a name="back-up-sql-server-databases-on-azure-vms"></a>Fazer cópias de segurança de bases de dados do SQL Server em VMs do Azure 

Bases de dados do SQL Server são cargas de trabalho críticas que exigem um objetivo de ponto de recuperação (RPO) e a retenção de longa duração. Pode fazer backup de bancos de dados do SQL Server em execução em VMs do Azure, utilizando [Azure Backup](backup-overview.md). 

Este tutorial mostra como fazer cópias de segurança de uma base de dados do SQL Server que está em execução numa VM do Azure para um cofre dos serviços de recuperação de cópia de segurança do Azure. Neste artigo, vai aprender a:

> [!div class="checklist"]
> * Verifique se os pré-requisitos para fazer backup de uma instância do SQL Server.
> * Criar e configurar um cofre.
> * Detetar as bases de dados e configurar cópias de segurança.
> * Configure a proteção automática para bases de dados.

> [!NOTE]
> Esta funcionalidade está atualmente em pré-visualização pública.

## <a name="before-you-start"></a>Antes de começar


- Certifique-se de que tem uma instância do SQL Server em execução no Azure. Pode rapidamente [criar uma instância do SQL Server](../sql-database/sql-database-get-started-portal.md) no Azure Marketplace.
- Reveja as limitações de pré-visualização pública.
- Reveja o suporte a cenários.
- [Reveja as perguntas mais comuns](faq-backup-sql-server.md) sobre este cenário.

## <a name="preview-limitations"></a>Limitações de pré-visualização

Esta pré-visualização pública tem várias limitações.

- A VM que está a executar o SQL Server necessita de conectividade de internet para acessar os endereços IP públicos do Azure. 
- Pode criar cópias de segurança 2.000 bancos de dados do SQL Server num cofre. Se tiver mais, crie outro cofre. 
- Cópias de segurança de [distribuído grupos de disponibilidade](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/distributed-availability-groups?view=sql-server-2017) totalmente não funcionam.
- Sempre na ativação pós-falha Cluster instâncias do SQL Server (FCIs) não são suportadas para cópia de segurança.
- Configure cópia de segurança do SQL Server no portal. Atualmente, não é possível configurar a cópia de segurança com o Azure PowerShell, CLI ou as APIs REST.
- Operações de cópia de segurança e restauro de bases de dados de espelho FCI, instantâneos de base de dados e bases de dados não são suportadas.
- Não não possível proteger bases de dados com um grande número de ficheiros. O número máximo de ficheiros que são suportadas não é determinístico. Depende do número de ficheiros e também depende do comprimento do caminho dos ficheiros. 

Revisão [perguntas mais frequentes sobre](faq-backup-sql-server.md) sobre como fazer backup de bancos de dados do SQL Server.
## <a name="scenario-support"></a>Suporte a cenários

**Suporte** | **Detalhes**
--- | ---
**Implementações suportadas** | VMs do Azure Marketplace e externas (SQL Server instalado manualmente) são suportadas VMs.
**Áreas geográficas suportadas** | Sudeste da Austrália (ASE); Sul do Brasil (BRS); Canadá Central (CNC); Leste do Canadá (CE); EUA central (CUS); Ásia Oriental (EA); Leste da Austrália (AE); EUA Leste (EUS); E.U.A. Leste 2 (EUS2); Índia Central (INC); Índia do Sul (INS); Leste do Japão (JPE); Oeste do Japão (JPW); Coreia Central (KRC); Sul da Coreia (KRS); E.U.A. Central do Norte (NCUS); Europa do Norte (m); EUA Centro-Sul (SCUS); Sudeste Asiático (SEA); Sul do Reino Unido (UKS); Oeste do Reino Unido (UKW); EUA Centro-Oeste (WCUS); Europa Ocidental (WE); E.U.A. oeste (WUS); E.U.A. oeste 2 (WUS 2)
**Sistemas operativos suportados** | Windows Server 2016, Windows Server 2012 R2, Windows Server 2012<br/><br/> Linux não é atualmente suportado.
**Versões suportadas do SQL Server** | SQL Server 2017, SQL Server 2016, SQL Server 2014, SQL Server 2012.<br/><br/> Enterprise, Standard, Web, Developer, Express.

## <a name="prerequisites"></a>Pré-requisitos

Antes de fazer cópias de segurança da base de dados do SQL Server, verifique as seguintes condições:

- Identificar ou [criar um cofre dos serviços de recuperação](backup-azure-sql-database.md#create-a-recovery-services-vault) na mesma região ou região do que a VM que aloja a instância do SQL Server.
- [Verifique as permissões de VM](#fix-sql-sysadmin-permissions) que são necessários para fazer backup de bancos de dados SQL.
- Certifique-se de que a VM tem [conectividade de rede](backup-azure-sql-database.md#establish-network-connectivity).
- Verifique se as bases de dados do SQL Server são nomeados de acordo com o [diretrizes de nomenclatura para o Azure Backup](backup-azure-sql-database.md).
- Certifique-se de que não tem quaisquer outras soluções de cópia de segurança ativadas para a base de dados. Desative todos os outros backups do SQL Server antes de configurar este cenário. Pode ativar a cópia de segurança do Azure para uma VM do Azure, juntamente com a cópia de segurança do Azure para uma base de dados do SQL Server que está em execução na VM, sem quaisquer conflitos.

### <a name="establish-network-connectivity"></a>Estabelecer conectividade de rede

Para todas as operações, a máquina virtual de VM do SQL Server necessita de conectividade para os endereços IP públicos do Azure. Operações da VM (base de dados de deteção, configurar cópias de segurança, agendar cópias de segurança, restaurar pontos de recuperação) falhar sem conectividade com os endereços IP públicos. Estabelecer conectividade com uma destas opções:

- **Permitir que os intervalos IP do datacenter do Azure**: Permitir a [intervalos de IP](https://www.microsoft.com/download/details.aspx?id=41653) no download. Para obter acesso um grupo de segurança de rede (NSG), utilize o `Set-AzureNetworkSecurityRule` cmdlet.
- **Implementar um servidor proxy HTTP para encaminhar o tráfego**: Quando copia em segurança um banco de dados do SQL Server numa VM do Azure, a extensão de cópia de segurança na VM utiliza as APIs de HTTPS para enviar comandos de gestão para cópia de segurança do Azure e para enviar dados para o armazenamento do Azure. A extensão de cópia de segurança também utiliza o Azure Active Directory (Azure AD) para autenticação. Encaminhe o tráfego de extensão de cópia de segurança para estes três serviços através do proxy HTTP. A extensão é o único componente que está configurado para acesso à internet pública.

Cada opção tem vantagens e desvantagens.

**Opção** | **Vantagens** | **Desvantagens**
--- | --- | ---
Permitir intervalos de IP | Sem custos adicionais. | Complexo de gerir porque os intervalos de endereços IP mudam ao longo do tempo. <br/><br/> Fornece acesso a totalidade do Azure, não apenas para o armazenamento do Azure.
Utilizar um proxy de HTTP   | Um controle granular no proxy sobre o armazenamento é permitida a URLs. <br/><br/> Único ponto de acesso à internet para VMs. <br/><br/> Não sujeitas a alterações ao endereço IP do Azure. | Custos adicionais para executar uma VM com o software de proxy. 

### <a name="set-vm-permissions"></a>Definir permissões de VM

Cópia de segurança do Azure faz várias coisas ao configurar a cópia de segurança para uma base de dados do SQL Server:

- Cópia de segurança do Azure adiciona a **AzureBackupWindowsWorkload** extensão.
- Cópia de segurança do Azure cria a conta **NT SERVICE\AzureWLBackupPluginSvc** para detetar as bases de dados na máquina virtual. Esta conta é utilizada para cópia de segurança e restauro e necessita de permissões de administrador do sistema do SQL.
- Tira partido de cópia de segurança do Azure a **NT AUTHORITY\SYSTEM** de conta para deteção de base de dados e consulta. Esta conta tem de ser um início de sessão público no SQL Server.

Se não criar a VM do SQL Server no Azure Marketplace, poderá receber um **UserErrorSQLNoSysadminMembership** erro. Se este erro ocorrer, [siga estas instruções](#fix-sql-sysadmin-permissions).

### <a name="verify-database-naming-guidelines-for-azure-backup"></a>Verifique se as diretrizes de nomenclatura de base de dados para cópia de segurança do Azure

Evite as seguintes opções para nomes de base de dados:

* Espaços à direita/líder
* À direita exclamação ponto (!!!)
* Fechar hranatá závorka (])

Temos o aliasing de caracteres de tabelas do Azure não suportada, mas recomendamos que evite-los. [Saiba mais](https://docs.microsoft.com/rest/api/storageservices/Understanding-the-Table-Service-Data-Model?redirectedfrom=MSDN).

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-sql-server-databases"></a>Detetar as bases de dados do SQL Server

Detete as bases de dados que estão em execução na VM. 

1. Na [portal do Azure](https://portal.azure.com), abra o Cofre de serviços de recuperação que utilizar para criar cópias de segurança da base de dados.

1. Sobre o **cofre dos serviços de recuperação** dashboard, selecione **cópia de segurança**. 

   ![Selecionar cópia de segurança para abrir o menu de objetivo de cópia de segurança](./media/backup-azure-sql-database/open-backup-menu.png)

1. Na **objetivo de cópia de segurança**, defina **em que a sua carga de trabalho é executado** para **Azure** (predefinição).

1. Na **o que fazer quiser a cópia de segurança**, selecione **do SQL Server na VM do Azure**.

    ![Selecione o SQL Server na VM do Azure para a cópia de segurança](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)


1. Na **objetivo de cópia de segurança** > **detetar DBs em VMs**, selecione **Iniciar deteção** para procurar as VMs não protegidas na subscrição. Pode demorar algum tempo, dependendo do número de máquinas virtuais não protegidas na subscrição.

    ![Cópia de segurança está pendente durante a pesquisa do DBs em VMs](./media/backup-azure-sql-database/discovering-sql-databases.png)

1. Na lista de VM, selecione a VM que está a executar a base de dados do SQL Server > **detetar bds**.

1. Deteção de base de dados de controle no **notificações** área. Pode demorar algum tempo para a tarefa concluir, consoante o número de bases de dados estão na VM. Quando forem detetadas as bases de dados selecionadas, é apresentada uma mensagem de êxito.

    ![Mensagem de êxito da implementação](./media/backup-azure-sql-database/notifications-db-discovered.png)

    - VMs não protegidas devem aparecer na lista após a deteção, listada por nome e grupo de recursos.
    - Se uma VM não estiver listada como esperado, verifique se ele já foi efetuado num cofre.
    - Várias VMs podem ter o mesmo nome, mas irá pertencem a diferentes grupos de recursos. 

1. Selecione a VM que está a executar a base de dados do SQL Server > **detetar bds**. 
1. Cópia de segurança do Azure Deteta todas as bases de dados do SQL Server na VM. Durante a deteção, a atividade seguinte ocorre em segundo plano:

    - O Azure Backup registra a VM com o Cofre de cópia de segurança da carga de trabalho. Todas as bases de dados na VM registada podem ser uma cópia de segurança apenas a este cofre.
    - O Azure Backup instala o **AzureBackupWindowsWorkload** extensão na VM. Nenhum agente está instalado na base de dados SQL.
    - O Azure Backup cria a conta de serviço **NT Service\AzureWLBackupPluginSvc** na VM.
      - Todas as operações de cópia de segurança e restauro, utilize a conta de serviço.
      - **NT Service\AzureWLBackupPluginSvc** necessita de permissões de administrador do sistema do SQL. Todas as VMs do SQL Server que são criados no Azure Marketplace são fornecidos com o **SqlIaaSExtension** instalado. O **AzureBackupWindowsWorkload** extensão utiliza o **SQLIaaSExtension** para obter automaticamente as permissões necessárias.
    - Se não criar a VM no Marketplace, a VM não tem o **SqlIaaSExtension** instalado, e a operação de deteção falha com o **UserErrorSQLNoSysAdminMembership** mensagem de erro. Siga as instruções no [#fix-sql--permissões sysadmin] para corrigir este problema.

        ![Selecione a VM e a base de dados](./media/backup-azure-sql-database/registration-errors.png)



## <a name="configure-a-backup-policy"></a>Configurar uma política de cópia de segurança 

Para otimizar cargas de cópia de segurança, cópia de segurança do Azure define um número máximo de bases de dados numa tarefa de cópia de segurança como 50.

- Para proteger mais de 50 bases de dados, configure várias cópias de segurança.
- Em alternativa, pode ativar a proteção automática. A proteção automática protege bases de dados existentes de uma vez e protege automaticamente novas bases de dados que são adicionados à instância do grupo de disponibilidade.


Configure cópia de segurança da seguinte forma:

1. No dashboard do cofre, selecione **cópia de segurança**. 

   ![Selecionar cópia de segurança para abrir o menu de objetivo de cópia de segurança](./media/backup-azure-sql-database/open-backup-menu.png)

1. Na **objetivo de cópia de segurança**, defina **em que a sua carga de trabalho é executado?** para **Azure**.

1. Na **o que pretende criar cópias de segurança?**, selecione **do SQL Server na VM do Azure**.

    ![Selecione o SQL Server na VM do Azure para a cópia de segurança](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

    
1. Na **objetivo de cópia de segurança**, selecione **configurar a cópia de segurança**.

    ![Selecione Configurar cópia de segurança](./media/backup-azure-sql-database/backup-goal-configure-backup.png)

    ![Exibir todas as instâncias do SQL Server com bases de dados autónomas](./media/backup-azure-sql-database/list-of-sql-databases.png)

1. Selecione todas as bases de dados que pretende proteger > **OK**.

    ![Proteger a base de dados](./media/backup-azure-sql-database/select-database-to-protect.png)

    - Todas as instâncias do SQL Server são apresentadas (grupos autónoma e a disponibilidade).
    - Selecione a seta para baixo à esquerda do grupo de disponibilidade ou nome de instância para filtrar.

1. Na **cópia de segurança** menu, selecione **política de cópia de segurança**. 

    ![Selecione a política de cópia de segurança](./media/backup-azure-sql-database/select-backup-policy.png)

1. Na **escolher política de cópia de segurança**, selecione uma política e, em seguida, selecione **OK**.

    - Selecione a política predefinida: **HourlyLogBackup**.
    - Escolha uma política de cópia de segurança existente que foi criada anteriormente para o SQL.
    - [Definir uma nova política](backup-azure-sql-database.md#configure-a-backup-policy) baseada no período de retenção e RPO.
    - Durante a pré-visualização, não é possível editar uma política de cópia de segurança existente.
    
1. Na **cópia de segurança** menu, selecione **ativar cópia de segurança**.

    ![Ativar a política de cópia de segurança escolhida](./media/backup-azure-sql-database/enable-backup-button.png)

1. Controlar o progresso da configuração no **notificações** área do portal.

    ![Área de notificação](./media/backup-azure-sql-database/notifications-area.png)

### <a name="create-a-backup-policy"></a>Criar uma política de cópia de segurança

Uma política de cópia de segurança determina quando os backups são feitos e o período de tempo são retidos. 

- É criada uma política ao nível do cofre.
- Vários cofres podem utilizar a mesma política de cópia de segurança, mas tem de aplicar a política de cópia de segurança para cada cofre.
- Quando cria uma política de cópia de segurança, um backup completo diário é o padrão.
- Pode adicionar um backup diferencial, mas apenas se configurar cópias de segurança completas para ocorrer semanalmente.
- [Saiba mais sobre](backup-architecture.md#sql-server-backup-types) diferentes tipos de políticas de cópia de segurança.

Para criar uma política de cópia de segurança, siga estes passos:

1. No cofre, selecione **políticas de cópia de segurança** > **Add**.
1. Na **Add** menu, selecione **do SQL Server na VM do Azure**. Isso define o tipo de política.

   ![Escolha um tipo de política para a nova política de cópia de segurança](./media/backup-azure-sql-database/policy-type-details.png)

1. Na **nome da política**, introduza um nome para a nova política. 
1. Na **política de cópia de segurança completa**, selecione um **frequência de cópia de segurança**. Escolher **diária** ou **semanal**.

    - Para **diária**, selecione a hora e a hora de zona para a cópia de segurança começar.
    - Tem de executar uma cópia de segurança completa. Não é possível desativar a **cópia de segurança completa** opção.
    - Selecione **cópia de segurança completa** para ver a política. 
    - Não é possível criar cópias de segurança diferenciais para cópias de segurança completas diárias.
    - Para **semanal**, selecione o dia da semana, hora e fuso horário para a cópia de segurança começar.

     ![novos campos de política de cópia de segurança](./media/backup-azure-sql-database/full-backup-policy.png)  

1. Na **período de retenção**, todas as opções estão selecionadas por predefinição. Desmarque os limites de intervalo de retenção indesejado que não pretende utilizar e defina os intervalos que pretende utilizar. 

    - Pontos de recuperação são marcados para retenção com base no seu período de retenção. Por exemplo, se selecionar uma cópia de segurança completa diária, apenas uma cópia de segurança completa é acionada por dia.
    - A cópia de segurança para um dia específico é marcado e mantido com base no período de retenção semanais e a definição de retenção semanal.
    - Mensal e anual períodos de retenção se comportam de maneira semelhante.

   ![Definições de intervalo de intervalo de retenção](./media/backup-azure-sql-database/retention-range-interval.png)

    

1. Na **política de cópia de segurança completa** menu, selecione **OK** para aceitar as configurações.
1. Para adicionar uma política de cópia de segurança diferencial, selecione **cópia de segurança diferencial**. 

   ![Definições de intervalo de intervalo de retenção](./media/backup-azure-sql-database/retention-range-interval.png)
   ![abrir o menu de política de cópia de segurança diferencial](./media/backup-azure-sql-database/backup-policy-menu-choices.png)

1. Na **política de cópia de segurança diferencial**, selecione **ativar** para abrir os controles de frequência e retenção. 

    - No máximo, pode acionar uma cópia de segurança diferencial por dia.
    - As cópias de segurança diferenciais podem ser mantidas para um máximo de 180 dias. Se precisar de retenção mais longa, tem de utilizar cópias de segurança completas.

1. Selecione **OK** para guardar a política e retornar o Main **política de cópia de segurança** menu.

1. Para adicionar uma política de cópia de segurança do registo transacional, selecione **cópia de segurança do registo**.
1. Na **registo de cópia de segurança**, selecione **ativar**e, em seguida, defina os controlos de frequência e retenção. Backups de log podem ocorrer com a frequência de 15 minutos e podem ser mantidos até 35 dias.
1. Selecione **OK** para guardar a política e retornar o Main **política de cópia de segurança** menu.

    ![Editar a política de cópia de segurança do registo](./media/backup-azure-sql-database/log-backup-policy-editor.png)

1. Na **política de cópia de segurança** menu, escolha se pretende ativar **compressão de cópia de segurança de SQL**.
    - Compressão está desativada por predefinição.
    - No back-end, o Azure Backup utiliza compressão de cópias de segurança nativa SQL.

1. Depois de concluir as edições para a política de cópia de segurança, selecione **OK**.



## <a name="enable-auto-protection"></a>Ativar a proteção automática  

Ative a proteção automática de backup automaticamente todas as bases de dados existentes e bases de dados que são adicionados no futuro, para uma instância do SQL Server autónomo ou a um grupo de disponibilidade Always On do SQL Server. 

- Quando ativar a proteção automática e selecione uma política, as bases de dados protegidos existentes continuarão a utilizar a política anterior.
- Não existe nenhum limite no número de bases de dados que pode selecionar para a proteção automática.

Siga estes passos para ativar a proteção automática:

1. Na **itens para cópia de segurança**, selecione a instância para o qual pretende ativar a proteção automática.
1. Selecione o menu suspenso sob **Autoprotect**e definido como **no**. Em seguida, selecione **OK**.

    ![Ativar a proteção automática no grupo de disponibilidade Always On](./media/backup-azure-sql-database/enable-auto-protection.png)

1. Cópia de segurança está configurada para todas as bases de dados em conjunto e pode ser controlada no **tarefas de cópia de segurança**.


Se tiver de desativar a proteção automática, selecione o nome da instância sob **configurar a cópia de segurança**e selecione **desativar Autoprotect** para a instância. Todas as bases de dados irão continuar a criar cópias de segurança. No entanto, as bases de dados futuros não protegidos automaticamente.

![Desative a proteção automática nessa instância](./media/backup-azure-sql-database/disable-auto-protection.png)


## <a name="fix-sql-sysadmin-permissions"></a>Corrigir permissões de administrador do sistema do SQL

Se precisa corrigir as permissões devido uma **UserErrorSQLNoSysadminMembership** erro, siga estes passos:

1. Utilize uma conta com permissões de administrador do sistema do SQL Server para iniciar sessão para o SQL Server Management Studio (SSMS). A menos que precise permissões especiais, autenticação do Windows deverá funcionar.
1. Na instância do SQL Server, abra a **inícios de sessão/segurança** pasta.

    ![Abra a pasta de segurança/inícios de sessão para ver as contas](./media/backup-azure-sql-database/security-login-list.png)

1. Com o botão direito a **inícios de sessão** pasta e selecione **novo início de sessão**. Na **início de sessão - novo**, selecione **pesquisa**.

    ![No início de sessão - nova caixa de diálogo, selecione Search](./media/backup-azure-sql-database/new-login-search.png)

1. Conta de serviço do Windows virtual **NT SERVICE\AzureWLBackupPluginSvc** foi criado durante o registo de máquina virtual e a fase de deteção SQL. Introduza o nome da conta, conforme mostrado na **introduza o nome de objeto a selecionar**. Selecione **verificar nomes** para resolver o nome. Selecione **OK**.

    ![Selecione verificar nomes para resolver o nome do serviço desconhecido](./media/backup-azure-sql-database/check-name.png)


1. Na **funções de servidor**, certifique-se a **sysadmin** função está selecionada. Selecione **OK**. As permissões necessárias agora devem existir.

    ![Certifique-se de que a função de servidor sysadmin](./media/backup-azure-sql-database/sysadmin-server-role.png)

1. Associe a base de dados com o Cofre dos serviços de recuperação. No portal do Azure, no **servidores protegidos** lista, clique com o botão direito do servidor que está em estado de erro > **Redeteção de DBs**.

    ![Verifique se que o servidor tem as permissões adequadas](./media/backup-azure-sql-database/check-erroneous-server.png)

1. Verificar o progresso **notificações** área. Quando são encontrados as bases de dados selecionadas, é apresentada uma mensagem de êxito.

    ![Mensagem de êxito da implementação](./media/backup-azure-sql-database/notifications-db-discovered.png)

Também é possível habilitar [proteção automática](backup-azure-sql-database.md#enable-auto-protection) no toda instância ou grupo de disponibilidade Always On. Para ativá-lo, selecione o **ON** opção na lista pendente correspondente no **AUTOPROTECT** coluna. O [proteção automática](backup-azure-sql-database.md#enable-auto-protection) funcionalidade ativa a proteção em todas as bases de dados existentes. Ele também automaticamente protege as novas bases de dados que vão ser adicionados a essa instância ou o grupo de disponibilidade no futuro.  

   ![Ativar a proteção automática no grupo de disponibilidade Always On](./media/backup-azure-sql-database/enable-auto-protection.png)

## <a name="next-steps"></a>Passos Seguintes

- [Saiba mais sobre](restore-sql-database-azure-vm.md) restaurar cópia de segurança de bases de dados do SQL Server.
- [Saiba mais sobre](manage-monitor-sql-database-backup.md) gerir cópia de segurança de bases de dados do SQL Server.

