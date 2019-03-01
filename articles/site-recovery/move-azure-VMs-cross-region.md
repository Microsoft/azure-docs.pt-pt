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
ms.openlocfilehash: 0f73e68fd0c01d4323e8675d3fa12f7ca1051cdb
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/01/2019
ms.locfileid: "57192927"
---
# <a name="move-azure-vms-to-another-region"></a>Mover VMs do Azure para outra região

Pode querer mover a infraestrutura do Azure como máquinas de virtuais do serviço (IaaS) de uma região para outro, para melhorar a fiabilidade, disponibilidade, gestão ou governação. Este tutorial mostra como mover VMs para outra região com o Azure Site Recovery. Vai aprender a:

> [!div class="checklist"]
> * Verificar os pré-requisitos
> * Preparar as VMs de origem
> * Preparar a região de destino
> * Copiar dados para a região de destino
> * Testar a configuração
> * Efetuar a mudança
> * Eliminar os recursos a partir da região de origem


> [!IMPORTANT]
> Este artigo descreve como mover VMs do Azure de uma região para outra *conforme é*. Se o seu objetivo é melhorar a disponibilidade da sua infraestrutura ao mover VMs para as zonas de disponibilidade, consulte [mover as VMs do Azure para zonas de disponibilidade](move-azure-vms-avset-azone.md).

## <a name="prerequisites"></a>Pré-requisitos

- Certifique-se de que tem VMs do Azure na região do Azure que pretende mover origem *partir*.
- Certifique-se de que sua escolha de [combinação de região de destino da região de origem é suportada](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix#region-support)e escolher cuidadosamente a região de destino.
- Certifique-se de que compreende a [arquitetura e os componentes do cenário](azure-to-azure-architecture.md).
- Veja os [requisitos e limitações de suporte](azure-to-azure-support-matrix.md).
- Verifique as permissões de conta. Se acabou de criar sua conta gratuita do Azure, *são* o administrador da sua subscrição. Se não o administrador, trabalhe com o administrador para obter as permissões que tem de:
  -  Para ativar a replicação para uma VM e cópia de dados para o destino com o Site Recovery, tem de ter permissões para criar uma VM nos recursos do Azure. A função incorporada Contribuinte de Máquina Virtual tem estas permissões. Com as permissões, pode:
        - Criar uma VM no grupo de recursos selecionado.
        - Criar uma VM na rede virtual selecionada.
        - Escrever na conta de armazenamento selecionada.

  - Também precisa de permissões para gerir as operações de recuperação de sites. A função de contribuinte do Site Recovery tem todas as permissões que são necessárias para gerir as operações do Site Recovery num cofre dos serviços de recuperação do Azure.

## <a name="prepare-the-source-vms"></a>Preparar as VMs de origem

1. Certifique-se de que as VMs do Azure que pretende mover tem os certificados de raiz mais recentes. Caso contrário, não é possível ativar a cópia de dados para a região de destino devido a restrições de segurança.

    - Para VMs do Windows, instale as atualizações mais recentes do Windows para que todos os certificados de raiz fidedigna fiquem na máquina. Num ambiente desligado, siga os processos do Windows Update e atualização do certificado standard para a sua organização.
    - Para VMs do Linux, siga a orientação do distribuidor do Linux para obter as mais recentes certificados de raiz fidedigna e a lista de revogação de certificados.
2. Certifique-se de que não está a utilizar um proxy de autenticação para controlar a conectividade de rede para as VMs que planeia mover.
3. Se uma VM que pretende mover não tem acesso à internet e está a utilizar um proxy de firewall para acesso de saída do controle, verifique os [requisitos](azure-to-azure-tutorial-enable-replication.md#configure-outbound-network-connectivity).
4. Documente o esquema de rede de origem e de todos os recursos que está atualmente a utilizar, incluindo (mas não limitado a) balanceadores de carga, grupos de segurança de rede e endereços IP públicos para verificação.

## <a name="prepare-the-target-region"></a>Preparar a região de destino

1. Na sua subscrição do Azure, certifique-se de que pode criar VMs na região de destino que é utilizada para recuperação após desastre. Contacte o suporte para ativar a quota necessária, se necessário.

2. Certifique-se de que a sua subscrição tem recursos suficientes para suportar as VMs de origem. Se estiver a utilizar o Site Recovery para copiar dados para o destino, ele escolhe o mesmo tamanho ou o tamanho disponível mais próximo para VMs de destino.

3. Certifique-se de que crie um recurso de destino para cada componente que identificou no esquema de rede de origem. Isto garante que as VMs tenham todas as funcionalidades e funções na região de destino que eles tinham na região de origem.

   O Azure Site Recovery Deteta e cria uma conta de armazenamento e rede virtual, quando ativar a replicação para a VM de origem automaticamente. Também pode criar previamente estes recursos e atribuí-los para a VM como parte do passo ativar a replicação. Mas tem de criar manualmente todos os outros recursos na região de destino. Consulte os seguintes documentos para criar os recursos de rede mais comumente usada com base na sua configuração de VM de origem:

   - [Grupos de segurança de rede](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)
   - [Balanceadores de carga](https://docs.microsoft.com/azure/load-balancer/#step-by-step-tutorials)
   - [IP público](https://docs.microsoft.com/azure/load-balancer/#step-by-step-tutorials)
    
   Para outros componentes de rede, consulte a [documentação de rede do Azure](https://docs.microsoft.com/azure/#pivot=products&panel=network). 

4. Para testar a configuração antes de efetuar manualmente a movimentação [criar uma rede de não produção](https://docs.microsoft.com/azure/virtual-network/quick-create-portal) na região de destino. Teste a configuração cria a interferência mínima com o ambiente de produção e o recomendamos.
    
## <a name="copy-data-to-the-target-region"></a>Copiar dados para a região de destino
Os passos seguintes utilizam o Azure Site Recovery para copiar dados para a região de destino.

### <a name="create-the-vault-in-any-region-except-the-source"></a>Crie o cofre em qualquer região, exceto a origem

1. Inicie sessão no [portal do Azure](https://portal.azure.com) > **Serviços de Recuperação**.
2. Selecione **criar um recurso** > **ferramentas de gestão** > **cópia de segurança e recuperação de Site**.
3. Para **Name**, especifique o nome amigável **ContosoVMVault**. Se tiver mais do que uma subscrição, selecione a que for adequada.
4. Crie um grupo de recursos **ContosoRG**.
5. Selecione uma região do Azure. Para verificar as regiões suportadas, veja [detalhes de preços do Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).
6. Para cofres dos serviços de recuperação, selecione **descrição geral** > **ConsotoVMVault** > **+ replicar**.
7. Para **origem**, selecione **Azure**.
8. Para **localização de origem**, selecione a região do Azure, onde atualmente em execução as suas VMs de origem.
9. Selecione o modelo de implementação Azure Resource Manager. Em seguida, selecione o **subscrição de origem** e **grupo de recursos de origem**.
10. Selecione **OK** para guardar as definições.

### <a name="enable-replication-for-azure-vms-and-start-copying-the-data"></a>Ativar a replicação para as VMs do Azure e começar a copiar os dados

O site Recovery obtém uma lista de VMs que estão associadas a subscrição e grupo de recursos.

1. Selecione a VM que pretende mover e, em seguida, selecione **OK**.
2. Para **configurações**, selecione **recuperação após desastre**.
3. Para **configurar a recuperação após desastre** > **região de destino**, selecione a região de destino que está a replicar para.
4. Opte por utilizar os recursos de destino predefinido ou aqueles que criou previamente.
5. Selecione **ativar a replicação** para iniciar a tarefa.

   ![Ativar a replicação](media/tutorial-migrate-azure-to-azure/settings.png)

 

## <a name="test-the-configuration"></a>Testar a configuração


1. Vá para o cofre. Na **configurações** > **itens replicados**, selecione a máquina virtual que pretende mover para a região de destino. Em seguida, selecione **ativação pós-falha de teste**.
2. Na **ativação pós-falha de teste**, selecione um ponto de recuperação a utilizar para a ativação pós-falha:

   - **Processado mais recentemente**: Falha da VM através do ponto de recuperação mais recente processado pelo serviço do Site Recovery. O carimbo de data/hora é apresentado. Não é despendido tempo a processar dados, pelo que esta opção fornece um objetivo de tempo de recuperação (RTO).
   - **Mais recente consistente com a aplicação**: Efetua a ativação pós-falha de todas as VMs para o ponto de recuperação consistente com a aplicação mais recente. O carimbo de data/hora é apresentado.
   - **Custom**: Selecione qualquer ponto de recuperação.

3. Selecione o destino do Azure para o qual pretende mover as VMs do Azure para testar a configuração de rede virtual.

   > [!IMPORTANT]
   > Recomendamos que utilize uma rede de VM do Azure separada para ativação pós-falha de teste, não a rede de produção na região de destino.

4. Para começar a testar a mudança, selecione **OK**. Para controlar o progresso, selecione a VM para abrir o respetivo **propriedades.** Em alternativa, selecione o **ativação pós-falha de teste** tarefa no cofre. Em seguida, selecione **configurações** > **tarefas** > **tarefas do Site Recovery**.
5. Após a conclusão da ativação pós-falha, a VM do Azure de réplica é apresentada no portal do Azure > **Máquinas Virtuais**. Certifique-se de que a VM está em execução, tem as dimensões adequadas e está ligada à rede devida.
6. Para eliminar a VM que criou para fins de teste, selecione **ativação pós-falha de teste de limpeza** no item replicado. Partir **notas**, registe e guarde todas as observações relacionados com o teste.

## <a name="perform-the-move-and-confirm"></a>Efetuar a mudança e confirmar

1. Vá para o Cofre na **configurações** > **itens replicados**, selecione a máquina virtual e, em seguida, selecione **ativação pós-falha**.
1. Para **ativação pós-falha**, selecione **mais recente**. 
2. Selecione **Encerrar a máquina antes de iniciar a ativação pós-falha**. Site Recovery tenta encerrar a VM de origem antes de acionar a ativação pós-falha. Mas a ativação pós-falha continua, mesmo que o encerramento falhe. Pode seguir o progresso da ativação pós-falha na página **Trabalhos**.
3. Quando a tarefa estiver concluída, verifique se a VM aparece a região do Azure de destino conforme esperado.
4. Na **itens replicados**, a VM com o botão direito e selecione **consolidar**. Isto conclui a migração. Aguarde até que a tarefa de consolidação é concluída.

## <a name="discard-the-resources-from-the-source-region"></a>Eliminar os recursos a partir da região de origem

- Vá para a VM e selecione **desativar replicação**. Isso interromperá o processo de copiar os dados para a VM.

  > [!IMPORTANT]
  > Conclua este passo para evitar a ser cobrado por replicação da recuperação de Site após a mudança.

Se não planeja reutilizar qualquer um dos recursos de origem, siga estes passos:

1. Eliminar todos os recursos de rede relevantes na região de origem que listados no passo 4 da [preparar as VMs de origem](#prepare-the-source-vms).
2. Elimine a conta de armazenamento correspondente na região de origem.

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, aprendeu como mover VMs do Azure para uma região diferente do Azure. Agora pode configurar a recuperação após desastre para essas VMs.

> [!div class="nextstepaction"]
> [Configurar a recuperação após desastre após a migração](azure-to-azure-quickstart.md)

