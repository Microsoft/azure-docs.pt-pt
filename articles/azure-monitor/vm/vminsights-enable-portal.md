---
title: Ativar o Azure Monitor para uma única máquina virtual ou escala de máquina virtual definida no portal Azure
description: Saiba como ativar insights em VM numa única máquina virtual Azure ou conjunto de balança de máquina virtual utilizando o portal Azure.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/27/2020
ms.openlocfilehash: 076fcab7b0747a7993407edd65f9d08efc27309f
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102035590"
---
# <a name="enable-azure-monitor-for-single-virtual-machine-or-virtual-machine-scale-set-in-the-azure-portal"></a>Ativar o Azure Monitor para uma única máquina virtual ou escala de máquina virtual definida no portal Azure
Este artigo descreve como ativar insights VM para uma máquina virtual ou conjunto de escala de máquina virtual usando o portal Azure. Este procedimento pode ser utilizado para o seguinte procedimento:

- Máquina virtual do Azure
- Conjunto de escala de máquina virtual Azure
- Máquina virtual híbrida ligada ao Arco Azure

## <a name="prerequisites"></a>Pré-requisitos

- [Criar e configurar um espaço de trabalho Log Analytics](./vminsights-configure-workspace.md). Em alternativa, pode criar um novo espaço de trabalho durante este processo.
- Consulte [sistemas operativos suportados](./vminsights-enable-overview.md#supported-operating-systems) para garantir que o sistema operativo da máquina virtual ou da balança de máquinas virtuais que está a ativar é suportado. 

## <a name="enable-vm-insights"></a>Ativar insights VM

A partir do portal Azure, selecione **máquinas Virtuais, conjuntos** **de escala de máquina virtual**, ou **Servidores - Azure Arc** e selecione um recurso da lista. Na secção **de Monitorização** do menu, selecione **Insights** e, em seguida, **Ative**. O exemplo a seguir mostra uma máquina virtual Azure, mas o menu é semelhante para o conjunto de escala de máquina virtual Azure ou Azure Arc.

![Ativar insights em VM para um VM](media/vminsights-enable-portal/enable-vminsights-vm-portal.png)

Se a máquina virtual ainda não estiver ligada a um espaço de trabalho do Log Analytics, então será solicitado a selecionar um. Se ainda não [criou um espaço de trabalho,](../logs/quick-create-workspace.md)pode selecionar um padrão para o local onde a máquina virtual ou o conjunto de escala de máquina virtual estão implantados na subscrição. Este espaço de trabalho será criado e configurado se já não existir. Se selecionar um espaço de trabalho existente, será configurado para insights VM se já não o existisse.

> [!NOTE]
> Se selecionar um espaço de trabalho que não tenha sido previamente configurado para insights VM, o pacote de gestão *VMInsights* será adicionado a este espaço de trabalho. Isto será aplicado a qualquer agente já ligado ao espaço de trabalho, quer esteja ou não habilitado para insights de VM. Os dados de desempenho serão recolhidos a partir destas máquinas virtuais e armazenados na tabela *InsightsMetrics.*

![Selecionar área de trabalho](media/vminsights-enable-portal/select-workspace.png)

Receberá mensagens de estado à medida que a configuração for executada.

>[!NOTE]
>Se utilizar um modelo de atualização manual para o conjunto de escalas de máquinas virtuais, atualize as instâncias para completar a configuração. Pode iniciar as atualizações a partir da página **Instâncias,** na secção **Definições.**

![Ativar o processamento de monitorização de insights VM](media/vminsights-enable-portal/onboard-vminsights-vm-portal-status.png)



## <a name="next-steps"></a>Passos seguintes

* Consulte [o Mapa de Insights VM](vminsights-maps.md) para visualizar as dependências de aplicações descobertas. 
* Consulte [o desempenho do View Azure VM](vminsights-performance.md) para identificar estrangulamentos, utilização geral e desempenho do seu VM.
