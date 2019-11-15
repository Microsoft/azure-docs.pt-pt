---
title: Mover VMs do Azure para outra região com Azure Site Recovery
description: Use Azure Site Recovery para mover VMs IaaS do Azure de uma região do Azure para outra.
services: site-recovery
author: rajani-janaki-ram
ms.service: site-recovery
ms.topic: tutorial
ms.date: 01/28/2019
ms.author: rajanaki
ms.custom: MVC
ms.openlocfilehash: 92388c54804d85d2825a25abd0c234081670e8d4
ms.sourcegitcommit: a170b69b592e6e7e5cc816dabc0246f97897cb0c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74092186"
---
# <a name="move-azure-vms-to-another-azure-region"></a>Mover VMs do Azure para outra região do Azure

Talvez você queira mover máquinas virtuais IaaS (infraestrutura como serviço) do Azure de uma região para outra para melhorar a confiabilidade, a disponibilidade, o gerenciamento ou a governança. Este tutorial mostra como mover VMs para outra região usando Azure Site Recovery. Vai aprender a:

> [!div class="checklist"]
> * Verificar os pré-requisitos
> * Preparar as VMs de origem
> * Preparar a região de destino
> * Copiar dados para a região de destino
> * Testar a configuração
> * Executar a movimentação
> * Descartar os recursos da região de origem


> [!IMPORTANT]
> Este artigo descreve como mover VMs do Azure de uma região para outra *como está*. Se seu objetivo é melhorar a disponibilidade de sua infraestrutura movendo VMs para zonas de disponibilidade, consulte [mover VMs do Azure para zonas de disponibilidade](move-azure-vms-avset-azone.md).

## <a name="prerequisites"></a>Pré-requisitos

- Verifique se você tem VMs do Azure na região de origem do Azure *da*qual você deseja mover.
- Verifique se a combinação de região de [origem-região de destino é suportada](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix#region-support)e escolha cuidadosamente a região de destino.
- Certifique-se de que compreende a [arquitetura e os componentes do cenário](azure-to-azure-architecture.md).
- Veja os [requisitos e limitações de suporte](azure-to-azure-support-matrix.md).
- Verifique as permissões da conta. Se acabou de criar sua conta gratuita do Azure, *são* o administrador da sua subscrição. Se você não for o administrador, trabalhe com o administrador para obter as permissões necessárias:
  -  Para habilitar a replicação para uma VM e copiar dados para o destino usando Site Recovery, você deve ter permissões para criar uma VM em seus recursos do Azure. A função incorporada Contribuinte de Máquina Virtual tem estas permissões. Com as permissões, você pode:
        - Criar uma VM no grupo de recursos selecionado.
        - Criar uma VM na rede virtual selecionada.
        - Escrever na conta de armazenamento selecionada.

  - Você também precisa de permissões para gerenciar operações de Site Recovery. A função colaborador de Site Recovery tem todas as permissões necessárias para gerenciar Site Recovery operações em um cofre dos serviços de recuperação do Azure.

## <a name="prepare-the-source-vms"></a>Preparar as VMs de origem

1. Verifique se as VMs do Azure que você planeja mover têm os certificados raiz mais recentes. Se não forem, você não poderá habilitar a cópia de dados para a região de destino devido a restrições de segurança.

    - Para VMs do Windows, instale as atualizações mais recentes do Windows para que todos os certificados raiz confiáveis estejam no computador. Em um ambiente desconectado, siga os Windows Update padrão e os processos de atualização de certificado para sua organização.
    - Para VMs do Linux, siga as orientações do seu distribuidor do Linux para obter os certificados mais recentes de raiz confiável e a lista de certificados revogados.
2. Certifique-se de que você não está usando um proxy de autenticação para controlar a conectividade de rede para VMs que você planeja mover.
3. Se uma VM que você deseja mover não tiver acesso à Internet e estiver usando um proxy de firewall para controlar o acesso de saída, verifique os [requisitos](azure-to-azure-tutorial-enable-replication.md#set-up-outbound-network-connectivity-for-vms).
4. Documente o layout de rede de origem e todos os recursos que você está usando no momento, incluindo (mas não limitado a) balanceadores de carga, grupos de segurança de rede e endereços IP públicos para verificação.

## <a name="prepare-the-target-region"></a>Preparar a região de destino

1. Em sua assinatura do Azure, verifique se você pode criar VMs na região de destino que é usada para recuperação de desastre. Entre em contato com o suporte para habilitar a cota necessária, se necessário.

2. Certifique-se de que sua assinatura tenha recursos suficientes para dar suporte às suas VMs de origem. Se você estiver usando Site Recovery para copiar dados para o destino, ele escolherá o mesmo tamanho ou o tamanho mais próximo disponível para as VMs de destino.

3. Certifique-se de criar um recurso de destino para cada componente que você identificou no layout de rede de origem. Isso garante que suas VMs terão toda a funcionalidade e os recursos na região de destino que tinham na região de origem.

   Azure Site Recovery descobre e cria automaticamente uma conta de armazenamento e rede virtual quando você habilita a replicação para a VM de origem. Você também pode criar previamente esses recursos e atribuí-los à VM como parte da etapa habilitar-replicação. Mas você deve criar manualmente quaisquer outros recursos na região de destino. Consulte os documentos a seguir para criar os recursos de rede usados com mais frequência com base na configuração da VM de origem:

   - [Grupos de segurança de rede](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)
   - [Balanceadores de carga](https://docs.microsoft.com/azure/load-balancer)
   - [IP público](../virtual-network/virtual-network-public-ip-address.md)
    
   Para qualquer outro componente de rede, consulte a [documentação de rede do Azure](https://docs.microsoft.com/azure/#pivot=products&panel=network). 

4. Para testar a configuração antes de executar a movimentação, [crie manualmente uma rede que não seja de produção](https://docs.microsoft.com/azure/virtual-network/quick-create-portal) na região de destino. O teste da instalação cria uma interferência mínima com o ambiente de produção e é recomendável.
    
## <a name="copy-data-to-the-target-region"></a>Copiar dados para a região de destino
As etapas a seguir usam Azure Site Recovery para copiar dados para a região de destino.

### <a name="create-the-vault-in-any-region-except-the-source"></a>Criar o cofre em qualquer região, exceto a origem

1. Inicie sessão no [portal do Azure](https://portal.azure.com) > **Serviços de Recuperação**.
2. Selecione **criar um recurso** > **ferramentas de gerenciamento** > **backup e site Recovery**.
3. Para **nome**, especifique o nome amigável **ContosoVMVault**. Se tiver mais do que uma subscrição, selecione a que for adequada.
4. Crie um grupo de recursos **ContosoRG**.
5. Selecione uma região do Azure. Para verificar as regiões com suporte, consulte [Azure site Recovery detalhes de preços](https://azure.microsoft.com/pricing/details/site-recovery/).
6. Para cofres dos serviços de recuperação, selecione **visão geral** > **ConsotoVMVault** >  **+ replicar**.
7. Para **origem**, selecione **Azure**.
8. Para **local de origem**, selecione a região de origem do Azure onde suas VMs estão em execução no momento.
9. Selecione o modelo de implantação Azure Resource Manager. Em seguida, selecione a **assinatura de origem** e o grupo de recursos de **origem**.
10. Selecione **OK** para salvar as configurações.

### <a name="enable-replication-for-azure-vms-and-start-copying-the-data"></a>Habilitar a replicação para VMs do Azure e começar a copiar os dados

Site Recovery recupera uma lista das VMs que estão associadas à assinatura e ao grupo de recursos.

1. Selecione a VM que você deseja mover e, em seguida, selecione **OK**.
2. Para **configurações**, selecione **recuperação de desastre**.
3. Para **Configurar a recuperação de desastre** > **região de destino**, selecione a região de destino para a qual você está replicando.
4. Escolha usar os recursos de destino padrão ou os que você criou previamente.
5. Selecione **habilitar replicação** para iniciar o trabalho.

   ![Ativar a replicação](media/tutorial-migrate-azure-to-azure/settings.png)

 

## <a name="test-the-configuration"></a>Testar a configuração


1. Vá para o cofre. Em **configurações** > **itens replicados**, selecione a máquina virtual que você deseja mover para a região de destino. Em seguida, selecione **failover de teste**.
2. Em **failover de teste**, selecione um ponto de recuperação a ser usado para o failover:

   - **Processado mais recentemente**: faz a ativação pós-falha da VM para o ponto de recuperação mais recente processado pelo serviço do Site Recovery. O carimbo de data/hora é apresentado. Nenhum tempo é gasto processando dados, portanto, essa opção fornece um RTO (objetivo de tempo de recuperação) baixo.
   - **Consistente com o aplicativo mais recente**: faz failover de todas as VMs para o ponto de recuperação consistente com o aplicativo mais recente. O carimbo de data/hora é apresentado.
   - **Personalizado**: selecione qualquer ponto de recuperação.

3. Selecione a rede virtual do Azure de destino para a qual você deseja mover as VMs do Azure para testar a configuração.

   > [!IMPORTANT]
   > Recomendamos que você use uma rede VM do Azure separada para o failover de teste, não a rede de produção na região de destino.

4. Para começar a testar a movimentação, selecione **OK**. Para acompanhar o progresso, selecione a VM para abrir suas **Propriedades.** Ou então, selecione o trabalho de **failover de teste** no cofre. Em seguida, selecione **configurações** > **trabalhos** > **trabalhos de site Recovery**.
5. Após a conclusão da ativação pós-falha, a VM do Azure de réplica é apresentada no portal do Azure > **Máquinas Virtuais**. Certifique-se de que a VM está em execução, tem as dimensões adequadas e está ligada à rede devida.
6. Para excluir a VM que você criou para teste, selecione **limpar failover de teste** no item replicado. Em **observações**, registre e salve as observações relacionadas ao teste.

## <a name="perform-the-move-and-confirm"></a>Executar mover e confirmar

1. Vá para o cofre em **configurações** > **itens replicados**, selecione a máquina virtual e, em seguida, selecione **failover**.
1. Para **failover**, selecione **mais recente**. 
2. Selecione **Encerrar a máquina antes de iniciar a ativação pós-falha**. Site Recovery tenta desligar a VM de origem antes de disparar o failover. Mas o failover continua mesmo se o desligamento falhar. Pode seguir o progresso da ativação pós-falha na página **Tarefas**.
3. Quando o trabalho for concluído, verifique se a VM aparece na região de destino do Azure conforme o esperado.
4. Em **itens replicados**, clique com o botão direito do mouse na VM e selecione **confirmar**. Isso finaliza a movimentação. Aguarde até que o trabalho de confirmação seja concluído.

## <a name="discard-the-resources-from-the-source-region"></a>Descartar os recursos da região de origem

- Vá para a VM e selecione **desabilitar replicação**. Isso interrompe o processo de copiar os dados para a VM.

  > [!IMPORTANT]
  > Conclua esta etapa para evitar ser cobrado pela replicação de Site Recovery após a movimentação.

Se você não planeja reutilizar nenhum dos recursos de origem, siga estas etapas:

1. Exclua todos os recursos de rede relevantes na região de origem que você listou na etapa 4 de [preparar as VMs de origem](#prepare-the-source-vms).
2. Exclua a conta de armazenamento correspondente na região de origem.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você aprendeu a mover as VMs do Azure para uma região diferente do Azure. Agora você pode configurar a recuperação de desastre para essas VMs.

> [!div class="nextstepaction"]
> [Configurar a recuperação após desastre após a migração](azure-to-azure-quickstart.md)

