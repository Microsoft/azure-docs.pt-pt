---
title: Tutorial-fazer backup de bancos de dados SAP HANA em VMs do Azure
description: Neste tutorial, saiba como fazer backup de SAP HANA bancos de dados em execução na VM do Azure para um cofre dos serviços de recuperação de backup do Azure.
ms.topic: tutorial
ms.date: 11/12/2019
ms.openlocfilehash: a622370fca3144aeb6a5d7c071c227b3c21cf135
ms.sourcegitcommit: e50a39eb97a0b52ce35fd7b1cf16c7a9091d5a2a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74288763"
---
# <a name="tutorial-back-up-sap-hana-databases-in-an-azure-vm"></a>Tutorial: fazer backup de bancos de dados SAP HANA em uma VM do Azure

Este tutorial mostra como fazer backup de SAP HANA bancos de dados em execução em VMs do Azure para um cofre dos serviços de recuperação de backup do Azure. Neste artigo, você aprenderá a:

> [!div class="checklist"]
>
> * Criar e configurar um cofre
> * Descobrir bancos de dados
> * Configurar backups

[Aqui](sap-hana-backup-support-matrix.md#scenario-support) estão todos os cenários com suporte no momento.

## <a name="onboard-to-the-public-preview"></a>Integração à visualização pública

Integre à visualização pública da seguinte maneira:

* No portal, registre sua ID de assinatura para o provedor de serviço de serviços de recuperação [seguindo este artigo](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-register-provider-errors#solution-3---azure-portal).

* Para o PowerShell, execute este cmdlet. Ele deve ser concluído como "registrado".

    ```powershell
    Register-AzProviderFeature -FeatureName "HanaBackup" –ProviderNamespace Microsoft.RecoveryServices
    ```

## <a name="prerequisites"></a>Pré-requisitos

Certifique-se de fazer o seguinte antes de configurar backups:

1. Na VM que executa o banco de dados SAP HANA, instale e habilite pacotes de driver ODBC do pacote/mídia oficial do SLES usando zypper, da seguinte maneira:

```bash
sudo zypper update
sudo zypper install unixODBC
```

>[!NOTE]
> Se você não estiver atualizando os repositórios, verifique se a versão de unixODBC é a mínima 2.3.4. Para saber a versão do uniXODBC, execute `odbcinst -j` como raiz
>

2. Permita a conectividade da VM com a Internet, para que ela possa acessar o Azure, conforme descrito no [procedimento abaixo](#set-up-network-connectivity).

3. Execute o script de pré-registro na máquina virtual em que o HANA está instalado como um usuário raiz. [Esse script](https://aka.ms/scriptforpermsonhana) definirá as [permissões corretas](#setting-up-permissions).

## <a name="set-up-network-connectivity"></a>Configurar a conectividade de rede

Para todas as operações, a VM SAP HANA precisa de conectividade com os endereços IP públicos do Azure. As operações de VM (descoberta de banco de dados, configurar backups, agendar backups, restaurar pontos de recuperação e assim por diante) não funcionam sem conectividade. Estabeleça a conectividade permitindo o acesso aos intervalos de IP do datacenter do Azure:

* Você pode baixar os [intervalos de endereços IP](https://www.microsoft.com/download/details.aspx?id=41653) para data centers do Azure e, em seguida, permitir o acesso a esses endereços IP.
* Se você estiver usando NSGs (grupos de segurança de rede), poderá usar a [marca de serviço](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) AzureCloud para permitir todos os endereços IP públicos do Azure. Você pode usar o [cmdlet Set-AzureNetworkSecurityRule](https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azurenetworksecurityrule?view=azuresmps-4.0.0) para modificar as regras de NSG.
* A porta 443 deve ser adicionada à lista de permissões, pois o transporte é via HTTPS.

## <a name="setting-up-permissions"></a>Configurando permissões

O script de pré-registro executa as seguintes ações:

1. Cria AZUREWLBACKUPHANAUSER no sistema HANA e adiciona essas funções e permissões necessárias:
   * ADMIN. do banco de dados: para criar novos bancos durante a restauração.
   * LEITURA do catálogo: para ler o catálogo de backup.
   * SAP_INTERNAL_HANA_SUPPORT: para acessar algumas tabelas privadas.
2. Adiciona uma chave para Hdbuserstore para o plug-in do HANA para lidar com todas as operações (consultas de banco de dados, operações de restauração, configuração e execução de backup).

Para confirmar a criação da chave, execute o comando HDBSQL no computador HANA com as credenciais do SIDADM:

```hdbsql
hdbuserstore list
```

A saída do comando deve exibir a chave {SID} {DBNAME}, com o usuário mostrado como AZUREWLBACKUPHANAUSER.

>[!NOTE]
> Verifique se você tem um conjunto exclusivo de arquivos SSFS em/usr/sap/{SID}/home/.hdb/. Deve haver apenas uma pasta neste caminho.
>

## <a name="create-a-recovery-service-vault"></a>Criar um cofre do serviço de recuperação

Um cofre dos serviços de recuperação é uma entidade que armazena os backups e os pontos de recuperação criados ao longo do tempo. O cofre dos serviços de recuperação também contém as políticas de backup associadas às máquinas virtuais protegidas.

Para criar um cofre dos Serviços de Recuperação:

1. Entre em sua assinatura no [portal do Azure](https://portal.azure.com/).

2. No menu à esquerda, selecione **todos os serviços**

![Selecionar todos os serviços](./media/tutorial-backup-sap-hana-db/all-services.png)

3. Na caixa de diálogo **todos os serviços** , insira **serviços de recuperação**. A lista de recursos filtra de acordo com sua entrada. Na lista de recursos, selecione **cofres dos serviços de recuperação**.

![Selecionar cofres dos serviços de recuperação](./media/tutorial-backup-sap-hana-db/recovery-services-vaults.png)

4. No painel cofres **dos serviços de recuperação** , selecione **Adicionar**.

![Adicionar cofre dos serviços de recuperação](./media/tutorial-backup-sap-hana-db/add-vault.png)

A caixa de diálogo **cofre dos serviços de recuperação** é aberta. Forneça valores para o **nome, a assinatura, o grupo de recursos** e o **local**

![Criar cofre dos Serviços de Recuperação](./media/tutorial-backup-sap-hana-db/create-vault.png)

* **Nome**: o nome é usado para identificar o cofre dos serviços de recuperação e deve ser exclusivo para a assinatura do Azure. Especifique um nome que tenha pelo menos dois, mas não mais de 50 caracteres. O nome deve começar com uma letra e consistir apenas em letras, números e hifens. Para este tutorial, usamos o nome **SAPHanaVault**.
* **Assinatura**: escolha a assinatura a ser usada. Se você for membro de apenas uma assinatura, verá esse nome. Se você não tiver certeza de qual assinatura usar, use a assinatura padrão (sugerida). Há várias opções somente se sua conta corporativa ou de estudante estiver associada a mais de uma assinatura do Azure. Aqui, usamos a assinatura de **assinatura do laboratório de solução SAP Hana** .
* **Grupo de recursos**: Use um grupo de recursos existente ou crie um novo. Aqui, usamos **SAPHANADemo**.<br>
Para ver a lista de grupos de recursos disponíveis em sua assinatura, selecione **usar existente**e, em seguida, selecione um recurso na caixa de listagem suspensa. Para criar um novo grupo de recursos, selecione **criar novo** e insira o nome. Para obter informações completas sobre grupos de recursos, consulte [Azure Resource Manager visão geral](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).
* **Local**: selecione a região geográfica para o cofre. O cofre deve estar na mesma região que a máquina virtual em execução SAP HANA. Nós usamos **leste dos EUA 2**.

5. Selecione **revisão + criar**.

   ![Selecione revisar & criar](./media/tutorial-backup-sap-hana-db/review-create.png)

O cofre dos serviços de recuperação agora é criado.

## <a name="discover-the-databases"></a>Descobrir os bancos de dados

1. No cofre, em **introdução**, clique em **backup**. Em **onde sua carga de trabalho está em execução?** , selecione **SAP Hana na VM do Azure**.
2. Clique em **Iniciar descoberta**. Isso inicia a descoberta de VMs do Linux desprotegidas na região do cofre. Você verá a VM do Azure que deseja proteger.
3. Em **selecionar máquinas virtuais**, clique no link para baixar o script que fornece permissões para o serviço de backup do Azure acessar as VMs de SAP Hana para descoberta de banco de dados.
4. Execute o script na VM que hospeda SAP HANA banco (s) de dados que você deseja fazer backup.
5. Depois de executar o script na VM, em **selecionar máquinas virtuais**, selecione a VM. Em seguida, clique em **descobrir bancos**de os.
6. O backup do Azure descobre todos os bancos de dados SAP HANA na VM. Durante a descoberta, o backup do Azure registra a VM com o cofre e instala uma extensão na VM. Nenhum agente está instalado no banco de dados.

   ![Descobrir os bancos de dados](./media/tutorial-backup-sap-hana-db/database-discovery.png)

## <a name="configure-backup"></a>Configurar a cópia de segurança

Agora que os bancos de dados que desejamos fazer backup são descobertos, vamos habilitar o backup.

1. Clique em **Configurar backup**.

![Configurar a cópia de segurança](./media/tutorial-backup-sap-hana-db/configure-backup.png)

2. Em **selecionar itens para fazer backup**, selecione um ou mais bancos de dados que você deseja proteger e clique em **OK**.

![Selecionar itens para backup](./media/tutorial-backup-sap-hana-db/select-items-to-backup.png)

3. Em **política de backup > escolha política de backup**, crie uma nova política de backup para os bancos de dados, de acordo com as instruções na próxima seção.

![Escolher política de backup](./media/tutorial-backup-sap-hana-db/backup-policy.png)

4. Depois de criar a política, no **menu backup**, clique em **habilitar backup**.

   ![Clique em Habilitar backup](./media/tutorial-backup-sap-hana-db/enable-backup.png)

5. Acompanhe o progresso da configuração de backup na área de **notificações** do Portal.

## <a name="creating-a-backup-policy"></a>Criando uma política de backup

Uma política de backup define quando os backups são feitos e por quanto tempo eles são retidos.

* Uma política é criada no nível do cofre.
* Vários cofres podem usar a mesma política de backup, mas você deve aplicar a política de backup a cada cofre.

Especifique as configurações de política da seguinte maneira:

1. Em **nome da política**, insira um nome para a nova política. Nesse caso, digite **SAPHANA**.

![Inserir nome para nova política](./media/tutorial-backup-sap-hana-db/new-policy.png)

2. Em **política de backup completo**, selecione uma **frequência de backup**. Você pode escolher **diariamente** ou **semanalmente**. Para este tutorial, escolhemos o backup **diário** .

![Selecione uma frequência de backup](./media/tutorial-backup-sap-hana-db/backup-frequency.png)

3. Em **período de retenção**, defina as configurações de retenção para o backup completo.
   * Por padrão, todas as opções são selecionadas. Desmarque os limites de período de retenção que você não deseja usar e defina os que você faz.
   * O período de retenção mínimo para qualquer tipo de backup (completo/diferencial/log) é de sete dias.
   * Os pontos de recuperação são marcados para retenção com base em seu período de retenção. Por exemplo, se você selecionar um backup completo diário, apenas um backup completo será disparado todos os dias.
   * O backup de um dia específico é marcado e mantido com base no período de retenção semanal e na configuração.
   * Os intervalos de retenção mensais e anuais se comportam de maneira semelhante.
4. No menu **política de backup completo** , clique em **OK** para aceitar as configurações.
5. Em seguida, selecione **backup diferencial** para adicionar uma política diferencial.
6. Em **política de backup diferencial**, selecione **habilitar** para abrir os controles de frequência e retenção. Habilitamos um backup diferencial a cada **domingo** às **2:00**, que é retido por **30 dias**.

   ![Política de backup diferencial](./media/tutorial-backup-sap-hana-db/differential-backup-policy.png)

>[!NOTE]
>Atualmente, não há suporte para backups incrementais.
>

7. Clique em **OK** para salvar a política e retornar ao menu principal da **política de backup** .
8. Selecione **backup de log** para adicionar uma política de backup de log transacional,
   * O **backup de log** é definido por padrão como **habilitar**. Isso não pode ser desabilitado, pois SAP HANA gerencia todos os backups de log.
   * Definimos **2 horas** como o agendamento de backup e **15 dias** de período de retenção.

    ![Política de backup de log](./media/tutorial-backup-sap-hana-db/log-backup-policy.png)

>[!NOTE]
> Os backups de log só começam a fluir depois que um backup completo bem-sucedido é concluído.
>

9. Clique em **OK** para salvar a política e retornar ao menu principal da **política de backup** .
10. Depois de terminar de definir a política de backup, clique em **OK**.

Você configurou com êxito os backups para seus bancos de dados de SAP HANA.

## <a name="next-steps"></a>Próximos Passos

* Saiba como [executar backups sob demanda em SAP Hana bancos de dados em execução em VMs do Azure](backup-azure-sap-hana-database.md#run-an-on-demand-backup)
* Saiba como [restaurar SAP Hana bancos de dados em execução em VMs do Azure](sap-hana-db-restore.md)
* Saiba como [gerenciar SAP Hana bancos de dados cujo backup é feito usando o backup do Azure](sap-hana-db-manage.md)
* Saiba como [solucionar problemas comuns ao fazer backup de bancos de dados do SAP Hana](backup-azure-sap-hana-database-troubleshoot.md)
