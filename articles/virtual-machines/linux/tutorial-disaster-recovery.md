---
title: Tutorial - Configurar a recuperação de desastres para os VMs do Linux com recuperação do local de Azure
description: Saiba como configurar a recuperação de desastres para os VMs Linux para uma região de Azure diferente, utilizando o serviço de Recuperação do Local Azure.
author: rayne-wiselman
ms.service: virtual-machines-linux
ms.subservice: recovery
ms.topic: tutorial
ms.date: 11/05/2020
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: d14d276c798e40d417a8038aee5b7550e84f4114
ms.sourcegitcommit: 0d171fe7fc0893dcc5f6202e73038a91be58da03
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/05/2020
ms.locfileid: "93380411"
---
# <a name="tutorial-set-up-disaster-recovery-for-linux-virtual-machines"></a>Tutorial: Criar recuperação de desastres para máquinas virtuais Linux


Este tutorial mostra-lhe como configurar a recuperação de desastres para os VMs Azure que executam o Linux. Neste artigo, saiba como:

> [!div class="checklist"]
> * Permitir a recuperação de desastres para um Linux VM
> * Executar um teste de recuperação após desastre
> * Pare de replicar o VM após a broca

Quando ativa a replicação de um VM, a extensão do serviço de mobilidade de recuperação do local instala-se no VM e regista-a com [a Recuperação do Sítio Azure](../../site-recovery/site-recovery-overview.md). Durante a replicação, os discos VM são enviados para uma conta de armazenamento de cache na região de origem. Os dados são enviados de lá para a região alvo, e os pontos de recuperação são gerados a partir dos dados.  Quando se falha um VM noutra região durante a recuperação de desastres, é utilizado um ponto de recuperação para restaurar o VM na região alvo.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

1. Verifique se a sua subscrição Azure permite criar um VM na região alvo. Se acabou de criar a sua conta Azure gratuita, é o administrador da subscrição e tem as permissões de que necessita.
2. Se não for o administrador de subscrição, trabalhe com o administrador para lhe atribuir:
    - Ou o papel incorporado do Contribuinte de Máquina Virtual, quer permissões específicas para:
        - Criar uma VM na rede virtual selecionada.
        - Escreva para uma conta de armazenamento Azure.
        - Escreva para um disco gerido pelo Azure.
     - O contributo de recuperação do local, para gerir as operações de recuperação do local no cofre. 
3. Verifique se o Linux VM está a executar um [sistema operativo suportado.](../../site-recovery/azure-to-azure-support-matrix.md#linux)
4. Se as ligações de saída VM utilizarem um representante baseado em URL, certifique-se de que pode aceder a estes URLs. Usar um representante autenticado não é suportado.

    **Name** | **Cloud pública** | **Nuvem do governo** | **Detalhes**
    --- | --- | --- | ---
    Armazenamento | `*.blob.core.windows.net` | `*.blob.core.usgovcloudapi.net`| Escreva dados do VM para a conta de armazenamento de cache na região de origem. 
    Azure AD  | `login.microsoftonline.com` | `login.microsoftonline.us`| Autorizar e autenticar para URLs de serviço de recuperação de sítios. 
    Replicação | `*.hypervrecoverymanager.windowsazure.com` | `*.hypervrecoverymanager.windowsazure.com`  |Comunicação VM com o serviço de Recuperação do Local. 
    Service Bus | `*.servicebus.windows.net` | `*.servicebus.usgovcloudapi.net` | VM escreve para a Recuperação do Local para dados de monitorização e diagnóstico. 

4. Se estiver a utilizar grupos de segurança de rede (NSGs) para limitar o tráfego de rede para VMs, crie regras NSG que permitam a conectividade de saída (HTTPS 443) para o VM utilizando estas tags de serviço (grupos de endereços IP). Experimente as regras num teste nSG primeiro.

    **Tag** | **Permitir** 
    --- | --- 
    Etiqueta de armazenamento | Permite que os dados sejam escritos do VM para a conta de armazenamento de cache.
    Etiqueta AD Azure | Permite o acesso a todos os endereços IP que correspondam ao Azure AD.
    Tag EventsHub | Permite o acesso à monitorização da Recuperação do Local.
    Etiqueta AzureSiteRecovery | Permite o acesso ao serviço de Recuperação do Local em qualquer região.
    GuestAndHybridManagement | Utilize se pretender atualizar automaticamente o agente de Mobilidade de Recuperação do Site que está a funcionar em VMs ativados para replicação.
5. Certifique-se de que os VMs têm os últimos certificados de raiz. No Linux VMs, siga as orientações fornecidas pelo seu distribuidor Linux, para obter os mais recentes certificados de raiz fidedignos e lista de revogação de certificados no VM.

## <a name="enable-disaster-recovery"></a>Permitir a recuperação de desastres

1. No portal Azure, abra a página de propriedades VM.
2. Em **Operações** , selecione **Recuperação após desastre**.
3. Na região **Base**  >  **Alvo,** selecione a região à qual pretende replicar o VM. As regiões-alvo e de origem devem estar no mesmo inquilino do Azure Ative Directory.
4. Clique **em Revisão + Iniciar a replicação.**

    :::image type="content" source="./media/tutorial-disaster-recovery/disaster-recovery.png" alt-text="Ativar a replicação na página de recuperação de desastres de propriedades VM.":::

5. Em **Revisão + Iniciar a replicação,** verifique as definições:

    - **Definições do alvo**. Por predefinição, a Recuperação do Site espelha as definições de origem para criar recursos-alvo.
    - **Definições de armazenamento-conta de armazenamento cache**. A recuperação utiliza uma conta de armazenamento na região origem. As alterações de origem VM são cached nesta conta, antes de serem replicadas para a localização do alvo.
    - **Definições de armazenamento-Réplica do disco**. Por predefinição, a Recuperação do Site cria discos geridos por réplicas na região alvo que espelham discos de origem VM geridos com o mesmo tipo de armazenamento (padrão ou premium).
    - **Definições de replicação**. Mostra os detalhes do cofre e indica que os pontos de recuperação criados pela Recuperação do Local são mantidos durante 24 horas.
    - **Definições de extensão**. Indica que a Recuperação do Site irá gerir as atualizações da extensão do Serviço de Mobilidade de Recuperação do Site que está instalada em VMs que replica. A conta de automação Azure indicada gere o processo de atualização.

    :::image type="content" source="./media/tutorial-disaster-recovery/settings-summary.png" alt-text="Página mostrando resumo das definições de alvo e replicação.":::

2. Selecione **a replicação do início**. A implementação começa e a Recuperação do Site começa a criar recursos-alvo. Pode monitorizar o progresso da replicação nas notificações.

    :::image type="content" source="./media/tutorial-disaster-recovery/notifications.png" alt-text="Notificação para o progresso da replicação.":::

## <a name="check-vm-status"></a>Verifique o estado do VM

Após o fim do trabalho de replicação, pode verificar o estado de replicação VM.

1. Abra a página de propriedades VM.
2. Em **Operações** , selecione **Recuperação após desastre**.
3. Expanda a secção **Essentials** para rever os predefinidos sobre o cofre, a política de replicação e as definições de destino.
4. Na **Saúde e no estado** , obtenha informações sobre o estado de replicação para o VM, a versão do agente, a prontidão de failover e os últimos pontos de recuperação. 

    :::image type="content" source="./media/tutorial-disaster-recovery/essentials.png" alt-text="Visão essencial para a recuperação de desastres em VM.":::

5. Na **vista de infraestrutura** , obtenha uma visão geral visual de VMs de origem e alvo, discos geridos e a conta de armazenamento de cache.

    :::image type="content" source="./media/tutorial-disaster-recovery/infrastructure.png" alt-text="mapa visual da infraestrutura para recuperação de desastres VM.":::


## <a name="run-a-drill"></a>Faça um exercício

Faça um exercício para garantir que a recuperação de desastres funcione como esperado. Quando se faz um teste de falha, cria uma cópia do VM, sem impacto na replicação contínua, ou no seu ambiente de produção.

1. Na página de recuperação de desastres em VM, selecione **Test failover**.
2. No **teste de failover** , deixe a definição processada **mais recente (RPO baixa)** para o ponto de recuperação.

   Esta opção fornece o objetivo de ponto de recuperação mais baixo (RPO), e geralmente gira o VM mais rapidamente na região alvo. Primeiro processa todos os dados que foram enviados para o serviço de Recuperação de Sítios, para criar um ponto de recuperação para cada VM, antes de falhar. Este ponto de recuperação tem todos os dados replicados para a Recuperação do Local quando a falha foi desencadeada.

3. Selecione a rede virtual na qual o VM será localizado após a falha. 

     :::image type="content" source="./media/tutorial-disaster-recovery/test-failover-settings.png" alt-text="Página para definir opções de failover de teste.":::

4. O processo de failover do teste começa. Pode monitorizar o progresso nas notificações.

    :::image type="content" source="./media/tutorial-disaster-recovery/test-failover-notification.png" alt-text="Teste notificações de failover."::: 
    
   Após o teste de failover concluído, o VM encontra-se no *teste de limpeza pendente* na página **Essentials.** 
  
## <a name="clean-up-resources"></a>Limpar os recursos

O VM é automaticamente limpo pela Recuperação do Local após a broca. 
 
1. Para iniciar a limpeza automática, selecione **Falha no teste de limpeza**.

    :::image type="content" source="./media/tutorial-disaster-recovery/start-cleanup.png" alt-text="Inicie a limpeza na página Essentials."::: 

6. Na **limpeza de failover test** , escreva em todas as notas que pretende gravar para a falha e, em seguida, selecione **Testes completos. Eliminar a máquina virtual de falha de teste**. Em seguida, selecione **OK**.

    :::image type="content" source="./media/tutorial-disaster-recovery/delete-test.png" alt-text="Página para gravar notas e apagar o VM de teste."::: 

7. O processo de eliminação começa. Pode monitorizar o progresso nas notificações.

    :::image type="content" source="./media/tutorial-disaster-recovery/delete-test-notification.png" alt-text="Notificações para monitorizar a eliminação do VM de teste."::: 


### <a name="stop-replicating-the-vm"></a>Pare de replicar o VM

Depois de completar um exercício de recuperação de desastres, sugerimos que continue a tentar um fracasso total. Se não quiser fazer um fracasso total, pode desativar a replicação. Isto faz o seguinte:

- Remove o VM da lista de recuperação do local de máquinas replicadas.
- Para a faturação de recuperação do local para o VM.
- Limpa automaticamente as definições de replicação de origem.

Pare a replicação da seguinte forma:

1. Na página de recuperação de desastres VM, selecione **Replicação de Desativação**.
2. Na **Replicação de Desativação,** selecione as razões pelas quais pretende desativar a replicação. Em seguida, selecione **OK**.

    :::image type="content" source="./media/tutorial-disaster-recovery/disable-replication.png" alt-text="Página para desativar a replicação e fornecer uma razão."::: 


A extensão de Recuperação do Local instalada no VM durante a replicação não é removida automaticamente. Se desativar a replicação para o VM e não quiser replicá-la mais tarde, pode remover manualmente a extensão de Recuperação do Local, da seguinte forma: 

1. Aceda às extensões de **definições** de >  >  **VM**.
2. Na página **Extensões,** selecione cada entrada *Microsoft.Azure.RecoveryServices* para Linux.
3. Na página de propriedades para a extensão, selecione **Desinstalar**.


## <a name="next-steps"></a>Passos seguintes

Neste tutorial, configuraste a recuperação de desastres para um VM Azure, e fizeste um exercício de recuperação de desastres. Agora, pode fazer uma falha total para o VM.

> [!div class="nextstepaction"]
> [Efetuar a ativação pós-falha de uma VM para outra região](../../site-recovery/azure-to-azure-tutorial-dr-drill.md)
