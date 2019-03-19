---
title: Mover VMs de IaaS do Azure para outra região do Azure com o serviço Azure Site Recovery | Documentos da Microsoft
description: Utilize o Azure Site Recovery para mover VMs de IaaS do Azure de uma região do Azure para outra.
services: site-recovery
author: rajani-janaki-ram
ms.service: site-recovery
ms.topic: tutorial
ms.date: 01/28/2019
ms.author: rajanaki
ms.custom: MVC
ms.openlocfilehash: 7619b8831d75ce639c6f6c773c7c7d491abc93e7
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58122034"
---
# <a name="move-azure-vms-to-another-region"></a>Mover VMs do Azure para outra região

Existem vários cenários em que gostaria de mover as máquinas de virtuais de IaaS do Azure (VMs) existentes de uma região para outra. Por exemplo, pretende melhorar a fiabilidade e disponibilidade das suas VMs existentes, para melhorar a capacidade de gestão ou para mover por motivos de governação. Para obter mais informações, consulte a [descrição geral de mover de VM do Azure](azure-to-azure-move-overview.md). 

Pode utilizar o [do Azure Site Recovery](site-recovery-overview.md) serviço para gerir e orquestrar a recuperação de desastre de máquinas no local e VMs do Azure para o negócio continuidade e recuperação após desastre (BCDR). Também pode utilizar o Site Recovery para gerir a migração de VMs do Azure para uma região secundária.

Neste tutorial, irá:

> [!div class="checklist"]
> 
> * Certifique-se de pré-requisitos para a migração
> * Preparar as VMs de origem e a região de destino
> * Copie os dados e ativar a replicação
> * Testar a configuração e efetuar a mudança
> * Eliminar os recursos na região de origem
> 
> [!NOTE]
> Este tutorial mostra como mover VMs do Azure de uma região para outro como está. Se precisar de melhorar a disponibilidade ao mover as VMs no conjunto de disponibilidade para a zona afixado VMs numa região diferente, consulte a [mover as VMs do Azure para o tutorial de zonas de disponibilidade](move-azure-vms-avset-azone.md).

## <a name="prerequisites"></a>Pré-requisitos

- Certifique-se de que as VMs do Azure são na região do Azure a partir do qual pretende mover.
- Certifique-se de que sua escolha de [é suportada a região de origem - combinação da região de destino](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix#region-support)e tomar uma decisão informada sobre a região de destino.
- Certifique-se de que compreende a [arquitetura e os componentes do cenário](azure-to-azure-architecture.md).
- Veja os [requisitos e limitações de suporte](azure-to-azure-support-matrix.md).
- Verifique as permissões de conta. Se tiver criado a sua conta gratuita do Azure, é o administrador da sua subscrição. Se não for o administrador da subscrição, combine com o administrador para atribuir as permissões que precisa. Para ativar a replicação para uma VM e, essencialmente, copiar dados com o Azure Site Recovery, tem de ter:

    * Permissões para criar uma VM nos recursos do Azure. A função incorporada de Contribuidor de Máquina Virtual tem estas permissões, que incluem:
        - Permissão para criar uma VM no grupo de recursos selecionado
        - Permissão para criar uma VM na rede virtual selecionada
        - Permissão para escrever na conta de armazenamento selecionada

    * Permissões para gerir as operações do Azure Site Recovery. A função de contribuinte do Site Recovery tem todas as permissões que são necessárias para gerir as operações do Site Recovery num cofre dos serviços de recuperação.

## <a name="prepare-the-source-vms"></a>Preparar as VMs de origem

1. Certifique-se de que todos os certificados de raiz mais recentes estão nas VMs do Azure que pretende mover. Se os certificados de raiz mais recentes não estiverem presentes na VM, as restrições de segurança irão impedir que a cópia de dados para a região de destino.

    - Para VMs do Windows, instale todas as atualizações mais recentes do Windows na VM, para que todos os certificados de raiz fidedigna fiquem na máquina. Num ambiente desligado, siga o padrão do Windows Update e os processos de atualização de certificados da sua organização.
    - Para VMs do Linux, siga as orientações fornecidas pelo distribuidor do Linux para obter os certificados de raiz fidedigna mais recentes e a lista de revogação de certificado na VM.
1. Certifique-se de que não está a utilizar um proxy de autenticação para controlar a conectividade de rede para as VMs que pretende mover.
1. Se a VM que está a tentar mover não tem acesso à internet ou se está a utilizar um proxy de firewall para controlar o acesso de saída [Verifique os requisitos](azure-to-azure-tutorial-enable-replication.md#configure-outbound-network-connectivity).
1. Identifique o esquema de rede de origem e de todos os recursos que está a utilizar atualmente. Isto inclui, mas não está limitado a balanceadores de carga, de rede (NSGs), os grupos de segurança e IPs públicos.

## <a name="prepare-the-target-region"></a>Preparar a região de destino

1. Certifique-se de que a sua subscrição do Azure permite-lhe criar VMs na região de destino que é utilizada para recuperação após desastre. Contacte o suporte para ativar a quota necessária.

1. Certifique-se de que a sua subscrição tem recursos suficientes para suportar as VMs com tamanhos que correspondem às VMs de origem. Se estiver a utilizar o Site Recovery para copiar dados para o destino, o Site Recovery escolhe o mesmo tamanho ou o tamanho mais próximo possível da VM de destino.

1. Certifique-se de que crie um recurso de destino para cada componente que esteja identificado no esquema de rede de origem. Este passo é importante certificar-se de que as VMs têm todas as funcionalidades e funções na região de destino que tinha na região de origem.

    > [!NOTE]
    > O Azure Site Recovery Deteta e cria uma rede virtual, quando ativa a replicação para a VM de origem automaticamente. Também pode criar previamente uma rede e atribua-o para a VM no fluxo de utilizador para ativar a replicação. Conforme mencionado mais tarde, terá de criar manualmente todos os outros recursos na região de destino.

     Para criar mais comumente usado os recursos de rede que são relevantes para si com base na configuração da VM de origem, consulte a seguinte documentação:

   - [Grupos de segurança de rede](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)
   - [Balanceadores de carga](https://docs.microsoft.com/azure/load-balancer/#step-by-step-tutorials)
   - [IP público](https://docs.microsoft.com/azure/load-balancer/#step-by-step-tutorials)
    
     Para outros componentes de rede, consulte a [documentação de rede](https://docs.microsoft.com/azure/#pivot=products&panel=network).

1. Manualmente [criar uma rede de não produção](https://docs.microsoft.com/azure/virtual-network/quick-create-portal) na região de destino se pretender testar a configuração antes de efetuar a transição final para a região de destino. Recomendamos que este passo porque ela garante o mínimo interferências com a rede de produção.

## <a name="copy-data-to-the-target-region"></a>Copiar dados para a região de destino
Os passos seguintes mostram como utilizar o Azure Site Recovery para copiar dados para a região de destino.

### <a name="create-the-vault-in-any-region-except-the-source-region"></a>Crie o Cofre na região que pretenderem, exceto a região de origem

1. Inicie sessão no [portal do Azure](https://portal.azure.com) > **Serviços de Recuperação**.
1. Selecione **criar um recurso** > **ferramentas de gestão** > **cópia de segurança e recuperação de Site**.
1. No **Nome**, especifique o nome amigável **ContosoVMVault**. Se tiver mais do que uma subscrição, selecione a que for adequada.
1. Criar o grupo de recursos **ContosoRG**.
1. Selecione uma região do Azure. Para verificar as regiões suportadas veja disponibilidade geográfica em [os detalhes dos preços do Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).
1. Na **cofres dos serviços de recuperação**, selecione **descrição geral** > **ContosoVMVault** > **+ replicar**.
1. Em **Origem**, selecione **Azure**.
1. Em **Localização de origem**, selecione a região do Azure de origem onde as VMs estão atualmente a ser executadas.
1. Selecione o modelo de implementação do Resource Manager. Em seguida, selecione o **subscrição de origem** e **grupo de recursos de origem**.
1. Selecione **OK** para guardar as definições.

### <a name="enable-replication-for-azure-vms-and-start-copying-the-data"></a>Ativar a replicação para as VMs do Azure e começar a copiar os dados

O site Recovery obtém uma lista de VMs que estão associadas a subscrição e grupo de recursos.

1. No próximo passo, selecione a VM que pretende mover, em seguida, selecione **OK**.
1. Na **configurações**, selecione **recuperação após desastre**.
1. Na **configurar a recuperação após desastre** > **região de destino**, selecione a região de destino ao qual irá replicar.
1. Para este tutorial, aceite as outras predefinições.
1. Selecione **ativar a replicação**. Este passo inicia uma tarefa para ativar a replicação para a VM.

    ![Ativar a replicação](media/tutorial-migrate-azure-to-azure/settings.png)

## <a name="test-the-configuration"></a>Testar a configuração

1. Vá para o cofre. Na **configurações** > **itens replicados**, selecione a VM que pretende mover para a região de destino e, em seguida, selecione **+ ativação pós-falha de teste**.
1. Na **ativação pós-falha de teste**, selecione um ponto de recuperação a utilizar para a ativação pós-falha:

   - **Processado mais recentemente**: Falha da VM através do ponto de recuperação mais recente processado pelo serviço do Site Recovery. O carimbo de data/hora é apresentado. Com esta opção, não é despendido tempo a processar dados, pelo que oferece um objetivo de tempo de recuperação (RTO) baixas.
   - **Mais recente consistente com a aplicação**: Esta opção faz a ativação pós-falha de todas as VMs para o ponto de recuperação consistente com a aplicação mais recente. O carimbo de data/hora é apresentado.
   - **Custom**: Selecione qualquer ponto de recuperação.

1. Selecione o destino do Azure para o qual pretende mover as VMs do Azure para testar a configuração de rede virtual.
    > [!IMPORTANT]
    > Recomendamos que utilize uma rede de VM do Azure separada para ativação pós-falha de teste. Não utilize a rede de produção que foi configurada quando ativou a replicação e de que pretende mover as suas VMs para, eventualmente.
1. Para começar a testar a mudança, clique em **OK**. Para acompanhar o progresso, clique em VM para abrir as respetivas propriedades. Também pode clicar na tarefa **Ativação Pós-falha de Teste** no nome do cofre > **Definições** > **Tarefas** > **Tarefas do Site Recovery**.
1. Após a conclusão da ativação pós-falha, a VM do Azure de réplica é apresentada no portal do Azure > **Máquinas Virtuais**. Certifique-se de que a VM está em execução, é o tamanho adequado e está ligada à rede pretendida.
1. Se pretender eliminar tha a VM foi criada como parte do teste a mudança, clique em **ativação pós-falha de teste de limpeza** no item replicado. Na **notas**, registe e guarde todas as observações associadas com o teste.

## <a name="perform-the-move-to-the-target-region-and-confirm"></a>Efetuar a mudança para a região de destino e confirme

1. Vá para o cofre. Na **configurações** > **itens replicados**, selecione a VM e, em seguida, selecione **ativação pós-falha**.
1. Em **Ativação pós-falha**, selecione **Mais recente**.
1. Selecione **Encerrar a máquina antes de iniciar a ativação pós-falha**. O Site Recovery tenta encerrar a VM de origem antes de acionar a ativação pós-falha. A ativação pós-falha continua, mesmo que o encerramento falhe. Pode seguir o progresso da ativação pós-falha na página **Trabalhos**.
1. Depois da tarefa estiver concluída, verifique se a VM aparece a região do Azure de destino conforme esperado.
1. Na **itens replicados**, à direita-selecione a VM > **consolidar**. Este passo conclui o processo de mover para a região de destino. Aguarde até que a tarefa de consolidação é concluída.

## <a name="delete-the-resource-in-the-source-region"></a>Eliminar o recurso na região de origem

Vá para a VM. Selecione **desative a replicação**. Este passo para o processo de copiar os dados para a VM.

> [!IMPORTANT]
> É importante efetuar este passo para evitar a ser cobrado por replicação do Azure Site Recovery.

Se tiver não existem planos para reutilizar qualquer um dos recursos de origem, siga estes passos:

1. Eliminar todos os recursos de rede relevantes na região de origem que listei como parte do passo 4 [preparar as VMs de origem](#prepare-the-source-vms).
1. Elimine a conta de armazenamento correspondente na região de origem.

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, Moveu uma VM do Azure para uma região do Azure diferente. Agora pode configurar a recuperação de desastres para a VM que moveu.

> [!div class="nextstepaction"]
> [Configurar a recuperação após desastre após a migração](azure-to-azure-quickstart.md)

