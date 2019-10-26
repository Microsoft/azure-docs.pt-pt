---
title: Criar perfil de aplicativos de serviço de Azure App ao vivo com Application Insights | Microsoft Docs
description: Perfil de aplicativos dinâmicos no serviço de Azure App com Application Insights Profiler.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: d463732fc8e8f488851a57fe520f138b101eb6cf
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72899947"
---
# <a name="profile-live-azure-app-service-apps-with-application-insights"></a>Criar perfil de aplicativos de serviço de Azure App ao vivo com Application Insights

Você pode executar o Profiler em aplicativos ASP.NET e ASP.NET Core que estão em execução no serviço Azure App usando a camada de serviço básica ou superior. Atualmente, habilitar o profiler no Linux só é possível por meio [desse método](profiler-aspnetcore-linux.md).

## <a id="installation"></a>Habilitar o Profiler para seu aplicativo
Para habilitar o Profiler para um aplicativo, siga as instruções abaixo. Se você estiver executando um tipo diferente de serviço do Azure, aqui estão as instruções para habilitar o Profiler em outras plataformas com suporte:
* [Serviços Cloud](../../azure-monitor/app/profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Service Fabric aplicativos](../../azure-monitor/app/profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [Máquinas Virtuais](../../azure-monitor/app/profiler-vm.md?toc=/azure/azure-monitor/toc.json)

Application Insights Profiler é pré-instalado como parte do tempo de execução dos serviços de aplicativo. As etapas a seguir mostrarão como habilitá-lo para o serviço de aplicativo. Siga estas etapas mesmo se você tiver incluído o SDK do App insights em seu aplicativo no momento da compilação.

1. Habilite a configuração "Always On" para o serviço de aplicativo. Você pode atualizar a configuração na página configuração do serviço de aplicativo em configurações gerais.
1. Vá para o painel **serviços de aplicativos** na portal do Azure.
1. Navegue até **configurações >** painel de Application insights.

   ![Habilitar o app insights no portal de serviços de aplicativos](./media/profiler/AppInsights-AppServices.png)

1. Siga as instruções no painel para criar um novo recurso ou selecione um recurso existente do App insights para monitorar seu aplicativo. Verifique também se o criador de perfil está **ativado**. Se o recurso de Application Insights estiver em uma assinatura diferente do serviço de aplicativo, você não poderá usar essa página para configurar Application Insights. Você ainda pode fazê-lo manualmente criando as configurações de aplicativo necessárias manualmente. [A próxima seção contém instruções para habilitar manualmente o profiler.](#enable-profiler-manually-or-with-azure-resource-manager) 

   ![Adicionar extensão do site do App insights][Enablement UI]

1. O criador de perfil agora está habilitado usando uma configuração de aplicativo dos serviços de aplicativo.

    ![Configuração do aplicativo para o criador de perfil][profiler-app-setting]

## <a name="enable-profiler-manually-or-with-azure-resource-manager"></a>Habilitar o profiler manualmente ou com Azure Resource Manager
Application Insights Profiler pode ser habilitado pela criação de configurações de aplicativo para seu serviço de Azure App. A página com as opções mostradas acima cria essas configurações de aplicativo para você. Mas você pode automatizar a criação dessas configurações usando um modelo ou outros meios. Essas configurações também funcionarão se o recurso de Application Insights estiver em uma assinatura diferente do serviço de Azure App.
Aqui estão as configurações necessárias para habilitar o criador de perfil:

|Definição da Aplicação    | Valor    |
|---------------|----------|
|APPINSIGHTS_INSTRUMENTATIONKEY         | iKey para o recurso de Application Insights    |
|APPINSIGHTS_PROFILERFEATURE_VERSION | 1.0.0 |
|DiagnosticServices_EXTENSION_VERSION | ~ 3 |


Você pode definir esses valores usando [modelos de Azure Resource Manager](../../azure-monitor/app/azure-web-apps.md#app-service-application-settings-with-azure-resource-manager), o [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.websites/set-azwebapp) [CLI do Azure](https://docs.microsoft.com/cli/azure/webapp/config/appsettings?view=azure-cli-latest).

### <a name="enabling-profiler-for-other-clouds-manually"></a>Habilitando o Profiler para outras nuvens manualmente

Se você quiser habilitar o criador de perfil para outras nuvens, poderá usar as configurações de aplicativo abaixo.

|Definição da Aplicação    | Valores do governo dos EUA| China Cloud |   
|---------------|---------------------|-------------|
|ApplicationInsightsProfilerEndpoint         | https://agent.serviceprofiler.azure.us    | https://profiler.applicationinsights.azure.cn |
|ApplicationInsightsEndpoint | https://dc.applicationinsights.us | https://dc.applicationinsights.azure.cn |

## <a name="disable-profiler"></a>Desabilitar criador de perfil

Para parar ou reiniciar o Profiler para uma instância do aplicativo individual, em **trabalhos da Web**, vá para o recurso de aplicativo. Para excluir o Profiler, vá para **extensões**.

![Desabilitar o Profiler para um trabalho Web][disable-profiler-webjob]

Recomendamos que você tenha o criador de perfil habilitado em todos os seus aplicativos para descobrir problemas de desempenho o mais cedo possível.

Os arquivos do criador de perfil podem ser excluídos ao usar o WebDeploy para implantar alterações em seu aplicativo Web. Você pode impedir a exclusão, excluindo a pasta App_Data de ser excluída durante a implantação. 


## <a name="next-steps"></a>Passos seguintes

* [Trabalhando com Application Insights no Visual Studio](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio)

[Enablement UI]: ./media/profiler/Enablement_UI.png
[profiler-app-setting]:./media/profiler/profiler-app-setting.png
[disable-profiler-webjob]: ./media/profiler/disable-profiler-webjob.png
