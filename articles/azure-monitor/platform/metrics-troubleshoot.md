---
title: Solução de problemas Azure Monitor gráficos de métricas
description: Solucionar problemas com a criação, personalização ou interpretação de gráficos de métricas
author: vgorbenko
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: vitalyg
ms.subservice: metrics
ms.openlocfilehash: d31b046bf02893affff84069ee92b3bd7735b904
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/10/2019
ms.locfileid: "72243229"
---
# <a name="troubleshooting-metrics-charts"></a>Solução de problemas de gráficos de métricas

Use este artigo se você encontrar problemas com a criação, personalização ou interpretação de gráficos no Azure Metrics Explorer. Se você for novo nas métricas, saiba mais sobre como [começar a usar o Metrics Explorer](metrics-getting-started.md) e [recursos avançados do Metrics Explorer](metrics-charts.md). Você também pode ver [exemplos](metric-chart-samples.md) dos gráficos de métricas configurados.

## <a name="cant-find-your-resource-to-select-it"></a>Não é possível localizar seu recurso para selecioná-lo

Clicou no botão **Selecionar um recurso**, mas não vê o seu recurso na caixa de diálogo do selecionador de recursos.

**Solução:** O Metrics Explorer exige que você selecione assinaturas e grupos de recursos antes de listar os recursos disponíveis. Se não vir o seu recurso:

1. Confirme que selecionou a subscrição correta na lista pendente **Subscrição**. Se a sua subscrição não estiver listada, clique nas **definições de Diretório + Subscrição** e adicione uma subscrição com o seu recurso.

1. Confirme que selecionou o grupo de recursos correto.
    > [!WARNING]
    > Para obter o melhor desempenho, quando você abre o Metrics Explorer pela primeira vez, o menu suspenso **grupo de recursos** não tem grupos de recursos previamente selecionados. Tem de escolher, pelo menos, um grupo antes de poder ver quaisquer recursos.

## <a name="chart-shows-no-data"></a>O gráfico não mostra dados

Às vezes, os gráficos podem não mostrar nenhum dado depois de selecionar os recursos e as métricas corretos. Esse comportamento pode ser causado por vários dos seguintes motivos:

### <a name="microsoftinsights-resource-provider-isnt-registered-for-your-subscription"></a>O provedor de recursos Microsoft. insights não está registrado para sua assinatura

Explorar métricas requer que o fornecedor de recursos *Microsoft.Insights* esteja registado na sua subscrição. Em muitos casos, este é registado automaticamente (isto é, depois de configurar uma regra de alerta, personalizar as definições de diagnóstico de qualquer recurso ou configurar uma regra de dimensionamento automático). Se o provedor de recursos Microsoft. insights não estiver registrado, você deverá registrá-lo manualmente seguindo as etapas descritas em [provedores de recursos e tipos do Azure](../../azure-resource-manager/resource-manager-supported-services.md).

**Solução:** Abra **assinaturas**, guia **provedores de recursos** e verifique se o *Microsoft. insights* está registrado para sua assinatura.

### <a name="you-dont-have-sufficient-access-rights-to-your-resource"></a>Você não tem direitos de acesso suficientes ao seu recurso

No Azure, o acesso às métricas é controlado pelo [controlo de acesso baseado em funções (RBAC)](../../role-based-access-control/overview.md). Tem de ser um membro do [leitor de monitorização](../../role-based-access-control/built-in-roles.md#monitoring-reader), [contribuidor de monitorização](../../role-based-access-control/built-in-roles.md#monitoring-contributor) ou [contribuidor](../../role-based-access-control/built-in-roles.md#contributor) para explorar as métricas de qualquer recurso.

**Solução:** Verifique se você tem permissões suficientes para o recurso do qual você está explorando métricas.

### <a name="your-resource-didnt-emit-metrics-during-the-selected-time-range"></a>O recurso não emitiu métricas durante o intervalo de tempo selecionado

Alguns recursos não emitem constantemente as respetivas métricas. Por exemplo, o Azure não recolhe métricas de máquinas virtuais paradas. Outros recursos podem emitir as respetivas métricas apenas quando ocorre alguma condição. Por exemplo, uma métrica que mostra o tempo de processamento de uma transação requer, pelo menos, uma transação. Se não houver qualquer transação no intervalo de tempo selecionado, o gráfico naturalmente vai estar vazio. Além disso, embora a maioria das métricas no Azure seja recolhida a cada minuto, algumas são recolhidas com menor frequência. Veja a documentação das métricas para obter mais detalhes sobre a métrica que está a tentar explorar.

**Solução:** Altere a hora do gráfico para um intervalo mais amplo. Você pode começar com os "últimos 30 dias" usando uma granularidade de tempo maior (ou contando com a opção "granularidade automática de tempo").

### <a name="you-picked-a-time-range-greater-than-30-days"></a>Você escolheu um intervalo de tempo maior que 30 dias

[A maioria das métricas no Azure são armazenadas durante 93 dias](data-platform-metrics.md#retention-of-metrics). No entanto, apenas pode consultar até 30 dias de dados em qualquer gráfico. Esta limitação não se aplica a [métricas baseadas no registo](../app/pre-aggregated-metrics-log-metrics.md#log-based-metrics).

**Solução:** Se você vir um gráfico em branco ou o gráfico exibir apenas parte dos dados da métrica, verifique se a diferença entre as datas de início e término no seletor de hora não excede o intervalo de 30 dias.

### <a name="all-metric-values-were-outside-of-the-locked-y-axis-range"></a>Todos os valores de métrica estavam fora do intervalo de eixo y bloqueado

Ao [bloquear os limites do eixo Y do gráfico](metrics-charts.md#lock-boundaries-of-chart-y-axis) pode, inadvertidamente, fazer com que a área de exibição do gráfico não mostre a linha do gráfico. Por exemplo, se o eixo Y estiver bloqueado num intervalo entre 0% e 50% e a métrica tiver um valor constante de 100%, a linha é sempre processada fora da área visível, transmitindo a ideia de que o gráfico está em branco.

**Solução:** Verifique se os limites do eixo y do gráfico não estão bloqueados fora do intervalo dos valores de métrica. Se os limites do eixo Y estiverem bloqueados, é recomendável que os reponha temporariamente para garantir que os valores das métricas não ficam fora do intervalo do gráfico. Não é recomendado bloquear o intervalo do eixo Y com uma granularidade automática para os gráficos com as agregações **soma**, **mín.** e **máx.** , uma vez que os valores vão mudar com a granularidade ao redimensionar a janela do browser ou ao passar de uma resolução de ecrã para outra. Mudar a granularidade pode deixar a área de exibição do seu gráfico vazia.

### <a name="you-are-looking-at-a-guest-os-metric-but-didnt-enable-azure-diagnostic-extension"></a>Você está olhando para uma métrica de SO convidado, mas não habilitou a extensão de diagnóstico do Azure

A recolha de métricas do **SO Convidado** requer a configuração da Extensão de Diagnóstico do Azure ou a ativação da mesma com o painel **Definições de Diagnóstico** para o seu recurso.

**Solução:** Se Diagnóstico do Azure extensão estiver habilitada, mas você ainda não conseguir ver suas métricas, siga as etapas descritas no [Guia de solução de problemas de extensão diagnóstico do Azure](diagnostics-extension-troubleshooting.md#metric-data-doesnt-appear-in-the-azure-portal). Consulte também as etapas de solução de problemas para [não pode escolher o namespace e as métricas do SO convidado](metrics-troubleshoot.md#cannot-pick-guest-os-namespace-and-metrics)

## <a name="error-retrieving-data-message-on-dashboard"></a>Mensagem "erro ao recuperar dados" no painel

Este problema pode ocorrer quando o dashboard foi criado com uma métrica que foi preterida e removida do Azure posteriormente. Para verificar se é o caso, abra o separador **Métricas** dos seus recursos e verifique as métricas disponíveis no selecionador de métricas. Se a métrica não for apresentada, foi removida do Azure. Normalmente, quando uma métrica é preterida, existe uma métrica nova e melhor que oferece uma perspetiva semelhante sobre o estado de funcionamento do recurso.

**Solução:** Atualize o bloco com falha selecionando uma métrica alternativa para seu gráfico no painel. Pode [analisar uma lista de métricas disponíveis para serviços do Azure](metrics-supported.md).

## <a name="chart-shows-dashed-line"></a>Gráfico mostra linha tracejada

Os gráficos de métricas do Azure usam o estilo de linha tracejado para indicar que há um valor ausente (também conhecido como "valor nulo") entre dois pontos de dados de intervalo de tempo conhecidos. Por exemplo, se no seletor de tempo você escolheu a granularidade de tempo "1 minuto", mas a métrica foi relatada às 07:26, 07:27, 07:29 e 07:30 (Observe uma lacuna de minuto entre o segundo e o terceiro ponto de dados), uma linha tracejada se conectará a 07:27 e 07:29 e uma linha sólida se conectará todos os outros pontos de dados. A linha tracejada cai para zero quando a métrica usa a agregação **Count** e **sum** . Para as agregações **AVG**, **min** ou **Max** , a linha tracejada conecta dois pontos de dados conhecidos mais próximos. Além disso, quando os dados estão em falta no lado mais à direita ou mais à esquerda do gráfico, a linha tracejada expande-se para a direção do ponto de dados em falta.
  imagem ![metric @ no__t-1

**Solução:** Esse comportamento é por design. É útil para identificar pontos de dados em falta. O gráfico de linhas é uma opção superior para a visualização de tendências de métricas de alta densidade, mas pode ser difícil interpretar para as métricas com valores esparsos, especialmente quando valores corelacionados com o intervalo de tempo são importantes. A linha tracejada facilita a leitura destes gráficos, mas se o gráfico ainda não for claro, considere ver as métricas com um tipo de gráfico diferente. Por exemplo, um gráfico de gráficos dispersos para a mesma métrica claramente mostra cada intervalo de tempo apenas visualizando um ponto quando há um valor e ignorando o ponto de dados quando o valor está ausente: imagem ![metric @ no__t-1

   > [!NOTE]
   > Se continuar a preferir um gráfico de linhas para a sua métrica, mover o rato sobre o gráfico pode ajudar a avaliar a granularidade do tempo ao destacar o ponto de dados no local do ponteiro do rato.

## <a name="chart-shows-unexpected-drop-in-values"></a>O gráfico mostra valores de queda inesperados

Em muitos casos, a queda percebida nas métricas resulta da falta de compreensão dos dados apresentados no gráfico. Pode deixar-se enganar por uma queda nas somas ou contagens quando o gráfico mostra os minutos mais recentes, uma vez que os últimos pontos de dados métricos não ainda não foram recebidos nem processados pelo Azure. Consoante o serviço, a latência das métricas de processamento pode ficar dentro de um intervalo de alguns minutos. Para gráficos mostrando um intervalo de tempo recente com uma granularidade de 1 ou 5 minutos, uma queda do valor nos últimos minutos torna-se mais perceptível: imagem ![metric @ no__t-1

**Solução:** Esse comportamento é por design. Acreditamos que é vantajoso mostrar os dados assim que os recebemos, mesmo quando são *parciais* ou *incompletos*. Tal permite-lhe tirar conclusões importantes mais cedo e iniciar imediatamente a investigação. Por exemplo, para uma métrica que mostra o número de falhas, ver um valor X parcial indica-nos que havia, pelo menos, X falhas num determinado minuto. Pode iniciar a investigação do problema imediatamente, ao invés de esperar para ver a quantidade exata de falhas que aconteceram nesse minuto, algo que pode não ser tão importante. O gráfico vai ser atualizado depois de recebermos todo o conjunto de dados, mas, nessa altura, também pode mostrar novos pontos de dados incompletos de minutos mais recentes.

## <a name="cannot-pick-guest-os-namespace-and-metrics"></a>Não é possível escolher o namespace e as métricas do SO convidado

As máquinas virtuais e os conjuntos de dimensionamento de máquinas virtuais têm duas categorias de métricas: métricas de **host de máquina virtual** que são coletadas pelo ambiente de hospedagem do Azure e métricas de **SO convidado (clássico)** coletadas pelo [agente de monitoramento ](agents-overview.md)em execução em suas máquinas virtuais. Instale o agente de monitorização ao ativar a [Extensão de Diagnóstico do Azure](diagnostics-extension-overview.md).

Por predefinição, as métricas do SO convidado são armazenadas na conta de Armazenamento do Azure, que escolhe no separador **Definições de diagnóstico** do seu recurso. Se as métricas do SO convidado não forem recolhidas ou se o Explorador de Métricas não conseguir aceder às mesmas, apenas será apresentado o espaço de nomes de métricas do **Anfitrião de Máquinas Virtuais**:

![imagem da métrica](./media/metrics-troubleshoot/cannot-pick-guest-os-namespace.png)

**Solução:** Se você não vir o namespace e as métricas do **sistema operacional convidado (clássico)** no Metrics Explorer:

1. Confirme que a [Extensão de Diagnóstico do Azure](diagnostics-extension-overview.md) está ativada e configurada para recolher métricas.
    > [!WARNING]
    > Não pode utilizar o [agente do Log Analytics](agents-overview.md#log-analytics-agent) (também referido como o Agente de Monitorização da Microsoft ou "MMA") para enviar o **SO convidado** para uma conta de armazenamento.

1. Verifique se o provedor de recursos **Microsoft. insights** está [registrado para sua assinatura](metrics-troubleshoot.md#microsoftinsights-resource-provider-isnt-registered-for-your-subscription).

1. Verifique se a conta de armazenamento não está protegida pelo firewall. Portal do Azure precisa acessar a conta de armazenamento para recuperar dados de métricas e plotar os gráficos.

1. Utilize o [Explorador de armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/) para validar que as métricas estão a fluir para a conta de armazenamento. Se não forem recolhidas métricas, siga o [guia de resolução de problemas de Extensão de Diagnóstico do Azure](diagnostics-extension-troubleshooting.md#metric-data-doesnt-appear-in-the-azure-portal).

## <a name="next-steps"></a>Passos seguintes

* [Saiba mais sobre como começar a usar o Gerenciador de métricas](metrics-getting-started.md)
* [Saiba mais sobre os recursos avançados do Gerenciador de métricas](metrics-charts.md)
* [See a list of available metrics for Azure services](metrics-supported.md) (Ver uma lista de métricas disponíveis para serviços do Azure)
* [See examples of configured charts](metric-chart-samples.md) (Ver exemplos de gráficos configurados)
