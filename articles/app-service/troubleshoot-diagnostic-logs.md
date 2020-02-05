---
title: Ativar o registo de diagnósticos
description: Saiba como habilitar o log de diagnóstico e adicionar instrumentação ao seu aplicativo, bem como acessar as informações registradas pelo Azure.
ms.assetid: c9da27b2-47d4-4c33-a3cb-1819955ee43b
ms.topic: article
ms.date: 09/17/2019
ms.custom: seodec18
ms.openlocfilehash: 433f8fa36f17f7cb145261273586a684658acda5
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/04/2020
ms.locfileid: "76985939"
---
# <a name="enable-diagnostics-logging-for-apps-in-azure-app-service"></a>Habilitar o log de diagnóstico para aplicativos no serviço Azure App
## <a name="overview"></a>Visão geral
O Azure fornece diagnósticos internos para auxiliar na depuração de um [aplicativo do serviço de aplicativo](overview.md). Neste artigo, você aprende a habilitar o log de diagnósticos e a adicionar instrumentação ao seu aplicativo, bem como acessar as informações registradas pelo Azure.

Este artigo usa o [portal do Azure](https://portal.azure.com) e CLI do Azure para trabalhar com logs de diagnóstico. Para obter informações sobre como trabalhar com logs de diagnóstico usando o Visual Studio, consulte [Solucionando problemas do Azure no Visual Studio](troubleshoot-dotnet-visual-studio.md).

> [!NOTE]
> Além das instruções de registro neste artigo, há uma nova funcionalidade integrada de registro em log com o monitoramento do Azure. Você encontrará mais informações sobre esse recurso na seção [enviar logs para Azure monitor (versão prévia)](#send-logs-to-azure-monitor-preview) . 
>
>

|Tipo|Plataforma|Localização|Descrição|
|-|-|-|-|
| Registo de aplicação | Windows, Linux | Sistema de arquivos do serviço de aplicativo e/ou BLOBs de armazenamento do Azure | Registra as mensagens geradas pelo código do aplicativo. As mensagens podem ser geradas pela estrutura da Web que você escolher ou do código do aplicativo diretamente usando o padrão de log padrão do seu idioma. Cada mensagem recebe uma das seguintes categorias: **crítico**, **erro**, **aviso**, **informações**, **depuração**e **rastreamento**. Você pode selecionar o quão detalhado deseja que o log seja definindo o nível de severidade ao habilitar o log do aplicativo.|
| Registo de servidor web| Windows | Sistema de arquivos do serviço de aplicativo ou BLOBs de armazenamento do Azure| Dados de solicitação HTTP brutos no [formato de arquivo de log estendido W3C](/windows/desktop/Http/w3c-logging). Cada mensagem de log inclui dados como o método HTTP, o URI de recurso, o IP do cliente, a porta do cliente, o agente do usuário, o código de resposta e assim por diante. |
| Mensagens de erro detalhadas| Windows | Sistema de arquivos do serviço de aplicativo | Cópias das páginas de erro *. htm* que seriam enviadas ao navegador do cliente. Por motivos de segurança, as páginas de erro detalhadas não devem ser enviadas aos clientes em produção, mas o serviço de aplicativo pode salvar a página de erro cada vez que um erro de aplicativo ocorre com código HTTP 400 ou superior. A página pode conter informações que podem ajudar a determinar por que o servidor retorna o código de erro. |
| Rastreio de pedido falhado | Windows | Sistema de arquivos do serviço de aplicativo | Informações detalhadas de rastreamento sobre solicitações com falha, incluindo um rastreamento dos componentes do IIS usados para processar a solicitação e o tempo gasto em cada componente. É útil se você quiser melhorar o desempenho do site ou isolar um erro de HTTP específico. Uma pasta é gerada para cada solicitação com falha, que contém o arquivo de log XML e a folha de estilos XSL com a qual exibir o arquivo de log. |
| Log de implantação | Windows, Linux | Sistema de arquivos do serviço de aplicativo | Registra quando você publica o conteúdo em um aplicativo. O log de implantação ocorre automaticamente e não há configurações configuráveis para o log de implantação. Ele ajuda a determinar por que uma implantação falhou. Por exemplo, se você usar um [script de implantação personalizado](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script), poderá usar o log de implantação para determinar por que o script está falhando. |

> [!NOTE]
> O serviço de aplicativo fornece uma ferramenta de diagnósticos dedicada e interativa para ajudá-lo a solucionar problemas de seu aplicativo. Para obter mais informações, consulte [visão geral do diagnóstico de serviço Azure app](overview-diagnostics.md).
>
> Além disso, você pode usar outros serviços do Azure para aprimorar os recursos de log e monitoramento do seu aplicativo, como [Azure monitor](../azure-monitor/app/azure-web-apps.md).
>

## <a name="enable-application-logging-windows"></a>Habilitar o log de aplicativo (Windows)

Para habilitar o log de aplicativos para aplicativos do Windows no [portal do Azure](https://portal.azure.com), navegue até seu aplicativo e selecione **logs do serviço de aplicativo**.

Selecione **ativado** para o **registro em log do aplicativo (Filesystem)** ou **log de aplicativo (BLOB)** ou ambos. 

A opção **FileSystem** é para fins de depuração temporária e fica desativada em 12 horas. A opção **blob** é para o log de longo prazo e precisa de um contêiner de armazenamento de BLOBs no qual os logs serão gravados.  A opção **blob** também inclui informações adicionais nas mensagens de log, como a ID da instância de VM de origem da mensagem de log (`InstanceId`), ID de thread (`Tid`) e um carimbo de data/hora mais granular ([`EventTickCount`](https://docs.microsoft.com/dotnet/api/system.datetime.ticks)).

> [!NOTE]
> No momento, somente os logs de aplicativos .NET podem ser gravados no armazenamento de BLOBs. Java, PHP, Node. js, os logs de aplicativo do Python só podem ser armazenados no sistema de arquivos do serviço de aplicativo (sem modificações de código para gravar logs no armazenamento externo).
>
> Além disso, se você [regenerar as chaves de acesso da sua conta de armazenamento](../storage/common/storage-create-storage-account.md), deverá redefinir a respectiva configuração de registro em log para usar as chaves de acesso atualizadas. Para efetuar este procedimento:
>
> 1. In the **Configure** tab, set the respective logging feature to **Off**. Save your setting.
> 2. Enable logging to the storage account blob again. Save your setting.
>
>

Select the **Level**, or the level of details to log. The following table shows the log categories included in each level:

| Nível | Included categories |
|-|-|
|**Desativado** | Nenhuma |
|**Error** | Error, Critical |
|**Warning** | Warning, Error, Critical|
|**Information** | Info, Warning, Error, Critical|
|**Verbose** | Trace, Debug, Info, Warning, Error, Critical (all categories) |

When finished, select **Save**.

## <a name="enable-application-logging-linuxcontainer"></a>Enable application logging (Linux/Container)

To enable application logging for Linux apps or custom container apps in the [Azure portal](https://portal.azure.com), navigate to your app and select **App Service logs**.

In **Application logging**, select **File System**.

In **Quota (MB)** , specify the disk quota for the application logs. In **Retention Period (Days)** , set the number of days the logs should be retained.

When finished, select **Save**.

## <a name="enable-web-server-logging"></a>Enable web server logging

To enable web server logging for Windows apps in the [Azure portal](https://portal.azure.com), navigate to your app and select **App Service logs**.

For **Web server logging**, select **Storage** to store logs on blob storage, or **File System** to store logs on the App Service file system. 

In **Retention Period (Days)** , set the number of days the logs should be retained.

> [!NOTE]
> If you [regenerate your storage account's access keys](../storage/common/storage-create-storage-account.md), you must reset the respective logging configuration to use the updated keys. Para efetuar este procedimento:
>
> 1. In the **Configure** tab, set the respective logging feature to **Off**. Save your setting.
> 2. Enable logging to the storage account blob again. Save your setting.
>
>

When finished, select **Save**.

## <a name="log-detailed-errors"></a>Log detailed errors

To save the error page or failed request tracing for Windows apps in the [Azure portal](https://portal.azure.com), navigate to your app and select **App Service logs**.

Under **Detailed Error Logging** or **Failed Request Tracing**, select **On**, then select **Save**.

Both types of logs are stored in the App Service file system. Up to 50 errors (files/folders) are retained. When the number of HTML files exceed 50, the oldest 26 errors are automatically deleted.

## <a name="add-log-messages-in-code"></a>Add log messages in code

In your application code, you use the usual logging facilities to send log messages to the application logs. Por exemplo:

- ASP.NET applications can use the [System.Diagnostics.Trace](/dotnet/api/system.diagnostics.trace) class to log information to the application diagnostics log. Por exemplo:

    ```csharp
    System.Diagnostics.Trace.TraceError("If you're seeing this, something bad happened");
    ```

- By default, ASP.NET Core uses the [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) logging provider. For more information, see [ASP.NET Core logging in Azure](https://docs.microsoft.com/aspnet/core/fundamentals/logging/).

## <a name="stream-logs"></a>Transmitir registos

Before you stream logs in real time, enable the log type that you want. Any information written to files ending in .txt, .log, or .htm that are stored in the */LogFiles* directory (d:/home/logfiles) is streamed by App Service.

> [!NOTE]
> Some types of logging buffer write to the log file, which can result in out of order events in the stream. For example, an application log entry that occurs when a user visits a page may be displayed in the stream before the corresponding HTTP log entry for the page request.
>

### <a name="in-azure-portal"></a>In Azure portal

To stream logs in the [Azure portal](https://portal.azure.com), navigate to your app and select **Log stream**. 

### <a name="in-cloud-shell"></a>In Cloud Shell

To stream logs live in [Cloud Shell](../cloud-shell/overview.md), use the following command:

```azurecli-interactive
az webapp log tail --name appname --resource-group myResourceGroup
```

To filter specific events, such as errors, use the **--Filter** parameter. Por exemplo:

```azurecli-interactive
az webapp log tail --name appname --resource-group myResourceGroup --filter Error
```
To filter specific log types, such as HTTP, use the **--Path** parameter. Por exemplo:

```azurecli-interactive
az webapp log tail --name appname --resource-group myResourceGroup --path http
```

### <a name="in-local-terminal"></a>In local terminal

To stream logs in the local console, [install Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) and [sign in to your account](https://docs.microsoft.com/cli/azure/authenticate-azure-cli). Once signed in, followed the [instructions for Cloud Shell](#in-cloud-shell)

## <a name="access-log-files"></a>Access log files

If you configure the Azure Storage blobs option for a log type, you need a client tool that works with Azure Storage. For more information, see [Azure Storage Client Tools](../storage/common/storage-explorers.md).

For logs stored in the App Service file system, the easiest way is to download the ZIP file in the browser at:

- Linux/container apps: `https://<app-name>.scm.azurewebsites.net/api/logs/docker/zip`
- Windows apps: `https://<app-name>.scm.azurewebsites.net/api/dump`

Para aplicativos do Linux/contêiner, o arquivo ZIP contém logs de saída do console para o host do Docker e o contêiner do Docker. Para um aplicativo expandido, o arquivo ZIP contém um conjunto de logs para cada instância. No sistema de arquivos do serviço de aplicativo, esses arquivos de log são o conteúdo do diretório */Home/LogFiles* .

Para aplicativos do Windows, o arquivo ZIP contém o conteúdo do diretório *D:\Home\LogFiles* no sistema de arquivos do serviço de aplicativo. Ele tem a seguinte estrutura:

| Tipo de log | Diretório | Descrição |
|-|-|-|
| **Logs de aplicativo** |*/LogFiles/Application/* | Contém um ou mais arquivos de texto. O formato das mensagens de log depende do provedor de log que você usa. |
| **Rastreamentos de solicitação com falha** | */LogFiles/W3SVC # # # # # # # # #/* | Contém arquivos XML e um arquivo XSL. Você pode exibir os arquivos XML formatados no navegador. |
| **Logs de erros detalhados** | */LogFiles/DetailedErrors/* | Contém arquivos de erro HTM. Você pode exibir os arquivos HTM no navegador.<br/>Outra maneira de exibir os rastreamentos de solicitação com falha é navegar até a página do aplicativo no Portal. No menu à esquerda, selecione **diagnosticar e resolver problemas**, em seguida, procure **logs de rastreamento de solicitação com falha**, clique no ícone para procurar e exibir o rastreamento desejado. |
| **Logs do servidor Web** | */LogFiles/http/RawLogs/* | Contém arquivos de texto formatados usando o [formato de arquivo de log estendido do W3C](/windows/desktop/Http/w3c-logging). Essas informações podem ser lidas usando um editor de texto ou um utilitário como o [Log Parser](https://go.microsoft.com/fwlink/?LinkId=246619).<br/>O serviço de aplicativo não dá suporte aos campos `s-computername`, `s-ip`ou `cs-version`. |
| **Logs de implantação** | */LogFiles/git/* e */Deployments/* | Conter logs gerados pelos processos de implantação internos, bem como logs para implantações do git. |

## <a name="send-logs-to-azure-monitor-preview"></a>Enviar logs para Azure Monitor (versão prévia)

Com a nova [integração de Azure monitor](https://aka.ms/appsvcblog-azmon), você pode [criar configurações de diagnóstico (versão prévia)](https://azure.github.io/AppService/2019/11/01/App-Service-Integration-with-Azure-Monitor.html#create-a-diagnostic-setting) para enviar logs para contas de armazenamento, Hubs de eventos e log Analytics. 

> [!div class="mx-imgBorder"]
> ![Definições de Diagnóstico (pré-visualização)](media/troubleshoot-diagnostic-logs/diagnostic-settings-page.png)

### <a name="supported-log-types"></a>Tipos de log com suporte

A tabela a seguir mostra os tipos de log e as descrições com suporte: 

| Tipo de log | Suporte do Windows | Suporte para Linux (Docker) | Descrição |
|-|-|-|
| AppServiceConsoleLogs | TBA | Sim | Saída padrão e erro padrão |
| AppServiceHTTPLogs | Sim | Sim | Logs do servidor Web |
| AppServiceEnvironmentPlatformLogs | Sim | Sim | Ambiente do Serviço de Aplicativo: dimensionamento, alterações de configuração e logs de status|
| AppServiceAuditLogs | Sim | Sim | Atividade de logon via FTP e kudu |
| AppServiceFileAuditLogs | TBA | Sim | Alterações de arquivo via FTP e kudu |
| AppServiceAppLogs | TBA | Java SE & Tomcat | Logs de aplicativo |

## <a name="nextsteps"></a> Passos seguintes
* [Logs de consulta com Azure Monitor](../azure-monitor/log-query/log-query-overview.md)
* [Como monitorar Azure App serviço](web-sites-monitor.md)
* [Solução de problemas de serviço de Azure App no Visual Studio](troubleshoot-dotnet-visual-studio.md)
* [Analisar logs de aplicativo no HDInsight](https://gallery.technet.microsoft.com/scriptcenter/Analyses-Windows-Azure-web-0b27d413)
