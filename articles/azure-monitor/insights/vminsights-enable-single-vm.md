---
title: Ativar o Azure Monitor para VMs (pré-visualização) para avaliação | Documentos da Microsoft
description: Este artigo descreve como ativar Azure Monitor para as VMs para uma única máquina virtual do Azure ou o conjunto para fins de avaliação de dimensionamento de máquina virtual.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/09/2019
ms.author: magoedte
ms.openlocfilehash: 673f153b551e4b0c89a564c96d6bd9819ca26f5d
ms.sourcegitcommit: bb85a238f7dbe1ef2b1acf1b6d368d2abdc89f10
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/10/2019
ms.locfileid: "65524089"
---
# <a name="enable-azure-monitor-for-vms-preview-for-evaluation"></a>Ativar o Azure Monitor para VMs (pré-visualização) para avaliação

Para avaliar o Azure Monitor para VMs (pré-visualização) num pequeno número de máquinas virtuais do Azure ou uma única máquina virtual ou o conjunto de dimensionamento de máquina virtual, a abordagem mais fácil e mais direta para ativar a monitorização é do portal do Azure. No final deste processo, será com êxito começaram a monitorização-los e saiba se ocorrerem problemas de desempenho ou disponibilidade. 

Antes de obter iniciado, não se esqueça de rever o [pré-requisitos](vminsights-enable-overview.md) e certifique-se de que a sua subscrição e os recursos de cumprir os requisitos.  

## <a name="enable-monitoring-for-a-single-azure-vm"></a>Ativar a monitorização para uma única VM do Azure
Para ativar a monitorização da sua VM do Azure no portal do Azure, efetue o seguinte:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. Selecione **máquinas virtuais**.

1. Na lista, selecione uma VM.

1. Na página de VM, na **monitorização** secção, selecione **Insights (pré-visualização)**.

1. Sobre o **Insights (pré-visualização)** página, selecione **Experimente agora o**.

    ![Ativar o Azure Monitor para as VMs para uma VM](./media/vminsights-enable-single-vm/enable-vminsights-vm-portal-01.png)

1. Sobre o **integração de informações do Azure Monitor** página, se tiver um existentes do Log Analytics área de trabalho na mesma subscrição, selecione-o na lista pendente.  

    A lista preselects a área de trabalho predefinida e a localização que a máquina virtual é implementada na subscrição. 

    >[!NOTE]
    >Se quiser criar uma nova área de trabalho do Log Analytics para armazenar os dados de monitorização da VM, siga as instruções em [criar uma área de trabalho do Log Analytics](../../azure-monitor/learn/quick-create-workspace.md) de uma das regiões suportadas listadas [aqui](vminsights-enable-overview.md#log-analytics).

Depois de ativar a monitorização, poderá demorar cerca de 10 minutos antes de poder visualizar as métricas de estado de funcionamento para a máquina virtual.

![Ativar o Azure Monitor para monitorização de processamento da implementação de VMs](./media/vminsights-enable-single-vm/onboard-vminsights-vm-portal-status.png)

## <a name="enable-monitoring-for-a-single-virtual-machine-scale-set"></a>Ative a monitorização para um conjunto de dimensionamento de máquina virtual única

Para ativar a monitorização do seu conjunto no portal do Azure de dimensionamento de máquina virtual do Azure, efetue o seguinte:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

2. Selecione **conjuntos de dimensionamento de máquinas virtuais**.

3. Na lista, selecione um conjunto de dimensionamento de máquina virtual.

4. Na máquina virtual de conjunto de dimensionamento de página, além da **monitorização** secção, selecione **Insights (pré-visualização)**.

5. Sobre o **Insights (pré-visualização)** página, se tiver uma área de trabalho do Log Analytics existente que pretende utilizar, selecione-o na lista pendente.

    A lista preselects a área de trabalho predefinida e a localização que a máquina virtual é implementada na subscrição. 

    ![Ativar o Azure Monitor para as VMs para um conjunto de dimensionamento de máquinas virtuais](./media/vminsights-enable-single-vm/enable-vminsights-vmss-portal-01.png)

    >[!NOTE]
    >Se quiser criar uma nova área de trabalho do Log Analytics para armazenar os dados de monitorização da VM, siga as instruções em [criar uma área de trabalho do Log Analytics](../learn/quick-create-workspace.md) de uma das regiões suportadas listadas [aqui](vminsights-enable-overview.md#log-analytics).

Depois de ativar a monitorização, poderá demorar cerca de 10 minutos antes de poder ver os dados de monitorização para o conjunto de dimensionamento.

>[!NOTE]
>Se estiver a utilizar um modelo de atualização manual para o conjunto de dimensionamento terá de atualizar as instâncias para concluir a configuração.  Isso pode ser feito a partir da página de instâncias sob o **definições** secção.

![Ativar o Azure Monitor para monitorização de processamento da implementação de VMs](./media/vminsights-enable-single-vm/onboard-vminsights-vmss-portal-status-01.png)

## <a name="next-steps"></a>Passos Seguintes

Agora que a monitorização estiver ativada para a sua máquina virtual ou um conjunto de dimensionamento de máquina virtual, estas informações são disponíveis para análise com o Azure Monitor para as VMs. Para saber como utilizar a funcionalidade de estado de funcionamento, veja [vista do Azure Monitor de estado de funcionamento de VMs](vminsights-health.md). Para ver dependências de aplicações detetadas, consulte [vista do Azure Monitor para o mapa de VMs](vminsights-maps.md). Para identificar afunilamentos e a utilização geral com o desempenho de VMs, veja [vista de desempenho da VM do Azure](vminsights-performance.md), ou para ver dependências de aplicações detetadas, consulte [vista de Azure Monitor para o mapa de VMs](vminsights-maps.md).