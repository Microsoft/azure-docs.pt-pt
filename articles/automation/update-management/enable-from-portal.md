---
title: Ativar a Azure Automation Update Management a partir do portal Azure
description: Este artigo diz como ativar a Gestão de Atualização a partir do portal Azure.
services: automation
ms.subservice: update-management
ms.date: 01/07/2021
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 089c5fea6ac4a6fc4fb25af2d631335ef51cf4cc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "99054911"
---
# <a name="enable-update-management-from-the-azure-portal"></a>Ativar a Gestão de Atualizações a partir do portal do Azure

Este artigo descreve como pode ativar a funcionalidade [de Gestão](overview.md) de Atualização para VMs navegando no portal Azure. Para ativar os VMs Azure em escala, tem de ativar um VM Azure existente utilizando a Gestão de Atualização.

O número de grupos de recursos que pode utilizar para gerir os seus VMs é limitado pelos [limites de implementação](../../azure-resource-manager/templates/deploy-to-resource-group.md)do Gestor de Recursos. As implementações do Gestor de Recursos, que não devem ser confundidas com as implementações de Atualização, estão limitadas a cinco grupos de recursos por implementação. Dois destes grupos de recursos estão reservados para configurar o espaço de trabalho Log Analytics, a conta de automação e recursos conexos. Isto deixa-lhe três grupos de recursos para selecionar para gestão por Update Management. Este limite aplica-se apenas à configuração simultânea, e não ao número de grupos de recursos que podem ser geridos por uma funcionalidade de Automação.

> [!NOTE]
> Ao ativar a Gestão de Atualização, apenas certas regiões são suportadas para ligar um espaço de trabalho log Analytics e uma Conta de Automação. Para obter uma lista dos pares de mapeamento suportados, consulte [o mapeamento da Região para a Conta de Automação e o espaço de trabalho log Analytics.](../how-to/region-mappings.md)

## <a name="prerequisites"></a>Pré-requisitos

* Subscrição do Azure. Se ainda não tiver um, pode [ativar os benefícios do seu assinante MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se numa [conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Conta de automatização](../automation-security-overview.md) para gerir máquinas.
* Uma [máquina virtual.](../../virtual-machines/windows/quick-create-portal.md)

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inscreva-se no Azure at https://portal.azure.com .

## <a name="enable-update-management"></a>Ativar a Gestão de Atualizações

1. No portal Azure, navegue para **máquinas Virtuais.**

2. Na página **de máquinas Virtuais,** utilize as caixas de verificação para escolher os VMs para adicionar à Gestão de Atualização. Pode adicionar máquinas para até três grupos de recursos diferentes de cada vez. Os VMs Azure podem existir em qualquer região, independentemente da localização da sua conta Automation.

    ![Lista de VMs](media/enable-from-portal/vmlist.png)

    > [!TIP]
    > Utilize os controlos do filtro para selecionar VMs de diferentes subscrições, locais e grupos de recursos. Pode clicar na caixa de verificação superior para selecionar todas as máquinas virtuais numa lista.

    [![Ativar a gestão de atualização](./media/enable-from-portal/onboard-feature.png)](./media/enable-from-portal/onboard-feature-expanded.png#lightbox)

3. Selecione **Serviços** e selecione **Gestão de Atualização** para a funcionalidade de Gestão de Atualização.

4. A lista de máquinas virtuais é filtrada para mostrar apenas as máquinas virtuais que estão na mesma subscrição e localização. Se as suas máquinas virtuais estiverem em mais de três grupos de recursos, os três primeiros grupos de recursos são selecionados.

5. Um espaço de trabalho e uma conta de Automação do Log Analytics existentes são selecionados por padrão. Se pretender utilizar um espaço de trabalho e uma conta de automação do Log Analytics diferente, selecione **CUSTOM** para selecioná-los na página de Configuração Personalizada. Ao escolher um espaço de trabalho Log Analytics, é feito um cheque para determinar se está ligado a uma conta Demômpa. Se for encontrada uma conta de Automação ligada, vê o seguinte ecrã. Quando terminar, selecione **OK**.

    [![Selecione espaço de trabalho e conta](./media/enable-from-portal/select-workspace-and-account.png)](./media/enable-from-portal/select-workspace-and-account-expanded.png#lightbox)

6. Se o espaço de trabalho selecionado não estiver ligado a uma conta Demôm automação, vê o seguinte ecrã. Selecione uma conta Dem automação e selecione **OK** quando terminar.

    ![Sem espaço de trabalho](media/enable-from-portal/no-workspace.png)

7. Desmarcar qualquer máquina virtual que não queira ativar. VMs que não podem ser ativados já estão desescolhidos.

8. **Selecione Ativar** para ativar a funcionalidade. Depois de ter ativado a Gestão de Atualização, pode demorar cerca de 15 minutos até poder ver a avaliação da atualização a partir deles.

## <a name="next-steps"></a>Passos seguintes

* Para utilizar a Gestão de Atualização para VMs, consulte [Gerir atualizações e patches para os seus VMs](manage-updates-for-vm.md).
* Para resolver os erros gerais de Gestão de Atualização, consulte [problemas de Gestão de Atualização de Resolução de Problemas](../troubleshoot/update-management.md).
* Para resolver problemas com o agente de atualização do Windows, consulte [problemas de agente de atualização do Windows de resolução de resolução](../troubleshoot/update-agent-issues.md)de problemas .
* Para resolver problemas com o agente de atualização Linux, consulte [problemas de agente de atualização do Linux de resolução](../troubleshoot/update-agent-issues-linux.md)de resolução de problemas .