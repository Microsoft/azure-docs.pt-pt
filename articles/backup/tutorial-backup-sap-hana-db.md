---
title: Tutorial - Apoiar bases de dados SAP HANA em VMs Azure
description: Neste tutorial, aprenda a apoiar as bases de dados SAP HANA em execução na Azure VM até um cofre dos Serviços de Recuperação de Backup Azure.
ms.topic: tutorial
ms.date: 02/24/2020
ms.openlocfilehash: 123f27a6e2114ed17cbb5e11b34202c17ba69a2d
ms.sourcegitcommit: 99d016949595c818fdee920754618d22ffa1cd49
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/15/2020
ms.locfileid: "84770735"
---
# <a name="tutorial-back-up-sap-hana-databases-in-an-azure-vm"></a>Tutorial: Apoiar as bases de dados SAP HANA num Azure VM

Este tutorial mostra-lhe como fazer backup das bases de dados SAP HANA que estão a decorrer em VMs Azure para um cofre dos Serviços de Recuperação de Backup Azure. Neste artigo você vai aprender a:

> [!div class="checklist"]
>
> * Criar e configurar um cofre
> * Descubra bases de dados
> * Configurar backups

[Aqui](sap-hana-backup-support-matrix.md#scenario-support) estão todos os cenários que apoiamos atualmente.

>[!NOTE]
>[Começa](https://docs.microsoft.com/azure/backup/tutorial-backup-sap-hana-db) com a pré-visualização de backup SAP HANA para RHEL (7.4, 7.6, 7.7 ou 8.1). Para mais perguntas, escreva-nos em [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com) .

## <a name="prerequisites"></a>Pré-requisitos

Certifique-se de que faz o seguinte antes de configurar backups:

* Identifique ou crie um [cofre dos Serviços de Recuperação](backup-sql-server-database-azure-vms.md#create-a-recovery-services-vault) na mesma região e subscrição que o VM que executa o SAP HANA.
* Permitir a conectividade do VM para a internet, para que possa chegar ao Azure, conforme descrito no procedimento de conectividade de [rede configurado](#set-up-network-connectivity) abaixo.
* Certifique-se de que o comprimento combinado do nome VM do servidor SAP HANA e o nome do Grupo de Recursos não excedem 84 caracteres para O Gestor resoure Azure (ARM_ VMs (e 77 caracteres para VMs clássicos). Esta limitação é porque alguns caracteres são reservados pelo serviço.
* Deve existir uma chave na **hdbuserstore** que presivam os seguintes critérios:
  * Deve estar presente na **hdbuserstore**padrão. O padrão é a conta sob a `<sid>adm` qual o SAP HANA está instalado.
  * Para o MDC, a chave deve apontar para a porta SQL de **NAMEERVER**. No caso do SDC, deve apontar para a porta SQL do **INDEXSERVER**
  * Deve ter credenciais para adicionar e apagar utilizadores
* Executar o script de configuração de backup SAP HANA (script pré-registo) na máquina virtual onde o HANA está instalado, como utilizador principal. [Este guião](https://aka.ms/scriptforpermsonhana) prepara o sistema HANA para cópias de segurança. Consulte a secção [O que o script de pré-registo faz](#what-the-pre-registration-script-does) para saber mais sobre o script pré-registo.

>[!NOTE]
>O script de pré-registo instala o **compat-unixODBC234** para cargas de trabalho SAP HANA em execução em RHEL (7.4, 7.6 e 7.7) e **unixODBC** para RHEL 8.1. [Este pacote está localizado no RHEL para SAP HANA (para RHEL 7 Server) Update Services for SAP Solutions (RPMs) repo](https://access.redhat.com/solutions/5094721).  Para a imagem Azure Marketplace RHEL, o repo seria **rhui-rhel-sap-hana-for-rhel-7-server-rhui-e4s-rpms**.

## <a name="set-up-network-connectivity"></a>Configurar conectividade de rede

Para todas as operações, o SAP HANA VM requer conectividade com endereços IP públicos Azure. As operações VM (descoberta de base de dados, cópias de segurança de configuração, cópias de segurança de agenda, restaurar pontos de recuperação, e assim por diante) falham sem conectividade com os endereços IP públicos do Azure.

Estabelecer conectividade utilizando uma das seguintes opções:

### <a name="allow-the-azure-datacenter-ip-ranges"></a>Permitir as gamas IP do datacenter Azure

Esta opção permite que os [intervalos de IP](https://www.microsoft.com/download/details.aspx?id=41653) no ficheiro descarregado. Para aceder a um grupo de segurança de rede (NSG), utilize o cmdlet Set-AzureNetworkSecurityRule. Se a sua lista de destinatários seguros apenas inclui IPs específicos da região, também terá de atualizar os destinatários seguros listando a etiqueta de serviço Azure Ative (Azure AD) para permitir a autenticação.

### <a name="allow-access-using-nsg-tags"></a>Permitir o acesso usando tags NSG

Se utilizar o NSG para restringir a conectividade, então deve utilizar a etiqueta de serviço AzureBackup para permitir o acesso de saída à Azure Backup. Além disso, deve também permitir a conectividade para a autenticação e transferência de dados utilizando [regras](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) para AZure AD e Azure Storage. Isto pode ser feito a partir do portal Azure ou via PowerShell.

Para criar uma regra utilizando o portal:

  1. Em **Todos os Serviços,** vá aos **grupos de segurança da Rede** e selecione o grupo de segurança da rede.
  2. Selecione **regras de segurança de saída** em **Definições**.
  3. Selecione **Adicionar**. Introduza todos os detalhes necessários para a criação de uma nova regra, conforme descrito nas [definições de regras de segurança](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group#security-rule-settings). Certifique-se de que a opção **Destino** está definida para tag de serviço **de serviço** e de **destino** está definida para **AzureBackup**.
  4. Clique **em Adicionar**, para guardar a regra de segurança de saída recém-criada.

Para criar uma regra utilizando o PowerShell:

 1. Adicione credenciais de conta Azure e atualize as nuvens nacionais<br/>
      `Add-AzureRmAccount`<br/>

 2. Selecione a subscrição do NSG<br/>
      `Select-AzureRmSubscription "<Subscription Id>"`

 3. Selecione o NSG<br/>
    `$nsg = Get-AzureRmNetworkSecurityGroup -Name "<NSG name>" -ResourceGroupName "<NSG resource group name>"`

 4. Adicionar regra de saída para a etiqueta de serviço de backup Azure<br/>
    `Add-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg -Name "AzureBackupAllowOutbound" -Access Allow -Protocol * -Direction Outbound -Priority <priority> -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix "AzureBackup" -DestinationPortRange 443 -Description "Allow outbound traffic to Azure Backup service"`

 5. Adicionar regra de saída para etiqueta de serviço de armazenamento<br/>
    `Add-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg -Name "StorageAllowOutbound" -Access Allow -Protocol * -Direction Outbound -Priority <priority> -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix "Storage" -DestinationPortRange 443 -Description "Allow outbound traffic to Azure Backup service"`

 6. Adicionar regra de saída para a etiqueta de serviço AzureActiveDirectory<br/>
    `Add-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg -Name "AzureActiveDirectoryAllowOutbound" -Access Allow -Protocol * -Direction Outbound -Priority <priority> -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix "AzureActiveDirectory" -DestinationPortRange 443 -Description "Allow outbound traffic to AzureActiveDirectory service"`

 7. Salvar o NSG<br/>
    `Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg`

**Permitir o acesso utilizando tags Azure Firewall**. Se estiver a utilizar o Azure Firewall, crie uma regra de aplicação utilizando a [etiqueta FQDN](https://docs.microsoft.com/azure/firewall/fqdn-tags)AzureBackup . Isto permite o acesso de saída ao Azure Backup.

**Implementar um servidor de procuração HTTP para encaminhar o tráfego**. Quando faz uma cópia de segurança de uma base de dados SAP HANA num Azure VM, a extensão de backup no VM utiliza as APIs HTTPS para enviar comandos de gestão para a Azure Backup e dados para o Azure Storage. A extensão de backup também utiliza Azure AD para autenticação. Encaminhe o tráfego de extensão de backup para estes três serviços através do representante HTTP. As extensões são o único componente configurado para acesso à internet pública.

As opções de conectividade incluem as seguintes vantagens e desvantagens:

**Opção** | **Vantagens** | **Desvantagens**
--- | --- | ---
Permitir gamas IP | Sem custos adicionais | Complexo de gerir porque os intervalos de endereço IP mudam ao longo do tempo <br/><br/> Fornece acesso a todo o Azure, não apenas Azure Storage
Use tags de serviço NSG | Mais fácil de gerir à medida que as mudanças de alcance são automaticamente fundidas <br/><br/> Sem custos adicionais <br/><br/> | Pode ser usado apenas com NSGs <br/><br/> Fornece acesso a todo o serviço
Use tags FQDN de Firewall Azure Firewall | Mais fácil de gerir como os FQDNs necessários são geridos automaticamente | Pode ser usado apenas com Azure Firewall
Use um representante HTTP | É permitido o controlo granular no proxy sobre os URLs de armazenamento <br/><br/> Ponto único de acesso à Internet aos VMs <br/><br/> Não sujeito a alterações de endereço Azure IP | Custos adicionais para executar um VM com o software proxy

## <a name="what-the-pre-registration-script-does"></a>O que o script pré-registo faz

Executar o script de pré-registo executa as seguintes funções:

* Com base na sua distribuição Linux, o script instala ou atualiza os pacotes necessários pelo agente Azure Backup.
* Realiza verificações de conectividade de rede de saída com servidores Azure Backup e serviços dependentes como O Azure Ative Directory e Azure Storage.
* Entra no seu sistema HANA utilizando a chave de utilizador listada como parte dos [pré-requisitos](#prerequisites). A chave do utilizador é utilizada para criar um utilizador de cópia de segurança (AZUREWLBACKUPHANAUSER) no sistema HANA e a chave do utilizador pode ser eliminada após o script pré-registo ser executado com sucesso.
* A AZUREWLUPHANAUSER é atribuída a estas funções e permissões necessárias:
  * BASE DE DADOS ADMIN (no caso de MDC) e BACKUP ADMIN (no caso do SDC): criar novas bases de dados durante a restauração.
  * CATÁLOGO LEIA: para ler o catálogo de backup.
  * SAP_INTERNAL_HANA_SUPPORT: aceder a algumas mesas privadas.
* O script adiciona uma chave para **a hdbuserstore** para AZUREWLBACKUPHANAUSER para o plug-in de backup HANA para lidar com todas as operações (consultas de base de dados, operações de restauro, configuração e backup de execução).

>[!NOTE]
> Pode passar explicitamente a chave de utilizador listada como parte dos [pré-requisitos](#prerequisites) como parâmetro para o script de pré-registo:`-sk SYSTEM_KEY_NAME, --system-key SYSTEM_KEY_NAME` <br><br>
>Para saber que outros parâmetros o script aceita, use o comando`bash msawb-plugin-config-com-sap-hana.sh --help`

Para confirmar a criação da chave, executar o comando HDBSQL na máquina HANA com credenciais SIDADM:

```hdbsql
hdbuserstore list
```

A saída do comando deve exibir a tecla {SID}{DBNAME}, com o utilizador apresentado como AZUREWLBACKUPHANAUSER.

>[!NOTE]
> Certifique-se de que tem um conjunto único de ficheiros SSFS em `/usr/sap/{SID}/home/.hdb/` . Deve haver apenas uma pasta neste caminho.

## <a name="create-a-recovery-service-vault"></a>Criar um cofre de serviço de recuperação

Um cofre dos Serviços de Recuperação é uma entidade que armazena os backups e pontos de recuperação criados ao longo do tempo. O cofre dos Serviços de Recuperação também contém as políticas de backup que estão associadas às máquinas virtuais protegidas.

Para criar um cofre dos Serviços de Recuperação:

1. Inscreva-se na sua subscrição no [portal Azure.](https://portal.azure.com/)

2. No menu à esquerda, selecione **Todos os serviços**

   ![Selecione Todos os serviços](./media/tutorial-backup-sap-hana-db/all-services.png)

3. Na caixa de diálogo **de todos os serviços,** insira **os Serviços de Recuperação.** A lista de filtros de recursos de acordo com a sua entrada. Na lista de recursos, selecione **Cofres dos Serviços de Recuperação.**

   ![Selecione cofres dos Serviços de Recuperação](./media/tutorial-backup-sap-hana-db/recovery-services-vaults.png)

4. No painel de cofres **dos Serviços de Recuperação,** selecione **Add**.

   ![Adicionar cofre de serviços de recuperação](./media/tutorial-backup-sap-hana-db/add-vault.png)

   A caixa de diálogo do **cofre dos Serviços de Recuperação** abre. Fornecer valores para o **Nome, Subscrição, Grupo de Recursos** e **Localização**

   ![Criar cofre dos Serviços de Recuperação](./media/tutorial-backup-sap-hana-db/create-vault.png)

   * **Nome**: O nome é usado para identificar o cofre dos serviços de recuperação e deve ser exclusivo da assinatura Azure. Especifique um nome que tenha pelo menos dois, mas não mais de 50 caracteres. O nome deve começar com uma letra e consistir apenas em letras, números e hífenes. Para este tutorial, usamos o nome **SAPHanaVault.**
   * **Assinatura**: Escolha a subscrição para utilizar. Se for membro de apenas uma subscrição, verá esse nome. Se não tiver a certeza de qual subscrição utilizar, utilize a subscrição padrão (sugerida). Só existem múltiplas escolhas se o seu trabalho ou conta escolar estiver associado a mais de uma subscrição do Azure. Aqui, usamos a assinatura de assinatura de **laboratório de solução SAP HANA.**
   * **Grupo de recursos**: Utilize um grupo de recursos existente ou crie um novo. Aqui, usamos **SAPHANADemo.**<br>
   Para ver a lista de grupos de recursos disponíveis na sua subscrição, selecione **Use existente**e, em seguida, selecione um recurso da caixa de listas drop-down. Para criar um novo grupo de recursos, selecione **Criar novo** e insira o nome. Para obter informações completas sobre grupos de recursos, consulte [a visão geral do Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).
   * **Localização**: Selecione a região geográfica para o cofre. O cofre deve estar na mesma região que a Máquina Virtual que funciona SAP HANA. Usamos **o Leste DOS 2.**

5. Selecione **Review + Criar**.

   ![Selecione & criar](./media/tutorial-backup-sap-hana-db/review-create.png)

O cofre dos serviços de recuperação está agora criado.

## <a name="discover-the-databases"></a>Descubra as bases de dados

1. No cofre, em **"Getting Started",** clique **em Backup**. Em Onde está a correr **SAP HANA in Azure VM**a sua carga **de trabalho?**
2. Clique **em Iniciar A descoberta**. Isto inicia a descoberta de VMs Linux desprotegidos na região do cofre. Verá o VM Azure que quer proteger.
3. Em **Select Virtual Machines**, clique no link para descarregar o script que fornece permissões para o serviço de Backup Azure aceder aos VMs SAP HANA para a descoberta da base de dados.
4. Execute o script no VM que hospeda a base de dados SAP HANA que pretende fazer.
5. Depois de executar o script no VM, em **Select Virtual Machines,** selecione o VM. Em seguida, clique em **Descobrir DBs**.
6. A Azure Backup descobre todas as bases de dados SAP HANA na VM. Durante a descoberta, a Azure Backup regista o VM com o cofre e instala uma extensão no VM. Nenhum agente está instalado na base de dados.

   ![Descubra as bases de dados](./media/tutorial-backup-sap-hana-db/database-discovery.png)

## <a name="configure-backup"></a>Configurar a cópia de segurança

Agora que as bases de dados que queremos fazer são descobertas, vamos ativar a cópia de segurança.

1. Clique **em Configurar Backup**.

   ![Configurar a cópia de segurança](./media/tutorial-backup-sap-hana-db/configure-backup.png)

2. Em **Selecionar itens para fazer o back up,** selecione uma ou mais bases de dados que pretende proteger e, em seguida, clique **em OK**.

   ![Selecione itens para fazer back-up](./media/tutorial-backup-sap-hana-db/select-items-to-backup.png)

3. Na **política de backup > Escolha a política de backup,** crie uma nova política de backup para a(s) base de dados, de acordo com as instruções na secção seguinte.

   ![Escolha a política de backup](./media/tutorial-backup-sap-hana-db/backup-policy.png)

4. Depois de criar a política, no **menu Backup,** clique em **Ativar a cópia de segurança**.

   ![Clique Em Ativar a cópia de segurança](./media/tutorial-backup-sap-hana-db/enable-backup.png)

5. Acompanhe o progresso da configuração de backup na área de **Notificações** do portal.

## <a name="creating-a-backup-policy"></a>Criar uma política de backup

Uma política de backup define quando os backups são levados, e quanto tempo são retidos.

* Uma política é criada ao nível do cofre.
* Vários cofres podem usar a mesma política de reserva, mas tens de aplicar a política de reserva em cada cofre.

Especificar as definições de política da seguinte forma:

1. Em **nome da Política,** insira um nome para a nova política. Neste caso, insira **SAPHANA**.

   ![Insira o nome para nova política](./media/tutorial-backup-sap-hana-db/new-policy.png)

2. Na **política de cópia de segurança completa,** selecione uma frequência de **backup**. Pode escolher **Diariamente** ou **Semanalmente.** Para este tutorial, escolhemos o **Backup Diário.**

   ![Selecione uma frequência de backup](./media/tutorial-backup-sap-hana-db/backup-frequency.png)

3. No **Alcance de Retenção,** configurar as definições de retenção para a cópia de segurança completa.
   * Por predefinição, todas as opções são selecionadas. Limpe os limites de alcance de retenção que não quer usar e desemote os que faz.
   * O período mínimo de retenção para qualquer tipo de backup (completo/diferencial/log) é de sete dias.
   * Os pontos de recuperação são marcados para retenção com base no seu alcance de retenção. Por exemplo, se selecionar uma cópia de segurança diária completa, apenas uma cópia de segurança completa é ativada todos os dias.
   * A cópia de segurança para um dia específico é marcada e mantida com base na gama e configuração semanais de retenção.
   * As gamas de retenção mensais e anual comportam-se de forma semelhante.
4. No menu **de política de cópia de segurança completa,** clique em **OK** para aceitar as definições.
5. Em seguida, selecione **Backup diferencial** para adicionar uma política diferencial.
6. Na **política de backup diferencial,** selecione **Ativar** para abrir os controlos de frequência e retenção. Permitimos um backup diferencial todos os **domingos** às **2:00**da manhã, que é mantido por **30 dias**.

   ![Política de backup diferencial](./media/tutorial-backup-sap-hana-db/differential-backup-policy.png)

   >[!NOTE]
   >Os backups incrementais não são suportados atualmente.
   >

7. Clique **em OK** para guardar a política e volte ao menu de política de **backup** principal.
8. Selecione **'Registar'** para adicionar uma política de backup de registo de transações,
   * **A Cópia de Segurança do Registo** é por definição padrão para **Ativar**. Isto não pode ser desativado, uma vez que o SAP HANA gere todas as cópias de segurança de registo.
   * Definimos **2 horas** como horário de reserva e **15 dias** de retenção.

    ![Política de backup de registo](./media/tutorial-backup-sap-hana-db/log-backup-policy.png)

   >[!NOTE]
   > As cópias de segurança de registo só começam a fluir depois de concluída uma cópia de segurança completa bem sucedida.
   >

9. Clique **em OK** para guardar a política e volte ao menu de política de **backup** principal.
10. Depois de terminar de definir a política de backup, clique em **OK**.

Já configura com sucesso cópias de segurança para a sua base de dados SAP HANA.

## <a name="next-steps"></a>Passos Seguintes

* Saiba como executar backups a [pedido nas bases de dados SAP HANA em execução em VMs Azure](backup-azure-sap-hana-database.md#run-an-on-demand-backup)
* Saiba como restaurar as [bases de dados SAP HANA em execução em VMs Azure](sap-hana-db-restore.md)
* Saiba como [gerir as bases de dados SAP HANA que são apoiadas através do Azure Backup](sap-hana-db-manage.md)
* Saiba como [resolver problemas comuns ao fazer backup das bases de dados SAP HANA](backup-azure-sap-hana-database-troubleshoot.md)
