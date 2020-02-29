---
title: 'Tutorial: Resolução de problemas com monitor Azure'
description: Saiba como o Azure Monitor e o Log Analytics ajudam a monitorizar a sua aplicação web do App Service. O Azure Monitor maximiza a disponibilidade através da entrega de uma solução abrangente para monitorizar os seus ambientes.
author: msangapu-msft
ms.author: msangapu
ms.topic: tutorial
ms.date: 1/29/2019
ms.openlocfilehash: c0df972d893e82d7b49d5b3ed2a2bbf7067548c2
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/29/2020
ms.locfileid: "78201816"
---
# <a name="tutorial-troubleshoot-an-app-service-app-with-azure-monitor"></a>Tutorial: Troubleshoot uma app de serviço de aplicações com monitor Azure

O [Serviço de Aplicações no Linux](app-service-linux-intro.md) oferece um serviço de alojamento na Web altamente dimensionável e com correção automática através do sistema operativo Linux. [O Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) maximiza a disponibilidade e desempenho das suas aplicações e serviços, fornecendo uma solução abrangente para recolher, analisar e atuar em telemetria a partir da sua nuvem e ambientes no local.

Este tutorial mostra como resolver problemas com uma aplicação usando [o Monitor Azure.](https://docs.microsoft.com/azure/azure-monitor/overview) A aplicação de amostra inclui código destinado a esgotar a memória e causar erros HTTP 500, para que possa diagnosticar e corrigir o problema usando o Monitor Azure.

Quando terminar, terá uma aplicação de amostra sintetizar no Serviço de Aplicações do Linux integrada com o [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview).

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Configure uma aplicação web com o Monitor Azure
> * Enviar registos de consolas para Log Analytics
> * Use consultas de registo para identificar e resolver erros de aplicações web

Pode seguir os passos neste tutorial em macOS, Linux e Windows.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para completar este tutorial, você precisará:

- [Subscrição do Azure](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli)
- [Git](https://git-scm.com/)

## <a name="create-azure-resources"></a>Criar recursos do Azure

Primeiro, executa vários comandos localmente para configurar uma aplicação de amostra para usar com este tutorial. Os comandos clonam uma aplicação de amostra, criam recursos Azure, criam um utilizador de implementação e implantam a app para o Azure. Será solicitado para a palavra-passe fornecida como parte da criação do utilizador de implementação. 

```bash
git clone https://github.com/Azure-Samples/App-Service-Troubleshoot-Azure-Monitor
az group create --name myResourceGroup --location "South Central US"
az webapp deployment user set --user-name <username> --password <password>
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku B1 --is-linux
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app-name> --runtime "PHP|7.3" --deployment-local-git
git remote add azure <url_from_previous_step>
git push azure master
```

## <a name="configure-azure-monitor"></a>Configure Azure Monitor

### <a name="create-a-log-analytics-workspace"></a>Criar um espaço de trabalho de Log Analytics

Agora que implementou a aplicação de amostras para o Azure App Service, irá configurar a capacidade de monitorização para resolver problemas na aplicação quando surgirem problemas. O Azure Monitor armazena dados de registo num espaço de trabalho do Log Analytics. Um espaço de trabalho é um recipiente que inclui dados e informações de configuração.

Neste passo, cria-se um espaço de trabalho de Log Analytics para configurar o Monitor Azure com a sua aplicação.

```bash
az monitor log-analytics workspace create --resource-group myResourceGroup --workspace-name myMonitorWorkspace
```

> [!NOTE]
> [Para o Azure Monitor Log Analytics, paga-se pela ingestão de dados e pela retenção de dados.](https://azure.microsoft.com/pricing/details/monitor/)
>

### <a name="create-a-diagnostic-setting"></a>Criar uma definição de diagnóstico

As definições de diagnóstico podem ser utilizadas para recolher métricas para determinados serviços Azure em Registos do Monitor Azure para análise com outros dados de monitorização utilizando consultas de registo. Para este tutorial, ativa o servidor web e os registos de saída/erro padrão. Consulte os tipos de [registo suportados](https://docs.microsoft.com/azure/app-service/troubleshoot-diagnostic-logs#supported-log-types) para obter uma lista completa de tipos de registo e descrições.

Executa os seguintes comandos para criar definições de diagnóstico para AppServiceConsoleLogs (saída/erro padrão) e AppServiceHTTPLogs (registos de servidores web). Substitua _\<nome de aplicativo>_ e _\<workspace-name>_ com os seus valores. 

> [!NOTE]
> Os dois primeiros comandos, `resourceID` e `workspaceID`, são variáveis a serem usadas no comando `az monitor diagnostic-settings create`. Consulte As definições de [diagnóstico Do Uso do Azure CLI](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings#create-diagnostic-settings-using-azure-cli) para obter mais informações sobre este comando.
>

```bash

resourceID=$(az webapp show -g myResourceGroup -n <app-name> --query id --output tsv)

workspaceID=$(az monitor log-analytics workspace show -g myResourceGroup --workspace-name <workspace-name> --query id --output tsv)

az monitor diagnostic-settings create --resource $resourceID \
 --workspace $workspaceID \
 -n myMonitorLogs \
 --logs '[{"category": "AppServiceConsoleLogs", "enabled": true},
  {"category": "AppServiceHTTPLogs", "enabled": true}]'

```

## <a name="troubleshoot-the-app"></a>Problemas de resolução da app

Navegue para `http://<app-name>.azurewebsites.net`.

A aplicação de amostra, ImageConverter, converte imagens de `JPG` a `PNG`. Um inseto foi deliberadamente colocado no código para este tutorial. Se selecionar imagens suficientes, a aplicação produz um erro HTTP 500 durante a conversão da imagem. Imagine que este cenário não foi considerado durante a fase de desenvolvimento. Vais usar o Monitor Azure para resolver o erro.

### <a name="verify-the-app-is-works"></a>Verifique se a aplicação está em funcionamento

Para converter imagens, clique em `Tools` e selecione `Convert to PNG`.

![Clique em 'Ferramentas' e selecione 'Converter para PNG'](./media/tutorial-azure-monitor/sample-monitor-app-tools-menu.png)

Selecione as duas primeiras imagens e clique em `convert`. Isto converter-se-á com sucesso.

![Selecione as duas primeiras imagens](./media/tutorial-azure-monitor/sample-monitor-app-convert-two-images.png)

### <a name="break-the-app"></a>Quebrar a app

Agora que verificou a aplicação convertendo duas imagens com sucesso, tentaremos converter as cinco primeiras imagens.

![Converter as primeiras cinco imagens](./media/tutorial-azure-monitor/sample-monitor-app-convert-five-images.png)

Esta ação falha e produz um erro `HTTP 500` que não foi testado durante o desenvolvimento.

![O convertido resultará num erro HTTP 500](./media/tutorial-azure-monitor/sample-monitor-app-http-500.png)

## <a name="use-log-query-to-view-azure-monitor-logs"></a>Utilize consulta de registo para ver registos do Monitor Azure

Vamos ver quais os registos disponíveis no espaço de trabalho do Log Analytics. 

Clique neste link espaço de [trabalho Log Analytics](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.OperationalInsights%2Fworkspaces) para aceder ao seu espaço de trabalho no portal Azure.

No portal Azure, selecione o seu espaço de trabalho Log Analytics.

### <a name="log-queries"></a>Registar consultas

As consultas de registo ajudam-no a alavancar totalmente o valor dos dados recolhidos nos Registos do Monitor do Azure. Utiliza consultas de registo para identificar os registos tanto em AppServiceHTTPLogs como em AppServiceConsoleLogs. Consulte a [visão geral](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview) da consulta de registo para obter mais informações sobre consultas de registo.

### <a name="view-appservicehttplogs-with-log-query"></a>Ver AppServiceHTTPLogs com consulta de registo

Agora que acedemos à aplicação, vamos ver os dados associados aos pedidos http, encontrados no `AppServiceHTTPLogs`.

1. Clique `Logs` da navegação à esquerda.

![Log Anlytics Worksace Logs](./media/tutorial-azure-monitor/log-analytics-workspace-logs.png)

2. Procure `appservice` e clique duplo `AppServiceHTTPLogs`.

![Tabelas de espaço de trabalho de análise de log analytics](./media/tutorial-azure-monitor/log-analytics-workspace-app-service-tables.png)

3. Clique em `Run`.

![Log Analytics Workspace App Service HTTP Logs](./media/tutorial-azure-monitor/log-analytics-workspace-app-service-http-logs.png)

A consulta `AppServiceHTTPLogs` devolve todos os pedidos nas últimas 24 horas. A coluna `ScStatus` contém o estado HTTP. Para diagnosticar os erros `HTTP 500`, limite a `ScStatus` a 500 e execute a consulta, como mostrado abaixo:

```kusto
AppServiceHTTPLogs
| where ScStatus == 500
```

### <a name="view-appserviceconsolelogs-with-log-query"></a>Ver AppServiceConsoleLogs com consulta de registo

Agora que confirmou o HTTP 500s, vamos dar uma olhada na saída/erros padrão da aplicação. Estes registos encontram-se em 'AppServiceConsoleLogs'.

(1) Clique `+` para criar uma nova consulta. 

(2) Clique duas vezes na tabela `AppServiceConsoleLogs` e clique `Run`. 

Uma vez que a conversão de cinco imagens resulta em erros do servidor, pode ver se a aplicação também está a escrever erros filtrando `ResultDescription` erros, como mostra abaixo:

```kusto
AppServiceConsoleLogs |
where ResultDescription  contains "error" 
```

Na coluna `ResultDescription`, verá o seguinte erro:

```
PHP Fatal error:  Allowed memory size of 134217728 bytes exhausted 
(tried to allocate 16384 bytes) in /home/site/wwwroot/process.php on line 20, 
referer: http://<app-name>.azurewebsites.net/
```

### <a name="join-appservicehttplogs-and-appserviceconsolelogs"></a>Junte-se a AppServiceHTTPLogs e AppServiceConsoleLogs

Agora que identificou os erros http 500 e os erros padrão, precisa confirmar se há uma correlação entre estas mensagens. Em seguida, junte-se às mesas com base no carimbo de tempo, `TimeGenerated`.

> [!NOTE]
> Foi preparada uma consulta para si que faz o seguinte:
>
> - Filtros HTTPLogs para 500 erros
> - Consultas de registos de consolas
> - Junta-se às mesas no `TimeGenerated`
>

Execute a seguinte consulta:

```kusto
let myHttp = AppServiceHTTPLogs | where  ScStatus == 500 | project TimeGen=substring(TimeGenerated, 0, 19), CsUriStem, ScStatus;  

let myConsole = AppServiceConsoleLogs | project TimeGen=substring(TimeGenerated, 0, 19), ResultDescription;

myHttp | join myConsole on TimeGen | project TimeGen, CsUriStem, ScStatus, ResultDescription;
```

Na coluna `ResultDescription`, verá o seguinte erro ao mesmo tempo que erros do servidor web:

```
PHP Fatal error:  Allowed memory size of 134217728 bytes exhausted 
(tried to allocate 16384 bytes) in /home/site/wwwroot/process.php on line 20, 
referer: http://<app-name>.azurewebsites.net/
```

A mensagem indica que a memória foi esgotada na linha 20 de `process.php`. Confirmou agora que a aplicação produziu um erro durante o erro HTTP 500. Vamos ver o código para identificar o problema.

## <a name="identify-the-error"></a>Identifique o erro

No diretório local, abra o `process.php` e olhe para a linha 20. 

```php
imagepng($imgArray[$x], $filename);
```

O primeiro argumento, `$imgArray[$x]`, é uma variável que mantém todos os JPGs (em memória) que precisam de conversão. No entanto, `imagepng` só precisa que a imagem seja convertida e não todas as imagens. As imagens de pré-carregamento não são necessárias e podem estar a causar a exaustão da memória, levando a HTTP 500s. Vamos atualizar o código para carregar imagens a pedido para ver se resolve o problema. Em seguida, melhorará o código para resolver o problema da memória.

## <a name="fix-the-app"></a>Corrigir a aplicação

### <a name="update-locally-and-redeploy-the-code"></a>Atualizar localmente e reimplementar o código

Faça as seguintes alterações na `process.php` para lidar com a exaustão da memória:

```php
<?php

//Retrieve query parameters
$maxImages = $_GET['images'];
$imgNames  = explode(",",$_GET['imgNames']);

//Load JPEGs into an array (in memory)
for ($x=0; $x<$maxImages; $x++){
    $filename = './images/converted_' . substr($imgNames[$x],0,-4) . '.png';
    imagepng(imagecreatefromjpeg("./images/" . $imgNames[$x]), $filename);
}
```

Consolide as suas alterações no Git e envie as alterações ao código para o Azure.

```bash
git commit -am "Load images on-demand in process.php"
git push azure master
```

### <a name="browse-to-the-azure-app"></a>Navegue na app Azure

Navegue para `http://<app-name>.azurewebsites.net`. 

A conversão de imagens não deve continuar a produzir os erros HTTP 500.

![Aplicação PHP em execução no Serviço de Aplicações do Azure](./media/tutorial-azure-monitor/sample-monitor-app-working.png)

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

Eliminar a definição de diagnóstico com o seguinte comando:

```bash
az monitor diagnostic-settings delete --resource $resourceID -n myMonitorLogs
```
O que aprendeu:

> [!div class="checklist"]
> * Configurado uma aplicação web com o Monitor Azure
> * Registos enviados para Log Analytics
> * Consultas de registo usadas para identificar e resolver problemas na aplicação web

## <a name="nextsteps"></a> Passos seguintes
* [Registos de consulta com monitor Azure](../../azure-monitor/log-query/log-query-overview.md)
* [Troubleshooting Azure App Service em Estúdio Visual](../troubleshoot-dotnet-visual-studio.md)
* [Analisar registos de aplicativos no HDInsight](https://gallery.technet.microsoft.com/scriptcenter/Analyses-Windows-Azure-web-0b27d413)
