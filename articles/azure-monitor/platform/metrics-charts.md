---
title: Recursos avançados do Explorador de métricas do Azure
description: Saiba mais sobre funcionalidades avançadas do Explorador de métricas do Azure Monitor
author: lingliw
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 04/12/19
ms.author: v-lingwu
ms.subservice: metrics
ms.openlocfilehash: 67e4281b24a7489cf202d82bdddbe99992aac095
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60256807"
---
# <a name="advanced-features-of-azure-metrics-explorer"></a>Recursos avançados do Explorador de métricas do Azure

> [!NOTE]
> Este artigo pressupõe que está familiarizado com funcionalidades básicas do Explorador de métricas. Se for um novo usuário e quer saber como criar seu primeiro gráfico de métricas, veja [introdução ao Explorador de métricas do Azure](metrics-getting-started.md).

## <a name="metrics-in-azure"></a>Métricas no Azure

[Métricas no Azure Monitor](data-platform-metrics.md) são a série de valores de medida e contagens de que são recolhidas e armazenadas ao longo do tempo. Existem métricas standard (ou "plataforma") e métricas personalizadas. As métricas standard são fornecidas pela própria plataforma do Azure. Métricas padrão refletem as estatísticas de estado de funcionamento e a utilização de recursos do Azure. Ao passo que as métricas personalizadas são enviadas para o Azure pelas suas aplicações com o [API do Application Insights para métricas e eventos personalizados](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics), [extensão do Windows Azure Diagnostics (WAD)](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostics-extension-overview), ou pelo [Azure Monitorizar a API de REST](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-store-custom-rest-api).

## <a name="create-views-with-multiple-metrics-and-charts"></a>Criar vistas com várias métricas e gráficos

Pode criar gráficos que desenhar várias linhas de métricas ou mostram vários gráficos de métricas ao mesmo tempo. Esta funcionalidade permite-lhe:

- correlacionar métricas relacionadas no mesmo gráfico para ver como um valor está relacionada para outro
- métricas de exibição com diferentes unidades de medida próximos
- visualmente, Agregar e comparar as métricas de vários recursos

Por exemplo, se tiver 5 contas de armazenamento e quiser saber a quantidade total de espaço é consumido entre elas, pode criar um gráfico de áreas (empilhadas) que mostra os indivíduos e a soma de todos os valores em pontos específicos no tempo.

### <a name="multiple-metrics-on-the-same-chart"></a>Várias métricas no mesmo gráfico

Primeiro, [criar um novo gráfico](metrics-getting-started.md#create-your-first-metric-chart). Clique em **métrica adicionar** e repita os passos para adicionar outra métrica no mesmo gráfico.

   > [!NOTE]
   > Geralmente não querem ter métricas com diferentes unidades de medida (ou seja, "milissegundos" e "quilobytes") ou com dimensionamento significativamente diferente num gráfico. Em vez disso, considere a utilização de vários gráficos. Clique no botão Adicionar gráfico para criar vários gráficos no Explorador de métricas.

### <a name="multiple-charts"></a>Vários gráficos

Clique nas **adicionar gráfico** e crie outro gráfico com uma métrica diferente.

### <a name="order-or-delete-multiple-charts"></a>Solicitar ou eliminar vários gráficos

Para encomendar ou eliminar vários gráficos, clicar nas elipses ( **...**  ) símbolo para abrir o menu de gráfico e escolha o item de menu apropriado **mover para cima**, **mover para baixo**, ou **eliminar**.

## <a name="apply-filters-to-charts"></a>Aplicar filtros para gráficos

Pode aplicar filtros para os gráficos que mostram as métricas com dimensões. Por exemplo, se a métrica "Contagem de transações" tem uma dimensão, "Tipo de resposta", que indica se a resposta das transações foi concluída com êxito ou falha, em seguida, filtrar esta dimensão seria desenhar uma linha do gráfico para apenas com êxito (ou apenas com falhas) transações. 

### <a name="to-add-a-filter"></a>Para adicionar um filtro

1. Selecione **Adicionar filtro** acima do gráfico

2. Selecione a dimensão (propriedade) que pretende filtrar

   ![imagem de métrica](./media/metrics-charts/00006.png)

3. Selecione os valores de dimensão que pretende incluir ao desenhar o gráfico (Este exemplo mostra a filtragem as transações de armazenamento com êxito):

   ![imagem de métrica](./media/metrics-charts/00007.png)

4. Depois de selecionar os valores de filtro, clique na direção oposta o Seletor de filtro para fechá-lo. Agora o gráfico mostra o número de transações de armazenamento tem falhado:

   ![imagem de métrica](./media/metrics-charts/00008.png)

5. Pode repetir os passos 1 a 4 para aplicar vários filtros para os mesmo gráficos.



## <a name="apply-splitting-to-a-chart"></a>Aplicam-se de que a divisão de um gráfico

Pode dividir uma métrica por dimensão para visualizar como diferentes segmentos da comparação de métrica em relação a si e identificar os segmentos afastados de uma dimensão.

### <a name="apply-splitting"></a>Aplicar divisão

1. Clique em **aplicam-se de que a divisão** acima do gráfico.
 
   > [!NOTE]
   > A divisão não pode ser utilizada com gráficos que têm várias métricas. Além disso, pode ter vários filtros, mas apenas uma dimensão de divisão aplicadas a qualquer único gráfico.

2. Escolha uma dimensão no qual pretende segmentar o gráfico:

   ![imagem de métrica](./media/metrics-charts/00010.png)

   Agora o gráfico mostra agora várias linhas, uma para cada segmento da dimensão:

   ![imagem de métrica](./media/metrics-charts/00012.png)

3. Clique de distância, a partir da **Seletor de agrupamento** para fechá-lo.

   > [!NOTE]
   > Utilize a filtragem e separação na mesma dimensão para ocultar os segmentos que são irrelevantes para o seu cenário e facilitam a leitura de gráficos.

## <a name="lock-boundaries-of-chart-y-axis"></a>Limites de bloqueio do eixo y do gráfico

Bloquear o intervalo do eixo y torna-se importante quando o gráfico mostra o menores flutuações de valores maiores. 

Por exemplo, quando o volume de pedidos com êxito cai de 99,99% a 99,5%, ele pode representar uma redução significativa na qualidade de serviço. No entanto, observar flutuação um pequeno valor numérico seria difícil ou até mesmo impossível as configurações de gráfico do padrão. Em vez disso pode bloquear o limite mais baixo do gráfico para 99%, o que tornaria esta lista pequena mais aparentes. 

Outro exemplo é uma flutuação na memória disponível, onde o valor tecnicamente nunca atingirá 0. Corrigir o intervalo a um valor mais alto pode fazer o cai na memória disponível mais fácil encontrá-las. 

Para controlar o intervalo do eixo y, utilize o "..." menu do gráfico e selecione **editar gráfico** para gráfico definições avançado de acesso. Modificar os valores na secção de intervalo do eixo y ou utilize **automática** botão para reverter para predefinições.

![imagem de métrica](./media/metrics-charts/00014-manually-set-granularity.png)

> [!WARNING]
> Bloquear os limites do eixo y os que monitorizam vários gráficos de conta ou soma num período de tempo (e, portanto, contagem de uso, soma, agregações mínimas ou máxima), normalmente, necessita de especificar uma granularidade de tempo fixo, em vez de confiar nas predefinições do automática. Isso é necessário porque os valores em gráficos de alteram quando a granularidade de tempo automaticamente é modificada pelo utilizador redimensionar a janela do browser ou vai da resolução do ecrã. Resultante alterar os efeitos de granularidade de tempo a aparência do gráfico, invalidando a seleção atual de intervalo do eixo y.

## <a name="pin-charts-to-dashboards"></a>Gráficos de PIN para dashboards

Depois de configurar os gráficos, pode querer adicioná-lo aos dashboards, para que possa vê-la novamente, possivelmente no contexto de outro telemetria de monitorização, ou partilhar com a sua equipa.

Para afixar um gráfico configurado a um dashboard:

Depois de configurar o seu gráfico, clique nas **ações do gráfico** menu no lado direito principais do gráfico e clique em **afixar ao dashboard**.

![imagem de métrica](./media/metrics-charts/00013.png)

## <a name="create-alert-rules"></a>Criar regras de alertas

Pode usar os critérios que definiu para visualizar as métricas, como a base de uma métrica baseada a regra de alerta. A nova regra de alerta irá incluir o recurso de destino, métrica, divisão e dimensões de filtro de gráfico. Será capaz de modificar estas definições mais tarde no painel de criação de regra de alerta.

### <a name="to-create-a-new-alert-rule-click-new-alert-rule"></a>Para criar uma nova regra de alerta, clique em **regra novo alerta**

![Botão de nova regra de alerta realçada em vermelho](./media/metrics-charts/015.png)

Será conduzido para o painel de criação de regra de alerta com as dimensões de métrica subjacentes do seu gráfico pré-preenchido, para que seja mais fácil gerar regras de alerta personalizadas.

![Criar regra de alerta](./media/metrics-charts/016.png)

Veja esta [artigo](alerts-metric.md) para saber mais sobre como configurar alertas de métricas.

## <a name="troubleshooting"></a>Resolução de problemas

*Não vejo todos os dados no meu gráfico.*

* Filtros de aplicam a todos os gráficos no painel. Certifique-se de que, embora se está a focar um gráfico, não defina um filtro que exclua todos os dados em outro.

* Se pretender definir filtros diferentes em gráficos de diferentes, criá-las nos diferentes painéis, salvá-los separados como favoritos. Se quiser, pode afixá-los ao dashboard para que pode vê-los em conjunto com outro.

* Se um gráfico de segmentar por uma propriedade que não está definida na métrica, em seguida, haverá nada no gráfico. Experimente limpar a segmentação (divisão) ou escolha uma propriedade diferente.

## <a name="next-steps"></a>Passos Seguintes

  Leia [criar dashboards de KPI personalizados](https://docs.microsoft.com/azure/application-insights/app-insights-tutorial-dashboards) para saber mais sobre as melhores práticas para a criação de dashboards passíveis de ação com a métrica.
