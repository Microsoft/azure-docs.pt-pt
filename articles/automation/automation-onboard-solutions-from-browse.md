---
title: Ativar a Azure Automation Update Management a partir do portal Azure
description: Este artigo diz como ativar a Gestão de Atualização a partir do portal Azure.
services: automation
ms.date: 04/11/2019
ms.topic: article
ms.custom: mvc
ms.openlocfilehash: 6eab9b543bfec90c0c338bdc758d690499f083e3
ms.sourcegitcommit: 0fa52a34a6274dc872832560cd690be58ae3d0ca
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/29/2020
ms.locfileid: "84204927"
---
# <a name="enable-update-management-from-the-azure-portal"></a>Ativar a Gestão de Atualizações a partir do portal do Azure

Este artigo descreve como pode ativar a funcionalidade [de Gestão](automation-update-management.md) de Atualização para VMs navegando no portal Azure. Para ativar os VMs Azure em escala, tem de ativar um VM existente utilizando a Gestão de Atualização. 

O número de grupos de recursos que pode utilizar para gerir os seus VMs é limitado pelos [limites de implementação](../azure-resource-manager/templates/cross-resource-group-deployment.md)do Gestor de Recursos. As implementações do Gestor de Recursos, que não devem ser confundidas com as implementações de Atualização, estão limitadas a cinco grupos de recursos por implementação. Dois destes grupos de recursos estão reservados para configurar o espaço de trabalho Log Analytics, a conta de automação e recursos conexos. Isto deixa-lhe três grupos de recursos para selecionar para gestão por Update Management. Este limite aplica-se apenas à configuração simultânea, e não ao número de grupos de recursos que podem ser geridos por uma funcionalidade de Automação.

> [!NOTE]
> Ao ativar a Gestão de Atualização, apenas certas regiões são suportadas para ligar um espaço de trabalho log Analytics e uma Conta de Automação. Para obter uma lista dos pares de mapeamento suportados, consulte [o mapeamento da Região para a Conta de Automação e o espaço de trabalho log Analytics.](how-to/region-mappings.md)

## <a name="prerequisites"></a>Pré-requisitos

* Subscrição do Azure. Se ainda não tiver um, pode [ativar os benefícios do seu assinante MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se numa [conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Conta de automatização](automation-offering-get-started.md) para gerir máquinas.
* Uma [máquina virtual.](../virtual-machines/windows/quick-create-portal.md)

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inscreva-se no Azure at https://portal.azure.com .

## <a name="enable-update-management"></a>Ativar a Gestão de Atualizações

1. No portal Azure, navegue para **máquinas Virtuais.**

2. Utilize as caixas de verificação para escolher os VMs para adicionar à Gestão de Atualização. Pode adicionar máquinas para até três grupos de recursos diferentes de cada vez. Os VMs Azure podem existir em qualquer região, independentemente da localização da sua conta Automation.

    ![Lista de VMs](media/automation-onboard-solutions-from-browse/vmlist.png)

    > [!TIP]
    > Utilize os controlos do filtro para selecionar VMs de diferentes subscrições, locais e grupos de recursos. Pode clicar na caixa de verificação superior para selecionar todas as máquinas virtuais numa lista.

    ![Ativar a Gestão de Atualizações](media/automation-onboard-solutions-from-browse/onboardsolutions.png)

3. Clique **em Serviços** e selecione **Gestão de Atualização** para a funcionalidade de Gestão de Atualização. 

4. A lista de máquinas virtuais é filtrada para mostrar apenas as máquinas virtuais que estão na mesma subscrição e localização. Se as suas máquinas virtuais estiverem em mais de três grupos de recursos, os três primeiros grupos de recursos são selecionados.

5. Um espaço de trabalho e uma conta de Automação do Log Analytics existentes são selecionados por padrão. Se pretender utilizar um espaço de trabalho e uma conta de automação do Log Analytics diferente, clique em **CUSTOM** para selecioná-los na página de Configuração Personalizada. Ao escolher um espaço de trabalho Log Analytics, é feito um cheque para determinar se está ligado a uma conta Demômpa. Se for encontrada uma conta de Automação ligada, vê o seguinte ecrã. Quando terminar, clique **em OK**.

    ![Selecione espaço de trabalho e conta](media/automation-onboard-solutions-from-browse/selectworkspaceandaccount.png)

6. Se o espaço de trabalho selecionado não estiver ligado a uma conta Demôm automação, vê o seguinte ecrã. Selecione uma conta Dem automação e clique **em OK** quando terminar.

    ![Sem espaço de trabalho](media/automation-onboard-solutions-from-browse/no-workspace.png)

7. Desmarcar a caixa de verificação ao lado de qualquer máquina virtual que não queira ativar. VMs que não podem ser ativados já estão desescolhidos.

8. Clique **em Ativar** para ativar a funcionalidade selecionada. A configuração leva até 15 minutos para ser concluída.

## <a name="next-steps"></a>Passos seguintes

* Para utilizar a Gestão de Atualização para VMs, consulte [Gerir atualizações e patches para os seus VMs Azure](automation-tutorial-update-management.md).
* Para resolver os erros gerais de Gestão de Atualização, consulte [problemas de Gestão de Atualização de Resolução de Problemas](troubleshoot/update-management.md).
* Para resolver problemas com o agente de atualização do Windows, consulte [problemas de agente de atualização do Windows de resolução de resolução](troubleshoot/update-agent-issues.md)de problemas .
* Para resolver problemas com o agente de atualização Linux, consulte [problemas de agente de atualização do Linux de resolução](troubleshoot/update-agent-issues-linux.md)de resolução de problemas .
