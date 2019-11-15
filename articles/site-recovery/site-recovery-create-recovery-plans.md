---
title: Criar/Personalizar planos de recuperação no Azure Site Recovery
description: Saiba como criar e personalizar planos de recuperação para recuperação de desastre usando o serviço de Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 11/14/2019
ms.author: raynew
ms.openlocfilehash: 9bb5a1a3aa0c2a4681ddecb5e20df41d481755ec
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74084514"
---
# <a name="create-and-customize-recovery-plans"></a>Criar e personalizar planos de recuperação

Este artigo descreve como criar e personalizar um plano de recuperação no [Azure site Recovery](site-recovery-overview.md). Antes de começar, [saiba mais](recovery-plan-overview.md) sobre os planos de recuperação.

## <a name="create-a-recovery-plan"></a>Criar um plano de recuperação

1. No cofre dos serviços de recuperação, selecione **planos de recuperação (site Recovery)**  >  **+ plano de recuperação**.
2. Em **criar plano de recuperação**, especifique um nome para o plano.
3. Escolha uma origem e um destino com base nas máquinas no plano e selecione **Gerenciador de recursos** para o modelo de implantação. O local de origem deve ter computadores habilitados para failover e recuperação. 

   **Ativação pós-falha** | **Origem** | **Destino** 
   --- | --- | ---
   Azure para o Azure | Região do Azure |Região do Azure
   VMware para Azure | Servidor de configuração | Azure
   Computadores físicos para o Azure | Servidor de configuração | Azure   
   Hyper-V gerenciado pelo VMM para o Azure  | Nome de exibição do VMM | Azure
   Hyper-V sem VMM para Azure | Nome do site do Hyper-V | Azure
   VMM para VMM |Nome amigável do VMM | Nome de exibição do VMM 

   > [!NOTE]
   > Um plano de recuperação pode conter computadores com a mesma origem e destino. As VMs do VMware e do Hyper-V gerenciadas pelo VMM não podem estar no mesmo plano. As VMs VMware e os servidores físicos podem estar no mesmo plano, onde a origem é um servidor de configuração.

2. Em **selecionar itens máquinas virtuais**, selecione os computadores (ou grupo de replicação) que você deseja adicionar ao plano. Em seguida, clique em **OK**.
    - Os computadores são adicionados ao grupo padrão (grupo 1) no plano. Após o failover, todos os computadores neste grupo são iniciados ao mesmo tempo.
    - Você só pode selecionar computadores que estejam nos locais de origem e de destino que você especificou. 
1. Clique em **OK** para criar o plano.

## <a name="add-a-group-to-a-plan"></a>Adicionar um grupo a um plano

Crie grupos adicionais e adicione computadores a grupos diferentes para que você possa especificar um comportamento diferente em um grupo por grupo. Por exemplo, você pode especificar quando os computadores em um grupo devem ser iniciados após o failover ou especificar ações personalizadas por grupo.

1. Em **planos de recuperação**, clique com o botão direito do mouse no plano > **Personalizar**. Por padrão, depois de criar um plano, todos os computadores adicionados a ele estão localizados no grupo padrão 1.
2. Clique em **+ grupo**. Por padrão, um novo grupo é numerado na ordem em que é adicionado. Você pode ter até sete grupos.
3. Selecione o computador que você deseja mover para o novo grupo, clique em **alterar grupo**e selecione o novo grupo. Como alternativa, clique com o botão direito do mouse no nome do grupo > **item protegido**e adicione computadores ao grupo. Um computador ou grupo de replicação só pode pertencer a um grupo em um plano de recuperação.


## <a name="add-a-script-or-manual-action"></a>Adicionar um script ou ação manual

Você pode personalizar um plano de recuperação adicionando um script ou uma ação manual. Tenha em atenção que:

- Se estiver replicando para o Azure, você poderá integrar runbooks de automação do Azure em seu plano de recuperação. [Saiba mais](site-recovery-runbook-automation.md).
- Se você estiver replicando VMs Hyper-V gerenciadas pelo System Center VMM, poderá criar um script no servidor VMM local e incluí-lo no plano de recuperação.
- Quando você adiciona um script, ele adiciona um novo conjunto de ações para o grupo. Por exemplo, um conjunto de etapas para o grupo 1 é criado com o nome *grupo 1: pré-etapas*. Todas as etapas anteriores são listadas dentro desse conjunto. Você pode adicionar um script no site primário somente se tiver um servidor VMM implantado.
- Se você adicionar uma ação manual, quando o plano de recuperação for executado, ele será interrompido no ponto em que você inseriu a ação manual. Uma caixa de diálogo solicita que você especifique que a ação manual foi concluída.
- Para criar um script no servidor do VMM, siga as instruções neste [artigo](hyper-v-vmm-recovery-script.md).
- Os scripts podem ser aplicados durante o failover para o site secundário e durante o failback do site secundário para o primário. O suporte depende do seu cenário de replicação:
    
    **Cenário** | **Ativação pós-falha** | **Reativação pós-falha**
    --- | --- | --- 
    Azure para o Azure  | Runbook | Runbook
    VMware para o Azure | Runbook | ND 
    Hyper-V com o VMM para o Azure | Runbook | Script
    Site Hyper-V para o Azure | Runbook | ND
    VMM para VMM secundário | Script | Script

1. No plano de recuperação, clique na etapa à qual a ação deve ser adicionada e especifique quando a ação deve ocorrer:
    1. Se você quiser que a ação ocorra antes que as máquinas no grupo sejam iniciadas após o failover, selecione **Adicionar ação prévia**.
    1. Se você quiser que a ação ocorra depois que as máquinas no grupo forem iniciadas após o failover, selecione **Adicionar ação de postagem**. Para mover a posição da ação, selecione os botões **mover para cima** ou **mover para baixo** .
2. Em **Inserir ação**, selecione **script** ou **ação manual**.
3. Se você quiser adicionar uma ação manual, faça o seguinte:
    1. Digite um nome para a ação e digite as instruções de ação. A pessoa que está executando o failover verá essas instruções.
    1. Especifique se você deseja adicionar a ação manual para todos os tipos de failover (teste, failover, failover planejado (se relevante)). Em seguida, clique em **OK**.
4. Se você quiser adicionar um script, faça o seguinte:
    1. Se você estiver adicionando um script do VMM, selecione **failover para o script do VMM**e, em caminho do **script** , digite o caminho relativo para o compartilhamento. Por exemplo, se o compartilhamento estiver localizado em \\\<VMMServerName > \MSSCVMMLibrary\RPScripts, especifique o caminho: \RPScripts\RPScript.PS1.
    1. Se você estiver adicionando um livro de execução de automação do Azure, especifique a **conta de automação do Azure** na qual o runbook está localizado e selecione o **script de runbook do Azure**apropriado.
5. Execute um failover de teste do plano de recuperação para garantir que o script funcione conforme o esperado.

## <a name="watch-a-video"></a>Ver um vídeo

Assista a um vídeo que demonstra como criar um plano de recuperação.


> [!VIDEO https://www.youtube.com/embed/1KUVdtvGqw8]

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre a [execução de failovers](site-recovery-failover.md).  

    
