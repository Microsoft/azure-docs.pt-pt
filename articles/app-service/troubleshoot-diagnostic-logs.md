---
title: Ativar o registo de diagnósticos
description: Saiba como permitir o registo de diagnóstico e adicionar instrumentação à sua aplicação, bem como como aceder à informação registada pelo Azure.
ms.assetid: c9da27b2-47d4-4c33-a3cb-1819955ee43b
ms.topic: article
ms.date: 09/17/2019
ms.custom: seodec18
ms.openlocfilehash: e945fd77c2615e6f5213a9aa4fc996f0c4d2f3dd
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81769993"
---
# <a name="enable-diagnostics-logging-for-apps-in-azure-app-service"></a>Ativar diagnósticos de login para apps no Serviço de Aplicações Azure
## <a name="overview"></a>Descrição geral
O Azure fornece diagnósticos incorporados para ajudar a depurar uma aplicação de Serviço de [Aplicações.](overview.md) Neste artigo, aprende-se a permitir o registo de diagnóstico e a adicionar instrumentação à sua aplicação, bem como como aceder à informação registada pelo Azure.

Este artigo utiliza o [portal Azure](https://portal.azure.com) e o Azure CLI para trabalhar com registos de diagnóstico. Para obter informações sobre o trabalho com registos de diagnóstico utilizando o Visual Studio, consulte [Troubleshooting Azure em Visual Studio](troubleshoot-dotnet-visual-studio.md).

> [!NOTE]
> Além das instruções de registo neste artigo, há uma nova capacidade de exploração madeireira integrada com a Monitorização Azure. Você encontrará mais sobre esta capacidade na secção [Enviar para o Monitor Azure (pré-visualização).](#send-logs-to-azure-monitor-preview) 
>
>

|Tipo|Plataforma|Localização|Descrição|
|-|-|-|-|
| Registo de aplicação | Windows, Linux | Sistema de ficheiros do Serviço de Aplicações e/ou blobs de armazenamento Azure | Regista mensagens geradas pelo seu código de aplicação. As mensagens podem ser geradas pelo quadro web que escolher, ou pelo seu código de aplicação diretamente utilizando o padrão padrão de registo da sua língua. Cada mensagem é atribuída uma das seguintes categorias: **Crítico,** **Erro,** **Aviso,** **Informação,** **Depuração**e **Rastreio**. Pode selecionar a verbosa que pretende que a exploração madeireira seja definindo o nível de gravidade quando ativa o registo da aplicação.|
| Registo de servidor web| Windows | Sistema de ficheiros do Serviço de Aplicações ou blobs de armazenamento Azure| Raw HTTP solicita mandam dados no [formato de ficheiro de registo alargado W3C](/windows/desktop/Http/w3c-logging). Cada mensagem de registo inclui dados como o método HTTP, o recurso URI, o IP do cliente, a porta cliente, o agente utilizador, o código de resposta, e assim por diante. |
| Mensagens de erro detalhadas| Windows | Sistema de ficheiros do Serviço de Aplicações | Cópias das páginas de erro *.htm* que teriam sido enviadas para o navegador cliente. Por razões de segurança, as páginas de erro detalhadas não devem ser enviadas aos clientes em produção, mas o Serviço de Aplicações pode guardar a página de erro sempre que ocorrer um erro de aplicação que tenha código HTTP 400 ou superior. A página pode conter informações que podem ajudar a determinar por que o servidor devolve o código de erro. |
| Rastreio de pedido falhado | Windows | Sistema de ficheiros do Serviço de Aplicações | Informações detalhadas sobre pedidos falhados, incluindo um vestígio dos componentes IIS utilizados para processar o pedido e o tempo decorrido em cada componente. É útil se quiser melhorar o desempenho do site ou isolar um erro HTTP específico. Uma pasta é gerada para cada pedido falhado, que contém o ficheiro de registo XML, e a folha de estilo XSL para visualizar o ficheiro de registo com. |
| Exploração madeireira de implantação | Windows, Linux | Sistema de ficheiros do Serviço de Aplicações | Faça logs para quando publica conteúdo numa aplicação. O registo de implantação ocorre automaticamente e não existem configurações configuráveis para o registo de implantação. Ajuda-te a determinar porque é que um destacamento falhou. Por exemplo, se utilizar um script de [implementação personalizado,](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script)poderá utilizar o registo de implementação para determinar porque é que o script está a falhar. |

> [!NOTE]
> O Serviço de Aplicações fornece uma ferramenta dedicada de diagnóstico interativa para ajudá-lo a resolver problemas com a sua aplicação. Para mais informações, consulte a visão geral dos diagnósticos do Serviço de [Aplicações Azure](overview-diagnostics.md).
>
> Além disso, pode utilizar outros serviços Azure para melhorar as capacidades de registo e monitorização da sua aplicação, como o [Azure Monitor](../azure-monitor/app/azure-web-apps.md).
>

## <a name="enable-application-logging-windows"></a>Ativar o registo de aplicações (Windows)

> [!NOTE]
> O registo de aplicações para armazenamento de bolhas só pode utilizar contas de armazenamento na mesma região que o Serviço de Aplicações

Para permitir o registo de aplicações para aplicações windows no [portal Azure,](https://portal.azure.com)navegue para a sua aplicação e selecione registos do **Serviço de Aplicações.**

Selecione **para** qualquer **registo de aplicações (Sistema de Ficheiros)** ou Registo de **Aplicações (Blob)**, ou ambos. 

A opção **Filesystem** é para fins de depuração temporária, e desliga-se em 12 horas. A opção **Blob** é para a exploração a longo prazo, e precisa de um recipiente de armazenamento de bolhas para escrever troncos.  A opção **Blob** também inclui informações adicionais nas mensagens de registo,`InstanceId`tais como`Tid`a identificação da instância[`EventTickCount`](https://docs.microsoft.com/dotnet/api/system.datetime.ticks)VM de origem da mensagem de log ( ), id de linha ( ) e um carimbo de tempo mais granular ().

> [!NOTE]
> Atualmente, apenas os registos de aplicações .NET podem ser escritos no armazenamento de blob. Java, PHP, Node.js, os registos de aplicações Python só podem ser armazenados no sistema de ficheiros do Serviço de Aplicações (sem modificações de código para escrever registos para armazenamento externo).
>
> Além disso, se [regenerar as chaves](../storage/common/storage-create-storage-account.md)de acesso da sua conta de armazenamento, tem de redefinir a respetiva configuração de registo para utilizar as teclas de acesso atualizadas. Para efetuar este procedimento:
>
> 1. No separador **Configurar,** desloque a respetiva função de registo para **desligar**. Guarde a sua configuração.
> 2. Ative o registo na bolha da conta de armazenamento novamente. Guarde a sua configuração.
>
>

Selecione o **Nível**, ou o nível de detalhes para registar. O quadro seguinte mostra as categorias de registo incluídas em cada nível:

| Nível | Categorias incluídas |
|-|-|
|**Desativado** | Nenhuma |
|**Erro** | Erro, Crítico |
|**Aviso** | Aviso, Erro, Crítico|
|**Informações** | Informação, Aviso, Erro, Crítico|
|**Verboso** | Trace, Debug, Info, Warning, Error, Critical (todas as categorias) |

Quando terminar, selecione **Guardar**.

## <a name="enable-application-logging-linuxcontainer"></a>Ativar o registo de aplicações (Linux/Contentor)

Para permitir o registo de aplicações para aplicações Linux ou aplicações de contentores personalizadas no [portal Azure,](https://portal.azure.com)navegue para a sua aplicação e selecione registos do **Serviço de Aplicações**.

No registo de registo de **aplicações,** selecione **Sistema de Ficheiros**.

No **Contingente (MB)**, especifique a quota do disco para os registos de aplicação. No período de **retenção (Dias)**, detete o número de dias em que os registos devem ser conservados.

Quando terminar, selecione **Guardar**.

## <a name="enable-web-server-logging"></a>Ativar o registo do servidor web

Para permitir o registo de servidores web para aplicações windows no [portal Azure,](https://portal.azure.com)navegue para a sua aplicação e selecione registos do **Serviço de Aplicações**.

Para **o registo do servidor Web,** selecione **Storage** para armazenar registos no armazenamento de blob ou sistema **de ficheiros** para armazenar registos no sistema de ficheiros do Serviço de Aplicações. 

No período de **retenção (Dias)**, detete o número de dias em que os registos devem ser conservados.

> [!NOTE]
> Se [regenerar as chaves](../storage/common/storage-create-storage-account.md)de acesso da sua conta de armazenamento, tem de redefinir a respetiva configuração de registo para utilizar as teclas atualizadas. Para efetuar este procedimento:
>
> 1. No separador **Configurar,** desloque a respetiva função de registo para **desligar**. Guarde a sua configuração.
> 2. Ative o registo na bolha da conta de armazenamento novamente. Guarde a sua configuração.
>
>

Quando terminar, selecione **Guardar**.

## <a name="log-detailed-errors"></a>Registar erros detalhados

Para guardar a página de erro ou o pedido falhado que rastreia as aplicações do Windows no [portal Azure,](https://portal.azure.com)navegue para a sua aplicação e selecione registos do Serviço de **Aplicações**.

Em registo **de erro detalhado ou** rastreio de pedido **falhado,** selecione, e depois selecione **Save**. **On**

Ambos os tipos de registos são armazenados no sistema de ficheiros do Serviço de Aplicações. São retidos até 50 erros (ficheiros/pastas). Quando o número de ficheiros HTML ultrapassa os 50, os 26 erros mais antigos são automaticamente eliminados.

## <a name="add-log-messages-in-code"></a>Adicione mensagens de registo no código

No seu código de aplicação, utiliza as instalações habituais de registo para enviar mensagens de registo para os registos de aplicação. Por exemplo:

- ASP.NET aplicações podem utilizar o [Sistema.Diagnostics.Trace](/dotnet/api/system.diagnostics.trace) class para registar informações no registo de diagnósticos da aplicação. Por exemplo:

    ```csharp
    System.Diagnostics.Trace.TraceError("If you're seeing this, something bad happened");
    ```

- Por predefinição, ASP.NET Core utiliza o fornecedor de registo [microsoft.Extensions.Logging.AzureAppServices.](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) Para mais informações, consulte [ASP.NET Core logging em Azure](https://docs.microsoft.com/aspnet/core/fundamentals/logging/).

## <a name="stream-logs"></a>Transmitir registos

Antes de fazer o streaming de registos em tempo real, ative o tipo de registo que deseja. Qualquer informação escrita para ficheiros que terminem em .txt, .log ou .htm que sejam armazenadas no diretório */LogFiles* (d:/home/logfiles) é transmitida pelo Serviço de Aplicações.

> [!NOTE]
> Alguns tipos de tampão de registo escrevem para o ficheiro de registo, o que pode resultar em eventos fora de ordem no fluxo. Por exemplo, uma entrada de registo de aplicação que ocorra quando um utilizador visita uma página pode ser exibida no fluxo antes da entrada correspondente de registo HTTP para o pedido da página.
>

### <a name="in-azure-portal"></a>No portal Azure

Para transmitir registos no [portal Azure,](https://portal.azure.com)navegue para a sua aplicação e selecione **log stream**. 

### <a name="in-cloud-shell"></a>Na nuvem shell

Para transmitir registos ao vivo na [Cloud Shell,](../cloud-shell/overview.md)utilize o seguinte comando:

```azurecli-interactive
az webapp log tail --name appname --resource-group myResourceGroup
```

Para filtrar eventos específicos, tais como erros, utilize o parâmetro **--Filtro.** Por exemplo:

```azurecli-interactive
az webapp log tail --name appname --resource-group myResourceGroup --filter Error
```
Para filtrar tipos de registo específicos, como http, utilize o parâmetro **--Caminho.** Por exemplo:

```azurecli-interactive
az webapp log tail --name appname --resource-group myResourceGroup --path http
```

### <a name="in-local-terminal"></a>No terminal local

Para transmitir registos na consola local, [instale o Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) e [inicie sessão na sua conta](https://docs.microsoft.com/cli/azure/authenticate-azure-cli). Uma vez assinado, seguiu as [instruções para Cloud Shell](#in-cloud-shell)

## <a name="access-log-files"></a>Aceder a ficheiros de registo

Se configurar a opção de blobs de armazenamento Azure para um tipo de log, precisa de uma ferramenta cliente que funcione com o Armazenamento Azure. Para mais informações, consulte ferramentas de cliente de [armazenamento azure](../storage/common/storage-explorers.md).

Para registos armazenados no sistema de ficheiros do Serviço de Aplicações, a forma mais fácil é descarregar o ficheiro ZIP no navegador em:

- Aplicativos Linux/contentores:`https://<app-name>.scm.azurewebsites.net/api/logs/docker/zip`
- Aplicativos windows:`https://<app-name>.scm.azurewebsites.net/api/dump`

Para aplicações Linux/contentor, o ficheiro ZIP contém registos de saída de consolas tanto para o hospedeiro do estivador como para o recipiente de estivador. Para uma aplicação com escala, o ficheiro ZIP contém um conjunto de registos para cada instância. No sistema de ficheiros do Serviço de Aplicações, estes ficheiros de registo são o conteúdo do diretório */home/LogFiles.*

Para aplicações Windows, o ficheiro ZIP contém o conteúdo do diretório *D:\Home\LogFiles* no sistema de ficheiros do Serviço de Aplicações. Tem a seguinte estrutura:

| Tipo de log | Diretório | Descrição |
|-|-|-|
| **Registos de aplicação** |*/Ficheiros de Registo/Aplicação/* | Contém um ou mais ficheiros de texto. O formato das mensagens de registo depende do fornecedor de registo que utiliza. |
| **Vestígios de pedido falhados** | */LogFiles/W3SVC##########* | Contém ficheiros XML e um ficheiro XSL. Pode ver os ficheiros XML formatados no navegador. |
| **Registos de erros detalhados** | */Ficheiros de Registo/Erros Detalhados/* | Contém ficheiros de erro HTM. Pode ver os ficheiros HTM no navegador.<br/>Outra forma de ver os vestígios de pedido falhados é navegar para a página da sua aplicação no portal. A partir do menu esquerdo, selecione **Diagnosticar e resolver problemas,** em seguida, procurar registos de rastreio de rastreio de **pedidos falhados,** em seguida, clique no ícone para navegar e ver o traço que deseja. |
| **Registos de servidores web** | */LogFiles/http/RawLogs/* | Contém ficheiros de texto formatados utilizando o formato de ficheiro de [registo alargado W3C](/windows/desktop/Http/w3c-logging). Estas informações podem ser lidas com um editor de texto ou um utilitário como [o Log Parser](https://go.microsoft.com/fwlink/?LinkId=246619).<br/>O Serviço de Aplicações `s-ip`não `cs-version` suporta os `s-computername`campos ou campos. |
| **Registos de implantação** | */LogFiles/Git/* e */implementações/* | Contenha registos gerados pelos processos internos de implantação, bem como registos para implementações git. |

## <a name="send-logs-to-azure-monitor-preview"></a>Envie registos para o Monitor Azure (pré-visualização)

Com a nova integração do [Monitor Azure,](https://aka.ms/appsvcblog-azmon)pode criar Definições de [Diagnóstico (pré-visualização)](https://azure.github.io/AppService/2019/11/01/App-Service-Integration-with-Azure-Monitor.html#create-a-diagnostic-setting) para enviar registos para Contas de Armazenamento, Centros de Eventos e Log Analytics. 

> [!div class="mx-imgBorder"]
> ![Definições de diagnóstico (pré-visualização)](media/troubleshoot-diagnostic-logs/diagnostic-settings-page.png)

### <a name="supported-log-types"></a>Tipos de registo suportados

A tabela que se segue mostra os tipos e descrições suportadas: 

| Tipo de log | Suporte para janelas | Suporte linux (Docker) | Descrição |
|-|-|-|
| AppServiceConsoleLogs | TBA | Sim | Saída padrão e erro padrão |
| AppServiceHTTPLogs | Sim | Sim | Registos de servidores web |
| AppServiceEnvironmentPlatformLogs | Sim | Sim | Ambiente de Serviço de Aplicações: escala, alterações de configuração e registos de estado|
| AppServiceAuditLogs | Sim | Sim | Atividade de login via FTP e Kudu |
| AppServiceFileAuditLogs | Sim | TBD | Alterações de ficheiros via FTP e Kudu |
| AppServiceAppLogs | TBA | Java SE & Tomcat | Registos de aplicação |

## <a name="next-steps"></a><a name="nextsteps"></a>Próximos passos
* [Registos de consulta com monitor Azure](../azure-monitor/log-query/log-query-overview.md)
* [Como monitorizar o Serviço de Aplicações Azure](web-sites-monitor.md)
* [Troubleshooting Azure App Service em Estúdio Visual](troubleshoot-dotnet-visual-studio.md)
* [Analisar registos de aplicativos no HDInsight](https://gallery.technet.microsoft.com/scriptcenter/Analyses-Windows-Azure-web-0b27d413)
