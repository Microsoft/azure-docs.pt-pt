---
title: Perfil de aplicações em direto do serviço de aplicações do Azure com o Application Insights | Documentos da Microsoft
description: Criar perfis para aplicações em direto no serviço de aplicações do Azure com o Application Insights Profiler.
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
ms.openlocfilehash: b8f6a2d12e1a9920421e6491432b516520ae110b
ms.sourcegitcommit: 6e32f493eb32f93f71d425497752e84763070fad
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/10/2019
ms.locfileid: "59469986"
---
# <a name="profile-live-azure-app-service-apps-with-application-insights"></a>Perfil aplicações de serviço de aplicações do Azure em direto com o Application Insights

Pode executar Profiler em ASP.NET e ASP.NET Core em execução no App Service do Azure com o escalão de serviço básico ou superior. Ativar Profiler no Linux atualmente só é possível por meio [esse método](profiler-aspnetcore-linux.md).

## <a id="installation"></a> Ative Profiler para a sua aplicação
Para ativar o Profiler para uma aplicação, siga as instruções abaixo. Se estiver executando um tipo diferente de serviço do Azure, aqui estão as instruções de ativação do Profiler em outras plataformas suportadas:
* [Serviços Cloud](../../azure-monitor/app/profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Aplicações do Service Fabric](../../azure-monitor/app/profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [Virtual Machines](../../azure-monitor/app/profiler-vm.md?toc=/azure/azure-monitor/toc.json)

Application Insights Profiler previamente é instalado como parte do tempo de execução dos serviços de aplicações. Os passos abaixo mostram como ativá-la para o serviço de aplicações. Siga estes passos, mesmo se incluí o SDK do App Insights na sua aplicação no momento da compilação.

1. Ative a definição "Always On" para o serviço de aplicações. Pode atualizar a definição na página de configuração do seu serviço de aplicações, definições gerais.
1. Vá para o **dos serviços de aplicações** painel no portal do Azure.
1. Navegue para **definições > Application Insights** painel.

   ![Ativar o App Insights no portal dos serviços de aplicações](./media/profiler/AppInsights-AppServices.png)

1. Ou siga as instruções no painel para criar um novo recurso ou selecione um recurso do App Insights existente para monitorizar a aplicação. Além disso, certifique-se de que o Profiler está **no**. Se o recurso do Application Insights está numa subscrição diferente do seu serviço de aplicações, não é possível utilizar esta página para configurar o Application Insights. Ainda pode fazer isso manualmente embora ao criar as definições de aplicação necessárias manualmente. [A secção seguinte contém instruções para ativar manualmente o Profiler.](#enable-profiler-manually-or-with-azure-resource-manager) 

   ![Adicionar extensão de site do App Insights][Enablement UI]

1. Profiler está agora ativada com uma definição de aplicação de serviços de aplicação.

    ![Definição de aplicação para Profiler][profiler-app-setting]

## <a name="enable-profiler-manually-or-with-azure-resource-manager"></a>Ative Profiler manualmente ou com o Azure Resource Manager
Application Insights Profiler pode ser ativado através da criação de definições de aplicações para o serviço de aplicações do Azure. A página com as opções mostradas acima cria estas definições de aplicação para si. Mas pode automatizar a criação dessas configurações usando um modelo ou de outros meios. Estas definições também funcionará se o recurso do Application Insights está numa subscrição diferente do seu serviço de aplicações do Azure.
Seguem-se as definições necessárias para permitir que o criador de perfil:

|Definição da Aplicação    | Valor    |
|---------------|----------|
|APPINSIGHTS_INSTRUMENTATIONKEY         | iKey do seu recurso do Application Insights    |
|APPINSIGHTS_PROFILERFEATURE_VERSION | 1.0.0 |
|DiagnosticServices_EXTENSION_VERSION | ~3 |


Pode definir esses valores usando [modelos do Azure Resource Manager](../../azure-monitor/app/azure-web-apps.md#app-service-application-settings-with-azure-resource-manager), [Azure Powershell](https://docs.microsoft.com/powershell/module/az.websites/set-azwebapp), [da CLI do Azure](https://docs.microsoft.com/cli/azure/webapp/config/appsettings?view=azure-cli-latest).


## <a name="disable-profiler"></a>Desativar Profiler

Para parar ou reiniciar Profiler para a instância de uma aplicação individual, em **Webjobs**, vá para o recurso de aplicação. Para eliminar o Profiler, aceda a **extensões**.

![Desativar Profiler para um trabalho web][disable-profiler-webjob]

Recomendamos que tenha Profiler ativada em todas as suas aplicações para detetar problemas de desempenho mais cedo possível.

Ficheiros do Profiler podem ser eliminados ao utilizar o WebDeploy para implementar as alterações ao seu aplicativo web. Pode impedir a eliminação excluindo pasta App_Data seja eliminado durante a implementação. 


## <a name="next-steps"></a>Passos Seguintes

* [Trabalhar com o Application Insights no Visual Studio](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio)

[Enablement UI]: ./media/profiler/Enablement_UI.png
[profiler-app-setting]:./media/profiler/profiler-app-setting.png
[disable-profiler-webjob]: ./media/profiler/disable-profiler-webjob.png
