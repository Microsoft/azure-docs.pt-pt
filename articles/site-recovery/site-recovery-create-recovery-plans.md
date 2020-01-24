---
title: Criar/personalizar planos de recuperação na Recuperação do Site Azure
description: Saiba como criar e personalizar planos de recuperação para recuperação de desastres utilizando o serviço de Recuperação de Sítios Azure.
ms.topic: how-to
ms.date: 01/23/2020
ms.openlocfilehash: 6540317324a9f0d9bccc046ecf95824d4128bd09
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76705841"
---
# <a name="create-and-customize-recovery-plans"></a>Criar e personalizar planos de recuperação

Este artigo descreve como criar e personalizar um plano de recuperação para falha na Recuperação do [Site Azure.](site-recovery-overview.md) Antes de começar, [saiba mais](recovery-plan-overview.md) sobre os planos de recuperação.

## <a name="create-a-recovery-plan"></a>Criar um plano de recuperação

1. No cofre dos Serviços de Recuperação, selecione Planos de **Recuperação (Recuperação do Local)**  > +Plano de **Recuperação**.
2. No **plano de recuperação Create,** especifique um nome para o plano.
3. Escolha uma fonte e um alvo com base nas máquinas do plano e selecione **Gestor de Recursos** para o modelo de implementação. A localização de origem deve ter máquinas habilitadas para a falha e recuperação. 

    **Ativação pós-falha** | **Origem** | **Destino** 
   --- | --- | ---
   Azure para o Azure | Selecione a região de Azure | Selecione a região de Azure
   VMware para Azure | Selecione o servidor de configuração | Selecione Azure
   Máquinas físicas para Azure | Selecione o servidor de configuração | Selecione Azure   
   Hyper-V para o Azure | Selecione o nome do site Hyper-V | Selecione Azure
   Hyper-V (gerido por VMM) para Azure  | Selecione o servidor VMM | Selecione Azure
  
    Tenha em atenção o seguinte:
    -  Só pode utilizar um plano de recuperação para a falha da localização de origem para o Azure. Não podes usar um plano de recuperação para o regresso do Azure.
    - A localização de origem deve ter máquinas habilitadas para a falha e recuperação. 
    - Um plano de recuperação pode conter máquinas com a mesma fonte e alvo. 
    - Pode incluir VMware VMs e VMs Hiper-V geridos pela VMM, no mesmo plano.
    - VMware VMs e servidores físicos podem estar no mesmo plano.

4. Em **Selecione itens máquinas virtuais,** selecione as máquinas (ou grupo de replicação) que pretende adicionar ao plano. Em seguida, clique em **OK**.
    - As máquinas são adicionadas ao grupo padrão (Grupo 1) no plano. Após a falha, todas as máquinas deste grupo começam ao mesmo tempo.
    - Só é possível selecionar máquinas nos locais de origem e alvo que especificou. 
5. Clique **em OK** para criar o plano.

## <a name="add-a-group-to-a-plan"></a>Adicione um grupo a um plano

Cria grupos adicionais e adiciona máquinas a diferentes grupos para que possa especificar comportamentos diferentes numa base de grupo a grupo. Por exemplo, pode especificar quando as máquinas de um grupo devem começar após a falha, ou especificar ações personalizadas por grupo.

1. Em Planos de **Recuperação,** clique à direita no plano > **Personalizar**. Por padrão, depois de criar um plano, todas as máquinas que adicionou estão localizadas no Grupo 1 predefinido.
2. Clique em **+Grupo**. Por defeito, um novo grupo é numerado na ordem em que é adicionado. Pode ter até sete grupos.
3. Selecione a máquina que pretende mover para o novo grupo, clique em mudar de **grupo**e, em seguida, selecione o novo grupo. Em alternativa, clique à direita no nome do grupo > **artigo protegido,** e adicione máquinas ao grupo. Uma máquina ou grupo de replicação só pode pertencer a um grupo num plano de recuperação.


## <a name="add-a-script-or-manual-action"></a>Adicione um script ou ação manual

Pode personalizar um plano de recuperação adicionando um script ou ação manual. Tenha em atenção que:

- Se estiver a replicar-se no Azure, pode integrar os livros de automação Azure no seu plano de recuperação. [Saiba mais](site-recovery-runbook-automation.md).
- Se estiver a replicar VMs Hiper-V geridos pelo System Center VMM, pode criar um script no servidor VMM no local e incluí-lo no plano de recuperação.
- Quando adiciona um guião, adiciona um novo conjunto de ações para o grupo. Por exemplo, é criado um conjunto de pré-passos para o Grupo 1 com o nome *Grupo 1: pré-passos*. Todos os pré-passos estão listados dentro deste conjunto. Só pode adicionar um script no site principal se tiver um servidor VMM implantado.
- Se adicionar uma ação manual, quando o plano de recuperação funciona, para no ponto em que inseriu a ação manual. Uma caixa de diálogo leva-o a especificar que a ação manual foi concluída.
- Para criar um script no servidor VMM, siga as instruções [deste artigo](hyper-v-vmm-recovery-script.md).
- Os scripts podem ser aplicados durante a falha no local secundário, e durante o failback do local secundário para o primário. O suporte depende do seu cenário de replicação:
    
    **Cenário** | **Ativação pós-falha** | **Reativação pós-falha**
    --- | --- | --- 
    Azure para o Azure  | Runbook | Runbook
    VMware para o Azure | Runbook | N/D 
    Hiper-V com VMM para Azure | Runbook | Script
    Site do Hyper-V para o Azure | Runbook | N/D
    VMM para VMM secundário | Script | Script

1. No plano de recuperação, clique no passo a que a ação deve ser adicionada e especifique quando deve ocorrer a ação:
    1. Se desejar que a ação ocorra antes de as máquinas do grupo serem iniciadas após a falha, **selecione Adicionar pré-acção**.
    1. Se desejar que a ação ocorra após o início das máquinas no grupo após a falha, selecione **Adicionar a ação do post**. Para mover a posição da ação, selecione os botões **Move Up** ou **Move Down.**
2. Na **ação Insira,** selecione **script** ou **ação manual**.
3. Se quiser adicionar uma ação manual, faça o seguinte:
    1. Digite um nome para a ação e escreva instruções de ação. A pessoa que executa o failover verá estas instruções.
    1. Especifique se pretende adicionar a ação manual para todos os tipos de failover (Teste, Failover, Failover Planeado (se for relevante)). Em seguida, clique em **OK**.
4. Se quiser adicionar um guião, faça o seguinte:
    1. Se estiver a adicionar um script VMM, selecione **Failover para vMM script**, e no **Script Path** escreva o caminho relativo para a partilha. Por exemplo, se a parte estiver localizada em \\\<VMMServerName>\MSSCVMMLibrary\RPScripts, especifique o caminho: \RPScripts\RPScripts.PS1.
    1. Se estiver a adicionar um livro de execução de automação Azure, especifique a **Conta de Automação Azure** na qual o livro de execução está localizado e selecione o script de **runbook azure**apropriado .
5. Execute uma falha de teste do plano de recuperação para garantir que o script funcione como esperado.

## <a name="watch-a-video"></a>Ver um vídeo

Veja um vídeo que demonstre como construir um plano de recuperação.


> [!VIDEO https://www.youtube.com/embed/1KUVdtvGqw8]

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [executar falhas.](site-recovery-failover.md)  

    
