---
title: Profile live Aplicativo sapp apps com Insights de Aplicação [ Microsoft Docs
description: Profile live apps no Azure App Service com Perfilde Insights de Aplicação.
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: ba9a2aca73dbdb8de298b68670fd6ab16f810a4d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275779"
---
# <a name="profile-live-azure-app-service-apps-with-application-insights"></a>Aplicativos de serviço de aplicações Azure ao vivo com Insights de Aplicação

Pode executar o Profiler em ASP.NET e ASP.NET aplicações Core que estão a funcionar no Azure App Service utilizando o nível de serviço Básico ou superior. Ativar o Profiler no Linux só é atualmente possível através [deste método](profiler-aspnetcore-linux.md).

## <a name="enable-profiler-for-your-app"></a><a id="installation"></a>Ativar o Profiler para a sua aplicação
Para ativar o Profiler para uma aplicação, siga as instruções abaixo. Se estiver a executar um tipo diferente de serviço Azure, aqui estão as instruções para ativar o Profiler noutras plataformas suportadas:
* [Serviços em Nuvem](../../azure-monitor/app/profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Aplicações de tecido de serviço](../../azure-monitor/app/profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [Máquinas Virtuais](../../azure-monitor/app/profiler-vm.md?toc=/azure/azure-monitor/toc.json)

O Perfil insights de aplicação é pré-instalado como parte do tempo de execução dos Serviços de Aplicações. Os passos abaixo mostrar-lhe-ão como o ativar para o seu Serviço de Aplicações. Siga estes passos mesmo que tenha incluído o App Insights SDK na sua aplicação no momento da construção.

1. Ative a definição "Always On" para o seu serviço de aplicações. Pode atualizar a definição na página de Configuração do seu Serviço de Aplicações em Definições Gerais.
1. Vá ao painel de Serviços de **Aplicações** no portal Azure.
1. Navegue para Definições > painel **insights de aplicação.**

   ![Ativar insights de aplicações no portal de serviços de aplicações](./media/profiler/AppInsights-AppServices.png)

1. Siga as instruções no painel para criar um novo recurso ou selecione um recurso app Insights existente para monitorizar a sua aplicação. Certifique-se também de que o Perfil está **ligado**. Se o seu recurso Application Insights estiver numa subscrição diferente do seu Serviço de Aplicações, não pode utilizar esta página para configurar insights de aplicação. Ainda pode fazê-lo manualmente, criando manualmente as definições necessárias da aplicação. [A secção seguinte contém instruções para ativar manualmente o Profiler.](#enable-profiler-manually-or-with-azure-resource-manager) 

   ![Adicionar extensão do site App Insights][Enablement UI]

1. O Profiler está agora ativado através de uma Definição de App Services.

    ![Definição de aplicativos para profiler][profiler-app-setting]

## <a name="enable-profiler-manually-or-with-azure-resource-manager"></a>Ativar o Profiler manualmente ou com o Gestor de Recursos Azure
O Profiler de Insights de Aplicação pode ser ativado através da criação de definições de aplicações para o seu Serviço de Aplicações Azure. A página com as opções acima mostradas cria estas definições de aplicação para si. Mas pode automatizar a criação destas configurações usando um modelo ou outros meios. Estas definições também funcionarão se o seu recurso Application Insights estiver numa subscrição diferente do seu Serviço de Aplicações Azure.
Aqui estão as definições necessárias para ativar o perfil:

|Definição da Aplicação    | Valor    |
|---------------|----------|
|APPINSIGHTS_INSTRUMENTATIONKEY         | iKey para o seu recurso Insights de Aplicação    |
|APPINSIGHTS_PROFILERFEATURE_VERSION | 1.0.0 |
|DiagnosticServices_EXTENSION_VERSION | ~3 |


Pode definir estes valores utilizando modelos de gestor de [recursos Azure,](../../azure-monitor/app/azure-web-apps.md#app-service-application-settings-with-azure-resource-manager) [Azure Powershell,](https://docs.microsoft.com/powershell/module/az.websites/set-azwebapp) [Azure CLI](https://docs.microsoft.com/cli/azure/webapp/config/appsettings?view=azure-cli-latest).

### <a name="enabling-profiler-for-other-clouds-manually"></a>Habilitar o Profiler para outras nuvens manualmente

Se quiser ativar o perfil para outras nuvens, pode utilizar as definições da aplicação abaixo.

|Definição da Aplicação    | Valores do Governo dos EUA| Nuvem da China |   
|---------------|---------------------|-------------|
|ApplicationInsightsProfilerEndpoint         | https://agent.serviceprofiler.azure.us    | https://profiler.applicationinsights.azure.cn |
|ApplicationInsightsEndpoint | https://dc.applicationinsights.us | https://dc.applicationinsights.azure.cn |

## <a name="disable-profiler"></a>Perfilde desativação

Para parar ou reiniciar o Profiler para uma instância de uma aplicação individual, no âmbito do **Web Jobs,** vá ao recurso da aplicação. Para eliminar o Profiler, vá a **Extensões**.

![Desativar o Profiler para um trabalho na web][disable-profiler-webjob]

Recomendamos que tenha o Profiler habilitado em todas as suas aplicações para descobrir quaisquer problemas de desempenho o mais cedo possível.

Os ficheiros do Profiler podem ser eliminados quando utilizar o WebDeploy para implementar alterações na sua aplicação web. Pode evitar que a eliminação, excluindo a App_Data pasta seja eliminada durante a implementação. 


## <a name="next-steps"></a>Passos seguintes

* [Trabalhar com o Application Insights no Visual Studio](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio)

[Enablement UI]: ./media/profiler/Enablement_UI.png
[profiler-app-setting]:./media/profiler/profiler-app-setting.png
[disable-profiler-webjob]: ./media/profiler/disable-profiler-webjob.png
