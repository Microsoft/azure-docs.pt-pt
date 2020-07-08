---
title: Apoiar VMs Azure em um cofre dos Serviços de Recuperação
description: Descreve como apoiar VMs Azure num cofre de Serviços de Recuperação usando o Backup Azure
ms.topic: conceptual
ms.date: 04/03/2019
ms.openlocfilehash: cba042efb08f121d4cd9fa5693edd69c827f1465
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "83727017"
---
# <a name="back-up-azure-vms-in-a-recovery-services-vault"></a>Apoiar VMs Azure em um cofre dos Serviços de Recuperação

Este artigo descreve como apoiar os VMs Azure num cofre dos Serviços de Recuperação, utilizando o serviço [de backup Azure.](backup-overview.md)

Neste artigo, vai aprender a:

> [!div class="checklist"]
>
> * Prepare os VMs Azure.
> * Criar um cofre.
> * Descubra VMs e configura uma política de backup.
> * Ativar a cópia de segurança para os VMs Azure.
> * Execute a cópia de segurança inicial.

> [!NOTE]
> Este artigo descreve como configurar um cofre e selecionar VMs para fazer back-up. É útil se quiser fazer o reforço de vários VM. Em alternativa, pode [fazer uma única posição de VM Azure](backup-azure-vms-first-look-arm.md) diretamente a partir das definições de VM.

## <a name="before-you-start"></a>Antes de começar

* [Reveja](backup-architecture.md#architecture-built-in-azure-vm-backup) a arquitetura de backup Azure VM.
* [Saiba mais](backup-azure-vms-introduction.md) Backup Azure VM, e a extensão de reserva.
* [Reveja a matriz de suporte](backup-support-matrix-iaas.md) antes de configurar a cópia de segurança.

Além disso, há algumas coisas que pode precisar fazer em algumas circunstâncias:

* **Instale o agente VM no VM**: Azure Backup apoia os VMs Azure instalando uma extensão ao agente Azure VM em funcionamento na máquina. Se o seu VM foi criado a partir de uma imagem de mercado Azure, o agente está instalado e em execução. Se criar um VM personalizado, ou migrar uma máquina no local, poderá ter de [instalar o agente manualmente](#install-the-vm-agent).

## <a name="create-a-vault"></a>Criar um cofre

 Um cofre armazena backups e pontos de recuperação criados ao longo do tempo, e armazena políticas de backup associadas a máquinas de backup. Criar um cofre da seguinte forma:

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Em busca, tipo **Serviços de Recuperação.** Em **Serviços,** clique nos **cofres dos Serviços de Recuperação.**

     ![Pesquisa de cofres dos Serviços de Recuperação](./media/backup-azure-arm-vms-prepare/browse-to-rs-vaults-updated.png)

3. No menu **de cofres dos Serviços de Recuperação,** clique **em +Adicionar**.

     ![Passo 2 da Criação do Cofre dos Serviços de Recuperação](./media/backup-azure-arm-vms-prepare/rs-vault-menu.png)

4. No **cofre dos Serviços de Recuperação,** escreva um nome amigável para identificar o cofre.
    * O nome tem de ser exclusivo para a subscrição do Azure.
    * Pode conter 2 a 50 caracteres.
    * Deve começar com uma letra, e só pode conter letras, números e hífens.
5. Selecione a subscrição Azure, grupo de recursos e região geográfica em que o cofre deve ser criado. Em seguida, clique em **Criar**.
    * Pode levar um tempo até que o cofre seja criado.
    * Monitorize as notificações de estado na área superior direita do portal.

Depois do cofre ser criado, aparece na lista de cofres dos Serviços de Recuperação. Se não vir o seu cofre, selecione **Refresh**.

![Lista de cofres de cópia de segurança](./media/backup-azure-arm-vms-prepare/rs-list-of-vaults.png)

>[!NOTE]
> O Azure Backup permite agora a personalização do nome do grupo de recursos criado pelo serviço Azure Backup. Para obter mais informações, consulte [o grupo de recursos de backup Azure para máquinas virtuais.](backup-during-vm-creation.md#azure-backup-resource-group-for-virtual-machines)

### <a name="modify-storage-replication"></a>Modificar a replicação do armazenamento

Por predefinição, os cofres utilizam [armazenamento geo-redundante (GRS)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs).

* Se o cofre for o seu mecanismo de reserva primário, recomendamos que utilize GRS.
* Você pode usar [armazenamento localmente redundante (LRS)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) para uma opção mais barata.

Modificar o tipo de replicação de armazenamento da seguinte forma:

1. No novo cofre, clique em **Propriedades** na secção **Definições.**
2. Em **Propriedades**, em **Configuração de Cópia de Segurança,** clique em **Atualização**.
3. Selecione o tipo de replicação de armazenamento e clique em **Guardar**.

      ![Definir a configuração de armazenamento do novo cofre](./media/backup-try-azure-backup-in-10-mins/full-blade.png)

> [!NOTE]
   > Não é possível modificar o tipo de replicação de armazenamento depois de o cofre ser configurado e contém itens de reserva. Se queres fazer isto, tens de recriar o cofre.

## <a name="apply-a-backup-policy"></a>Aplicar uma política de backup

Configure uma apólice de reserva para o cofre.

1. No cofre, clique em **+Backup** na secção **'Visão Geral'.**

   ![Botão de reserva](./media/backup-azure-arm-vms-prepare/backup-button.png)

2. Em **Backup Goal**Onde está a sua carga de trabalho a  >  **correr?** **Azure** Em **O que pretende fazer para fazer o back-up?** Selecione Máquina **Virtual**  >   **OK**. Isto regista a extensão VM no cofre.

   ![Backup e Backup Goal](./media/backup-azure-arm-vms-prepare/select-backup-goal-1.png)

3. Na **política de backup,** selecione a política que pretende associar ao cofre.
    * A política por defeito confirma o VM uma vez por dia. Os backups diários são mantidos por 30 dias. Os instantâneos instantâneos de recuperação são retidos por dois dias.
    * Se não quiser utilizar a política predefinitiva, **selecione Create New**e crie uma política personalizada como descrito no procedimento seguinte.

      ![Política de backup predefinido](./media/backup-azure-arm-vms-prepare/default-policy.png)

4. Em **Selecione máquinas virtuais**, selecione os VMs que pretende fazer com a política. Em seguida, clique em **OK**.

   * Os VMs selecionados são validados.
   * Só pode selecionar VMs na mesma região que o cofre.
   * Os VMs só podem ser apoiados num único cofre.

     ![Painel "Selecione máquinas virtuais"](./media/backup-azure-arm-vms-prepare/select-vms-to-backup.png)

    >[!NOTE]
    > Apenas VMs na mesma região e subscrição que a do cofre estarão disponíveis para configurar backup.

5. Em **Cópia de Segurança,** clique em **Ativar a cópia de segurança**. Isto implementa a política para o cofre e para os VMs, e instala a extensão de backup no agente VM em execução no Azure VM.

     ![Botão "Ativar backup"](./media/backup-azure-arm-vms-prepare/vm-validated-click-enable.png)

Depois de permitir a cópia de segurança:

* O serviço de cópia de segurança instala a extensão de reserva quer o VM esteja ou não em funcionamento.
* Uma cópia de segurança inicial será executada de acordo com o seu horário de reserva.
* Quando os backups correrem, note que:
  * Um VM que está a funcionar tem a maior hipótese de capturar um ponto de recuperação consistente com aplicações.
  * No entanto, mesmo que o VM esteja desligado, está apoiado. Tal VM é conhecido como um VM offline. Neste caso, o ponto de recuperação será consistente.
* A conectividade de saída explícita não é necessária para permitir a cópia de segurança dos VMs Azure.

### <a name="create-a-custom-policy"></a>Criar uma política personalizada

Se selecionou para criar uma nova política de backup, preencha as definições de política.

1. Em **nome da Política,** especifique um nome significativo.
2. No **horário de backup,** especifique quando devem ser feitas cópias de segurança. Pode fazer backups diários ou semanais para VMs Azure.
3. No **Instant Restore**, especifique quanto tempo pretende reter os instantâneos localmente para restaurar instantaneamente.
    * Quando restaura, os discos VM com suporte são copiados do armazenamento, através da rede até ao local de armazenamento de recuperação. Com a restauração instantânea, pode aproveitar as fotos armazenadas localmente tiradas durante um trabalho de reserva, sem esperar que os dados de backup sejam transferidos para o cofre.
    * Pode reter instantâneos para restauro instantâneo entre um a cinco dias. Dois dias é a definição padrão.
4. No **intervalo de retenção**, especifique quanto tempo pretende manter os seus pontos de backup diários ou semanais.
5. Na **Retenção do ponto de backup mensal**, especifique se pretende manter uma cópia de segurança mensal das suas cópias de segurança diárias ou semanais.
6. Clique **em OK** para salvar a política.

    ![Nova política de backup](./media/backup-azure-arm-vms-prepare/new-policy.png)

> [!NOTE]
   > O Azure Backup não suporta o ajuste automático do relógio para alterações de poupança de luz do dia para cópias de segurança Azure VM. À medida que ocorrem alterações de tempo, modifique as políticas de backup manualmente, conforme necessário.

## <a name="trigger-the-initial-backup"></a>Desencadeie a cópia de segurança inicial

A cópia de segurança inicial será executada de acordo com o horário, mas pode executá-la imediatamente da seguinte forma:

1. No menu do cofre, clique em **itens de cópia de segurança.**
2. Em **Itens de cópia de segurança,** clique na **Máquina Virtual Azure**.
3. Na lista **de Itens de Reserva,** clique nas elipses (...).
4. Clique **em Backup agora**.
5. Em **Backup Now,** utilize o controlo do calendário para selecionar o último dia em que o ponto de recuperação deve ser mantido. Em seguida, clique em **OK**.
6. Monitorize as notificações do portal. Pode monitorizar o progresso do trabalho no painel de segurança do cofre > **trabalhos de reserva**  >  **em curso**. Dependendo do tamanho da sua VM, a criação da cópia de segurança inicial poderá demorar algum tempo.

## <a name="verify-backup-job-status"></a>Verificar o estado do trabalho de backup

Os detalhes do trabalho de backup para cada cópia de segurança VM consistem em duas fases, a fase **Snapshot** seguida dos **dados de Transferência para a** fase do cofre.<br/>
A fase instantânea garante a disponibilidade de um ponto de recuperação armazenado juntamente com os discos para **Restauros Instantâneos** e estão disponíveis por um máximo de cinco dias, dependendo da retenção instantânea configurada pelo utilizador. Os dados de transferência para o cofre criam um ponto de recuperação no cofre para retenção a longo prazo. Os dados de transferência para o cofre só começam depois da fase instantânea estar concluída.

  ![Estado do trabalho de backup](./media/backup-azure-arm-vms-prepare/backup-job-status.png)

Existem **duas Sub Tarefas** em execução no backend, uma para o trabalho de backup frontal que pode ser verificada a partir da lâmina de detalhes de **trabalho de backup** como dado abaixo:

  ![Estado do trabalho de backup](./media/backup-azure-arm-vms-prepare/backup-job-phase.png)

Os **dados de transferência para a** fase do cofre podem demorar vários dias a ser concluídos dependendo do tamanho dos discos, churn por disco e vários outros fatores.

O estado do trabalho pode variar dependendo dos seguintes cenários:

**Instantâneo** | **Transferir dados para o cofre** | **Estatuto do Trabalho**
--- | --- | ---
Concluído | Em curso | Em curso
Concluído | Ignorado | Concluído
Concluído | Concluído | Concluído
Concluído | Falhou | Concluído com aviso
Falhou | Falhou | Falhou

Agora com esta capacidade, para o mesmo VM, duas cópias de segurança podem ser executadas em paralelo, mas em ambas as fases (instantâneo, dados de transferência para o cofre) apenas uma sub tarefa pode estar em execução. Assim, em cenários, um trabalho de backup em andamento resultou que o backup do dia seguinte para falhar será evitado com esta funcionalidade de dissociação. As cópias de segurança do dia seguinte podem ter a imagem completada enquanto **os dados de transferência para o cofre** saltaram se o trabalho de reserva de um dia anterior estiver em progresso.
O ponto de recuperação incremental criado no cofre irá capturar todo o churn do último ponto de recuperação criado no cofre. Não há impacto de custos no utilizador.

## <a name="optional-steps"></a>Passos opcionais

### <a name="install-the-vm-agent"></a>Instale o agente VM

O Azure Backup apoia os VMs Azure instalando uma extensão ao agente Azure VM em funcionamento na máquina. Se o seu VM foi criado a partir de uma imagem do Azure Marketplace, o agente está instalado e em execução. Se criar um VM personalizado, ou migrar uma máquina no local, poderá ter de instalar o agente manualmente, como resumido na tabela.

**VM** | **Detalhes**
--- | ---
**Windows** | 1. [Descarregue e instale](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409) o ficheiro MSI do agente.<br/><br/> 2. Instale com permissões de administração na máquina.<br/><br/> 3. Verifique a instalação. Em *C:\WindowsAzure\Pacotes* no VM, clique à direita **WaAppAgent.exe**  >  **Propriedades**. No separador **Detalhes,** a **versão do produto** deve ser 2.6.1198.718 ou superior.<br/><br/> Se estiver a atualizar o agente, certifique-se de que não estão a ser operações de backup e [reinstala o agente](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409).
**Linux** | Instale utilizando um RPM ou um pacote DEB do repositório de pacotes da sua distribuição. Este é o método preferido para instalar e atualizar o agente Azure Linux. Todos os [fornecedores de distribuição endossados](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) integram o pacote de agente Azure Linux nas suas imagens e repositórios. O agente está disponível no [GitHub,](https://github.com/Azure/WALinuxAgent)mas não recomendamos a instalação a partir daí.<br/><br/> Se estiver a atualizar o agente, certifique-se de que não estão a ser operações de backup e atualize os binários.

>[!NOTE]
> **O Azure Backup suporta agora a cópia de segurança seletiva do disco e restaura utilizando a solução de backup da Máquina Virtual Azure.**
>
>Hoje em dia, o Azure Backup suporta o backup de todos os discos (Sistema Operativo e dados) num VM em conjunto utilizando a solução de backup da Máquina Virtual. Com a funcionalidade de exclusão do disco, obtém-se uma opção de backup de um ou alguns dos muitos discos de dados num VM. Isto fornece uma solução eficiente e rentável para as suas necessidades de backup e restauro. Cada ponto de recuperação contém dados dos discos incluídos na operação de backup, o que permite ainda ter um subconjunto de discos restaurados a partir do ponto de recuperação dado durante a operação de restauro. Isto aplica-se para restaurar tanto do instantâneo como do cofre.
>
>**Para se inscrever para a pré-visualização, escreva-nos emAskAzureBackupTeam@microsoft.com**

## <a name="next-steps"></a>Próximos passos

* Resolver problemas com [agentes Azure VM](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md) ou [backup Azure VM](backup-azure-vms-troubleshoot.md).
* [Restaurar](backup-azure-arm-restore-vms.md) VMs Azure.
