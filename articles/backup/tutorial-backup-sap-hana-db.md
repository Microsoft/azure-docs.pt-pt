---
title: Tutorial - Back up Bases de dados SAP HANA em VMs Azure
description: Neste tutorial, aprenda a apoiar as bases de dados SAP HANA em execução em Azure VM para um cofre dos Serviços de Recuperação de Backup Azure.
ms.topic: tutorial
ms.date: 02/24/2020
ms.openlocfilehash: f64dd74ad0e038c5cad152e20ae2255de03114e3
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "79501447"
---
# <a name="tutorial-back-up-sap-hana-databases-in-an-azure-vm"></a>Tutorial: Back up Bases de dados SAP HANA em um VM Azure

Este tutorial mostra-lhe como fazer backup nas bases de dados da SAP HANA em execução em VMs Azure para um cofre dos Serviços de Recuperação de Backup Azure. Neste artigo aprenderá a:

> [!div class="checklist"]
>
> * Criar e configurar um cofre
> * Descubra bases de dados
> * Configurar cópias de segurança

[Aqui](sap-hana-backup-support-matrix.md#scenario-support) estão todos os cenários que apoiamos atualmente.

## <a name="prerequisites"></a>Pré-requisitos

Certifique-se de que faz o seguinte antes de configurar as cópias de segurança:

* Permita a conectividade do VM para a internet, para que possa chegar ao Azure, conforme descrito no procedimento de conectividade da [rede de configuração](#set-up-network-connectivity) abaixo.
* Deve existir uma chave na **loja hdbuser** que preencha os seguintes critérios:
  * Deve estar presente na loja de **hdbusers** padrão
  * Para o MDC, a chave deve apontar para a porta SQL do **NAMESERVER**. No caso da SDC deve apontar para a porta SQL do **INDEXSERVER**
  * Deve ter credenciais para adicionar e eliminar utilizadores
* Execute o script de configuração de cópia de segurança SAP HANA (script pré-registo) na máquina virtual onde hana está instalada, como utilizador raiz. [Este guião](https://aka.ms/scriptforpermsonhana) prepara o sistema HANA para cópia de segurança. Consulte o [what the pre-registration script does](#what-the-pre-registration-script-does) section to understand more about the pre-registration script.

## <a name="set-up-network-connectivity"></a>Configurar a conectividade da rede

Para todas as operações, o VM SAP HANA requer conectividade com endereços IP públicos do Azure. As operações vM (descoberta de bases de dados, configurar backups, agendar backups, restaurar pontos de recuperação, e assim por diante) falham sem conectividade com endereços IP públicos do Azure.

Estabelecer conectividade utilizando uma das seguintes opções:

### <a name="allow-the-azure-datacenter-ip-ranges"></a>Permitir as gamas IP do datacenter Azure

Esta opção permite que as [gamas IP](https://www.microsoft.com/download/details.aspx?id=41653) no ficheiro descarregado. Para aceder a um grupo de segurança de rede (NSG), utilize o cmdlet Set-AzureNetworkSecurityRule. Se a sua lista de destinatários seguros incluir apenas IPs específicos da região, também terá de atualizar a lista de destinatários seguros da etiqueta de serviço Azure Ative Directory (Azure AD) para permitir a autenticação.

### <a name="allow-access-using-nsg-tags"></a>Permitir o acesso utilizando tags NSG

Se utilizar o NSG para restringir a conectividade, deverá utilizar a etiqueta de serviço AzureBackup para permitir o acesso de saída ao Azure Backup. Além disso, deve também permitir a conectividade para a autenticação e transferência de dados utilizando [regras](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) para o Azure AD e O Armazenamento Azure. Isto pode ser feito a partir do portal Azure ou via PowerShell.

Para criar uma regra usando o portal:

  1. Em **Todos os Serviços,** vá a **grupos** de segurança da Rede e selecione o grupo de segurança da rede.
  2. Selecione regras de **segurança de saída** em **Definições**.
  3. Selecione **Adicionar**. Introduza todos os detalhes necessários para criar uma nova regra, conforme descrito nas [definições](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group#security-rule-settings)de regra de segurança . Certifique-se de que a opção **Destino** está definida para **etiqueta de serviço** e etiqueta de serviço **destino** está definida para **AzureBackup**.
  4. Clique em **Adicionar,** para salvar a regra de segurança de saída recém-criada.

Para criar uma regra usando o PowerShell:

 1. Adicione credenciais de conta Azure e atualize as nuvens nacionais<br/>
      `Add-AzureRmAccount`<br/>

 2. Selecione a subscrição NSG<br/>
      `Select-AzureRmSubscription "<Subscription Id>"`

 3. Selecione o NSG<br/>
    `$nsg = Get-AzureRmNetworkSecurityGroup -Name "<NSG name>" -ResourceGroupName "<NSG resource group name>"`

 4. Adicione a regra de saída para a etiqueta de serviço de backup Azure<br/>
    `Add-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg -Name "AzureBackupAllowOutbound" -Access Allow -Protocol * -Direction Outbound -Priority <priority> -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix "AzureBackup" -DestinationPortRange 443 -Description "Allow outbound traffic to Azure Backup service"`

 5. Adicione a regra de saída para a etiqueta de serviço de armazenamento<br/>
    `Add-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg -Name "StorageAllowOutbound" -Access Allow -Protocol * -Direction Outbound -Priority <priority> -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix "Storage" -DestinationPortRange 443 -Description "Allow outbound traffic to Azure Backup service"`

 6. Adicione a regra de saída para a etiqueta de serviço AzureActiveDirectory<br/>
    `Add-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg -Name "AzureActiveDirectoryAllowOutbound" -Access Allow -Protocol * -Direction Outbound -Priority <priority> -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix "AzureActiveDirectory" -DestinationPortRange 443 -Description "Allow outbound traffic to AzureActiveDirectory service"`

 7. Salve o NSG<br/>
    `Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg`

**Permitir o acesso utilizando tags De firewall Azure**. Se estiver a utilizar o Firewall Azure, crie uma regra de aplicação utilizando a [etiqueta AzureBackup FQDN](https://docs.microsoft.com/azure/firewall/fqdn-tags). Isto permite o acesso de saída ao Azure Backup.

**Implemente um servidor proxy HTTP para encaminhar**o tráfego . Quando faz o backup de uma base de dados SAP HANA num VM Azure, a extensão de backup do VM utiliza as APIs HTTPS para enviar comandos de gestão para Backup Azure e dados para o Armazenamento Azure. A extensão de reserva também utiliza AD Azure para autenticação. Encaminhe o tráfego de extensão de reserva para estes três serviços através do proxy HTTP. As extensões são o único componente configurado para o acesso à internet pública.

As opções de conectividade incluem as seguintes vantagens e desvantagens:

**Opção** | **Vantagens** | **Desvantagens**
--- | --- | ---
Permitir gamas IP | Sem custos adicionais | Complexo de gerir porque as gamas de endereços IP mudam ao longo do tempo <br/><br/> Dá acesso a todo o Azure, e não apenas ao Armazenamento Azure
Utilize etiquetas de serviço NSG | Mais fácil de gerir à medida que as alterações de gama são automaticamente fundidas <br/><br/> Sem custos adicionais <br/><br/> | Pode ser usado apenas com NSGs <br/><br/> Fornece acesso a todo o serviço
Utilize tags FQDN de firewall Azure | Mais fácil de gerir à medida que as FQDNs necessárias são geridas automaticamente | Pode ser usado apenas com Firewall Azure
Use um proxy HTTP | É permitido o controlo granular no proxy sobre os URLs de armazenamento <br/><br/> Ponto único de acesso à Internet aos VMs <br/><br/> Não sujeito a alterações de endereço IP do Azure | Custos adicionais para executar um VM com o software proxy

## <a name="what-the-pre-registration-script-does"></a>O que o guião pré-inscrição faz

Executar o script de pré-registo executa as seguintes funções:

* Instala ou atualiza quaisquer pacotes necessários exigidos pelo agente de backup Azure na sua distribuição.
* Realiza verificações de conectividade de rede de saída com servidores de backup Azure e serviços dependentes como o Azure Ative Directory e o Azure Storage.
* Inicia sessão no seu sistema HANA utilizando a chave do utilizador listada como parte dos [pré-requisitos](#prerequisites). A chave do utilizador é utilizada para criar um utilizador de backup (AZUREWLBACKUPHANAUSER) no sistema HANA e a chave do utilizador pode ser eliminada após o script de pré-registo ser executado com sucesso.
* É atribuído ao AZUREWLBACKUPHANAUSERESTAS Estas funções e permissões necessárias:
  * BASE DE DADOS ADMIN (no caso de MDC) e BACKUP ADMIN (no caso de SDC): criar novas bases de dados durante a restauração.
  * CATÁLOGO LEIA: para ler o catálogo de cópias de segurança.
  * SAP_INTERNAL_HANA_SUPPORT: aceder a algumas mesas privadas.
* O script adiciona uma chave para **hdbuserstore** para AZUREWLBACKUPHANAUSER PARA o plug-in de backup HANA para lidar com todas as operações (consultas de base de dados, restaurar operações, configurar e executar backup).

>[!NOTE]
> Pode passar explicitamente a chave do utilizador listada como parte dos [pré-requisitos](#prerequisites) como parâmetro para a script de pré-registo:`-sk SYSTEM_KEY_NAME, --system-key SYSTEM_KEY_NAME` <br><br>
>Para saber que outros parâmetros o script aceita, use o comando`bash msawb-plugin-config-com-sap-hana.sh --help`

Para confirmar a criação da chave, execute o comando HDBSQL na máquina HANA com credenciais SIDADM:

```hdbsql
hdbuserstore list
```

A saída de comando deve apresentar a tecla {SID}{DBNAME} com o utilizador apresentado como AZUREWLBACKUPHANAUSER.

>[!NOTE]
> Certifique-se de que tem um conjunto `/usr/sap/{SID}/home/.hdb/`único de ficheiros SSFS em baixo . . Só deve haver uma pasta neste caminho.

## <a name="create-a-recovery-service-vault"></a>Criar um cofre de serviço de recuperação

Um cofre de Serviços de Recuperação é uma entidade que armazena os backups e pontos de recuperação criados ao longo do tempo. O cofre dos Serviços de Recuperação também contém as políticas de backup que estão associadas às máquinas virtuais protegidas.

Para criar um cofre dos Serviços de Recuperação:

1. Inscreva-se na sua subscrição no [portal Azure.](https://portal.azure.com/)

2. No menu à esquerda, selecione **Todos os serviços**

   ![Selecione Todos os serviços](./media/tutorial-backup-sap-hana-db/all-services.png)

3. Na caixa de diálogo **de todos os serviços,** insira Serviços de **Recuperação.** A lista de filtros de recursos de acordo com a sua entrada. Na lista de recursos, selecione cofres de Serviços de **Recuperação.**

   ![Selecione cofres de Serviços de Recuperação](./media/tutorial-backup-sap-hana-db/recovery-services-vaults.png)

4. No painel de abóbadas dos Serviços de **Recuperação,** selecione **Adicionar**.

   ![Adicionar cofre de Serviços de Recuperação](./media/tutorial-backup-sap-hana-db/add-vault.png)

   A caixa de diálogo do **cofre dos Serviços de Recuperação** abre. Fornecer valores para o **Nome, Subscrição, Grupo de Recursos** e **Localização**

   ![Criar cofre dos Serviços de Recuperação](./media/tutorial-backup-sap-hana-db/create-vault.png)

   * **Nome**: O nome é utilizado para identificar o cofre dos serviços de recuperação e deve ser exclusivo da subscrição Azure. Especifique um nome que tenha pelo menos dois, mas não mais de 50 caracteres. O nome deve começar com uma letra e consistir apenas em letras, números e hífenes. Para este tutorial, usamos o nome **SAPHanaVault.**
   * **Subscrição**: Escolha a subscrição para utilizar. Se for membro de apenas uma subscrição, verá esse nome. Se não tiver a certeza de qual subscrição utilizar, utilize a subscrição predefinida (sugerida). Existem várias opções apenas se o seu trabalho ou conta escolar estiver associado a mais de uma subscrição Azure. Aqui, usamos a subscrição de subscrição de laboratório de **soluções SAP HANA.**
   * **Grupo de recursos**: Utilize um grupo de recursos existente ou crie um novo. Aqui, usamos **o SAPHANADemo.**<br>
   Para ver a lista de grupos de recursos disponíveis na sua subscrição, selecione **Use existente**, e, em seguida, selecione um recurso da caixa de lista saltada. Para criar um novo grupo de recursos, selecione **Criar novo** e insira o nome. Para obter informações completas sobre grupos de recursos, consulte a [visão geral do Gestor de Recursos do Azure.](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)
   * **Localização**: Selecione a região geográfica para o cofre. O cofre deve estar na mesma região que a Máquina Virtual que executa o SAP HANA. Usamos **east us 2**.

5. Selecione **Rever + Criar**.

   ![Selecione rever & Criar](./media/tutorial-backup-sap-hana-db/review-create.png)

O cofre dos serviços de recuperação está agora criado.

## <a name="discover-the-databases"></a>Descubra as bases de dados

1. No cofre, em **Getting Started,** clique em **Backup**. Em Onde está a funcionar **SAP HANA in Azure VM**a **sua carga de trabalho?**
2. Clique em **Iniciar Descoberta**. Isto inicia a descoberta de VMs linux desprotegidos na região do cofre. Verá o VM Azure que quer proteger.
3. Em **Select Virtual Machines,** clique no link para descarregar o script que fornece permissões para o serviço de backup Azure aceder aos VMs SAP HANA para descoberta de base de dados.
4. Execute o script na base de dados SAP HANA(s) que pretende fazer.
5. Depois de executar o script no VM, em **Select Virtual Machines,** selecione o VM. Em seguida, clique em **Descobrir DBs**.
6. O Azure Backup descobre todas as bases de dados da SAP HANA no VM. Durante a descoberta, o Azure Backup regista o VM com o cofre e instala uma extensão no VM. Nenhum agente está instalado na base de dados.

   ![Descubra as bases de dados](./media/tutorial-backup-sap-hana-db/database-discovery.png)

## <a name="configure-backup"></a>Configurar a cópia de segurança

Agora que as bases de dados que queremos apoiar são descobertas, vamos permitir reforços.

1. Clique em **Configurar cópia de segurança**.

   ![Configurar a cópia de segurança](./media/tutorial-backup-sap-hana-db/configure-backup.png)

2. Em **Selecione itens para fazer o 'back',** selecione uma ou mais bases de dados que pretende proteger e, em seguida, clique em **OK**.

   ![Selecione itens para fazer back-up](./media/tutorial-backup-sap-hana-db/select-items-to-backup.png)

3. Na Política de Backup > Escolha a política de **backup,** crie uma nova política de backup para a base de dados ou as bases de dados, de acordo com as instruções na secção seguinte.

   ![Escolha a política de backup](./media/tutorial-backup-sap-hana-db/backup-policy.png)

4. Depois de criar a política, no **menu 'Backup',** clique em **ativar a cópia de segurança**.

   ![Clique na cópia de segurança de ativar](./media/tutorial-backup-sap-hana-db/enable-backup.png)

5. Acompanhe o progresso da configuração de backup na área de **Notificações** do portal.

## <a name="creating-a-backup-policy"></a>Criar uma política de backup

Uma política de reserva define quando os backups são tomados, e quanto tempo são retidos.

* Uma apólice é criada ao nível do cofre.
* Vários cofres podem usar a mesma política de reserva, mas tens de aplicar a política de reserva a cada cofre.

Especifique as definições de política da seguinte forma:

1. Em **nome de Política,** insira um nome para a nova política. Neste caso, **insira a SAPHANA**.

   ![Insira o nome para nova política](./media/tutorial-backup-sap-hana-db/new-policy.png)

2. Na **política de backup completo,** selecione uma Frequência de **Backup**. Pode escolher **Diariamente** ou **Semanalmente.** Para este tutorial, escolhemos o **backup diário.**

   ![Selecione uma frequência de backup](./media/tutorial-backup-sap-hana-db/backup-frequency.png)

3. No **Intervalo de Retenção,** configure as definições de retenção para a cópia de segurança completa.
   * Por padrão, todas as opções são selecionadas. Limpe os limites de alcance de retenção que não queira usar e desloque os que faz.
   * O período mínimo de retenção para qualquer tipo de cópia de segurança (full/diferencial/registo) é de sete dias.
   * Os pontos de recuperação estão marcados para retenção com base na sua gama de retenção. Por exemplo, se selecionar uma cópia de segurança completa diária, apenas um backup completo é acionado todos os dias.
   * A cópia de segurança para um dia específico é marcada e mantida com base na gama e configuração semanais de retenção.
   * As gamas de retenção mensal e anual comportam-se de forma semelhante.
4. No menu de **política de backup completo,** clique em **OK** para aceitar as definições.
5. Em seguida, selecione **Backup Diferencial** para adicionar uma política diferencial.
6. Na **política de backup diferencial,** selecione **Ativar** para abrir os controlos de frequência e retenção. Permitimos um reforço diferencial todos os **domingos** às **2:00 da manhã,** que é retido por **30 dias.**

   ![Política de backup diferencial](./media/tutorial-backup-sap-hana-db/differential-backup-policy.png)

   >[!NOTE]
   >As cópias de segurança incrementais não são suportadas atualmente.
   >

7. Clique em **OK** para salvar a apólice e volte ao menu principal de política de **backup.**
8. Selecione **'Log Backup'** para adicionar uma política de backup de registo transacional,
   * **A cópia de segurança de log** é definida por predefinição para **ativar**. Isto não pode ser desativado uma vez que o SAP HANA gere todas as cópias de segurança de registo.
   * Definimos **2 horas** como o horário de backup e **15 dias** de período de retenção.

    ![Política de backup de log](./media/tutorial-backup-sap-hana-db/log-backup-policy.png)

   >[!NOTE]
   > As cópias de segurança de log só começam a fluir depois de uma cópia de segurança completa bem sucedida ser concluída.
   >

9. Clique em **OK** para salvar a apólice e volte ao menu principal de política de **backup.**
10. Depois de terminar de definir a política de backup, **clique**ok .

Já configuramos com sucesso cópias de dados para a sua base de dados SAP HANA.

## <a name="next-steps"></a>Passos Seguintes

* Saiba como executar backups a pedido nas bases de [dados SAP HANA em execução em VMs Azure](backup-azure-sap-hana-database.md#run-an-on-demand-backup)
* Saiba como restaurar as bases de [dados SAP HANA em execução em VMs Azure](sap-hana-db-restore.md)
* Saiba como gerir as bases de [dados SAP HANA que são apoiadas usando o Azure Backup](sap-hana-db-manage.md)
* Saiba como [resolver problemas comuns ao apoiar as bases de dados SAP HANA](backup-azure-sap-hana-database-troubleshoot.md)
