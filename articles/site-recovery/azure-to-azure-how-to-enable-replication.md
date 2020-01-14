---
title: Configurar a replicação para VMs do Azure no Azure Site Recovery | Microsoft Docs
description: Este artigo descreve como configurar a replicação para VMs do Azure, de uma região do Azure para outra usando Site Recovery.
services: site-recovery
author: carmonmills
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/29/2018
ms.author: carmonm
ms.openlocfilehash: 4dbac05ddf747ccaf483e547a2070505487a3706
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2020
ms.locfileid: "75929862"
---
# <a name="replicate-azure-vms-to-another-azure-region"></a>Replicar VMs do Azure para outra região do Azure


Este artigo descreve como habilitar a replicação de VMs do Azure, de uma região do Azure para outra.

## <a name="before-you-start"></a>Antes de começar

Este artigo pressupõe que você se preparou para Site Recovery implantação, conforme descrito no [tutorial de recuperação de desastre do Azure para o Azure](azure-to-azure-tutorial-enable-replication.md).

Os pré-requisitos devem estar em vigor e você deve ter criado um cofre dos serviços de recuperação.


## <a name="enable-replication"></a>Ativar a replicação

Ative a replicação. Este procedimento pressupõe que a região primária do Azure está Ásia Oriental e a região secundária é Ásia Oriental do Sul.

1. No cofre, clique em **+ replicar**.
2. Observe os seguintes campos:
   - **Origem**: o ponto de origem das VMs, que neste caso é o **Azure**.
   - **Local de origem**: a região do Azure de onde você deseja proteger suas VMs. Para esta ilustração, o local de origem é ' Ásia Oriental '
   - **Modelo de implantação**: modelo de implantação do Azure dos computadores de origem.
   - **Assinatura de origem**: a assinatura à qual as VMs de origem pertencem. Isto pode ser qualquer subscrição com o mesmo inquilino do Azure Active Directory onde existe o cofre de serviços de recuperação.
   - **Grupo de recursos**: o grupo de recursos ao qual as máquinas virtuais de origem pertencem. Todas as VMs no grupo de recursos selecionado são listadas para proteção na próxima etapa.

     ![Ativar a replicação](./media/site-recovery-replicate-azure-to-azure/enabledrwizard1.png)

3. Em **máquinas virtuais > selecionar máquinas virtuais**, clique e selecione cada VM que você deseja replicar. Só pode selecionar máquinas para as quais a replicação pode ser ativada. Em seguida, clique em **OK**.
    ![Ativar replicação](./media/site-recovery-replicate-azure-to-azure/virtualmachine_selection.png)

4. Em **configurações**, você pode opcionalmente definir as configurações de site de destino:

   - **Local de destino**: o local onde os dados da máquina virtual de origem serão replicados. Dependendo do local das máquinas selecionadas, Site Recovery fornecerá a lista de regiões de destino adequadas. Recomendamos que você mantenha o local de destino da mesma forma que o local do cofre dos serviços de recuperação.
   - **Subscrição de destino**: A subscrição de destino utilizada para recuperação após desastre. Por predefinição, a subscrição de destino será igual à subscrição de origem.
   - **Grupo de recursos de destino**: o grupo de recursos ao qual todas as máquinas virtuais replicadas pertencem.
       - Por padrão Site Recovery cria um novo grupo de recursos na região de destino com um sufixo "ASR" no nome.
       - Se o grupo de recursos criado pelo Site Recovery já existir, ele será reutilizado.
       - Você pode personalizar as configurações do grupo de recursos.
       - O local do grupo de recursos de destino pode ser qualquer região do Azure, exceto a região em que as VMs de origem estão hospedadas.
   - **Rede virtual de destino**: por padrão, site Recovery cria uma nova rede virtual na região de destino com um sufixo "ASR" no nome. Isso é mapeado para sua rede de origem e usado para qualquer proteção futura. [Saiba mais](site-recovery-network-mapping-azure-to-azure.md) sobre o mapeamento de rede.
   - **Contas de armazenamento de destino (a VM de origem não usa discos gerenciados)** : por padrão, site Recovery cria uma nova conta de armazenamento de destino imitando sua configuração de armazenamento de VM de origem. Caso a conta de armazenamento já exista, ela será reutilizada.
   - **Discos gerenciados por réplica (a VM de origem usa discos gerenciados)** : Site Recovery cria novos discos gerenciados por réplica na região de destino para espelhar os discos gerenciados da VM de origem com o mesmo tipo de armazenamento (Standard ou Premium) que o disco gerenciado da VM de origem.
   - **Contas de armazenamento em cache**: Site Recovery precisa de uma conta de armazenamento extra chamada armazenamento em cache na região de origem. Todas as alterações que ocorrem nas VMs de origem são rastreadas e enviadas para a conta de armazenamento de cache antes de replicar essas no local de destino. Essa conta de armazenamento deve ser padrão.
   - **Conjuntos de disponibilidade de destino**: por padrão, site Recovery cria um novo conjunto de disponibilidade na região de destino com o sufixo "ASR" no nome, para VMs que fazem parte de um conjunto de disponibilidade na região de origem. Se o conjunto de disponibilidade criado pelo Site Recovery já existir, ele será reutilizado.
   - **Zonas de disponibilidade de destino**: por padrão, site Recovery atribui o mesmo número de zona que a região de origem na região de destino se a região de destino oferecer suporte a zonas de disponibilidade.

     Se a região de destino não oferecer suporte a zonas de disponibilidade, as VMs de destino serão configuradas como instâncias únicas por padrão. Se necessário, você pode configurar essas VMs para que façam parte dos conjuntos de disponibilidade na região de destino clicando em ' Personalizar '.

     >[!NOTE]
     >Você não pode alterar o tipo de disponibilidade-instância única, conjunto de disponibilidade ou zona de disponibilidade, depois de habilitar a replicação. Você precisa desabilitar e habilitar a replicação para alterar o tipo de disponibilidade.
     >
    
   - **Política de replicação**: define as configurações para o histórico de retenção de ponto de recuperação e a frequência de instantâneo consistente do aplicativo. Por padrão, Azure Site Recovery cria uma nova política de replicação com configurações padrão de "24 horas" para retenção de ponto de recuperação e "4 horas" para a frequência de instantâneo consistente do aplicativo.

     ![Ativar a replicação](./media/site-recovery-replicate-azure-to-azure/enabledrwizard3.PNG)

### <a name="enable-replication-for-added-disks"></a>Habilitar a replicação para discos adicionados

Se você adicionar discos a uma VM do Azure para a qual a replicação está habilitada, ocorrerá o seguinte:
-   A integridade da replicação para a VM mostra um aviso, e uma observação informa que um ou mais discos estão disponíveis para proteção.
-   Se você habilitar a proteção para os discos adicionados, o aviso desaparecerá após a replicação inicial do disco.
-   Se optar por não habilitar a replicação para o disco, você poderá optar por ignorar o aviso.

    
    ![Novo disco adicionado](./media/azure-to-azure-how-to-enable-replication/newdisk.png)

Para habilitar a replicação para um disco adicionado, faça o seguinte:

1.  No cofre > **itens replicados**, clique na VM para a qual você adicionou o disco.
2.  Clique em **discos**e selecione o disco de dados para o qual você deseja habilitar a replicação (esses discos têm um status **não protegido** ).
3.  Em **detalhes do disco**, clique em **habilitar replicação**.

    ![Habilitar a replicação para disco adicionado](./media/azure-to-azure-how-to-enable-replication/enabled-added.png)

Depois que o trabalho habilitar replicação é executado e a replicação inicial é concluída, o aviso de integridade da replicação para o problema do disco é removido.


  
## <a name="customize-target-resources"></a>Personalizar recursos de destino

Você pode modificar as configurações de destino padrão usadas pelo Site Recovery.

1. Clique em **Personalizar:** ao lado de "assinatura de destino" para modificar a assinatura de destino padrão. Selecione a assinatura na lista de todas as assinaturas disponíveis no mesmo locatário Azure Active Directory (AAD).

2. Clique em **Personalizar:** para modificar as configurações padrão:
    - Em **grupo de recursos de destino**, selecione o grupo de recursos na lista de todos os grupos de recursos no local de destino da assinatura.
    - Em **rede virtual de destino**, selecione a rede em uma lista de todas as redes virtuais no local de destino.
    - No **conjunto de disponibilidade**, você pode adicionar configurações de conjunto de disponibilidade à VM, se elas fizerem parte de um conjunto de disponibilidade na região de origem.
    - Em **contas de armazenamento de destino**, selecione a conta que você deseja usar.

        ![Ativar a replicação](./media/site-recovery-replicate-azure-to-azure/customize.PNG)
3. Clique em **Personalizar:** para modificar as configurações de replicação.
4. Em **consistência de várias VMs**, selecione as VMs que você deseja replicar juntas.
    - Todas as máquinas num grupo de replicação irão partilhar pontos de recuperação consistentes com a aplicação e com falhas aquando da ativação pós-falha.
    - A habilitação da consistência de várias VMs pode afetar o desempenho da carga de trabalho (com uso intensivo de CPU). Ele só deverá ser habilitado se os computadores estiverem executando a mesma carga de trabalho e você precisar de consistência em vários computadores.
    - Por exemplo, se um aplicativo tiver 2 SQL Server máquinas virtuais e dois servidores Web, você deverá adicionar apenas as VMs SQL Server a um grupo de replicação.
    - Você pode optar por ter um máximo de 16 VMs em um grupo de replicação.
    - Se ativar a consistência multi-VM, as máquinas no grupo de replicação comunicam entre si pela porta 20004.
    - Verifique se não há nenhum dispositivo de firewall bloqueando a comunicação interna entre as VMs pela porta 20004.
    - Se você quiser que as VMs do Linux façam parte de um grupo de replicação, certifique-se de que o tráfego de saída na porta 20004 seja aberto manualmente de acordo com as diretrizes para a versão específica do Linux.
![Ativar replicação](./media/site-recovery-replicate-azure-to-azure/multivmsettings.PNG)
    
5. Clique em **criar recurso de destino** > **habilitar a replicação**.
6. Depois que as VMs estiverem habilitadas para replicação, você poderá verificar o status da integridade da VM em **itens replicados**

>[!NOTE]
>Durante a replicação inicial, o status pode levar algum tempo para ser atualizado, sem progresso. Clique no botão **Atualizar** para obter o status mais recente.
>

## <a name="next-steps"></a>Passos seguintes

[Saiba mais](site-recovery-test-failover-to-azure.md) sobre como executar um failover de teste.
