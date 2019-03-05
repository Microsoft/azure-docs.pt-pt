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
ms.openlocfilehash: ae1e65162ffab35e6d3ef3bf84c1eb6bbe75c65f
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2019
ms.locfileid: "57314301"
---
# <a name="move-azure-vms-to-another-region"></a>Mover VMs do Azure para outra região

Existem vários cenários onde iria querer mover as máquinas de virtuais de IaaS do Azure existentes de uma região para outra - para melhorar a fiabilidade e disponibilidade das suas VMs existentes ou para melhorar a capacidade de gestão ou por razões de governação etc., como detalhadas [aqui](azure-to-azure-move-overview.md). Além de utilizar o [do Azure Site Recovery](site-recovery-overview.md) serviço para gerir e orquestrar a recuperação após desastre de máquinas no local e VMs do Azure para fins de continuidade do negócio e recuperação após desastre (BCDR), também pode utilizar o Site Recuperação para gerir mover VMs do Azure para uma região secundária.       

Este tutorial mostra como mover VMs do Azure para outra região com o Azure Site Recovery. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * [Certifique-se de pré-requisitos](#verify-prerequisites)
> * [Preparar as VMs de origem](#prepare-the-source-vms)
> * [Preparar a região de destino](#prepare-the-target-region)
> * [Copiar dados para a região de destino](#copy-data-to-the-target-region)
> * [Testar a configuração](#test-the-configuration) 
> * [Efetuar a mudança](#perform-the-move-to-the-target-region-and-confirm) 
> * [Eliminar os recursos na região de origem](#discard-the-resource-in-the-source-region) 

> [!IMPORTANT]
> Este documento orienta-o para mover as VMs do Azure de uma região para outra, conforme é, se o requisito melhorar a disponibilidade ao mover as VMs no conjunto de disponibilidade para VMs de afixadas por zona numa região diferente, consulte o tutorial [aqui](move-azure-VMs-AVset-Azone.md).

## <a name="verify-prerequisites"></a>Certifique-se de pré-requisitos

- Certifique-se de que ter as VMs do Azure na região do Azure a partir do qual pretende mover.
- Certifique-se de que se sua escolha de [é suportada a região de origem - combinação da região de destino](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix#region-support) e tomar uma decisão informada sobre a região de destino
- Certifique-se de que compreende a [arquitetura e os componentes do cenário](azure-to-azure-architecture.md).
- Veja os [requisitos e limitações de suporte](azure-to-azure-support-matrix.md).
- Verificar permissões de conta: Se acabou de criar a conta gratuita do Azure, então é o administrador da sua subscrição. Se não for o administrador da subscrição, combine com o administrador para atribuir as permissões que precisa. Para ativar a replicação para uma VM e, essencialmente, copiar dados com o Azure Site Recovery, tem de ter:

    1. Permissões para criar uma VM nos recursos do Azure. A função incorporada "Contribuinte de Máquina Virtual" tem estas permissões, que incluem:
        - Permissão para criar uma VM no grupo de recursos selecionado
        - Permissão para criar uma VM na rede virtual selecionada
        - Permissão para escrever na conta de armazenamento selecionada

    2. Também precisa de permissão para gerir as operações do Azure Site Recovery. A função "Contribuinte do Site Recovery" tem todas as permissões precisas para gerir as operações do Azure Site Recovery num cofre dos Serviços de Recuperação.

## <a name="prepare-the-source-vms"></a>Preparar as VMs de origem

1. Verifique se todos os certificados de raiz mais recentes estão presentes nas VMs do Azure que pretende mover. Se os certificados de raiz mais recentes não estão presentes, a cópia de dados para a região de destino não pode ser ativada devido a restrições de segurança.

    - Para VMs do Windows, instale todas as atualizações mais recentes do Windows na VM, para que todos os certificados de raiz fidedigna fiquem na máquina. Num ambiente desligado, siga o padrão do Windows Update e os processos de atualização de certificados da sua organização.
    - Para VMs do Linux, siga as orientações fornecidas pelo distribuidor do Linux, para obter os certificados de raiz fidedigna mais recentes e a lista de revogação de certificados na VM.
2. Certifique-se de que não estiver a utilizar um proxy de autenticação para controlar a conectividade de rede para as VMs que pretende mover.
3. Se a VM está a tentar mover não tiver acesso à internet são está a utilizar um proxy de firewall para controlar o acesso de saída, verifique os requisitos [aqui](azure-to-azure-tutorial-enable-replication.md#configure-outbound-network-connectivity).
4. Identifique o esquema de rede de origem e de todos os recursos que estão atualmente utilizando - incluindo mas não limitado a balanceadores de carga, NSGs, etc IP público.

## <a name="prepare-the-target-region"></a>Preparar a região de destino

1. Certifique-se de que a sua subscrição do Azure permite criar VMs na região de destino utilizada para recuperação após desastre. Contacte o suporte para ativar a quota necessária.

2. Certifique-se de que a sua subscrição tem recursos suficientes para suportar as VMs com tamanhos que correspondem às VMs de origem. Se estiver a utilizar o Site Recovery para copiar dados para o destino, ele escolhe o mesmo tamanho ou o tamanho mais próximo possível da VM de destino.

3. Certifique-se de que criar um recurso de destino para cada componente identificado no esquema de rede de origem. Isso é importante para se certificar de que, postagens cutting durante para a região de destino, as VMs têm todas as funcionalidades e recursos que tinha na origem.

    > [!NOTE]
    > O Azure Site Recovery Deteta e cria uma rede virtual, quando ativar a replicação para a VM de origem, ou pode também criar previamente uma rede e atribuir à VM no fluxo de utilizador para ativar a replicação automaticamente. Mas para outros recursos, conforme mencionado abaixo, terá de criá-los manualmente na região de destino.

     Consulte os seguintes documentos para criar os recursos de rede mais comumente usada relevantes para si, com base na configuração da VM de origem.

    - [Grupos de segurança de rede](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)
    - [Balanceadores de carga](https://docs.microsoft.com/azure/load-balancer/#step-by-step-tutorials)
    - [IP público](https://docs.microsoft.com/azure/load-balancer/#step-by-step-tutorials)
    
    Para outros componentes de rede, consulte o funcionamento em rede [documentação.](https://docs.microsoft.com/azure/#pivot=products&panel=network) 

4. Manualmente [criar uma rede de não produção](https://docs.microsoft.com/azure/virtual-network/quick-create-portal) na região de destino se pretender testar a configuração antes de executar o desligamento final sobre a região de destino. Isso criará um mínimo interferências com a produção e é recomendado.
    
## <a name="copy-data-to-the-target-region"></a>Copiar dados para a região de destino
Os passos abaixo irão orientá-lo como utilizar o Azure Site Recovery para copiar dados para a região de destino.

### <a name="create-the-vault-in-any-region-except-the-source-region"></a>Crie o cofre em qualquer região, exceto na região de origem.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) > **Serviços de Recuperação**.
2. Clique em **criar um recurso** > **ferramentas de gestão** > **cópia de segurança e recuperação de Site**.
3. No **Nome**, especifique o nome amigável **ContosoVMVault**. Se tiver mais do que uma um. subscrição, selecionar a adequada.
4. Crie um grupo de recursos **ContosoRG**.
5. Selecione uma região do Azure. Para verificar as regiões suportadas, veja a disponibilidade geográfica em [Detalhes dos Preços do Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).
6. Nos cofres de serviços de recuperação, clique em **descrição geral** > **ConsotoVMVault** > **+ replicar**.
7. Em **Origem**, selecione **Azure**.
8. Em **Localização de origem**, selecione a região do Azure de origem onde as VMs estão atualmente a ser executadas.
9. Selecione o modelo de implementação do Resource Manager. Em seguida, selecione o **subscrição de origem** e **grupo de recursos de origem**.
10. Clique em **OK** para guardar as definições.

### <a name="enable-replication-for-azure-vms-and-start-copying-the-data"></a>Ativar a replicação para as VMs do Azure e comece a copiar os dados.

O Site Recovery obtém uma lista das VMs associadas à subscrição e o grupo de recursos.

1. No próximo passo. Selecione a VM que pretende mover. Em seguida, clique em **OK**.
3. Nas **Definições**, clique em **Recuperação após desastre**.
4. Em **Configurar recuperação após desastre** > **Região de destino**, selecione a região de destino para a qual irá replicar.
5. Para este tutorial, aceite as outras predefinições.
6. Clique em **Ativar replicação**. Esta ação inicia uma tarefa para ativar a replicação para a VM.

    ![ativar replicação](media/tutorial-migrate-azure-to-azure/settings.png)

 

## <a name="test-the-configuration"></a>Testar a configuração


1. Navegue para o cofre, no **configurações** > **itens replicados**, clique na máquina Virtual que pretende mover para a região de destino, clique em **+ ativação pós-falha de teste** ícone.
2. Em **Ativação Pós-falha**, selecione um ponto de recuperação para utilizar na ativação pós-falha:

   - **Processado mais recentemente**: Falha da VM através do ponto de recuperação mais recente processado pelo serviço do Site Recovery. O carimbo de data/hora é apresentado. Com esta opção, não é despendido tempo a processar os dados, pelo que oferece um RTO (Objetivo de Tempo de Recuperação) baixo
   - **Mais recente consistente com a aplicação**: Esta opção faz a ativação pós-falha de todas as VMs para o ponto de recuperação consistente com a aplicação mais recente. O carimbo de data/hora é apresentado.
   - **Custom**: Selecione qualquer ponto de recuperação.

3. Selecione o destino do Azure para o qual pretende mover as VMs do Azure para testar a configuração de rede virtual. 

> [!IMPORTANT]
> Recomendamos que utilize uma rede de VM do Azure separada para ativação pós-falha de teste e não a rede de produção para o qual pretende mover as suas VMs, eventualmente, que foi configurada quando ativou a replicação.

4. Para começar a testar a mudança, clique em **OK**. Para acompanhar o progresso, clique em VM para abrir as respetivas propriedades. Também pode clicar na tarefa **Ativação Pós-falha de Teste** no nome do cofre > **Definições** > **Tarefas** > **Tarefas do Site Recovery**.
5. Após a conclusão da ativação pós-falha, a VM do Azure de réplica é apresentada no portal do Azure > **Máquinas Virtuais**. Certifique-se de que a VM está em execução, tem as dimensões adequadas e está ligada à rede devida.
6. Se pretender eliminar a VM criada como parte do teste a mudança, clique em **ativação pós-falha de teste de limpeza** no item replicado. Na **notas**, registe e guarde todas as observações associadas com o teste.

## <a name="perform-the-move-to-the-target-region-and-confirm"></a>Efetuar a mudança para a região de destino e confirme.

1.  Navegue para o cofre, no **configurações** > **itens replicados**, clique na máquina virtual e, em seguida, clique em **ativação pós-falha**.
2. Em **Ativação pós-falha**, selecione **Mais recente**. 
3. Selecione **Encerrar a máquina antes de iniciar a ativação pós-falha**. O Site Recovery tenta encerrar a VM de origem antes de acionar a ativação pós-falha. A ativação pós-falha continua, mesmo que o encerramento falhe. Pode seguir o progresso da ativação pós-falha na página **Trabalhos**. 
4. Quando a tarefa estiver concluída, verifique se a VM aparece a região do Azure de destino conforme esperado.
5. Em **Itens replicados**, clique com o botão direito do rato na VM > **Consolidar**. Isto conclui o processo de mover para a região de destino. Aguarde até que a tarefa de consolidação é concluída.

## <a name="discard-the-resource-in-the-source-region"></a>Eliminar o recurso na região de origem 

1. Navegue para a VM.  Clique em **desative a replicação**.  Isso interromperá o processo de copiar os dados para a VM.  

> [!IMPORTANT]
> É importante efetuar este passo para evitar ser cobrado para a replicação de ASR.

No caso de ter não existem planos para reutilizar qualquer um dos recursos de origem, avance com o próximo conjunto de passos.

1. Avance para eliminar todos os recursos de rede relevantes na região de origem que listei como parte do passo 4 em [preparar as VMs de origem](#prepare-the-source-vms) 
2. Elimine a conta de armazenamento correspondente na região de origem.



## <a name="next-steps"></a>Passos Seguintes

Neste tutorial Moveu uma VM do Azure para uma região do Azure diferente. Agora pode configurar a recuperação de desastres para a VM movida.

> [!div class="nextstepaction"]
> [Configurar a recuperação após desastre após a migração](azure-to-azure-quickstart.md)

