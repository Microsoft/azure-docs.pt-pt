---
title: Ativar o registo de diagnóstico de aplicações - serviço de aplicações do Azure
description: Saiba como ativar o registo de diagnósticos e adicionar instrumentação à sua aplicação, bem como aceder às informações registadas pelo Azure.
services: app-service
documentationcenter: .net
author: cephalin
manager: erikre
editor: jimbe
ms.assetid: c9da27b2-47d4-4c33-a3cb-1819955ee43b
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/06/2016
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 023d12764e3dcfcf2f5471cb431528a14fbc1fed
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58339640"
---
# <a name="enable-diagnostics-logging-for-apps-in-azure-app-service"></a>Ativar registo de diagnósticos para aplicações no serviço de aplicações do Azure
## <a name="overview"></a>Descrição geral
O Azure disponibiliza diagnósticos incorporados para ajudar a depurar uma [aplicação de serviço de aplicações](https://go.microsoft.com/fwlink/?LinkId=529714). Neste artigo, saiba como ativar o registo de diagnósticos e adicionar instrumentação à sua aplicação, bem como aceder às informações registadas pelo Azure.

Este artigo utiliza a [portal do Azure](https://portal.azure.com) e CLI do Azure para trabalhar com registos de diagnóstico. Para obter informações sobre como trabalhar com registos de diagnóstico com o Visual Studio, consulte [resolução de problemas do Azure no Visual Studio](troubleshoot-dotnet-visual-studio.md).

## <a name="whatisdiag"></a>Diagnóstico do servidor Web e ao application diagnostics
Serviço de aplicações fornece funcionalidade de diagnóstico para informações de registo do servidor web e a aplicação web. Estes são logicamente separados em **diagnóstico do servidor da web** e **diagnóstico de aplicação**.

### <a name="web-server-diagnostics"></a>Diagnóstico do servidor Web
Pode ativar ou desativar os seguintes tipos de registos:

* **Detalhadas de registo de erro** -informações detalhadas para qualquer solicitação que resulta num código de estado HTTP 400 ou superior. Pode conter informações que podem ajudar a determinar o motivo pelo qual o servidor devolveu o código de erro. Um arquivo HTML é gerado para cada erro no sistema de ficheiros da aplicação e até 50 erros (ficheiros) são retidos. Quando o número de ficheiros HTML exceder 50, ficheiros de 26 mais antigos são automaticamente eliminados.
* **Falha do rastreio de pedido** -informações detalhadas sobre pedidos com falhas, incluindo um rastreamento dos componentes do IIS, usado para processar o pedido e o tempo decorrido em cada componente. É útil se pretender melhorar o desempenho do site ou isolar um erro HTTP específico. Uma pasta é gerada para cada erro de sistema de ficheiros da aplicação. Políticas de retenção de ficheiros são os mesmos que o registo acima de erro detalhado.
* **O registo do servidor da Web** -informações sobre transações de HTTP utilizando o [formato de ficheiro de registo expandido de W3C](https://msdn.microsoft.com/library/windows/desktop/aa814385.aspx). É útil ao determinar geral métrica do site, tais como o número de pedidos processados ou o número de pedidos é de um endereço IP específico.

### <a name="application-diagnostics"></a>Application diagnostics
Diagnóstico de aplicação permite-lhe capturar informações produzidas por um aplicativo web. As aplicações ASP.NET podem utilizar o [Trace](https://msdn.microsoft.com/library/36hhw2t6.aspx) classe para registar informações no registo de diagnóstico de aplicação. Por exemplo:

    System.Diagnostics.Trace.TraceError("If you're seeing this, something bad happened");

No tempo de execução, pode obter estes registos para ajudar a resolver problemas. Para obter mais informações, consulte [resolução de problemas do serviço de aplicações do Azure no Visual Studio](troubleshoot-dotnet-visual-studio.md).

Serviço de aplicações também regista informações de implementação quando publicar conteúdo para uma aplicação. Isso acontece automaticamente e não há nenhuma definição de configuração para o registo de implementação. Registo de implementação permite-lhe determinar o motivo pelo qual uma implementação falhou. Por exemplo, se usar um script de implementação personalizada, poderá utilizar o registo de implementação para determinar por que o script está a falhar.

## <a name="enablediag"></a>Como ativar o diagnóstico
Para ativar o diagnóstico na [portal do Azure](https://portal.azure.com), vá para a página para a sua aplicação e clique em **definições > os registos de diagnóstico**.

<!-- todo:cleanup dogfood addresses in screenshot -->
![Parte de registos](./media/web-sites-enable-diagnostic-log/logspart.png)

Quando ativa **diagnóstico de aplicação**, também é escolher o **nível**. A tabela seguinte mostra as categorias de registos de que cada nível inclui:

| Nível| Categorias de registo incluídos |
|-|-|
|**Desativado** | Nenhuma |
|**Error** | Erro, crítico |
|**Aviso** | Aviso, erro, críticos|
|**Informações** | Informação, aviso, erro, críticos|
|**Verbose** | Rastreio, Debug, Info, aviso, erro, crítico (todas as categorias) |
|-|-|

Para **registo de aplicações**, pode ativar a opção de sistema de ficheiros temporariamente para fins de depuração. Esta opção se desligar automaticamente em 12 horas. Pode também ativar a opção de armazenamento de BLOBs para selecionar um contentor de BLOBs para escrever os registos.

> [!NOTE]
> Atualmente, apenas registos de aplicações de .NET podem ser escritos para o armazenamento de Blobs. Java, PHP, node. js, Python, registos de aplicações só podem ser armazenados no sistema de ficheiros (sem modificações no código para escrever registos no armazenamento externo).
>
>

Para **registo de servidor Web**, pode selecionar **armazenamento** ou **sistema de ficheiros**. Selecionando **armazenamento** permite-lhe selecionar uma conta de armazenamento e, em seguida, um contentor de BLOBs que os registos são escritos. 

Se armazenar os registos no sistema de ficheiros, os ficheiros podem ser acedidos por FTP ou transferiu como um arquivo Zip com a CLI do Azure.

Por predefinição, os registos não são automaticamente eliminados (com exceção do **registo de aplicação (sistema de ficheiros)**). Para eliminar automaticamente os registos, defina o **período de retenção (dias)** campo.

> [!NOTE]
> Se [regenerar chaves de acesso da conta de armazenamento](../storage/common/storage-create-storage-account.md), tem de repor a configuração de registo correspondente para utilizar as chaves atualizadas. Para efetuar este procedimento:
>
> 1. Na **configurar** separador, defina a funcionalidade de registo correspondentes **desativar**. Salve sua configuração.
> 2. Ative o registo novamente para o blob da conta de armazenamento. Salve sua configuração.
>
>

Qualquer combinação de armazenamento de BLOBs ou de sistema de ficheiros pode ser ativada ao mesmo tempo e ter configurações de nível de registo individuais. Por exemplo, convém registar erros e avisos para armazenamento de BLOBs como uma solução de registo de longo prazo, ao ativar o registo do sistema de ficheiros com um nível de verbose.

Embora ambas as localizações de armazenamento fornecem as mesmas informações básicas para eventos registrados, **armazenamento de BLOBs** regista informações adicionais, como o ID de instância, ID de thread e um carimbo mais granular (formato de escala) que o registo para **sistema de ficheiros**.

> [!NOTE]
> Informações armazenadas na **armazenamento de BLOBs** só pode ser acedido através de um cliente de armazenamento ou um aplicativo que pode trabalhar diretamente com estes sistemas de armazenamento. Por exemplo, o Visual Studio 2013 contém um Explorador de armazenamento que podem ser utilizadas para explorar o armazenamento de BLOBs e o HDInsight pode aceder a dados armazenados no armazenamento de Blobs. Também pode escrever uma aplicação que acede ao armazenamento do Azure ao utilizar uma da [SDKs do Azure](https://azure.microsoft.com/downloads/).
>

## <a name="download"></a> Como: Registos de transferências
Informações de diagnóstico armazenadas no sistema de ficheiros de aplicação podem ser acedidas diretamente com o FTP. Também pode ser transferido como um arquivo Zip com a CLI do Azure.

Segue-se a estrutura de diretório que os registos são armazenados em:

* **Registos de aplicações** -/LogFiles/aplicação /. Esta pasta contém um ou mais ficheiros de texto que contém informações produzidas pelo registo de aplicações.
* **Falha de rastreios de pedido** -/ LogFiles/W3SVC # # # /. Esta pasta contém um arquivo XSL e um ou mais arquivos XML. Certifique-se de que baixe o arquivo XSL no mesmo diretório como o ficheiro (s) o XML como o arquivo XSL fornece funcionalidade de formatação e filtrar o conteúdo dos ficheiros pelo XML quando visualizado no Internet Explorer.
* **Registos de erros de detalhadas** -/LogFiles/DetailedErrors /. Esta pasta contém um ou mais arquivos. htm que fornecem informações abrangentes para quaisquer erros HTTP que ocorreram.
* **Os registos do servidor da Web** -/LogFiles/http/RawLogs. Esta pasta contém uma ou mais ficheiros de texto formatado utilizando o [formato de ficheiro de registo expandido de W3C](https://msdn.microsoft.com/library/windows/desktop/aa814385.aspx).
* **Logs de implantação** -/ LogFiles/Git. Esta pasta contém registos gerados pelos processos de implantação interna utilizados pelo serviço de aplicações do Azure, bem como registos para implementações do Git. Também pode encontrar registos de implementação em D:\home\site\deployments.

### <a name="ftp"></a>FTP

Para abrir uma ligação de FTP para o servidor FTP da sua aplicação, consulte [implementar a aplicação no serviço de aplicações do Azure com FTP/S](deploy-ftp.md).

Assim que estiver ligado ao servidor FTP/S da sua aplicação, abra a **LogFiles** pasta, onde são armazenados os ficheiros de registo.

### <a name="download-with-azure-cli"></a>Transferir com a CLI do Azure
Para transferir os ficheiros de registo utilizando a Interface de linha de comandos do Azure, abra uma nova linha de comandos, o PowerShell, o Bash ou a sessão de Terminal e introduza o seguinte comando:

    az webapp log download --resource-group resourcegroupname --name appname

Este comando guarda os registos para a aplicação com o nome "appname" num ficheiro denominado **webapp_logs.zip** no diretório atual.

> [!NOTE]
> Se ainda não instalou a CLI do Azure, ou ainda não configurado para utilizar a sua subscrição do Azure, veja [como utilizar o Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest).
>
>

## <a name="how-to-view-logs-in-application-insights"></a>Como: Ver registos no Application Insights
O Visual Studio Application Insights fornece ferramentas para filtragem e pesquisa de registos e para correlacionar os registos com pedidos e outros eventos.

1. Adicione o Application Insights SDK ao seu projeto no Visual Studio.
   * No Solution Explorer, clique no projeto e escolha adicionar o Application Insights. A interface orienta-o pelos passos que incluem a criação de um recurso do Application Insights. [Saiba mais](../azure-monitor/app/asp-net.md)
2. Adicione o pacote de serviço de escuta de rastreamento para seu projeto.
   * Clique no projeto e selecione gerir pacotes NuGet. Selecione `Microsoft.ApplicationInsights.TraceListener` [Saiba mais](../azure-monitor/app/asp-net-trace-logs.md)
3. Carregue seu projeto e execute-o para gerar dados de registo.
4. Na [portal do Azure](https://portal.azure.com/), navegue para o novo recurso do Application Insights e abra **pesquisa**. Deverá ver os dados de registo, juntamente com o pedido, a utilização e outra telemetria. Alguma telemetria poderá demorar alguns minutos a chegar: clique em Atualizar. [Saiba mais](../azure-monitor/app/diagnostic-search.md)

[Saiba mais sobre o desempenho de controlo com o Application Insights](../azure-monitor/app/azure-web-apps.md)

## <a name="streamlogs"></a> Como: Transmitir registos
Ao desenvolver um aplicativo, geralmente é útil ver informações de registo em tempo quase real. Pode transmitir informações de registo para o seu ambiente de desenvolvimento com a CLI do Azure.

> [!NOTE]
> Alguns tipos de memória intermédia de registo de escrita para o ficheiro de registo, que pode resultar em eventos fora de ordem no fluxo. Por exemplo, uma entrada de registo de aplicação que ocorre quando um usuário visitar uma página pode ser apresentada no fluxo antes da entrada de registo HTTP correspondente para o pedido de página.
>
> [!NOTE]
> Também a transmissão de registos transmite informações gravadas em qualquer arquivo de texto armazenado no **D:\\doméstica\\LogFiles\\**  pasta.
>
>

### <a name="streaming-with-azure-cli"></a>Transmissão em fluxo com a CLI do Azure
Para transmitir informações de registo, abra uma nova linha de comandos, o PowerShell, o Bash ou a sessão de Terminal e introduza o seguinte comando:

    az webapp log tail --name appname --resource-group myResourceGroup

Este comando liga-se para a aplicação com o nome "appname" e começar a transmissão em fluxo de informações na janela à medida que ocorrem eventos de registo na aplicação. Todas as informações de escrita em ficheiros terminados em. txt,. log ou. htm que estão armazenados no diretório /LogFiles (d: / home/logfiles) são transmitidas para a consola local.

Para filtrar eventos específicos, como erros, utilize o **– filtro** parâmetro. Por exemplo:

    az webapp log tail --name appname --resource-group myResourceGroup --filter Error

Para filtrar os tipos de registo específicos, como HTTP, utilize o **– caminho** parâmetro. Por exemplo:

    az webapp log tail --name appname --resource-group myResourceGroup --path http

> [!NOTE]
> Se ainda não instalou a CLI do Azure, ou ainda não configurado para utilizar a sua subscrição do Azure, veja [como utilizar o Azure CLI](../cli-install-nodejs.md).
>
>

## <a name="understandlogs"></a> Como: Compreender os registos de diagnóstico
### <a name="application-diagnostics-logs"></a>Registos de diagnóstico de aplicações
Diagnóstico de aplicação armazena informações num formato específico para aplicações de .NET, dependendo se armazena os registos para o armazenamento de BLOBs ou de sistema de ficheiros. 

O conjunto base de dados armazenados é a mesma em ambos os tipos de armazenamento - a data e hora que ocorreu o evento, o ID de processo que produziu o evento, o tipo de evento (informação, aviso, erro) e a mensagem de evento. Utilizar o sistema de ficheiros para o armazenamento de registo é útil quando precisa ter acesso imediato ao solucionar um problema porque os ficheiros de registo são atualizados quase instantaneamente. Armazenamento de BLOBs é utilizado para fins de arquivamento, porque os ficheiros são colocados em cache e, em seguida, libertados para o contentor de armazenamento com base numa agenda.

**Sistema de Ficheiros**

Cada linha com sessão iniciada no sistema de arquivos ou recebidas usando a transmissão em fluxo é o seguinte formato:

    {Date}  PID[{process ID}] {event type/level} {message}

Por exemplo, um evento de erro seria ter um aspeto semelhante ao exemplo seguinte:

    2014-01-30T16:36:59  PID[3096] Error       Fatal error on the page!

O registo para o sistema de ficheiros fornece as informações mais básicas dos três métodos disponíveis, fornecendo apenas o tempo, ID de processo, nível do evento e mensagem.

**Armazenamento de blobs**

Ao iniciar sessão para o armazenamento de BLOBs, os dados são armazenados no formato de valores separados por vírgulas (CSV). Campos adicionais são registados para fornecer informações mais detalhadas sobre o evento. As seguintes propriedades são usadas para cada linha no CSV:

| Nome da propriedade | Formato do valor |
| --- | --- |
| Date |A data e hora em que ocorreu o evento |
| Nível |Nível do evento (por exemplo, erro, aviso, informações) |
| ApplicationName |O nome da aplicação |
| Id da Instância |Instância da aplicação que ocorreu o evento |
| EventTickCount |A data e hora em que ocorreu o evento, no formato de escala (maior precisão) |
| EventId |O ID do evento deste evento<p><p>Predefinição é 0 se nenhum especificado |
| PID |ID de Processo |
| NIF |O ID de thread do thread que produziu o evento |
| Mensagem |Mensagem de detalhes do evento |

Os dados armazenados num blob teria uma aparência semelhantes ao seguinte exemplo:

    date,level,applicationName,instanceId,eventTickCount,eventId,pid,tid,message
    2014-01-30T16:36:52,Error,mywebapp,6ee38a,635266966128818593,0,3096,9,An error occurred

> [!NOTE]
> Para o ASP.NET Core, o registo é conseguido utilizando o [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) fornecedor este registo adicional do fornecedor depósitos ficheiros para o contentor de Blobs. Para obter mais informações, consulte [ASP.NET Core, iniciar sessão no Azure](/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#logging-in-azure).
>
>

### <a name="failed-request-traces"></a>Falha de rastreios de pedido
Rastreios de pedido falhado são armazenados nos arquivos XML chamados **. XML de # # # fr**. Para que seja mais fácil ver as informações com sessão iniciada, uma folha de estilos XSL com o nome **freb.xsl** é fornecido no mesmo diretório que os arquivos XML. Se abrir um dos arquivos XML no Internet Explorer, o Internet Explorer usa a folha de estilos XSL para fornecer uma apresentação formatada das informações de rastreio, semelhantes ao seguinte exemplo:

![pedidos falhados visualizado no navegador da](./media/web-sites-enable-diagnostic-log/tws-failedrequestinbrowser.png)

> [!NOTE]
> É uma forma fácil de ver os rastreios de pedido falhado formatado navegar para a página da sua aplicação no portal. No menu à esquerda, selecione **diagnosticar e resolver problemas**, em seguida, procure **falha do pedido de rastreio registos**, em seguida, clique no ícone para procurar e ver o rastreio de que pretende.
>

### <a name="detailed-error-logs"></a>Registos de erros detalhados
Registos de erros detalhados são documentos HTML que fornecem informações mais detalhadas sobre os erros HTTP que ocorreram. Uma vez que eles são simplesmente os documentos HTML, eles podem ser exibidos num navegador da web.

### <a name="web-server-logs"></a>Registos de servidores Web
Os registos do servidor web são formatados com o [formato de ficheiro de registo expandido de W3C](https://msdn.microsoft.com/library/windows/desktop/aa814385.aspx). Estas informações podem ser lidos com um editor de texto ou analisado com o uso, como utilitários [Log Parser](https://go.microsoft.com/fwlink/?LinkId=246619).

> [!NOTE]
> Os registos produzidos pelo App Service do Azure não suportam o **s-computername**, **s-ip**, ou **cs-version** campos.
>
>

## <a name="nextsteps"></a> Passos seguintes
* [Como monitorizar o serviço de aplicações do Azure](web-sites-monitor.md)
* [Resolução de problemas do serviço de aplicações do Azure no Visual Studio](troubleshoot-dotnet-visual-studio.md)
* [Analisar registos de aplicações no HDInsight](https://gallery.technet.microsoft.com/scriptcenter/Analyses-Windows-Azure-web-0b27d413)
