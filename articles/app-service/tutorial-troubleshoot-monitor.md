---
title: 'Tutorial: Resolução de problemas com monitor Azure'
description: Saiba como o Azure Monitor e o Log Analytics o ajudam a monitorizar a sua aplicação web do Serviço de Aplicações. O Azure Monitor maximiza a disponibilidade através da entrega de uma solução abrangente para monitorizar os seus ambientes.
author: msangapu-msft
ms.author: msangapu
ms.topic: tutorial
ms.date: 06/20/2020
ms.openlocfilehash: af2711a3d219bb472334ad61bad0b87f6c691dab
ms.sourcegitcommit: 0aec60c088f1dcb0f89eaad5faf5f2c815e53bf8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2021
ms.locfileid: "98183205"
---
# <a name="tutorial-troubleshoot-an-app-service-app-with-azure-monitor"></a>Tutorial: Resolução de problemas de uma aplicação do Serviço de Aplicações com o Azure Monitor

> [!NOTE]
> A integração do Azure Monitor com o Serviço de Aplicações está em [pré-visualização.](https://aka.ms/appsvcblog-azmon)
>

Este tutorial mostra como resolver problemas numa aplicação [do Serviço de Aplicações](overview.md) utilizando [o Azure Monitor.](../azure-monitor/overview.md) A aplicação da amostra inclui código destinado a esgotar a memória e causar erros HTTP 500, para que possa diagnosticar e corrigir o problema usando o Azure Monitor. Quando terminar, terá uma aplicação de amostra em execução no Serviço de Aplicações em Linux integrada com [o Azure Monitor.](../azure-monitor/overview.md)

[O Azure Monitor](../azure-monitor/overview.md) maximiza a disponibilidade e desempenho das suas aplicações e serviços, fornecendo uma solução abrangente para recolher, analisar e agir em telemetria a partir dos seus ambientes de nuvem e no local.

Neste tutorial, vai aprender a:

> [!div class="checklist"]
> * Configure uma aplicação web com o Azure Monitor
> * Enviar registos de consolas para Registar Analytics
> * Use consultas de Registo para identificar e resolver erros de aplicações web

Pode seguir os passos neste tutorial em macOS, Linux e Windows.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para completar este tutorial, você precisará:

- [Subscrição do Azure](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- [CLI do Azure](/cli/azure/install-azure-cli)
- [Rio Git](https://git-scm.com/)

## <a name="create-azure-resources"></a>Criar recursos do Azure

Primeiro, você executou vários comandos localmente para configurar uma aplicação de amostra para usar com este tutorial. Os comandos clonam uma aplicação de amostra, criam recursos Azure, criam um utilizador de implementação e implementam a aplicação para o Azure. Será solicitado a palavra-passe fornecida como parte da criação do utilizador de implementação. 

```bash
git clone https://github.com/Azure-Samples/App-Service-Troubleshoot-Azure-Monitor
az group create --name myResourceGroup --location "South Central US"
az webapp deployment user set --user-name <username> --password <password>
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku B1 --is-linux
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app-name> --runtime "PHP|7.3" --deployment-local-git
git remote add azure <url_from_previous_step>
git push azure main
```

## <a name="configure-azure-monitor-preview"></a>Configure Azure Monitor (pré-visualização)

### <a name="create-a-log-analytics-workspace"></a>Criar um espaço de trabalho de Log Analytics

Agora que implementou a aplicação da amostra para o Azure App Service, irá configurar a capacidade de monitorização para resolver problemas na aplicação quando surgirem problemas. O Azure Monitor armazena dados de registo num espaço de trabalho do Log Analytics. Um espaço de trabalho é um recipiente que inclui informações de dados e configuração.

Neste passo, cria um espaço de trabalho Log Analytics para configurar o Azure Monitor com a sua aplicação.

```bash
az monitor log-analytics workspace create --resource-group myResourceGroup --workspace-name myMonitorWorkspace
```

> [!NOTE]
> [Para o Azure Monitor Log Analytics, paga pela ingestão de dados e retenção de dados.](https://azure.microsoft.com/pricing/details/monitor/)
>

### <a name="create-a-diagnostic-setting"></a>Criar uma definição de diagnóstico

As definições de diagnóstico podem ser usadas para recolher métricas de certos serviços Azure em Registos monitores Azure para análise com outros dados de monitorização utilizando consultas de registo. Para este tutorial, ativa o servidor web e os registos de saída/erro padrão. Consulte [os tipos de registo suportados](./troubleshoot-diagnostic-logs.md#supported-log-types) para obter uma lista completa de tipos e descrições de registos.

Executou os seguintes comandos para criar definições de diagnóstico para AppServiceConsoleLogs (saída/erro padrão) e AppServiceHTTPLogs (registos de servidores web). Substitua _\<app-name>_ e _\<workspace-name>_ os seus valores. 

> [!NOTE]
> Os dois primeiros `resourceID` `workspaceID` comandos, e, são variáveis para serem usados no `az monitor diagnostic-settings create` comando. Consulte [Configurações de diagnóstico utilizando O Azure CLI](../azure-monitor/platform/diagnostic-settings.md#create-using-azure-cli) para obter mais informações sobre este comando.
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

## <a name="troubleshoot-the-app"></a>Resolução de problemas da app

Navegue para `http://<app-name>.azurewebsites.net`.

A aplicação da amostra, ImageConverter, converte imagens incluídas de `JPG` `PNG` . Um inseto foi deliberadamente colocado no código para este tutorial. Se selecionar imagens suficientes, a aplicação produz um erro HTTP 500 durante a conversão de imagem. Imagine que este cenário não foi considerado durante a fase de desenvolvimento. Utilizará o Monitor Azure para resolver o erro.

### <a name="verify-the-app-is-works"></a>Verifique se a aplicação está funcionando

Para converter imagens, clique `Tools` e selecione `Convert to PNG` .

![Clique em 'Ferramentas' e selecione 'Converta para PNG'](./media/tutorial-azure-monitor/sample-monitor-app-tools-menu.png)

Selecione as duas primeiras imagens e clique `convert` em . Isto converter-se-á com sucesso.

![Selecione as duas primeiras imagens](./media/tutorial-azure-monitor/sample-monitor-app-convert-two-images.png)

### <a name="break-the-app"></a>Quebre a aplicação

Agora que verificou a aplicação convertendo duas imagens com sucesso, tentaremos converter as primeiras cinco imagens.

![Converter as primeiras cinco imagens](./media/tutorial-azure-monitor/sample-monitor-app-convert-five-images.png)

Esta ação falha e produz um `HTTP 500` erro que não foi testado durante o desenvolvimento.

![A conversão resultará num erro HTTP 500](./media/tutorial-azure-monitor/sample-monitor-app-http-500.png)

## <a name="use-log-query-to-view-azure-monitor-logs"></a>Utilize consulta de log para visualizar registos do Monitor Azure

Vamos ver que registos estão disponíveis no espaço de trabalho do Log Analytics. 

Clique neste [link do espaço de trabalho Do Log Analytics](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.OperationalInsights%2Fworkspaces) para aceder ao seu espaço de trabalho no portal Azure.

No portal Azure, selecione o seu espaço de trabalho Log Analytics.

### <a name="log-queries"></a>Registar consultas

As consultas de registo ajudam-no a aproveitar totalmente o valor dos dados recolhidos nos Registos do Monitor Azure. Utiliza consultas de registo para identificar os registos tanto em AppServiceHTTPLogs como em AppServiceConsoleLogs. Consulte a [visão geral](../azure-monitor/log-query/log-query-overview.md) da consulta de registo para obter mais informações sobre consultas de registo.

### <a name="view-appservicehttplogs-with-log-query"></a>Ver AppServiceHTTPLogs com consulta de log

Agora que acedemos à app, vamos ver os dados associados aos pedidos HTTP, encontrados na `AppServiceHTTPLogs` .

1. Clique `Logs` na navegação à esquerda.

![Log Anlytics Worksace Logs](./media/tutorial-azure-monitor/log-analytics-workspace-logs.png)

2. Procure `appservice` e clique duas `AppServiceHTTPLogs` vezes.

![Tabelas de espaço de trabalho de analítica de registo](./media/tutorial-azure-monitor/log-analytics-workspace-app-service-tables.png)

3. Clique em `Run`.

![Registar Registos HTTP Do Serviço de Aplicações do Espaço De Trabalho do Registo](./media/tutorial-azure-monitor/log-analytics-workspace-app-service-http-logs.png)

A `AppServiceHTTPLogs` consulta devolve todos os pedidos nas últimas 24 horas. A coluna `ScStatus` contém o estado HTTP. Para diagnosticar os `HTTP 500` erros, limite o `ScStatus` a 500 e faça a consulta, como mostra abaixo:

```kusto
AppServiceHTTPLogs
| where ScStatus == 500
```

### <a name="view-appserviceconsolelogs-with-log-query"></a>Ver AppServiceConsoleLogs com consulta de registo

Agora que confirmou o HTTP 500s, vamos dar uma olhada na saída/erros padrão da aplicação. Estes registos encontram-se em 'AppServiceConsoleLogs'.

(1) Clique `+` para criar uma nova consulta. 

(2) Clique duas vezes na `AppServiceConsoleLogs` tabela e `Run` clique. 

Uma vez que a conversão de cinco imagens resulta em erros de servidor, pode ver se a aplicação também está a escrever erros filtrando `ResultDescription` erros, como mostra abaixo:

```kusto
AppServiceConsoleLogs |
where ResultDescription  contains "error" 
```

Na `ResultDescription` coluna, verá o seguinte erro:

```output
PHP Fatal error:  Allowed memory size of 134217728 bytes exhausted 
(tried to allocate 16384 bytes) in /home/site/wwwroot/process.php on line 20, 
referer: http://<app-name>.azurewebsites.net/
```

### <a name="join-appservicehttplogs-and-appserviceconsolelogs"></a>Junte-se a AppServiceHTTPLogs e AppServiceConsoleLogs

Agora que identificou http 500s e erros padrão, tem de confirmar se existe uma correlação entre estas mensagens. Em seguida, junte-se às mesas com base na hora marcada, `TimeGenerated` .

> [!NOTE]
> Foi preparada uma consulta para si que faz o seguinte:
>
> - Filtros HTTPLogs para 500 erros
> - Registos de consola de consultas
> - Junta-se às mesas `TimeGenerated`
>

Execute a seguinte consulta:

```kusto
let myHttp = AppServiceHTTPLogs | where  ScStatus == 500 | project TimeGen=substring(TimeGenerated, 0, 19), CsUriStem, ScStatus;  

let myConsole = AppServiceConsoleLogs | project TimeGen=substring(TimeGenerated, 0, 19), ResultDescription;

myHttp | join myConsole on TimeGen | project TimeGen, CsUriStem, ScStatus, ResultDescription;
```

Na `ResultDescription` coluna, verá o seguinte erro ao mesmo tempo que erros do servidor web:

```output
PHP Fatal error:  Allowed memory size of 134217728 bytes exhausted 
(tried to allocate 16384 bytes) in /home/site/wwwroot/process.php on line 20, 
referer: http://<app-name>.azurewebsites.net/
```

A mensagem diz que a memória foi esgotada na linha 20 de `process.php` . Confirmou agora que a aplicação produziu um erro durante o erro HTTP 500. Vamos ver o código para identificar o problema.

## <a name="identify-the-error"></a>Identificar o erro

No diretório local, abra a `process.php` linha 20. 

```php
imagepng($imgArray[$x], $filename);
```

O primeiro argumento, `$imgArray[$x]` é uma variável que mantém todos os JPGs (na memória) que precisam de conversão. No entanto, `imagepng` só precisa que a imagem seja convertida e não todas as imagens. As imagens de pré-carregamento não são necessárias e podem estar a causar o esgotamento da memória, levando a HTTP 500s. Vamos atualizar o código para carregar imagens a pedido para ver se resolve o problema. Em seguida, irá melhorar o código para resolver o problema da memória.

## <a name="fix-the-app"></a>Corrija a app

### <a name="update-locally-and-redeploy-the-code"></a>Atualizar localmente e reimplementar o código

Esm faça as seguintes alterações `process.php` para lidar com o esgotamento da memória:

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
git push azure main
```

### <a name="browse-to-the-azure-app"></a>Navegue pela app Azure

Navegue para `http://<app-name>.azurewebsites.net`. 

A conversão de imagens não deve continuar a produzir os erros HTTP 500.

![Aplicação PHP em execução no Serviço de Aplicações do Azure](./media/tutorial-azure-monitor/sample-monitor-app-working.png)

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

Eliminar a definição de diagnóstico com o seguinte comando:

```bash
az monitor diagnostic-settings delete --resource $resourceID -n myMonitorLogs
```
O que aprendeu:

> [!div class="checklist"]
> * Configurado uma aplicação web com o Azure Monitor
> * Enviaram registos para Registar Analytics
> * Consultas de registo usadas para identificar e resolver erros de aplicações web

## <a name="next-steps"></a><a name="nextsteps"></a> Próximos passos
* [Registos de consulta com monitor Azure](../azure-monitor/log-query/log-query-overview.md)
* [Serviço de aplicações Azure em Estúdio Visual](troubleshoot-dotnet-visual-studio.md)
* [Analisar registos de aplicativos em HDInsight](https://gallery.technet.microsoft.com/scriptcenter/Analyses-Windows-Azure-web-0b27d413)