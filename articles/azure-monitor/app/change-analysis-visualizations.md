---
title: Visualizações para Análise de Alteração de Aplicações - Monitor Azure
description: Saiba como utilizar visualizações na Análise de Mudança de Aplicação no Monitor Azure.
ms.topic: conceptual
author: cawams
ms.author: cawa
ms.date: 02/11/2021
ms.openlocfilehash: 643645eb3b361cd68def1177ba7e8af89f1963bc
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100521188"
---
# <a name="visualizations-for-application-change-analysis-preview"></a>Visualizações para Análise de Alteração de Aplicações (pré-visualização)

## <a name="standalone-ui"></a>UI autónomo

No Azure Monitor, existe um painel autónomo para a Análise de Alterações para visualizar todas as alterações com insights sobre dependências de aplicações e recursos.

Procure a Análise de Alterações na barra de pesquisa no portal Azure para lançar a experiência.

![Screenshot de pesquisa de Análise de Mudança no portal Azure](./media/change-analysis/search-change-analysis.png)

Todos os recursos sob uma subscrição selecionada são apresentados com alterações das últimas 24 horas. Para otimizar o desempenho da carga da página, o serviço apresenta 10 recursos de cada vez. Selecione a página seguinte para ver mais recursos. Estamos a trabalhar para eliminar esta limitação.

![Screenshot da lâmina de análise de mudança no portal Azure](./media/change-analysis/change-analysis-standalone-blade.png)

Clicando num recurso para ver todas as suas alterações. Se necessário, faça uma alteração para ver detalhes e insights de alteração formatados json.

![Screenshot de detalhes de mudança](./media/change-analysis/change-details.png)

Para qualquer feedback, utilize o botão de feedback do envio ou e-mail changeanalysisteam@microsoft.com .

![Screenshot do botão de feedback no separador Análise de Alterações](./media/change-analysis/change-analysis-feedback.png)

### <a name="multiple-subscription-support"></a>Suporte de subscrição múltipla

A UI suporta a seleção de várias subscrições para visualizar alterações de recursos. Utilize o filtro de subscrição:

![Screenshot do filtro de subscrição que suporta a seleção de várias subscrições](./media/change-analysis/multiple-subscriptions-support.png)

### <a name="web-app-diagnose-and-solve-problems"></a>Diagnóstico e resolução de problemas de aplicações web

No Azure Monitor, a Change Analysis também é integrada no autosserviço Diagnosticar e resolver a experiência de **problemas.** Aceda a esta experiência a partir da página **geral** da sua aplicação Serviço de Aplicações.

![Screenshot do botão "Visão Geral" e do botão "Diagnosticar e resolver problemas"](./media/change-analysis/change-analysis.png)

## <a name="application-change-analysis-in-the-diagnose-and-solve-problems-tool"></a>Análise de alteração de aplicação na ferramenta diagnosticar e resolver problemas

Application Change Analysis é um detetor autónomo na Web App diagnosticar e resolver ferramentas de problemas. Também é agregado em **detetores de aplicações e** web **app down.** Ao introduzir a ferramenta Diagnose and Solve Problems, o fornecedor de recursos **Microsoft.ChangeAnalysis** será automaticamente registado. Siga estas instruções para permitir o rastreio de alterações na web no hóspede.

1. Selecione **Disponibilidade e Performance**.

    ![Screenshot das opções de resolução de problemas "Disponibilidade e desempenho"](./media/change-analysis/availability-and-performance.png)

2. Selecione **Alterações de Aplicação**. A funcionalidade também está disponível em **Application Crashes.**

   ![Screenshot do botão "Application Crashes"](./media/change-analysis/application-changes.png)

3. O link leva à Aplicação Change Analysis UI telescópio para a aplicação web. Se o rastreio de alterações de aplicativos na Web no hóspede não estiver ativado, siga o banner para obter alterações nas definições de ficheiros e aplicações.

   ![Screenshot das opções "Application Crashes"](./media/change-analysis/enable-changeanalysis.png)

4. Ligue a **Análise de Alterações** e selecione **Guardar**. A ferramenta exibe todas as aplicações web ao abrigo de um plano de Serviço de Aplicações. Pode utilizar o interruptor de nível de plano para ativar a Change Analysis para todas as aplicações web ao abrigo de um plano.

    ![Screenshot da interface de utilizador "Enable Change Analysis"](./media/change-analysis/change-analysis-on.png)

5. Os dados de alteração também estão disponíveis em detetores de **aplicações web para baixo** e **de falhas de aplicação** selecionados. Você verá um gráfico que resume o tipo de mudanças ao longo do tempo, juntamente com detalhes sobre essas alterações. Por padrão, as alterações nas últimas 24 horas são apresentadas para ajudar com problemas imediatos.

     ![Screenshot da visão de mudança diff](./media/change-analysis/change-view.png)

## <a name="virtual-machine-diagnose-and-solve-problems"></a>Diagnóstico e resolução de máquinas virtuais

Vá para a ferramenta Diagnosticar e Resolver Problemas para uma Máquina Virtual.  Vá a **Ferramentas de Resolução de Problemas,** navegue pela página e selecione **Analise as alterações recentes** para ver as alterações na Máquina Virtual.

![Screenshot do VM Diagnosticar e Resolver Problemas](./media/change-analysis/vm-dnsp-troubleshootingtools.png)

![Alterar analisador em ferramentas de resolução de problemas](./media/change-analysis/analyze-recent-changes.png)

## <a name="activity-log-change-history"></a>Histórico de alteração de registo de atividade

A funcionalidade [de histórico de alteração de visualização](../platform/activity-log.md#view-change-history) no Registo de Atividade chama o backend do serviço de análise de alteração de aplicação para obter alterações associadas a uma operação. **Alterar o histórico** usado para chamar [o Azure Resource Graph](../../governance/resource-graph/overview.md) diretamente, mas trocou o backend para ligar para Application Change Analysis para que as alterações devolvidas incluam alterações no nível de recursos a partir do [Azure Resource Graph,](../../governance/resource-graph/overview.md)propriedades de recursos do [Azure Resource Manager](../../azure-resource-manager/management/overview.md), e alterações in-guest dos serviços PaaS, como app Web App Services. Para que o serviço de Análise de Alterações de Aplicação possa pesquisar alterações nas subscrições dos utilizadores, é necessário registar um fornecedor de recursos. A primeira vez que introduzir o separador **Change History,** a ferramenta começará automaticamente a registar o fornecedor de recursos **Microsoft.ChangeAnalysis.** Depois de registadas, as alterações a partir do **Azure Resource Graph** estarão disponíveis imediatamente e cobrirão os últimos 14 dias. As alterações de outras fontes estarão disponíveis após ~4 horas após a subscrição estar a bordo.

![Atividade Log alterar integração de histórico](./media/change-analysis/activity-log-change-history.png)

## <a name="vm-insights-integration"></a>Integração de VM Insights

Os utilizadores com [O Visor VM](../insights/vminsights-overview.md) habilitado podem ver o que mudou nas suas máquinas virtuais que pode ter causado picos num gráfico de métricas como CPU ou Memória. Os dados de alteração estão integrados na barra de navegação lateral VM Insights. O utilizador pode ver se alguma alteração ocorreu no VM e selecione **Investigar Alterações** para ver detalhes de alteração na UI de alteração de aplicação.

[![Integração de insights VM](./media/change-analysis/vm-insights.png)](./media/change-analysis/vm-insights.png#lightbox)

## <a name="next-steps"></a>Passos seguintes

- Saiba como [resolver problemas na Análise de Mudanças](change-analysis-troubleshoot.md)
