---
title: Criar e personalizar planos de recuperação para a recuperação após desastre com o Azure Site Recovery | Documentos da Microsoft
description: Saiba como criar e personalizar planos de recuperação para recuperação após desastre com o serviço Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
services: site-recovery
ms.service: site-recovery
ms.topic: article
ms.date: 12/27/2018
ms.author: raynew
ms.openlocfilehash: d52aa3b39a17c42c0f0e0cb669c69d336b41ba48
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61035827"
---
# <a name="create-and-customize-recovery-plans"></a>Criar e personalizar planos de recuperação

Este artigo descreve como criar e personalizar um plano de recuperação no [do Azure Site Recovery](site-recovery-overview.md). Antes de começar, [Saiba mais](recovery-plan-overview.md) sobre os planos de recuperação.

## <a name="create-a-recovery-plan"></a>Criar um plano de recuperação

1. No cofre dos serviços de recuperação, selecione **(recuperação de sites) de planos de recuperação** > **+ plano de recuperação**.
2. Na **criar plano de recuperação**, especifique um nome para o plano.
3. Escolha uma origem e de destino com base nas máquinas no plano e selecione **Resource Manager** para o modelo de implementação. A localização de origem tem de ter máquinas que estão ativadas para ativação pós-falha e recuperação. 

   **Ativação pós-falha** | **Origem** | **Target** 
   --- | --- | ---
   Azure para o Azure | Região do Azure |Região do Azure
   VMware para o Azure | Servidor de configuração | Azure
   Máquinas físicas para o Azure | Servidor de configuração | Azure   
   Hyper-V gerida pelo VMM para o Azure  | Nome a apresentar do VMM | Azure
   Hyper-V sem o VMM para o Azure | Nome de site Hyper-V | Azure
   VMM para o VMM |Nome amigável do VMM | Nome a apresentar do VMM 

   > [!NOTE]
   > Um plano de recuperação pode conter máquinas com a mesma origem e destino. VMware e VMs de Hyper-V geridas pelo VMM não podem estar no mesmo plano. VMs de VMware e servidores físicos podem estar no mesmo plano, em que a origem é um servidor de configuração.

2. Na **selecione itens de máquinas virtuais**, selecione os computadores (ou grupo de replicação) que pretende adicionar ao plano. Em seguida, clique em **OK**.
    - Grupo predefinido (grupo 1) são adicionadas máquinas no plano. Após a ativação pós-falha, todas as máquinas neste grupo iniciam-se ao mesmo tempo.
    - Só pode selecionar máquinas estão a ser as localizações de origem e de destino que especificou. 
1. Clique em **OK** para criar o plano.

## <a name="add-a-group-to-a-plan"></a>Adicionar um grupo para um plano

Criar grupos adicionais e adicionar máquinas para diferentes grupos, para que pode especificar um comportamento diferente numa base por grupo. Por exemplo, pode especificar quando as máquinas num grupo de devem iniciar após a ativação pós-falha ou especificar ações personalizadas por grupo.

1. Na **planos de recuperação**, clique com o botão direito do plano > **personalizar**. Por predefinição, depois de criar um plano de todas as máquinas que adicionou à mesma estão localizados em default grupo 1.
2. Clique em **+ grupo**. Por predefinição, um novo grupo esteja enumerado na ordem em que é adicionado. Pode ter até sete grupos.
3. Selecione a máquina que pretende mover para o novo grupo, clique em **alterar grupo**e, em seguida, selecione o novo grupo. Em alternativa, clique com botão direito no nome do grupo > **item protegido**e adicionar máquinas ao grupo. Um grupo de replicação ou máquina só pode pertencer a um grupo num plano de recuperação.


## <a name="add-a-script-or-manual-action"></a>Adicionar uma ação de script ou manual

Pode personalizar um plano de recuperação através da adição de um script ou a ação manual. Tenha em atenção que:

- Se estiver a replicar para o Azure pode integrar os runbooks de automatização do Azure no seu plano de recuperação. [Saiba mais](site-recovery-runbook-automation.md).
- Se estiver a replicar VMs de Hyper-V geridas pelo VMM do System Center, pode criar um script no servidor do VMM no local e incluí-lo no plano de recuperação.
- Quando adiciona um script, ele adiciona um novo conjunto de ações para o grupo. Por exemplo, é criado um conjunto de passos anteriores para o grupo 1 com o nome *grupo 1: passos anteriores*. Todos os pré-passos de estão listados dentro deste conjunto. Pode adicionar um script no site primário, somente se tiver um servidor VMM implementado.
- Se adicionar uma ação manual, quando o plano de recuperação é executado, ele será interrompido no ponto em que tiver inserido a ação manual. Uma caixa de diálogo pede-lhe para especificar que a ação manual foi concluída.
- Para criar um script no servidor do VMM, siga as instruções em [este artigo](hyper-v-vmm-recovery-script.md).
- Scripts podem ser aplicados durante a ativação pós-falha para o site secundário e durante a reativação pós-falha do site secundário para o primário. Suporte depende do seu cenário de replicação:
    
    **Cenário** | **Ativação pós-falha** | **Reativação pós-falha**
    --- | --- | --- 
    Azure para o Azure  | Runbook | Runbook
    VMware para o Azure | Runbook | ND 
    Hyper-V com o VMM para o Azure | Runbook | Script
    Site Hyper-V para o Azure | Runbook | ND
    VMM para VMM secundário | Script | Script

1. No plano de recuperação, clique no passo para o qual a ação deve ser adicionado e especificar quando a ação precisa ocorrer: um. Se pretender que a ação para ocorrer antes das máquinas no grupo são iniciadas após a ativação pós-falha, selecione **adicionar pré-ação**.
    b. Se pretender que a ação de ocorrer após as máquinas no grupo início após a ativação pós-falha, selecione **adicionar ação posterior**. Para mover a posição da ação, selecione o **mover para cima** ou **mover para baixo** botões.
2. Na **inserir ação**, selecione **Script** ou **ação Manual**.
3. Se pretender adicionar uma ação manual, efetue o seguinte procedimento"um. Escreva um nome para a ação e escreva instruções da ação. A pessoa a executar a ativação pós-falha irá ver estas instruções.
    b. Especifique se pretende adicionar a ação manual para todos os tipos de ativação pós-falha (teste, ativação pós-falha, a ativação pós-falha planeada (se relevante)). Em seguida, clique em **OK**.
4. Se pretender adicionar um script, faça o seguinte: um. Se estiver a adicionar um script do VMM, selecione **ativação pós-falha para o script do VMM**e, na **caminho do Script** escreva o caminho relativo para a partilha. Por exemplo, se a partilha está localizada em \\ <VMMServerName>\MSSCVMMLibrary\RPScripts, especifique o caminho: \RPScripts\RPScript.PS1.
    b. Se estiver a adicionar uma livro de executar a automatização do Azure, especifique a **conta de automatização do Azure** em que o runbook está localizado e selecionar a adequada **Script do Runbook do Azure**.
5. Execute uma ativação pós-falha do plano de recuperação para garantir que o script funcione conforme esperado.

## <a name="watch-a-video"></a>Ver um vídeo

Ver um vídeo que demonstra como criar um plano de recuperação.


> [!VIDEO https://www.youtube.com/embed/1KUVdtvGqw8]

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre [executar as ativações pós-falha](site-recovery-failover.md).  

    
