---
title: Mover SQL Server VM para outra região no Azure com Azure Site Recovery Services | Microsoft Docs
description: Saiba como você pode migrar sua máquina virtual SQL Server de uma região para outra no Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
tags: azure-resource-manager
ms.assetid: aa5bf144-37a3-4781-892d-e0e300913d03
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 07/30/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 7b9a24161c76f9fbb0993ffb6719c1ea1b51dc55
ms.sourcegitcommit: 4b5dcdcd80860764e291f18de081a41753946ec9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/03/2019
ms.locfileid: "68775711"
---
# <a name="move-sql-server-vm-to-another-region-within-azure-with-azure-site-recovery-services"></a>Mover SQL Server VM para outra região no Azure com os serviços Azure Site Recovery

Este artigo ensina como usar Azure Site Recovery para migrar sua VM (máquina virtual) SQL Server de uma região para outra no Azure. 

Mover uma VM SQL Server para uma região diferente requer o seguinte:
1. [**Preparando**](#prepare-to-move): Confirme se a VM SQL Server de origem e a região de destino estão adequadamente preparados para a movimentação. 
1. [](#configure-azure-site-recovery-vault)Configurando: Mover sua VM de SQL Server requer que seja um objeto replicado dentro do cofre de Azure Site Recovery. Você precisa adicionar sua VM SQL Server ao cofre de Azure Site Recovery. 
1. [**Teste**](#test-move-process): Migrar a VM SQL Server requer o failover da região de origem para a região de destino replicada. Para garantir que o processo de movimentação terá êxito, você precisa primeiro testar se a VM SQL Server pode fazer failover com êxito para a região de destino. Isso ajudará a expor quaisquer problemas e evitá-los ao executar a movimentação real. 
1. [**Movendo**](#move-the-sql-server-vm): Depois que o failover de teste for aprovado e você souber que é seguro migrar sua VM SQL Server, você pode executar a movimentação da VM para a região de destino. 
1. [**Limpando**](#clean-up-source-resources): Para evitar encargos de cobrança, remova o SQL Server VM do cofre e todos os recursos desnecessários que são deixados no grupo de recursos. 

## <a name="verify-prerequisites"></a>Verificar os pré-requisitos 

- Confirme se [há suporte](../../../site-recovery/azure-to-azure-support-matrix.md#region-support)para a mudança da região de origem para a região de destino.  
- Examine a [arquitetura e os componentes do cenário](../../../site-recovery/azure-to-azure-architecture.md) , bem como as [limitações e os requisitos de suporte](../../../site-recovery/azure-to-azure-support-matrix.md). 
- Verifique as permissões da conta. Se você criou sua conta gratuita do Azure, você é o administrador da sua assinatura. Se você não for o administrador da assinatura, trabalhe com o administrador para atribuir as permissões necessárias. Para habilitar a replicação para uma VM e copiar dados usando Azure Site Recovery, você deve ter: 
    - Permissões para criar uma VM. A função interna *colaborador da máquina virtual* tem essas permissões, que incluem: 
        - Permissões para criar uma VM no grupo de recursos selecionado. 
        - Permissões para criar uma VM na rede virtual selecionada. 
        - Permissões para gravar na conta de armazenamento selecionada. 
      - Permissões para gerenciar operações de Azure Site Recovery. A função *colaborador de site Recovery* tem todas as permissões necessárias para gerenciar site Recovery operações em um cofre dos serviços de recuperação.  

## <a name="prepare-to-move"></a>Preparar para mover
Prepare a VM SQL Server de origem e a região de destino para a movimentação. 

### <a name="prepare-the-source-sql-server-vm"></a>Preparar a VM de SQL Server de origem

- Verifique se todos os certificados raiz mais recentes estão na VM SQL Server que você deseja mover. Se os certificados raiz mais recentes não estiverem lá, as restrições de segurança impedirão a cópia de dados para a região de destino. 
- Para VMs do Windows, instale todas as atualizações mais recentes do Windows na VM, para que todos os certificados raiz confiáveis estejam no computador. Em um ambiente desconectado, siga o processo padrão de atualização de certificado e atualização do Windows para sua organização. 
- Para VMs do Linux, siga as orientações fornecidas pelo seu distribuidor do Linux para obter os certificados raiz confiáveis mais recentes e a lista de certificados revogados na VM. 
- Verifique se você não está usando um proxy de autenticação para controlar a conectividade de rede para as VMs que deseja mover. 
- Se a VM que você está tentando mover não tiver acesso à Internet ou se estiver usando um proxy de firewall para controlar o acesso de saída, verifique os requisitos. 
- Identifique o layout de rede de origem e todos os recursos que your'e usam atualmente. Isso inclui, mas não se limita a balanceadores de carga, NSGs (grupos de segurança de rede) e IPs públicos. 

### <a name="prepare-the-target-region"></a>Preparar a região de destino

- Verifique se sua assinatura do Azure permite que você crie VMs na região de destino que é usada para recuperação de desastre. Contacte o suporte para ativar a quota necessária. 
- Verifique se sua assinatura tem recursos suficientes para dar suporte a VMs com tamanho que correspondam às suas VMs de origem. Se você estiver usando Site Recovery para copiar dados para o destino, Site Recovery escolherá o mesmo tamanho ou o tamanho mais próximo possível para a VM de destino. 
- Certifique-se de criar um recurso de destino para cada componente identificado no layout de rede de origem. Essa etapa é importante para garantir que suas VMs tenham toda a funcionalidade e os recursos na região de destino que você tinha na região de origem. 
    - Azure Site Recovery descobre e cria automaticamente uma rede virtual quando você habilita a replicação para a VM de origem. Você também pode criar previamente uma rede e atribuí-la à VM no fluxo do usuário para habilitar a replicação. Você precisa criar manualmente quaisquer outros recursos na região de destino.
- Para criar os recursos de rede usados com mais frequência que são relevantes para você com base na configuração da VM de origem, consulte a seguinte documentação: 
    - [Grupos de segurança de rede](../../../virtual-network/tutorial-filter-network-traffic.md) 
    - [Balanceador de carga](../../../load-balancer/tutorial-load-balancer-basic-internal-portal.md)
    - [Endereço IP público](../../../virtual-network/virtual-network-public-ip-address.md)
    - Para qualquer componente de rede adicional, consulte a [documentação de rede](../../../virtual-network/virtual-networks-overview.md).
- Crie manualmente uma rede que não seja de produção na região de destino se desejar testar a configuração antes de executar a movimentação final para a região de destino. Recomendamos esta etapa porque ela garante o mínimo de interferência com a rede de produção. 

## <a name="configure-azure-site-recovery-vault"></a>Configurar o cofre de Azure Site Recovery

As etapas a seguir mostram como usar Azure Site Recovery para copiar dados para a região de destino. Crie o cofre dos serviços de recuperação em qualquer região que não seja a região de origem. 

1. Inicie sessão no [Portal do Azure](https://portal.azure.com). 
1. Escolha **criar um recurso** no canto superior esquerdo do painel de navegação. 
1. Selecione **-o & ferramentas de gerenciamento** e, em seguida, selecione **backup e site Recovery**. 
1. Na guia **noções básicas** , em **detalhes do projeto**, crie um novo grupo de recursos na região de destino ou selecione um grupo de recursos existente na região de destino. 
1. Em **detalhes da instância**, especifique um nome para o cofre e, em seguida, selecione a **região** de destino na lista suspensa. 
1. Selecione **examinar + criar** para criar o cofre dos serviços de recuperação. 
1. Selecione **todos os serviços** no canto superior esquerdo do painel de navegação e, no tipo de caixa de pesquisa `recovery services`, digite. 
1. Opcionalmente Selecione a estrela ao lado **dos cofres dos serviços de recuperação** para adicioná-lo à sua barra de navegação rápida. 
1. Selecione **cofres dos serviços de recuperação** e selecione o cofre dos serviços de recuperação que você criou. 
1. No painel **visão geral** , selecione **replicar**. 

   ![Configurar a replicação](media/virtual-machines-windows-sql-move-to-new-region/configure-replication.png)

1. Selecione **origem** e, em seguida, selecione **Azure** como a origem. Selecione os valores apropriados para os outros campos suspensos, como o local para suas VMs de origem. Somente os grupos de recursos localizados na região do **local de origem** ficarão visíveis no campo grupo de **recursos de origem** . 
1. Selecione **máquinas virtuais** e, em seguida, escolha as máquinas virtuais que você deseja migrar. Selecione **OK** para salvar a seleção de VM. 
1. Selecione **configurações**e, em seguida, escolha o **local de destino** na lista suspensa. Esse deve ser o grupo de recursos que você preparou anteriormente. 
1. Depois de personalizar a replicação, selecione **criar recursos de destino** para criar os recursos no novo local. 
1. Após a conclusão da criação de recursos, selecione **habilitar replicação** para iniciar a replicação da VM SQL Server da origem para a região de destino.
1. Você pode verificar o status da replicação navegando até o cofre de recuperação, selecionando **itens replicados** e exibindo o **status** de sua VM SQL Server. Um status **protegido** indica que a replicação foi concluída. 

   ![Verificar status de replicação](media/virtual-machines-windows-sql-move-to-new-region/check-replication-status.png)

## <a name="test-move-process"></a>Processo de movimentação de teste
As etapas a seguir mostram como usar Azure Site Recovery para testar o processo de movimentação. 

1. Navegue até o **cofre dos serviços de recuperação** no [portal do Azure](https://portal.azure.com) e selecione **itens replicados**. 
1. Selecione a VM SQL Server que você deseja mover, verifique se a **integridade da replicação** é mostrada como **íntegra** e, em seguida, selecione **failover de teste**. 

   ![Failover de teste para sua VM](media/virtual-machines-windows-sql-move-to-new-region/test-failover-of-replicated-vm.png)

1. Na página **failover de teste** , selecione o ponto de recuperação **consistente com o aplicativo mais recente** a ser usado para o failover, pois esse é o único tipo de instantâneo que pode garantir SQL Server consistência de dados. 
1. Selecione a rede virtual na **rede virtual do Azure** e, em seguida, selecione **OK** para testar o failover. 
   
   >[!IMPORTANT]
   > Recomendamos que você use uma rede de VM do Azure separada para o teste de failover. Não use a rede de produção que foi configurada quando você habilitou a replicação e para a qual você deseja mover suas VMs eventualmente. 

1. Para monitorar o progresso, navegue até o cofre, selecione **site Recovery trabalhos** em **monitoramento**e, em seguida, selecione o trabalho de **failover de teste** em andamento.

   ![Monitorar o andamento do teste de failover](media/virtual-machines-windows-sql-move-to-new-region/monitor-failover-test-job.png)

1. Quando o teste for concluído, navegue até **máquinas virtuais** no portal e examine a máquina virtual recém-criada. Verifique se a VM SQL Server está em execução, se está dimensionada corretamente e se está conectada à rede apropriada. 
1. Exclua a VM que foi criada como parte do teste, pois a opção de **failover** ficará esmaecida até que os recursos de teste de failover sejam limpos. Navegue de volta para o cofre, selecione **itens replicados**, selecione o SQL Server VM e, em seguida, selecione **limpar failover de teste**. Registre e salve as observações associadas ao teste na seção **observações** e marque a caixa de seleção ao lado **de teste concluída. Excluir máquinas**virtuais de failover de teste. Selecione **OK** para limpar os recursos após o teste. 

   ![limpar itens após o teste de failover](media/virtual-machines-windows-sql-move-to-new-region/cleanup-test-items.png)

## <a name="move-the-sql-server-vm"></a>Mover a VM SQL Server 
As etapas a seguir mostram como mover a VM SQL Server da sua região de origem para sua região de destino. 

1. Navegue até o cofre **dos serviços de recuperação** , selecione **itens replicados**, selecione a VM e, em seguida, selecione **failover**. 

   ![Iniciar ativação pós-falha](media/virtual-machines-windows-sql-move-to-new-region/initiate-failover.png)

1. Selecione o ponto de recuperação **consistente com o aplicativo mais recente** no **ponto de restauração**. 
1. Marque a caixa de seleção ao lado de **desligar o computador antes de iniciar o failover**. Site Recovery tentará desligar a VM de origem antes de disparar o failover. O failover continuará mesmo se o desligamento falhar. 
1. Selecione **OK** para iniciar o failover.
1. Você pode monitorar o processo de failover na mesma página de **trabalhos site Recovery** exibida ao monitorar o teste de failover na seção anterior. 
1. Após a conclusão do trabalho, verifique se a VM SQL Server aparece na região de destino conforme o esperado. 
1. Navegue de volta para o cofre, selecione **itens replicados**, selecione o SQL Server VM e selecione **confirmar** para concluir o processo de movimentação para a região de destino. Aguarde até que o trabalho de confirmação seja concluído. 
1. Registre sua VM do SQL Server com o provedor de recursos da VM do SQL para habilitar a gerenciabilidade da **máquina virtual do SQL** no portal do Azure e recursos associados ao provedor de recursos. Para obter mais informações, consulte [registrar SQL Server VM com o provedor de recursos da VM do SQL](virtual-machines-windows-sql-register-with-rp.md). 

  > [!WARNING]
  > SQL Server consistência de dados só é garantida com instantâneos consistentes com o aplicativo. O instantâneo **mais recente processado** não pode ser usado para failover de SQL Server porque um instantâneo de recuperação de falha não pode garantir SQL Server consistência de dados. 

## <a name="clean-up-source-resources"></a>Limpar recursos de origem
Para evitar encargos de cobrança, remova o SQL Server VM do cofre e exclua todos os recursos associados desnecessários. 

1. Navegue de volta para o cofre de **site Recovery** , selecione **itens replicados**e selecione a VM SQL Server. 
1. Selecione **desabilitar replicação**. Selecione um motivo para desabilitar a proteção e, em seguida, selecione **OK** para desabilitar a replicação. 

   >[!IMPORTANT]
   > É importante executar essa etapa para evitar ser cobrado pela replicação de Azure Site Recovery. 

1. Se você não tiver planos para reutilizar nenhum dos recursos na região de origem, exclua todos os recursos de rede relevantes e as contas de armazenamento correspondentes. 


## <a name="next-steps"></a>Passos seguintes

Para obter mais informações, veja os artigos seguintes: 

* [Visão geral de SQL Server em uma VM do Windows](virtual-machines-windows-sql-server-iaas-overview.md)
* [Perguntas frequentes sobre o SQL Server em uma VM do Windows](virtual-machines-windows-sql-server-iaas-faq.md)
* [Diretrizes de preços do SQL Server em uma VM do Windows](virtual-machines-windows-sql-server-pricing-guidance.md)
* [SQL Server em notas de versão da VM do Windows](virtual-machines-windows-sql-server-iaas-release-notes.md)


