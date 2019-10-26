---
title: Solucionar problemas com o Aplicativo Azure insights Profiler | Microsoft Docs
description: Este artigo apresenta as etapas e as informações de solução de problemas para ajudar os desenvolvedores que estão tendo problemas para habilitar ou usar Application Insights Profiler.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 7430f04846a1e66680f85f939854fd50a5df41e4
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72899979"
---
# <a name="troubleshoot-problems-enabling-or-viewing-application-insights-profiler"></a>Solucionar problemas de habilitação ou exibição de Application Insights Profiler

## <a id="troubleshooting"></a>Solução de problemas gerais

### <a name="profiles-are-uploaded-only-if-there-are-requests-to-your-application-while-profiler-is-running"></a>Os perfis são carregados somente se houver solicitações para seu aplicativo enquanto o profiler estiver em execução

Aplicativo Azure o profiler do insights coleta dados de criação de perfil por dois minutos a cada hora. Ele também coleta dados quando você seleciona o botão **perfil agora** no painel **Configurar Application insights Profiler** . Mas os dados de criação de perfil são carregados somente quando podem ser anexados a uma solicitação que ocorreu enquanto o criador de perfil estava em execução. 

O criador de perfil grava mensagens de rastreamento e eventos personalizados em seu Application Insights recurso. Você pode usar esses eventos para ver como o profiler está em execução. Se você acredita que o criador de perfil deve estar executando e capturando rastreamentos, mas eles não são exibidos no painel de **desempenho** , você pode verificar para ver como o profiler está em execução:

1. Pesquise mensagens de rastreamento e eventos personalizados enviados pelo Profiler para o recurso de Application Insights. Você pode usar essa cadeia de caracteres de pesquisa para localizar os dados relevantes:

    ```
    stopprofiler OR startprofiler OR upload OR ServiceProfilerSample
    ```
    A imagem a seguir exibe dois exemplos de pesquisas de dois recursos de ia: 
    
   * À esquerda, o aplicativo não está recebendo solicitações enquanto o profiler está em execução. A mensagem explica que o carregamento foi cancelado devido a nenhuma atividade. 

   * À direita, o criador de perfil iniciou e enviou eventos personalizados quando detectou solicitações que ocorreram enquanto o profiler estava em execução. Se o evento personalizado ServiceProfilerSample for exibido, isso significará que o criador de perfil anexou um rastreamento a uma solicitação e você poderá exibir o rastreamento no painel de **desempenho Application insights** .

     Se nenhuma telemetria for exibida, o criador de perfil não estará em execução. Para solucionar problemas, consulte as seções de solução de problemas para seu tipo de aplicativo específico mais adiante neste artigo.  

     ![Pesquisar telemetria do criador de perfil][profiler-search-telemetry]

1. Se houver solicitações enquanto o criador de perfil foi executado, verifique se as solicitações são tratadas pela parte do aplicativo que tem o criador de perfil habilitado. Embora os aplicativos às vezes consistem em vários componentes, o profiler é habilitado somente para alguns dos componentes. O painel **configurar Application insights Profiler** exibe os componentes que carregaram rastreamentos.

### <a name="other-things-to-check"></a>Outros itens a serem verificados
* Verifique se seu aplicativo está em execução no .NET Framework 4,6.
* Se seu aplicativo Web for um aplicativo ASP.NET Core, ele deverá estar executando pelo menos ASP.NET Core 2,0.
* Se os dados que você está tentando exibir forem mais antigos do que duas semanas, tente limitar o filtro de tempo e tente novamente. Os rastreamentos são excluídos após sete dias.
* Verifique se os proxies ou um firewall não bloqueou o acesso ao https://gateway.azureserviceprofiler.net.

### <a id="double-counting"></a>Contagem dupla em threads paralelos

Em alguns casos, a métrica de tempo total no Visualizador de pilha é maior que a duração da solicitação.

Essa situação pode ocorrer quando dois ou mais threads estão associados a uma solicitação e estão operando em paralelo. Nesse caso, o tempo total do thread é maior que o tempo decorrido. Um thread pode estar aguardando o outro para ser concluído. O visualizador tenta detectar essa situação e omite a espera não interessante. Ao fazer isso, erra por no lado da exibição de muitas informações em vez de omitir o que pode ser informações críticas.

Quando você vir threads paralelos em seus rastreamentos, determine quais threads estão aguardando para que você possa verificar o caminho crítico para a solicitação. Normalmente, o thread que entra rapidamente em um estado de espera está simplesmente aguardando os outros threads. Concentre-se nos outros threads e ignore o tempo nos threads em espera.

### <a name="error-report-in-the-profile-viewer"></a>Relatório de erros no Visualizador de perfil
Envie um tíquete de suporte no Portal. Certifique-se de incluir a ID de correlação da mensagem de erro.

## <a name="troubleshoot-profiler-on-azure-app-service"></a>Solucionar problemas do profiler no serviço de Azure App
Para que o profiler funcione corretamente:
* O plano do serviço de aplicativo Web deve ser uma camada básica ou superior.
* Seu aplicativo Web deve ter Application Insights habilitado.
* Seu aplicativo Web deve ter as seguintes configurações de aplicativo:

    |Definição da Aplicação    | Valor    |
    |---------------|----------|
    |APPINSIGHTS_INSTRUMENTATIONKEY         | iKey para o recurso de Application Insights    |
    |APPINSIGHTS_PROFILERFEATURE_VERSION | 1.0.0 |
    |DiagnosticServices_EXTENSION_VERSION | ~ 3 |


* O trabalho Web do **ApplicationInsightsProfiler3** deve estar em execução. Para verificar o webjob:
   1. Vá para [kudu](https://blogs.msdn.microsoft.com/cdndevs/2015/04/01/the-kudu-debug-console-azure-websites-best-kept-secret/).
   1. No menu **ferramentas** , selecione **painel trabalhos**Web.  
      O painel **trabalhos** Web é aberto. 
   
      ![criador de perfil – webjob]   
   
   1. Para exibir os detalhes do webjob, incluindo o log, selecione o link **ApplicationInsightsProfiler3** .  
     O painel de **detalhes do WebJob contínuo** é aberto.

      ![criador de perfil-webjob-log]

Se você não conseguir descobrir por que o profiler não está funcionando para você, poderá baixar o log e enviá-lo para nossa equipe para obter assistência, serviceprofilerhelp@microsoft.com. 
    
### <a name="manual-installation"></a>Instalação manual

Quando você configura o Profiler, as atualizações são feitas nas configurações do aplicativo Web. Se o seu ambiente exigir, você poderá aplicar as atualizações manualmente. Um exemplo pode ser que seu aplicativo esteja sendo executado em um ambiente de aplicativos Web para o PowerApps. Para aplicar atualizações manualmente:

1. No painel de **controle do aplicativo Web** , abra **configurações**.

1. Defina **.NET Framework versão** como **v 4.6**.

1. Defina **Always on** como **ativado**.
1. Crie estas configurações de aplicativo:

    |Definição da Aplicação    | Valor    |
    |---------------|----------|
    |APPINSIGHTS_INSTRUMENTATIONKEY         | iKey para o recurso de Application Insights    |
    |APPINSIGHTS_PROFILERFEATURE_VERSION | 1.0.0 |
    |DiagnosticServices_EXTENSION_VERSION | ~ 3 |

### <a name="too-many-active-profiling-sessions"></a>Muitas sessões de criação de perfil ativas

No momento, você pode habilitar o Profiler em um máximo de quatro aplicativos Web do Azure e slots de implantação em execução no mesmo plano de serviço. Se você tiver mais de quatro aplicativos Web em execução em um plano do serviço de aplicativo, o profiler poderá gerar um *Microsoft. Service Profile. Exceptions. TooManyETWSessionException*. O criador de perfil é executado separadamente para cada aplicativo Web e tenta iniciar uma sessão ETW (rastreamento de eventos para Windows) para cada aplicativo. Mas um número limitado de sessões de ETW pode estar ativo ao mesmo tempo. Se o webjob do criador de perfil relatar muitas sessões de criação de perfil ativas, mova alguns aplicativos Web para um plano de serviço diferente.

### <a name="deployment-error-directory-not-empty-dhomesitewwwrootapp_datajobs"></a>Erro de implantação: diretório não vazio:\\Home\\site\\wwwroot\\App_Data\\Jobs '

Se você estiver reimplantando seu aplicativo Web em um recurso de aplicativos Web com o criador de perfil habilitado, você poderá ver a seguinte mensagem:

*Diretório não vazio:\\Home\\site\\wwwroot\\App_Data\\Jobs '*

Esse erro ocorrerá se você executar Implantação da Web de scripts ou do pipeline de implantação do Azure DevOps. A solução é adicionar os seguintes parâmetros de implantação adicionais à tarefa de Implantação da Web:

```
-skip:Directory='.*\\App_Data\\jobs\\continuous\\ApplicationInsightsProfiler.*' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs\\continuous$' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs$'  -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data$'
```

Esses parâmetros excluem a pasta usada pelo Application Insights Profiler e desbloqueiam o processo de reimplantação. Eles não afetam a instância do criador de perfil que está em execução no momento.

### <a name="how-do-i-determine-whether-application-insights-profiler-is-running"></a>Como fazer determinar se Application Insights Profiler está em execução?

O profiler é executado como um webjob contínuo no aplicativo Web. Você pode abrir o recurso de aplicativo Web no [portal do Azure](https://portal.azure.com). No painel **trabalhos** Web, verifique o status de **ApplicationInsightsProfiler**. Se não estiver em execução, abra **logs** para obter mais informações.

## <a name="troubleshoot-problems-with-profiler-and-azure-diagnostics"></a>Solucionar problemas com o Profiler e Diagnóstico do Azure

>**O bug no criador de perfil que acompanha o WAD para serviços de nuvem foi corrigido.** A versão mais recente do WAD (1.12.2.0) para serviços de nuvem funciona com todas as versões recentes do SDK do App insights. Os hosts do serviço de nuvem atualizarão automaticamente o WAD, mas não será imediato. Para forçar uma atualização, você pode reimplantar o serviço ou reinicializar o nó.

Para ver se o criador de perfil está configurado corretamente pelo Diagnóstico do Azure, faça o seguinte: 
1. Primeiro, verifique se o conteúdo da configuração de Diagnóstico do Azure implantada é o que você espera. 

1. Em segundo lugar, certifique-se de que Diagnóstico do Azure passa o iKey apropriado na linha de comando do criador de perfil. 

1. Terceiro, verifique o arquivo de log do Profiler para ver se o criador de perfil foi executado, mas retornou um erro. 

Para verificar as configurações que foram usadas para configurar Diagnóstico do Azure:

1. Entre na máquina virtual (VM) e, em seguida, abra o arquivo de log neste local. (A unidade pode ser c: ou d: e a versão do plug-in pode ser diferente.)

    ```
    c:\logs\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\1.11.3.12\DiagnosticsPlugin.log  
    ```
    ou
    ```
    c:\WindowsAzure\logs\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\1.11.3.12\DiagnosticsPlugin.log
    ```

1. No arquivo, você pode procurar a cadeia de caracteres **WadCfg** para localizar as configurações que foram passadas para a VM para configurar diagnóstico do Azure. Você pode verificar se o iKey usado pelo coletor do criador de perfil está correto.

1. Verifique a linha de comando usada para iniciar o profiler. Os argumentos que são usados para iniciar o profiler estão no arquivo a seguir. (A unidade pode ser c: ou d:)

    ```
    D:\ProgramData\ApplicationInsightsProfiler\config.json
    ```

1. Verifique se o iKey na linha de comando do criador de perfil está correto. 

1. Usando o caminho encontrado no arquivo *config. JSON* anterior, verifique o arquivo de log do criador de perfil. Ele exibe as informações de depuração que indicam as configurações que o criador de perfil está usando. Ele também exibe mensagens de status e de erro do profiler.  

    Se o profiler estiver em execução enquanto seu aplicativo estiver recebendo solicitações, a seguinte mensagem será exibida: *atividade detectada em iKey*. 

    Quando o rastreamento está sendo carregado, a seguinte mensagem é exibida: *Iniciar para carregar o rastreamento*. 


[profiler-search-telemetry]:./media/profiler-troubleshooting/Profiler-Search-Telemetry.png
[criador de perfil – webjob]:./media/profiler-troubleshooting/Profiler-webjob.png
[criador de perfil-webjob-log]:./media/profiler-troubleshooting/Profiler-webjob-log.png








