---
title: Descrição geral de diagnóstico de serviço de aplicações do Azure | Documentos da Microsoft
description: Saiba como pode resolver problemas com a sua aplicação com o diagnóstico do serviço de aplicações.
keywords: serviço de aplicações, serviço de aplicações do azure, diagnóstico, suporte, aplicação web, resolução de problemas, ajuda autónoma
services: app-service
documentationcenter: ''
author: jen7714
manager: cfowler
editor: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/10/2017
ms.author: jennile
ms.custom: seodec18
ms.openlocfilehash: f2f798be85e9c3aeb8d4b54cba89d8be059427e0
ms.sourcegitcommit: 72f1d1210980d2f75e490f879521bc73d76a17e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/14/2019
ms.locfileid: "67147356"
---
# <a name="azure-app-service-diagnostics-overview"></a>Descrição geral de diagnóstico de serviço de aplicações do Azure

Quando estiver a executar uma aplicação web, é importante estar preparado para quaisquer problemas que possam surgir, de 500 erros aos seus utilizadores informando que o site está inativo. Diagnóstico do serviço de aplicações é uma experiência interativa e inteligente para o ajudar a resolver problemas da aplicação sem qualquer configuração necessária. Quando se deparar com problemas com a sua aplicação, o diagnóstico do serviço de aplicações destaca o que há de errado para orientá-lo para as informações certas para a forma mais fácil e rapidamente resolver problemas e resolver o problema.

Embora esta experiência é mais útil quando estiver a ter problemas com a sua aplicação nas últimas 24 horas, todos os gráficos de diagnóstico estão sempre disponíveis analisar.

Diagnóstico do serviço de aplicações funciona para não apenas seu aplicativo no Windows, mas também aplicações no [contentores do Linux](https://docs.microsoft.com/azure/app-service/containers/app-service-linux-intro), [ambiente de serviço de aplicações](https://docs.microsoft.com/azure/app-service/environment/intro), e [as funções do Azure](https://docs.microsoft.com/azure/azure-functions/functions-overview).

## <a name="open-app-service-diagnostics"></a>Abra diagnóstico do serviço de aplicações

Para aceder ao diagnóstico do serviço de aplicações, navegue até à sua aplicação do serviço de aplicações web ou o ambiente de serviço de aplicações no [portal do Azure](https://portal.azure.com). No painel de navegação esquerdo, clique em **diagnosticar e resolver problemas**.

Para as funções do Azure, navegue até à sua aplicação de função e no painel de navegação superior, clique em **funcionalidades de plataforma**e selecione **diagnosticar e resolver problemas** partir o **degestãoderecursos** secção.

Na home page diagnóstico do serviço de aplicações, pode escolher a categoria que melhor descreve o problema com a sua aplicação ao utilizar as palavras-chave em cada mosaico de home page. Além disso, esta página é onde encontrará **ferramentas de diagnóstico** para aplicações do Windows. Ver [ferramentas de diagnóstico (apenas para a aplicação do Windows)](#diagnostic-tools-only-for-windows-app).

![Homepage](./media/app-service-diagnostics/app-service-diagnostics-homepage-1.png)

## <a name="interactive-interface"></a>Interface interativa

Depois de selecionar uma categoria de home page que melhor se alinha com problema da sua aplicação, à interface interativa dos diagnósticos do serviço de aplicações, Genie, pode ajudá-lo por meio de diagnosticar e resolver o problema com a sua aplicação. Pode usar os atalhos de mosaico fornecidos pelo Genie para ver o relatório de diagnóstico completo da categoria de problema que está interessado. Os atalhos de mosaico fornecem uma forma direta de acessar suas métricas de diagnóstico.

![Atalhos de mosaico](./media/app-service-diagnostics/tile-shortcuts-2.png)

Depois de clicar destes mosaicos, pode ver uma lista de tópicos relacionados com o problema descrito no mosaico. Estes tópicos fornecem fragmentos de informações importantes de todo o relatório. Pode clicar em qualquer um dos seguintes tópicos para investigar os problemas ainda mais. Além disso, pode clicar em **ver o relatório completo** para explorar todos os tópicos numa única página.

![Tópicos](./media/app-service-diagnostics/application-logs-insights-3.png)

![Ver o relatório completo](./media/app-service-diagnostics/view-full-report-4.png)

## <a name="diagnostic-report"></a>Relatório de diagnóstico

Depois de escolher a investigar o problema ainda mais ao clicar num tópico, pode ver mais detalhes sobre o tópico, muitas vezes, é complementado com gráficos e markdowns. Relatório de diagnóstico pode ser uma ferramenta poderosa para indicar o problema com a sua aplicação.

![Relatório de diagnóstico](./media/app-service-diagnostics/full-diagnostic-report-5.png)

## <a name="health-checkup"></a>Revisão do Estado de funcionamento

Se não sabe o que há de errado com a sua aplicação ou não sabe onde começar a resolver problemas, a revisão do Estado de funcionamento é um bom lugar para começar. A revisão do Estado de funcionamento analisa as suas aplicações para lhe dar uma visão geral rápida e interativa que destaca o que há de bom estado de funcionamento e o que está errado, a informá-lo onde procurar para investigar o problema. Sua interface interativo e inteligente fornece orientação no processo de resolução de problemas. Revisão do Estado de funcionamento está integrado com a experiência de Genie para aplicações do Windows e a aplicação web para baixo diagnóstico relatório para aplicações Linux.

### <a name="health-checkup-graphs"></a>Gráficos de revisão do Estado de funcionamento

Existem quatro gráficos diferentes a revisão do Estado de funcionamento.

- **pedidos e erros:** Um gráfico que mostra o número de pedidos efetuados nas últimas 24 horas, juntamente com erros de servidor HTTP.
- **desempenho da aplicação:** Um gráfico que mostra o tempo de resposta nas últimas 24 horas para vários grupos de percentil.
- **Utilização da CPU:** Um gráfico que mostra a utilização da CPU percentagem geral por instância nas últimas 24 horas.  
- **utilização de memória:** Um gráfico que mostra a utilização de memória física percentagem geral por instância nas últimas 24 horas.

![Revisão do Estado de funcionamento](./media/app-service-diagnostics/health-checkup-6.png)

### <a name="investigate-application-code-issues-only-for-windows-app"></a>Investigar problemas de código de aplicação (apenas para a aplicação do Windows)

Porque muitos problemas de aplicação estão relacionadas com problemas no código da aplicação, diagnóstico do serviço de aplicações integra [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) para realçar exceções e problemas de dependência para correlacionar com o tempo de inatividade selecionado. O Application Insights tem de ser ativada em separado.

![Application Insights](./media/app-service-diagnostics/application-insights-7.png)

Para ver as exceções do Application Insights e as dependências, selecione o **aplicação web para baixo** ou **aplicação web lenta** mosaico atalhos.

### <a name="troubleshooting-steps-only-for-windows-app"></a>Passos de resolução de problemas (apenas para a aplicação do Windows)

Se for detetado um problema com uma categoria de problema específico nas últimas 24 horas, pode ver o relatório de diagnóstico completo e diagnóstico do serviço de aplicações poderá pedir-lhe para ver os conselhos de resolução de problemas mais e os passos seguintes para obter uma experiência mais interativa.

![O Application Insights e passos de resolução de problemas e seguintes](./media/app-service-diagnostics/troubleshooting-and-next-steps-8.png)

## <a name="diagnostic-tools-only-for-windows-app"></a>Ferramentas de diagnóstico (apenas para a aplicação do Windows)

Ferramentas de diagnóstico incluem ferramentas mais avançadas de diagnóstico que o ajuda, investigue aplicações de código problemas, lentidão, cadeias de ligação e muito mais. e ferramentas proativa que o ajudam a reduzir os problemas com a utilização da CPU, pedidos e memória.

### <a name="proactive-cpu-monitoring"></a>A monitorização proativa de CPU

A monitorização proativa de CPU fornece uma forma simples e proativa a tomar uma ação quando de seu processo de aplicação ou filho para a sua aplicação está a consumir recursos de CPU elevados. Pode definir suas próprias regras de limiar de CPU para reduzir temporariamente um problema de CPU elevado até encontra a causa real para o problema inesperado.

![A monitorização proativa de CPU](./media/app-service-diagnostics/proactive-cpu-monitoring-9.png)

### <a name="proactive-auto-healing"></a>Proativa autorrecuperável

Como o monitoramento proativo de CPU, proativa autorrecuperação oferece uma abordagem proativa e fácil à redução de um comportamento inesperado da sua aplicação. Pode definir suas próprias regras com base na contagem de pedidos, o pedido lento, o limite de memória e o código de estado HTTP para realizar ações de atenuação. Essa ferramenta pode ser usada para atenuar temporariamente um comportamento inesperado até encontra a causa real para o problema. Para obter mais informações sobre proativa autorrecuperação, visite [anunciar a novo automática recuperação experiência no diagnóstico do serviço de aplicações](https://azure.github.io/AppService/2018/09/10/Announcing-the-New-Auto-Healing-Experience-in-App-Service-Diagnostics.html).

![Proativa autorrecuperável](./media/app-service-diagnostics/proactive-auto-healing-10.png)

## <a name="change-analysis-only-for-windows-app"></a>Análise de alterações (apenas para a aplicação do Windows)

Por vezes, num ambiente de desenvolvimento rápido, pode ser difícil manter o controle de todas as alterações feitas à sua aplicação e permitir que o pinpoint somente numa alteração que causou o comportamento de mau estado de funcionamento. Análise de alterações pode ajudar a restringir sobre as alterações feitas à sua aplicação para facilitar a experiência trouble-shooting. Análise de alterações se encontra no **as alterações da aplicação** e também incorporado como um relatório de diagnóstico **falha aplicação** para que possa utilizá-lo simultaneamente com outras métricas.

Análise de alterações tem de ser ativado antes de utilizar a funcionalidade. Para obter mais informações sobre a análise de alteração, visite [Apresentamos a nova experiência de análise de alterações no diagnóstico do serviço de aplicações](https://azure.github.io/AppService/2019/05/07/Announcing-the-new-change-analysis-experience-in-App-Service-Diagnostics-Analysis.html).

![Página de padrão de análise de alteração](./media/app-service-diagnostics/change-analysis-default-page-11.png)

![Vista de diff](./media/app-service-diagnostics/diff-view-12.png)