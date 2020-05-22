---
title: Ativar o rastreio e inventário de alterações de automatização azure do portal Azure
description: Este artigo diz como ativar a funcionalidade de Rastreio e Inventário de Alterações do portal Azure.
services: automation
ms.date: 04/11/2019
ms.topic: article
ms.custom: mvc
ms.openlocfilehash: b53c63fc1a14c456e0bca0725ca29b311301ce66
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83749082"
---
# <a name="enable-change-tracking-and-inventory-from-the-azure-portal"></a>Ativar o rastreio e o inventário de alterações do portal Azure

Este artigo descreve como pode ativar a funcionalidade de Rastreio e Inventário de [Alterações](change-tracking.md) para VMs através da navegação no portal Azure. Para ativar os VMs Azure em escala, deve ativar um VM existente utilizando o Change Tracking e o Inventory. 

O número de grupos de recursos que pode utilizar para gerir os seus VMs é limitado pelos limites de implementação do Gestor de [Recursos.](../azure-resource-manager/templates/cross-resource-group-deployment.md) As implementações do Gestor de Recursos, não confundir com as implementações de atualizações, estão limitadas a cinco grupos de recursos por implantação. Dois destes grupos de recursos estão reservados para configurar o espaço de trabalho log Analytics, a conta de Automação e recursos relacionados. Isto deixa-o com três grupos de recursos para selecionar para gestão por Change Tracking e Inventário. Este limite aplica-se apenas à configuração simultânea, não ao número de grupos de recursos que podem ser geridos por uma funcionalidade de Automação.

> [!NOTE]
> Ao permitir o rastreio e inventário de alterações, apenas certas regiões são suportadas para ligar um espaço de trabalho log Analytics e uma Conta de Automação. Para obter uma lista dos pares de mapeamento suportados, consulte [O Mapeamento da Região para A Conta de Automação e espaço de trabalho log Analytics](how-to/region-mappings.md).

## <a name="prerequisites"></a>Pré-requisitos

* Subscrição do Azure. Se ainda não tiver um, pode ativar os seus benefícios de [subscrição da MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se para uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Conta de automatização](automation-offering-get-started.md) para gerir máquinas.
* Uma [máquina virtual.](../virtual-machines/windows/quick-create-portal.md)

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inscreva-se no Azure em https://portal.azure.com .

## <a name="enable-change-tracking-and-inventory"></a>Ativar o Controlo de Alterações e Inventário

1. No portal Azure, navegue para **máquinas virtuais.**

2. Utilize as caixas de verificação para escolher os VMs para adicionar ao Change Tracking e Ao Inventário. Pode adicionar máquinas para até três grupos de recursos diferentes de cada vez. Os VMs Azure podem existir em qualquer região, independentemente da localização da sua conta Deautomação.

    ![Lista de VMs](media/automation-enable-changes-from-browse/vmlist.png)

    > [!TIP]
    > Utilize os controlos de filtro para selecionar VMs de diferentes subscrições, locais e grupos de recursos. Pode clicar na caixa de verificação superior para selecionar todas as máquinas virtuais numa lista.

3. Selecione **alterar o rastreio** ou o **inventário** sob a gestão de **configuração**.

4. A lista de máquinas virtuais é filtrada para mostrar apenas as máquinas virtuais que estão na mesma subscrição e localização. Se as suas máquinas virtuais estiverem em mais de três grupos de recursos, os três primeiros grupos de recursos são selecionados.

5. Um espaço de trabalho de Log Analytics existente e uma conta de Automação são selecionados por padrão. Se pretender utilizar um espaço de trabalho e uma conta de Automação de Log Analytics diferentes, clique em **CUSTOM** para selecioná-los na página de Configuração Personalizada. Ao escolher um espaço de trabalho log Analytics, é feito um cheque para determinar se está ligado a uma conta Deautomação. Se for encontrada uma conta automatizada ligada, consulte o seguinte ecrã. Quando terminar, clique em **OK**.

    ![Selecione espaço de trabalho e conta](media/automation-enable-changes-from-browse/selectworkspaceandaccount.png)

6. Se o espaço de trabalho selecionado não estiver ligado a uma conta Deautomatização, consulte o seguinte ecrã. Selecione uma conta De automação e clique **EM OK** quando terminar.

    ![Sem espaço de trabalho](media/automation-enable-changes-from-browse/no-workspace.png)

7. Desmarque a caixa de verificação ao lado de qualquer máquina virtual que não queira ativar. VMs que não podem ser ativados já estão desselecionados.

8. Clique em **Ativar** para ativar a funcionalidade que selecionou. A configuração leva até 15 minutos para ser concluída.

## <a name="next-steps"></a>Passos seguintes

* [Gerir o rastreio e o inventário de alterações](change-tracking-file-contents.md)
* [Trabalhar com configurações de âmbito para rastreio e inventário de alterações](automation-scope-configurations-change-tracking.md)
* [Identificar o software numa VM](automation-tutorial-installed-software.md)
* [Desvincular o espaço de trabalho da conta da Automação para rastreio de alterações e inventário](automation-unlink-workspace-change-tracking.md)
* [Remover VMs do Rastreio e Inventário de Alterações](automation-remove-vms-from-change-tracking.md)
* [Mudanças de resolução de problemas num VM Azure](automation-tutorial-troubleshoot-changes.md)
* [Problemas de mudança de mudança e problemas de inventário](troubleshoot/change-tracking.md)