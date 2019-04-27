---
title: Descrição geral de diagnóstico - serviço de aplicações do Azure | Documentos da Microsoft
description: Saiba como pode resolver problemas com a sua aplicação web com o diagnóstico do serviço de aplicações.
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
ms.openlocfilehash: a8b256f43d8e4103404ab4276431ceb06d9ed36a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60839440"
---
# <a name="azure-app-service-diagnostics-overview"></a>Descrição geral de diagnóstico de serviço de aplicações do Azure 

Quando estiver a executar uma aplicação web, é importante estar preparado para quaisquer problemas que possam surgir, de 500 erros aos seus utilizadores informando que o site está inativo. Diagnóstico do serviço de aplicações é uma experiência interativa e inteligente para o ajudar a resolver problemas da aplicação de web sem qualquer configuração necessária. Quando se deparar com problemas com a sua aplicação web, o diagnóstico do serviço de aplicações ressaltar que está errado para orientá-lo para as informações certas para a forma mais fácil e rapidamente resolver problemas e resolver o problema. 
 
Embora esta experiência é mais útil quando estiver a ter problemas com a sua aplicação web nas últimas 24 horas, todos os gráficos de diagnóstico vão estar disponíveis para analisar o tempo todo. Ferramentas de solução de problemas adicionais e ligações para documentação útil e fóruns estão localizadas na coluna à direita.

Diagnóstico do serviço de aplicações funciona para não apenas seu aplicativo no Windows, mas também aplicações no [contentores do Linux](https://docs.microsoft.com/azure/app-service/containers/app-service-linux-intro), [ambiente de serviço de aplicações](https://docs.microsoft.com/azure/app-service/environment/intro), e [as funções do Azure](https://docs.microsoft.com/azure/azure-functions/functions-overview). 

## <a name="open-app-service-diagnostics"></a>Abra diagnóstico do serviço de aplicações

Para aceder ao diagnóstico do serviço de aplicações, navegue até à sua aplicação de serviço de aplicações ou o ambiente de serviço de aplicações no [portal do Azure](https://portal.azure.com). No painel de navegação esquerdo, clique em **diagnosticar e resolver problemas**. 

Para as funções do Azure, navegue até à sua aplicação de função e no painel de navegação superior, clique em **funcionalidades de plataforma** e selecione **diagnosticar e resolver problemas** partir o **monitorização**secção. 

![Home page](./media/app-service-diagnostics/Homepage1.png)

## <a name="health-checkup"></a>Revisão do Estado de funcionamento

Se não sabe o que há de errado com a sua aplicação web ou não sabe onde começar a resolver problemas, a revisão do Estado de funcionamento é um bom lugar para começar. A revisão do Estado de funcionamento irá analisar seus aplicativos web para que tenha uma visão geral rápida e interativa que destaca o que há de bom estado de funcionamento e o que está errado, a informá-lo onde procurar para investigar o problema. Sua interface interativo e inteligente fornece orientação no processo de resolução de problemas.  

![Revisão do Estado de funcionamento](./media/app-service-diagnostics/HealthCheckup2.png)

Se for detetado um problema com uma categoria de problema específico nas últimas 24 horas, pode ver o relatório de diagnóstico completo e diagnóstico do serviço de aplicações poderá pedir-lhe para ver os conselhos de resolução de problemas mais e os passos seguintes para obter uma experiência mais interativa.

![Passos de resolução de problemas e seguintes](./media/app-service-diagnostics/Troubleshooting3.png)

## <a name="tile-shortcuts"></a>Atalhos de mosaico

Se sabe exatamente o tipo de solução de problemas de informações que procura, os atalhos do mosaico leva-o diretamente para o relatório de diagnóstico completo da categoria de problema que está interessado em. Em comparação com a revisão do Estado de funcionamento, os atalhos de mosaico são mais direto, mas menos orientada a forma de acessar suas métricas de diagnóstico. Como parte dos atalhos de mosaico, isso também é onde encontrará **ferramentas de diagnóstico** que são abordadas ferramentas mais avançado que irão ajudar a investigar problemas relacionados com problemas de código do aplicativo, lentidão, cadeias de ligação e muito mais. 

![Atalhos de mosaico](./media/app-service-diagnostics/TileShortcuts4.png)

## <a name="diagnostic-report"></a>Relatório de diagnóstico

Se desejar obter mais informações após a execução um [revisão do Estado de funcionamento](#health-checkup) ou clicar da [mosaico atalhos](#tile-shortcuts), o relatório de diagnóstico completo mostrará a as métricas relevantes de representar no gráfico das últimas 24 horas. Se a sua aplicação sofre qualquer período de inatividade, este é representado por uma cor de laranja barra por baixo da linha do tempo. Pode selecionar uma das barras de cor de laranja para selecionar o tempo de inatividade para ver as observações sobre esse tempo de inatividade e os passos de resolução de problemas sugeridos. 

![Relatório de diagnóstico](./media/app-service-diagnostics/DiagnosticReport5.png)


## <a name="investigating-application-code-issues"></a>Investigar os problemas de código de aplicativos

Porque muitos problemas de aplicação estão relacionadas com problemas no código da aplicação, diagnóstico do serviço de aplicações integra [Application Insights](https://azure.microsoft.com/services/application-insights/) para realçar exceções e problemas de dependência para correlacionar com o tempo de inatividade selecionado. O Application Insights tem de ser ativada em separado. 

Para ver as exceções do Application Insights e as dependências, selecione o **aplicação Web inativa** ou **lenta de aplicação Web** mosaico atalhos. 

![O Application insights](./media/app-service-diagnostics/AppInsights6.png)

