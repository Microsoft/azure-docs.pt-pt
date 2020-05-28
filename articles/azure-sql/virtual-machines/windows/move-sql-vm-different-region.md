---
title: Mova a máquina virtual para outra região (Recuperação do Site Azure)
description: Saiba como pode migrar a sua máquina virtual SQL Server de uma região para outra dentro do Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
tags: azure-resource-manager
ms.assetid: aa5bf144-37a3-4781-892d-e0e300913d03
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 07/30/2019
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: bca7237b38c1164d14ccf796e18980ba326090ac
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84042751"
---
# <a name="move-sql-server-vm-to-another-region-within-azure-with-azure-site-recovery-services"></a>Mova o SQL Server VM para outra região dentro do Azure com serviços de recuperação de sites Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Este artigo ensina-o a usar a Recuperação do Site Azure para migrar a sua máquina virtual SQL Server (VM) de uma região para outra dentro do Azure. 

Mover um VM de servidor SQL para uma região diferente requer fazer o seguinte:
1. [**Preparação**](#prepare-to-move): Confirme se tanto a sua fonte SQL Server VM como a região alvo estão adequadamente preparadas para a mudança. 
1. [**Configuração**](#configure-azure-site-recovery-vault): Mover o vM do servidor SQL requer que seja um objeto replicado dentro do cofre de recuperação do site Azure. Você precisa adicionar o seu VM de servidor SQL ao cofre de recuperação do site Azure. 
1. [**Teste**](#test-move-process): Migrar o VM do Servidor SQL requer que falhe na região de origem para a região-alvo replicada. Para garantir que o processo de movimento será bem sucedido, precisa primeiro de testar que o seu VM de Servidor SQL pode falhar com sucesso na região alvo. Isto ajudará a expor quaisquer problemas e evitá-los na execução do movimento real. 
1. [**Movendo-se**](#move-the-sql-server-vm): Uma vez que o seu teste falhou, e sabe que está seguro para migrar o seu VM sQL Server, pode executar a mudança do VM para a região alvo. 
1. [**Limpeza**](#clean-up-source-resources): Para evitar taxas de faturação, retire o VM do Servidor SQL do cofre e quaisquer recursos desnecessários que sobrarem no grupo de recursos. 

## <a name="verify-prerequisites"></a>Verificar os pré-requisitos 

- Confirme que a mudança da sua região de origem para a sua região alvo [é apoiada](../../../site-recovery/azure-to-azure-support-matrix.md#region-support).  
- Reveja a arquitetura e componentes do [cenário,](../../../site-recovery/azure-to-azure-architecture.md) bem como as [limitações e requisitos](../../../site-recovery/azure-to-azure-support-matrix.md)de suporte. 
- Verifique as permissões da conta. Se criou a sua conta Azure gratuita, é o administrador da sua subscrição. Se não for o administrador de subscrição, trabalhe com o administrador para atribuir as permissões de que necessita. Para permitir a replicação de um VM e copiar dados utilizando a Recuperação do Site Azure, deve ter: 
    - Permissões para criar um VM. O papel integrado da *Máquina Virtual* tem estas permissões, que incluem: 
        - Permissões para criar um VM no grupo de recursos selecionados. 
        - Permissões para criar um VM na rede virtual selecionada. 
        - Permissões para escrever na conta de armazenamento selecionada. 
      - Permissões para gerir operações de recuperação do site azure. O papel do Colaborador de *Recuperação* do Site tem todas as permissões necessárias para gerir as operações de Recuperação do Local num cofre dos Serviços de Recuperação.  

## <a name="prepare-to-move"></a>Prepare-se para se mover
Prepare tanto a fonte SQL Server VM como a região alvo para a mudança. 

### <a name="prepare-the-source-sql-server-vm"></a>Prepare a fonte SQL Server VM

- Certifique-se de que todos os certificados de raiz mais recentes estão no VM do Servidor SQL que pretende mover. Se os certificados de raiz mais recentes não existirem, as restrições de segurança impedirão a cópia de dados para a região alvo. 
- Para os VMs do Windows, instale todas as atualizações mais recentes do Windows no VM, de modo a que todos os certificados de raiz confiáveis estejam na máquina. Num ambiente desligado, siga o processo padrão de UPdate do Windows e atualização de certificados para a sua organização. 
- Para os VMs Linux, siga as orientações fornecidas pelo seu distribuidor Linux para obter os mais recentes certificados de raiz fidedignos e lista de revogação de certificados no VM. 
- Certifique-se de que não está a utilizar um proxy de autenticação para controlar a conectividade da rede para os VMs que pretende mover. 
- Se o VM que está a tentar mover não tem acesso à internet, ou está a usar um representante de firewall para controlar o acesso de saída, verifique os requisitos. 
- Identifique o layout de rede de origem e todos os recursos que está a utilizar. Isto inclui, mas não se limita a equilibradores de carga, grupos de segurança de rede (NSGs) e IPs públicos. 

### <a name="prepare-the-target-region"></a>Preparar a região alvo

- Verifique se a sua subscrição Azure permite criar VMs na região alvo que é usada para a recuperação de desastres. Contacte o suporte para ativar a quota necessária. 
- Certifique-se de que a sua subscrição dispõe de recursos suficientes para suportar VMs com tamanho que correspondam aos seus VMs de origem. Se estiver a utilizar a Recuperação do Site para copiar dados para o alvo, a Recuperação do Site escolhe o mesmo tamanho ou o tamanho mais próximo possível para o VM alvo. 
- Certifique-se de que cria um recurso-alvo para cada componente identificado no layout de rede de origem. Este passo é importante para garantir que os seus VMs possuam todas as funcionalidades e funcionalidades na região alvo que você tinha na região de origem. 
    - A Recuperação do Site Azure descobre e cria automaticamente uma rede virtual quando ativa a replicação para o VM de origem. Também pode pré-criar uma rede e atribuí-la ao VM no fluxo do utilizador para permitir a replicação. É necessário criar manualmente quaisquer outros recursos na região alvo.
- Para criar os recursos de rede mais utilizados que são relevantes para si com base na configuração vm de origem, consulte a seguinte documentação: 
    - [Grupos de segurança de rede](../../../virtual-network/tutorial-filter-network-traffic.md) 
    - [Equilibrador de carga](../../../load-balancer/tutorial-load-balancer-basic-internal-portal.md)
    - [Endereço IP público](../../../virtual-network/virtual-network-public-ip-address.md)
    - Para quaisquer componentes adicionais de rede, consulte a [documentação](../../../virtual-network/virtual-networks-overview.md)de rede .
- Crie manualmente uma rede de não produção na região alvo se pretender testar a configuração antes de realizar o movimento final para a região alvo. Recomendamos este passo porque garante uma interferência mínima na rede de produção. 

## <a name="configure-azure-site-recovery-vault"></a>Configure cofre de recuperação de site azure

Os seguintes passos mostram como usar a Recuperação do Site Azure para copiar dados para a região alvo. Crie o cofre dos Serviços de Recuperação em qualquer região que não a região de origem. 

1. Inicie sessão no [portal do Azure](https://portal.azure.com). 
1. Escolha **criar um recurso** a partir do canto superior esquerdo do painel de navegação. 
1. Selecione **as ferramentas de gestão de TI &** e, em seguida, selecione Backup e **Recuperação**do Site . 
1. No separador **Basics,** sob os detalhes do **Projeto,** ou criar um novo grupo de recursos na região alvo, ou selecionar um grupo de recursos existente na região alvo. 
1. Em detalhes de **instância,** especifique um nome para o seu cofre e, em seguida, selecione a **região** alvo a partir da queda. 
1. Selecione **Review + Crie** para criar o seu cofre de Serviços de Recuperação. 
1. Selecione **Todos os serviços** do canto superior esquerdo do painel de navegação e do tipo de caixa de pesquisa `recovery services` . 
1. (Opcionalmente) Selecione a estrela junto aos **cofres dos Serviços** de Recuperação para adicioná-la à sua barra de navegação rápida. 
1. Selecione os cofres dos **serviços de recuperação** e, em seguida, selecione o cofre dos Serviços de Recuperação que criou. 
1. No painel de **visão geral,** selecione **Replicate**. 

   ![Configurar a replicação](./media/move-sql-vm-different-region/configure-replication.png)

1. Selecione **Fonte** e, em seguida, selecione **Azure** como fonte. Selecione os valores apropriados para os outros campos de entrega, como a localização para os seus VMs de origem. Apenas grupos de recursos localizados na região de **localização Fonte** serão visíveis no campo do grupo de **recursos Fonte.** 
1. Selecione **máquinas Virtuais** e, em seguida, escolha as máquinas virtuais que pretende migrar. Selecione **OK** para salvar a sua seleção vm. 
1. Selecione **Definições**e, em seguida, escolha a **localização** do target a partir do drop-down. Este deve ser o grupo de recursos que preparou anteriormente. 
1. Uma vez que tenha replicação personalizada, selecione **Criar recursos-alvo** para criar os recursos na nova localização. 
1. Uma vez concluída a criação de recursos, **selecione A replicação de Ativar** para iniciar a replicação do seu VM do Servidor SQL da fonte para a região alvo.
1. Pode verificar o estado da replicação navegando até ao seu cofre de recuperação, selecionando **itens replicados** e visualizando o **Estado** do seu VM do Servidor SQL. Um estado de **Proteção** indica que a replicação está concluída. 

   ![Verificar o estado de replicação](./media/move-sql-vm-different-region/check-replication-status.png)

## <a name="test-move-process"></a>Processo de movimento de teste
Os seguintes passos mostram-lhe como utilizar a Recuperação do Site Azure para testar o processo de movimento. 

1. Navegue até ao seu cofre de Serviços de **Recuperação** no [portal Azure](https://portal.azure.com) e selecione **itens Replicados**. 
1. Selecione o VM do Servidor SQL que gostaria de mover, verifique se a **Saúde** de Replicação **mostra-se** saudável e, em seguida, selecione **Failover**de teste . 

   ![Falha no teste para o seu VM](./media/move-sql-vm-different-region/test-failover-of-replicated-vm.png)

1. Na página Failover do **Teste,** selecione o mais recente ponto de recuperação consistente com **aplicações** para utilizar para a falha, uma vez que este é o único tipo de instantâneo que pode garantir a consistência dos dados do SQL Server. 
1. Selecione a rede virtual sob **a rede virtual Azure** e, em seguida, selecione **OK** para testar falha. 
   
   >[!IMPORTANT]
   > Recomendamos que utilize uma rede Azure VM separada para o teste de failover. Não utilize a rede de produção que foi criada quando permitiu a replicação e que pretende mover os seus VMs eventualmente. 

1. Para monitorizar o progresso, navegue até ao seu cofre, selecione **trabalhos** de recuperação do site sob **monitorização**e, em seguida, selecione o trabalho de **failover** do Teste que está em curso.

   ![Monitorizar o progresso do teste de failover](./media/move-sql-vm-different-region/monitor-failover-test-job.png)

1. Uma vez concluído o teste, navegue para **máquinas Virtuais** no portal e reveja a máquina virtual recém-criada. Certifique-se de que o VM do Servidor SQL está em execução, está dimensionado adequadamente e está ligado à rede apropriada. 
1. Eliminar o VM que foi criado como parte do teste, uma vez que a opção **Failover** será acinzentada até que os recursos de teste de failover sejam limpos. Navegue de volta ao cofre, selecione **itens Replicados,** selecione o VM do Servidor SQL e, em seguida, selecione falha no teste de **limpeza**. Grave e guarde quaisquer observações associadas ao teste na secção **Notas** e selecione a caixa de verificação ao lado do **Teste está completa. Eliminar**falhas de teste sobre máquinas virtuais . Selecione **OK** para limpar os recursos após o teste. 

   ![limpar itens após teste failover](./media/move-sql-vm-different-region/cleanup-test-items.png)

## <a name="move-the-sql-server-vm"></a>Mova o VM do Servidor SQL 
Os seguintes passos mostram-lhe como mover o VM do Servidor SQL da sua região de origem para a sua região alvo. 

1. Navegue para o cofre dos Serviços de **Recuperação,** selecione **itens Replicados,** selecione o VM e, em seguida, selecione **Failover**. 

   ![Iniciar a falha](./media/move-sql-vm-different-region/initiate-failover.png)

1. Selecione o mais recente ponto de recuperação consistente com **aplicações** no ponto de **recuperação**. 
1. Selecione a caixa de verificação ao lado de **desligar a máquina antes**de iniciar a falha . A Recuperação do Local tentará desligar a Fonte VM antes de desencadear a falha. A falha continuará mesmo que o encerramento falhe. 
1. Selecione **OK** para iniciar a falha.
1. Pode monitorizar o processo de failover a partir da mesma página de trabalhos de **recuperação** do site visualizada ao monitorizar o teste de failover na secção anterior. 
1. Após o trabalho estar concluído, verifique se o VM do Servidor SQL aparece na região alvo como esperado. 
1. Navegue de volta ao cofre, selecione **Itens Replicados,** selecione o VM do Servidor SQL e selecione **Comprometer-se** a terminar o processo de movimento para a região alvo. Espere até que o trabalho de compromisso termine. 
1. Registe o seu VM De Servidor SQL com o fornecedor de recursos SQL VM para permitir a gestão da **máquina virtual SQL** no portal Azure e funcionalidades associadas ao fornecedor de recursos. Para mais informações, consulte [O Registo VM do Servidor SQL com o fornecedor de recursos SQL VM](sql-vm-resource-provider-register.md). 

  > [!WARNING]
  > A consistência dos dados do SQL Server só é garantida com instantâneos consistentes com aplicações. O **instantâneo mais recente processado** não pode ser usado para falha do SQL Server, uma vez que um instantâneo de recuperação de falhas não pode garantir a consistência dos dados do SQL Server. 

## <a name="clean-up-source-resources"></a>Limpar os recursos de origem
Para evitar taxas de faturação, retire o VM do Servidor SQL do cofre e elimine quaisquer recursos associados desnecessários. 

1. Navegue de volta para o cofre de **recuperação** do site, selecione **itens replicados,** e selecione o VM do Servidor SQL. 
1. Selecione **Desativar a replicação**. Selecione uma razão para desativar a proteção e, em seguida, selecione **OK** para desativar a replicação. 

   >[!IMPORTANT]
   > É importante realizar este passo para evitar ser cobrado para a replicação da Recuperação do Local Azure. 

1. Se não tiver planos para reutilizar nenhum dos recursos na região fonte, elimine todos os recursos de rede relevantes e contas de armazenamento correspondentes. 


## <a name="next-steps"></a>Próximos passos

Para obter mais informações, veja os seguintes artigos: 

* [Visão geral do Servidor SQL num VM do Windows](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Servidor SQL em uma FAQ VM do Windows](frequently-asked-questions-faq.md)
* [Servidor SQL em uma orientação de preços VM do Windows](pricing-guidance.md)
* [SQL Server em notas de lançamento do Windows VM](doc-changes-updates-release-notes.md)


