---
title: Utilizar o painel de definições do Azure Application Insights Profiler | Documentos da Microsoft
description: Ver estado do Profiler e iniciar sessões de criação de perfis
services: application-insights
documentationcenter: ''
author: cweining
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: mbullwin
ms.date: 08/06/2018
ms.author: cweining
ms.openlocfilehash: 8c9fba14bd3f7d3b55a245f8e647f0eae1f8ef83
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58118430"
---
# <a name="configure-application-insights-profiler"></a>Configurar Application Insights Profiler

## <a name="profiler-settings-pane"></a>Painel de definições do Profiler

Para abrir o painel de definições do Azure Application Insights Profiler, vá para o painel de desempenho do Application Insights e, em seguida, selecione o **Profiler** botão.

![Configurar o painel do Profiler][configure-profiler-entry]

O **configurar o Application Insights Profiler** painel contém quatro recursos: 
* **Perfil agora**: Inicia a criação de perfis de sessões para todas as aplicações que estejam ligadas a esta instância do Application Insights.
* **Ligado aplicações**: Apresenta uma lista de aplicações que a criação de perfis de enviar dados para este recurso do Application Insights.
* **Sessões em curso**: Apresenta o estado da sessão quando seleciona **perfil agora**. 
* **Recentes de sessões de criação de perfis**: Mostra informações sobre sessões de criação de perfis nos últimos.

![Profiler sob demanda][profiler-on-demand]

## <a name="app-service-environment"></a>Ambiente do Serviço de Aplicações
Dependendo da configuração do ambiente de serviço de aplicações do Azure, a chamada para verificar o estado do agente poderá ser bloqueada. O painel pode apresentar uma mensagem que o agente não está em execução mesmo quando está em execução. Para certificar-se de que é, verifique o webjob em seu aplicativo. Se todos os valores de definições de aplicação estão corretos e a extensão de site do Application Insights está instalada no seu aplicativo, Profiler está em execução. Se a sua aplicação está a receber suficiente tráfego, recentes sessões de criação de perfis devem ser exibidos numa lista.

## <a id="profileondemand"></a> Acionar manualmente o Profiler

Pode acionar manualmente Profiler com um único clique. Suponha que está a executar um teste de desempenho da web. Precisará rastreios para ajudar a compreender sobre a execução da sua aplicação web sob carga. Ter controlo sobre o quando os rastreios são capturados é crucial, porque sabe quando o teste de carga será executado. Mas o intervalo de amostragem aleatória pode perdê-lo.

As secções seguintes mostram como funciona este cenário:

### <a name="step-1-optional-generate-traffic-to-your-web-app-by-starting-a-web-performance-test"></a>Passo 1: (Opcional) Gerar tráfego para a sua aplicação web ao iniciar um teste de desempenho da web

Se a sua aplicação web já tiver o tráfego de entrada ou se quiser gerar tráfego manualmente, ignore esta secção e continue no passo 2.

1. No portal do Application Insights, selecione **configurar** > **testes de desempenho**. 

1. Para iniciar um novo teste de desempenho, selecione o **New** botão.

   ![Criar novo teste de desempenho][create-performance-test]

1. Na **novo teste de desempenho** painel, configurar o URL de destino do teste. Aceite todas as configurações padrão e, em seguida, selecione **teste de execução** para começar a executar o teste de carga.

    ![Configurar teste de carga][configure-performance-test]

    Novo teste é colocada na fila em primeiro lugar, seguido de estado *em curso*.

    ![teste de carga é submetido e colocados em fila][load-test-queued]

    ![teste de carga está em execução em curso][load-test-in-progress]

### <a name="step-2-start-a-profiler-on-demand-session"></a>Passo 2: Iniciar uma sessão de sob demanda do Profiler

1. Quando o teste de carga está em execução, comece Profiler para capturar rastreios na aplicação web, enquanto ele está recebendo a carga.

1. Vá para o **Profiler configurar** painel.


### <a name="step-3-view-traces"></a>Passo 3: Ver rastreios

Após a conclusão do Profiler em execução, siga as instruções na notificação para aceder à rastreios de painel e vista de desempenho.

## <a name="troubleshoot-the-profiler-on-demand-session"></a>Resolver problemas relacionados com a sessão de sob demanda do Profiler

Depois de uma sessão sob demanda, pode receber uma mensagem de erro de tempo limite do Profiler:

![Erro de tempo limite do Profiler][profiler-timeout]

Pode receber este erro para qualquer um dos seguintes motivos:

* A sessão de Profiler sob demanda foi concluída com êxito, mas demorou mais tempo que o esperado para processar os dados recolhidos do Application Insights.  

  Se os dados não processados em 15 minutos, o portal apresenta uma mensagem de tempo limite. Depois de um tempo, no entanto, rastreios do Profiler serão exibido. Se receber uma mensagem de erro, ignorá-la por agora. Estamos a trabalhar ativamente numa solução.

* A aplicação web tem uma versão mais antiga do agente do Profiler que não tem a funcionalidade sob demanda.  

  Se ativou o Application Insights Profiler anteriormente, precisará de atualizar o agente do Profiler para começar a utilizar a funcionalidade sob demanda.
  
Vá para os serviços de aplicação **as definições da aplicação** painel e verificar as seguintes definições:
* **APPINSIGHTS_INSTRUMENTATIONKEY**: Substitua a chave de instrumentação adequada para o Application Insights.
* **APPINSIGHTS_PORTALINFO**: ASP.NET
* **APPINSIGHTS_PROFILERFEATURE_VERSION**: 1.0.0

Se qualquer um dos valores anteriores não se encontram definidos, instale a extensão de site mais recente, fazendo o seguinte:

1. Vá para o **Application Insights** painel no portal dos serviços de aplicações.

    ![Ativar o Application Insights a partir do portal dos serviços de aplicações][enable-app-insights]

1. Se o **Application Insights** painel apresenta um **atualizar** botão, selecione-o para atualizar a extensão de site do Application Insights que vai instalar o agente mais recente do Profiler.

    ![Atualizar a extensão de site][update-site-extension]

1. Para garantir que o Profiler está ativado, selecione **alteração**e, em seguida, selecione **OK** para guardar as alterações.

    ![Alterar e salvar o app insights][change-and-save-appinsights]

1. Volte ao **as definições da aplicação** painel para o serviço de aplicações para se certificar de que os seguintes valores estão definidos:
   * **APPINSIGHTS_INSTRUMENTATIONKEY**: Substitua a chave de instrumentação adequada para o application insights.
   * **APPINSIGHTS_PORTALINFO**: ASP.NET 
   * **APPINSIGHTS_PROFILERFEATURE_VERSION**: 1.0.0

     ![Definições de aplicações para Profiler][app-settings-for-profiler]

1. Opcionalmente, selecione **extensões**e, em seguida, verifique a versão de extensão e determinar se uma atualização está disponível.

    ![verificação de atualização da extensão][check-for-extension-update]

## <a name="next-steps"></a>Passos Seguintes
[Ative Profiler e ver rastreios](profiler-overview.md?toc=/azure/azure-monitor/toc.json)

[profiler-on-demand]: ./media/profiler-settings/Profiler-on-demand.png
[configure-profiler-entry]: ./media/profiler-settings/configure-profiler-entry.png
[create-performance-test]: ./media/profiler-settings/new-performance-test.png
[configure-performance-test]: ./media/profiler-settings/configure-performance-test.png
[load-test-queued]: ./media/profiler-settings/load-test-queued.png
[load-test-in-progress]: ./media/profiler-settings/load-test-inprogress.png
[enable-app-insights]: ./media/profiler-settings/enable-app-insights-blade-01.png
[update-site-extension]: ./media/profiler-settings/update-site-extension-01.png
[change-and-save-appinsights]: ./media/profiler-settings/change-and-save-appinsights-01.png
[app-settings-for-profiler]: ./media/profiler-settings/appsettings-for-profiler-01.png
[check-for-extension-update]: ./media/profiler-settings/check-extension-update-01.png
[profiler-timeout]: ./media/profiler-settings/profiler-timeout.png
