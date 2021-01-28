---
title: Perfil ao vivo Aplicativos Azure App Service com Application Insights | Microsoft Docs
description: Perfil aplicativo ao vivo no Azure App Service com Perfil de Insights de Aplicação.
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: b1158a614da9ba32f628aba5dd2ed2cc71b4b455
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98947038"
---
# <a name="profile-live-azure-app-service-apps-with-application-insights"></a>Perfil ao vivo Aplicativos Azure App Service com Insights de Aplicações

Pode executar profiler em ASP.NET e ASP.NET aplicações Core que estão a ser executadas no Azure App Service usando o nível de serviço Básico ou superior. Atualmente, a ativação do Profiler no Linux só é possível através [deste método.](profiler-aspnetcore-linux.md)

## <a name="enable-profiler-for-your-app"></a><a id="installation"></a> Ativar o Profiler para a sua aplicação
Para ativar o Profiler para uma aplicação, siga as instruções abaixo. Se estiver a executar um tipo diferente de serviço Azure, aqui estão as instruções para permitir o Profiler em outras plataformas suportadas:
* [Serviços em Nuvem](./profiler-cloudservice.md?toc=%2fazure%2fazure-monitor%2ftoc.json)
* [Aplicações de tecido de serviço](./profiler-servicefabric.md?toc=%2fazure%2fazure-monitor%2ftoc.json)
* [Máquinas Virtuais](./profiler-vm.md?toc=%2fazure%2fazure-monitor%2ftoc.json)

O Application Insights Profiler está pré-instalado como parte do tempo de funcionamento dos Serviços de Aplicação. Os passos abaixo mostrar-lhe-ão como capacitá-lo para o seu Serviço de Aplicações. Siga estes passos mesmo que tenha incluído a App Insights SDK na sua aplicação no momento de construção.

1. Navegue no painel de controlo Azure para o seu Serviço de Aplicações.
1. Ativar a definição "Always On" para o seu serviço de aplicações. Pode encontrar esta definição em **Definições**, página **de configuração** (ver screenshot no passo seguinte) e clicar no **separador Definições Gerais.**
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

### <a name="enabling-profiler-for-other-clouds-manually"></a>Habilitação do Profiler para outras nuvens manualmente

Se pretender ativar o perfil para outras nuvens, pode utilizar as definições de aplicação abaixo.

|Definição da Aplicação    | Valores do Governo dos EUA| Nuvem da China |   
|---------------|---------------------|-------------|
|ApplicationInsightsProfilerEndpoint         | `https://profiler.monitor.azure.us`    | `https://profiler.monitor.azure.cn` |
|ApplicationInsightsEndpoint | `https://dc.applicationinsights.us` | `https://dc.applicationinsights.azure.cn` |

## <a name="disable-profiler"></a>Perfil de desativação

Para parar ou reiniciar profiler para uma aplicação individual, no **WebJobs** e parar o webjob chamado ApplicationInsightsProfiler3. Mesmo que o perfilador seja desativado utilizando o interruptor na página 'Insights de aplicação' como descrito acima, o processo do perfil ainda será executado. O perfis verificará se está ativado. Se estiver desativado, irá dormir por um período de tempo antes de verificar novamente. Não faz perfis se for desativado. Se desativar este webjob, o processo do profiler não funcionará de todo, mesmo para verificar se está ativado.

  ![Desativar o Profiler para um trabalho na web][disable-profiler-webjob]

Recomendamos que tenha o Profiler habilitado em todas as suas aplicações para descobrir quaisquer problemas de desempenho o mais cedo possível.

Os ficheiros do profiler podem ser eliminados quando utilizar o WebDeploy para implementar alterações na sua aplicação web. Pode evitar a eliminação excluindo a pasta App_Data de ser eliminada durante a colocação. 


## <a name="next-steps"></a>Próximos passos

* [Trabalhar com o Application Insights no Visual Studio](./visual-studio.md)

[Enablement UI]: ./media/profiler/Enablement_UI.png
[profiler-app-setting]:./media/profiler/profiler-app-setting.png
[disable-profiler-webjob]: ./media/profiler/disable-profiler-webjob.png

