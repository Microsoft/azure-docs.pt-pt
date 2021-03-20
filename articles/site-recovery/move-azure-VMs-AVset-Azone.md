---
title: Mover VMs para uma região do Azure com zonas de disponibilidade usando a recuperação do site Azure
description: Saiba como mover VMs para uma zona de disponibilidade numa região diferente com recuperação de site
services: site-recovery
author: sideeksh
ms.service: site-recovery
ms.topic: tutorial
ms.date: 01/28/2019
ms.author: sideeksh
ms.custom: MVC
ms.openlocfilehash: 8224ae4a48bb4915492240c414b90edb86a4c258
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "93393137"
---
# <a name="move-azure-vms-into-availability-zones"></a>Mover VMs Azure em Zonas de Disponibilidade

Estes artigos descrevem como mover VMs Azure para uma zona de disponibilidade em uma região diferente. Se quiser mudar-se para uma zona diferente na mesma região, [reveja este artigo.](./azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery.md)


As Zonas de Disponibilidade no Azure ajudam a proteger as suas aplicações e dados contra falhas no datacenter. Cada Zona de Disponibilidade é constituída por um ou mais datacenters equipados com energia, refrigeração e redes independentes. Para garantir a resiliência, há um mínimo de três zonas separadas em todas as regiões ativadas. A separação física das Zonas de Disponibilidade dentro de uma região ajuda a proteger aplicações e dados de falhas no datacenter. Com Zonas de Disponibilidade, o Azure oferece um contrato de nível de serviço (SLA) de 99,99% para o tempo de uptime das máquinas virtuais (VMs). As Zonas de Disponibilidade são suportadas em regiões selecionadas, como referido nas [Regiões que suportam Zonas de Disponibilidade.](../availability-zones/az-region.md)

Num cenário em que os seus VMs são implantados como *uma instância única* numa região específica, e pretende melhorar a sua disponibilidade movendo estes VMs para uma Zona de Disponibilidade, pode fazê-lo utilizando a Recuperação do Site Azure. Esta ação pode ainda ser categorizada em:

- Mover VMs de instância única em zonas de disponibilidade numa região-alvo
- Mover VMs em uma disponibilidade definida em Zonas de Disponibilidade numa região alvo

> [!IMPORTANT]
> Para mover os VMs Azure para uma zona de disponibilidade numa região diferente, recomendamos agora a utilização do [Azure Resource Mover](../resource-mover/move-region-availability-zone.md). O Resource Mover está em pré-visualização pública e fornece:
> - Um único centro para movimentar recursos através das regiões.
> - Tempo de movimento reduzido e complexidade. Tudo o que precisa está num único local.
> - Uma experiência simples e consistente para mover diferentes tipos de recursos Azure.
> - Uma maneira fácil de identificar dependências entre recursos que quer mover. Isto ajuda-o a mover recursos relacionados em conjunto, para que tudo funcione como esperado na região alvo, após a mudança.
> - Limpeza automática dos recursos na região de origem, se quiser eliminá-los após a mudança.
> - A testar. Podes experimentar um movimento, e depois deitá-lo fora se não quiseres fazer um movimento completo.



## <a name="check-prerequisites"></a>Verificar pré-requisitos

- Verifique se a região alvo tem [suporte para Zonas de Disponibilidade.](../availability-zones/az-region.md) Verifique se a sua escolha da [combinação região de origem/região-alvo é suportada](./azure-to-azure-support-matrix.md#region-support). Tome uma decisão informada sobre a região alvo.
- Certifique-se de que compreende a [arquitetura e os componentes do cenário](azure-to-azure-architecture.md).
- Veja os [requisitos e limitações de suporte](azure-to-azure-support-matrix.md).
- Verifique as permissões da conta. Se acabou de criar a sua conta Azure gratuita, é o administrador da sua subscrição. Se não é o administrador de subscrição, trabalhe com o administrador para atribuir as permissões de que necessita. Para permitir a replicação de um VM e, eventualmente, copiar dados para o alvo utilizando a Recuperação do Site Azure, deve ter:

    1. Permissão para criar um VM em recursos Azure. O *papel integrado do Contribuinte De Máquina Virtual* tem estas permissões, que incluem:
        - Permissão para criar uma VM no grupo de recursos selecionado
        - Permissão para criar uma VM na rede virtual selecionada
        - Permissão para escrever na conta de armazenamento selecionada

    2. Permissão para gerir tarefas de Recuperação do Site Azure. A função *de Contribuinte de Recuperação de Sítios* tem todas as permissões necessárias para gerir as ações de Recuperação do Local num cofre dos Serviços de Recuperação.

## <a name="prepare-the-source-vms"></a>Preparar os VMs de origem

1. Os seus VMs devem utilizar discos geridos se quiser movê-los para uma Zona de Disponibilidade utilizando a Recuperação do Site. Pode converter VMs do Windows existentes que utilizam discos não geridos para utilizar discos geridos. Siga os passos na [Conversão de uma máquina virtual windows de discos não geridos para discos geridos](../virtual-machines/windows/convert-unmanaged-to-managed-disks.md). Certifique-se de que o conjunto de disponibilidade está configurado conforme *gerido*.
2. Verifique se todos os certificados de raiz mais recentes estão presentes nos VMs Azure que pretende mover. Se os certificados de raiz mais recentes não estiverem presentes, a cópia de dados para a região alvo não pode ser ativada devido a restrições de segurança.

3. Para VMs do Windows, instale todas as atualizações mais recentes do Windows na VM, para que todos os certificados de raiz fidedigna fiquem na máquina. Num ambiente desligado, siga os processos padrão de atualização de atualização do Windows e de atualização de certificados para a sua organização.

4. Para os VMs Linux, siga as orientações fornecidas pelo seu distribuidor Linux para obter os mais recentes certificados de raiz fidedignos e lista de revogação de certificados no VM.
5. Certifique-se de que não utiliza um representante de autenticação para controlar a conectividade da rede para VMs que pretende mover.

6. Verifique os [requisitos de conectividade de saída para os VMs](azure-to-azure-tutorial-enable-replication.md#set-up-vm-connectivity).

7. Identifique o layout de rede de origem e os recursos que utiliza atualmente para verificação, incluindo equilibradores de carga, NSGs e IP público.

## <a name="prepare-the-target-region"></a>Preparar a região alvo

1. Verifique se a sua subscrição Azure permite criar VMs na região-alvo utilizada para a recuperação de desastres. Se necessário, contacte o suporte para ativar a quota exigida.

2. Certifique-se de que a sua subscrição tem recursos suficientes para suportar as VMs com tamanhos que correspondem às VMs de origem. Se utilizar a Recuperação do Site para copiar dados para o alvo, ele escolhe o mesmo tamanho ou o tamanho mais próximo possível para o VM alvo.

3. Crie um recurso-alvo para cada componente identificado no layout de rede de origem. Esta ação garante que depois de cortar para a região alvo, os seus VMs têm todas as funcionalidades e funcionalidades que tinha na fonte.

    > [!NOTE]
    > A Azure Site Recovery descobre e cria automaticamente uma conta de rede e armazenamento virtual quando ativa a replicação para o VM de origem. Também pode pré-criar estes recursos e atribuir ao VM como parte do passo de replicação de ativação. Mas para quaisquer outros recursos, como mencionado mais tarde, é necessário criá-los manualmente na região alvo.

     Os seguintes documentos dizem como criar os recursos de rede mais utilizados que são relevantes para si, com base na configuração VM de origem.

    - [Grupos de segurança de rede](../virtual-network/manage-network-security-group.md)
    - [Balanceadores de carga](../load-balancer/index.yml)
    - [IP público](../virtual-network/virtual-network-public-ip-address.md)
    
   Para quaisquer outros componentes de rede, consulte a [documentação](../index.yml?pivot=products&panel=network)de networking .

    > [!IMPORTANT]
    > Certifique-se de que utiliza um equilibrador de carga redundante de zona no alvo. Pode ler mais em [Zonas Padrão de Balanceador de Carga e Disponibilidade.](../load-balancer/load-balancer-standard-availability-zones.md)

4. Crie manualmente [uma rede de não produção](../virtual-network/quick-create-portal.md) na região alvo se quiser testar a configuração antes de cortar para a região alvo. Recomendamos esta abordagem porque causa uma interferência mínima com o ambiente de produção.

## <a name="enable-replication"></a>Ativar a replicação
Os seguintes passos irão guiá-lo quando utilizar a Recuperação do Site Azure para permitir a replicação de dados para a região alvo, antes de os transferir para zonas de disponibilidade.

> [!NOTE]
> Estes passos são para um único VM. Pode estender o mesmo a vários VMs. Vá ao cofre dos Serviços de Recuperação, selecione **+ Replica,** e selecione os VMs relevantes juntos.

1. No portal Azure, selecione **máquinas Virtuais** e selecione o VM que pretende mover-se para Zonas de Disponibilidade.
2. Em **Operações**, selecione **Recuperação após desastre**.
3. Em **Configurar recuperação após desastre** > **Região de destino**, selecione a região de destino para a qual irá replicar. Certifique-se de que esta região [suporta Zonas](../availability-zones/az-region.md) de Disponibilidade.
4. Selecione **Seguinte: Definições avançadas**.
5. Escolha os valores adequados para a subscrição-alvo, o grupo de recursos VM alvo e a rede virtual.
6. Na secção **Disponibilidade,** escolha a Zona de Disponibilidade na qual pretende mover o VM. 
   > [!NOTE]
   > Se não vir a opção para disponibilidade definida ou Zona de Availabilty, certifique-se de que os [pré-requisitos](#prepare-the-source-vms) são cumpridos e a [preparação](#prepare-the-source-vms) de VMs de origem está completa.
  

7. Selecione **Ativar a replicação**. Esta ação inicia um trabalho para permitir a replicação para o VM.

## <a name="check-settings"></a>Verificar definições

Após a tarefa de replicação ser concluída, pode ver o estado da replicação, modificar a replicação e testar a implementação.

1. No menu da VM, clique em **Recuperação após desastre**.
2. Pode verificar a saúde da replicação, os pontos de recuperação que foram criados e a fonte, e as regiões-alvo no mapa.


## <a name="test-the-configuration"></a>Teste a configuração.

1. No menu de máquinas virtuais, selecione  **Recuperação de Desastres**.
2. Selecione o ícone **de failover de teste.**
3. No **Teste Failover**, selecione um ponto de recuperação para a falha:

   - **Processado mais recentemente**: faz a ativação pós-falha da VM para o ponto de recuperação mais recente processado pelo serviço do Site Recovery. O carimbo de data/hora é apresentado. Com esta opção, não é despendido tempo a processar os dados, pelo que oferece um RTO (objetivo de tempo de recuperação) baixo.
   - **Consistente com a aplicação mais recente**: esta opção faz a ativação pós-falha de todas as VMs para o último ponto de recuperação consistente com a aplicação. O carimbo de data/hora é apresentado.
   - **Personalizado**: selecione qualquer ponto de recuperação.

3. Selecione o alvo de teste Azure rede virtual para a qual pretende mover os VMs Azure para testar a configuração. 

    > [!IMPORTANT]
    > Recomendamos que utilize uma rede Azure VM separada para a falha de teste e não a rede de produção na região-alvo para a qual pretende mover os seus VMs.

4. Para começar a testar o movimento, selecione **OK**. Para acompanhar o progresso, selecione o VM para abrir as suas propriedades. Ou, pode selecionar o trabalho **de Falha de Teste** no nome do cofre > Trabalhos de Recuperação do Local de   >  **Trabalhos** de  >  **Definições**.
5. Após a conclusão da ativação pós-falha, a VM do Azure de réplica é apresentada no portal do Azure > **Máquinas Virtuais**. Certifique-se de que a VM está em execução, tem as dimensões adequadas e está ligada à rede devida.
6. Se pretender eliminar o VM criado como parte do teste do movimento, selecione a falha do **teste de limpeza** no item replicado. Em **Notas,** registem e guarde quaisquer observações associadas ao teste.

## <a name="move-to-the-target-region-and-confirm"></a>Mova-se para a região alvo e confirme

1.  No menu de máquinas virtuais, selecione  **Recuperação de Desastres**.
2. Selecione o ícone **Failover.**
3. Em **Ativação pós-falha**, selecione **Mais recente**. 
4. Selecione **Encerrar a máquina antes de iniciar a ativação pós-falha**. O Site Recovery tenta encerrar a VM de origem antes de acionar a ativação pós-falha. A ativação pós-falha continua, mesmo que o encerramento falhe. Pode acompanhar o progresso falhado na página **Jobs.** 
5. Após o fim do trabalho, verifique se o VM aparece na região de Azure alvo, como esperado.
6. Em **Itens replicados**, clique com o botão direito do rato na VM > **Consolidar**. Isto termina o processo de mudança para a região alvo. Espere até que o trabalho de compromisso esteja terminado.

## <a name="discard-the-resource-in-the-source-region"></a>Deite fora o recurso na região de origem

Vai ao VM. Selecione **a replicação de desativação**. Esta ação impede o processo de cópia dos dados para o VM.  

> [!IMPORTANT]
> Faça o passo anterior para evitar ser carregado para a replicação da Recuperação do Local após o movimento. As definições de replicação da origem são limpas automaticamente. Note que a extensão de Recuperação do Local que é instalada como parte da replicação não é removida e precisa de ser removida manualmente.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aumentou a disponibilidade de um VM Azure mudando-se para um conjunto de disponibilidade ou Zona de Disponibilidade. Agora podes preparar a recuperação de desastres para o VM em movido.

> [!div class="nextstepaction"]
> [Configurar a recuperação após desastre após a migração](azure-to-azure-quickstart.md)
