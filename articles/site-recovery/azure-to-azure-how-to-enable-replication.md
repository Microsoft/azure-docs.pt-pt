---
title: Configure replicação para VMs Azure na Recuperação do Local de Azure
description: Aprenda a configurar a replicação para outra região para VMs Azure, utilizando a Recuperação do Local.
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 04/29/2018
ms.openlocfilehash: fe5feed4bb6f9b84a3f161692310922f7a6d2f00
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92424801"
---
# <a name="replicate-azure-vms-to-another-azure-region"></a>Replicar VMs do Azure para outra região do Azure


Este artigo descreve como permitir a replicação de VMs Azure, de uma região de Azure para outra.

## <a name="before-you-start"></a>Antes de começar

Este artigo pressupõe que se preparou para a implantação da Recuperação do Local, conforme descrito no tutorial de [recuperação de desastres Azure to Azure](azure-to-azure-tutorial-enable-replication.md).

Os pré-requisitos devem estar no lugar, e devias ter criado um cofre dos Serviços de Recuperação.


## <a name="enable-replication"></a>Ativar a replicação

Ative a replicação. Este procedimento pressupõe que a região primária de Azure é a Ásia Oriental, e a região secundária é o Sudeste Asiático.

1. No cofre, clique **em +Replicate.**
2. Note os seguintes campos:
   - **Fonte**: O ponto de origem dos VMs, que neste caso é **Azure**.
   - **Localização da origem**: A região de Azure de onde pretende proteger os seus VMs. Para esta ilustração, a localização da origem é 'Ásia Oriental'
   - **Modelo de implantação**: Modelo de implantação azul das máquinas de origem.
   - **Assinatura de origem**: A subscrição a que pertencem os seus VMs de origem. Isto pode ser qualquer subscrição com o mesmo inquilino do Azure Active Directory onde existe o cofre de serviços de recuperação.
   - **Grupo de Recursos**: O grupo de recursos a que pertencem as máquinas virtuais de origem. Todos os VMs do grupo de recursos selecionados estão listados para proteção no passo seguinte.

     ![Screenshot que realça os campos necessários para configurar a replicação.](./media/site-recovery-replicate-azure-to-azure/enabledrwizard1.png)

3. Em **Máquinas Virtuais > Selecione máquinas virtuais,** clique e selecione cada VM que pretende replicar. Só pode selecionar máquinas para as quais a replicação pode ser ativada. Em seguida, clique em **OK**.
    ![Screenshot que realça onde seleciona máquinas virtuais.](./media/site-recovery-replicate-azure-to-azure/virtualmachine_selection.png)

4. Nas **Definições,** pode configurar opcionalmente as definições do site do alvo:

   - **Localização do alvo**: O local onde os dados da sua máquina virtual de origem serão replicados. Dependendo da localização das máquinas selecionadas, a Recuperação do Local fornecer-lhe-á a lista de regiões-alvo adequadas. Recomendamos que mantenha o local do alvo igual ao local do cofre dos Serviços de Recuperação.
   - **Subscrição de destino**: A subscrição de destino utilizada para recuperação após desastre. Por predefinição, a subscrição de destino será igual à subscrição de origem.
   - **Grupo de recursos-alvo**: O grupo de recursos ao qual todas as suas máquinas virtuais replicadas pertencem.
       - Por predefinição, a Recuperação do Local cria um novo grupo de recursos na região alvo com um sufixo "asr" no nome.
       - Se o grupo de recursos criado pela Recuperação do Local já existir, é reutilizado.
       - Pode personalizar as definições do grupo de recursos.
       - A localização do grupo de recursos-alvo pode ser qualquer região de Azure, exceto a região em que os VMs de origem estão hospedados.
   - **Rede virtual alvo**: Por padrão, a Recuperação do Site cria uma nova rede virtual na região alvo com um sufixo "asr" no nome. Isto está mapeado para a sua rede de origem, e usado para qualquer proteção futura. [Saiba mais](./azure-to-azure-network-mapping.md) sobre mapeamento de rede.
   - **Contas de armazenamento de destino (fonte VM não utiliza discos geridos)**: Por padrão, a Recuperação do Site cria uma nova conta de armazenamento-alvo que imita a configuração de armazenamento de VM de origem. Caso a conta de armazenamento já exista, é reutilizada.
   - **Discos geridos por réplicas (fonte VM utiliza discos geridos)**: A Recuperação do Local cria novos discos geridos por réplicas na região alvo para espelhar os discos geridos pela fonte VM com o mesmo tipo de armazenamento (Standard ou premium) que o disco gerido pela VM de origem.
   - **Cache Contas de Armazenamento**: A Recuperação do Local necessita de uma conta de armazenamento extra chamada armazenamento de cache na região de origem. Todas as alterações que ocorram nos VMs de origem são rastreadas e enviadas para a conta de armazenamento de cache antes de as replicarem para o local alvo. Esta conta de armazenamento deve ser standard.
   - **Conjuntos de disponibilidade de destino**: Por predefinição, a Recuperação do Site cria uma nova disponibilidade definida na região alvo com o sufixo "asr" no nome, para VMs que fazem parte de uma disponibilidade definida na região de origem. Se o conjunto de disponibilidade criado pela Recuperação do Site já existir, é reutilizado.
   - **Zonas de disponibilidade de destino**: Por predefinição, a Recuperação do Local atribui o mesmo número de zona que a região-fonte na região-alvo se a região alvo apoiar zonas de disponibilidade.

     Se a região alvo não suporta zonas de disponibilidade, os VM-alvo são configurados como instâncias únicas por padrão. Se necessário, pode configurar esses VMs para fazer parte dos conjuntos de disponibilidade na região alvo clicando em 'Personalizar'.

     >[!NOTE]
     >Não é possível alterar o tipo de disponibilidade - instância única, definição de disponibilidade ou zona de disponibilidade, depois de ativar a replicação. É necessário desativar e permitir a replicação para alterar o tipo de disponibilidade.
     >

   - **Política de replicação**: Define as definições para o histórico de retenção de pontos de recuperação e frequência de instantâneo consistente da aplicação. Por predefinição, a Azure Site Recovery cria uma nova política de replicação com definições padrão de '24 horas' para retenção de pontos de recuperação e '4 horas' para a frequência instantânea consistente da aplicação.

     ![Ativar a replicação](./media/site-recovery-replicate-azure-to-azure/enabledrwizard3.PNG)

### <a name="enable-replication-for-added-disks"></a>Ativar a replicação para discos adicionados

Se adicionar discos a um VM Azure para o qual a replicação está ativada, ocorre o seguinte:
-   A saúde de replicação para o VM mostra um aviso, e uma nota informa que um ou mais discos estão disponíveis para proteção.
-   Se ativar a proteção dos discos adicionados, o aviso desaparecerá após a replicação inicial do disco.
-   Se optar por não ativar a replicação do disco, pode selecionar para rejeitar o aviso.


    ![Novo disco adicionado](./media/azure-to-azure-how-to-enable-replication/newdisk.png)

Para permitir a replicação de um disco adicionado, faça o seguinte:

1.  No cofre > **Itens Replicados,** clique no VM ao qual adicionou o disco.
2.  Clique **em Discos**e, em seguida, selecione o disco de dados para o qual pretende ativar a replicação (estes discos têm um estado não **protegido).**
3.  Em **Detalhes do Disco**, clique em **Ativar a replicação.**

    ![Ativar a replicação para o disco adicionado](./media/azure-to-azure-how-to-enable-replication/enabled-added.png)

Após o funcionar do trabalho de replicação ativa e o acabamento da replicação inicial, o aviso de saúde de replicação para o problema do disco é removido.



## <a name="customize-target-resources"></a>Personalizar recursos-alvo

Pode modificar as definições de alvo predefinidos utilizadas pela Recuperação do Site.

1. Clique **em Personalizar:** ao lado da subscrição 'Target' para modificar a subscrição de destino predefinido. Selecione a subscrição da lista de todas as subscrições disponíveis no mesmo inquilino Azure Ative Directory (AAD).

2. Clique **em Personalizar:** para modificar as definições predefinições:
    - No **grupo de recursos Target,** selecione o grupo de recursos da lista de todos os grupos de recursos na localização alvo da subscrição.
    - Na **rede virtual Target,** selecione a rede a partir de uma lista de toda a rede virtual no local alvo.
    - No **conjunto Disponibilidade,** pode adicionar definições de disponibilidade ao VM, se fizerem parte de um conjunto de disponibilidade na região de origem.
    - Nas **contas de Armazenamento Alvo,** selecione a conta que pretende utilizar.

        ![Screenshot que mostra como personalizar as definições de subscrição de alvo.](./media/site-recovery-replicate-azure-to-azure/customize.PNG)
3. Clique **em Personalizar:** para modificar as definições de replicação.
4. Na **consistência multi-VM,** selecione os VMs que pretende replicar juntos.
    - Todas as máquinas num grupo de replicação irão partilhar pontos de recuperação consistentes com a aplicação e com falhas aquando da ativação pós-falha.
    - Permitir a consistência multi-VM pode ter impacto no desempenho da carga de trabalho (uma vez que é intensivo em CPU). Só deve ser ativado se as máquinas estiverem a funcionar da mesma carga de trabalho e necessitar de consistência em várias máquinas.
    - Por exemplo, se uma aplicação tiver 2 máquinas virtuais SQL Server e dois servidores web, então deve adicionar apenas os VMs do SQL Server a um grupo de replicação.
    - Pode optar por ter um máximo de 16 VMs num grupo de replicação.
    - Se ativar a consistência multi-VM, as máquinas no grupo de replicação comunicam entre si pela porta 20004.
    - Certifique-se de que não existe um aparelho de firewall que bloqueie a comunicação interna entre os VMs sobre a porta 20004.
    - Se pretender que os VMs do Linux fazem parte de um grupo de replicação, certifique-se de que o tráfego de saída na porta 20004 é aberto manualmente de acordo com as orientações para a versão específica do Linux.
![Screenshot que mostra as definições de consistência multi-VM.](./media/site-recovery-replicate-azure-to-azure/multivmsettings.PNG)

5. Clique **em Criar recurso-alvo**  >  **Ativar a replicação**.
6. Depois de os VMs estarem ativados para replicação, pode verificar o estado da saúde em VM em **itens replicados**

>[!NOTE]
>
> - Durante a replicação inicial, o estado pode demorar algum tempo a refrescar-se, sem progressos. Clique no botão **Refresh,** para obter o estado mais recente.
> - Se um ponto de recuperação não tiver sido gerado nos últimos 60 minutos, a saúde de replicação da máquina virtual tornar-se-á crítica.

## <a name="next-steps"></a>Passos seguintes

[Saiba mais](site-recovery-test-failover-to-azure.md) sobre a execução de um teste falhado.
