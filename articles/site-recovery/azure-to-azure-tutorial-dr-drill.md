---
title: Tutorial para executar um exercício de recuperação de desastres Azure VM com recuperação do local de Azure
description: Neste tutorial, faça um exercício de recuperação de desastres Azure VM para outra região usando a Recuperação do Local.
services: site-recovery
ms.topic: tutorial
ms.date: 11/05/2020
ms.custom: mvc
ms.openlocfilehash: c7cd1898f27f3b7255009efb40f6bcc8938dbf9e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "93395655"
---
# <a name="tutorial-run-a-disaster-recovery-drill-for-azure-vms"></a>Tutorial: Executar um exercício de recuperação de desastres para VMs Azure

Saiba como executar um exercício de recuperação de desastres para outra região do Azure, para VMs Azure replicando com [Azure Site Recovery](site-recovery-overview.md). Neste artigo, irá:

> [!div class="checklist"]
> * Verificar os pré-requisitos
> * Verifique as definições de VM antes da broca
> * Executar uma ativação pós-falha de teste
> * Limpe depois do exercício


> [!NOTE]
> Este tutorial fornece passos mínimos para executar um exercício de recuperação de desastres. Se quiser realizar um exercício com testes completos de infraestrutura, conheça a [rede](azure-to-azure-about-networking.md)Azure VM, [automação](azure-to-azure-powershell.md)e [resolução de problemas.](azure-to-azure-troubleshoot-errors.md)

## <a name="prerequisites"></a>Pré-requisitos

Antes de iniciar este tutorial, deve permitir a recuperação de desastres para um ou mais VMs Azure. Para isso, [complete o primeiro tutorial](azure-to-azure-tutorial-enable-replication.md) desta série.

## <a name="verify-vm-settings"></a>Verificar definições de VM

1. No cofre > **itens replicados,** selecione o VM.

    ![Opção para abrir página de recuperação de desastres em propriedades VM](./media/azure-to-azure-tutorial-dr-drill/vm-settings.png)

2. Na página **geral,** verifique se o VM está protegido e saudável.
3. Quando fizer um teste de falha, selecione uma rede virtual Azure na região alvo. O Azure VM criado após a falha é colocado nesta rede. 

    - Neste tutorial, selecionamos uma rede existente quando executarmos o teste de failover.
    - Recomendamos que escolha uma rede de não produção para a broca, para que os endereços IP e componentes de rede permaneçam disponíveis nas redes de produção.
   - Também pode configurar as definições de rede para serem utilizadas para o teste de falha. As configurações granulares que pode atribuir para cada NIC incluem sub-rede, endereço IP privado, endereço IP público, equilibrador de carga e grupo de segurança de rede. Não estamos a usar este método, mas [podes rever este artigo](azure-to-azure-customize-networking.md#customize-failover-and-test-failover-networking-configurations) para saber mais.


## <a name="run-a-test-failover"></a>Executar uma ativação pós-falha de teste


1. Na página **'Visão Geral',** selecione **Test Failover**.

    
    ![Teste botão de failover para o item replicado](./media/azure-to-azure-tutorial-dr-drill/test-failover-button.png)

2. Em **Test Failover,** escolha um ponto de recuperação. O Azure VM na região alvo é criado usando dados deste ponto de recuperação.
  
   - **Processo mais recente**: Utiliza o último ponto de recuperação processado pela Recuperação do Local. O carimbo de data/hora é apresentado. Não é gasto tempo a processar dados, pelo que fornece um objetivo de tempo de recuperação baixo (RTO).
   -  **Mais recente:** Processa todos os dados enviados para a Recuperação do Local, para criar um ponto de recuperação para cada VM antes de falhar. Fornece o objetivo de ponto de recuperação mais baixo (RPO), porque todos os dados são replicados para a Recuperação do Local quando a falha é desencadeada.
   - **Aplicações mais recentes consistentes**: Esta opção falha em VMs até ao mais recente ponto de recuperação consistente com aplicações. O carimbo de data/hora é apresentado.
   - **Costume**: Falhe no ponto de recuperação particular. O costume só está disponível quando falha num único VM e não usa um plano de recuperação.

3. Na **rede virtual Azure,** selecione a rede alvo para colocar VMs Azure criados após o failover. Selecione uma rede de não produção, se possível, e não a rede que foi criada quando ativou a replicação.

    ![Testar página de definições de failover](./media/azure-to-azure-tutorial-dr-drill/test-failover-settings.png)    

4. Para iniciar a falha, selecione **OK**.
5. Monitorize o teste de falha nas notificações.

    ![Notificação de ](./media/azure-to-azure-tutorial-dr-drill/notification-start-test-failover.png) ![ progresso Notificação de sucesso](./media/azure-to-azure-tutorial-dr-drill/notification-finish-test-failover.png)     


5. Após o fim do failover, o VM Azure criado na região alvo aparece no portal Azure **Virtual Machines**. Certifique-se de que o VM está a funcionar, dimensionado adequadamente e ligado à rede selecionada.

## <a name="clean-up-resources"></a>Limpar os recursos

1. Na página **Essentials,** selecione **'Falha no teste de limpeza'.**

    ![Botão para iniciar o processo de limpeza](./media/azure-to-azure-tutorial-dr-drill/select-cleanup.png)

2. Em **Notas de limpeza de falha**  >  **de** teste, registem e guarde quaisquer observações associadas ao teste de failover. 
3. O **Teste selecionado está completo** para eliminar os VMs criados durante a falha do teste.

    ![Página com opções de limpeza](./media/azure-to-azure-tutorial-dr-drill/cleanup-failover.png)

4. Monitorize o progresso da limpeza nas notificações.

    ![Notificação de progresso de limpeza ](./media/azure-to-azure-tutorial-dr-drill/notification-start-cleanup.png) ![ Notificação de sucesso de limpeza](./media/azure-to-azure-tutorial-dr-drill/notification-finish-cleanup.png)

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, fez um exercício de recuperação de desastres para verificar se o fracasso funciona como esperado. Agora pode tentar um fracasso total.

> [!div class="nextstepaction"]
> [Executar uma ativação pós-falha ](azure-to-azure-tutorial-failover-failback.md)
