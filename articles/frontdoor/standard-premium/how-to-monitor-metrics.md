---
title: Métricas de monitorização para Azure Front Door Standard/Premium
description: Este artigo descreve as métricas de monitorização Azure Front Door Standard/Premium.
services: frontdoor
author: duau
manager: KumudD
ms.service: frontdoor
ms.topic: how-to
ms.date: 02/18/2021
ms.author: yuajia
ms.openlocfilehash: 72388eb8006ff1b9628db5066dc63e6a0811f3d5
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105557331"
---
# <a name="real-time-monitoring-in-azure-front-door-standardpremium"></a>Monitorização em tempo real em Azure Front Door Standard/Premium

> [!Note]
> Esta documentação destina-se ao Azure Front Door Standard/Premium (Preview). À procura de informações sobre a Porta da Frente Azure? Ver [aqui](../front-door-overview.md).

O Azure Front Door Standard/Premium está integrado com o Azure Monitor e tem 11 métricas para ajudar a monitorizar o Azure Front Door Standard/Premium em tempo real para rastrear, resolver problemas e depurar problemas.  

Azure Front Door Standard/Premium mede e envia as suas métricas em intervalos de 60 segundos. As métricas podem levar até 3 minutos para aparecer no portal. As métricas podem ser exibidas em gráficos ou grelha à sua escolha e são acessíveis através do portal, PowerShell, CLI e API. Para obter mais informações, consulte as [métricas do Monitor Azure.](../../azure-monitor/essentials/data-platform-metrics.md)  

As métricas predefinidos são gratuitas. Pode ativar métricas adicionais por um custo extra. 

Pode configurar alertas para cada métrica, como um limiar para 4XXErrorRate ou 5XXErrorRate. Quando a taxa de erro exceder o limiar, irá desencadear um alerta conforme configurado. Para obter mais informações, consulte [Criar, ver e gerir alertas métricos utilizando o Azure Monitor](../../azure-monitor/alerts/alerts-metric.md). 

> [!IMPORTANT]
> Azure Front Door Standard/Premium (Preview) está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="metrics-supported-in-azure-front-door-standardpremium"></a>Métricas suportadas em Azure Front Door Standard/Premium

| Métricas  | Description | Dimensões |
| ------------- | ------------- | ------------- |
| Rácio bytes Hit | A percentagem de saídas da cache AFD, calculada contra o total da saída. </br> **Byte Hit Ratio** = (saída da borda - saída da origem)/saída da borda. </br> **Cenários excluídos no cálculo do rácio de impacto dos bytes:**</br> 1. Não configura explicitamente nenhuma cache através do comportamento de caching de regras ou de tipo de corda de consulta. </br> 2. Configura explicitamente a diretiva relativa ao controlo de caches com cache não armazenado ou privado. </br>3. A razão de paragem do byte pode ser baixa se a maior parte do tráfego for reencaminhada para a origem em vez de ser servida de caching com base nas suas configurações ou cenários. | Ponto final |
| PedidoCount | O número de pedidos de clientes servidos pela CDN. | Endpoint, país cliente, região cliente, estado HTTP, grupo de status HTTP |
| Tamanho das respostas | O número de pedidos de clientes servidos pela AFD. |Endpoint, país cliente, região cliente, estado HTTP, grupo de status HTTP |
| TotalLatency | O tempo total do pedido de cliente recebido pela CDN **até à última resposta byte enviar da CDN para o cliente**. |Endpoint, país cliente, região cliente, estado HTTP, grupo de status HTTP |
| Solicitação | O número de bytes enviados como pedidos de clientes para a AFD. | Endpoint, país cliente, região cliente, estado HTTP, grupo de status HTTP |
| Erro 4XX % | A percentagem de todos os pedidos de cliente para os quais o código de estado de resposta é 4XX. | Endpoint, País cliente, Região cliente |
| Erro de 5XX % | A percentagem de todos os pedidos de cliente para os quais o código de estado de resposta é 5XX. | Endpoint, País cliente, Região cliente |
| OriginRequestCount  | O número de pedidos enviados da AFD para a origem | Ponto final, origem, estado HTTP, grupo de status HTTP |
| OriginLatency | O tempo calculado a partir do momento em que o pedido foi enviado pela borda da AFD para o backend até que a AFD recebeu a última resposta byte do backend. | Ponto final, Origem |
| OrigemHealth% | A percentagem de sondas de saúde bem sucedidas da AFD até à origem.| Origin, Grupo Origin |
| Contagem de pedidos da WAF | Pedido de WAF combinado. | Ação, nome de regra, Nome da Política |

## <a name="access-metrics-in-azure-portal"></a>Métricas de Acesso no portal Azure

1. A partir do menu do portal Azure, selecione **Todos os**  >>  **\<your-AFD Standard/Premium (Preview) -profile>** Recursos.

2. Em **Monitorização,** selecione **Métricas:**

3. Em **Métricas,** selecione a métrica para adicionar:

   :::image type="content" source="../media/how-to-monitoring-metrics/front-door-metrics-1.png" alt-text="Screenshot da página de métricas." lightbox="../media/how-to-monitoring-metrics/front-door-metrics-1-expanded.png":::

4. **Selecione Adicionar filtro** para adicionar um filtro:

    :::image type="content" source="../media/how-to-monitoring-metrics/front-door-metrics-2.png" alt-text="Screenshot de adicionar filtros às métricas." lightbox="../media/how-to-monitoring-metrics/front-door-metrics-2-expanded.png":::
    
5. Selecione **Aplicar a divisão** para dividir dados por diferentes dimensões:

   :::image type="content" source="../media/how-to-monitoring-metrics/front-door-metrics-4.png" alt-text="Screenshot de adicionar dimensões às métricas." lightbox="../media/how-to-monitoring-metrics/front-door-metrics-4-expanded.png":::

6. Selecione **novo gráfico** para adicionar um novo gráfico:

## <a name="configure-alerts-in-azure-portal"></a>Alertas de Configuração no portal Azure

1. Configurar alertas no Azure Front Door Standard/Premium (Preview) selecionando Alertas **de**  >>  **Monitorização**.

1. Selecione **Nova regra de alerta** para as métricas listadas na secção Métricas.

O alerta será carregado com base no Monitor Azure. Para obter mais informações sobre alertas, consulte [os alertas do Azure Monitor](../../azure-monitor/alerts/alerts-overview.md).

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [O Azure Front Door Standard/Premium Reports](how-to-reports.md).
- Saiba mais sobre [Azure Front Door Standard/Premium Logs](how-to-logs.md).