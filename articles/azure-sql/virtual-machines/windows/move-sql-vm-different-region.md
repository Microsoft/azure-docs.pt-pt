---
title: Mover uma máquina virtual para outra região (Recuperação do Sítio Azure)
description: Saiba como pode migrar a sua máquina virtual SQL Server de uma região para outra dentro de Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.assetid: aa5bf144-37a3-4781-892d-e0e300913d03
ms.service: virtual-machines-sql
ms.subservice: migration
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 07/30/2019
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: 789554121af1c83d9077e6153ca9db01477bde25
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97360157"
---
# <a name="move-a-sql-server-vm-to-another-region-within-azure-with-azure-site-recovery"></a>Mover um SQL Server VM para outra região dentro de Azure com recuperação do site Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Este artigo ensina-o a usar a Recuperação do Site Azure para migrar a sua máquina virtual SQL Server (VM) de uma região para outra dentro do Azure. 

Mover um SQL Server VM para uma região diferente requer fazer o seguinte:
1. [Preparação](#prepare-to-move): Confirme que tanto o seu SQL Server VM como a região alvo estão adequadamente preparados para a mudança. 
1. [Configuração:](#configure-azure-site-recovery-vault)Mover o seu SQL Server VM requer que se trata de um objeto replicado dentro do cofre de recuperação do local de Azure. Tem de adicionar o seu SQL Server VM ao cofre de recuperação do local de Azure. 
1. [Testes](#test-move-process): A migração do SQL Server VM requer a sua falha da região de origem para a região alvo replicada. Para garantir que o processo de movimento será bem sucedido, é necessário primeiro testar que o seu SQL Server VM pode falhar com sucesso na região alvo. Isto ajudará a expor quaisquer problemas e evitá-los ao realizar o movimento real. 
1. [Movimento](#move-the-sql-server-vm): Uma vez que o seu teste falhou e sabe que está seguro para migrar o seu SQL Server VM, pode efetuar a deslocação do VM para a região alvo. 
1. [Limpeza](#clean-up-source-resources): Para evitar encargos de faturação, retire o SQL Server VM do cofre e quaisquer recursos desnecessários que sejam deixados no grupo de recursos. 

## <a name="verify-prerequisites"></a>Verificar os pré-requisitos 

- Confirme que a deslocação da sua região de origem para a sua região alvo [é apoiada](../../../site-recovery/azure-to-azure-support-matrix.md#region-support).  
- Reveja a arquitetura e componentes do [cenário,](../../../site-recovery/azure-to-azure-architecture.md) bem como as [limitações e requisitos](../../../site-recovery/azure-to-azure-support-matrix.md)de suporte. 
- Verifique as permissões da conta. Se criou a sua conta Azure gratuita, é o administrador da sua subscrição. Se não for o administrador de subscrição, trabalhe com o administrador para atribuir as permissões de que necessita. Para ativar a replicação de um VM e copiar dados utilizando a Recuperação do Site Azure, deve ter: 
    - Permissões para criar um VM. O *papel integrado do Contribuinte De Máquina Virtual* tem estas permissões, que incluem: 
        - Permissões para criar um VM no grupo de recursos selecionado. 
        - Permissões para criar um VM na rede virtual selecionada. 
        - Permissões para escrever na conta de armazenamento selecionada. 
      - Permissões para gerir as operações de Recuperação do Local de Azure. A função *de Contribuinte de Recuperação do Local* tem todas as permissões necessárias para gerir as operações de Recuperação do Local num cofre dos Serviços de Recuperação.  

## <a name="prepare-to-move"></a>Preparar para mover
Prepare tanto a fonte SQL Server VM como a região alvo para a mudança. 

### <a name="prepare-the-source-sql-server-vm"></a>Preparar a fonte SQL Server VM

- Certifique-se de que todos os certificados de raiz mais recentes estão no SQL Server VM que pretende mover. Se os certificados de raiz mais recentes não estiverem lá, as restrições de segurança impedirão a cópia de dados para a região alvo. 
- Para os VMs do Windows, instale todas as atualizações mais recentes do Windows no VM, de modo a que todos os certificados de raiz fidedignos estejam na máquina. Num ambiente desligado, siga o processo padrão de atualização do Windows Update e certificado para a sua organização. 
- Para os VMs Linux, siga as orientações fornecidas pelo seu distribuidor Linux para obter os mais recentes certificados de raiz fidedignos e lista de revogação de certificados no VM. 
- Certifique-se de que não está a utilizar um representante de autenticação para controlar a conectividade da rede para os VMs que pretende mover. 
- Se o VM que está a tentar mover não tiver acesso à internet, ou se estiver a usar um representante de firewall para controlar o acesso de saída, verifique os requisitos. 
- Identifique o layout de rede de origem e todos os recursos que está a usar. Isto inclui, mas não se limita a equilibradores de carga, grupos de segurança de rede (NSGs) e IPs públicos. 

### <a name="prepare-the-target-region"></a>Preparar a região alvo

- Verifique se a sua subscrição Azure permite criar VMs na região alvo que é usada para a recuperação de desastres. Contacte o suporte para ativar a quota necessária. 
- Certifique-se de que a sua subscrição tem recursos suficientes para suportar VMs com tamanho que correspondam aos seus VMs de origem. Se estiver a utilizar a Recuperação do Site para copiar dados para o alvo, a Recuperação do Site escolhe o mesmo tamanho ou o tamanho mais próximo possível para o VM alvo. 
- Certifique-se de que cria um recurso-alvo para todos os componentes identificados no layout de rede de origem. Este passo é importante para garantir que os seus VMs tenham todas as funcionalidades e funcionalidades na região-alvo que tinha na região de origem. 
    - A Azure Site Recovery descobre e cria automaticamente uma rede virtual quando ativa a replicação para o VM de origem. Também pode pré-criar uma rede e atribuí-la ao VM no fluxo do utilizador para permitir a replicação. É necessário criar manualmente quaisquer outros recursos na região alvo.
- Para criar os recursos de rede mais utilizados que são relevantes para si com base na configuração VM de origem, consulte a seguinte documentação: 
    - [Grupos de segurança de rede](../../../virtual-network/tutorial-filter-network-traffic.md) 
    - [Equilibrador de carga](../../../load-balancer/quickstart-load-balancer-standard-internal-portal.md)
    - [Endereço IP público](../../../virtual-network/virtual-network-public-ip-address.md)
    - Para obter quaisquer componentes de rede adicionais, consulte a [documentação de networking](../../../virtual-network/virtual-networks-overview.md).
- Crie manualmente uma rede de não produção na região alvo se quiser testar a configuração antes de realizar a última mudança para a região alvo. Recomendamos este passo porque garante uma interferência mínima com a rede de produção. 

## <a name="configure-azure-site-recovery-vault"></a>Cofre de recuperação do local de configure

Os passos seguintes mostram-lhe como utilizar a Recuperação do Sítio Azure para copiar dados para a região alvo. Crie o cofre dos Serviços de Recuperação em qualquer região que não seja a região nascente. 

1. Inicie sessão no [portal do Azure](https://portal.azure.com). 
1. Opte por **criar um recurso** a partir do canto superior esquerdo do painel de navegação. 
1. Selecione **as ferramentas de gestão de TI &** e, em seguida, selecione Backup e Site **Recovery**. 
1. No separador **Basics,** nos **detalhes do Projeto,** ou cria um novo grupo de recursos na região alvo ou seleciona um grupo de recursos existente na região alvo. 
1. De acordo com **os detalhes da instância,** especifique um nome para o seu cofre e, em seguida, selecione a **região** alvo a partir do drop-down. 
1. Selecione **Review + Criar** para criar o cofre dos Serviços de Recuperação. 
1. Selecione **Todos os serviços** a partir do canto superior esquerdo do painel de navegação e no tipo de caixa de pesquisa `recovery services` . 
1. (Opcionalmente) Selecione a estrela ao lado **dos cofres dos Serviços de Recuperação** para adicioná-la à sua barra de navegação rápida. 
1. Selecione **cofres de serviços de recuperação** e, em seguida, selecione o cofre dos Serviços de Recuperação que criou. 
1. No **painel de visão** geral, selecione **Replicar**. 

   ![Configurar a replicação](./media/move-sql-vm-different-region/configure-replication.png)

1. Selecione **Source** e, em seguida, selecione **Azure** como a fonte. Selecione os valores adequados para os outros campos de sistência, como a localização dos seus VMs de origem. Apenas grupos de recursos localizados na região **de localização Fonte** serão visíveis no campo do **grupo de recursos Fonte.** 
1. Selecione **máquinas virtuais** e, em seguida, escolha as máquinas virtuais que pretende migrar. Selecione **OK** para guardar a sua seleção VM. 
1. Selecione **Definições** e, em seguida, escolha a **localização** do alvo a partir do drop-down. Este deve ser o grupo de recursos que preparou anteriormente. 
1. Uma vez que tenha replicado personalizado, **selecione Criar recursos-alvo** para criar os recursos na nova localização. 
1. Assim que a criação de recursos estiver concluída, selecione Ative a **replicação** para iniciar a replicação do seu SQL Server VM da fonte para a região alvo.
1. Pode verificar o estado da replicação navegando no seu cofre de recuperação, selecionando **itens replicados** e visualizando o **Estado** do seu VM do seu Servidor SQL. Um estado de **Proteção** indica que a replicação foi concluída. 

   ![Verificar o estado de replicação](./media/move-sql-vm-different-region/check-replication-status.png)

## <a name="test-move-process"></a>Processo de movimento de teste
Os passos seguintes mostram-lhe como utilizar a Recuperação do Local de Azure para testar o processo de movimento. 

1. Navegue para o **cofre dos Serviços de Recuperação** no [portal Azure](https://portal.azure.com) e selecione **itens replicados.** 
1. Selecione o SQL Server VM que gostaria de mover, verifique se a **Saúde** de Replicação mostra como **Saudável** e, em seguida, selecione **Test Failover**. 

   ![Teste de failover para o seu VM](./media/move-sql-vm-different-region/test-failover-of-replicated-vm.png)

1. Na página **Test Failover,** selecione o último ponto de recuperação **consistente da aplicação** para usar para a falha, pois este é o único tipo de instantâneo que pode garantir a consistência dos dados do SQL Server. 
1. Selecione a rede virtual sob **rede virtual Azure** e, em seguida, selecione **OK** para testar failover. 
   
   >[!IMPORTANT]
   > Recomendamos que utilize uma rede Azure VM separada para o teste de failover. Não utilize a rede de produção que foi criada quando ativou a replicação e que pretende mover os seus VMs eventualmente. 

1. Para monitorizar o progresso, navegue para o seu cofre, selecione **os trabalhos de Recuperação** do Local em **Monitorização** e, em seguida, selecione o trabalho **de failover** test que está em curso.

   ![Monitorização do progresso do teste de failover](./media/move-sql-vm-different-region/monitor-failover-test-job.png)

1. Assim que o teste estiver concluído, navegue para **máquinas Virtuais** no portal e reveja a máquina virtual recém-criada. Certifique-se de que o SQL Server VM está em funcionamento, é dimensionado adequadamente e está ligado à rede apropriada. 
1. Eliminar o VM que foi criado como parte do teste, uma vez que a opção **Failover** será acinzentada até que os recursos de teste de failover sejam limpos. Navegue de volta para o cofre, **selecione itens replicados,** selecione o SQL Server VM e, em seguida, selecione **Falha no teste de limpeza**. Grave e guarde quaisquer observações associadas ao teste na secção **Notas** e selecione a caixa de verificação ao lado **do Teste. Eliminar o teste de falha em máquinas virtuais**. Selecione **OK** para limpar os recursos após o teste. 

   ![limpar itens após teste de failover](./media/move-sql-vm-different-region/cleanup-test-items.png)

## <a name="move-the-sql-server-vm"></a>Mover o SQL Server VM 
Os passos seguintes mostram-lhe como mover o SQL Server VM da sua região de origem para a sua região alvo. 

1. Navegue para o cofre **dos Serviços de Recuperação,** selecione **itens replicados,** selecione o VM e, em seguida, selecione **Failover**. 

   ![Iniciar failover](./media/move-sql-vm-different-region/initiate-failover.png)

1. Selecione o mais recente ponto de recuperação **consistente da aplicação** no **ponto de recuperação**. 
1. Selecione a caixa de verificação ao lado **de Desligar a máquina antes de começar a falhar**. A Recuperação do Local tentará desligar a fonte VM antes de desencadear a falha. A falha continuará mesmo que o encerramento falhe. 
1. Selecione **OK** para iniciar a falha.
1. Pode monitorizar o processo de failover a partir da mesma página de **trabalhos de Recuperação** de Sítio que viu ao monitorizar o teste de failover na secção anterior. 
1. Após o fim do trabalho, verifique se o SQL Server VM aparece na região alvo como esperado. 
1. Navegue de volta para o cofre, **selecione Itens Replicados,** selecione o SQL Server VM e selecione **Comprometa-se** a terminar o processo de movimento para a região alvo. Espere até que o trabalho de compromisso termine. 
1. Registe o seu SQL Server VM com a extensão SQL IaaS Agent para permitir a gestão **da máquina virtual SQL** no portal Azure e as funcionalidades associadas à extensão. Para obter mais informações, consulte [Register SQL Server VM com a extensão SQL IaaS Agent](sql-agent-extension-manually-register-single-vm.md). 

  > [!WARNING]
  > A consistência dos dados do SQL Server só é garantida com instantâneos consistentes com aplicações. O **instantâneo processado mais recente** não pode ser usado para falha do SQL Server, uma vez que um instantâneo de recuperação de falhas não pode garantir a consistência dos dados do SQL Server. 

## <a name="clean-up-source-resources"></a>Limpar recursos de origem
Para evitar cobrar, retire o SQL Server VM do cofre e elimine quaisquer recursos associados desnecessários. 

1. Volte para o cofre **de recuperação** do site, selecione **itens replicados** e selecione o SQL Server VM. 
1. Selecione **a replicação de desativação**. Selecione uma razão para desativar a proteção e, em seguida, selecione **OK** para desativar a replicação. 

   >[!IMPORTANT]
   > É importante executar este passo para evitar ser carregado para a replicação do Azure Site Recovery. 

1. Se não tiver planos para reutilizar nenhum dos recursos na região de origem, elimine todos os recursos de rede relevantes e as correspondentes contas de armazenamento. 


## <a name="next-steps"></a>Próximos passos

Para obter mais informações, veja os seguintes artigos: 

* [Visão geral do SQL Server num VM do Windows](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [SqL Server em um Windows VM FAQ](frequently-asked-questions-faq.md)
* [SQL Server em uma orientação de preços do Windows VM](pricing-guidance.md)
* [SQL Server em um Windows VM notas de lançamento](doc-changes-updates-release-notes.md)