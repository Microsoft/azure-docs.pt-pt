---
title: Perfil ao vivo Aplicativos Azure App Service com Application Insights | Microsoft Docs
description: Perfil aplicativo ao vivo no Azure App Service com Perfil de Insights de Aplicação.
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: a53db9deb07863010c792943c71eb0af5d845af8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105026510"
---
# <a name="profile-live-azure-app-service-apps-with-application-insights"></a>Perfil ao vivo Aplicativos Azure App Service com Insights de Aplicações

Pode executar profiler em ASP.NET e ASP.NET aplicações Core que estão a ser executadas no Azure App Service usando o nível de serviço Básico ou superior. Atualmente, a ativação do Profiler no Linux só é possível através [deste método.](profiler-aspnetcore-linux.md)

## <a name="enable-profiler-for-your-app"></a><a id="installation"></a> Ativar o Profiler para a sua aplicação
Para ativar o Profiler para uma aplicação, siga as instruções abaixo. Se estiver a executar um tipo diferente de serviço Azure, aqui estão as instruções para permitir o Profiler em outras plataformas suportadas:
* [Serviços em Nuvem](./profiler-cloudservice.md?toc=%2fazure%2fazure-monitor%2ftoc.json)
* [Aplicações de tecido de serviço](./profiler-servicefabric.md?toc=%2fazure%2fazure-monitor%2ftoc.json)
* [Máquinas Virtuais](./profiler-vm.md?toc=%2fazure%2fazure-monitor%2ftoc.json)

O Application Insights Profiler está pré-instalado como parte do tempo de funcionamento dos Serviços de Aplicação. Os passos abaixo mostrar-lhe-ão como capacitá-lo para o seu Serviço de Aplicações. Siga estes passos mesmo que tenha incluído a App Insights SDK na sua aplicação no momento de construção.

> [!NOTE]
> A instalação sem código do Application Insights Profiler segue a política de suporte .NET Core.
> Para obter mais informações sobre os tempos de funcionação suportados, consulte [a Política de Suporte do Núcleo .NET](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).

1. Navegue no painel de controlo Azure para o seu Serviço de Aplicações.
1. Ativar a definição "Always On" para o seu serviço de aplicações. Pode encontrar esta definição em **Definições**, página **de configuração** (ver screenshot no passo seguinte) e selecionar o separador **Definições Gerais.**
1. Navegue para Definições > página **de Insights de Aplicação.**

   ![Ativar insights de aplicativos no portal de serviços de aplicações](./media/profiler/AppInsights-AppServices.png)

1. Siga as instruções no painel para criar um novo recurso ou selecione um recurso de App Insights existente para monitorizar a sua aplicação. Certifique-se também de que o Profiler está **ligado**. Se o seu recurso Application Insights estiver numa subscrição diferente do seu Serviço de Aplicações, não pode utilizar esta página para configurar insights de aplicação. Ainda pode fazê-lo manualmente, criando manualmente as definições necessárias da aplicação. [A secção seguinte contém instruções para ativar manualmente o profiler.](#enable-profiler-manually-or-with-azure-resource-manager) 

   ![Adicionar extensão do site App Insights][Enablement UI]

1. O profiler está agora ativado através de uma Definição de Aplicação de Serviços de Aplicação.

    ![Definição de aplicativo para profiler][profiler-app-setting]

## <a name="enable-profiler-manually-or-with-azure-resource-manager"></a>Ativar o Profiler manualmente ou com o Gestor de Recursos Azure
O Profiler De Insights de Aplicações pode ser ativado criando definições de aplicações para o seu Serviço de Aplicações Azure. A página com as opções acima mostradas cria estas definições de aplicações para si. Mas pode automatizar a criação destas definições usando um modelo ou outros meios. Estas definições também funcionarão se o seu recurso Application Insights estiver numa subscrição diferente do seu Serviço de Aplicações Azure.
Aqui estão as definições necessárias para ativar o perfil:

|Definição da Aplicação    | Valor    |
|---------------|----------|
|APPINSIGHTS_INSTRUMENTATIONKEY         | iKey para o seu recurso Application Insights    |
|APPINSIGHTS_PROFILERFEATURE_VERSION | 1.0.0 |
|DiagnosticServices_EXTENSION_VERSION | ~3 |


Pode definir estes valores utilizando [modelos de gestores de recursos Azure,](./azure-web-apps.md#app-service-application-settings-with-azure-resource-manager) [Azure PowerShell,](/powershell/module/az.websites/set-azwebapp)  [Azure CLI](/cli/azure/webapp/config/appsettings).

## <a name="enable-profiler-for-other-clouds"></a>Ativar o Profiler para outras nuvens

Atualmente, as únicas regiões que necessitam de modificações no ponto final são [o Governo de Azure](../../azure-government/compare-azure-government-global-azure.md#application-insights) e [a Azure China.](/azure/china/resources-developer-guide)

|Definição da Aplicação    | Nuvem do Governo dos EUA | Nuvem da China |   
|---------------|---------------------|-------------|
|ApplicationInsightsProfilerEndpoint         | `https://profiler.monitor.azure.us`    | `https://profiler.monitor.azure.cn` |
|ApplicationInsightsEndpoint | `https://dc.applicationinsights.us` | `https://dc.applicationinsights.azure.cn` |

## <a name="disable-profiler"></a>Perfil de desativação

Para parar ou reiniciar profiler para uma instância individual de uma aplicação, na barra lateral esquerda, selecione **WebJobs** e pare o webjob denominado `ApplicationInsightsProfiler3` .

  ![Desativar o Profiler para um trabalho na web][disable-profiler-webjob]

Recomendamos que tenha o Profiler habilitado em todas as suas aplicações para descobrir quaisquer problemas de desempenho o mais cedo possível.

Os ficheiros do profiler podem ser eliminados quando utilizar o WebDeploy para implementar alterações na sua aplicação web. Pode evitar a eliminação excluindo a pasta App_Data de ser eliminada durante a colocação. 


## <a name="next-steps"></a>Passos seguintes

* [Trabalhar com o Application Insights no Visual Studio](./visual-studio.md)

[Enablement UI]: ./media/profiler/Enablement_UI.png
[profiler-app-setting]:./media/profiler/profiler-app-setting.png
[disable-profiler-webjob]: ./media/profiler/disable-profiler-webjob.png