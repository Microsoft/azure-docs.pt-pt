---
title: Ativar o Rastreio e Inventário de Alteração de Automação Azure a partir do portal Azure
description: Este artigo diz como ativar a funcionalidade de Rastreio e Inventário de Alterações a partir do portal Azure.
services: automation
ms.date: 04/11/2019
ms.topic: article
ms.custom: mvc
ms.openlocfilehash: 11ae873ae4700dc4f9cb3d02a898a3ded9f6db59
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87367423"
---
# <a name="enable-change-tracking-and-inventory-from-azure-portal"></a>Ativar o rastreio de alterações e o inventário a partir do portal Azure

Este artigo descreve como pode ativar a funcionalidade [de 'Change Tracking and Inventory'](change-tracking.md) para VMs navegando no portal Azure. Para ativar os VMs Azure em escala, deve ativar um VM existente utilizando o Change Tracking and Inventory. 

O número de grupos de recursos que pode utilizar para gerir os seus VMs é limitado pelos [limites de implementação](../azure-resource-manager/templates/cross-scope-deployment.md)do Gestor de Recursos. As implementações do Gestor de Recursos, que não devem ser confundidas com implementações de atualização, estão limitadas a cinco grupos de recursos por implementação. Dois destes grupos de recursos estão reservados para configurar o espaço de trabalho Log Analytics, a conta de automação e recursos conexos. Isto deixa-lhe três grupos de recursos para selecionar para gestão por Change Tracking e Inventory. Este limite aplica-se apenas à configuração simultânea, e não ao número de grupos de recursos que podem ser geridos por uma funcionalidade de Automação.

> [!NOTE]
> Ao permitir o Rastreio e Inventário de Alterações, apenas certas regiões são suportadas para ligar um espaço de trabalho Log Analytics e uma Conta de Automação. Para obter uma lista dos pares de mapeamento suportados, consulte [o mapeamento da Região para a Conta de Automação e o espaço de trabalho log Analytics.](how-to/region-mappings.md)

## <a name="prerequisites"></a>Pré-requisitos

* Subscrição do Azure. Se ainda não tiver um, pode [ativar os benefícios do seu assinante MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se numa [conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Conta de automatização](./index.yml) para gerir máquinas.
* Uma [máquina virtual.](../virtual-machines/windows/quick-create-portal.md)

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inscreva-se no Azure at https://portal.azure.com .

## <a name="enable-change-tracking-and-inventory"></a>Ativar o Controlo de Alterações e Inventário

1. No portal Azure, navegue para **máquinas Virtuais.**

2. Utilize as caixas de verificação para escolher os VMs para adicionar ao Change Tracking and Inventory. Pode adicionar máquinas para até três grupos de recursos diferentes de cada vez. Os VMs Azure podem existir em qualquer região, independentemente da localização da sua conta Automation.

    ![Lista de VMs](media/automation-enable-changes-from-browse/vmlist.png)

    > [!TIP]
    > Utilize os controlos do filtro para selecionar VMs de diferentes subscrições, locais e grupos de recursos. Pode clicar na caixa de verificação superior para selecionar todas as máquinas virtuais numa lista.

3. Selecione **O rastreio** de alteração ou **inventário** sob **gestão de configuração**.

4. A lista de máquinas virtuais é filtrada para mostrar apenas as máquinas virtuais que estão na mesma subscrição e localização. Se as suas máquinas virtuais estiverem em mais de três grupos de recursos, os três primeiros grupos de recursos são selecionados.

5. Um espaço de trabalho e uma conta de Automação do Log Analytics existentes são selecionados por padrão. Se pretender utilizar um espaço de trabalho e uma conta de automação do Log Analytics diferente, clique em **CUSTOM** para selecioná-los na página de Configuração Personalizada. Ao escolher um espaço de trabalho Log Analytics, é feito um cheque para determinar se está ligado a uma conta Demômpa. Se for encontrada uma conta de Automação ligada, vê o seguinte ecrã. Quando terminar, clique **em OK**.

    ![Selecione espaço de trabalho e conta](media/automation-enable-changes-from-browse/selectworkspaceandaccount.png)

6. Se o espaço de trabalho selecionado não estiver ligado a uma conta Demôm automação, vê o seguinte ecrã. Selecione uma conta Dem automação e clique **em OK** quando terminar.

    ![Sem espaço de trabalho](media/automation-enable-changes-from-browse/no-workspace.png)

7. Desmarcar a caixa de verificação ao lado de qualquer máquina virtual que não queira ativar. VMs que não podem ser ativados já estão desescolhidos.

8. Clique **em Ativar** para ativar a funcionalidade selecionada. A configuração leva até 15 minutos para ser concluída.

## <a name="next-steps"></a>Passos seguintes

* Para obter detalhes sobre o trabalho com a funcionalidade, consulte [Manage Change Tracking and Inventory](change-tracking-file-contents.md).
* Para resolver problemas gerais com a funcionalidade, consulte [problemas de rastreio e inventário de alterações de resolução de problemas](troubleshoot/change-tracking.md).
