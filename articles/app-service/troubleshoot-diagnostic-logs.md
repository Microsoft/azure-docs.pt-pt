---
title: Habilitar o log de diagnóstico para aplicativos-Azure App serviço
description: Saiba como habilitar o log de diagnóstico e adicionar instrumentação ao seu aplicativo, bem como acessar as informações registradas pelo Azure.
services: app-service
documentationcenter: .net
author: cephalin
manager: erikre
editor: jimbe
ms.assetid: c9da27b2-47d4-4c33-a3cb-1819955ee43b
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/06/2016
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: af6d8b61c5d49ae219e90513abb93185f957222e
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70074062"
---
# <a name="enable-diagnostics-logging-for-apps-in-azure-app-service"></a>Habilitar o log de diagnóstico para aplicativos no serviço Azure App
## <a name="overview"></a>Descrição geral
O Azure fornece diagnósticos internos para auxiliar na depuração de um [aplicativo do serviço de aplicativo](https://go.microsoft.com/fwlink/?LinkId=529714). Neste artigo, você aprende a habilitar o log de diagnósticos e a adicionar instrumentação ao seu aplicativo, bem como acessar as informações registradas pelo Azure.

Este artigo usa o [portal do Azure](https://portal.azure.com) e CLI do Azure para trabalhar com logs de diagnóstico. Para obter informações sobre como trabalhar com logs de diagnóstico usando o Visual Studio, consulte [Solucionando problemas do Azure no Visual Studio](troubleshoot-dotnet-visual-studio.md).

## <a name="whatisdiag"></a>Diagnóstico de servidor Web e diagnóstico de aplicativo
O serviço de aplicativo fornece a funcionalidade de diagnóstico para registrar informações do servidor Web e do aplicativo Web. Eles são separados logicamente no **diagnóstico do servidor Web** e no **Application Diagnostics**.

### <a name="web-server-diagnostics"></a>Diagnóstico do servidor Web
Você pode habilitar ou desabilitar os seguintes tipos de logs:

* **Log de erros detalhado** -informações detalhadas para qualquer solicitação que resulte no código de status HTTP 400 ou superior. Ele pode conter informações que podem ajudar a determinar por que o servidor retornou o código de erro. Um arquivo HTML é gerado para cada erro no sistema de arquivos do aplicativo e até 50 erros (arquivos) são mantidos. Quando o número de arquivos HTML exceder 50, os 26 arquivos mais antigos são excluídos automaticamente.
* **Rastreamento de solicitação com falha** -informações detalhadas sobre solicitações com falha, incluindo um rastreamento dos componentes do IIS usados para processar a solicitação e o tempo gasto em cada componente. É útil se você quiser melhorar o desempenho do site ou isolar um erro de HTTP específico. Uma pasta é gerada para cada erro no sistema de arquivos do aplicativo. As políticas de retenção de arquivo são as mesmas do log de erros detalhado acima.
* **Log de servidor Web** -informações sobre transações http usando o [formato de arquivo de log estendido W3C](/windows/desktop/Http/w3c-logging). É útil para determinar as métricas gerais do site, como o número de solicitações tratadas ou quantas solicitações são de um endereço IP específico.

### <a name="application-diagnostics"></a>Application diagnostics
O Application Diagnostics permite que você capture informações produzidas por um aplicativo Web. Os aplicativos ASP.NET podem usar a classe [System. Diagnostics. Trace](/dotnet/api/system.diagnostics.trace) para registrar informações no log do Application Diagnostics. Por exemplo:

    System.Diagnostics.Trace.TraceError("If you're seeing this, something bad happened");

Em tempo de execução, você pode recuperar esses logs para ajudar na solução de problemas. Para obter mais informações, consulte [solução de problemas de serviço de Azure app no Visual Studio](troubleshoot-dotnet-visual-studio.md).

O serviço de aplicativo também registra informações de implantação ao publicar conteúdo em um aplicativo. Ele ocorre automaticamente e não há definições de configuração para o log de implantação. O log de implantação permite que você determine por que a implantação falhou. Por exemplo, se você usar um script de implantação personalizado, poderá usar o log de implantação para determinar por que o script está falhando.

## <a name="enablediag"></a>Como habilitar o diagnóstico
Para habilitar o diagnóstico no [portal do Azure](https://portal.azure.com), vá para a página do seu aplicativo e clique em **configurações > logs de diagnóstico**.

<!-- todo:cleanup dogfood addresses in screenshot -->
![Parte de logs](./media/web-sites-enable-diagnostic-log/logspart.png)

Ao habilitar o **Application Diagnostics**, você também escolhe o **nível**. A tabela a seguir mostra as categorias de logs que cada nível inclui:

| Nível| Categorias de log incluídas |
|-|-|
|**Desativado** | Nenhum |
|**Ao** | Erro, crítico |
|**Alerta** | Aviso, erro, crítico|
|**Divulgação** | Informações, aviso, erro, crítico|
|**Verbose** | Rastreamento, depuração, informações, aviso, erro, crítico (todas as categorias) |
|-|-|

Para o **log do aplicativo**, você pode ativar a opção do sistema de arquivos temporariamente para fins de depuração. Essa opção é desativada automaticamente em 12 horas. Você também pode ativar a opção de armazenamento de BLOBs para selecionar um contêiner de BLOBs no qual os logs são gravados.

> [!NOTE]
> No momento, somente os logs de aplicativos .NET podem ser gravados no armazenamento de BLOBs. Java, PHP, Node. js, os logs de aplicativo do Python só podem ser armazenados no sistema de arquivos (sem modificações de código para gravar logs no armazenamento externo).
>
>

Para o **log do servidor Web**, você pode selecionar **armazenamento** ou **sistema de arquivos**. Selecionar **armazenamento** permite que você selecione uma conta de armazenamento e, em seguida, um contêiner de blob no qual os logs são gravados. 

Se você armazenar logs no sistema de arquivos, os arquivos poderão ser acessados por FTP ou baixados como um arquivo zip usando CLI do Azure.

Por padrão, os logs não são excluídos automaticamente (com exceção de **log de aplicativo (Filesystem)** ). Para excluir logs automaticamente, defina o campo **período de retenção (dias)** .

> [!NOTE]
> Se você [regenerar as chaves de acesso da sua conta de armazenamento](../storage/common/storage-create-storage-account.md), deverá redefinir a respectiva configuração de registro em log para usar as chaves atualizadas. Para efetuar este procedimento:
>
> 1. Na guia **Configurar** , defina o respectivo recurso de registro em log como **desativado**. Salve sua configuração.
> 2. Habilite o log para o blob da conta de armazenamento novamente. Salve sua configuração.
>
>

Qualquer combinação de sistema de arquivos ou armazenamento de BLOBs pode ser habilitada ao mesmo tempo e ter configurações de nível de log individuais. Por exemplo, talvez você queira registrar erros e avisos no armazenamento de BLOBs como uma solução de log de longo prazo, ao mesmo tempo que habilita o log do sistema de arquivos com um nível detalhado.

Embora os dois locais de armazenamento forneçam as mesmas informações básicas para eventos registrados, o **armazenamento** de BLOBs registra informações adicionais, como a ID da instância, a ID do thread e um carimbo de data/hora mais granular do que o registro no **sistema de arquivos**.

> [!NOTE]
> As informações armazenadas no **armazenamento** de BLOBs só podem ser acessadas usando um cliente de armazenamento ou um aplicativo que pode trabalhar diretamente com esses sistemas de armazenamento. Por exemplo, Visual Studio 2013 contém um Gerenciador de Armazenamento que pode ser usado para explorar o armazenamento de BLOBs, e o HDInsight pode acessar dados armazenados no armazenamento de BLOBs. Você também pode escrever um aplicativo que acesse o armazenamento do Azure usando um dos [SDKs do Azure](https://azure.microsoft.com/downloads/).
>

## <a name="download"></a>Como: Registos de transferências
As informações de diagnóstico armazenadas no sistema de arquivos do aplicativo podem ser acessadas diretamente usando o FTP. Ele também pode ser baixado como um arquivo zip usando CLI do Azure.

A estrutura de diretório na qual os logs são armazenados é a seguinte:

* **Logs de aplicativo** -/LogFiles/Application/. Esta pasta contém um ou mais arquivos de texto que contêm informações produzidas pelo log de aplicativo.
* **Rastreamentos de solicitação com falha** -/LogFiles/W3SVC # # # # # # # # #/. Essa pasta contém um arquivo XSL e um ou mais arquivos XML. Certifique-se de baixar o arquivo XSL no mesmo diretório que os arquivos XML, pois o arquivo XSL fornece funcionalidade para formatar e filtrar o conteúdo dos arquivos XML quando exibidos no Internet Explorer.
* **Logs de erro detalhados** -/LogFiles/DetailedErrors/. Esta pasta contém um ou mais arquivos. htm que fornecem informações abrangentes para quaisquer erros de HTTP ocorridos.
* **Logs do servidor Web** -/LogFiles/http/RawLogs. Esta pasta contém um ou mais arquivos de texto formatados usando o [formato de arquivo de log estendido do W3C](/windows/desktop/Http/w3c-logging).
* **Logs de implantação** -/LogFiles/git. Essa pasta contém logs gerados pelos processos de implantação internos usados pelo serviço Azure App, bem como logs para implantações do git. Você também pode encontrar os logs de implantação em D:\home\site\deployments.

### <a name="ftp"></a>FTP

Para abrir uma conexão FTP para o servidor FTP do seu aplicativo, consulte [implantar seu aplicativo no serviço de Azure App usando FTP/s](deploy-ftp.md).

Uma vez conectado ao servidor FTP/S do aplicativo, abra a pasta logfiles, onde os arquivos de log são armazenados.

### <a name="download-with-azure-cli"></a>Baixar com CLI do Azure
Para baixar os arquivos de log usando a interface de linha de comando do Azure, abra um novo prompt de comando, PowerShell, bash ou sessão de terminal e insira o seguinte comando:

    az webapp log download --resource-group resourcegroupname --name appname

Esse comando salva os logs do aplicativo denominado ' AppName ' em um arquivo chamado **webapp_logs. zip** no diretório atual.

> [!NOTE]
> Se você ainda não instalou o CLI do Azure ou não o configurou para usar sua assinatura do Azure, consulte [como usar CLI do Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest).
>
>

## <a name="how-to-view-logs-in-application-insights"></a>Como: Exibir logs no Application Insights
O Visual Studio Application Insights fornece ferramentas para filtrar e pesquisar logs e para correlacionar os logs com solicitações e outros eventos.

1. Adicione o SDK do Application Insights ao seu projeto no Visual Studio.
   * Em Gerenciador de Soluções, clique com o botão direito do mouse em seu projeto e escolha Adicionar Application Insights. A interface orienta você pelas etapas que incluem a criação de um recurso de Application Insights. [Saiba mais](../azure-monitor/app/asp-net.md)
2. Adicione o pacote do ouvinte de rastreamento ao seu projeto.
   * Clique com o botão direito do mouse no projeto e escolha gerenciar pacotes NuGet. Selecione `Microsoft.ApplicationInsights.TraceListener` [saiba mais](../azure-monitor/app/asp-net-trace-logs.md)
3. Carregue seu projeto e execute-o para gerar dados de log.
4. No [portal do Azure](https://portal.azure.com/), navegue até o novo recurso de Application insights e abra a **pesquisa**. Você deve ver os dados de log, juntamente com a solicitação, o uso e outras telemetrias. Algumas telemetrias podem levar alguns minutos para chegar: clique em atualizar. [Saiba mais](../azure-monitor/app/diagnostic-search.md)

[Saiba mais sobre o rastreamento de desempenho com o Application Insights](../azure-monitor/app/azure-web-apps.md)

## <a name="streamlogs"></a>Como: Transmitir registos
Ao desenvolver um aplicativo, geralmente é útil ver informações de log em tempo quase real. Você pode transmitir informações de log para seu ambiente de desenvolvimento usando CLI do Azure.

> [!NOTE]
> Alguns tipos de buffer de log gravam no arquivo de log, o que pode resultar em eventos fora de ordem no fluxo. Por exemplo, uma entrada de log de aplicativo que ocorre quando um usuário visita uma página pode ser exibida no fluxo antes da entrada de log HTTP correspondente para a solicitação de página.
>
> [!NOTE]
> O streaming de log também transmite informações gravadas em qualquer arquivo de texto armazenado na pasta **\\D\\ : Home\\LogFiles** .
>
>

### <a name="streaming-with-azure-cli"></a>Streaming com CLI do Azure
Para transmitir informações de log, abra um novo prompt de comando, PowerShell, bash ou sessão de terminal e insira o seguinte comando:

    az webapp log tail --name appname --resource-group myResourceGroup

Esse comando conecta-se ao aplicativo chamado ' AppName ' e inicia as informações de streaming para a janela, à medida que os eventos de log ocorrem no aplicativo. Todas as informações gravadas em arquivos que terminam em. txt,. log ou. htm que são armazenadas no diretório/LogFiles (d:/Home/LogFiles) são transmitidas para o console local.

Para filtrar eventos específicos, como erros, use o parâmetro **--Filter** . Por exemplo:

    az webapp log tail --name appname --resource-group myResourceGroup --filter Error

Para filtrar tipos de log específicos, como HTTP, use o parâmetro **--path** . Por exemplo:

    az webapp log tail --name appname --resource-group myResourceGroup --path http

> [!NOTE]
> Se você ainda não instalou o CLI do Azure ou não o configurou para usar sua assinatura do Azure, consulte [como usar CLI do Azure](../cli-install-nodejs.md).
>
>

## <a name="understandlogs"></a>Como: Entender os logs de diagnóstico
### <a name="application-diagnostics-logs"></a>Logs do Application Diagnostics
O Application Diagnostics armazena informações em um formato específico para aplicativos .NET, dependendo se você armazena logs no sistema de arquivos ou no armazenamento de BLOBs. 

O conjunto base de dados armazenados é o mesmo em ambos os tipos de armazenamento: a data e a hora em que o evento ocorreu, a ID do processo que produziu o evento, o tipo de evento (informações, aviso, erro) e a mensagem do evento. Usar o sistema de arquivos para armazenamento de log é útil quando você precisa de acesso imediato para solucionar um problema porque os arquivos de log são atualizados quase instantaneamente. O armazenamento de BLOBs é usado para fins de arquivamento porque os arquivos são armazenados em cache e liberados para o contêiner de armazenamento em um agendamento.

**Sistema de Ficheiros**

Cada linha registrada no sistema de arquivos ou recebida usando streaming está no seguinte formato:

    {Date}  PID[{process ID}] {event type/level} {message}

Por exemplo, um evento de erro seria semelhante ao exemplo a seguir:

    2014-01-30T16:36:59  PID[3096] Error       Fatal error on the page!

O registro em log no sistema de arquivos fornece as informações mais básicas dos três métodos disponíveis, fornecendo apenas a hora, a ID do processo, o nível do evento e a mensagem.

**Armazenamento de blobs**

Ao fazer logon no armazenamento de BLOBs, os dados são armazenados em formato CSV (valores separados por vírgula). Campos adicionais são registrados para fornecer informações mais granulares sobre o evento. As propriedades a seguir são usadas para cada linha no CSV:

| Nome da propriedade | Valor/formato |
| --- | --- |
| Date |A data e a hora em que o evento ocorreu |
| Nível |Nível de evento (por exemplo, erro, aviso, informações) |
| ApplicationName |O nome do aplicativo |
| InstanceId |Instância do aplicativo em que o evento ocorreu |
| EventTickCount |A data e a hora em que o evento ocorreu, no formato de tique (maior precisão) |
| EventId |A ID do evento deste evento<p><p>O padrão será 0 se nenhum for especificado |
| Pessoal |ID de Processo |
| Tid |A ID de thread do thread que produziu o evento |
| Message |Mensagem de detalhe do evento |

Os dados armazenados em um blob seriam semelhantes ao exemplo a seguir:

    date,level,applicationName,instanceId,eventTickCount,eventId,pid,tid,message
    2014-01-30T16:36:52,Error,mywebapp,6ee38a,635266966128818593,0,3096,9,An error occurred

> [!NOTE]
> Por ASP.NET Core, o registro em log é realizado usando o provedor [Microsoft. Extensions. Logging. AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) , este provedor deposita arquivos de log adicionais no contêiner de BLOB. Para obter mais informações, consulte [ASP.NET Core log no Azure](/aspnet/core/fundamentals/logging).
>
>

### <a name="failed-request-traces"></a>Rastreamentos de solicitação com falha
Os rastreamentos de solicitação com falha são armazenados em arquivos XML chamados **fr # # # # # #. xml**. Para facilitar a exibição das informações registradas, uma folha de estilos XSL denominada **Freb. xsl** é fornecida no mesmo diretório que os arquivos XML. Se você abrir um dos arquivos XML no Internet Explorer, o Internet Explorer usará a folha de estilos XSL para fornecer uma exibição formatada das informações de rastreamento, semelhante ao exemplo a seguir:

![solicitação com falha exibida no navegador](./media/web-sites-enable-diagnostic-log/tws-failedrequestinbrowser.png)

> [!NOTE]
> Uma maneira fácil de exibir os rastreamentos de solicitação com falha formatado é navegar até a página do aplicativo no Portal. No menu à esquerda, selecione **diagnosticar e resolver problemas**, em seguida, procure **logs de rastreamento de solicitação com falha**, clique no ícone para procurar e exibir o rastreamento desejado.
>

### <a name="detailed-error-logs"></a>Logs de erros detalhados
Os logs de erro detalhados são documentos HTML que fornecem informações mais detalhadas sobre erros de HTTP ocorridos. Como são simplesmente documentos HTML, eles podem ser exibidos usando um navegador da Web.

### <a name="web-server-logs"></a>Registos de servidores Web
Os logs do servidor Web são formatados usando o [formato de arquivo de log estendido do W3C](/windows/desktop/Http/w3c-logging). Essas informações podem ser lidas usando um editor de texto ou analisadas usando utilitários como o [Log Parser](https://go.microsoft.com/fwlink/?LinkId=246619).

> [!NOTE]
> Os logs produzidos pelo serviço de Azure App não dão suporte aos campos **s-computername**, **s-IP**ou **cs-version** .
>
>

## <a name="nextsteps"></a> Passos seguintes
* [Como monitorar Azure App serviço](web-sites-monitor.md)
* [Solução de problemas de serviço de Azure App no Visual Studio](troubleshoot-dotnet-visual-studio.md)
* [Analisar logs de aplicativo no HDInsight](https://gallery.technet.microsoft.com/scriptcenter/Analyses-Windows-Azure-web-0b27d413)
