---
title: Configurar a replicação das VMs do Azure no Azure Site Recovery | Documentos da Microsoft
description: Este artigo descreve como configurar a replicação de VMs do Azure, de uma região do Azure para outra com o Site Recovery.
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: asgang
ms.openlocfilehash: c8b5cf840b8cb5eec2a993cfe35c8a8a7ac904fb
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58079782"
---
# <a name="replicate-azure-virtual-machines-to-another-azure-region"></a>Replicar máquinas virtuais do Azure para outra região do Azure



Este artigo descreve como ativar a replicação de VMs do Azure, de uma região do Azure para outra.

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que já tiver definido a recuperação de Site para este cenário, conforme descrito no [tutorial do Azure para o Azure](azure-to-azure-tutorial-enable-replication.md). Certifique-se de que já preparou os pré-requisitos e criou o Cofre dos serviços de recuperação.



## <a name="enable-replication"></a>Ativar a replicação

Ative a replicação. Este procedimento assume que a região primária do Azure é a Ásia Oriental e a região secundária é Sudeste asiático.

1. No cofre, clique em **+ replicar**.
2. Tenha em atenção os seguintes campos:
   - **origem**: O ponto de origem das VMs, que neste caso é **Azure**.
   - **Localização de origem**: A região do Azure em que pretende proteger as suas máquinas virtuais. Nesta ilustração, a localização de origem é 'Ásia Oriental'
   - **Modelo de implementação**: Modelo de implementação do Azure, máquinas de origem.
   - **Subscrição de origem**: A subscrição ao qual pertencem a suas máquinas de virtuais de origem. Isto pode ser qualquer subscrição com o mesmo inquilino do Azure Active Directory onde existe o cofre de serviços de recuperação.
   - **Grupo de recursos**: O grupo de recursos ao qual pertencem a suas máquinas de virtuais de origem. Todas as VMs no grupo de recursos selecionada são listadas para proteção no próximo passo.

     ![Ativar a replicação](./media/site-recovery-replicate-azure-to-azure/enabledrwizard1.png)

3. Na **máquinas virtuais > Selecione as máquinas virtuais**, clique e selecione cada VM que pretende replicar. Só pode selecionar máquinas para as quais a replicação pode ser ativada. Em seguida, clique em **OK**.
    ![Ativar replicação](./media/site-recovery-replicate-azure-to-azure/virtualmachine_selection.png)

4. Na **definições**, opcionalmente, pode configurar as definições de site de destino:

   - **Localização de destino**: A localização onde os dados de máquina virtual de origem serão replicados. Consoante a localização de máquinas selecionadas, Site Recovery fornece a lista de regiões de destino adequado. Recomendamos que mantenha a localização de destino a mesma da localização do Cofre de serviços de recuperação.
   - **Subscrição de destino**: A subscrição de destino utilizada para recuperação após desastre. Por predefinição, a subscrição de destino será igual à subscrição de origem.
   - **Grupo de recursos de destino**: O grupo de recursos para que todos os seus máquinas virtuais replicadas pertence. Por predefinição o Azure Site Recovery cria um novo grupo de recursos na região de destino com um nome que o sufixo "asr". No caso de grupo de recursos criado pelo Azure Site Recovery já existe, este é reutilizado. Também é possível personalizá-lo, conforme mostrado na secção abaixo. A localização do grupo de recursos de destino pode ser qualquer região do Azure, exceto a região na qual as máquinas de virtuais de origem estão alojadas.
   - **Rede Virtual de destino**: Por predefinição, o Site Recovery cria uma nova rede virtual na região de destino com um nome que o sufixo "asr". Este é mapeado para a sua rede de origem e utilizado para qualquer proteção futura. [Saiba mais](site-recovery-network-mapping-azure-to-azure.md) sobre mapeamento de rede.
   - **Contas de armazenamento de destino (se a sua origem de VM não utiliza discos geridos)**: Por predefinição, o Site Recovery cria uma nova conta de armazenamento de destino imitando a configuração de armazenamento VM de origem. No caso de já existe uma conta de armazenamento, este é reutilizado.
   - **Discos de geridos de réplica (se a VM de origem utilizar discos geridos)**: Site Recovery cria novos discos geridos de réplica na região de destino para espelhar os discos geridos da VM de origem com o mesmo tipo de armazenamento (Standard ou premium), como a VM de origem de disco gerido.
   - **Contas de armazenamento em cache**: Site Recovery precisa da conta de armazenamento extra chamada de armazenamento de cache na região de origem. Todas as alterações acontecendo nas VMs de origem são controladas e enviadas para a conta de armazenamento de cache antes de replicar os para a localização de destino.
   - **Conjuntos de disponibilidade de destino**: Por predefinição, o Azure Site Recovery cria uma novo conjunto de disponibilidade na região de destino com um nome que o sufixo "asr" para a VMs que fazem parte de um conjunto de disponibilidade na região de origem. No caso de conjunto de disponibilidade criado pelo Azure Site Recovery já existe, este é reutilizado.
   - **As zonas de disponibilidade de destino**: Por predefinição, o Site Recovery atribui o mesmo número de zona e a região de origem na região de destino se a região de destino suporta zonas de disponibilidade.

     Se a região de destino não suporta zonas de disponibilidade, as VMs de destino estão configuradas como instâncias únicas por predefinição. Se for necessário, pode configurar essas VMs para fazer parte de conjuntos de disponibilidade na região de destino ao clicar em "Personalizar".

     >[!NOTE]
     >Não é possível alterar o tipo de disponibilidade - instância única, a zona de disponibilidade ou conjunto de disponibilidade, depois de ativar a replicação. Terá de desativar e ativar a replicação alterar o tipo de disponibilidade.
     >
    
   - **Política de replicação**: Define as definições de recuperação ponto retenção histórico e aplicação de frequência de instantâneo consistente. Por predefinição, o Azure Site Recovery cria uma nova política de replicação com as predefinições dos ' 24 horas para retenção do ponto de recuperação e "60 minutos para a frequência de instantâneo consistente da aplicação.

     ![Ativar a replicação](./media/site-recovery-replicate-azure-to-azure/enabledrwizard3.PNG)
  
## <a name="customize-target-resources"></a>Personalizar os recursos de destino

Pode modificar as predefinições de destino utilizadas pelo Site Recovery.

1. Clique em **personalizar:** junto a "Subscrição de destino" para modificar a subscrição de destino predefinido. Selecione a subscrição da lista de todas as subscrições disponíveis no mesmo inquilino do Azure Active Directory (AAD).

2. Clique em **personalizar:** para modificar as predefinições:
    - Na **grupo de recursos de destino**, selecione o grupo de recursos na lista de todos os grupos de recursos na localização de destino da subscrição.
    - Na **rede virtual de destino**, selecione a rede de uma lista de todos os a rede virtual na localização de destino.
    - Na **conjunto de disponibilidade**, pode adicionar as definições de conjunto de disponibilidade para a VM, se eles são parte de um conjunto de disponibilidade na região de origem.
    - Na **contas de armazenamento de destino**, selecione a conta que pretende utilizar.

        ![Ativar a replicação](./media/site-recovery-replicate-azure-to-azure/customize.PNG)
1. Clique em **personalizar:** para modificar as definições de replicação.
   - Na **consistência multi-VM**, selecione as VMs que pretende replicar em conjunto 
   - Todas as máquinas num grupo de replicação irão partilhar pontos de recuperação consistentes com a aplicação e com falhas aquando da ativação pós-falha. Ativar a consistência multi-VM pode afetar o desempenho da carga de trabalho (como ele é exigente em termos de CPU) e deve ser usado apenas se as máquinas estão a executar a mesma carga de trabalho e precisar de consistência entre várias máquinas. Por exemplo, se um aplicativo tem 2 máquinas virtuais do sql e 2 servidores web, em seguida, deve adicionar apenas máquinas de virtuais do sql como parte do grupo de replicação.
   - Pode optar por ter no máximas 16 máquinas de virtuais num grupo de replicação.
   - Se ativar a consistência multi-VM, as máquinas no grupo de replicação comunicam entre si pela porta 20004. Certifique-se de que não há nenhum dispositivo de firewall a bloquear a comunicação interna entre as VMs através da porta 20004. Se pretender que as VMs do Linux pertençam a um grupo de replicação, certifique-se de que o tráfego de saída na porta 20004 é aberto manualmente de acordo com as orientações da versão específica do Linux.
![Ativar replicação](./media/site-recovery-replicate-azure-to-azure/multivmsettings.PNG)
    
2. Clique em **criar o recurso de destino** > **ativar replicação**.
3. Depois das VMs estiverem ativadas para replicação, pode verificar o estado do Estado de funcionamento da VM em **itens replicados**

>[!NOTE]
>Durante a replicação inicial, o estado pode demorar algum tempo para atualizar, sem o progresso. Clique nas **atualizar** botão, para obter o estado mais recente.
>

# <a name="next-steps"></a>Passos Seguintes

[Saiba mais](site-recovery-test-failover-to-azure.md) sobre como executar uma ativação pós-falha de teste.
