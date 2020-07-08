---
title: Ativar o Monitor Azure para VMs no portal Azure
description: Saiba como avaliar o Azure Monitor para VMs numa única máquina virtual Azure ou num conjunto de escala de máquina virtual.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/25/2020
ms.openlocfilehash: 4cdb9390b3146df74f2cbe8eba7b170a5d11fb2c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85507063"
---
# <a name="enable-azure-monitor-for-single-vm-or-vmss-in-the-azure-portal"></a>Ativar o Monitor Azure para um VM ou VMSS único no portal Azure
Este artigo descreve como ativar o Azure Monitor para VMs para uma única máquina virtual ou conjunto de balança de máquina virtual utilizando o portal Azure. Este procedimento pode ser utilizado para o seguinte procedimento:

- Máquina virtual do Azure
- Conjunto de escala de máquina virtual Azure
- Máquina de Arco Azul

Antes de começar, reveja os [pré-requisitos](vminsights-enable-overview.md) e certifique-se de que a sua subscrição e recursos cumprem os requisitos.  

## <a name="enable-azure-monitor-for-vms"></a>Ativar monitor Azure para VMs

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. Selecione **máquinas virtuais, conjuntos** **de balança de máquinas virtuais,** ou **Máquinas - Arco Azul**.

1. Selecione um recurso da lista.

1. Na secção **de Monitorização** do menu, selecione **Insights** e, em seguida, **Ative**. O exemplo a seguir mostra uma máquina virtual Azure, mas o menu é semelhante para Azure VMSS ou Azure Arc.

    ![Ativar o Monitor Azure para VMs para um VM](media/vminsights-enable-single-vm/enable-vminsights-vm-portal.png)

1. Se o VM ainda não estiver ligado a um espaço de trabalho do Log Analytics, então será solicitado a selecionar um. Se ainda não [criou um espaço de trabalho,](../../azure-monitor/learn/quick-create-workspace.md)pode selecionar um padrão para o local onde o VM ou VMSS está implantado na subscrição. Este espaço de trabalho será criado e configurado se já não existir.

2. Receberá mensagens de estado à medida que a configuração for executada.

    >[!NOTE]
    >Se utilizar um modelo de atualização manual para o seu conjunto de escala, atualize as instâncias para completar a configuração. Pode iniciar as atualizações a partir da página **Instâncias,** na secção **Definições.**

    ![Ativar o Monitor Azure para o processamento de implementação de monitorização de VMs](media/vminsights-enable-single-vm/onboard-vminsights-vm-portal-status.png)



## <a name="next-steps"></a>Próximos passos

* Para ver as dependências de aplicações descobertas, consulte [o Use Azure Monitor para o Mapa de VMs](vminsights-maps.md). 
* Para identificar estrangulamentos, utilização geral e desempenho do seu VM, consulte o desempenho do [See Azure VM](vminsights-performance.md).
