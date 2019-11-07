---
title: Mover VMs para uma região do Azure com zonas de disponibilidade usando Azure Site Recovery
services: site-recovery
author: rajani-janaki-ram
ms.service: site-recovery
ms.topic: tutorial
ms.date: 01/28/2019
ms.author: rajanaki
ms.custom: MVC
ms.openlocfilehash: c9513ca38c665a53bc14f284e87a1cc01035d135
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73693178"
---
# <a name="move-azure-vms-into-availability-zones"></a>Mover VMs do Azure para Zonas de Disponibilidade
Zonas de Disponibilidade no Azure ajudam a proteger seus aplicativos e dados de falhas do datacenter. Cada Zona de Disponibilidade é composta por um ou mais datacenters equipados com energia, refrigeração e rede independentes. Para garantir a resiliência, há um mínimo de três zonas separadas em todas as regiões habilitadas. A separação física de Zonas de Disponibilidade em uma região ajuda a proteger aplicativos e dados de falhas do datacenter. Com o Zonas de Disponibilidade, o Azure oferece um SLA (contrato de nível de serviço) de 99,99% para o tempo de atividade de VMs (máquinas virtuais). Zonas de Disponibilidade têm suporte em regiões selecionadas, conforme mencionado no [que são zonas de disponibilidade no Azure?](https://docs.microsoft.com/azure/availability-zones/az-overview#services-support-by-region).

Em um cenário em que suas VMs são implantadas como *instância única* em uma região específica e você deseja melhorar sua disponibilidade movendo essas VMs para uma zona de disponibilidade, você pode fazer isso usando Azure site Recovery. Essa ação pode ser categorizada ainda mais em:

- Mover VMs de instância única para Zonas de Disponibilidade em uma região de destino
- Mover VMs em um conjunto de disponibilidade para Zonas de Disponibilidade em uma região de destino

> [!IMPORTANT]
> Atualmente, o Azure Site Recovery dá suporte à movimentação de VMs de uma região para outra, mas não dá suporte à movimentação em uma região.

## <a name="check-prerequisites"></a>Verificar pré-requisitos

- Verifique se a região de destino tem [suporte para zonas de disponibilidade](https://docs.microsoft.com/azure/availability-zones/az-overview#services-support-by-region). Verifique se [há suporte para a combinação](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix#region-support)de região da região de origem/destino. Tome uma decisão informada sobre a região de destino.
- Certifique-se de que compreende a [arquitetura e os componentes do cenário](azure-to-azure-architecture.md).
- Veja os [requisitos e limitações de suporte](azure-to-azure-support-matrix.md).
- Verifique as permissões da conta. Se você acabou de criar sua conta gratuita do Azure, você é o administrador da sua assinatura. Se você não for o administrador da assinatura, trabalhe com o administrador para atribuir as permissões necessárias. Para habilitar a replicação para uma VM e eventualmente copiar dados para o destino usando Azure Site Recovery, você deve ter:

    1. Permissão para criar uma VM nos recursos do Azure. A função interna *colaborador da máquina virtual* tem essas permissões, que incluem:
        - Permissão para criar uma VM no grupo de recursos selecionado
        - Permissão para criar uma VM na rede virtual selecionada
        - Permissão para escrever na conta de armazenamento selecionada

    2. Permissão para gerenciar tarefas de Azure Site Recovery. A função *colaborador de site Recovery* tem todas as permissões necessárias para gerenciar site Recovery ações em um cofre dos serviços de recuperação.

## <a name="prepare-the-source-vms"></a>Preparar as VMs de origem

1. Suas VMs devem usar discos gerenciados se você quiser movê-los para uma zona de disponibilidade usando Site Recovery. Você pode converter VMs do Windows existentes que usam discos não gerenciados para usar discos gerenciados. Siga as etapas em [converter uma máquina virtual do Windows de discos não gerenciados em discos gerenciados](https://docs.microsoft.com/azure/virtual-machines/windows/convert-unmanaged-to-managed-disks). Verifique se o conjunto de disponibilidade está configurado como *gerenciado*.
2. Verifique se todos os certificados raiz mais recentes estão presentes nas VMs do Azure que você deseja mover. Se os certificados raiz mais recentes não estiverem presentes, a cópia de dados para a região de destino não poderá ser habilitada devido a restrições de segurança.

3. Para VMs do Windows, instale todas as atualizações mais recentes do Windows na VM, para que todos os certificados de raiz fidedigna fiquem na máquina. Em um ambiente desconectado, siga os processos padrão de atualização de certificado e atualização do Windows para sua organização.

4. Para VMs do Linux, siga as orientações fornecidas pelo seu distribuidor do Linux para obter os certificados raiz confiáveis mais recentes e a lista de certificados revogados na VM.
5. Certifique-se de não usar um proxy de autenticação para controlar a conectividade de rede para as VMs que você deseja mover.

6. Se a VM que você está tentando mover não tiver acesso à Internet e usar um proxy de firewall para controlar o acesso de saída, verifique os requisitos em [configurar conectividade de rede de saída](azure-to-azure-tutorial-enable-replication.md#set-up-outbound-network-connectivity-for-vms).

7. Identifique o layout de rede de origem e os recursos que você usa atualmente para verificação, incluindo balanceadores de carga, NSGs e IP público.

## <a name="prepare-the-target-region"></a>Preparar a região de destino

1. Verifique se sua assinatura do Azure permite criar VMs na região de destino usada para recuperação de desastre. Se necessário, entre em contato com o suporte para habilitar a cota necessária.

2. Certifique-se de que a sua subscrição tem recursos suficientes para suportar as VMs com tamanhos que correspondem às VMs de origem. Se você usar Site Recovery para copiar dados para o destino, ele escolherá o mesmo tamanho ou o tamanho mais próximo possível para a VM de destino.

3. Crie um recurso de destino para cada componente identificado no layout de rede de origem. Essa ação garante que depois de você passar para a região de destino, suas VMs terão toda a funcionalidade e os recursos que você tinha na origem.

    > [!NOTE]
    > Azure Site Recovery descobre e cria automaticamente uma conta de armazenamento e rede virtual quando você habilita a replicação para a VM de origem. Você também pode criar previamente esses recursos e atribuir à VM como parte da etapa habilitar replicação. Mas para todos os outros recursos, conforme mencionado posteriormente, você precisa criá-los manualmente na região de destino.

     Os documentos a seguir informam como criar os recursos de rede usados com mais frequência que são relevantes para você, com base na configuração da VM de origem.

    - [Grupos de segurança de rede](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)
    - [Balanceadores de carga](https://docs.microsoft.com/azure/load-balancer)
    - [IP público](../virtual-network/virtual-network-public-ip-address.md)
    
   Para qualquer outro componente de rede, consulte a [documentação](https://docs.microsoft.com/azure/#pivot=products&panel=network)de rede.

    > [!IMPORTANT]
    > Certifique-se de usar um balanceador de carga com redundância de zona no destino. Você pode ler mais em [Standard Load Balancer e zonas de disponibilidade](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones).

4. [Crie manualmente uma rede que não seja de produção](https://docs.microsoft.com/azure/virtual-network/quick-create-portal) na região de destino se desejar testar a configuração antes de passar para a região de destino. É recomendável que essa abordagem cause interferência mínima com o ambiente de produção.

## <a name="enable-replication"></a>Ativar a replicação
As etapas a seguir o orientarão ao usar Azure Site Recovery para habilitar a replicação de dados para a região de destino, antes de você movê-los para o Zonas de Disponibilidade.

> [!NOTE]
> Essas etapas são para uma única VM. Você pode estender o mesmo para várias VMs. Vá para o cofre dos serviços de recuperação, selecione **+ replicar**e selecione as VMs relevantes juntas.

1. No portal do Azure, selecione **máquinas virtuais**e selecione a VM que você deseja mover para zonas de disponibilidade.
2. Em **Operações**, selecione **Recuperação após desastre**.
3. Em **Configurar recuperação de desastre** > **região de destino**, selecione a região de destino para a qual você irá replicar. Verifique se essa região [dá suporte](https://docs.microsoft.com/azure/availability-zones/az-overview#services-support-by-region) a zonas de disponibilidade.

    ![Seleção da região de destino](media/azure-vms-to-zones/enable-rep-1.PNG)

4. Selecione **Avançar: configurações avançadas**.
5. Escolha os valores apropriados para a assinatura de destino, grupo de recursos de VM de destino e rede virtual.
6. Na seção **disponibilidade** , escolha a zona de disponibilidade na qual você deseja mover a VM. 
   > [!NOTE]
   > Se você não vir a opção de conjunto de disponibilidade ou zona disponibilidade, verifique se os [pré-requisitos](#prepare-the-source-vms) foram atendidos e se a [preparação](#prepare-the-source-vms) das VMs de origem foi concluída.
  
    ![Seleções para escolher uma zona de disponibilidade](media/azure-vms-to-zones/enable-rep-2.PNG)

7. Selecione **Ativar Replicação**. Essa ação inicia um trabalho para habilitar a replicação para a VM.

## <a name="check-settings"></a>Verificar configurações

Após a tarefa de replicação ser concluída, pode ver o estado da replicação, modificar a replicação e testar a implementação.

1. No menu da VM, clique em **Recuperação após desastre**.
2. Você pode verificar a integridade da replicação, os pontos de recuperação que foram criados e a origem e as regiões de destino no mapa.

   ![Estado de replicação](media/azure-to-azure-quickstart/replication-status.png)

## <a name="test-the-configuration"></a>Testar a configuração

1. No menu máquina virtual, selecione **recuperação de desastre**.
2. Selecione o ícone **failover de teste** .
3. Em **failover de teste**, selecione um ponto de recuperação a ser usado para o failover:

   - **Processado mais recentemente**: faz a ativação pós-falha da VM para o ponto de recuperação mais recente processado pelo serviço do Site Recovery. O carimbo de data/hora é apresentado. Com esta opção, não é despendido tempo a processar os dados, pelo que oferece um RTO (objetivo de tempo de recuperação) baixo.
   - **Consistente com a aplicação mais recente**: esta opção faz a ativação pós-falha de todas as VMs para o último ponto de recuperação consistente com a aplicação. O carimbo de data/hora é apresentado.
   - **Personalizado**: selecione qualquer ponto de recuperação.

3. Selecione a rede virtual do Azure de destino para a qual você deseja mover as VMs do Azure para testar a configuração. 

    > [!IMPORTANT]
    > Recomendamos que você use uma rede VM do Azure separada para a falha de teste, e não a rede de produção na região de destino na qual você deseja mover suas VMs.

4. Para começar a testar a movimentação, selecione **OK**. Para acompanhar o progresso, selecione a VM para abrir suas propriedades. Ou, você pode selecionar o trabalho de **failover de teste** no nome do cofre > **configurações** > **trabalhos** > **trabalhos de site Recovery**.
5. Após a conclusão da ativação pós-falha, a VM do Azure de réplica é apresentada no portal do Azure > **Máquinas Virtuais**. Certifique-se de que a VM está em execução, tem as dimensões adequadas e está ligada à rede devida.
6. Se você quiser excluir a VM criada como parte do teste da movimentação, selecione **limpar failover de teste** no item replicado. Em **observações**, registre e salve as observações associadas ao teste.

## <a name="move-to-the-target-region-and-confirm"></a>Mover para a região de destino e confirmar

1.  No menu máquina virtual, selecione **recuperação de desastre**.
2. Selecione o ícone de **failover** .
3. Em **Ativação pós-falha**, selecione **Mais recente**. 
4. Selecione **Encerrar a máquina antes de iniciar a ativação pós-falha**. O Site Recovery tenta encerrar a VM de origem antes de acionar a ativação pós-falha. A ativação pós-falha continua, mesmo que o encerramento falhe. Pode seguir o progresso da ativação pós-falha na página **Tarefas**. 
5. Depois que o trabalho for concluído, verifique se a VM aparece na região de destino do Azure conforme o esperado.
6. Em **Itens replicados**, clique com o botão direito do rato na VM > **Consolidar**. Isso conclui o processo de movimentação para a região de destino. Aguarde até que o trabalho de confirmação seja concluído.

## <a name="discard-the-resource-in-the-source-region"></a>Descartar o recurso na região de origem

Vá para a VM. Selecione **desabilitar replicação**. Essa ação interrompe o processo de copiar os dados para a VM.  

> [!IMPORTANT]
> Faça a etapa anterior para evitar a cobrança por Site Recovery replicação após a movimentação. As definições de replicação da origem são limpas automaticamente. Observe que a extensão de Site Recovery instalada como parte da replicação não é removida e precisa ser removida manualmente.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você aumentou a disponibilidade de uma VM do Azure ao migrar para um conjunto de disponibilidade ou zona de disponibilidade. Agora você pode definir a recuperação de desastre para a VM movida.

> [!div class="nextstepaction"]
> [Configurar a recuperação após desastre após a migração](azure-to-azure-quickstart.md)


