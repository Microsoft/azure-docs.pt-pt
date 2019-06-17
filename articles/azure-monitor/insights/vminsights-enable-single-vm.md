---
title: Ativar o Azure Monitor para VMs (pré-visualização) para avaliação | Documentos da Microsoft
description: Saiba como avaliar o Azure Monitor para VMs numa única máquina virtual do Azure ou num conjunto de dimensionamento de máquina virtual.
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
ms.openlocfilehash: ec909bcd16f923bbd7036f6a69df2bbb07e561b8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67122469"
---
# <a name="enable-azure-monitor-for-vms-preview-for-evaluation"></a>Ativar o Azure Monitor para VMs (pré-visualização) para avaliação

Pode avaliar o Azure Monitor para VMs (pré-visualização) num pequeno número de máquinas virtuais do Azure (VMs) ou num único de dimensionamento de VM ou numa máquina virtual definido. É a forma mais fácil e mais direta para ativar a monitorização do portal do Azure. Seu objetivo é monitorizar as suas VMs e descobrir quaisquer problemas de disponibilidade ou desempenho. 

Antes de começar, reveja os [pré-requisitos](vminsights-enable-overview.md) e certificar-se de que a sua subscrição e recursos de cumprir os requisitos.  

## <a name="enable-monitoring-for-a-single-azure-vm"></a>Ativar a monitorização para uma única VM do Azure
Para ativar a monitorização da sua VM do Azure:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. Selecione **máquinas virtuais**.

1. Na lista, selecione uma VM.

1. Na página de VM, na **monitorização** secção, selecione **Insights (pré-visualização)** .

1. Sobre o **Insights (pré-visualização)** página, selecione **Experimente agora o**.

    ![Ativar o Azure Monitor para as VMs para uma VM](./media/vminsights-enable-single-vm/enable-vminsights-vm-portal-01.png)

1. Sobre o **integração de informações do Azure Monitor** página, se tiver um existentes do Log Analytics área de trabalho na mesma subscrição, selecione-o na lista pendente.  

    A lista preselects a área de trabalho predefinida e localização em que a VM é implementada na subscrição. 

    >[!NOTE]
    >Para criar uma nova área de trabalho do Log Analytics para armazenar os dados de monitorização da VM, veja [criar uma área de trabalho do Log Analytics](../../azure-monitor/learn/quick-create-workspace.md). A área de trabalho do Log Analytics têm de pertencer a um da [regiões suportadas](vminsights-enable-overview.md#log-analytics).

Depois de ativar a monitorização, poderá ter de esperar cerca de 10 minutos antes de poder visualizar as métricas de estado de funcionamento para a VM.

![Ativar o Azure Monitor para monitorização de processamento da implementação de VMs](./media/vminsights-enable-single-vm/onboard-vminsights-vm-portal-status.png)

## <a name="enable-monitoring-for-a-single-virtual-machine-scale-set"></a>Ative a monitorização para um conjunto de dimensionamento de máquina virtual única

Para ativar a monitorização do seu conjunto de dimensionamento de máquina virtual do Azure:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

2. Selecione **conjuntos de dimensionamento de máquinas virtuais**.

3. Na lista, selecione um conjunto de dimensionamento de máquina virtual.

4. Na máquina virtual de conjunto de dimensionamento de página, além da **monitorização** secção, selecione **Insights (pré-visualização)** .

5. Sobre o **Insights (pré-visualização)** página, se pretender utilizar uma área de trabalho do Log Analytics existente, selecione-o na lista pendente.

    A lista preselects a área de trabalho predefinida e a localização que a VM é implementada na subscrição. 

    ![Ativar o Azure Monitor para as VMs para um conjunto de dimensionamento de máquinas virtuais](./media/vminsights-enable-single-vm/enable-vminsights-vmss-portal-01.png)

    >[!NOTE]
    >Para criar uma nova área de trabalho do Log Analytics para armazenar os dados de monitorização do conjunto de dimensionamento de máquina virtual, veja [criar uma área de trabalho do Log Analytics](../learn/quick-create-workspace.md). A área de trabalho do Log Analytics têm de pertencer a um da [regiões suportadas](vminsights-enable-overview.md#log-analytics).

Depois de ativar a monitorização, poderá ter de esperar cerca de 10 minutos antes de poder ver os dados de monitorização para o conjunto de dimensionamento.

>[!NOTE]
>Se utilizar um modelo de atualização manual para o conjunto de dimensionamento, Atualize as instâncias para concluir a configuração. Pode começar as atualizações a partir da **instâncias** página, além do **definições** secção.

![Ativar o Azure Monitor para monitorização de processamento da implementação de VMs](./media/vminsights-enable-single-vm/onboard-vminsights-vmss-portal-status-01.png)

Agora que ativou a monitorização para o conjunto de dimensionamento VM ou numa máquina virtual, as informações de monitorização estão disponíveis para análise no Azure Monitor para as VMs. 

## <a name="next-steps"></a>Passos Seguintes

* Para saber como utilizar a funcionalidade de estado de funcionamento, veja [compreender o estado de funcionamento das suas VMs do Azure Monitor](vminsights-health.md). 
* Para ver dependências de aplicações detetadas, consulte [utilização do Azure Monitor para o mapa de VMs](vminsights-maps.md). 
* Para identificar afunilamentos, a utilização geral e desempenho da sua VM, consulte [o desempenho da VM do Azure de modo de exibição](vminsights-performance.md).