---
title: Ativar a Gestão de Atualização de Automação Azure do portal Azure
description: Este artigo diz como ativar a Gestão de Atualizações a partir do portal Azure.
services: automation
ms.date: 04/11/2019
ms.topic: article
ms.custom: mvc
ms.openlocfilehash: 039e79f043e4e898ab85f003a84f9f4db2d36d27
ms.sourcegitcommit: 6a9f01bbef4b442d474747773b2ae6ce7c428c1f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84117235"
---
# <a name="enable-update-management-from-the-azure-portal"></a>Ativar a Gestão de Atualizações a partir do portal do Azure

Este artigo descreve como pode ativar a funcionalidade [De Gestão](automation-update-management.md) de Atualizações para VMs através da navegação no portal Azure. Para ativar os VMs Azure em escala, deve ativar um VM existente utilizando a Atualização. 

O número de grupos de recursos que pode utilizar para gerir os seus VMs é limitado pelos limites de implementação do Gestor de [Recursos.](../azure-resource-manager/templates/cross-resource-group-deployment.md) As implementações do Gestor de Recursos, não confundir com as implementações da Atualização, estão limitadas a cinco grupos de recursos por implantação. Dois destes grupos de recursos estão reservados para configurar o espaço de trabalho log Analytics, a conta de Automação e recursos relacionados. Isto deixa-o com três grupos de recursos para selecionar para gestão pela Update Management. Este limite aplica-se apenas à configuração simultânea, não ao número de grupos de recursos que podem ser geridos por uma funcionalidade de Automação.

> [!NOTE]
> Ao ativar a Gestão de Atualizações, apenas certas regiões são suportadas para ligar um espaço de trabalho log Analytics e uma Conta de Automação. Para obter uma lista dos pares de mapeamento suportados, consulte [O Mapeamento da Região para A Conta de Automação e espaço de trabalho log Analytics](how-to/region-mappings.md).

## <a name="prerequisites"></a>Pré-requisitos

* Subscrição do Azure. Se ainda não tiver um, pode ativar os seus benefícios de [subscrição da MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se para uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Conta de automatização](automation-offering-get-started.md) para gerir máquinas.
* Uma [máquina virtual.](../virtual-machines/windows/quick-create-portal.md)

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inscreva-se no Azure em https://portal.azure.com .

## <a name="enable-update-management"></a>Ativar a Gestão de Atualizações

1. No portal Azure, navegue para **máquinas virtuais.**

2. Utilize as caixas de verificação para escolher os VMs para adicionar à Atualização de Gestão. Pode adicionar máquinas para até três grupos de recursos diferentes de cada vez. Os VMs Azure podem existir em qualquer região, independentemente da localização da sua conta Deautomação.

    ![Lista de VMs](media/automation-onboard-solutions-from-browse/vmlist.png)

    > [!TIP]
    > Utilize os controlos de filtro para selecionar VMs de diferentes subscrições, locais e grupos de recursos. Pode clicar na caixa de verificação superior para selecionar todas as máquinas virtuais numa lista.

    ![Ativar a Gestão de Atualizações](media/automation-onboard-solutions-from-browse/onboardsolutions.png)

3. Clique em **Serviços** e selecione Gestão de **Atualizações** para a funcionalidade 'Gestão de Actualizações'. 

4. A lista de máquinas virtuais é filtrada para mostrar apenas as máquinas virtuais que estão na mesma subscrição e localização. Se as suas máquinas virtuais estiverem em mais de três grupos de recursos, os três primeiros grupos de recursos são selecionados.

5. Um espaço de trabalho de Log Analytics existente e uma conta de Automação são selecionados por padrão. Se pretender utilizar um espaço de trabalho e uma conta de Automação de Log Analytics diferentes, clique em **CUSTOM** para selecioná-los na página de Configuração Personalizada. Ao escolher um espaço de trabalho log Analytics, é feito um cheque para determinar se está ligado a uma conta Deautomação. Se for encontrada uma conta automatizada ligada, consulte o seguinte ecrã. Quando terminar, clique em **OK**.

    ![Selecione espaço de trabalho e conta](media/automation-onboard-solutions-from-browse/selectworkspaceandaccount.png)

6. Se o espaço de trabalho selecionado não estiver ligado a uma conta De automatização, consulte o seguinte ecrã. Selecione uma conta De automação e clique **EM OK** quando terminar.

    ![Sem espaço de trabalho](media/automation-onboard-solutions-from-browse/no-workspace.png)

7. Desmarque a caixa de verificação ao lado de qualquer máquina virtual que não queira ativar. VMs que não podem ser ativados já estão desselecionados.

8. Clique em **Ativar** para ativar a funcionalidade que selecionou. A configuração leva até 15 minutos para ser concluída.

## <a name="next-steps"></a>Próximos passos

* Para utilizar a Gestão de Atualizações para VMs, consulte ['Gerir atualizações e patches' para os seus VMs Azure](automation-tutorial-update-management.md).
* Se já não necessitar do espaço de trabalho do Log Analytics, consulte instruções no [espaço de trabalho Unlink da Automação para gestão](automation-unlink-workspace-update-management.md)de atualizações .
* Para eliminar VMs da Atualização, consulte [Remover VMs da Gestão de Atualizações](automation-remove-vms-from-update-management.md).
* Para resolver os erros gerais de Gestão de Atualizações, consulte [os problemas](troubleshoot/update-management.md)de Gestão de Atualização de Resolução de Problemas .
* Para resolver problemas com o agente de atualização do Windows, consulte problemas de agente de [atualização do Windows](troubleshoot/update-agent-issues.md).
* Para resolver problemas com o agente de atualização linux, consulte problemas de agente de[atualização Da Troubleshoot Linux](troubleshoot/update-agent-issues-linux.md).
