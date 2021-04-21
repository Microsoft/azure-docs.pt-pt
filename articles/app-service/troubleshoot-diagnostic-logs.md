---
title: Ativar o registo de diagnósticos
description: Saiba como ativar a registo de diagnóstico e adicionar instrumentação à sua aplicação, bem como como aceder às informações registadas pelo Azure.
ms.assetid: c9da27b2-47d4-4c33-a3cb-1819955ee43b
ms.topic: article
ms.date: 09/17/2019
ms.custom: devx-track-csharp, seodec18
ms.openlocfilehash: ffff4215ddbe3f01da927cb47fb4e06f4946a207
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107833855"
---
# <a name="enable-diagnostics-logging-for-apps-in-azure-app-service"></a>Permitir o início de sessão de diagnóstico de apps no Azure App Service
## <a name="overview"></a>Descrição Geral
A Azure fornece diagnósticos incorporados para ajudar na depuração de uma [aplicação do Serviço de Aplicações.](overview.md) Neste artigo, aprende-se a ativar a gravação de diagnóstico e a adicionar instrumentação à sua aplicação, bem como a aceder às informações registadas pelo Azure.

Este artigo utiliza o [portal Azure](https://portal.azure.com) e o Azure CLI para trabalhar com registos de diagnóstico. Para obter informações sobre o trabalho com registos de diagnóstico utilizando o Visual Studio, consulte [Troubleshooting Azure in Visual Studio](troubleshoot-dotnet-visual-studio.md).

> [!NOTE]
> Além das instruções de registo neste artigo, há uma nova capacidade integrada de registo com monitorização Azure. Encontrará mais sobre esta capacidade na secção Enviar registos para a secção [Azure Monitor (pré-visualização).](#send-logs-to-azure-monitor-preview) 
>
>

|Tipo|Plataforma|Localização|Description|
|-|-|-|-|
| Registo de aplicação | Windows, Linux | Sistema de ficheiros do Serviço de Aplicações e/ou bolhas de armazenamento Azure | Regista mensagens geradas pelo seu código de aplicação. As mensagens podem ser geradas pela estrutura web que escolher, ou pelo seu código de aplicação diretamente utilizando o padrão de registo padrão do seu idioma. A cada mensagem é atribuída uma das seguintes categorias: **Critical,** **Error**, **Warning**, **Info**, **Debug** e **Trace**. Pode selecionar o quão verbose pretende que a sessão seja, definindo o nível de gravidade quando ativa a sessão de registo de aplicações.|
| Início de sessão do servidor web| Windows | Sistema de ficheiros do Serviço de Aplicações ou bolhas de armazenamento Azure| Dados de pedidos de HTTP brutos no [formato de ficheiro de registo estendido W3C](/windows/desktop/Http/w3c-logging). Cada mensagem de registo inclui dados como o método HTTP, recurso URI, COMPUTADOR DO Cliente, porta do cliente, agente do utilizador, código de resposta, e assim por diante. |
| Mensagens de erro detalhadas| Windows | Sistema de ficheiros do Serviço de Aplicações | Cópias das *páginas de* erro .htm que teriam sido enviadas para o navegador cliente. Por razões de segurança, as páginas de erro detalhadas não devem ser enviadas para os clientes em produção, mas o Serviço de Aplicações pode guardar a página de erro sempre que ocorrer um erro de aplicação que tenha o código HTTP 400 ou superior. A página pode conter informações que podem ajudar a determinar por que o servidor devolve o código de erro. |
| Rastreio de pedido falhado | Windows | Sistema de ficheiros do Serviço de Aplicações | Informações detalhadas sobre os pedidos falhados, incluindo um traço dos componentes IIS utilizados para processar o pedido e o tempo de cada componente. É útil se pretender melhorar o desempenho do site ou isolar um erro HTTP específico. Uma pasta é gerada para cada pedido falhado, que contém o ficheiro de registo XML, e a folha de estilo XSL para visualizar o ficheiro de registo com. |
| Registo de implantação | Windows, Linux | Sistema de ficheiros do Serviço de Aplicações | Regista para quando publica conteúdo numa aplicação. O registo de implementação ocorre automaticamente e não existem configurações configuráveis para a verificação da sessão. Ajuda-te a determinar porque é que uma implantação falhou. Por exemplo, se utilizar um [script de implementação personalizado,](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script)poderá utilizar o registo de implementação para determinar por que o script está a falhar. |

> [!NOTE]
> O App Service fornece uma ferramenta de diagnóstico dedicada e interativa para ajudá-lo a resolver problemas com a sua aplicação. Para mais informações, consulte [a visão geral do Serviço de Aplicações Azure](overview-diagnostics.md).
>
> Além disso, pode utilizar outros serviços Azure para melhorar as capacidades de registo e monitorização da sua aplicação, como [o Azure Monitor.](../azure-monitor/app/azure-web-apps.md)
>

## <a name="enable-application-logging-windows"></a>Ativar o registo de aplicações (Windows)

> [!NOTE]
> A sessão de registo de aplicações para armazenamento de bolhas só pode usar contas de armazenamento na mesma região que o Serviço de Aplicações

Para ativar o registo de aplicações para aplicações do Windows no [portal Azure,](https://portal.azure.com)navegue para a sua aplicação e selecione **registos do Serviço de Aplicações.**

Selecione **On** for **Application Logging (Filesystem)** ou **Application Logging (Blob)** ou ambos. 

A opção **Filesystem** destina-se a depurações temporárias e desliga-se em 12 horas. A opção **Blob** é para a exploração madeireira a longo prazo, e precisa de um recipiente de armazenamento de bolhas para escrever registos.  A opção **Blob** também inclui informações adicionais nas mensagens de registo, tais como o ID da origem VM instância da mensagem de registo `InstanceId` (, thread ID ( `Tid` ) e um timetamp mais granular ( [`EventTickCount`](/dotnet/api/system.datetime.ticks) ).

> [!NOTE]
> Atualmente apenas os registos de aplicações .NET podem ser escritos para o armazenamento do blob. Java, PHP, Node.js, os registos de aplicações Python só podem ser armazenados no sistema de ficheiros do Serviço de Aplicações (sem modificações de código para escrever registos para armazenamento externo).
>
> Além disso, se [regenerar as teclas de acesso da sua conta de armazenamento,](../storage/common/storage-account-create.md)tem de redefinir a respetiva configuração de registo para utilizar as teclas de acesso atualizadas. Para efetuar este procedimento:
>
> 1. No **separador Configurar,** desafie a respetiva função de registo para **Desligar**. Guarde a sua configuração.
> 2. Volte a ativar o registo na bolha da conta de armazenamento. Guarde a sua configuração.
>
>

Selecione o **Nível**, ou o nível de detalhes para registar. A tabela a seguir mostra as categorias de registo incluídas em cada nível:

| Level | Categorias incluídas |
|-|-|
|**Desativado** | Nenhum |
|**Erro** | Erro, Crítico |
|**Aviso** | Aviso, Erro, Crítico|
|**Informações** | Informação, Aviso, Erro, Crítico|
|**Verboso** | Trace, Debug, Info, Warning, Error, Critical (todas as categorias) |

Quando terminar, **selecione Save**.

## <a name="enable-application-logging-linuxcontainer"></a>Ativar o registo de aplicações (Linux/Contentor)

Para ativar o registo de aplicações para apps Linux ou aplicações personalizadas de contentores no [portal Azure,](https://portal.azure.com)navegue para a sua aplicação e selecione **registos do Serviço de Aplicações.**

Na **sessão de registo de aplicações**, selecione **o Sistema de Ficheiros**.

Na **Quota (MB),** especifique a quota de disco para os registos de aplicação. No **Período de Retenção (Dias)**, desa um número de dias os registos devem ser mantidos.

Quando terminar, **selecione Save**.

## <a name="enable-web-server-logging"></a>Ativar a sessão de registo do servidor web

Para ativar o registo de servidores web para aplicações do Windows no [portal Azure,](https://portal.azure.com)navegue para a sua aplicação e selecione **registos do Serviço de Aplicações**.

Para **fazer login no servidor Web**, selecione **Armazenamento** para armazenar registos no armazenamento de bolhas ou sistema **de ficheiros** para armazenar registos no sistema de ficheiros do Serviço de Aplicações. 

No **Período de Retenção (Dias)**, desa um número de dias os registos devem ser mantidos.

> [!NOTE]
> Se [regenerar as teclas de acesso da sua conta de armazenamento,](../storage/common/storage-account-create.md)tem de redefinir a respetiva configuração de registo para utilizar as teclas atualizadas. Para efetuar este procedimento:
>
> 1. No **separador Configurar,** desafie a respetiva função de registo para **Desligar**. Guarde a sua configuração.
> 2. Volte a ativar o registo na bolha da conta de armazenamento. Guarde a sua configuração.
>
>

Quando terminar, **selecione Save**.

## <a name="log-detailed-errors"></a>Registar erros detalhados

Para guardar a página de erro ou rastrear o rastreio de pedidos falhados para aplicações do Windows no [portal Azure,](https://portal.azure.com)navegue para a sua aplicação e selecione **registos do Serviço de Aplicações**.

Em **Registo de Erros Detalhado** ou Rastreio de Pedido **Falhado**, selecione **On** e, em seguida, selecione **Guardar**.

Ambos os tipos de registos são armazenados no sistema de ficheiros Do Serviço de Aplicações. São retidos até 50 erros (ficheiros/pastas). Quando o número de ficheiros HTML exceder 50, os 26 erros mais antigos são automaticamente eliminados.

## <a name="add-log-messages-in-code"></a>Adicionar mensagens de registo no código

No seu código de aplicação, utiliza as instalações habituais de registo para enviar mensagens de registo para os registos de aplicações. Por exemplo:

- ASP.NET aplicações podem utilizar a classe [System.Diagnostics.Trace](/dotnet/api/system.diagnostics.trace) para registar informações no registo de diagnósticos da aplicação. Por exemplo:

    ```csharp
    System.Diagnostics.Trace.TraceError("If you're seeing this, something bad happened");
    ```

- Por padrão, ASP.NET Core utiliza o fornecedor de registo [microsoft.Extensions.Logging.AzureAppServices.](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) Para obter mais informações, consulte [ASP.NET Core a registar-se em Azure](/aspnet/core/fundamentals/logging/). Para obter informações sobre a sessão webJobs SDK, consulte [Começar com o Azure WebJobs SDK](./webjobs-sdk-get-started.md#enable-console-logging)

## <a name="stream-logs"></a>Transmitir registos

Antes de transmitir os registos em tempo real, ative o tipo de registo que deseja. Qualquer informação escrita para ficheiros que terminam em .txt, .log ou .htm que sejam armazenados no diretório */LogFiles* (d:/home/logfiles) é transmitido pelo Serviço de Aplicações.

> [!NOTE]
> Alguns tipos de buffer de registo escrevem no ficheiro de registo, o que pode resultar em eventos fora de ordem no fluxo. Por exemplo, um registo de registo de aplicação que ocorre quando um utilizador visita uma página pode ser apresentado no fluxo antes do registo HTTP correspondente para o pedido de página.
>

### <a name="in-azure-portal"></a>No portal Azure

Para transmitir registos no [portal Azure,](https://portal.azure.com)navegue para a sua aplicação e selecione **Log stream**. 

### <a name="in-cloud-shell"></a>Em Cloud Shell

Para transmitir registos em direto na [Cloud Shell,](../cloud-shell/overview.md)utilize o seguinte comando:

> [!IMPORTANT]
> Este comando pode não funcionar com aplicações web hospedadas num plano de serviço de aplicações Linux.

```azurecli-interactive
az webapp log tail --name appname --resource-group myResourceGroup
```

Para filtrar tipos específicos de registo, tais como HTTP, utilize o parâmetro **--Fornecedor.** Por exemplo:

```azurecli-interactive
az webapp log tail --name appname --resource-group myResourceGroup --provider http
```

### <a name="in-local-terminal"></a>No terminal local

Para transmitir registos na consola local, [instale o Azure CLI](/cli/azure/install-azure-cli) e [inicie sessão na sua conta.](/cli/azure/authenticate-azure-cli) Uma vez assinado, seguiu as [instruções para Cloud Shell](#in-cloud-shell)

## <a name="access-log-files"></a>Aceder a ficheiros de registos

Se configurar a opção Azure Storage blobs para um tipo de registo, precisa de uma ferramenta cliente que funcione com o Azure Storage. Para mais informações, consulte [as Ferramentas do Cliente de Armazenamento Azure.](../storage/common/storage-explorers.md)

Para registos armazenados no sistema de ficheiros Do Serviço de Aplicações, a forma mais fácil é descarregar o ficheiro ZIP no navegador em:

- Aplicativos Linux/contentor: `https://<app-name>.scm.azurewebsites.net/api/logs/docker/zip`
- Aplicativos windows: `https://<app-name>.scm.azurewebsites.net/api/dump`

Para aplicações Linux/contentor, o ficheiro ZIP contém registos de saída da consola tanto para o anfitrião do estivador como para o recipiente do estivador. Para uma aplicação de escala, o ficheiro ZIP contém um conjunto de registos para cada instância. No sistema de ficheiros Do Serviço de Aplicações, estes ficheiros de registo são o conteúdo do diretório */home/LogFiles.*

Para aplicações windows, o ficheiro ZIP contém o conteúdo do diretório *D:\Home\LogFiles* no sistema de ficheiros Do Serviço de Aplicações. Tem a seguinte estrutura:

| Tipo de registo | Diretório | Description |
|-|-|-|
| **Registos de aplicações** |*/Registos/Aplicação/* | Contém um ou mais ficheiros de texto. O formato das mensagens de registo depende do fornecedor de registo que utiliza. |
| **Rastreios de pedidos falhados** | */LogFiles/W3SVC###### #* | Contém ficheiros XML e um ficheiro XSL. Pode ver os ficheiros XML formatados no navegador. |
| **Registos de erros detalhados** | */LogFiles/DetalhadosErrors/* | Contém ficheiros de erro HTM. Pode ver os ficheiros HTM no navegador.<br/>Outra forma de ver os vestígios de pedidos falhados é navegar para a sua página de aplicações no portal. A partir do menu esquerdo, selecione **Diagnosticar e resolver problemas,** em seguida, procure por **Registos de Rastreio de Pedidos Falhados**, em seguida, clique no ícone para navegar e ver o traço que deseja. |
| **Registos de servidor web** | */LogFiles/http/RawLogs/* | Contém ficheiros de texto formatados utilizando o [formato de ficheiro de registo estendido W3C](/windows/desktop/Http/w3c-logging). Estas informações podem ser lidas usando um editor de texto ou um utilitário como [Log Parser](https://www.iis.net/downloads/community/2010/04/log-parser-22).<br/>O Serviço de Aplicações não suporta, `s-computername` `s-ip` ou `cs-version` campos. |
| **Registos de implantação** | */LogFiles/Git/* e */deployments/* | Conter registos gerados pelos processos de implementação internos, bem como registos para implementações de Git. |

## <a name="send-logs-to-azure-monitor-preview"></a>Enviar registos para Azure Monitor (pré-visualização)

Com a nova integração do [Azure Monitor,](https://aka.ms/appsvcblog-azmon)pode [criar Definições de Diagnóstico (pré-visualização)](https://azure.github.io/AppService/2019/11/01/App-Service-Integration-with-Azure-Monitor.html#create-a-diagnostic-setting) para enviar registos para Contas de Armazenamento, Centros de Eventos e Analíticos de Registo. 

> [!div class="mx-imgBorder"]
> ![Definições de diagnóstico (pré-visualização)](media/troubleshoot-diagnostic-logs/diagnostic-settings-page.png)

### <a name="supported-log-types"></a>Tipos de registo suportados

A tabela a seguir mostra os tipos e descrições de registos suportados: 

| Tipo de registo | Windows | Recipiente windows | Linux | Recipiente Linux | Description |
|-|-|-|-|-|-|
| AppServiceConsoleLogs | Java SE & Tomcat | Yes | Yes | Yes | Saída padrão e erro padrão |
| AppServiceHTTPLogs | Yes | Yes | Yes | Yes | Registos de servidores web |
| AppServiceEnvironmentPlatformLogs | Yes | N/D | Sim | Yes | Ambiente de Serviço de Aplicações: escala, alterações de configuração e registos de estado|
| AppServiceAuditLogs | Yes | Yes | Yes | Yes | Atividade de login via FTP e Kudu |
| AppServiceFileAuditLogs | Yes | Yes | TBA | TBA | Alterações de ficheiros es feitas no conteúdo do site; **apenas disponível para nível Premium e acima** |
| AppServiceAppLogs | ASP .NET & Java Tomcat <sup>1</sup> | ASP .NET & Java Tomcat <sup>1</sup> | Java SE & Tomcat Imagens Abençoadas <sup>2</sup> | Java SE & Tomcat Imagens Abençoadas <sup>2</sup> | Registos de aplicações |
| AppServiceIPSecAuditLogs  | Yes | Yes | Yes | Yes | Pedidos de Regras de IP |
| AppServicePlatformLogs  | TBA | Yes | Yes | Yes | Troncos de operação de contentores |
| AppServiceAntivirusScanAuditLogs | Yes | Yes | Yes | Yes | [Registos de verificação antivírus](https://azure.github.io/AppService/2020/12/09/AzMon-AppServiceAntivirusScanAuditLogs.html) utilizando o Microsoft Defender; **apenas disponível para nível Premium** | 

<sup>1</sup> Para as aplicações Java Tomcat, adicione "TOMCAT_USE_STARTUP_BAT" às definições da aplicação e defina-a para falso ou 0. Tem de estar na *versão mais recente* do Tomcat e utilizar *java.util.logging*.

<sup>2</sup> Para aplicações Java SE, adicione "$WEBSITE_AZMON_PREVIEW_ENABLED" às definições da aplicação e defina-as para verdadeira ou para 1.

## <a name="next-steps"></a><a name="nextsteps"></a> Próximos passos
* [Registos de consulta com monitor Azure](../azure-monitor/logs/log-query-overview.md)
* [Como monitorizar o Serviço de Aplicações Azure](web-sites-monitor.md)
* [Serviço de aplicações Azure em Estúdio Visual](troubleshoot-dotnet-visual-studio.md)
* [Analisar registos de aplicativos em HDInsight](https://gallery.technet.microsoft.com/scriptcenter/Analyses-Windows-Azure-web-0b27d413)
