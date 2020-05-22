---
title: Ativar o rastreio e o inventário de alterações de automatização azure a partir de um VM Azure
description: Este artigo diz como ativar o Rastreio de Mudanças e Inventário de um VM Azure.
services: automation
ms.date: 03/04/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 4eccdef6bd3f2bfcd0eced8281f7b998536f22a9
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83749187"
---
# <a name="enable-change-tracking-and-inventory-from-an-azure-vm"></a>Ativar o rastreio e o inventário de alterações de um VM Azure

Este artigo descreve como pode utilizar um VM Azure para ativar a funcionalidade de Rastreio e Inventário de [Alterações](change-tracking.md) noutras máquinas. Para ativar os VMs Azure em escala, deve ativar um VM existente utilizando o Change Tracking e o Inventory. 

> [!NOTE]
> Ao permitir o rastreio e inventário de alterações, apenas certas regiões são suportadas para ligar um espaço de trabalho log Analytics e uma conta de Automação. Para obter uma lista dos pares de mapeamento suportados, consulte [O mapeamento da Região para a conta de Automação e o espaço de trabalho log Analytics](how-to/region-mappings.md).

## <a name="prerequisites"></a>Pré-requisitos

* Subscrição do Azure. Se ainda não tiver um, pode ativar os seus benefícios de [subscrição da MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se para uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Conta de automatização](automation-offering-get-started.md) para gerir máquinas.
* Uma [máquina virtual.](../virtual-machines/windows/quick-create-portal.md)

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no portal do Azure em https://portal.azure.com.

## <a name="enable-change-tracking-and-inventory"></a>Ativar o Controlo de Alterações e Inventário

1. No [portal Azure, selecione](https://portal.azure.com) **máquinas Virtuais** ou procure e selecione **máquinas Virtuais** a partir da página Inicial.

2. Selecione o VM para o qual pretende ativar o Rastreio de Alterações e o Inventário. Os VMs podem existir em qualquer região, independentemente da localização da sua conta De automação.

3. Na página VM, selecione **o rastreio de Inventário** ou **alteração** sob gestão de **configuração**.

4. Deve ter `Microsoft.OperationalInsights/workspaces/read` a permissão para determinar se o VM está habilitado para um espaço de trabalho. Para saber mais sobre permissões adicionais que são necessárias, consulte [as permissões](automation-role-based-access-control.md#feature-setup-permissions)de configuração do Recurso . Para aprender a ativar várias máquinas ao mesmo tempo, consulte [Enable Change Tracking and Inventory a partir de uma conta de Automação](automation-enable-changes-from-auto-acct.md).

5. Escolha o espaço de trabalho e a conta de Automação do Log Analytics e clique em **Ativar** o Rastreio de Alterações e O Inventário para o VM. A configuração leva até 15 minutos para ser concluída. 

## <a name="check-the-scope-configuration"></a><a name="scope-configuration"></a>Verifique a configuração do âmbito

Alterar o Rastreio e o Inventário utiliza uma configuração de âmbito dentro do espaço de trabalho para direcionar os computadores para ativar a funcionalidade. A configuração de âmbito é um grupo de uma ou mais pesquisas guardadas que são usadas para limitar o âmbito da funcionalidade a computadores específicos. Para mais informações, consulte [Trabalhar com configurações de âmbito para Rastreio e Inventário](automation-scope-configurations-change-tracking.md)de Alterações .

## <a name="next-steps"></a>Passos seguintes

* [Gerir o rastreio e o inventário de alterações](change-tracking-file-contents.md)
* [Trabalhar com configurações de âmbito para rastreio e inventário de alterações](automation-scope-configurations-change-tracking.md)
* [Identificar o software numa VM](automation-tutorial-installed-software.md)
* [Desvincular o espaço de trabalho da conta da Automação para rastreio de alterações e inventário](automation-unlink-workspace-change-tracking.md)
* [Remover VMs do Rastreio e Inventário de Alterações](automation-remove-vms-from-change-tracking.md)
* [Mudanças de resolução de problemas num VM Azure](automation-tutorial-troubleshoot-changes.md)
* [Problemas de mudança de mudança e problemas de inventário](troubleshoot/change-tracking.md)
