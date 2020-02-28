---
title: Ativar o Snapshot Debugger para aplicações .NET no Serviço de Aplicações Azure  Microsoft Docs
description: Ativar snapshot Debugger para .NET apps no Serviço de Aplicações Azure
ms.topic: conceptual
author: brahmnes
ms.author: bfung
ms.date: 03/07/2019
ms.reviewer: mbullwin
ms.openlocfilehash: c23da585034e74d85be5a3c41b124f00408a0f4a
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77671431"
---
# <a name="enable-snapshot-debugger-for-net-apps-in-azure-app-service"></a>Ativar snapshot Debugger para .NET apps no Serviço de Aplicações Azure

O Snapshot Debugger trabalha atualmente para ASP.NET e ASP.NET aplicações Core que estão a funcionar no Azure App Service nos planos de serviço do Windows.

## <a id="installation"></a>Ativar o Debugger Snapshot
Para ativar o Snapshot Debugger para uma aplicação, siga as instruções abaixo. Se estiver a executar um tipo diferente de serviço Azure, aqui estão as instruções para ativar o Snapshot Debugger noutras plataformas suportadas:
* [Serviços em Nuvem do Azure](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Serviço Azure Fabric](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Máquinas virtuais azure e conjuntos de escala de máquinas virtuais](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [No local máquinas virtuais ou físicas](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)

Se estiver a utilizar uma versão de pré-visualização de .NET Core, siga as instruções para [enable Snapshot Debugger para outros ambientes](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) primeiro para incluir o pacote [Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet com a aplicação e, em seguida, complete o resto das instruções abaixo. 

A aplicação Insights Snapshot Debugger está pré-instalada como parte do tempo de funcionamento dos Serviços de Aplicações, mas precisa de o ligar para obter fotografias para a sua aplicação App Service. Depois de ter implementado uma aplicação, mesmo que tenha incluído o SDK de Insights de Aplicação no código fonte, siga os passos abaixo para ativar o desbugger instantâneo.

1. Vá ao painel de Serviços de **Aplicações** no portal Azure.
2. Navegue para **Configurações > Painel de Insights de Aplicação.**

   ![Ativar o App Insights no portal dos serviços de aplicações](./media/snapshot-debugger/applicationinsights-appservices.png)

3. Siga as instruções no painel para criar um novo recurso ou selecione um recurso app Insights existente para monitorizar a sua aplicação. Certifique-se também de que os dois interruptores para Snapshot Debugger estão **ligados**.

   ![Adicionar extensão de site do App Insights][Enablement UI]

4. O Snapshot Debugger está agora ativado através de uma definição de app services.

    ![Definição de aplicativo saqueado para debugger instantâneo][snapshot-debugger-app-setting]

## <a name="disable-snapshot-debugger"></a>Desativar o Debugger Snapshot

Siga os mesmos passos que para **Enable Snapshot Debugger**, mas mude ambos os interruptores para Snapshot Debugger to **Off**.
Recomendamos que tenha o Snapshot Debugger habilitado em todas as suas aplicações para facilitar o diagnóstico das exceções à aplicação.

## <a name="next-steps"></a>Passos seguintes

- Gere tráfego para a sua aplicação que pode desencadear uma exceção. Em seguida, aguarde 10 a 15 minutos para que as fotos sejam enviadas para a instância Deinsights de Aplicação.
- Veja [as fotos](snapshot-debugger.md?toc=/azure/azure-monitor/toc.json#view-snapshots-in-the-portal) no portal Azure.
- Para ajuda na resolução de problemas de problemas, consulte [a resolução de problemas do Snapshot Debugger.](snapshot-debugger-troubleshoot.md?toc=/azure/azure-monitor/toc.json)

[Enablement UI]: ./media/snapshot-debugger/enablement-ui.png
[snapshot-debugger-app-setting]:./media/snapshot-debugger/snapshot-debugger-app-setting.png

