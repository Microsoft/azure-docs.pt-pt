---
title: Configure replicação para VMs Azure na recuperação do site azure
description: Saiba como configurar a replicação para outra região para VMs Azure, utilizando a Recuperação do Site.
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 04/29/2018
ms.openlocfilehash: 1c6b7cfbf193f02598052b6922efec17fb16ec83
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75973698"
---
# <a name="replicate-azure-vms-to-another-azure-region"></a>Replicar VMs Azure para outra região de Azure


Este artigo descreve como permitir a replicação de VMs Azure, de uma região de Azure para outra.

## <a name="before-you-start"></a>Antes de começar

Este artigo assume que se preparou para a implantação da recuperação do site, como descrito no tutorial de recuperação de [desastres azure a Azure.](azure-to-azure-tutorial-enable-replication.md)

Os pré-requisitos devem estar no lugar, e você deveria ter criado um cofre dos Serviços de Recuperação.


## <a name="enable-replication"></a>Ativar a replicação

Ative a replicação. Este procedimento pressupõe que a região principal de Azure é a Ásia Oriental, e a região secundária é o Sudeste Asiático.

1. No cofre, clique em **+Replicate**.
2. Note os seguintes campos:
   - **Fonte**: O ponto de origem dos VMs, que neste caso é **O Azure**.
   - **Localização da fonte**: A região de Azure de onde pretende proteger os seus VMs. Para esta ilustração, a localização de origem é "Leste asiático"
   - **Modelo de implantação**: Modelo de implantação Azure das máquinas de origem.
   - **Subscrição de origem**: A subscrição a que pertencem os Seus VMs de origem. Isto pode ser qualquer subscrição com o mesmo inquilino do Azure Active Directory onde existe o cofre de serviços de recuperação.
   - **Grupo de Recursos**: O grupo de recursos a que pertencem as suas máquinas virtuais de origem. Todos os VMs sob o grupo de recursos selecionados estão listados para proteção no próximo passo.

     ![Ativar a replicação](./media/site-recovery-replicate-azure-to-azure/enabledrwizard1.png)

3. Em **Máquinas Virtuais > Selecione máquinas virtuais,** clique e selecione cada VM que pretende replicar. Só pode selecionar máquinas para as quais a replicação pode ser ativada. Em seguida, clique em **OK**.
    ![Ativar replicação](./media/site-recovery-replicate-azure-to-azure/virtualmachine_selection.png)

4. Em **Definições,** pode configurar opcionalmente as definições do site-alvo:

   - **Localização do alvo**: A localização onde os dados da máquina virtual de origem serão replicados. Dependendo da localização das máquinas selecionadas, a Recuperação do Site fornecer-lhe-á a lista de regiões-alvo adequadas. Recomendamos que mantenha a localização do alvo da mesma forma que a localização do cofre dos Serviços de Recuperação.
   - **Subscrição de destino**: A subscrição de destino utilizada para recuperação após desastre. Por predefinição, a subscrição de destino será igual à subscrição de origem.
   - **Grupo de recursos-alvo**: O grupo de recursos ao qual todas as suas máquinas virtuais replicadas pertencem.
       - Por padrão, a Recuperação do Site cria um novo grupo de recursos na região alvo com um sufixo "asr" no nome.
       - Se o grupo de recursos criado pela Recovery do Site já existir, é reutilizado.
       - Pode personalizar as definições do grupo de recursos.
       - A localização do grupo de recursos-alvo pode ser qualquer região de Azure, exceto a região em que as VMs de origem estão hospedadas.
   - **Rede virtual alvo**: Por padrão, a Recuperação do Site cria uma nova rede virtual na região alvo com um sufixo "asr" no nome. Isto é mapeado para a sua rede de origem, e usado para qualquer proteção futura. [Saiba mais](site-recovery-network-mapping-azure-to-azure.md) sobre o mapeamento da rede.
   - Contas de armazenamento de alvos **(fonte VM não utiliza discos geridos)**: Por padrão, a Recuperação do Site cria uma nova conta de armazenamento de destino que imita a configuração de armazenamento vM de origem. No caso de já existir uma conta de armazenamento, é reutilizada.
   - **Discos geridos por réplicas (fonte VM utiliza discos geridos)**: A Recuperação do Site cria novos discos geridos por réplicas na região alvo para espelhar os discos geridos pela Fonte VM com o mesmo tipo de armazenamento (Standard ou premium) que o disco gerido pela VM da fonte.
   - **Contas de Depósito cache**: A Recuperação do Site necessita de uma conta de armazenamento extra chamada armazenamento de cache na região fonte. Todas as alterações que ocorrem na fonte de VMs são rastreadas e enviadas para a conta de armazenamento de cache antes de as replicarem para o local alvo. Esta conta de armazenamento deve ser standard.
   - **Conjuntos**de disponibilidade de destinos : Por padrão, a Recuperação do Site cria uma nova disponibilidade definida na região alvo com o sufixo "asr" no nome, para VMs que fazem parte de uma disponibilidade definida na região de origem. Se o conjunto de disponibilidade criado pela Recuperação do Site já existir, é reutilizado.
   - **Zonas**de disponibilidade alvo : Por predefinição, a Recuperação do Local atribui o mesmo número de zona que a região-alvo na região alvo se a região alvo apoiar as zonas de disponibilidade.

     Se a região alvo não suportar zonas de disponibilidade, os VMs-alvo são configurados como instâncias únicas por padrão. Se necessário, pode configurar esses VMs para fazer parte de conjuntos de disponibilidade na região alvo clicando em 'Personalizar'.

     >[!NOTE]
     >Não é possível alterar o tipo de disponibilidade - instância única, conjunto de disponibilidade ou zona de disponibilidade, depois de ativar a replicação. É necessário desativar e ativar a replicação para alterar o tipo de disponibilidade.
     >

   - **Política de Replicação**: Define as definições para o histórico de retenção de pontos de recuperação e frequência instantânea consistente da aplicação. Por padrão, a Recuperação do Site Azure cria uma nova política de replicação com definições padrão de '24 horas' para retenção de pontos de recuperação e "4 horas" para uma frequência instantânea consistente da aplicação.

     ![Ativar a replicação](./media/site-recovery-replicate-azure-to-azure/enabledrwizard3.PNG)

### <a name="enable-replication-for-added-disks"></a>Ativar a replicação para discos adicionados

Se adicionar discos a um VM Azure para o qual a replicação está ativada, ocorrem os seguintes:
-   A saúde de replicação para o VM mostra um aviso, e uma nota informa que lhe diz que um ou mais discos estão disponíveis para proteção.
-   Se ativar a proteção dos discos adicionados, o aviso desaparecerá após a replicação inicial do disco.
-   Se optar por não ativar a replicação do disco, pode selecionar para descartar o aviso.


    ![Novo disco adicionado](./media/azure-to-azure-how-to-enable-replication/newdisk.png)

Para permitir a replicação de um disco adicionado, faça o seguinte:

1.  No cofre > **Itens Replicados,** clique no VM ao qual adicionou o disco.
2.  Clique em **Discos**, e, em seguida, selecione o disco de dados para o qual pretende ativar a replicação (estes discos têm um estado **não protegido).**
3.  Em **Detalhes do disco,** clique em **ativar a replicação**.

    ![Ativar a replicação para o disco adicionado](./media/azure-to-azure-how-to-enable-replication/enabled-added.png)

Após o funcionar do trabalho de replicação ativa e os acabamentos iniciais da replicação, o aviso de saúde de replicação para a questão do disco é removido.



## <a name="customize-target-resources"></a>Personalizar recursos-alvo

Pode modificar as definições de alvo predefinidas utilizadas pela Recuperação do Site.

1. Clique em **Personalizar:** ao lado da subscrição 'Target' para modificar a subscrição do alvo predefinido. Selecione a subscrição da lista de todas as subscrições disponíveis no mesmo inquilino do Azure Ative Directory (AAD).

2. Clique em **Personalizar:** para modificar as definições predefinidas:
    - No **grupo de recursos Target,** selecione o grupo de recursos da lista de todos os grupos de recursos na localização-alvo da subscrição.
    - Na **rede virtual Target,** selecione a rede a partir de uma lista de toda a rede virtual na localização alvo.
    - No **conjunto de**disponibilidade, pode adicionar definições de disponibilidade ao VM, se fizerem parte de um conjunto de disponibilidade na região de origem.
    - Nas **contas de Armazenamento de Destino,** selecione a conta que pretende utilizar.

        ![Ativar a replicação](./media/site-recovery-replicate-azure-to-azure/customize.PNG)
3. Clique em **Personalizar:** para modificar as definições de replicação.
4. Na **consistência multi-VM,** selecione os VMs que pretende replicar em conjunto.
    - Todas as máquinas num grupo de replicação irão partilhar pontos de recuperação consistentes com a aplicação e com falhas aquando da ativação pós-falha.
    - Permitir a consistência multi-VM pode ter impacto no desempenho da carga de trabalho (uma vez que é intensivo em CPU). Só deve ser ativado se as máquinas estiverem a funcionar com a mesma carga de trabalho, e precisar de consistência em várias máquinas.
    - Por exemplo, se uma aplicação tiver 2 máquinas virtuais do SQL Server e dois servidores web, então deve adicionar apenas os VMs do Servidor SQL a um grupo de replicação.
    - Pode optar por ter um máximo de 16 VMs num grupo de replicação.
    - Se ativar a consistência multi-VM, as máquinas no grupo de replicação comunicam entre si pela porta 20004.
    - Certifique-se de que não há nenhum aparelho de firewall que bloqueie a comunicação interna entre os VMs sobre a porta 20004.
    - Se quiser que os VMs Linux façam parte de um grupo de replicação, certifique-se de que o tráfego de saída na porta 20004 está manualmente aberto de acordo com a orientação para a versão específica do Linux.
![Ativar replicação](./media/site-recovery-replicate-azure-to-azure/multivmsettings.PNG)

5. Clique em Criar**a replicação de ativação**do **recurso-alvo** > .
6. Depois de os VMs estarem habilitados para a replicação, pode verificar o estado da saúde vm em **itens replicados**

>[!NOTE]
>Durante a replicação inicial, o estado pode levar algum tempo a refrescar-se, sem progressos. Clique no botão **Refresh,** para obter o estado mais recente.
>

## <a name="next-steps"></a>Passos seguintes

[Saiba mais](site-recovery-test-failover-to-azure.md) sobre executar um teste failover.
