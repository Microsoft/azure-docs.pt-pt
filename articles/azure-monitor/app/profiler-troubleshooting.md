---
title: Resolver problemas relacionados com o Azure Application Insights Profiler | Documentos da Microsoft
description: Este artigo apresenta os passos de resolução de problemas e informações para ajudar os desenvolvedores que estão a ter problemas ao ativar ou utilizar o Application Insights Profiler.
services: application-insights
documentationcenter: ''
author: cweining
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: mbullwin
ms.date: 08/06/2018
ms.author: cweining
ms.openlocfilehash: 6c96b7139787a3863b3f7a47949d9cdf20cc5021
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57855678"
---
# <a name="troubleshoot-problems-enabling-or-viewing-application-insights-profiler"></a>Resolução de problemas de ativação ou ao visualizar o Application Insights Profiler

## <a id="troubleshooting"></a>Resolução de problemas

### <a name="profiles-are-uploaded-only-if-there-are-requests-to-your-application-while-profiler-is-running"></a>Os perfis são carregados apenas se existirem pedidos para seu aplicativo, enquanto o Profiler está em execução

O Azure Application Insights Profiler recolhe dados de criação de perfis durante dois minutos por hora. Também recolhe dados ao selecionar o **perfil agora** botão no **configurar o Application Insights Profiler** painel. Mas os dados de criação de perfis são carregados apenas quando podem ser anexado a um pedido que aconteceu enquanto o Profiler estava em execução. 

Profiler escreve as mensagens de rastreio e eventos personalizados para o recurso do Application Insights. Pode utilizar estes eventos como Profiler está em execução. Se acha que o Profiler deve estar em execução e capturar rastreios, mas não são apresentados no **desempenho** painel, pode verificar como Profiler está em execução:

1. Procure mensagens de rastreio e eventos personalizados enviados pelo Profiler para o recurso do Application Insights. Pode usar essa cadeia de caracteres de pesquisa para encontrar os dados relevantes:

    ```
    stopprofiler OR startprofiler OR upload OR ServiceProfilerSample
    ```
    A imagem seguinte mostra dois exemplos de pesquisas de dois recursos de IA: 
    
   * À esquerda, a aplicação não está a receber pedidos enquanto o Profiler está em execução. A mensagem explica que o carregamento foi cancelado devido a nenhuma atividade. 

   * No lado direito, Profiler iniciado e enviados eventos personalizados quando este detetado pedidos que aconteceram enquanto o Profiler estava em execução. Se o evento personalizado ServiceProfilerSample for apresentado, significa que o Profiler anexados um rastreio a um pedido e pode ver o rastreio da **desempenho do Application Insights** painel.

     Não se for apresentada nenhum telemetria, Profiler não está em execução. Para resolver problemas, consulte as secções de resolução de problemas para o seu tipo de aplicação específica neste artigo.  

     ![Pesquisar a telemetria do Profiler][profiler-search-telemetry]

1. Se existirem pedidos enquanto o Profiler foi executado, certifique-se de que os pedidos são processados pela parte do aplicativo que tenha o Profiler ativada. Embora, às vezes, aplicativos consistem em vários componentes, o Profiler está ativado para apenas alguns dos componentes. O **configurar o Application Insights Profiler** painel apresenta os componentes que carregou rastreios.

### <a name="other-things-to-check"></a>Outros aspetos a verificar
* Certifique-se de que a aplicação está em execução no .NET Framework 4.6.
* Se a sua aplicação web é uma aplicação ASP.NET Core, tem de executar, pelo menos, ASP.NET Core 2.0.
* Se os dados que está a tentar a ver com mais de duas semanas, experimente a limitar o filtro de tempo e tente novamente. Rastreios são eliminados após sete dias.
* Certifique-se de que uma firewall ou proxies não bloqueou o acesso a https://gateway.azureserviceprofiler.net.

### <a id="double-counting"></a>Contagem de threads paralelos de valor de duplo

Em alguns casos, a métrica de tempo total no Visualizador de pilha é mais do que a duração do pedido.

Esta situação poderá ocorrer quando dois ou mais threads estão associados um pedido e que estão a funcionar em paralelo. Nesse caso, o tempo total de threads é mais do que o tempo decorrido. Um thread pode estar esperando por outra seja concluída. O Visualizador tenta detetar esta situação e omite a espera desinteressante. Ao fazer isso, ele erra por exibir informações em excesso em vez de omitir o que pode ser informações críticas.

Quando vir threads paralelos em seus rastreios, determine quais segmentos estão aguardando para que pode determinar o caminho crítico para o pedido. Normalmente, o thread que rapidamente entra num Estado de espera é simplesmente aguardando outros threads. Concentrar-se a outros threads e ignorar o tempo em que os threads em espera.

### <a name="error-report-in-the-profile-viewer"></a>Relatório de erro no Visualizador de perfil
Submeta um pedido de suporte no portal. Certifique-se de que inclui o ID de correlação da mensagem de erro.

## <a name="troubleshoot-profiler-on-azure-app-service"></a>Resolver problemas de Profiler no serviço de aplicações do Azure
Para Profiler funcione corretamente:
* Seu plano de serviço de aplicações web tem de ser escalão básico ou superior.
* A aplicação web tem de ter ativado do Application Insights.
* A aplicação web tem de ter o **APPINSIGHTS_INSTRUMENTATIONKEY** definição de aplicação configurada com a mesma chave de instrumentação que é utilizada pelo SDK do Application Insights.
* A aplicação web tem de ter o **APPINSIGHTS_PROFILERFEATURE_VERSION** definição de aplicação definida e configurada para 1.0.0.
* A aplicação web tem de ter o **DiagnosticServices_EXTENSION_VERSION** definido de definição de aplicação e o valor definido para ~ 3.
* O **ApplicationInsightsProfiler3** trabalho Web tem de estar em execução. Para verificar o webjob:
   1. Aceda a [Kudu](https://blogs.msdn.microsoft.com/cdndevs/2015/04/01/the-kudu-debug-console-azure-websites-best-kept-secret/).
   1. Na **ferramentas** menu, selecione **Dashboard de WebJobs**.  
      O **WebJobs** painel abre-se. 
   
      ![profiler-webjob]   
   
   1. Para ver os detalhes do webjob, incluindo o log, selecione o **ApplicationInsightsProfiler2** ligação.  
     O **detalhes do trabalho Web contínuo** painel abre-se.

      ![profiler-webjob-log]

Se não pode descobrir por que motivo Profiler não está a funcionar para, pode transferir o registo e enviá-lo para a nossa equipa para obter assistência. 
    
### <a name="manual-installation"></a>Instalação manual

Quando configura o Profiler, as atualizações são efetuadas às definições da aplicação web. Se o seu ambiente exigir, pode aplicar as atualizações manualmente. Por exemplo, poderá que seu aplicativo seja executado num ambiente de aplicações Web para o PowerApps. Para aplicar atualizações manualmente, faça o seguinte:

1. Na **controlo de aplicação Web** painel, abra **definições**.

1. Definir **versão do .NET Framework** ao **v4.6**.

1. Definir **Always On** ao **no**.

1. Adicionar a **APPINSIGHTS_INSTRUMENTATIONKEY** aplicação definição e defina o valor para a mesma chave de instrumentação que é utilizada pelo SDK.

1. Adicionar a **APPINSIGHTS_PROFILERFEATURE_VERSION** definição de aplicação e defina o valor como 1.0.0.

1. Adicionar a **DiagnosticServices_EXTENSION_VERSION** definição de aplicação e defina o valor como ~ 3.

### <a name="too-many-active-profiling-sessions"></a>Demasiadas sessões ativas de criação de perfis

Atualmente, pode ativar Profiler, no máximo, quatro de web do Azure de aplicações e ranhuras de implementação que estão em execução no mesmo plano de serviço. Se tiver mais de quatro aplicações web em execução no plano do serviço de uma aplicação, o Profiler pode gerar um *Microsoft.ServiceProfiler.Exceptions.TooManyETWSessionException*. Profiler é executado em separado para cada aplicação web e tenta iniciar uma sessão de rastreio de eventos para Windows (ETW) para cada aplicação. Mas um número limitado de sessões do ETW pode estar ativo de uma só vez. Se o webjob Profiler reporta demasiadas sessões ativas de criação de perfis, mova algumas aplicações web para um plano de serviço diferentes.

### <a name="deployment-error-directory-not-empty-dhomesitewwwrootappdatajobs"></a>Erro de implementação: Diretório não vazio "D:\\doméstica\\site\\wwwroot\\App_Data\\das tarefas

Se estiver a Reimplementar a aplicação web a um recurso de aplicações Web com o Profiler ativado, poderá ver a mensagem seguinte:

*Diretório não vazio "D:\\doméstica\\site\\wwwroot\\App_Data\\das tarefas*

Este erro ocorre se executar o Web Deploy de scripts ou de pipeline de implementação do Azure DevOps. A solução é adicionar os seguintes parâmetros de implementação adicionais para a tarefa de implementação da Web:

```
-skip:Directory='.*\\App_Data\\jobs\\continuous\\ApplicationInsightsProfiler.*' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs\\continuous$' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs$'  -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data$'
```

Estes parâmetros elimine a pasta que é utilizada pelo Application Insights Profiler e desbloquear o processo de voltar a implementar. Não afetam a instância de Profiler está atualmente em execução.

### <a name="how-do-i-determine-whether-application-insights-profiler-is-running"></a>Como posso determinar se o Application Insights Profiler está em execução?

Profiler é executado como um webjob contínuo na aplicação web. É possível abrir o recurso de aplicação web no [portal do Azure](https://portal.azure.com). Na **WebJobs** painel, verifique o estado dos **ApplicationInsightsProfiler**. Se não estiver em execução, abra **registos** para obter mais informações.

## <a name="troubleshoot-problems-with-profiler-and-azure-diagnostics"></a>Resolver problemas relacionados com o Profiler e o diagnóstico do Azure

  >**Há um bug no Criador de perfil que é fornecido na versão mais recente do WAD para serviços Cloud.** Para usar o criador de perfil com um serviço em nuvem, ela oferece suporte apenas SDK de ia para a versão 2.7.2. Se estiver a utilizar uma versão mais recente do SDK do IA, terá de voltar atrás e 2.7.2 para usar o criador de perfil. Se utilizar o Visual Studio para mudar a versão do SDK de informações da aplicação, poderá receber um erro de redirecionamento de ligação em tempo de execução. Isto acontece porque o "newVersion" no ficheiro Web. config do applicationinsights deve ser definido para "2.7.2.0" depois de fazer downgrade o SDK de IA, mas não é atualizado automaticamente.

Para ver se o Profiler está configurado corretamente ao diagnóstico do Azure, efetue as seguintes três coisas: 
1. Em primeiro lugar, verifique se o conteúdo da configuração do diagnóstico do Azure que é implementado é os esperados. 

1. Em segundo lugar, certifique-se de que o diagnóstico do Azure passa a iKey adequada na linha de comando Profiler. 

1. Em terceiro lugar, verifique o ficheiro de registo do Profiler para ver se Profiler foi executado, mas devolveu um erro. 

Para verificar as definições que foram utilizadas para configurar o diagnóstico do Azure:

1. Inicie sessão para a máquina virtual (VM) e, em seguida, abra o ficheiro de registo nesta localização. (A unidade pode ser c: ou d: e a versão de plug-in podem ser diferentes).

    ```
    c:\logs\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\1.11.3.12\DiagnosticsPlugin.log  
    ```
    ou
    ```
    c:\WindowsAzure\logs\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\1.11.3.12\DiagnosticsPlugin.log
    ```

1. No ficheiro, pode procurar a cadeia de caracteres **WadCfg** para encontrar as definições que foram transmitidas para a VM para configurar o diagnóstico do Azure. Pode verificar se a iKey usada pelo coletor Profiler está correta.

1. Verifique a linha de comando que é utilizada para iniciar o Profiler. Os argumentos que são utilizados para iniciar o Profiler estão no seguinte ficheiro. (A unidade pode ser c: ou d:)

    ```
    D:\ProgramData\ApplicationInsightsProfiler\config.json
    ```

1. Certifique-se de que a iKey na linha de comando Profiler está correta. 

1. Através do caminho encontrado no precedente *config* de ficheiros, verifique o ficheiro de registo do Profiler. Apresenta as informações de depuração que indica as definições que o Profiler está a utilizar. Ele também exibe mensagens de estado e o erro do Profiler.  

    Se o Profiler está em execução enquanto seu aplicativo está recebendo solicitações, é apresentada a mensagem seguinte: *Atividade detetada em iKey*. 

    Quando o rastreio está a ser carregado, é apresentada a seguinte mensagem: *Iniciar o carregamento de rastreio*. 


[profiler-search-telemetry]:./media/profiler-troubleshooting/Profiler-Search-Telemetry.png
[profiler-webjob]:./media/profiler-troubleshooting/Profiler-webjob.png
[profiler-webjob-log]:./media/profiler-troubleshooting/Profiler-webjob-log.png








