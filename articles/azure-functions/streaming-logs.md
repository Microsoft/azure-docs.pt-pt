---
title: Registos de execução de fluxo em Funções Azure
description: 115-145 caracteres, incluindo espaços. Esta síntese é apresentada no resultado da pesquisa.
ms.date: 9/1/2020
ms.topic: how-to
ms.service: azure-functions
ms.custom: can-be-multiple-comma-separated
ms.openlocfilehash: e2e4bdb2c1adf53fabf593a4f0c84873786ca32a
ms.sourcegitcommit: 419c8c8061c0ff6dc12c66ad6eda1b266d2f40bd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/18/2020
ms.locfileid: "92168950"
---
# <a name="enable-streaming-execution-logs-in-azure-functions"></a>Ativar registos de execução em funções de Azure

Ao desenvolver uma aplicação, muitas vezes quer ver o que está a ser escrito nos registos em tempo real quando corre em Azure.

Existem duas formas de ver um fluxo de ficheiros de registo a ser gerado pelas execuções de funções.

* **Streaming de registo incorporado**: a plataforma Serviço de Aplicações permite-lhe visualizar um fluxo de ficheiros de registo de aplicações. Isto é equivalente à saída observada quando desativa as suas funções durante o [desenvolvimento local](functions-develop-local.md) e quando utiliza o separador **Teste** no portal. Todas as informações baseadas em registos são apresentadas. Para obter mais informações, consulte [os registos de stream](../app-service/troubleshoot-diagnostic-logs.md#stream-logs). Este método de streaming suporta apenas uma única instância, e não pode ser usado com uma aplicação em execução no Linux num plano de Consumo.

* **Live Metrics Stream**: quando a sua aplicação de função está [ligada ao Application Insights](configure-monitoring.md#enable-application-insights-integration), pode visualizar dados de registo e outras métricas em quase tempo real no portal Azure utilizando live [metrics stream](../azure-monitor/app/live-stream.md). Utilize este método ao monitorizar as funções em execução em múltiplas instâncias ou em Linux num plano de consumo. Este método utiliza [dados amostrados.](configure-monitoring.md#configure-sampling)

Os fluxos de log podem ser vistos tanto no portal como na maioria dos ambientes de desenvolvimento local. 

## <a name="portal"></a>Portal

Pode ver ambos os tipos de correntes de registo no portal.

### <a name="built-in-log-streaming"></a>Transmissão em fluxo de registos incorporada

Para visualizar registos de streaming no portal, selecione o separador **funcionalidades da Plataforma** na sua aplicação de função. Em seguida, em **Monitoring,** escolha **o streaming de Registo**.

![Ativar registos de streaming no portal](./media/functions-monitoring/enable-streaming-logs-portal.png)

Isto liga a sua aplicação ao serviço de streaming de registos e os registos de aplicações são apresentados na janela. Pode alternar entre **registos de aplicações** e **registos de servidores Web**.  

![Ver registos de streaming no portal](./media/functions-monitoring/streaming-logs-window.png)

### <a name="live-metrics-stream"></a>Live Metrics Stream

Para ver o Live Metrics Stream para a sua aplicação, selecione o **separador Visão Geral** da sua aplicação de função. Quando tiver o Application Insights ativa, vê um link **'Insights de Aplicação'** nas **funcionalidades configuradas.** Este link leva-o à página 'Insights' de aplicação para a sua aplicação.

Em Application Insights, selecione **Live Metrics Stream**. [As entradas de registo amostradas](configure-monitoring.md#configure-sampling) são apresentadas em **Telemetria de Amostra**.

![Ver Live Metrics Stream no portal](./media/functions-monitoring/live-metrics-stream.png) 

## <a name="visual-studio-code"></a>Visual Studio Code

[!INCLUDE [functions-enable-log-stream-vs-code](../../includes/functions-enable-log-stream-vs-code.md)]

## <a name="core-tools"></a>Ferramentas Centrais

[!INCLUDE [functions-streaming-logs-core-tools](../../includes/functions-streaming-logs-core-tools.md)]

## <a name="azure-cli"></a>CLI do Azure

Pode ativar os registos de streaming utilizando o [Azure CLI](/cli/azure/install-azure-cli). Utilize os seguintes comandos para iniciar sessão, escolher a sua subscrição e transmitir ficheiros de registo:

```azurecli
az login
az account list
az account set --subscription <subscriptionNameOrId>
az webapp log tail --resource-group <RESOURCE_GROUP_NAME> --name <FUNCTION_APP_NAME>
```

## <a name="azure-powershell"></a>Azure PowerShell

Pode ativar registos de streaming utilizando [a Azure PowerShell](/powershell/azure/). Para powerShell, utilize o comando [Set-AzWebApp](/powershell/module/az.websites/set-azwebapp) para permitir o início de sessão na aplicação de função, como mostrado no seguinte corte: 

:::code language="powershell" source="~/powershell_scripts/app-service/monitor-with-logs/monitor-with-logs.ps1" range="19-20":::

Para mais informações, consulte o exemplo completo do [código.](../app-service/scripts/powershell-monitor.md#sample-script) 

## <a name="next-steps"></a>Passos seguintes

+ [Monitorizar as Funções do Azure](functions-monitoring.md)
+ [Analisar telemetria de funções Azure em Insights de Aplicação](analyze-telemetry-data.md)
