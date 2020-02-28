---
title: Ativar o Monitor Azure para VMs (pré-visualização) para avaliação / Microsoft Docs
description: Aprenda a avaliar o Monitor Azure para VMs numa única máquina virtual Azure ou num conjunto de escala de máquina virtual.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/14/2019
ms.openlocfilehash: 3b79eaeed420426fe79b2b6caf19ba1d1be61939
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77664616"
---
# <a name="enable-azure-monitor-for-vms-preview-for-evaluation"></a>Ativar o Monitor Azure para VMs (pré-visualização) para avaliação

Pode avaliar o Monitor Azure para VMs (pré-visualização) num pequeno número de máquinas virtuais Azure (VMs) ou num único conjunto de vm ou de escala virtual. A forma mais fácil e direta de permitir a monitorização é a partir do portal Azure. O seu objetivo é monitorizar os seus VMs e descobrir quaisquer problemas de desempenho ou disponibilidade. 

Antes de começar, reveja os [pré-requisitos](vminsights-enable-overview.md) e certifique-se de que a sua subscrição e recursos cumprem os requisitos.  

## <a name="enable-monitoring-for-a-single-azure-vm"></a>Ativar a monitorização de um único VM Azure
Para permitir a monitorização do seu VM Azure:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. Selecione **Máquinas Virtuais**.

1. Na lista, selecione uma VM.

1. Na página VM, na secção **monitora,** selecione **Insights (pré-visualização)** .

1. Na página **Insights (pré-visualização),** selecione **Tente agora**.

    ![Ativar o Azure Monitor para as VMs para uma VM](./media/vminsights-enable-single-vm/enable-vminsights-vm-portal.png)

1. Na página de **Onboarding Do Monitor Azure** Insights, se tiver um espaço de trabalho existente no Log Analytics na mesma subscrição, selecione-o na lista de drop-down.  

    A lista pré-seleciona o espaço de trabalho padrão e a localização onde o VM é implantado na subscrição. 

    >[!NOTE]
    >Para criar um novo espaço de trabalho log analytics para armazenar os dados de monitorização do VM, consulte Criar um espaço de [trabalho Log Analytics](../../azure-monitor/learn/quick-create-workspace.md). O seu espaço de trabalho Log Analytics deve pertencer a uma das [regiões apoiadas.](vminsights-enable-overview.md#log-analytics)

Depois de ter ativado a monitorização, poderá ter de esperar cerca de 10 minutos para poder ver as métricas de saúde do VM.

![Ativar o Azure Monitor para monitorização de processamento da implementação de VMs](./media/vminsights-enable-single-vm/onboard-vminsights-vm-portal-status.png)

## <a name="enable-monitoring-for-a-single-virtual-machine-scale-set"></a>Ativar a monitorização para um único conjunto de escala de máquina virtual

Para permitir a monitorização do conjunto de escala de máquinas virtuais Azure:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

2. Selecione **conjuntos**de escala de máquina virtual .

3. A partir da lista, selecione um conjunto de escala de máquina virtual.

4. Na página de conjunto de escala de máquina virtual, na secção **monitoramento,** selecione **Insights (pré-visualização)** .

5. Na página **Insights (pré-visualização),** se pretender utilizar um espaço de trabalho existente no Log Analytics, selecione-o na lista de drop-down.

    A lista pré-seleciona o espaço de trabalho padrão e a localização para a a que o VM é implantado na subscrição. 

    ![Ativar o Monitor Azure para VMs para um conjunto de escala de máquina virtual](./media/vminsights-enable-single-vm/enable-vminsights-vmss-portal.png)

    >[!NOTE]
    >Para criar um novo espaço de trabalho log analytics para armazenar os dados de monitorização do conjunto de escala de máquina virtual, consulte Criar um espaço de [trabalho Log Analytics](../learn/quick-create-workspace.md). O seu espaço de trabalho Log Analytics deve pertencer a uma das [regiões apoiadas.](vminsights-enable-overview.md#log-analytics)

Depois de ter ativado a monitorização, poderá ter de esperar cerca de 10 minutos para poder visualizar os dados de monitorização do conjunto de escala.

>[!NOTE]
>Se utilizar um modelo de atualização manual para o seu conjunto de escala, atualize as instâncias para completar a configuração. Pode iniciar as atualizações a partir da página **Casos,** na secção **Definições.**

![Ativar o Azure Monitor para monitorização de processamento da implementação de VMs](./media/vminsights-enable-single-vm/onboard-vminsights-vmss-portal-status-01.png)

Agora que permitiu a monitorização do seu conjunto de vm ou de escala de máquina virtual, a informação de monitorização está disponível para análise no Monitor Azure para VMs. 

## <a name="next-steps"></a>Passos seguintes

* Para visualizar as dependências de aplicações descobertas, consulte use O Monitor Azure para o Mapa de [VMs](vminsights-maps.md). 
* Para identificar estrangulamentos, utilização geral e desempenho do seu VM, consulte o desempenho do [VM view Azure](vminsights-performance.md).
