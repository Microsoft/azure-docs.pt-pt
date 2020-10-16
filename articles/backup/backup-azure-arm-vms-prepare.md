---
title: Apoiar VMs Azure em um cofre dos Serviços de Recuperação
description: Descreve como apoiar VMs Azure num cofre de Serviços de Recuperação usando o Backup Azure
ms.topic: conceptual
ms.date: 07/28/2020
ms.openlocfilehash: 28cc995afc131e747314032c1363f73531e6915c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90986515"
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

* **Instale o agente VM no VM**: Azure Backup apoia os VMs Azure instalando uma extensão ao agente Azure VM em funcionamento na máquina. Se o seu VM foi criado a partir de uma imagem do Azure Marketplace, o agente está instalado e em execução. Se criar um VM personalizado, ou migrar uma máquina no local, poderá ter de [instalar o agente manualmente](#install-the-vm-agent).

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

### <a name="modify-storage-replication"></a>Modificar a replicação do armazenamento

Por predefinição, os cofres utilizam [armazenamento geo-redundante (GRS)](../storage/common/storage-redundancy.md#geo-redundant-storage).

* Se o cofre for o seu mecanismo de reserva primário, recomendamos que utilize GRS.
* Você pode usar [armazenamento localmente redundante (LRS)](../storage/common/storage-redundancy.md#locally-redundant-storage) para uma opção mais barata.
* [O armazenamento redundante de zona (ZRS)](../storage/common/storage-redundancy.md#zone-redundant-storage) replica os seus dados em [zonas de disponibilidade,](https://docs.microsoft.com/azure/availability-zones/az-overview#availability-zones)garantindo residência de dados e resiliência na mesma região.

Modificar o tipo de replicação de armazenamento da seguinte forma:

1. No novo cofre, selecione **Propriedades** na secção **Definições.**
2. Em **Propriedades**, em **Configuração de Cópia de Segurança**, selecione **Update**.
3. Selecione o tipo de replicação de armazenamento e **selecione Guardar**.

      ![Definir a configuração de armazenamento do novo cofre](./media/backup-azure-arm-vms-prepare/full-blade.png)

> [!NOTE]
   > Não é possível modificar o tipo de replicação de armazenamento depois de o cofre ser configurado e contém itens de reserva. Se queres fazer isto, tens de recriar o cofre.

## <a name="apply-a-backup-policy"></a>Aplicar uma política de backup

Configure uma apólice de reserva para o cofre.

1. No cofre, selecione **+Backup** na secção **'Visão Geral'.**

   ![Botão de reserva](./media/backup-azure-arm-vms-prepare/backup-button.png)

1. Em **Backup Goal**Onde está a sua carga de trabalho a  >  **correr?** **Azure** Em **O que pretende fazer para fazer o back-up?** Selecione Máquina **Virtual**  >   **OK**. Isto regista a extensão VM no cofre.

   ![Backup e Backup Goal](./media/backup-azure-arm-vms-prepare/select-backup-goal-1.png)

1. Na **política de backup,** selecione a política que pretende associar ao cofre.
    * A política por defeito confirma o VM uma vez por dia. Os backups diários são mantidos por 30 dias. Os instantâneos instantâneos de recuperação são retidos por dois dias.

      ![Política de backup predefinido](./media/backup-azure-arm-vms-prepare/default-policy.png)

    * Se não quiser utilizar a política predefinitiva, **selecione Create New**e crie uma política personalizada como descrito no procedimento seguinte.

1. Em **Máquinas Virtuais**, selecione **Adicionar**.

      ![Adicionar máquinas virtuais](./media/backup-azure-arm-vms-prepare/add-virtual-machines.png)

1. O **painel de máquinas virtuais Select** abre-se. Selecione os VMs que pretende fazer com a apólice. Em seguida, selecione **OK**.

   * Os VMs selecionados são validados.
   * Só pode selecionar VMs na mesma região que o cofre.
   * Os VMs só podem ser apoiados num único cofre.

     ![Painel "Selecione máquinas virtuais"](./media/backup-azure-arm-vms-prepare/select-vms-to-backup.png)

    >[!NOTE]
    > Todos os VMs da mesma região e subscrição que o cofre estão disponíveis para configurar backup. Ao configurar a cópia de segurança, pode navegar pelo nome da máquina virtual e pelo seu grupo de recursos, mesmo que não tenha a permissão necessária nesses VMs. Se o seu VM estiver em estado de apagação suave, então não será visível nesta lista. Se precisar de voltar a proteger o VM, terá de esperar que o período de eliminação suave expire ou desembolse o VM da lista de eliminação suave. Para obter mais informações, consulte [o apaga-se suavemente para o artigo VMs](soft-delete-virtual-machines.md#soft-delete-for-vms-using-azure-portal).

1. Em **Cópia de Segurança**, selecione Ative **backup**. Isto implementa a política para o cofre e para os VMs, e instala a extensão de backup no agente VM em execução no Azure VM.

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
    * Quando restaura, os discos VM com suporte são copiados do armazenamento, através da rede até ao local de armazenamento de recuperação. Com a restauração instantânea, pode aproveitar as imagens armazenadas localmente tiradas durante um trabalho de reserva, sem esperar que os dados de backup sejam transferidos para o cofre.
    * Pode reter instantâneos para restauro instantâneo entre um a cinco dias. Dois dias é a definição padrão.
4. No **intervalo de retenção**, especifique quanto tempo pretende manter os seus pontos de backup diários ou semanais.
5. Na **retenção do ponto de backup mensal** e da **retenção do ponto de backup anual,** especifique se pretende manter uma cópia de segurança mensal ou anual das suas cópias de segurança diárias ou semanais.
6. Selecione **OK** para salvar a apólice.

    ![Nova política de backup](./media/backup-azure-arm-vms-prepare/new-policy.png)

> [!NOTE]
   > O Azure Backup não suporta o ajuste automático do relógio para alterações de poupança de luz do dia para cópias de segurança Azure VM. À medida que ocorrem alterações de tempo, modifique as políticas de backup manualmente, conforme necessário.

## <a name="trigger-the-initial-backup"></a>Desencadeie a cópia de segurança inicial

A cópia de segurança inicial será executada de acordo com o horário, mas pode executá-la imediatamente da seguinte forma:

1. No menu do cofre, selecione **itens de reserva**.
2. Em **Itens de Cópia de Segurança**, selecione **Azure Virtual Machine**.
3. Na lista **de Itens de Cópia de Segurança,** selecione as elipses (...).
4. **Selecione Backup agora**.
5. Em **Backup Now,** utilize o controlo do calendário para selecionar o último dia em que o ponto de recuperação deve ser mantido. Em seguida, selecione **OK**.
6. Monitorize as notificações do portal. Pode monitorizar o progresso do trabalho no painel de segurança do cofre > **trabalhos de reserva**  >  **em curso**. Dependendo do tamanho da sua VM, a criação da cópia de segurança inicial poderá demorar algum tempo.

## <a name="verify-backup-job-status"></a>Verificar o estado do trabalho de backup

Os detalhes do trabalho de backup para cada cópia de segurança VM consistem em duas fases, a fase **Snapshot** seguida dos **dados de Transferência para a** fase do cofre.<br/>
A fase instantânea garante a disponibilidade de um ponto de recuperação armazenado juntamente com os discos para **Restauros Instantâneos** e estão disponíveis por um máximo de cinco dias, dependendo da retenção instantânea configurada pelo utilizador. Os dados de transferência para o cofre criam um ponto de recuperação no cofre para retenção a longo prazo. Os dados de transferência para o cofre só começam depois da fase instantânea estar concluída.

  ![Estado do trabalho de backup](./media/backup-azure-arm-vms-prepare/backup-job-status.png)

Existem **duas Sub Tarefas** em execução no backend, uma para o trabalho de backup frontal que pode ser verificado a partir do painel de detalhes do **trabalho de backup** como dado abaixo:

  ![Sub-tarefas de backup do estado do trabalho](./media/backup-azure-arm-vms-prepare/backup-job-phase.png)

Os **dados de transferência para a** fase do cofre podem demorar vários dias a ser concluídos dependendo do tamanho dos discos, churn por disco e vários outros fatores.

O estado do trabalho pode variar dependendo dos seguintes cenários:

**Instantâneo** | **Transferir dados para o cofre** | **Estatuto do Trabalho**
--- | --- | ---
Concluído | Em curso | Em curso
Concluído | Ignorado | Concluído
Concluído | Concluído | Concluído
Concluído | Com falhas | Concluído com aviso
Com falhas | Com falhas | Com falhas

Agora com esta capacidade, para o mesmo VM, duas cópias de segurança podem ser executadas em paralelo, mas em ambas as fases (instantâneo, dados de transferência para o cofre) apenas uma sub tarefa pode estar em execução. Assim, em cenários em que um trabalho de backup em andamento resultou no backup do dia seguinte para falhar, será evitado com esta funcionalidade de dissociação. Os backups dos dias seguintes podem ter o instantâneo concluído, enquanto **os dados de transferência para o cofre** são ignorados se o trabalho de backup de um dia anterior estiver em estado de progresso.
O ponto de recuperação incremental criado no cofre irá capturar todo o churn do mais recente ponto de recuperação criado no cofre. Não há impacto de custos no utilizador.

## <a name="optional-steps"></a>Passos opcionais

### <a name="install-the-vm-agent"></a>Instale o agente VM

O Azure Backup apoia os VMs Azure instalando uma extensão ao agente Azure VM em funcionamento na máquina. Se o seu VM foi criado a partir de uma imagem do Azure Marketplace, o agente está instalado e em execução. Se criar um VM personalizado, ou migrar uma máquina no local, poderá ter de instalar o agente manualmente, como resumido na tabela.

**VM** | **Detalhes**
--- | ---
**Windows** | 1. [Descarregue e instale](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409) o ficheiro MSI do agente.<br/><br/> 2. Instale com permissões de administração na máquina.<br/><br/> 3. Verifique a instalação. Em *C:\WindowsAzure\Pacotes* no VM, clique à direita **WaAppAgent.exe**  >  **Propriedades**. No separador **Detalhes,** a **versão do produto** deve ser 2.6.1198.718 ou superior.<br/><br/> Se estiver a atualizar o agente, certifique-se de que não estão a ser operações de backup e [reinstala o agente](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409).
**Linux** | Instale utilizando um RPM ou um pacote DEB do repositório de pacotes da sua distribuição. Este é o método preferido para instalar e atualizar o agente Azure Linux. Todos os [fornecedores de distribuição endossados](../virtual-machines/linux/endorsed-distros.md) integram o pacote de agente Azure Linux nas suas imagens e repositórios. O agente está disponível no [GitHub,](https://github.com/Azure/WALinuxAgent)mas não recomendamos a instalação a partir daí.<br/><br/> Se estiver a atualizar o agente, certifique-se de que não estão a ser operações de backup e atualize os binários.

## <a name="next-steps"></a>Passos seguintes

* Resolver problemas com [agentes Azure VM](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md) ou [backup Azure VM](backup-azure-vms-troubleshoot.md).
* [Restaurar](backup-azure-arm-restore-vms.md) VMs Azure.
