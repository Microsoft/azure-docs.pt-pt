---
title: Gráficos métricos do Monitor Azure de resolução de problemas
description: Resolver problemas com a criação, personalização ou interpretação de gráficos métricos
author: vgorbenko
services: azure-monitor
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: vitalyg
ms.subservice: metrics
ms.openlocfilehash: 54f99f2f8708fca9c02950a8886a2a9b976a93dd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89440682"
---
# <a name="troubleshooting-metrics-charts"></a>Troubleshooting metrics charts (Resolução de problemas de gráficos de métricas)

Utilize este artigo se tiver problemas com a criação, personalização ou interpretação de gráficos no explorador de métricas Azure. Se é novo em métricas, aprenda [a começar com o explorador de métricas](metrics-getting-started.md) e as [funcionalidades avançadas do explorador de métricas.](metrics-charts.md) Também pode ver exemplos dos [gráficos](metric-chart-samples.md) métricos configurados.

## <a name="cant-find-your-resource-to-select-it"></a>Não consegue encontrar o seu recurso para selecioná-lo

Clicou no botão **Selecionar um recurso**, mas não vê o seu recurso na caixa de diálogo do selecionador de recursos.

**Solução:** O explorador de métricas requer que selecione subscrições e grupos de recursos antes de listar os recursos disponíveis. Se não vir o seu recurso:

1. Confirme que selecionou a subscrição correta na lista pendente **Subscrição**. Se a sua subscrição não estiver listada, clique nas **definições de Diretório + Subscrição** e adicione uma subscrição com o seu recurso.

1. Confirme que selecionou o grupo de recursos correto.
    > [!WARNING]
    > Para obter o melhor desempenho, quando abrir pela primeira vez o explorador de métricas, a lista pendente **Grupo de recursos** não tem qualquer grupo de recursos previamente selecionado. Tem de escolher, pelo menos, um grupo antes de poder ver quaisquer recursos.

## <a name="chart-shows-no-data"></a>Gráfico não mostra dados

Por vezes, os gráficos podem não mostrar dados depois de selecionar recursos e métricas corretas. Este comportamento pode ser causado por várias das seguintes razões:

### <a name="microsoftinsights-resource-provider-isnt-registered-for-your-subscription"></a>O fornecedor de recursos Microsoft.Insights não está registado para a sua subscrição

Explorar métricas requer que o fornecedor de recursos *Microsoft.Insights* esteja registado na sua subscrição. Em muitos casos, este é registado automaticamente (isto é, depois de configurar uma regra de alerta, personalizar as definições de diagnóstico de qualquer recurso ou configurar uma regra de dimensionamento automático). Se o fornecedor de recursos Microsoft.Insights não estiver registado, deve registá-lo manualmente seguindo as etapas descritas nos [fornecedores e tipos de recursos Azure](../../azure-resource-manager/management/resource-providers-and-types.md).

**Solução:** Abrir **subscrições,** separador **fornecedores de recursos** e verificar se o *Microsoft.Insights* está registado para a sua subscrição.

### <a name="you-dont-have-sufficient-access-rights-to-your-resource"></a>Não tem direitos de acesso suficientes para o seu recurso

Em Azure, o acesso às métricas é controlado pelo [controlo de acesso baseado em funções Azure (Azure RBAC)](../../role-based-access-control/overview.md). Tem de ser um membro do [leitor de monitorização](../../role-based-access-control/built-in-roles.md#monitoring-reader), [contribuidor de monitorização](../../role-based-access-control/built-in-roles.md#monitoring-contributor) ou [contribuidor](../../role-based-access-control/built-in-roles.md#contributor) para explorar as métricas de qualquer recurso.

**Solução:** Certifique-se de que tem permissões suficientes para o recurso a partir do qual está a explorar métricas.

### <a name="your-resource-didnt-emit-metrics-during-the-selected-time-range"></a>O seu recurso não emitiu métricas durante o intervalo de tempo selecionado

Alguns recursos não emitem constantemente as respetivas métricas. Por exemplo, o Azure não recolhe métricas de máquinas virtuais paradas. Outros recursos podem emitir as respetivas métricas apenas quando ocorre alguma condição. Por exemplo, uma métrica que mostra o tempo de processamento de uma transação requer, pelo menos, uma transação. Se não houver qualquer transação no intervalo de tempo selecionado, o gráfico naturalmente vai estar vazio. Além disso, embora a maioria das métricas no Azure seja recolhida a cada minuto, algumas são recolhidas com menor frequência. Veja a documentação das métricas para obter mais detalhes sobre a métrica que está a tentar explorar.

**Solução:** Altere o tempo da tabela para um intervalo mais largo. Pode começar a partir de "Últimos 30 dias" utilizando uma granularidade de maior tempo (ou contando com a opção "Granularidade automática do tempo").

### <a name="you-picked-a-time-range-greater-than-30-days"></a>Selecionou um intervalo de tempo superior a 30 dias

[A maioria das métricas no Azure são armazenadas durante 93 dias](data-platform-metrics.md#retention-of-metrics). No entanto, apenas pode consultar até 30 dias de dados em qualquer gráfico. Esta limitação não se aplica a [métricas baseadas no registo](../app/pre-aggregated-metrics-log-metrics.md#log-based-metrics).

**Solução:** Se vir um gráfico em branco ou o seu gráfico apenas apresentar uma parte dos dados métricos, verifique se a diferença entre datas de início e fim no selecionador de tempo não excede o intervalo de 30 dias.

### <a name="all-metric-values-were-outside-of-the-locked-y-axis-range"></a>Todos os valores de métricas estavam fora do intervalo do eixo Y bloqueado

Ao [bloquear os limites do eixo Y do gráfico](metrics-charts.md#lock-boundaries-of-chart-y-axis) pode, inadvertidamente, fazer com que a área de exibição do gráfico não mostre a linha do gráfico. Por exemplo, se o eixo Y estiver bloqueado num intervalo entre 0% e 50% e a métrica tiver um valor constante de 100%, a linha é sempre processada fora da área visível, transmitindo a ideia de que o gráfico está em branco.

**Solução:** Verifique se os limites do eixo y do gráfico não estão bloqueados fora do alcance dos valores métricos. Se os limites do eixo Y estiverem bloqueados, é recomendável que os reponha temporariamente para garantir que os valores das métricas não ficam fora do intervalo do gráfico. Não é recomendado bloquear o intervalo do eixo Y com uma granularidade automática para os gráficos com as agregações **soma**, **mín.** e **máx.**, uma vez que os valores vão mudar com a granularidade ao redimensionar a janela do browser ou ao passar de uma resolução de ecrã para outra. Mudar a granularidade pode deixar a área de exibição do seu gráfico vazia.

### <a name="you-are-looking-at-a-guest-os-metric-but-didnt-enable-azure-diagnostic-extension"></a>Você está olhando para uma métrica de SO convidado, mas não permitiu a extensão de diagnóstico Azure

A recolha de métricas do **SO Convidado** requer a configuração da Extensão de Diagnóstico do Azure ou a ativação da mesma com o painel **Definições de Diagnóstico** para o seu recurso.

**Solução:** Se a extensão de diagnóstico Azure estiver ativada, mas ainda não conseguir ver as suas métricas, siga os passos descritos no [guia de resolução de problemas de extensão de diagnóstico Azure](diagnostics-extension-troubleshooting.md#metric-data-doesnt-appear-in-the-azure-portal). Consulte também os passos de resolução de problemas para [Não pode escolher o espaço de nome do Guest OS e métricas](#cannot-pick-guest-os-namespace-and-metrics)

## <a name="error-retrieving-data-message-on-dashboard"></a>Mensagem de "obtenção de erros" no painel de instrumentos

Este problema pode ocorrer quando o dashboard foi criado com uma métrica que foi preterida e removida do Azure posteriormente. Para verificar se é o caso, abra o separador **Métricas** dos seus recursos e verifique as métricas disponíveis no selecionador de métricas. Se a métrica não for apresentada, foi removida do Azure. Normalmente, quando uma métrica é preterida, existe uma métrica nova e melhor que oferece uma perspetiva semelhante sobre o estado de funcionamento do recurso.

**Solução:** Atualize o azulejo de falha escolhendo uma métrica alternativa para o seu gráfico no painel de instrumentos. Pode [analisar uma lista de métricas disponíveis para serviços do Azure](metrics-supported.md).

## <a name="chart-shows-dashed-line"></a>Gráfico mostra linha tracejada

Os gráficos de métricas azure usam o estilo de linha tracejado para indicar que há um valor em falta (também conhecido como "valor nulo") entre dois pontos de dados de grãos de tempo conhecidos. Por exemplo, se no seletor de tempo escolheu a granularidade de tempo de "1 minuto", mas a métrica foi reportada às 07:26, 07:27, 07:29 e 07:30 (note um intervalo de minutos entre segundo e terceiro pontos de dados), então uma linha tracejada ligará 07:27 e 07:29 e uma linha sólida ligará todos os outros pontos de dados. A linha tracejada desce para zero quando a métrica utiliza **a contagem** e a agregação de **soma.** Para as agregações **avg**, **min** ou **max,** a linha tracejada liga dois pontos de dados mais próximos conhecidos. Além disso, quando os dados estão em falta no lado mais à direita ou mais à esquerda do gráfico, a linha tracejada expande-se para a direção do ponto de dados em falta.
  ![imagem da métrica](./media/metrics-troubleshoot/missing-data-point-line-chart.png)

**Solução:** Este comportamento é por desígnio. É útil para identificar pontos de dados em falta. O gráfico de linha é uma escolha superior para visualizar tendências de métricas de alta densidade, mas pode ser difícil de interpretar para as métricas com valores escassos, especialmente quando os valores de coreização com grão de tempo é importante. A linha tracejada facilita a leitura destes gráficos, mas se o gráfico ainda não for claro, considere ver as métricas com um tipo de gráfico diferente. Por exemplo, um gráfico de enredo disperso para a mesma métrica mostra claramente cada vez que o grão apenas visualiza um ponto quando há um valor e saltando completamente o ponto de dados quando o valor está em falta: ![ imagem métrica](./media/metrics-troubleshoot/missing-data-point-scatter-chart.png)

   > [!NOTE]
   > Se continuar a preferir um gráfico de linhas para a sua métrica, mover o rato sobre o gráfico pode ajudar a avaliar a granularidade do tempo ao destacar o ponto de dados no local do ponteiro do rato.

## <a name="chart-shows-unexpected-drop-in-values"></a>Gráfico mostra queda inesperada de valores

Em muitos casos, a queda percebida nas métricas resulta da falta de compreensão dos dados apresentados no gráfico. Pode deixar-se enganar por uma queda nas somas ou contagens quando o gráfico mostra os minutos mais recentes, uma vez que os últimos pontos de dados métricos não ainda não foram recebidos nem processados pelo Azure. Consoante o serviço, a latência das métricas de processamento pode ficar dentro de um intervalo de alguns minutos. Para gráficos que mostram um intervalo de tempo recente com uma granularidade de 1 ou 5 minutos, uma queda do valor ao longo dos últimos minutos torna-se mais percetível: ![ imagem métrica](./media/metrics-troubleshoot/drop-in-values.png)

**Solução:** Este comportamento é por desígnio. Acreditamos que é vantajoso mostrar os dados assim que os recebemos, mesmo quando são *parciais* ou *incompletos*. Tal permite-lhe tirar conclusões importantes mais cedo e iniciar imediatamente a investigação. Por exemplo, para uma métrica que mostra o número de falhas, ver um valor X parcial indica-nos que havia, pelo menos, X falhas num determinado minuto. Pode iniciar a investigação do problema imediatamente, ao invés de esperar para ver a quantidade exata de falhas que aconteceram nesse minuto, algo que pode não ser tão importante. O gráfico vai ser atualizado depois de recebermos todo o conjunto de dados, mas, nessa altura, também pode mostrar novos pontos de dados incompletos de minutos mais recentes.

## <a name="cannot-pick-guest-os-namespace-and-metrics"></a>Não é possível escolher o espaço de nome do Guest OS e métricas

As máquinas virtuais e os conjuntos de dimensionamento de máquinas virtuais têm duas categorias de métricas: Métricas do **Anfitrião de Máquinas Virtuais** que são recolhidas pelo ambiente de alojamento do Azure e métricas do **SO Convidado (clássico)** que são recolhidas pelo [agente de monitorização](agents-overview.md) em execução nas máquinas virtuais. Instale o agente de monitorização ao ativar a [Extensão de Diagnóstico do Azure](diagnostics-extension-overview.md).

Por predefinição, as métricas do SO convidado são armazenadas na conta de Armazenamento do Azure, que escolhe no separador **Definições de diagnóstico** do seu recurso. Se as métricas do SO convidado não forem recolhidas ou se o Explorador de Métricas não conseguir aceder às mesmas, apenas será apresentado o espaço de nomes de métricas do **Anfitrião de Máquinas Virtuais**:

![imagem da métrica](./media/metrics-troubleshoot/cannot-pick-guest-os-namespace.png)

**Solução:** Se não vir **o guest OS (clássico)** espaço de nome e métricas no explorador de métricas:

1. Confirme que a [Extensão de Diagnóstico do Azure](diagnostics-extension-overview.md) está ativada e configurada para recolher métricas.
    > [!WARNING]
    > Não pode utilizar o [agente do Log Analytics](agents-overview.md#log-analytics-agent) (também referido como o Agente de Monitorização da Microsoft ou "MMA") para enviar o **SO convidado** para uma conta de armazenamento.

1. Certifique-se de que o fornecedor de recursos **Microsoft.Insights** está [registado para a sua subscrição.](#microsoftinsights-resource-provider-isnt-registered-for-your-subscription)

1. Confirme que a conta de armazenamento não está protegida pela firewall. O portal do Azure precisa do acesso à conta de armazenamento para obter dados de métricas e para delinear os gráficos.

1. Utilize [o Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) para validar que as métricas estão a fluir para a conta de armazenamento. Se não forem recolhidas métricas, siga o [guia de resolução de problemas de Extensão de Diagnóstico do Azure](diagnostics-extension-troubleshooting.md#metric-data-doesnt-appear-in-the-azure-portal).

## <a name="next-steps"></a>Passos seguintes

* [Saiba como começar com o Metric Explorer](metrics-getting-started.md)
* [Conheça as funcionalidades avançadas do Metric Explorer](metrics-charts.md)
* [See a list of available metrics for Azure services](metrics-supported.md) (Ver uma lista de métricas disponíveis para serviços do Azure)
* [See examples of configured charts](metric-chart-samples.md) (Ver exemplos de gráficos configurados)
