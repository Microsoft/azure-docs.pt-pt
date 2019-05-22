---
title: Cópia de segurança de bases de dados do SQL Server para o Azure | Documentos da Microsoft
description: Este tutorial explica como criar cópias de segurança do SQL Server para o Azure.
services: backup
author: dcurwin
manager: ''
ms.service: backup
ms.topic: tutorial
ms.date: 05/22/2019
ms.author: dacurwin
ms.openlocfilehash: bfe48fb1bf6a361ce79d0ddc5281a6380a5367e4
ms.sourcegitcommit: db3fe303b251c92e94072b160e546cec15361c2c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/22/2019
ms.locfileid: "66016483"
---
# <a name="back-up-sql-server-databases-in-azure-vms"></a>Fazer cópias de segurança de bases de dados do SQL Server em VMs do Azure



Este artigo mostra-lhe como fazer cópias de segurança de uma base de dados do SQL Server em execução numa VM do Azure para um cofre dos serviços de recuperação de cópia de segurança do Azure. Neste artigo, vai aprender a:

> [!div class="checklist"]
> * Criar e configurar um cofre.
> * Detetar as bases de dados e configurar cópias de segurança.
> * Configure a proteção automática para bases de dados.
> * Execute cópias de segurança ad-hoc.


## <a name="prerequisites"></a>Pré-requisitos

Antes de fazer cópias de segurança da base de dados do SQL Server, verifique as seguintes condições:

1. Identificar ou [criar](backup-sql-server-database-azure-vms.md#create-a-recovery-services-vault) um cofre de serviços de recuperação na mesma região ou região do que a VM que aloja a instância do SQL Server.
2. [Verifique as permissões de VM](backup-azure-sql-database.md#fix-sql-sysadmin-permissions) necessários para fazer backup de bancos de dados SQL.
3. Certifique-se de que a VM tem [conectividade de rede](backup-sql-server-database-azure-vms.md#establish-network-connectivity).
4. Verifique se as bases de dados do SQL Server são nomeados de acordo com [diretrizes de nomenclatura](#verify-database-naming-guidelines-for-azure-backup) para cópia de segurança do Azure.
5. Certifique-se de que não tem quaisquer outras soluções de cópia de segurança ativadas para a base de dados. Desative todos os outros backups do SQL Server antes de configurar este cenário. Pode ativar a cópia de segurança do Azure para uma VM do Azure, juntamente com a cópia de segurança do Azure para uma base de dados do SQL Server em execução na VM sem quaisquer conflitos.


### <a name="establish-network-connectivity"></a>Estabelecer conectividade de rede

Para todas as operações, a máquina virtual de VM do SQL Server necessita de conectividade para os endereços IP públicos do Azure. Operações de VM (deteção de base de dados, configurar cópias de segurança, agendar cópias de segurança, restaurar pontos de recuperação e assim por diante) falharem sem conectividade com os endereços IP públicos. Estabelecer conectividade com uma destas opções:

- **Permitir que os intervalos IP do datacenter do Azure**: Permitir a [intervalos de IP](https://www.microsoft.com/download/details.aspx?id=41653) no download. Para aceder ao grupo de segurança de rede (NSG), utilize o **Set-AzureNetworkSecurityRule** cmdlet.
- **Implementar um servidor proxy HTTP para encaminhar o tráfego**: Quando copia em segurança um banco de dados do SQL Server numa VM do Azure, a extensão de cópia de segurança na VM utiliza as APIs de HTTPS para enviar comandos de gestão para a cópia de segurança do Azure e dados para o armazenamento do Azure. A extensão de cópia de segurança também utiliza o Azure Active Directory (Azure AD) para autenticação. Encaminhe o tráfego de extensão de cópia de segurança para estes três serviços através do proxy HTTP. As extensões são o único componente que está configurado para acesso à internet pública.

Cada opção tem vantagens e desvantagens

**Opção** | **Vantagens** | **Desvantagens**
--- | --- | ---
Permitir intervalos de IP | Sem custos adicionais. | Complexo de gerir porque os intervalos de endereços IP mudam ao longo do tempo. <br/><br/> Fornece acesso a todo do Azure, não apenas o armazenamento do Azure.
Utilizar um proxy de HTTP   | Um controle granular no proxy sobre o armazenamento é permitida a URLs. <br/><br/> Único ponto de acesso à internet para VMs. <br/><br/> Não sujeitas a alterações ao endereço IP do Azure. | Custos adicionais para executar uma VM com o software de proxy.

### <a name="set-vm-permissions"></a>Definir permissões de VM

Cópia de segurança do Azure faz uma série de coisas quando configurar a cópia de segurança para uma base de dados do SQL Server:

- Adiciona os **AzureBackupWindowsWorkload** extensão.
- Para detetar as bases de dados na máquina virtual, o Azure Backup cria a conta **NT SERVICE\AzureWLBackupPluginSvc**. Esta conta é utilizada para cópia de segurança e restauro e necessita de permissões de administrador do sistema do SQL.
- Tira partido de cópia de segurança do Azure a **NT AUTHORITY\SYSTEM** de conta para deteção/consulta de base de dados, para que esta conta tem de ser um início de sessão público no SQL.

Se não criar a VM do SQL Server no Azure Marketplace, poderá receber um erro **UserErrorSQLNoSysadminMembership**. Se isto ocorrer [siga estas instruções](backup-azure-sql-database.md#fix-sql-sysadmin-permissions).

### <a name="verify-database-naming-guidelines-for-azure-backup"></a>Verifique se as diretrizes de nomenclatura de base de dados para cópia de segurança do Azure

Evitar o abaixo para nomes de base de dados:

  * Espaços à direita/líder
  * À direita '!'
  * Fechar parêntesis Reto de fecho ']'
  * Nomes de bases de dados começados por 'F:\'

Temos o aliasing de caracteres de tabelas do Azure não suportada, mas recomendamos que evite-los. [Saiba mais](https://docs.microsoft.com/rest/api/storageservices/Understanding-the-Table-Service-Data-Model?redirectedfrom=MSDN).


[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-sql-server-databases"></a>Detetar as bases de dados do SQL Server

Detete as bases de dados em execução na VM.

1. Na [portal do Azure](https://portal.azure.com), abra o Cofre de serviços de recuperação que utilizar para criar cópias de segurança da base de dados.

2. Sobre o **cofre dos serviços de recuperação** dashboard, selecione **cópia de segurança**.

   ![Selecionar cópia de segurança para abrir o menu de objetivo de cópia de segurança](./media/backup-azure-sql-database/open-backup-menu.png)

3. Na **objetivo de cópia de segurança**, defina **em que a sua carga de trabalho é executado** para **Azure** (predefinição).

4. Na **o que fazer quiser a cópia de segurança**, selecione **do SQL Server na VM do Azure**.

    ![Selecione o SQL Server na VM do Azure para a cópia de segurança](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

5. Na **objetivo de cópia de segurança** > **detetar DBs em VMs**, selecione **Iniciar deteção** para procurar as VMs não protegidas na subscrição. Pode demorar algum tempo, dependendo do número de máquinas virtuais não protegidas na subscrição.

   - VMs não protegidas devem aparecer na lista após a deteção, listada por nome e grupo de recursos.
   - Se uma VM não estiver listada como esperado, verifique se ele já foi efetuado num cofre.
   - Várias VMs podem ter o mesmo nome mas irá pertencem a diferentes grupos de recursos.

     ![Cópia de segurança está pendente durante a pesquisa do DBs em VMs](./media/backup-azure-sql-database/discovering-sql-databases.png)

6. Na lista de VM, selecione a VM em execução a base de dados do SQL Server > **detetar bds**.

7. Deteção de base de dados de controle no **notificações** área. Pode demorar algum tempo para a tarefa a concluir, consoante o número de bases de dados está na VM. Quando forem detetadas as bases de dados selecionadas, é apresentada uma mensagem de êxito.

    ![Mensagem de êxito da implementação](./media/backup-azure-sql-database/notifications-db-discovered.png)

8. Cópia de segurança do Azure Deteta todas as bases de dados do SQL Server na VM. Durante a deteção de abaixo ocorre em segundo plano:

    - Cópia de segurança do Azure registar a VM com o Cofre de cópia de segurança da carga de trabalho. Todas as bases de dados na VM registada só podem ser copiadas para este cofre.
    - O Azure Backup instala o **AzureBackupWindowsWorkload** extensão na VM. Nenhum agente está instalado na base de dados SQL.
    - O Azure Backup cria a conta de serviço **NT Service\AzureWLBackupPluginSvc** na VM.
      - Todas as operações de cópia de segurança e restauro, utilize a conta de serviço.
      - **NT Service\AzureWLBackupPluginSvc** necessita de permissões de administrador do sistema do SQL. Todas as VMs do SQL Server criado no Azure Marketplace são fornecidos com o **SqlIaaSExtension** instalado. O **AzureBackupWindowsWorkload** extensão utiliza o **SQLIaaSExtension** para obter automaticamente as permissões necessárias.
    - Se não criar a VM no marketplace, a VM não tem o **SqlIaaSExtension** instalado, e a operação de deteção falha com a mensagem de erro **UserErrorSQLNoSysAdminMembership**. Siga os [instruções](backup-azure-sql-database.md#fix-sql-sysadmin-permissions) para corrigir este problema.

        ![Selecione a VM e a base de dados](./media/backup-azure-sql-database/registration-errors.png)

## <a name="configure-backup"></a>Configurar a cópia de segurança  

Configure cópia de segurança da seguinte forma:

1. Na **objetivo de cópia de segurança** selecionar **configurar a cópia de segurança**.

   ![Selecione Configurar cópia de segurança](./media/backup-azure-sql-database/backup-goal-configure-backup.png)

2. Clique em **configurar a cópia de segurança**, o **selecionar itens para cópia de segurança** é apresentado o painel. Isto apresenta uma lista de todos os grupos de disponibilidade registados e autónomos SQL Servers. Expanda a divisa para a esquerda da linha para ver todas as bases de dados não protegidos em que a instância ou sempre em AG.  

    ![Exibir todas as instâncias do SQL Server com bases de dados autónomas](./media/backup-azure-sql-database/list-of-sql-databases.png)

3. Selecione todas as bases de dados que pretende proteger > **OK**.

   ![Proteger a base de dados](./media/backup-azure-sql-database/select-database-to-protect.png)

   Para otimizar cargas de cópia de segurança, cópia de segurança do Azure define um número máximo de bases de dados numa tarefa de cópia de segurança como 50.

    
     * Em alternativa, pode ativar a proteção automática no toda instância ou grupo de sempre disponibilidade, selecionando o **ON** opção na lista pendente correspondente no **AUTOPROTECT** coluna. A funcionalidade de proteção automática não só permite a proteção em todas as bases de dados existentes de uma vez como protege também automaticamente novas bases de dados que serão adicionados a essa instância ou o grupo de disponibilidade no futuro.  

4. Clique em **OK** para abrir o **política de cópia de segurança** painel.

    ![Ativar a proteção automática no grupo de disponibilidade Always On](./media/backup-azure-sql-database/enable-auto-protection.png)

5. Na **escolher política de cópia de segurança**, selecione uma política, em seguida, clique em **OK**.

   - Selecione a política predefinida: HourlyLogBackup.
   - Escolha uma política de cópia de segurança existente criada anteriormente para SQL.
   - Defina uma nova política baseada no período de retenção e RPO.

     ![Selecione a política de cópia de segurança](./media/backup-azure-sql-database/select-backup-policy.png)

6. No **cópia de segurança** menu, selecione **ativar cópia de segurança**.

    ![Ativar a política de cópia de segurança escolhida](./media/backup-azure-sql-database/enable-backup-button.png)

7. Controlar o progresso da configuração no **notificações** área do portal.

    ![Área de notificação](./media/backup-azure-sql-database/notifications-area.png)

### <a name="create-a-backup-policy"></a>Criar uma política de cópia de segurança

Uma política de cópia de segurança define quando os backups são feitos e o período de tempo são retidos.

- É criada uma política ao nível do cofre.
- Vários cofres podem utilizar a mesma política de cópia de segurança, mas tem de aplicar a política de cópia de segurança para cada cofre.
- Quando cria uma política de cópia de segurança, um backup completo diário é o padrão.
- Pode adicionar um backup diferencial, mas apenas se configurar cópias de segurança completas para ocorrer semanalmente.
- [Saiba mais sobre](backup-architecture.md#sql-server-backup-types) diferentes tipos de políticas de cópia de segurança.

Para criar uma política de cópia de segurança:

1. No cofre, clique em **políticas de cópia de segurança** > **Add**.
2. Na **Add** menu, clique em **do SQL Server na VM do Azure**. Para define o tipo de política.

   ![Escolha um tipo de política para a nova política de cópia de segurança](./media/backup-azure-sql-database/policy-type-details.png)

3. Na **nome da política**, introduza um nome para a nova política.
4. Na **política de cópia de segurança completa**, selecione um **frequência de cópia de segurança**, escolha **diária** ou **semanal**.

   - Para **diária**, selecione a hora e fuso horário quando começa a tarefa de cópia de segurança.
   - Tem de executar um backup completo, não pode desativar a **cópia de segurança completa** opção.
   - Clique em **cópia de segurança completa** para ver a política.
   - Não é possível criar cópias de segurança diferenciais para cópias de segurança completas diárias.
   - Para **semanal**, selecione o dia da semana, hora e fuso horário quando começa a tarefa de cópia de segurança.

     ![novos campos de política de cópia de segurança](./media/backup-azure-sql-database/full-backup-policy.png)  

5. Para **período de retenção**, por predefinição, todas as opções são selecionadas. Desmarque os limites de intervalo de retenção indesejado que não pretende utilizar e defina os intervalos de usar.

    - Período de retenção mínimo para qualquer tipo de cópia de segurança (diferencial/completo/log) é de 7 dias.
    - Pontos de recuperação são marcados para retenção com base no seu período de retenção. Por exemplo, se selecionar uma cópia de segurança completa diária, apenas uma cópia de segurança completa é acionada por dia.
    - A cópia de segurança para um dia específico é marcado e mantido com base no período de retenção semanais e a definição de retenção semanal.
    - Mensal e anual períodos de retenção se comportam de maneira semelhante.

   ![Definições de intervalo de intervalo de retenção](./media/backup-azure-sql-database/retention-range-interval.png)

6. Na **política de cópia de segurança completa** menu, selecione **OK** para aceitar as configurações.
7. Para adicionar uma política de cópia de segurança diferencial, selecione **cópia de segurança diferencial**.

   ![Definições de intervalo de intervalo de retenção](./media/backup-azure-sql-database/retention-range-interval.png)
   ![abrir o menu de política de cópia de segurança diferencial](./media/backup-azure-sql-database/backup-policy-menu-choices.png)

8. Na **política de cópia de segurança diferencial**, selecione **ativar** para abrir os controles de frequência e retenção.

    - No máximo, pode acionar uma cópia de segurança diferencial por dia.
    - As cópias de segurança diferenciais podem ser mantidas para um máximo de 180 dias. Se precisar de retenção mais longa, tem de utilizar cópias de segurança completas.

9. Selecione **OK** para guardar a política e retornar o Main **política de cópia de segurança** menu.

10. Para adicionar uma política de cópia de segurança do registo transacional, selecione **cópia de segurança do registo**.
11. Na **registo de cópia de segurança**, selecione **ativar**e, em seguida, defina os controlos de frequência e retenção. Backups de log podem ocorrer com a frequência de 15 minutos e podem ser mantidos até 35 dias.
12. Selecione **OK** para guardar a política e retornar o Main **política de cópia de segurança** menu.

    ![Editar a política de cópia de segurança do registo](./media/backup-azure-sql-database/log-backup-policy-editor.png)

13. Sobre o **política de cópia de segurança** menu, escolha se pretende ativar **compressão de cópia de segurança de SQL**.
    - Compressão está desativada por predefinição.
    - No back-end, o Azure Backup utiliza compressão de cópias de segurança nativa SQL.

14. Depois de concluir as edições para a política de cópia de segurança, selecione **OK**.

## <a name="run-an-ad-hoc-backup"></a>Executar cópias de segurança ad-hoc

1. O seu Cofre de serviços de recuperação, selecione os itens de cópia de segurança.
2. Clique em "SQL numa VM do Azure".
3. Clique com o botão direito do rato numa base de dados e escolha "Cópia de segurança agora".
4. Escolha o tipo de cópia de segurança (completa/diferencial/registo/cópia apenas completa) e a compressão (Ativar/desativar)
5. Selecione OK para iniciar a cópia de segurança.
6. Monitorize a tarefa de cópia de segurança ao aceder ao Cofre de serviços de recuperação e escolhendo "Tarefas de cópia de segurança".


## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, utilizou o portal do Azure para:

> [!div class="checklist"]
> * Criar e configurar um cofre.
> * Detetar as bases de dados e configurar cópias de segurança.
> * Configure a proteção automática para bases de dados.
> * Execute cópias de segurança ad-hoc.

Avance para o próximo tutorial para restaurar uma máquina virtual do Azure a partir do disco.

> [!div class="nextstepaction"]
> [Restaurar bases de dados do SQL Server em VMs do Azure](./restore-sql-database-azure-vm.md)
 

