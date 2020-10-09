---
title: Tutorial – Fazer cópia de segurança de bases de dados do SQL Server para o Azure
description: Neste tutorial, saiba como fazer a cópia de segurança de uma base de dados do SQL Server em execução numa VM do Azure para um cofre dos Serviços de Recuperação do Azure Backup.
ms.topic: tutorial
ms.date: 06/18/2019
ms.openlocfilehash: 17a8472da2595c08cb198baaf853faf110a619fa
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88612457"
---
# <a name="back-up-a-sql-server-database-in-an-azure-vm"></a>Fazer cópia de segurança de uma base de dados do SQL Server numa VM do Azure

Este tutorial mostra-lhe como fazer a cópia de segurança de uma base de dados do SQL Server em execução numa VM do Azure para um cofre dos Serviços de Recuperação do Azure Backup. Neste artigo, vai aprender a:

> [!div class="checklist"]
>
> * Criar e configurar um cofre.
> * Detetar bases de dados e configurar cópias de segurança.
> * Configurar a proteção automática para bases de dados.
> * Executar uma cópia de segurança a pedido.

## <a name="prerequisites"></a>Pré-requisitos

Antes de fazer a cópia de segurança da base de dados do SQL Server, verifique as seguintes condições:

1. Identifique ou [crie](backup-sql-server-database-azure-vms.md#create-a-recovery-services-vault) um cofre dos Serviços de Recuperação na mesma região ou local que a VM que aloja a instância do SQL Server.
2. [Verifique as permissões da VM](backup-azure-sql-database.md#set-vm-permissions) necessárias para fazer a cópia de segurança das bases de dados SQL.
3. Verifique se a VM tem [conectividade de rede](backup-sql-server-database-azure-vms.md#establish-network-connectivity).
4. Verifique se as bases de dados do SQL Server são nomeadas de acordo com as [diretrizes de nomenclatura](#verify-database-naming-guidelines-for-azure-backup) para o Azure Backup.
5. Verifique se não tem outras soluções de cópia de segurança ativadas para a base de dados. Desative todas as outras cópias de segurança do SQL Server antes de configurar este cenário. Pode ativar o Azure Backup para uma VM do Azure juntamente com o Azure Backup para uma base de dados do SQL Server em execução na VM sem qualquer conflito.

### <a name="establish-network-connectivity"></a>Estabelecer conectividade de rede

Para todas as operações, a máquina virtual da VM do SQL Server precisa de conectividade para os endereços IP públicos do Azure. As operações da VM (detetar bases de dados, configurar cópias de segurança, agendar cópias de segurança, restaurar pontos de recuperação, entre outras) falham sem conectividade aos endereços IP públicos. Estabeleça a conectividade com uma destas opções:

* **Permitir os intervalos de IP do datacenter do Azure**: Permita os [intervalos de IP](https://www.microsoft.com/download/details.aspx?id=41653) na transferência. Para aceder ao grupo de segurança de rede (NSG), utilize o cmdlet **Set-AzureNetworkSecurityRule**.
* **Implementar um servidor proxy HTTP para encaminhar o tráfego**: Quando faz uma cópia de segurança de uma base de dados do SQL Server numa VM do Azure, a extensão da cópia de segurança na VM utiliza as APIs HTTPS para enviar comandos de gestão para o Azure Backup e dados para o Armazenamento do Microsoft Azure. A extensão da cópia de segurança também utiliza o Azure Active Directory (Azure AD) para autenticação. Encaminhe o tráfego da extensão da cópia de segurança destes três serviços através do proxy HTTP. As extensões são o único componente que está configurado para acesso à Internet pública.

Cada opção tem vantagens e desvantagens

**Opção** | **Vantagens** | **Desvantagens**
--- | --- | ---
Permitir intervalos de IP | Sem custos adicionais. | Complexo de gerir, pois os intervalos de endereços IP mudam ao longo do tempo. <br/><br/> Proporciona acesso a todo o Azure, não apenas ao Armazenamento do Microsoft Azure.
Utilizar um proxy HTTP   | O controlo granular no proxy sobre os URLs de armazenamento é permitido. <br/><br/> Ponto único de acesso à Internet para VMs. <br/><br/> Não está sujeito às alterações de endereço IP do Azure. | Custos adicionais para executar uma VM com o software proxy.

### <a name="set-vm-permissions"></a>Definir permissões da VM

O Azure Backup faz várias coisas quando configura a cópia de segurança para uma base de dados do SQL Server:

* Adiciona a extensão **AzureBackupWindowsWorkload**.
* Para detetar bases de dados na máquina virtual, o Azure Backup cria a conta **NT SERVICE\AzureWLBackupPluginSvc**. Esta conta serve para cópias de segurança e restauro e requer permissões de administrador do sistema do SQL.
* O Azure Backup aproveita a conta **NT AUTHORITY\SYSTEM** para a deteção/consulta de bases de dados, portanto, esta conta precisa de ser um início de sessão público no SQL.

Se não criou a VM do SQL Server a partir do Azure Marketplace, pode receber um erro **UserErrorSQLNoSysadminMembership**. Se ocorrer essa situação, [siga estas instruções](backup-azure-sql-database.md#set-vm-permissions).

### <a name="verify-database-naming-guidelines-for-azure-backup"></a>Verificar as diretrizes de nomenclatura da base de dados do Azure Backup

Evite o seguinte para os nomes de bases de dados:

* Espaços à direita/à esquerda
* "!" à direita
* Parêntese reto de fecho "]"
* Nomes de bases de dados a começar com "F:\"

Temos aliasing para carateres não suportados de tabelas do Azure, mas é recomendável evitá-los. [Saiba mais](/rest/api/storageservices/understanding-the-table-service-data-model).

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-sql-server-databases"></a>Descobrir bases de dados do SQL Server

Detete as bases de dados em execução na VM.

1. No [portal do Azure](https://portal.azure.com), abra o cofre dos Serviços de Recuperação que utiliza para fazer cópia de segurança da base de dados.

2. No dashboard **Cofre dos Serviços de Recuperação**, selecione **Cópia de Segurança**.

   ![Selecione Cópia de Segurança para abrir o menu Objetivo da Cópia de Segurança](./media/backup-azure-sql-database/open-backup-menu.png)

3. Em **Objetivo da Cópia de Segurança**, defina **Onde está a sua carga de trabalho a ser executada** como **Azure** (a predefinição).

4. Em **O que pretende guardar numa cópia de segurança**, selecione **SQL Server numa VM do Azure**.

    ![Selecione SQL Server numa VM do Azure para a cópia de segurança](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

5. Em **Objetivo da Cópia de Segurança** > **Detetar BDs em VMs**, selecione **Iniciar Deteção** para procurar VMs desprotegidas na subscrição. Pode demorar um pouco, dependendo do número de máquinas virtuais desprotegidas na subscrição.

   * As VMs desprotegidas devem aparecer na lista após a deteção, listadas por nome e grupo de recursos.
   * Se uma VM não estiver listada como esperado, verifique se já foi feita a cópia de segurança desta para um cofre.
   * Várias VMs podem ter o mesmo nome, mas pertencer a grupos de recursos diferentes.

     ![Cópia de segurança pendente durante a pesquisa de BDs nas VMs](./media/backup-azure-sql-database/discovering-sql-databases.png)

6. Na lista de VMs, selecione a VM que está a executar a base de dados do SQL Server > **Detetar BDs**.

7. Acompanhe a deteção de bases de dados na área **Notificações**. Pode demorar algum tempo para que a tarefa seja concluída, dependendo de quantas bases de dados estão na VM. Quando as bases de dados selecionadas são detetadas, é apresentada uma mensagem de êxito.

    ![Mensagem de implementação com êxito](./media/backup-azure-sql-database/notifications-db-discovered.png)

8. O Azure Backup deteta todas as bases de dados do SQL Server na VM. Durante a deteção, ocorre o seguinte em segundo plano:

    * O Azure Backup regista a VM com o cofre para a cópia de segurança da carga de trabalho. Todas as bases de dados na VM registada podem apenas ser incluídas na cópia de segurança deste cofre.
    * O Azure Backup instala a extensão **AzureBackupWindowsWorkload** na VM. Nenhum agente é instalado na base de dados SQL.
    * O Azure Backup cria a conta de serviço **NT Service\AzureWLBackupPluginSvc** na VM.
      * Todas as operações de cópia de segurança e restauro utilizam a conta de serviço.
      * **NT Service\AzureWLBackupPluginSvc** precisa de permissões de administrador do sistema do SQL. Todas as VMs do SQL Server criadas no Azure Marketplace são fornecidas com a **SqlIaaSExtension** instalada. A extensão **AzureBackupWindowsWorkload** utiliza a **SQLIaaSExtension** para obter automaticamente as permissões necessárias.
    * Se não criou a VM a partir do marketplace, a VM não tem a **SqlIaaSExtension** instalada e a operação de deteção falha com a mensagem de erro **UserErrorSQLNoSysAdminMembership**. Siga as [instruções](backup-azure-sql-database.md#set-vm-permissions) para corrigir este problema.

        ![Selecionar a VM e a base de dados](./media/backup-azure-sql-database/registration-errors.png)

## <a name="configure-backup"></a>Configurar a cópia de segurança  

Configure a cópia de segurança da seguinte forma:

1. Em **Objetivo da Cópia de Segurança**, selecione **Configurar a Cópia de Segurança**.

   ![Selecionar Configurar a Cópia de Segurança](./media/backup-azure-sql-database/backup-goal-configure-backup.png)

2. Depois de selecionar **Configurar a Cópia de Segurança**, é apresentado o painel **Selecionar itens para cópia de segurança**. Esta ação lista todos os grupos de disponibilidade registados e SQL Servers autónomos. Expanda a divisa à esquerda da linha para ver todas as bases de dados desprotegidas nessa instância ou Grupos de Disponibilidade AlwaysOn.  

    ![Apresentar todas as instâncias do SQL Server com bases de dados autónomas](./media/backup-azure-sql-database/list-of-sql-databases.png)

3. Selecione todas as bases de dados que pretende proteger > **OK**.

   ![Proteger a base de dados](./media/backup-azure-sql-database/select-database-to-protect.png)

   Para otimizar as cargas da cópia de segurança, o Azure Backup define o número máximo de bases de dados numa tarefa de cópia de segurança como 50.

     * Como alternativa, pode ativar a proteção automática em toda a instância ou no Grupo de disponibilidade AlwaysOn ao selecionar a opção **ATIVADO** na lista pendente correspondente na coluna **AUTOPROTECT**. A funcionalidade de proteção automática não só ativa a proteção em todas as bases de dados existentes num só lugar, mas também protege automaticamente quaisquer novas bases de dados adicionadas a essa instância ou ao grupo de disponibilidade no futuro.  

4. Selecione **OK** para abrir o painel **Política de cópias de segurança**.

    ![Ativar a proteção automática no grupo de disponibilidade AlwaysOn](./media/backup-azure-sql-database/enable-auto-protection.png)

5. Em **Escolher política de cópia de segurança**, selecione uma política e, em seguida, selecione **OK**.

   * Selecione a política predefinida: HourlyLogBackup.
   * Escolha uma política de cópias de segurança existente criada anteriormente para SQL.
   * Defina uma nova política com base no RPO e no período de retenção.

     ![Selecionar Política de cópias de segurança](./media/backup-azure-sql-database/select-backup-policy.png)

6. No menu **Cópia de segurança**, selecione **Ativar cópia de segurança**.

    ![Ativar a política de cópias de segurança escolhida](./media/backup-azure-sql-database/enable-backup-button.png)

7. Acompanhe o progresso da configuração na área **Notificações** do portal.

    ![Área de notificação](./media/backup-azure-sql-database/notifications-area.png)

### <a name="create-a-backup-policy"></a>Criar uma política de cópias de segurança

Uma política de cópias de segurança define quando as cópias de segurança são feitas e por quanto tempo são retidas.

* Uma política é criada ao nível do cofre.
* Vários cofres podem utilizar a mesma política de cópias de segurança, mas deve aplicar a política a cada cofre.
* Por predefinição, quando cria uma política de cópias de segurança, é criada uma cópia de segurança completa diariamente.
* Pode adicionar uma cópia de segurança diferencial, mas apenas se configurar a realização de cópias de segurança completas semanalmente.
* [Saiba mais](backup-architecture.md#sql-server-backup-types) sobre os diferentes tipos de políticas de cópias de segurança.

Para criar uma política de cópias de segurança:

1. No cofre, selecione **Políticas de cópias de segurança** > **Adicionar**.
2. No menu **Adicionar**, selecione **SQL Server numa VM do Azure** para definir o tipo de política.

   ![Escolher um tipo de política para a nova política de cópias de segurança](./media/backup-azure-sql-database/policy-type-details.png)

3. Em **Nome da política**, introduza um nome para a nova política.
4. Em **Política de Cópia de segurança completa**, selecione uma **Frequência de Cópia de Segurança**: escolha **Diária** ou **Semanal**.

   * Para **Diária**, selecione a hora e o fuso horário nos quais a tarefa de cópia de segurança começa.
   * Deve executar uma cópia de segurança completa, pois não pode desativar a opção **Cópia de Segurança Completa**.
   * Selecione **Cópia de Segurança Completa** para ver a política.
   * Não pode criar cópias de segurança diferenciais para cópias de segurança completas diárias.
   * Para **Semanal**, selecione o dia da semana, a hora e o fuso horário nos quais a tarefa de cópia de segurança começa.

     ![Campos da nova política de cópias de segurança](./media/backup-azure-sql-database/full-backup-policy.png)  

5. Por predefinição, todas as opções do **Período de Retenção** estão selecionadas. Desmarque quaisquer limites do período de retenção indesejados que não quer utilizar e defina os intervalos a serem utilizados.

    * O período de retenção mínimo para qualquer tipo de cópia de segurança (completa/diferencial/registo) é de sete dias.
    * Os pontos de recuperação são marcados para retenção com base no respetivo período de retenção. Por exemplo, se selecionar uma cópia de segurança completa diária, vai ser acionada apenas uma cópia de segurança completa por dia.
    * A cópia de segurança de um dia específico é marcada e retida com base no período de retenção semanal e na sua definição de retenção semanal.
    * Os períodos de retenção mensais e anuais comportam-se de forma semelhante.

   ![Definições do intervalo do período de retenção](./media/backup-azure-sql-database/retention-range-interval.png)

6. No menu **Política de Cópia de segurança completa**, selecione **OK** para aceitar as definições.
7. Para adicionar uma política de cópia de segurança diferencial, selecione **Cópia de Segurança Diferencial**.

   ![Definições do intervalo do período de retenção](./media/backup-azure-sql-database/retention-range-interval.png)
   ![Abrir o menu da política de cópia de segurança diferencial](./media/backup-azure-sql-database/backup-policy-menu-choices.png)

8. Em **Política de Cópia de segurança diferencial**, selecione **Ativar** para abrir os controlos de frequência e retenção.

    * No máximo, pode acionar uma cópia de segurança diferencial por dia.
    * As cópias de segurança diferenciais podem ser retidas durante um máximo de 180 dias. Se precisar de uma maior retenção, deve utilizar cópias de segurança completas.

9. Selecione **OK** para guardar a política e voltar ao menu principal **Política de cópia de segurança**.

10. Para adicionar uma política de cópia de segurança de registo de transações, selecione **Cópia de Segurança de Registo**.
11. Em **Cópia de Segurança de Registo**, selecione **Ativar** e, em seguida, defina os controlos de frequência e retenção. As cópias de segurança de registo podem ocorrer com uma frequência de 15 minutos e podem ser retidas por até 35 dias.
12. Selecione **OK** para guardar a política e voltar ao menu principal **Política de cópia de segurança**.

    ![Editar a política de cópia de segurança de registo](./media/backup-azure-sql-database/log-backup-policy-editor.png)

13. No menu **Política de cópia de segurança**, escolha se pretende ativar a **Compressão de Cópia de Segurança de SQL**.
    * Por predefinição, a compressão está desativada.
    * No back-end, o Azure Backup utiliza a compressão de cópia de segurança nativa de SQL.

14. Depois de concluir as edições na política de cópias de segurança, selecione **OK**.

## <a name="run-an-on-demand-backup"></a>Executar uma cópia de segurança a pedido

1. No cofre dos Serviços de Recuperação, escolha Itens de cópia de segurança.
2. Selecione "SQL na VM do Azure".
3. Clique com o botão direito do rato numa base de dados e escolha "Fazer cópia de segurança agora".
4. Escolha o Tipo de Cópia de Segurança (Completa/Diferencial/Registo/Cópia Apenas Completa) e a Compressão (Ativar/Desativar)
5. Selecione OK para iniciar a cópia de segurança.
6. Monitorize a tarefa de cópia de segurança ao aceder ao cofre dos Serviços de Recuperação e escolher "Tarefas de Cópia de Segurança".

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, utilizou o portal do Azure para:

> [!div class="checklist"]
>
> * Criar e configurar um cofre.
> * Detetar bases de dados e configurar cópias de segurança.
> * Configurar a proteção automática para bases de dados.
> * Executar uma cópia de segurança a pedido.

Avance para o próximo tutorial para restaurar uma máquina virtual do Azure a partir do disco.

> [!div class="nextstepaction"]
> [Restaurar bases de dados do SQL Server em VMs do Azure](./restore-sql-database-azure-vm.md)
