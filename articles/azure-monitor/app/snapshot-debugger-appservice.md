---
title: Ativar o depurador de instantâneos para aplicações de .NET no serviço de aplicações do Azure | Documentos da Microsoft
description: Ativar o depurador de instantâneos para aplicações de .NET no serviço de aplicações do Azure
services: application-insights
documentationcenter: ''
author: brahmnes
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: mbullwin
ms.date: 03/07/2019
ms.author: brahmnes
ms.openlocfilehash: 7ff93dffe2dd82bdbba204b4235a297b337438f5
ms.sourcegitcommit: b8f9200112cae265155b8877f7e1621c4bcc53fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/14/2019
ms.locfileid: "57864095"
---
# <a name="enable-snapshot-debugger-for-net-apps-in-azure-app-service"></a>Ativar o depurador de instantâneos para aplicações de .NET no serviço de aplicações do Azure

Depurador de instantâneos trabalha atualmente para aplicações ASP.NET e ASP.NET Core em execução no App Service do Azure nos planos de serviço do Windows.

## <a id="installation"></a> Ativar o depurador de instantâneos
Para ativar o depurador de instantâneos para uma aplicação, siga as instruções abaixo. Se estiver a executar um tipo diferente de serviço do Azure, aqui estão as instruções para ativar o Snapshot Debugger em outras plataformas suportadas:
* [Azure Cloud Services](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Serviços do Azure Service Fabric](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Conjuntos de dimensionamento de máquinas virtuais e máquinas virtuais do Azure](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Máquinas virtuais ou físicos no local](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)

Snapshot Debugger do Application Insights é previamente instalado como parte do tempo de execução dos serviços de aplicações, mas terá de ativá-la para get instantâneos para a sua aplicação de serviço de aplicações. Assim que tiver implementado uma aplicação, mesmo se o ter incluído o Application Insights SDK no código-fonte, siga os passos abaixo para ativar o snapshot debugger.

1. Vá para o **dos serviços de aplicações** painel no portal do Azure.
2. Navegue para **definições > Application Insights** painel.

   ![Ativar o App Insights no portal dos serviços de aplicações](./media/snapshot-debugger/applicationinsights-appservices.png)

3. Ou siga as instruções no painel para criar um novo recurso ou selecione um recurso do App Insights existente para monitorizar a aplicação. Além disso, certifique-se de que ambas as opções para o Snapshot Debugger **no**.

   ![Adicionar extensão de site do App Insights][Enablement UI]

4. Depurador de instantâneos está agora ativada com uma definição de aplicação de serviços de aplicação.

    ![Definição de aplicação para o depurador de instantâneos][snapshot-debugger-app-setting]

## <a name="disable-snapshot-debugger"></a>Desativar o depurador de instantâneos

Siga os passos como para **ativar o Snapshot Debugger**, mas mudar de ambas as opções para o depurador de instantâneos para **desativar**.
Recomendamos que tenha Snapshot Debugger ativada em todas as suas aplicações para facilitar o diagnóstico de exceções de aplicativos.

## <a name="next-steps"></a>Passos Seguintes

* [Trabalhar com o Application Insights no Visual Studio](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio)

[Enablement UI]: ./media/snapshot-debugger/enablement-ui.png
[snapshot-debugger-app-setting]:./media/snapshot-debugger/snapshot-debugger-app-setting.png

