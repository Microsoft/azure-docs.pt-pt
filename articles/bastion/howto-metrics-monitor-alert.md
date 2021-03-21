---
title: Configure monitorização e métricas usando O Monitor Azure
titleSuffix: Azure Bastion
description: Saiba mais sobre a monitorização do Azure Bastion, e métricas usando o Azure Monitor, a solução para métricas, alertando, registos de diagnóstico em Azure.
services: bastion
author: mialdrid
ms.service: bastion
ms.topic: how-to
ms.date: 03/12/2021
ms.author: mialdrid
ms.openlocfilehash: 3a5beba3938b5a845a378ede155f2f64e6baac7a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103417948"
---
# <a name="how-to-configure-monitoring-and-metrics-for-azure-bastion-using-azure-monitor"></a>Como configurar a monitorização e as métricas para o Bastião Azure utilizando o Monitor Azure

Este artigo ajuda-o a trabalhar com monitorização e métricas para Azure Bastion usando O Monitor Azure.

>[!NOTE]
>Não é recomendável utilizar **métricas clássicas.**
>

## <a name="about-metrics"></a>Sobre métricas

Azure Bastion tem várias métricas que estão disponíveis. A tabela a seguir mostra a categoria e as dimensões para cada métrica disponível.

|**Métrica**|**Categoria**|**Dimensões(s)**|
| --- | --- | --- |
|Estado de comunicação de bastião**|[Disponibilidade](#availability)|N/D|
|Memória total|[Disponibilidade](#availability)|Instância|
|CPU usado|[Trânsito](#traffic)|Instância
|Memória usada|[Trânsito](#traffic)|Instância
|Contagem de sessão|[Desempenho](#performance)|Instância|

** O estado de comunicação do Bastião só é aplicável aos anfitriões de bastião implantados a partir de novembro de 2020.

### <a name="availability-metrics"></a><a name="availability"></a>Métricas de disponibilidade

#### <a name="bastion-communication-status"></a><a name="communication-status"></a>Estado de comunicação de bastião

Pode ver o estado de comunicação de Azure Bastion, agregado em todos os casos que compõem o hospedeiro de bastião.

* Um valor de **1** indica que o bastião está disponível.
* Um valor de **0** indica que o serviço de bastião não está disponível.

:::image type="content" source="./media/metrics-monitor-alert/communication-status.png" alt-text="Screenshot mostrando estado de comunicação.":::

#### <a name="total-memory"></a><a name="total-memory"></a>Memória total

Pode ver a memória total de Azure Bastion, dividida em cada instância de bastião.

:::image type="content" source="./media/metrics-monitor-alert/total-memory.png" alt-text="Screenshot mostrando memória total.":::

### <a name="traffic-metrics"></a><a name="traffic"></a>Métricas de tráfego

#### <a name="used-cpu"></a><a name="used-cpu"></a>CPU usado

Pode ver a utilização do CPU de Azure Bastion, dividida em cada instância de bastião. A monitorização desta métrica ajudará a medir a disponibilidade e a capacidade dos casos que compõem o Bastião Azure.

:::image type="content" source="./media/metrics-monitor-alert/used-cpu.png" alt-text="Screenshot mostrando CPU usado.":::

#### <a name="used-memory"></a><a name="used-memory"></a>Memória usada

Pode ver a utilização da memória em cada instância de bastião, dividida em cada instância de bastião. A monitorização desta métrica ajudará a medir a disponibilidade e a capacidade dos casos que compõem o Bastião Azure.

:::image type="content" source="./media/metrics-monitor-alert/used-memory.png" alt-text="Imagem mostrando memória usada.":::

### <a name="performance-metrics"></a><a name="performance"></a>Métricas de desempenho

#### <a name="session-count"></a>Contagem de sessão

Pode ver a contagem de sessões ativas por instância de bastião, agregadas em cada tipo de sessão (RDP e SSH). Cada Bastião Azure pode suportar uma gama de sessões de RDP e SSH ativas. A monitorização desta métrica irá ajudá-lo a entender se precisa de ajustar o número de casos que executam o serviço de bastião. Para obter mais informações sobre a contagem de sessão Azure Bastion pode suportar, consulte as FAQ do [Bastião Azure.](bastion-faq.md)

:::image type="content" source="./media/metrics-monitor-alert/session-count.png" alt-text="Screenshot mostrando contagem de sessão.":::

## <a name="how-to-view-metrics"></a><a name="metrics"></a>Como ver métricas

1. Para ver as métricas, navegue até ao seu anfitrião de bastião.
1. Na lista **de monitorização,** selecione **Métricas.**
1. Selecione os parâmetros. Se não forem definidas métricas, clique em **Adicionar métrica** e, em seguida, selecione os parâmetros.

   * **Âmbito:** Por predefinição, o âmbito é definido para o hospedeiro de bastião.
   * **Espaço de nome métrico:** Métricas Padrão.
   * **Métrica:** Selecione a métrica que pretende visualizar.

1. Uma vez selecionada uma métrica, a agregação padrão será aplicada. Opcionalmente, pode aplicar a divisão, que mostrará a métrica com diferentes dimensões.

## <a name="next-steps"></a>Passos seguintes

Leia as [FAQ do Bastião.](bastion-faq.md)
  
