---
title: Criar/personalizar planos de recuperação na Recuperação do Local de Azure
description: Saiba como criar e personalizar planos de recuperação para recuperação de desastres utilizando o serviço de Recuperação do Local Azure.
ms.topic: how-to
ms.date: 01/23/2020
ms.openlocfilehash: 0dcde98e8dcaef12896c18c25429f0ba7b1b27d4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96009726"
---
# <a name="create-and-customize-recovery-plans"></a>Criar e personalizar planos de recuperação

Este artigo descreve como criar e personalizar um plano de recuperação para o failover na [Recuperação do Sítio Azure](site-recovery-overview.md). Antes de começar, [saiba mais](recovery-plan-overview.md) sobre planos de recuperação.

## <a name="create-a-recovery-plan"></a>Criar um plano de recuperação

1. No cofre dos Serviços de Recuperação, selecione **Planos de Recuperação (Recuperação do Local)**  >  **+Plano de Recuperação**.
2. No **Plano de Recuperação,** especifique um nome para o plano.
3. Escolha uma fonte e um alvo com base nas máquinas do plano e selecione **Gestor de Recursos** para o modelo de implementação. A localização da fonte deve ter máquinas habilitados para a falha e recuperação. 

    **Ativação pós-falha** | **Origem** | **Destino** 
   --- | --- | ---
   Azure para o Azure | Selecione a região de Azure | Selecione a região de Azure
   VMware para Azure | Selecione o servidor de configuração | Selecione Azure
   Máquinas físicas para Azure | Selecione o servidor de configuração | Selecione Azure   
   Hyper-V para o Azure | Selecione o nome do site Hyper-V | Selecione Azure
   Hiper-V (gerido por VMM) para Azure  | Selecione o servidor VMM | Selecione Azure
  
    Tenha em atenção o seguinte:
    - Pode usar um plano de recuperação para falhar para Azure e falhar com o Azure.
    - A localização da fonte deve ter máquinas habilitados para a falha e recuperação.
    - Um plano de recuperação pode conter máquinas com a mesma fonte e alvo.
    - Pode incluir VMware VMs e VMs hiper-V geridos pela VMM, no mesmo plano.
    - VMware VMs e servidores físicos podem estar no mesmo plano.

4. Em **Selecionar máquinas virtuais,** selecione as máquinas (ou grupo de replicação) que pretende adicionar ao plano. Em seguida, clique em **OK**.
    - As máquinas são adicionadas ao grupo predefinido (Grupo 1) no plano. Depois do fracasso, todas as máquinas deste grupo começam ao mesmo tempo.
    - Só é possível selecionar máquinas nas localizações de origem e alvo que especificou. 
5. Clique **em OK** para criar o plano.

## <a name="add-a-group-to-a-plan"></a>Adicione um grupo a um plano

Cria grupos adicionais e adiciona máquinas a diferentes grupos para que possa especificar comportamentos diferentes numa base grupo a grupo. Por exemplo, pode especificar quando as máquinas de um grupo devem começar após a falha, ou especificar ações personalizadas por grupo.

1. Em **Planos de Recuperação,** clique à direita no plano > **Personalizar.** Por predefinição, depois de criar um plano, todas as máquinas que adicionou ao mesmo estão localizadas no Grupo 1 predefinido.
2. Clique **em +Grupo**. Por predefinição, um novo grupo é numerado na ordem em que é adicionado. Pode ter até sete grupos.
3. Selecione a máquina que pretende mover-se para o novo grupo, clique em **Alterar grupo** e, em seguida, selecione o novo grupo. Em alternativa, clique com o nome do grupo > **Produto Protegido** e adicione máquinas ao grupo. Uma máquina ou grupo de replicação só pode pertencer a um grupo num plano de recuperação.


## <a name="add-a-script-or-manual-action"></a>Adicione um script ou ação manual

Pode personalizar um plano de recuperação adicionando um script ou ação manual. Tenha em atenção que:

- Se estiver a replicar para o Azure, pode integrar os runbooks de automação Azure no seu plano de recuperação. [Saiba mais](site-recovery-runbook-automation.md).
- Se estiver a replicar VMs Hiper-V geridos pelo System Center VMM, pode criar um script no servidor VMM no local e incluí-lo no plano de recuperação.
- Quando adiciona um script, adiciona um novo conjunto de ações para o grupo. Por exemplo, é criado um conjunto de pré-etapas para o Grupo 1 com o nome *Grupo 1: pré-etapas*. Todos os pré-passos estão listados dentro deste conjunto. Só pode adicionar um script no site principal se tiver um servidor VMM implantado.
- Se adicionar uma ação manual, quando o plano de recuperação for executado, para no ponto em que inseriu a ação manual. Uma caixa de diálogo solicita-lhe que especifique que a ação manual foi concluída.
- Para criar um script no servidor VMM, siga as instruções [deste artigo](hyper-v-vmm-recovery-script.md).
- Os scripts podem ser aplicados durante o failover para o site secundário, e durante o recuo do site secundário para o primário. O suporte depende do seu cenário de replicação:
    
    **Cenário** | **Ativação pós-falha** | **Recuo**
    --- | --- | --- 
    Azure para o Azure  | Runbook | Runbook
    VMware para o Azure | Runbook | ND 
    Hiper-V com VMM a azul | Runbook | Script
    Site Hyper-V para o Azure | Runbook | ND
    VMM para VMM secundário | Script | Script

1. No plano de recuperação, clique no passo a que a ação deve ser adicionada e especifique quando a ação deve ocorrer:
    1. Se pretender que a ação ocorra antes de as máquinas do grupo serem iniciadas após o failover, **selecione Adicionar pré-acção**.
    1. Se pretender que a ação ocorra após o início das máquinas do grupo após o início do failover, **selecione Adicione a ação de post .** Para mover a posição da ação, selecione os botões **Move Up** ou **Move Down.**
2. **Insira a ação**, selecione **Script** ou **Ação Manual**.
3. Se quiser adicionar uma ação manual, faça o seguinte:
    1. Digite um nome para a ação e escreva instruções de ação. A pessoa que executa o failover verá estas instruções.
    1. Especificar se pretende adicionar a ação manual para todos os tipos de failover (Teste, Failover, Failover, Failover (se relevante)). Em seguida, clique em **OK**.
4. Se quiser adicionar um script, faça o seguinte:
    1. Se adicionar um script VMM, selecione **Failover para O script VMM** e no **Script Path** digite o caminho relativo para a partilha. Por exemplo, se a partilha estiver localizada em \\ \<VMMServerName> \MSSCVMMLibrary\RPScripts, especifique o caminho: \RPScripts\RPScript.PS1.
    1. Se adicionar um livro de funcionamento da automatização Azure, especifique a **Conta de Automação Azure** em que o livro de execução está localizado e selecione o **Roteiro do Runbook Azure** apropriado .
5. Executar um teste de falha do plano de recuperação para garantir que o script funciona como esperado.

## <a name="watch-a-video"></a>Veja um vídeo

Veja um vídeo que demonstra como construir um plano de recuperação.


> [!VIDEO https://www.youtube.com/embed/1KUVdtvGqw8]

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [execução de falhas.](site-recovery-failover.md)  

    
