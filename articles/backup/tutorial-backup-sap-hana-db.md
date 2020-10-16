---
title: Tutorial - Apoiar bases de dados SAP HANA em VMs Azure
description: Neste tutorial, aprenda a apoiar as bases de dados SAP HANA em execução na Azure VM até um cofre dos Serviços de Recuperação de Backup Azure.
ms.topic: tutorial
ms.date: 02/24/2020
ms.openlocfilehash: 0e0f6ff89f59b862ea15148124f44abc3ed196bf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91254352"
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
>A partir de 1 de agosto de 2020, o backup SAP HANA para a RHEL (7.4, 7.6, 7,7 & 8.1) está geralmente disponível.

## <a name="prerequisites"></a>Pré-requisitos

Certifique-se de que faz o seguinte antes de configurar backups:

* Identifique ou crie um [cofre dos Serviços de Recuperação](backup-sql-server-database-azure-vms.md#create-a-recovery-services-vault) na mesma região e subscrição que o VM que executa o SAP HANA.
* Permitir a conectividade do VM para a internet, para que possa chegar ao Azure, conforme descrito no procedimento de conectividade de [rede configurado](#set-up-network-connectivity) abaixo.
* Certifique-se de que o comprimento combinado do nome VM do servidor SAP HANA e o nome do Grupo de Recursos não excedem 84 caracteres para O Gestor de Recursos Azure (ARM_ VMs (e 77 caracteres para VMs clássicos). Esta limitação é porque alguns caracteres são reservados pelo serviço.
* Deve existir uma chave na **hdbuserstore** que presivam os seguintes critérios:
  * Deve estar presente na **hdbuserstore**padrão. O padrão é a conta sob a `<sid>adm` qual o SAP HANA está instalado.
  * Para o MDC, a chave deve apontar para a porta SQL de **NAMEERVER**. No caso do SDC, deve apontar para a porta SQL do **INDEXSERVER**
  * Deve ter credenciais para adicionar e apagar utilizadores
  * Note que esta chave pode ser eliminada após executar o script pré-registo com sucesso
* Executar o script de configuração de backup SAP HANA (script pré-registo) na máquina virtual onde o HANA está instalado, como utilizador principal. [Este guião](https://aka.ms/scriptforpermsonhana) prepara o sistema HANA para cópias de segurança. Consulte a secção [O que o script de pré-registo faz](#what-the-pre-registration-script-does) para saber mais sobre o script pré-registo.
* Se a sua configuração HANA utilizar pontos de terminação privados, execute o [script de pré-registo](https://aka.ms/scriptforpermsonhana) com o parâmetro de verificação da rede *de sn* ou *skip-- skip-network.*

>[!NOTE]
>O script de pré-registo instala o **compat-unixODBC234** para cargas de trabalho SAP HANA em execução em RHEL (7.4, 7.6 e 7.7) e **unixODBC** para RHEL 8.1. [Este pacote está localizado no RHEL para SAP HANA (para RHEL 7 Server) Update Services for SAP Solutions (RPMs) repo](https://access.redhat.com/solutions/5094721).  Para uma imagem Azure Marketplace RHEL, o repo seria **rhui-rhel-sap-hana-for-rhel-7-server-rhui-e4s-rpms**.

## <a name="set-up-network-connectivity"></a>Configurar conectividade de rede

Para todas as operações, uma base de dados SAP HANA em execução num Azure VM requer conectividade ao serviço de Backup Azure, Azure Storage e Azure Ative Directory. Isto pode ser conseguido utilizando pontos finais privados ou permitindo o acesso aos endereços IP públicos necessários ou FQDNs. Não permitir a conectividade adequada aos serviços Azure necessários pode levar a falhas em operações como a descoberta de bases de dados, configurar backup, realizar backups e restaurar dados.

A tabela a seguir enumera as várias alternativas que pode utilizar para estabelecer conectividade:

| **Opção**                        | **Vantagens**                                               | **Desvantagens**                                            |
| --------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| Pontos finais privados                 | Permitir cópias de segurança sobre iPs privados dentro da rede virtual  <br><br>   Fornecer controlo granular no lado da rede e do cofre | Incorre [nos custos](https://azure.microsoft.com/pricing/details/private-link/) padrão do ponto final privado |
| Etiquetas de serviço NSG                  | Mais fácil de gerir à medida que as mudanças de alcance são automaticamente fundidas   <br><br>   Sem custos adicionais | Pode ser usado apenas com NSGs  <br><br>    Fornece acesso a todo o serviço |
| Tags FQDN de Firewall Azure Firewall          | Mais fácil de gerir uma vez que os FQDNs necessários são geridos automaticamente | Pode ser usado apenas com Azure Firewall                         |
| Permitir o acesso ao serviço FQDNs/IPs | Sem custos adicionais   <br><br>  Funciona com todos os aparelhos de segurança da rede e firewalls | Um conjunto alargado de IPs ou FQDNs pode ser obrigado a ser acedido   |
| Utilizar um proxy HTTP                 | Ponto único de acesso à Internet aos VMs                       | Custos adicionais para executar um VM com o software proxy         |

Mais detalhes sobre a utilização destas opções são partilhados abaixo:

### <a name="private-endpoints"></a>Pontos finais privados

Os pontos finais privados permitem-lhe ligar-se de forma segura a partir de servidores dentro de uma rede virtual ao cofre dos Serviços de Recuperação. O ponto final privado utiliza um IP a partir do espaço de endereço VNET para o seu cofre. O tráfego de rede entre os seus recursos dentro da rede virtual e o cofre viaja pela sua rede virtual e uma ligação privada na rede de espinha dorsal da Microsoft. Isto elimina a exposição da internet pública. Leia mais sobre os pontos finais privados para o Azure Backup [aqui](./private-endpoints.md).

### <a name="nsg-tags"></a>Etiquetas NSG

Se utilizar grupos de segurança de rede (NSG), utilize a etiqueta de serviço *AzureBackup* para permitir o acesso de saída ao Azure Backup. Além da etiqueta Azure Backup, também precisa de permitir a conectividade para a autenticação e transferência de dados, criando [regras de NSG semelhantes](../virtual-network/security-overview.md#service-tags) para Azure*AD (AzureActiveDirectory)* e Azure Storage *(Armazenamento).* Os seguintes passos descrevem o processo para criar uma regra para a etiqueta de backup Azure:

1. Em **Todos os Serviços,** vá aos **grupos de segurança da Rede** e selecione o grupo de segurança da rede.

1. Selecione **regras de segurança de saída** em **Definições**.

1. Selecione **Adicionar**. Introduza todos os detalhes necessários para a criação de uma nova regra, conforme descrito nas [definições de regras de segurança](../virtual-network/manage-network-security-group.md#security-rule-settings). Certifique-se de que a opção **Destino** está definida para tag de serviço *de serviço* e de **destino** está definida para *AzureBackup*.

1. **Selecione Adicionar** para salvar a regra de segurança de saída recém-criada.

Pode igualmente criar [regras de segurança de saída NSG](https://docs.microsoft.com/azure/virtual-network/network-security-groups-overview#service-tags) para Azure Storage e Azure AD. Para obter mais informações sobre etiquetas de serviço, consulte [este artigo.](../virtual-network/service-tags-overview.md)

### <a name="azure-firewall-tags"></a>Tags Azure Firewall

Se estiver a utilizar o Azure Firewall, crie uma regra de aplicação utilizando a [etiqueta FQDN Azure Firewall AzureBackup .](../firewall/fqdn-tags.md) *AzureBackup* Isto permite que todo o acesso de saída ao Azure Backup.

### <a name="allow-access-to-service-ip-ranges"></a>Permitir o acesso às gamas IP de serviço

Se optar por permitir iPs de serviço de acesso, consulte os intervalos IP no ficheiro JSON disponível [aqui](https://www.microsoft.com/download/confirmation.aspx?id=56519). Terá de permitir o acesso aos IPs correspondentes ao Azure Backup, Azure Storage e Azure Ative Directory.

### <a name="allow-access-to-service-fqdns"></a>Permitir o acesso ao serviço FQDNs

Também pode utilizar os seguintes FQDNs para permitir o acesso aos serviços necessários a partir dos seus servidores:

| Serviço    | Nomes de domínio a serem acedidos                             |
| -------------- | ------------------------------------------------------------ |
| Reserva Azure  | `*.backup.windowsazure.com`                             |
| Armazenamento Azure | `*.blob.core.windows.net` <br><br> `*.queue.core.windows.net` |
| Azure AD      | Permitir o acesso às FQDNs nos termos das secções 56 e 59 de acordo com [este artigo](/office365/enterprise/urls-and-ip-address-ranges#microsoft-365-common-and-office-online) |

### <a name="use-an-http-proxy-server-to-route-traffic"></a>Use um servidor de procuração HTTP para encaminhar o tráfego

Quando faz backup de uma base de dados SAP HANA em execução num Azure VM, a extensão de backup no VM utiliza as APIs HTTPS para enviar comandos de gestão para Azure Backup e dados para Azure Storage. A extensão de backup também utiliza Azure AD para autenticação. Encaminhe o tráfego da extensão da cópia de segurança destes três serviços através do proxy HTTP. Utilize a lista de IPs e FQDNs acima mencionadas para permitir o acesso aos serviços necessários. Os servidores de procuração autenticados não são suportados.

## <a name="what-the-pre-registration-script-does"></a>O que o script pré-registo faz

Executar o script de pré-registo executa as seguintes funções:

* Com base na sua distribuição Linux, o script instala ou atualiza os pacotes necessários pelo agente Azure Backup.
* Realiza verificações de conectividade de rede de saída com servidores Azure Backup e serviços dependentes como O Azure Ative Directory e Azure Storage.
* Entra no seu sistema HANA utilizando a chave de utilizador listada como parte dos [pré-requisitos](#prerequisites). A chave do utilizador é utilizada para criar um utilizador de cópia de segurança (AZUREWLBACKUPHANAUSER) no sistema HANA e **a chave do utilizador pode ser eliminada após o script pré-registo ser executado com sucesso**.
* A AZUREWLUPHANAUSER é atribuída a estas funções e permissões necessárias:
  * BASE DE DADOS ADMIN (no caso do MDC) e BACKUP ADMIN (no caso do SDC): criar novas bases de dados durante a restauração.
  * CATÁLOGO LEIA: para ler o catálogo de backup.
  * SAP_INTERNAL_HANA_SUPPORT: aceder a algumas mesas privadas.
* O script adiciona uma chave para **a hdbuserstore** para AZUREWLBACKUPHANAUSER para o plug-in de backup HANA para lidar com todas as operações (consultas de base de dados, operações de restauro, configuração e backup de execução).

>[!NOTE]
> Pode passar explicitamente a chave de utilizador listada como parte dos [pré-requisitos](#prerequisites) como parâmetro para o script de pré-registo: `-sk SYSTEM_KEY_NAME, --system-key SYSTEM_KEY_NAME` <br><br>
>Para saber que outros parâmetros o script aceita, use o comando `bash msawb-plugin-config-com-sap-hana.sh --help`

Para confirmar a criação da chave, executar o comando HDBSQL na máquina HANA com credenciais SIDADM:

```hdbsql
hdbuserstore list
```

A saída do comando deve exibir a tecla {SID}{DBNAME}, com o utilizador apresentado como AZUREWLBACKUPHANAUSER.

>[!NOTE]
> Certifique-se de que tem um conjunto único de ficheiros SSFS em `/usr/sap/{SID}/home/.hdb/` . Deve haver apenas uma pasta neste caminho.

## <a name="create-a-recovery-services-vault"></a>Criar um cofre dos Serviços de Recuperação 

Um cofre dos Serviços de Recuperação é uma entidade que armazena os backups e pontos de recuperação criados ao longo do tempo. O cofre dos Serviços de Recuperação também contém as políticas de backup que estão associadas às máquinas virtuais protegidas.

Para criar um cofre dos Serviços de Recuperação:

1. Inicie sessão na sua subscrição no [portal do Azure](https://portal.azure.com/).

2. No menu à esquerda, selecione **Todos os serviços**

   ![Selecionar Todos os serviços](./media/tutorial-backup-sap-hana-db/all-services.png)

3. Na caixa de diálogo **Todos os serviços**, introduza **Serviços de Recuperação**. A lista de recursos é filtrada de acordo com a sua entrada. Na lista de recursos, selecione **Cofres dos Serviços de Recuperação**.

   ![Selecione cofres dos Serviços de Recuperação](./media/tutorial-backup-sap-hana-db/recovery-services-vaults.png)

4. No painel de cofres **dos Serviços de Recuperação,** selecione **Add**.

   ![Adicionar cofre de serviços de recuperação](./media/tutorial-backup-sap-hana-db/add-vault.png)

   A caixa de diálogo **Cofre dos Serviços de Recuperação** abre-se. Fornecer valores para o **Nome, Subscrição, Grupo de Recursos** e **Localização**

   ![Criar cofre dos Serviços de Recuperação](./media/tutorial-backup-sap-hana-db/create-vault.png)

   * **Nome**: O nome é usado para identificar o cofre dos Serviços de Recuperação e deve ser exclusivo da assinatura Azure. Especifique um nome que tenha pelo menos dois, mas não mais de 50 caracteres. O nome tem de começar com uma letra e ser composto apenas por letras, números e hífenes. Para este tutorial, usamos o nome **SAPHanaVault.**
   * **Subscrição**: Escolha a subscrição a utilizar. Se for membro de apenas uma subscrição, vai ver esse nome. Se não tiver a certeza de que subscrição utilizar, utilize a subscrição predefinida (sugerida). Terá várias escolhas apenas se a sua conta escolar ou profissional estiver associada a mais do que uma subscrição do Azure. Aqui, usamos a assinatura de assinatura de laboratório de **solução SAP HANA.**
   * **Grupo de recursos**: Utilize um grupo de recursos existente ou crie um novo. Aqui, usamos **SAPHANADemo.**<br>
   Para ver a lista de grupos de recursos disponíveis na sua subscrição, selecione **Use existente**e, em seguida, selecione um recurso da caixa de listas drop-down. Para criar um novo grupo de recursos, selecione **Criar novo** e introduza o nome. Para obter informações completas sobre grupos de recursos, consulte [a visão geral do Azure Resource Manager](../azure-resource-manager/management/overview.md).
   * **Localização**: Selecione a região geográfica do cofre. O cofre deve estar na mesma região que a Máquina Virtual que funciona SAP HANA. Usamos o **East US 2.**

5. Selecione **Review + Criar**.

   ![Selecione & criar](./media/tutorial-backup-sap-hana-db/review-create.png)

O cofre dos Serviços de Recuperação está agora criado.

## <a name="discover-the-databases"></a>Descubra as bases de dados

1. No cofre, em **"Getting Started",** selecione **Backup**. Em Onde está a correr **SAP HANA in Azure VM**a sua carga **de trabalho?**
2. Selecione **Iniciar a Descoberta**. Isto inicia a descoberta de VMs Linux desprotegidos na região do cofre. Verá o VM Azure que quer proteger.
3. Em **Select Virtual Machines**, selecione o link para descarregar o script que fornece permissões para o serviço de Backup Azure para aceder aos VMs SAP HANA para a descoberta da base de dados.
4. Execute o script no VM que hospeda a base de dados SAP HANA que pretende fazer.
5. Depois de executar o script no VM, em **Select Virtual Machines,** selecione o VM. Em seguida, **selecione Discover DBs**.
6. A Azure Backup descobre todas as bases de dados SAP HANA na VM. Durante a descoberta, a Azure Backup regista o VM com o cofre e instala uma extensão no VM. Nenhum agente está instalado na base de dados.

   ![Descubra as bases de dados](./media/tutorial-backup-sap-hana-db/database-discovery.png)

## <a name="configure-backup"></a>Configurar a cópia de segurança

Agora que as bases de dados que queremos fazer são descobertas, vamos ativar a cópia de segurança.

1. Selecione **Configurar a cópia de segurança**.

   ![Configurar a cópia de segurança](./media/tutorial-backup-sap-hana-db/configure-backup.png)

2. Em **Selecione itens para fazer o back up,** selecione uma ou mais bases de dados que pretende proteger e, em seguida, selecione **OK**.

   ![Selecione itens para fazer back-up](./media/tutorial-backup-sap-hana-db/select-items-to-backup.png)

3. Na **política de backup > Escolha a política de backup,** crie uma nova política de backup para a(s) base de dados, de acordo com as instruções na secção seguinte.

   ![Escolha a política de backup](./media/tutorial-backup-sap-hana-db/backup-policy.png)

4. Depois de criar a política, no **menu Backup**, selecione **Ative backup**.

   ![Selecione Ativar a cópia de segurança](./media/tutorial-backup-sap-hana-db/enable-backup.png)

5. Acompanhe o progresso da configuração de backup na área de **Notificações** do portal.

## <a name="creating-a-backup-policy"></a>Criar uma política de backup

Uma política de backup define quando os backups são levados, e quanto tempo são retidos.

* Uma política é criada ao nível do cofre.
* Vários cofres podem utilizar a mesma política de cópias de segurança, mas deve aplicar a política a cada cofre.

Especificar as definições de política da seguinte forma:

1. Em **Nome da política**, introduza um nome para a nova política. Neste caso, insira **SAPHANA**.

   ![Insira o nome para nova política](./media/tutorial-backup-sap-hana-db/new-policy.png)

2. Na **política de cópia de segurança completa,** selecione uma frequência de **backup**. Pode escolher **Diariamente** ou **Semanalmente.** Para este tutorial, escolhemos o **Backup Diário.**

   ![Selecione uma frequência de backup](./media/tutorial-backup-sap-hana-db/backup-frequency.png)

3. No **Alcance de Retenção,** configurar as definições de retenção para a cópia de segurança completa.
   * Por predefinição, todas as opções são selecionadas. Limpe os limites de alcance de retenção que não quer usar e desemote os que faz.
   * O período mínimo de retenção para qualquer tipo de backup (completo/diferencial/log) é de sete dias.
   * Os pontos de recuperação são marcados para retenção com base no respetivo período de retenção. Por exemplo, se selecionar uma cópia de segurança completa diária, vai ser acionada apenas uma cópia de segurança completa por dia.
   * A cópia de segurança para um dia específico é marcada e mantida com base na gama e configuração semanais de retenção.
   * Os períodos de retenção mensais e anuais comportam-se de forma semelhante.
4. No menu **Política de Cópia de segurança completa**, selecione **OK** para aceitar as definições.
5. Em seguida, selecione **Backup diferencial** para adicionar uma política diferencial.
6. Em **Política de Cópia de segurança diferencial**, selecione **Ativar** para abrir os controlos de frequência e retenção. Permitimos um backup diferencial todos os **domingos** às **2:00**da manhã, que é mantido por **30 dias.**

   ![Política de backup diferencial](./media/tutorial-backup-sap-hana-db/differential-backup-policy.png)

   >[!NOTE]
   >Os backups incrementais não são suportados atualmente.
   >

7. Selecione **OK** para guardar a política e voltar ao menu principal **Política de cópia de segurança**.
8. Selecione **'Registar'** para adicionar uma política de backup de registo de transações,
   * **A Cópia de Segurança do Registo** é por definição padrão para **Ativar**. Isto não pode ser desativado, uma vez que a SAP HANA gere todos os backups de registo.
   * Definimos **2 horas** como horário de reserva e **15 dias** de retenção.

    ![Política de backup de registo](./media/tutorial-backup-sap-hana-db/log-backup-policy.png)

   >[!NOTE]
   > As cópias de segurança de registo só começam a fluir depois de concluída uma cópia de segurança completa bem sucedida.
   >

9. Selecione **OK** para guardar a política e voltar ao menu principal **Política de cópia de segurança**.
10. Depois de terminar de definir a política de backup, selecione **OK**.

Agora, configura com sucesso, backup(s) para a sua base de dados SAP HANA.

## <a name="next-steps"></a>Passos Seguintes

* Saiba como executar backups a [pedido nas bases de dados SAP HANA em execução em VMs Azure](backup-azure-sap-hana-database.md#run-an-on-demand-backup)
* Saiba como restaurar as [bases de dados SAP HANA em execução em VMs Azure](sap-hana-db-restore.md)
* Saiba como [gerir as bases de dados SAP HANA que são apoiadas através do Azure Backup](sap-hana-db-manage.md)
* Saiba como [resolver problemas comuns ao fazer backup das bases de dados SAP HANA](backup-azure-sap-hana-database-troubleshoot.md)
