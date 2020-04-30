---
title: Mover VMs para uma região de Azure com zonas de disponibilidade usando a recuperação do site Azure
services: site-recovery
author: rajani-janaki-ram
ms.service: site-recovery
ms.topic: tutorial
ms.date: 01/28/2019
ms.author: rajanaki
ms.custom: MVC
ms.openlocfilehash: 1d771d1e13d1ffd92a18658d08bb948d97e55999
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82209014"
---
# <a name="move-azure-vms-into-availability-zones"></a>Mover VMs do Azure para Zonas de Disponibilidade
As Zonas de Disponibilidade em Azure ajudam a proteger as suas aplicações e dados de falhas no datacenter. Cada Zona de Disponibilidade é constituída por um ou mais datacenters equipados com energia, refrigeração e redes independentes. Para garantir a resiliência, há um mínimo de três zonas separadas em todas as regiões habilitadas. A separação física das Zonas de Disponibilidade dentro de uma região ajuda a proteger aplicações e dados de falhas no datacenter. Com Zonas de Disponibilidade, o Azure oferece um acordo de nível de serviço (SLA) de 99,99% para o tempo de uptime das máquinas virtuais (VMs). As Zonas de Disponibilidade são suportadas em regiões selecionadas, como mencionado nas [Regiões que suportam zonas de disponibilidade.](https://docs.microsoft.com/azure/availability-zones/az-region)

Num cenário em que os seus VMs são implantados como *instância única* numa região específica, e você quer melhorar a sua disponibilidade movendo estes VMs para uma Zona de Disponibilidade, você pode fazê-lo usando a Recuperação do Site Azure. Esta ação pode ser ainda categorizada em:

- Mover VMs de instância única para zonas de disponibilidade numa região-alvo
- Mover VMs em uma disponibilidade definida em Zonas de Disponibilidade em uma região alvo

> [!IMPORTANT]
> Atualmente, a Azure Site Recovery apoia a deslocação de VMs de uma região para outra, mas não suporta a deslocação dentro de uma região.

## <a name="check-prerequisites"></a>Verificar pré-requisitos

- Verifique se a região alvo tem [suporte para zonas de disponibilidade](https://docs.microsoft.com/azure/availability-zones/az-region). Verifique se a sua escolha da [combinação região fonte/região alvo é suportada](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix#region-support). Tome uma decisão informada sobre a região alvo.
- Certifique-se de que compreende a [arquitetura e os componentes do cenário](azure-to-azure-architecture.md).
- Veja os [requisitos e limitações de suporte](azure-to-azure-support-matrix.md).
- Verifique as permissões da conta. Se acabou de criar a sua conta Azure gratuita, é o administrador da sua subscrição. Se não for o administrador de subscrição, trabalhe com o administrador para atribuir as permissões de que necessita. Para permitir a replicação de um VM e, eventualmente, copiar dados para o alvo utilizando a Recuperação do Site Azure, deve ter:

    1. Permissão para criar um VM em recursos Azure. O papel integrado da *Máquina Virtual* tem estas permissões, que incluem:
        - Permissão para criar uma VM no grupo de recursos selecionado
        - Permissão para criar uma VM na rede virtual selecionada
        - Permissão para escrever na conta de armazenamento selecionada

    2. Permissão para gerir tarefas de recuperação de sites do Azure. O papel do Colaborador de *Recuperação* do Site tem todas as permissões necessárias para gerir as ações de recuperação do site num cofre de Serviços de Recuperação.

## <a name="prepare-the-source-vms"></a>Prepare as VMs de origem

1. Os seus VMs devem utilizar discos geridos se pretender movê-los para uma Zona de Disponibilidade utilizando a Recuperação do Site. Pode converter vMs windows existentes que utilizam discos não geridos para utilizar discos geridos. Siga os passos em [Converter uma máquina virtual do Windows de discos não geridos para discos geridos](https://docs.microsoft.com/azure/virtual-machines/windows/convert-unmanaged-to-managed-disks). Certifique-se de que o conjunto de disponibilidade está *configurado*como gerido .
2. Verifique se todos os certificados de raiz mais recentes estão presentes nos VMs Azure que pretende mover. Se os certificados de raiz mais recentes não estiverem presentes, a cópia de dados para a região alvo não pode ser ativada devido a restrições de segurança.

3. Para VMs do Windows, instale todas as atualizações mais recentes do Windows na VM, para que todos os certificados de raiz fidedigna fiquem na máquina. Num ambiente desligado, siga os processos padrão de atualização do Windows e atualização de certificados para a sua organização.

4. Para os VMs Linux, siga as orientações fornecidas pelo seu distribuidor Linux para obter os mais recentes certificados de raiz fidedignos e lista de revogação de certificados no VM.
5. Certifique-se de que não utiliza um proxy de autenticação para controlar a conectividade da rede para VMs que pretende mover.

6. Se o VM que está a tentar mover não tem acesso à internet e utiliza um proxy de firewall para controlar o acesso de saída, verifique os requisitos na conectividade de rede de saída da [Configure](azure-to-azure-tutorial-enable-replication.md#set-up-outbound-network-connectivity-for-vms).

7. Identifique o layout de rede de origem e os recursos que utiliza atualmente para verificação, incluindo equilibradores de carga, NSGs e IP público.

## <a name="prepare-the-target-region"></a>Preparar a região alvo

1. Verifique se a sua subscrição Azure permite criar VMs na região alvo utilizada para a recuperação de desastres. Se necessário, contacte o suporte para ativar a quota necessária.

2. Certifique-se de que a sua subscrição tem recursos suficientes para suportar as VMs com tamanhos que correspondem às VMs de origem. Se utilizar a Recuperação do Site para copiar dados para o alvo, ele escolhe o mesmo tamanho ou o tamanho mais próximo possível para o VM alvo.

3. Crie um recurso-alvo para todos os componentes identificados no layout de rede de origem. Esta ação garante que, depois de cortar para a região alvo, os seus VMs têm todas as funcionalidades e funcionalidades que tinha na fonte.

    > [!NOTE]
    > A Recuperação do Site Azure descobre e cria automaticamente uma rede virtual e uma conta de armazenamento quando ativa a replicação para o VM de origem. Também pode pré-criar estes recursos e atribuir ao VM como parte do passo de replicação ativa. Mas para quaisquer outros recursos, como mencionado mais tarde, é necessário criá-los manualmente na região alvo.

     Os seguintes documentos dizem como criar os recursos de rede mais utilizados que são relevantes para si, com base na configuração vm de origem.

    - [Grupos de segurança de rede](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)
    - [Balanceadores de carga](https://docs.microsoft.com/azure/load-balancer)
    - [IP público](../virtual-network/virtual-network-public-ip-address.md)
    
   Para quaisquer outros componentes de rede, consulte a [documentação](https://docs.microsoft.com/azure/?pivot=products&panel=network)de rede .

    > [!IMPORTANT]
    > Certifique-se de que utiliza um equilibrador de carga redundante no alvo. Pode ler mais nas Zonas de [Equilíbrio de Carga Padrão e Disponibilidade.](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones)

4. [Crie](https://docs.microsoft.com/azure/virtual-network/quick-create-portal) manualmente uma rede de não produção na região alvo se quiser testar a configuração antes de cortar para a região alvo. Recomendamos esta abordagem porque causa uma interferência mínima no ambiente de produção.

## <a name="enable-replication"></a>Ativar a replicação
Os seguintes passos irão guiá-lo ao utilizar a Recuperação do Site Azure para permitir a replicação de dados para a região alvo, antes de os transferir eventualmente para Zonas de Disponibilidade.

> [!NOTE]
> Estes passos são para um único VM. Pode estender o mesmo a vários VMs. Vá ao cofre dos Serviços de Recuperação, selecione **+ Replicate,** e selecione os VMs relevantes em conjunto.

1. No portal Azure, selecione **máquinas Virtuais,** e selecione o VM que pretende mover-se para Zonas de Disponibilidade.
2. Em **Operações**, selecione **Recuperação após desastre**.
3. Na > **região-alvo**de recuperação de **desastres configure,** selecione a região alvo para a qual irá replicar. Certifique-se de que esta região [suporta](https://docs.microsoft.com/azure/availability-zones/az-region) zonas de disponibilidade.

    ![Seleção da região-alvo](media/azure-vms-to-zones/enable-rep-1.PNG)

4. Selecione **Seguinte: Configurações avançadas**.
5. Escolha os valores apropriados para a subscrição alvo, grupo de recursos VM alvo e rede virtual.
6. Na secção **Disponibilidade,** escolha a Zona de Disponibilidade para onde pretende mover o VM. 
   > [!NOTE]
   > Se não vir a opção para o conjunto de disponibilidade ou zona de availabilty, certifique-se de que os [pré-requisitos](#prepare-the-source-vms) são cumpridos e a [preparação](#prepare-the-source-vms) de VMs de origem está completa.
  
    ![Seleções para escolher uma Zona de Disponibilidade](media/azure-vms-to-zones/enable-rep-2.PNG)

7. Selecione **Ativar a replicação**. Esta ação inicia um trabalho para permitir a replicação para o VM.

## <a name="check-settings"></a>Verificar as definições

Após a tarefa de replicação ser concluída, pode ver o estado da replicação, modificar a replicação e testar a implementação.

1. No menu da VM, clique em **Recuperação após desastre**.
2. Pode verificar a saúde da replicação, os pontos de recuperação que foram criados e a fonte, e as regiões-alvo no mapa.

   ![Estado de replicação](media/azure-to-azure-quickstart/replication-status.png)

## <a name="test-the-configuration"></a>Testar a configuração

1. No menu da máquina virtual, selecione **Recuperação de Desastres.**
2. Selecione o ícone Failover do **teste.**
3. No **Teste Failover,** selecione um ponto de recuperação para utilizar para a falha:

   - **Processado mais recentemente**: faz a ativação pós-falha da VM para o ponto de recuperação mais recente processado pelo serviço do Site Recovery. O carimbo de data/hora é apresentado. Com esta opção, não é despendido tempo a processar os dados, pelo que oferece um RTO (objetivo de tempo de recuperação) baixo.
   - **Consistente com a aplicação mais recente**: esta opção faz a ativação pós-falha de todas as VMs para o último ponto de recuperação consistente com a aplicação. O carimbo de data/hora é apresentado.
   - **Personalizado**: selecione qualquer ponto de recuperação.

3. Selecione o alvo de teste Rede virtual Azure para a qual pretende mover os VMs Azure para testar a configuração. 

    > [!IMPORTANT]
    > Recomendamos que utilize uma rede Azure VM separada para a falha de teste e não a rede de produção na região alvo para a qual pretende mover os seus VMs.

4. Para começar a testar o movimento, selecione **OK**. Para acompanhar o progresso, selecione o VM para abrir as suas propriedades. Ou, pode selecionar o trabalho **de Failover** de Teste no nome do cofre > **Empregos** > de**Recuperação do Local**de**Definições** > .
5. Após a conclusão da ativação pós-falha, a VM do Azure de réplica é apresentada no portal do Azure > **Máquinas Virtuais**. Certifique-se de que a VM está em execução, tem as dimensões adequadas e está ligada à rede devida.
6. Se pretender eliminar o VM criado como parte do teste do movimento, **selecione falha** no teste de limpeza no item replicado. Em **Notas,** registe e guarde quaisquer observações associadas ao teste.

## <a name="move-to-the-target-region-and-confirm"></a>Mover-se para a região alvo e confirmar

1.  No menu da máquina virtual, selecione **Recuperação de Desastres.**
2. Selecione o ícone **Failover.**
3. Em **Ativação pós-falha**, selecione **Mais recente**. 
4. Selecione **Encerrar a máquina antes de iniciar a ativação pós-falha**. O Site Recovery tenta encerrar a VM de origem antes de acionar a ativação pós-falha. A ativação pós-falha continua, mesmo que o encerramento falhe. Pode acompanhar o progresso da falha na página **Jobs.** 
5. Depois de terminar o trabalho, verifique se o VM aparece na região-alvo de Azure, como esperado.
6. Em **Itens replicados**, clique com o botão direito do rato na VM > **Consolidar**. Isto termina o processo de mudança para a região alvo. Espere até que o trabalho de compromisso esteja terminado.

## <a name="discard-the-resource-in-the-source-region"></a>Descartar o recurso na região nascente

Vai para a VM. Selecione **Desativar a replicação**. Esta ação interrompe o processo de cópia dos dados para o VM.  

> [!IMPORTANT]
> Faça o passo anterior para evitar ser cobrado para a replicação da Recuperação do Local após a mudança. As definições de replicação da origem são limpas automaticamente. Note que a extensão de recuperação do local que é instalada como parte da replicação não é removida e precisa de ser removida manualmente.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aumentou a disponibilidade de um VM Azure, movendo-se para um conjunto de disponibilidade ou Zona de Disponibilidade. Agora pode definir a recuperação de desastres para o VM movido.

> [!div class="nextstepaction"]
> [Configurar a recuperação após desastre após a migração](azure-to-azure-quickstart.md)


