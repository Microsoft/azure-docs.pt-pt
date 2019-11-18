---
title: Habilitar Azure Monitor para VMs (visualização) para avaliação | Microsoft Docs
description: Saiba como avaliar Azure Monitor para VMs em uma única máquina virtual do Azure ou em um conjunto de dimensionamento de máquinas virtuais.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 11/14/2019
ms.openlocfilehash: 3ab3164db7702566e62b2eedf2a9a03e9f7fb55c
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2019
ms.locfileid: "74109154"
---
# <a name="enable-azure-monitor-for-vms-preview-for-evaluation"></a>Habilitar Azure Monitor para VMs (versão prévia) para avaliação

Você pode avaliar Azure Monitor para VMs (versão prévia) em um pequeno número de VMs (máquinas virtuais) do Azure ou em uma única VM ou conjunto de dimensionamento de máquinas virtuais. A maneira mais fácil e direta de habilitar o monitoramento é da portal do Azure. Seu objetivo é monitorar suas VMs e descobrir problemas de desempenho ou disponibilidade. 

Antes de começar, examine os [pré-requisitos](vminsights-enable-overview.md) e verifique se sua assinatura e seus recursos atendem aos requisitos.  

## <a name="enable-monitoring-for-a-single-azure-vm"></a>Habilitar o monitoramento para uma única VM do Azure
Para habilitar o monitoramento de sua VM do Azure:

1. Iniciar sessão no [portal do Azure](https://portal.azure.com).

1. Selecione **máquinas virtuais**.

1. Na lista, selecione uma VM.

1. Na página de VM, na **monitorização** secção, selecione **Insights (pré-visualização)** .

1. Sobre o **Insights (pré-visualização)** página, selecione **Experimente agora o**.

    ![Ativar o Azure Monitor para as VMs para uma VM](./media/vminsights-enable-single-vm/enable-vminsights-vm-portal.png)

1. Sobre o **integração de informações do Azure Monitor** página, se tiver um existentes do Log Analytics área de trabalho na mesma subscrição, selecione-o na lista pendente.  

    A lista seleciona o espaço de trabalho padrão e o local onde a VM é implantada na assinatura. 

    >[!NOTE]
    >Para criar um novo espaço de trabalho Log Analytics para armazenar os dados de monitoramento da VM, consulte [criar um espaço de trabalho log Analytics](../../azure-monitor/learn/quick-create-workspace.md). Seu espaço de trabalho do Log Analytics deve pertencer a uma das [regiões com suporte](vminsights-enable-overview.md#log-analytics).

Depois de habilitar o monitoramento, talvez seja necessário aguardar cerca de 10 minutos para poder exibir as métricas de integridade da VM.

![Ativar o Azure Monitor para monitorização de processamento da implementação de VMs](./media/vminsights-enable-single-vm/onboard-vminsights-vm-portal-status.png)

## <a name="enable-monitoring-for-a-single-virtual-machine-scale-set"></a>Habilitar o monitoramento para um único conjunto de dimensionamento de máquinas virtuais

Para habilitar o monitoramento do conjunto de dimensionamento de máquinas virtuais do Azure:

1. Iniciar sessão no [portal do Azure](https://portal.azure.com).

2. Selecione **conjuntos de dimensionamento de máquinas virtuais**.

3. Na lista, selecione um conjunto de dimensionamento de máquinas virtuais.

4. Na página conjunto de dimensionamento de máquinas virtuais, na seção **monitoramento** , selecione **insights (versão prévia)** .

5. Na página **insights (versão prévia)** , se você quiser usar um espaço de trabalho log Analytics existente, selecione-o na lista suspensa.

    A lista seleciona o espaço de trabalho padrão e o local em que a VM é implantada na assinatura. 

    ![Habilitar Azure Monitor para VMs para um conjunto de dimensionamento de máquinas virtuais](./media/vminsights-enable-single-vm/enable-vminsights-vmss-portal.png)

    >[!NOTE]
    >Para criar um novo espaço de trabalho Log Analytics para armazenar os dados de monitoramento do conjunto de dimensionamento de máquinas virtuais, consulte [criar um espaço de trabalho log Analytics](../learn/quick-create-workspace.md). Seu espaço de trabalho do Log Analytics deve pertencer a uma das [regiões com suporte](vminsights-enable-overview.md#log-analytics).

Depois de habilitar o monitoramento, talvez seja necessário aguardar cerca de 10 minutos antes de exibir os dados de monitoramento para o conjunto de dimensionamento.

>[!NOTE]
>Se você usar um modelo de atualização manual para seu conjunto de dimensionamento, atualize as instâncias para concluir a instalação. Você pode iniciar as atualizações na página **instâncias** , na seção **configurações** .

![Ativar o Azure Monitor para monitorização de processamento da implementação de VMs](./media/vminsights-enable-single-vm/onboard-vminsights-vmss-portal-status-01.png)

Agora que você habilitou o monitoramento para sua VM ou conjunto de dimensionamento de máquinas virtuais, as informações de monitoramento estão disponíveis para análise no Azure Monitor para VMs. 

## <a name="next-steps"></a>Passos seguintes

* Para exibir dependências de aplicativo descobertas, consulte [usar o mapa de Azure monitor para VMs](vminsights-maps.md). 
* Para identificar afunilamentos, a utilização geral e o desempenho da VM, consulte [Exibir o desempenho da VM do Azure](vminsights-performance.md).