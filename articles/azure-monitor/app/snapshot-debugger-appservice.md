---
title: Habilitar Depurador de Instantâneos para aplicativos .NET no serviço Azure App | Microsoft Docs
description: Habilitar Depurador de Instantâneos para aplicativos .NET no serviço Azure App
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: brahmnes
ms.author: bfung
ms.date: 03/07/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 0f6eb6376075337edd7656e4bc83b5b7fddde479
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72899898"
---
# <a name="enable-snapshot-debugger-for-net-apps-in-azure-app-service"></a>Habilitar Depurador de Instantâneos para aplicativos .NET no serviço Azure App

Depurador de Instantâneos atualmente trabalha para aplicativos ASP.NET e ASP.NET Core que estão em execução no serviço Azure App nos planos de serviço do Windows.

## <a id="installation"></a>Habilitar Depurador de Instantâneos
Para habilitar Depurador de Instantâneos para um aplicativo, siga as instruções abaixo. Se você estiver executando um tipo diferente de serviço do Azure, aqui estão as instruções para habilitar Depurador de Instantâneos em outras plataformas com suporte:
* [Azure Cloud Services](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Serviços de Service Fabric do Azure](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Máquinas virtuais do Azure e conjuntos de dimensionamento de máquinas virtuais](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Máquinas virtuais ou físicas locais](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)

Se você estiver usando uma versão de visualização do .NET Core, siga as instruções para [habilitar depurador de instantâneos para outros ambientes](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) primeiro para incluir o pacote NuGet [Microsoft. ApplicationInsights. SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) com o aplicativo, e, em seguida, conclua o restante das instruções abaixo. 

Application Insights Depurador de Instantâneos é pré-instalado como parte do tempo de execução dos serviços de aplicativo, mas você precisa ativá-lo para obter instantâneos para seu aplicativo do serviço de aplicativo. Depois de implantar um aplicativo, mesmo que você tenha incluído o SDK do Application Insights no código-fonte, siga as etapas abaixo para habilitar o depurador de instantâneos.

1. Vá para o painel **serviços de aplicativos** na portal do Azure.
2. Navegue até **configurações >** painel de Application insights.

   ![Habilitar o app insights no portal de serviços de aplicativos](./media/snapshot-debugger/applicationinsights-appservices.png)

3. Siga as instruções no painel para criar um novo recurso ou selecione um recurso existente do App insights para monitorar seu aplicativo. Verifique também se ambas as opções de Depurador de Instantâneos estão **ativadas**.

   ![Adicionar extensão do site do App insights][Enablement UI]

4. Depurador de Instantâneos agora está habilitado usando uma configuração de aplicativo dos serviços de aplicativo.

    ![Configuração de aplicativo para Depurador de Instantâneos][snapshot-debugger-app-setting]

## <a name="disable-snapshot-debugger"></a>Desabilitar Depurador de Instantâneos

Siga as mesmas etapas necessárias para **habilitar o depurador de instantâneos**, mas alterne ambos os comutadores para depurador de instantâneos para **desativado**.
É recomendável que você tenha Depurador de Instantâneos habilitado em todos os seus aplicativos para facilitar o diagnóstico de exceções de aplicativo.

## <a name="next-steps"></a>Passos seguintes

- Gere o tráfego para o aplicativo que pode disparar uma exceção. Em seguida, aguarde de 10 a 15 minutos para que os instantâneos sejam enviados para a instância de Application Insights.
- Consulte [instantâneos](snapshot-debugger.md?toc=/azure/azure-monitor/toc.json#view-snapshots-in-the-portal) na portal do Azure.
- Para obter ajuda com a solução de problemas de Depurador de Instantâneos, consulte [depurador de instantâneos solução de problemas](snapshot-debugger-troubleshoot.md?toc=/azure/azure-monitor/toc.json).

[Enablement UI]: ./media/snapshot-debugger/enablement-ui.png
[snapshot-debugger-app-setting]:./media/snapshot-debugger/snapshot-debugger-app-setting.png

