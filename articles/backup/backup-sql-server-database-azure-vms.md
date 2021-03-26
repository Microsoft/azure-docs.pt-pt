---
title: Fazer a cópia de segurança de várias VMs do SQL Server a partir do cofre
description: Neste artigo, aprenda a fazer backup das bases de dados do SQL Server em máquinas virtuais Azure com cópia de segurança Azure a partir do cofre dos Serviços de Recuperação
ms.topic: conceptual
ms.date: 09/11/2019
ms.openlocfilehash: 798dc81012ad968c3ecc287717240513a08a1349
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "105567220"
---
# <a name="back-up-multiple-sql-server-vms-from-the-recovery-services-vault"></a>Fazer o back up vários VMs sql do cofre dos Serviços de Recuperação

As bases de dados do SQL Server são cargas de trabalho críticas que requerem um objetivo de baixo ponto de recuperação (RPO) e retenção a longo prazo. Pode fazer backup das bases de dados do SQL Server em funcionamento em máquinas virtuais Azure (VMs) utilizando [o Azure Backup](backup-overview.md).

Este artigo mostra como fazer backup de uma base de dados do SQL Server que está a funcionar num Azure VM para um cofre dos Serviços de Recuperação de Backup Azure.

Neste artigo, vai aprender a:

> [!div class="checklist"]
>
> * Criar e configurar um cofre.
> * Descubra bases de dados e crie backups.
> * Configurar a proteção automática para bases de dados.

>[!NOTE]
>**A exclusão suave para o servidor SQL em Azure VM e a eliminação suave para as cargas de trabalho SAP HANA em Azure VM** já está disponível na pré-visualização.<br>
>Para se inscrever para a pré-visualização, escreva-nos em AskAzureBackupTeam@microsoft.com

## <a name="prerequisites"></a>Pré-requisitos

Antes de fazer uma versão de base de dados do SQL Server, verifique os seguintes critérios:

1. Identifique ou crie um [cofre de Serviços de Recuperação](backup-sql-server-database-azure-vms.md#create-a-recovery-services-vault) na mesma região e subscrição que o VM que hospeda a instância do SQL Server.
1. Verifique se o VM tem [conectividade de rede.](backup-sql-server-database-azure-vms.md#establish-network-connectivity)
1. Certifique-se de que as bases de dados do SQL Server seguem [as diretrizes de nomeação da base de dados para a Azure Backup](#database-naming-guidelines-for-azure-backup).
1. Certifique-se de que o comprimento combinado do nome VM do SQL Server e o nome do grupo de recursos não excedem 84 caracteres para VMs do Gestor de Recursos Azure (ou 77 caracteres para VMs clássicos). Esta limitação é porque alguns caracteres são reservados pelo serviço.
1. Verifique se não tem outras soluções de backup ativadas para a base de dados. Desative todas as outras cópias de segurança do SQL Server antes de fazer cópia de segurança na base de dados.
1. Ao utilizar o SQL Server 2008 R2 ou SQL Server 2012, poderá encontrar o problema do fuso horário para cópia de segurança, conforme descrito [aqui.](https://support.microsoft.com/help/2697983/kb2697983-fix-an-incorrect-value-is-stored-in-the-time-zone-column-of) Certifique-se de que está nas últimas atualizações cumulativas para evitar o problema relacionado com o fuso horário acima descrito. Se não for viável aplicar as atualizações à instância do SQL Server no Azure VM, desative o horário de verão (DST) para o fuso horário da máquina virtual.

> [!NOTE]
> Pode ativar o Azure Backup para um Azure VM e também para uma base de dados SQL Server que esteja a decorrer no VM sem conflitos.

### <a name="establish-network-connectivity"></a>Estabelecer conectividade de rede

Para todas as operações, um SQL Server VM requer conectividade ao serviço de Backup Azure, Azure Storage e Azure Ative Directory. Isto pode ser conseguido utilizando pontos finais privados ou permitindo o acesso aos endereços IP públicos necessários ou FQDNs. Não permitir a conectividade adequada aos serviços Azure necessários pode levar a falhas em operações como a descoberta de bases de dados, configurar backup, realizar backups e restaurar dados.

A tabela a seguir enumera as várias alternativas que pode utilizar para estabelecer conectividade:

| **Opção**                        | **Vantagens**                                               | **Desvantagens**                                            |
| --------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| Pontos finais privados                 | Permitir cópias de segurança sobre iPs privados dentro da rede virtual  <br><br>   Fornecer controlo granular no lado da rede e do cofre | Incorre [nos custos](https://azure.microsoft.com/pricing/details/private-link/) padrão do ponto final privado |
| Etiquetas de serviço NSG                  | Mais fácil de gerir à medida que as mudanças de alcance são automaticamente fundidas   <br><br>   Sem custos adicionais | Pode ser usado apenas com NSGs  <br><br>    Fornece acesso a todo o serviço |
| Tags FQDN de Firewall Azure Firewall          | Mais fácil de gerir uma vez que os FQDNs necessários são geridos automaticamente | Pode ser usado apenas com Azure Firewall                         |
| Permitir o acesso ao serviço FQDNs/IPs | Sem custos adicionais   <br><br>  Funciona com todos os aparelhos de segurança da rede e firewalls | Um conjunto alargado de IPs ou FQDNs pode ser obrigado a ser acedido   |
| Utilizar um proxy HTTP                 | Ponto único de acesso à Internet aos VMs                       | Custos adicionais para executar um VM com o software proxy         |

Mais detalhes sobre a utilização destas opções são partilhados abaixo:

#### <a name="private-endpoints"></a>Pontos finais privados

Os pontos finais privados permitem-lhe ligar-se de forma segura a partir de servidores dentro de uma rede virtual ao cofre dos Serviços de Recuperação. O ponto final privado utiliza um IP a partir do espaço de endereço VNET para o seu cofre. O tráfego de rede entre os seus recursos dentro da rede virtual e o cofre viaja pela sua rede virtual e uma ligação privada na rede de espinha dorsal da Microsoft. Isto elimina a exposição da internet pública. Leia mais sobre os pontos finais privados para o Azure Backup [aqui](./private-endpoints.md).

#### <a name="nsg-tags"></a>Etiquetas NSG

Se utilizar grupos de segurança de rede (NSG), utilize a etiqueta de serviço *AzureBackup* para permitir o acesso de saída ao Azure Backup. Além da etiqueta Azure Backup, também precisa de permitir a conectividade para a autenticação e transferência de dados, criando [regras de NSG semelhantes](../virtual-network/network-security-groups-overview.md#service-tags) para Azure *AD (AzureActiveDirectory)* e Azure Storage *(Armazenamento).*  Os seguintes passos descrevem o processo para criar uma regra para a etiqueta de backup Azure:

1. Em **Todos os Serviços,** vá aos **grupos de segurança da Rede** e selecione o grupo de segurança da rede.

1. Selecione **regras de segurança de saída** em **Definições**.

1. Selecione **Adicionar**. Introduza todos os detalhes necessários para a criação de uma nova regra, conforme descrito nas [definições de regras de segurança](../virtual-network/manage-network-security-group.md#security-rule-settings). Certifique-se de que a opção **Destino** está definida para tag de serviço *de serviço* e de **destino** está definida para *AzureBackup*.

1. **Selecione Adicionar** para salvar a regra de segurança de saída recém-criada.

Pode igualmente criar regras de segurança de saída NSG para Azure Storage e Azure AD.

#### <a name="azure-firewall-tags"></a>Tags Azure Firewall

Se estiver a utilizar o Azure Firewall, crie uma regra de aplicação utilizando a [etiqueta FQDN Azure Firewall AzureBackup .](../firewall/fqdn-tags.md)  Isto permite que todo o acesso de saída ao Azure Backup.

#### <a name="allow-access-to-service-ip-ranges"></a>Permitir o acesso às gamas IP de serviço

Se optar por permitir iPs de serviço de acesso, consulte os intervalos IP no ficheiro JSON disponível [aqui](https://www.microsoft.com/download/confirmation.aspx?id=56519). Terá de permitir o acesso aos IPs correspondentes ao Azure Backup, Azure Storage e Azure Ative Directory.

#### <a name="allow-access-to-service-fqdns"></a>Permitir o acesso ao serviço FQDNs

Também pode utilizar os seguintes FQDNs para permitir o acesso aos serviços necessários a partir dos seus servidores:

| Serviço    | Nomes de domínio a serem acedidos                             | Portas
| -------------- | ------------------------------------------------------------ | ---
| Reserva Azure  | `*.backup.windowsazure.com`                             | 443
| Armazenamento Azure | `*.blob.core.windows.net` <br><br> `*.queue.core.windows.net` | 443
| Azure AD      | Permitir o acesso às FQDNs nos termos das secções 56 e 59 de acordo com [este artigo](/office365/enterprise/urls-and-ip-address-ranges#microsoft-365-common-and-office-online) | Conforme aplicável

#### <a name="use-an-http-proxy-server-to-route-traffic"></a>Use um servidor de procuração HTTP para encaminhar o tráfego

Quando faz uma cópia de segurança de uma base de dados do SQL Server num Azure VM, a extensão de backup no VM utiliza as APIs HTTPS para enviar comandos de gestão para a Azure Backup e dados para o Azure Storage. A extensão de backup também utiliza Azure AD para autenticação. Encaminhe o tráfego da extensão da cópia de segurança destes três serviços através do proxy HTTP. Utilize a lista de IPs e FQDNs acima mencionadas para permitir o acesso aos serviços necessários. Os servidores de procuração autenticados não são suportados.

### <a name="database-naming-guidelines-for-azure-backup"></a>Diretrizes de nomeação de base de dados para backup Azure

Evite utilizar os seguintes elementos em nomes de bases de dados:

* Espaços de trailing e de liderança
* Marcas de exclamação em fuga (!)
* Fecho dos suportes quadrados (])
* Ponto e vírgula';
* Corte para a frente '/'

O aliasing está disponível para caracteres não suportados, mas recomendamos que os evitem. Para obter mais informações, consulte [Noções Básicas sobre o Modelo de Dados do Serviço Tabela](/rest/api/storageservices/understanding-the-table-service-data-model).

>[!NOTE]
>A **operação de Proteção de Configuração** para bases de dados com caracteres especiais como "+" ou "&" em seu nome não é suportada. Pode alterar o nome da base de dados ou ativar a **Proteção Automática,** que pode proteger com sucesso estas bases de dados.

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-sql-server-databases"></a>Descobrir bases de dados do SQL Server

Como descobrir bases de dados em execução num VM:

1. No [portal do Azure](https://portal.azure.com), abra o cofre dos Serviços de Recuperação que utiliza para fazer cópia de segurança da base de dados.

2. No painel de **abóbada dos Serviços de Recuperação,** selecione **Backup**.

   ![Selecione Cópia de Segurança para abrir o menu Objetivo da Cópia de Segurança](./media/backup-azure-sql-database/open-backup-menu.png)

3. In **Backup Goal**, set Where is your **workload running?**

4. Em **O que pretende guardar numa cópia de segurança**, selecione **SQL Server numa VM do Azure**.

    ![Selecione SQL Server numa VM do Azure para a cópia de segurança](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

5. Em **Objetivo da Cópia de Segurança** > **Detetar BDs em VMs**, selecione **Iniciar Deteção** para procurar VMs desprotegidas na subscrição. Esta pesquisa pode demorar algum tempo, dependendo do número de VMs desprotegidos na subscrição.

   * As VMs desprotegidas devem aparecer na lista após a deteção, listadas por nome e grupo de recursos.
   * Se um VM não está listado como espera, veja se já está num cofre.
   * Vários VMs podem ter o mesmo nome, mas pertencerão a diferentes grupos de recursos.

     ![Cópia de segurança pendente durante a pesquisa de BDs nas VMs](./media/backup-azure-sql-database/discovering-sql-databases.png)

6. Na lista de VMs, selecione a VM que está a executar a base de dados do SQL Server > **Detetar BDs**.

7. Rastreio de descoberta de bases de dados em **Notificações.** O tempo necessário para esta ação depende do número de bases de dados VM. Quando as bases de dados selecionadas são detetadas, é apresentada uma mensagem de êxito.

    ![Mensagem de implementação com êxito](./media/backup-azure-sql-database/notifications-db-discovered.png)

8. O Azure Backup deteta todas as bases de dados do SQL Server na VM. Durante a descoberta, os seguintes elementos ocorrem em segundo plano:

    * A Azure Backup regista o VM com o cofre para cópia de segurança da carga de trabalho. Todas as bases de dados do VM registado só podem ser registadas neste cofre.
    * O Azure Backup instala a extensão AzureBackupWindowsWorkload na VM. Nenhum agente está instalado numa base de dados SQL.
    * O Azure Backup cria a conta de serviço NT Service\AzureWLBackupPluginSvc na VM.
      * Todas as operações de cópia de segurança e restauro utilizam a conta de serviço.
      * O serviço NT\AzureWLBackupPluginSvc requer permissões de sysadmin SQL. Todos os VMs do Servidor SQL criados no Marketplace vêm com a instalação sqlIaaSExtension. A extensão AzureBackupWindowsWorkload utiliza a SQLIaaSExtension para obter automaticamente as permissões necessárias.
    * Se não criou o VM a partir do Marketplace ou se estiver no SQL 2008 e 2008 R2, o VM pode não ter a SqlIaaSExtension instalada, e a operação de descoberta falha com a mensagem de erro UserErrorSQLNoSysAdminMembership. Para corrigir este problema, siga as instruções sob [as permissões set VM](backup-azure-sql-database.md#set-vm-permissions).

        ![Selecionar a VM e a base de dados](./media/backup-azure-sql-database/registration-errors.png)

## <a name="configure-backup"></a>Configurar a cópia de segurança  

1. In **Backup Goal** Step  >  **2: Configure Backup**, selecione **Configure Backup**.

   ![Selecionar Configurar a Cópia de Segurança](./media/backup-azure-sql-database/backup-goal-configure-backup.png)

1. Selecione **Adicionar Recursos** para ver todos os grupos de disponibilidade registados e instâncias autónomas do SQL Server.

    ![Selecione adicionar recursos](./media/backup-azure-sql-database/add-resources.png)

1. No **ecrã de reserva selecione itens para o** ecrã de reserva, selecione a seta à esquerda de uma linha para expandir a lista de todas as bases de dados desprotegidas nesse caso ou grupo de disponibilidade Always On.

    ![Selecione itens para backup](./media/backup-azure-sql-database/select-items-to-backup.png)

1. Escolha todas as bases de dados que pretende proteger e, em seguida, selecione **OK**.

   ![Proteger a base de dados](./media/backup-azure-sql-database/select-database-to-protect.png)

   Para otimizar as cargas da cópia de segurança, o Azure Backup define o número máximo de bases de dados numa tarefa de cópia de segurança como 50.

     * Para proteger mais de 50 bases de dados, configuure várias cópias de segurança.
     * Para [ativar](#enable-auto-protection) toda a instância ou o grupo de disponibilidade Always On, na lista de drop-down **AUTOPROTECT,** selecione  **ON** e, em seguida, selecione **OK**.

         > [!NOTE]
         > A funcionalidade [de proteção automática](#enable-auto-protection) não só permite a proteção em todas as bases de dados existentes de uma só vez, como também protege automaticamente quaisquer novas bases de dados adicionadas a esse caso ou ao grupo de disponibilidade.  

1. Defina a **política de backup**. Pode optar por uma das seguintes opções:

   * Selecione a política predefinitiva como *HourlyLogBackup*.
   * Escolha uma política de cópias de segurança existente criada anteriormente para SQL.
   * Defina uma nova política com base no RPO e no período de retenção.

     ![Selecionar Política de cópias de segurança](./media/backup-azure-sql-database/select-backup-policy.png)

1. **Selecione Ativar** a Cópia de Segurança para submeter a **operação de Proteção de Configuração** e acompanhar o progresso da configuração na área de **Notificações** do portal.

   ![Progresso da configuração da faixa](./media/backup-azure-sql-database/track-configuration-progress.png)

### <a name="create-a-backup-policy"></a>Criar uma política de cópias de segurança

Uma política de cópias de segurança define quando as cópias de segurança são feitas e por quanto tempo são retidas.

* Uma política é criada ao nível do cofre.
* Vários cofres podem utilizar a mesma política de cópias de segurança, mas deve aplicar a política a cada cofre.
* Por predefinição, quando cria uma política de cópias de segurança, é criada uma cópia de segurança completa diariamente.
* Pode adicionar uma cópia de segurança diferencial, mas apenas se configurar a realização de cópias de segurança completas semanalmente.
* Conheça [diferentes tipos de políticas de backup.](backup-architecture.md#sql-server-backup-types)

Para criar uma política de cópias de segurança:

1. No cofre, selecione **Políticas de cópias de segurança** > **Adicionar**.
1. Em **Adicionar**, selecione **SQL Server em Azure VM** para definir o tipo de política.

   ![Escolher um tipo de política para a nova política de cópias de segurança](./media/backup-azure-sql-database/policy-type-details.png)

1. Em **Nome da política**, introduza um nome para a nova política.

    ![Insira o nome da política](./media/backup-azure-sql-database/policy-name.png)

1. Selecione o link **Edit** correspondente, para **cópia de segurança completa,** para modificar as definições predefinidos.

   * Selecione uma **frequência de backup**. Escolha **o Daily** ou **o Weekly**.
   * Para **Diária**, selecione a hora e o fuso horário nos quais a tarefa de cópia de segurança começa. Não pode criar cópias de segurança diferenciais para cópias de segurança completas diárias.

     ![Campos da nova política de cópias de segurança](./media/backup-azure-sql-database/full-backup-policy.png)  

1. No **INTERVALO DE RETENÇÃO,** todas as opções são selecionadas por predefinição. Limpe os limites de alcance de retenção que não deseja e, em seguida, desa um pouco dos intervalos a utilizar.

    * O período mínimo de retenção para qualquer tipo de backup (completo, diferencial e log) é de sete dias.
    * Os pontos de recuperação são marcados para retenção com base no respetivo período de retenção. Por exemplo, se selecionar uma cópia de segurança completa diária, vai ser acionada apenas uma cópia de segurança completa por dia.
    * A cópia de segurança para um dia específico é marcada e mantida com base na gama de retenção semanal e na definição de retenção semanal.
    * As gamas de retenção mensais e anual comportam-se de forma semelhante.

       ![Definições do intervalo do período de retenção](./media/backup-azure-sql-database/retention-range-interval.png)

1. Selecione **OK** para aceitar a definição para cópias de segurança completas.
1. Selecione o link **Editar** correspondente à **cópia de segurança diferencial,** para modificar as definições predefinidos.

    * Em **Política de Cópia de segurança diferencial**, selecione **Ativar** para abrir os controlos de frequência e retenção.
    * Só pode acionar uma cópia de segurança diferencial por dia. Um reforço diferencial não pode ser acionado no mesmo dia que um reforço completo.
    * As cópias de segurança diferenciais podem ser retidas durante um máximo de 180 dias.
    * A cópia de segurança diferencial não é suportada para a base de dados principal.

      ![Política de backup diferencial](./media/backup-azure-sql-database/differential-backup-policy.png)

1. Selecione o link **editar** correspondente à **cópia de segurança do Registo,** para modificar as definições predefinidos

    * Em **Cópia de Segurança de Registo**, selecione **Ativar** e, em seguida, defina os controlos de frequência e retenção.
    * As cópias de segurança podem ocorrer com a maior frequência a cada 15 minutos e podem ser mantidas até 35 dias.
    * Se a base de dados estiver no [modelo de recuperação simples,](/sql/relational-databases/backup-restore/recovery-models-sql-server)o horário de cópia de segurança para essa base de dados será interrompido e, por isso, não serão acionados os backups de registo.
    * Se o modelo de recuperação da base de dados mudar de **Full** para **Simple,** as cópias de segurança de registo serão interrompidas no prazo de 24 horas após a alteração do modelo de recuperação. Da mesma forma, se o modelo de recuperação mudar de **Simples**, implicando que as cópias de segurança de registo podem agora ser suportadas na base de dados, os horários de backup de registo serão ativados dentro de 24 horas após a alteração do modelo de recuperação.

      ![Política de backup de registo](./media/backup-azure-sql-database/log-backup-policy.png)

1. No menu **de política de cópia de segurança,** escolha se ativa ou não a **compressão de backup SQL.** Esta opção está desativada por predefinição. Se estiver ativado, o SQL Server enviará um fluxo de backup comprimido para o VDI. O Azure Backup substitui os padrãos de nível de instância com cláusula compression /NO_COMPRESSION dependendo do valor deste controlo.

1. Depois de concluir as edições na política de cópias de segurança, selecione **OK**.

> [!NOTE]
> Cada cópia de segurança de registo está acorrentada à cópia de segurança completa anterior para formar uma cadeia de recuperação. Este backup completo será mantido até que a retenção da última cópia de segurança de registo tenha expirado. Isto pode significar que a cópia de segurança completa é retida por um período extra para garantir que todos os registos podem ser recuperados. Vamos supor que tem uma cópia de segurança semanal, diferencial diário e registos de 2 horas. Todos estão retidos por 30 dias. Mas, a totalidade semanal só pode ser limpa/apagada após a próxima cópia de segurança completa disponível, ou seja, após 30 + 7 dias. Por exemplo, um backup semanal completo acontece no dia 16 de novembro. De acordo com a política de retenção, deve ser mantida até 16 de dezembro. O último registo para este registo completo acontece antes do próximo dia 22 de novembro. Até que este registo esteja disponível até 22 de dezembro, o 16 de novembro completo não pode ser apagado. Assim, o 16º de novembro completo é mantido até 22 de dezembro.

## <a name="enable-auto-protection"></a>Ativar a proteção automática  

Pode permitir que a proteção automática faça o back-up automaticamente de todas as bases de dados existentes e futuras para uma instância autónoma do SQL Server ou para um grupo de disponibilidade Always On.

* Não há limite para o número de bases de dados que pode selecionar para proteção automática de cada vez. A descoberta normalmente corre a cada oito horas. No entanto, pode descobrir e proteger novas bases de dados imediatamente se executar manualmente uma descoberta selecionando a opção **Redescobrir DBs.**
* Não é possível proteger ou excluir seletivamente bases de dados de proteção, numa instância no momento em que ativa a proteção automática.
* Se o seu caso já inclui algumas bases de dados protegidas, elas permanecerão protegidas sob as respetivas políticas, mesmo depois de ligar a auto-protecção. Todas as bases de dados desprotegidas adicionadas posteriormente terão apenas uma política que definir no momento de permitir a auto-protecção, listada no âmbito do **Configure Backup**. No entanto, pode alterar a política associada a uma base de dados auto-protegida mais tarde.  

Para permitir a auto-protecção:

  1. Em **Itens para fazer cópia de segurança,** selecione a instância para a qual pretende ativar a proteção automática.
  2. Selecione a lista de drop-down em **AUTOPROTECT,** escolha **ON** e, em seguida, selecione **OK**.

      ![Permitir a proteção automática no grupo de disponibilidade](./media/backup-azure-sql-database/enable-auto-protection.png)

  3. A cópia de segurança está configurada para todas as bases de dados em conjunto e pode ser rastreada em **Backup Jobs**.

Se precisar de desativar a proteção automática, selecione o nome da instância em **"Configurar Backup"** e, em seguida, selecione **Desativar** o Protecto Automático para o caso. Todas as bases de dados continuarão a ser apoiadas, mas as futuras bases de dados não serão automaticamente protegidas.

![Desativar a proteção automática nesse caso](./media/backup-azure-sql-database/disable-auto-protection.png)

## <a name="next-steps"></a>Passos seguintes

Aprenda a:

* [Restaurar bases de dados de servidores SQL com ressarçamento](restore-sql-database-azure-vm.md)
* [Gerir bases de dados de servidores SQL com apoio](manage-monitor-sql-database-backup.md)
