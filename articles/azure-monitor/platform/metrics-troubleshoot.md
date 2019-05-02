---
title: Resolução de problemas de gráficos de métricas do Azure Monitor
description: Resolver problemas com a criar, personalizar ou interpretar os gráficos de métricas
author: vgorbenko
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: vitalyg
ms.subservice: metrics
ms.openlocfilehash: cff1bbefc3c54f7f9c02c646bd9eef528fe28c73
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/30/2019
ms.locfileid: "64939574"
---
# <a name="troubleshooting-metrics-charts"></a>Gráficos de métricas de resolução de problemas

Utilize este artigo caso se depare com problemas com a criar, personalizar ou a interpretação de gráficos no Explorador de métricas do Azure. Se estiver familiarizado com as métricas, saiba mais sobre [introdução ao Explorador de métricas](metrics-getting-started.md) e [recursos avançados do Explorador de métricas](metrics-charts.md). Também pode ver [exemplos](metric-chart-samples.md) dos gráficos de métricos configurados.

## <a name="cant-find-your-resource-to-select-it"></a>Não é possível localizar o recurso para selecioná-lo

Que clicou a **selecionar um recurso** botão, mas não a vir o seu recurso na caixa de diálogo do selecionador de recursos.

**Solução:** Explorador de métricas exige que selecione as subscrições e grupos de recursos antes de listagem de recursos disponíveis. Se não vir o seu recurso:

1. Certifique-se de que selecionou a subscrição correta no **subscrição** lista pendente. Se a sua subscrição não está listada, clique nas **diretório + subscrição definições** e adicionar uma subscrição com o seu recurso.

1. Certifique-se de que selecionou o grupo de recursos correto.
    > [!WARNING]
    > Para obter melhor desempenho, primeira vez que abrir o Explorador de métricas, o **grupo de recursos** suspensa não tem nenhum grupo de recursos previamente selecionado. Tem de escolher pelo menos um grupo para visualizar todos os recursos.

## <a name="chart-shows-no-data"></a>Gráfico mostra sem dados

Por vezes, os gráficos poderão mostrar não existem dados depois de selecionar recursos corretos e de métricas. Esse comportamento pode ser causado por várias das seguintes razões:

### <a name="microsoftinsights-resource-provider-isnt-registered-for-your-subscription"></a>Fornecedor de recursos de Microsoft. insights não está registado para a sua subscrição

Explorar métricas requer *Microsoft. insights* fornecedor de recursos registado na sua subscrição. Em muitos casos, está registado automaticamente (ou seja, depois de configurar uma regra de alerta, personalizar definições de diagnóstico para qualquer recurso ou configurar uma regra de dimensionamento automático). Se o fornecedor de recursos Microsoft. insights não está registado, tem de registar manualmente-la ao seguir os passos descritos [fornecedores de recursos do Azure e tipos de](../../azure-resource-manager/resource-manager-supported-services.md).

**Solução:** Open **subscrições**, **fornecedores de recursos** separador e certifique-se de que *Microsoft. insights* está registado para a sua subscrição.

### <a name="you-dont-have-sufficient-access-rights-to-your-resource"></a>Não tem direitos de acesso suficientes para o seu recurso

No Azure, o acesso à métrica é controlado pelas [controlo de acesso baseado em funções (RBAC)](../../role-based-access-control/overview.md). Tem de ser um membro de [leitor de monitorização](../../role-based-access-control/built-in-roles.md#monitoring-reader), [Contribuidor de monitorização](../../role-based-access-control/built-in-roles.md#monitoring-contributor), ou [contribuinte](../../role-based-access-control/built-in-roles.md#contributor) para explorar métricas para qualquer recurso.

**Solução:** Certifique-se de que tem permissões suficientes para o recurso a partir do qual está a explorar métricas.

### <a name="your-resource-didnt-emit-metrics-during-the-selected-time-range"></a>O recurso não emite métricas durante o intervalo de tempo selecionado

Alguns recursos não constantemente a emitir as suas métricas. Por exemplo, o Azure não irá recolher as métricas para máquinas virtuais paradas. Outros recursos podem emitir seus métricas apenas quando ocorre alguma condição. Por exemplo, uma métrica que mostra o tempo de processamento de uma transação requer, pelo menos, uma transação. Se não houve nenhuma transação no intervalo de tempo selecionado, o gráfico naturalmente estará vazio. Além disso, embora a maioria das métricas no Azure é recolhida a cada minuto, há alguns que são recolhidos com menos frequência. Consulte a documentação de métrica para obter mais detalhes sobre a métrica que está a tentar explorar.

**Solução:** Altere a hora do gráfico para uma gama mais vasta. Pode iniciar a partir de "Últimos 30 dias" através de uma maior granularidade de tempo (ou da entidade confiadora na opção "granularidade de tempo automática").

### <a name="you-picked-a-time-range-greater-than-30-days"></a>Selecionou um intervalo de tempo superior a 30 dias

[A maioria das métricas no Azure são armazenadas durante dias 93](data-platform-metrics.md#retention-of-metrics). No entanto, apenas pode consultar para não mais de 30 dias que vale a pena de dados em qualquer gráfico único. Esta limitação não se aplica a [as métricas baseadas no registo](../app/pre-aggregated-metrics-log-metrics.md#log-based-metrics).

**Solução:** Se vir um gráfico em branco ou o gráfico apresenta apenas a parte de dados de métrica, certifique-se de que a diferença entre o início - e -datas de término no selecionador de hora não excede o intervalo de 30 dias.

### <a name="all-metric-values-were-outside-of-the-locked-y-axis-range"></a>Todos os valores de métrica foram fora do intervalo do eixo y bloqueado

Por [bloqueio os limites do eixo y do gráfico](metrics-charts.md#lock-boundaries-of-chart-y-axis), inadvertidamente pode tornar a área de exibição de gráfico não mostrar a linha do gráfico. Por exemplo, se o eixo y está bloqueado para um intervalo entre 0% e 50% e a métrica tem um valor constante de 100%, a linha sempre é processada fora da área visível, tornando o gráfico aparecer em branco.

**Solução:** Certifique-se de que os limites de eixo y do gráfico não estão impedidos de fora do intervalo de valores de métrica. Se os limites do eixo y são bloqueados, pode querer temporariamente repô-los para garantir que a métrica valores não ficam fora do intervalo de gráfico. O intervalo do eixo y de bloqueio não é recomendado com granularidade de automática para os gráficos com **soma**, **Mín**, e **máximo** agregação porque os valores serão alterados com granularidade ao redimensionar a janela do browser ou vai da resolução do ecrã. Mudar de granularidade pode deixar a área de exibição de sua vazio de gráfico.

### <a name="you-are-looking-at-a-guest-os-metric-but-didnt-enable-azure-diagnostic-extension"></a>Está Considerando uma métrica de SO convidado, mas não tiver ativado a extensão de diagnóstico do Azure

Coleção de **SO convidado** métricas requer configurar a extensão de diagnóstico do Azure ou ativá-la utilizando o **das definições de diagnóstico** painel para o seu recurso.

**Solução:** Se a extensão de diagnóstico do Azure está ativada, mas ainda não conseguir ver as métricas, siga os passos descritos em [guia de resolução de problemas de extensão de diagnóstico do Azure](diagnostics-extension-troubleshooting.md#metric-data-doesnt-appear-in-the-azure-portal). Consulte também os passos de resolução de problemas para [não é possível escolher espaço de nomes do SO convidado e métricas](metrics-troubleshoot.md#cannot-pick-guest-os-namespace-and-metrics)

## <a name="error-retrieving-data-message-on-dashboard"></a>Mensagem de "Erro ao obter dados" no dashboard

Esse problema é comum quando o dashboard foi criado com uma métrica que mais tarde foi preterida e removida do Azure. Para verificar que é o caso, abra a **métricas** separador dos seus recursos e as métricas disponíveis no selecionador de métrica de verificação. Se a métrica não é apresentada, a métrica foi removida do Azure. Normalmente, quando uma métrica é preterida, existe uma nova melhor métrica que fornece uma perspectiva semelhante sobre o estado de funcionamento do recurso.

**Solução:** Atualize o mosaico de falha ao selecionar uma métrica alternativa para o gráfico no dashboard. Pode [reveja uma lista de métricas disponíveis para serviços do Azure](metrics-supported.md).

## <a name="chart-shows-dashed-line"></a>Gráfico mostra a linha tracejada

Gráficos de métricas do Azure utilizam o estilo de linha tracejada para indicar que existe um valor em falta (também conhecido como "valor nulo") entre o intervalo de agregação de tempo conhecido dois pontos de dados. Por exemplo, se no Seletor de tempo selecionou granularidade de tempo de "1 minuto", mas a métrica de funcionamento foi considerada em 07:26, 07:27, 07:29 e 07:30 (observe uma minuto lacuna entre pontos de dados de segunda e terceira), em seguida, uma linha tracejada conectará 07:27 e 07:29 e irá ligar uma linha sólida todos os outros pontos de dados. Os drops linha tracejada para zero quando utiliza a métrica **contagem** e **soma** agregação. Para o **avg**, **Mín** ou **máximo** agregações, a linha tracejada liga-se em dois pontos de dados conhecidos mais próximos. Além disso, quando os dados estão em falta no lado mais à direita ou esquerdo do gráfico, a linha tracejada se expande para a direção do ponto de dados em falta.
  ![imagem de métrica](./media/metrics-troubleshoot/missing-data-point-line-chart.png)

**Solução:** Este comportamento é propositado. É útil para identificar os pontos de dados em falta. O gráfico de linhas é uma opção superior para visualizar as tendências das métricas de alta densidade, mas pode ser difíceis de interpretar para as métricas com valores dispersas, especialmente quando corelating valores com grão de tempo é importante. A linha tracejada facilita a leitura nestes gráficos, mas se o gráfico é ainda claro, considere ver as métricas com um tipo de gráfico diferentes. Por exemplo, um gráfico de plotagem dispersos para a mesma métrica mostra claramente cada grão de tempo por só de visualização de um ponto quando existe um valor e a ignorar os dados de pontos de completamente quando o valor está em falta: ![imagem métrica](./media/metrics-troubleshoot/missing-data-point-scatter-chart.png)

   > [!NOTE]
   > Se preferir ainda um gráfico de linhas para sua métrica, movendo o mouse sobre o gráfico pode ajudar a avaliar a granularidade de tempo por destacar o ponto de dados no local do ponteiro do mouse.

## <a name="chart-shows-unexpected-drop-in-values"></a>Gráfico mostra o menu inesperado em valores

Em muitos casos, o drop percebido os valores de métrica é um mal-entendido os dados apresentados no gráfico. Pode se deixe enganar por uma queda no somas ou conta quando o gráfico mostra os mais recentes minutos porque o último ponto de dados de métrica não tenham sido recebido ou processado pelo Azure ainda. Dependendo do serviço, a latência das métricas de processamento pode ser dentro do intervalo de alguns minutos. Gráficos que mostra um intervalo de tempo recente com uma granularidade de 1 ou 5 minutos, de uma redução do valor durante os últimos minutos alguns torna-se mais perceptível: ![imagem métrica](./media/metrics-troubleshoot/drop-in-values.png)

**Solução:** Este comportamento é propositado. Acreditamos que é vantajoso que mostra os dados assim que podemos recebê-lo, mesmo quando os dados são *parcial* ou *incompleta*. Isso faz com que faça mais cedo conclusão importante e iniciar imediatamente a investigação. Por exemplo, para uma métrica que mostra o número de falhas, ver um valor parcial X indica que havia, pelo menos, X falhas num determinado minuto. Pode começar a investigar o problema imediatamente, em vez de esperar para ver a quantidade exata de falhas que aconteceu nesse minuto, o que pode não ser tão importante. O gráfico será atualizado depois de recebermos todo o conjunto de dados, mas nessa altura pode também mostrar novos pontos de dados incompletos de minutos mais recentes.

## <a name="cannot-pick-guest-os-namespace-and-metrics"></a>Não é possível escolher espaço de nomes do SO convidado e métricas

Máquinas virtuais e conjuntos de dimensionamento de máquinas virtuais têm duas categorias de métricas: **Anfitrião de máquinas virtuais** métricas que são recolhidas pelo ambiente de alojamento do Azure, e **SO convidado** métricas que são recolhidas pela [agente de monitorização](agents-overview.md) em execução nas suas máquinas virtuais. Instalar o agente de monitorização, ativando [extensão de diagnóstico do Azure](diagnostics-extension-overview.md).

Por predefinição, as métricas de SO convidado são armazenadas na conta de armazenamento do Azure, o que escolha entre o **das definições de diagnóstico** separador do seu recurso. Se não são recolhidas métricas de SO convidado ou o Explorador de métricas não é possível acessá-los, apenas será apresentada a **anfitrião da Máquina Virtual** espaço de nomes de métrica:

![imagem de métrica](./media/metrics-troubleshoot/cannot-pick-guest-os-namespace.png)

**Solução:** Se não vir **SO convidado** espaço de nomes e as métricas no Explorador de métricas:

1. Confirme que [extensão de diagnóstico do Azure](diagnostics-extension-overview.md) está ativado e configurado para recolher métricas.
    > [!WARNING]
    > Não é possível usar [agente do Log Analytics](agents-overview.md#log-analytics-agent) (também referido como o Microsoft Monitoring Agent, ou "MMA") para enviar **SO convidado** para uma conta de armazenamento.

1. Certifique-se de que a conta de armazenamento não está protegida pela firewall.

1. Utilize o [Explorador de armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/) para validar que as métricas estão a fluir para a conta de armazenamento. Se não são recolhidas métricas, siga os [guia de resolução de problemas de extensão de diagnóstico do Azure](diagnostics-extension-troubleshooting.md#metric-data-doesnt-appear-in-the-azure-portal).

## <a name="next-steps"></a>Passos Seguintes

* [Saiba mais sobre a introdução ao Explorador de métricas](metrics-getting-started.md)
* [Saiba mais sobre funcionalidades avançadas do Explorador de métricas](metrics-charts.md)
* [Ver uma lista de métricas disponíveis para serviços do Azure](metrics-supported.md)
* [Veja exemplos de gráficos configurados](metric-chart-samples.md)