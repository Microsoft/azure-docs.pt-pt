---
title: Ativar o Azure Monitor para uma única máquina virtual ou escala de máquina virtual definida no portal Azure
description: Saiba como ativar o Azure Monitor para VMs numa única máquina virtual Azure ou conjunto de balanças de máquinas virtuais utilizando o portal Azure.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/27/2020
ms.openlocfilehash: 55e5ff2af62c903efeab5c4932eae0c9dc9b535c
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/18/2020
ms.locfileid: "94842316"
---
# <a name="enable-azure-monitor-for-single-virtual-machine-or-virtual-machine-scale-set-in-the-azure-portal"></a>Ativar o Azure Monitor para uma única máquina virtual ou escala de máquina virtual definida no portal Azure
Este artigo descreve como ativar o Azure Monitor para VMs para uma máquina virtual ou conjunto de balança de máquina virtual utilizando o portal Azure. Este procedimento pode ser utilizado para o seguinte procedimento:

- Máquina virtual do Azure
- Conjunto de escala de máquina virtual Azure
- Máquina virtual híbrida ligada ao Arco Azure

## <a name="prerequisites"></a>Pré-requisitos

- [Criar e configurar um espaço de trabalho Log Analytics](vminsights-configure-workspace.md). Em alternativa, pode criar um novo espaço de trabalho durante este processo.
- Consulte [sistemas operativos suportados](vminsights-enable-overview.md#supported-operating-systems) para garantir que o sistema operativo da máquina virtual ou da balança de máquinas virtuais que está a ativar é suportado. 

## <a name="enable-azure-monitor-for-vms"></a>Ativar monitor Azure para VMs

A partir do portal Azure, selecione **máquinas Virtuais, conjuntos** **de escala de máquina virtual**, ou **Servidores - Azure Arc** e selecione um recurso da lista. Na secção **de Monitorização** do menu, selecione **Insights** e, em seguida, **Ative**. O exemplo a seguir mostra uma máquina virtual Azure, mas o menu é semelhante para o conjunto de escala de máquina virtual Azure ou Azure Arc.

![Ativar o Monitor Azure para VMs para um VM](media/vminsights-enable-single-vm/enable-vminsights-vm-portal.png)

Se a máquina virtual ainda não estiver ligada a um espaço de trabalho do Log Analytics, então será solicitado a selecionar um. Se ainda não [criou um espaço de trabalho,](../../azure-monitor/learn/quick-create-workspace.md)pode selecionar um padrão para o local onde a máquina virtual ou o conjunto de escala de máquina virtual estão implantados na subscrição. Este espaço de trabalho será criado e configurado se já não existir. Se selecionar um espaço de trabalho existente, será configurado para Azure Monitor para VMs se já não o fosse.

> [!NOTE]
> Se selecionar um espaço de trabalho que não tenha sido previamente configurado para O Azure Monitor para VMs, o pacote de gestão *VMInsights* será adicionado a este espaço de trabalho. Isto será aplicado a qualquer agente já ligado ao espaço de trabalho, quer esteja ou não habilitado para o Azure Monitor para VMs. Os dados de desempenho serão recolhidos a partir destas máquinas virtuais e armazenados na tabela *InsightsMetrics.*

![Selecionar área de trabalho](media/vminsights-configure-workspace/select-workspace.png)

Receberá mensagens de estado à medida que a configuração for executada.

>[!NOTE]
>Se utilizar um modelo de atualização manual para o conjunto de escalas de máquinas virtuais, atualize as instâncias para completar a configuração. Pode iniciar as atualizações a partir da página **Instâncias,** na secção **Definições.**

![Ativar o Monitor Azure para o processamento de implementação de monitorização de VMs](media/vminsights-enable-single-vm/onboard-vminsights-vm-portal-status.png)



## <a name="next-steps"></a>Passos seguintes

* Consulte [o Monitor Azure para visualizar](vminsights-maps.md) as dependências de aplicações descobertas. 
* Consulte [o desempenho do View Azure VM](vminsights-performance.md) para identificar estrangulamentos, utilização geral e desempenho do seu VM.
