---
title: Explorador de métricas do Azure Monitor
description: Saiba mais sobre os novos recursos do Explorador de métricas do Azure Monitor
author: vgorbenko
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 01/22/2019
ms.author: vitalyg
ms.subservice: metrics
ms.openlocfilehash: 576a7948d4df7957d934fb4c8888712fa94cb202
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57337080"
---
# <a name="azure-monitor-metrics-explorer"></a>Explorador de métricas do Azure Monitor

Explorador de métricas do Azure Monitor é um componente do portal do Microsoft Azure que permite que o desenho de gráficos, visualmente correlacionar as tendências e investigar picos e quedas nos valores das métricas. Explorador de métricas é um ponto de partida essencial para vários problemas de disponibilidade com as suas aplicações e infraestrutura alojados no Azure ou monitorizado pelos serviços do Azure Monitor de desempenho e a investigar.

## <a name="metrics-in-azure"></a>Métricas no Azure

[Métricas no Azure Monitor](data-collection.md#metrics) são a série de valores de medida e contagens de que são recolhidas e armazenadas ao longo do tempo. Existem métricas standard (ou "plataforma") e métricas personalizadas. As métricas standard são fornecidas pela própria plataforma do Azure. Métricas padrão refletem as estatísticas de estado de funcionamento e a utilização de recursos do Azure. Ao passo que as métricas personalizadas são enviadas para o Azure pelas suas aplicações com o [API do Application Insights para métricas e eventos personalizados](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics), [extensão do Windows Azure Diagnostics (WAD)](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostics-extension-overview), ou pelo [Azure Monitorizar a API de REST](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-store-custom-rest-api).

## <a name="create-a-new-chart"></a>Crie um novo gráfico

1. Abra o portal do Azure
2. Navegue para a nova **Monitor** separador e, em seguida, selecione **métricas**.

   ![Imagem de métricas](./media/metrics-charts/00001.png)

3. O **Seletor de métrica** será automaticamente aberta para. Escolha um recurso a partir da lista para ver o respetiva das métricas associadas. Apenas os recursos com as métricas são apresentados na lista.

   ![Imagem de métricas](./media/metrics-charts/00002.png)

   > [!NOTE]
   >Se tiver mais do que uma subscrição do Azure, o Explorador de métricas obtém os recursos em todas as subscrições selecionadas nas definições do Portal -> filtro por lista de subscrições. Para alterá-lo, clique no ícone de engrenagem de definições de Portal na parte superior do ecrã e selecione as subscrições que pretende utilizar.

4. Para alguns tipos de recursos (contas de armazenamento e máquinas virtuais), antes de selecionar uma métrica tem de escolher uma **espaço de nomes**. Cada espaço de nomes carrega seu próprio conjunto de métricas que são relevantes para este espaço de nomes apenas e não para outros namespaces.

   Por exemplo, cada armazenamento do Azure tem as métricas para subservices "Blobs", "Ficheiros", "Filas" e "Tabelas", que são todas as partes da conta de armazenamento. No entanto, a métrica "contagem de mensagens da fila" é naturalmente aplicável para o subservice "Fila" e não para quaisquer outros subservices de conta de armazenamento.

   ![Imagem de métricas](./media/metrics-charts/00003.png)

5. Selecione uma métrica da lista. Se souber o nome parcial da métrica que pretende, pode começar escrevendo-o para ver uma lista filtrada de métricas disponíveis:

   ![Imagem de métricas](./media/metrics-charts/00004.png)

6. Depois de selecionar uma métrica, o gráfico serão compostos com a agregação predefinida para a métrica selecionada. Neste momento só precisa clicar em distância, a partir da **Seletor de métricas** para fechá-lo. Opcionalmente, também pode mudar o gráfico para uma agregação diferente. Para algumas métricas, alternância de agregação permite-lhe escolher qual o valor que pretende ver no gráfico. Por exemplo, pode alternar entre os valores de média, mínimos e máximo. 

7. Ao clicar em **adicionar métrica** e repetir os passos 3 a 6, pode adicionar mais métricas no mesmo gráfico.

   > [!NOTE]
   > Geralmente não querem ter métricas com diferentes unidades de medida (ou seja, "milissegundos" e "quilobytes") ou com dimensionamento significativamente diferente num gráfico. Em vez disso, considere a utilização de vários gráficos. Clique no botão Adicionar gráfico para criar vários gráficos no Explorador de métricas.

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

### <a name="to-apply-splitting"></a>Para aplicar a divisão

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

