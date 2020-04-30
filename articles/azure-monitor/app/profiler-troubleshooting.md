---
title: Problemas de resolução de problemas com perfilde insights de aplicação Azure
description: Este artigo apresenta passos e informações de resolução de problemas para ajudar os desenvolvedores que estão com dificuldades em permitir ou usar o Profiler de Insights de Aplicação.
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 55bc4ff05b650884ef17e0de10d7156cbf458a9c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81640953"
---
# <a name="troubleshoot-problems-enabling-or-viewing-application-insights-profiler"></a>Problemas de resolução de problemas que permitem ou visualizam o Perfil de Insights de Aplicação

## <a name="active-issues"></a>Questões ativas

* O perfil para ASP.NET aplicações Core 3.x é suportado agora nos Serviços de Aplicações Azure.

## <a name="general-troubleshooting"></a><a id="troubleshooting"></a>Resolução geral de problemas

### <a name="profiles-are-uploaded-only-if-there-are-requests-to-your-application-while-profiler-is-running"></a>Os perfis só são carregados se houver pedidos para a sua aplicação enquanto o Profiler está em execução

O Perfil de Insights de Aplicação Azure recolhe dados de perfis durante dois minutos por hora. Também recolhe dados quando seleciona o botão **Profile Now** no painel de perfis de insights de configuração da **aplicação.** Mas os dados de perfis só são carregados quando podem ser anexados a um pedido que aconteceu enquanto o Profiler estava em execução. 

O Profiler escreve mensagens de rastreio e eventos personalizados para o seu recurso Application Insights. Podes usar estes eventos para ver como o Profiler está a funcionar. Se acha que o Profiler deve estar a correr e a capturar vestígios, mas não estão expostos no painel **performance,** pode verificar como está o Profiler:

1. Procure mensagens de rastreio e eventos personalizados enviados pelo Profiler para o seu recurso Application Insights. Pode utilizar esta cadeia de pesquisa para encontrar os dados relevantes:

    ```
    stopprofiler OR startprofiler OR upload OR ServiceProfilerSample
    ```
    A imagem que se segue apresenta dois exemplos de pesquisas de dois recursos de IA: 
    
   * À esquerda, o pedido não está a receber pedidos enquanto o Profiler está a funcionar. A mensagem explica que o upload foi cancelado por falta de atividade. 

   * À direita, o Profiler iniciou e enviou eventos personalizados quando detetou pedidos que aconteceram enquanto o Profiler estava a funcionar. Se o evento personalizado ServiceProfilerSample for apresentado, significa que o Profiler anexou um vestígio a um pedido e pode ver o rastreio no painel de desempenho de Insights de **Aplicação.**

     Se não houver telemetria, o Profiler não está a funcionar. Para resolver problemas, consulte as secções de resolução de problemas para o seu tipo de aplicação específico mais tarde neste artigo.  

     ![Telemetria de perfil de pesquisa][profiler-search-telemetry]

1. Se houver pedidos enquanto o Profiler executou, certifique-se de que os pedidos são tratados pela parte da sua aplicação que tem o Profiler ativado. Embora as aplicações por vezes consistam em múltiplos componentes, o Profiler está habilitado apenas para alguns dos componentes. O painel de perfis de insights de **aplicação configure** apresenta os componentes que carregaram os vestígios.

### <a name="other-things-to-check"></a>Outras coisas para verificar
* Certifique-se de que a sua aplicação está em execução no .NET Framework 4.6.
* Se a sua aplicação web for uma aplicação ASP.NET Core, deve estar a funcionar pelo menos ASP.NET Core 2.0.
* Se os dados que está a tentar visualizar forem mais antigos do que algumas semanas, tente limitar o filtro de tempo e tente novamente. Os vestígios são apagados após sete dias.
* Certifique-se de que os proxies ou https://gateway.azureserviceprofiler.netuma firewall não bloquearam o acesso a .
* O Profiler não é suportado em planos de serviço de aplicações gratuitos ou partilhados. Se está a usar um desses planos, tente aumentar para um dos planos básicos e o Profiler deve começar a trabalhar.

### <a name="double-counting-in-parallel-threads"></a><a id="double-counting"></a>Contagem dupla em fios paralelos

Em alguns casos, a métrica total do tempo no espectador da pilha é mais do que a duração do pedido.

Esta situação pode ocorrer quando dois ou mais fios estão associados a um pedido e estão a funcionar em paralelo. Nesse caso, o tempo total de fio é mais do que o tempo decorrido. Um fio pode estar à espera que o outro esteja concluído. O espectador tenta detetar esta situação e omite a espera desinteressante. Ao fazê-lo, errs do lado de mostrar demasiada informação em vez de omitir o que pode ser informação crítica.

Quando vir fios paralelos nos seus vestígios, determine quais os fios que aguardam para que possa determinar o caminho crítico para o pedido. Normalmente, o fio que rapidamente entra em estado de espera está simplesmente à espera dos outros fios. Concentre-se nos outros fios e ignore o tempo nas linhas de espera.

### <a name="error-report-in-the-profile-viewer"></a>Relatório de erro no visualizador de perfil
Envie um bilhete de apoio no portal. Certifique-se de incluir o ID de correlação da mensagem de erro.

## <a name="troubleshoot-profiler-on-azure-app-service"></a>Profiler de resolução de problemas no Serviço de Aplicações Azure
Para que o Profiler funcione corretamente:
* O seu plano de serviço de aplicações web deve ser de nível básico ou superior.
* A sua aplicação web deve ter Informações de Aplicação ativadas.
* A sua aplicação web deve ter as seguintes definições de aplicação:

    |Definição da Aplicação    | Valor    |
    |---------------|----------|
    |APPINSIGHTS_INSTRUMENTATIONKEY         | iKey para o seu recurso Insights de Aplicação    |
    |APPINSIGHTS_PROFILERFEATURE_VERSION | 1.0.0 |
    |DiagnosticServices_EXTENSION_VERSION | ~3 |


* O webjob **ApplicationInsightsProfiler3** deve estar em execução. Para verificar o trabalho web:
   1. Vai para [Kudu.](https://blogs.msdn.microsoft.com/cdndevs/2015/04/01/the-kudu-debug-console-azure-websites-best-kept-secret/)
   1. No menu **Ferramentas,** selecione **WebJobs Dashboard**.  
      O painel **WebJobs** abre. 
   
      ![perfilr-webjob]   
   
   1. Para ver os detalhes do webjob, incluindo o registo, selecione o link **ApplicationInsightsProfiler3.**  
     Abre-se o painel **de detalhes contínuos do WebJob.**

      ![profiler-webjob-log]

Se não consegue descobrir porque é que o Profiler não está a trabalhar para si, serviceprofilerhelp@microsoft.compode descarregar o registo e enviá-lo para a nossa equipa para obter assistência. 
    
### <a name="manual-installation"></a>Instalação manual

Quando configura o Profiler, as atualizações são feitas para as definições da aplicação web. Se o seu ambiente o exigir, pode aplicar as atualizações manualmente. Um exemplo pode ser que a sua aplicação está a funcionar num ambiente de Aplicações Web para PowerApps. Para aplicar as atualizações manualmente:

1. No painel de controlo de **aplicações web,** abra **as definições**.

1. Definir **a versão de enquadramento .NET** para **v4.6**.

1. Coloque **sempre ligado** a **ligado**.
1. Crie estas definições de aplicações:

    |Definição da Aplicação    | Valor    |
    |---------------|----------|
    |APPINSIGHTS_INSTRUMENTATIONKEY         | iKey para o seu recurso Insights de Aplicação    |
    |APPINSIGHTS_PROFILERFEATURE_VERSION | 1.0.0 |
    |DiagnosticServices_EXTENSION_VERSION | ~3 |

### <a name="too-many-active-profiling-sessions"></a>Demasiadas sessões de perfis ativos

Atualmente, pode ativar o Profiler num máximo de quatro aplicações web Azure e slots de implementação que estão a funcionar no mesmo plano de serviço. Se tiver mais de quatro aplicações web a funcionar num plano de serviço de aplicações, o Profiler poderá lançar um *Microsoft.ServiceProfiler.Exceptions.TooManyETWSessionException*. O Profiler funciona separadamente para cada aplicação web e tenta iniciar uma sessão de Rastreio de Eventos para Windows (ETW) para cada aplicação. Mas um número limitado de sessões de ETW pode estar ativa ao mesmo tempo. Se o Webjob profiler reportar demasiadas sessões de perfis ativos, mova algumas aplicações web para um plano de serviço diferente.

### <a name="deployment-error-directory-not-empty-dhomesitewwwrootapp_datajobs"></a>Erro de implantação: Diretório\\\\Não\\Vazio\\\\'D: site doméstico wwwroot App_Data empregos'

Se estiver a reimplantar a sua aplicação web para um recurso de Aplicações Web com o Profiler ativado, poderá ver a seguinte mensagem:

*Diretório Não Vazio\\'D:\\site\\\\\\wwwroot App_Data empregos'*

Este erro ocorre se executar web Deploy a partir de scripts ou a partir do pipeline de implementação Azure DevOps. A solução consiste em adicionar os seguintes parâmetros de implementação adicionais à tarefa de implantação web:

```
-skip:Directory='.*\\App_Data\\jobs\\continuous\\ApplicationInsightsProfiler.*' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs\\continuous$' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs$'  -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data$'
```

Estes parâmetros eliminam a pasta utilizada pelo Perfilde Insights da Aplicação e desbloqueiam o processo de reimplantação. Não afetam a instância do Profiler que está a decorrer.

### <a name="how-do-i-determine-whether-application-insights-profiler-is-running"></a>Como determino se o Perfil de Insights de Aplicação está a funcionar?

O Profiler funciona como um webjob contínuo na aplicação web. Pode abrir o recurso da aplicação web no [portal Azure.](https://portal.azure.com) No painel **WebJobs,** verifique o estado do **ApplicationInsightsProfiler**. Se não estiver a funcionar, abra **os Registos** para obter mais informações.

## <a name="troubleshoot-problems-with-profiler-and-azure-diagnostics"></a>Problemas de resolução de problemas com Profiler e Azure Diagnostics

>**O bug no perfil que os navios do WAD para serviços de nuvem foi corrigido.** A versão mais recente do WAD (1.12.2.0) para serviços de cloud funciona com todas as versões recentes do App Insights SDK. Os anfitriões do Cloud Service atualizarão o WAD automaticamente, mas não é imediato. Para forçar uma atualização, pode recolocar o seu serviço ou reiniciar o nó.

Para ver se o Profiler está configurado corretamente pela Azure Diagnostics, faça as seguintes três coisas: 
1. Primeiro, verifique se o conteúdo da configuração de Diagnóstico Sinuoso Azure que está implementado é o que espera. 

1. Em segundo lugar, certifique-se de que o Azure Diagnostics passa o iKey adequado na linha de comando profiler. 

1. Terceiro, verifique o ficheiro de registo do Profiler para ver se o Profiler correu, mas devolveu um erro. 

Para verificar as definições utilizadas para configurar diagnósticos azure:

1. Inicie sessão na máquina virtual (VM) e abra o ficheiro de registo neste local. (A unidade pode ser c: ou d: e a versão plugin poderia ser diferente.)

    ```
    c:\logs\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\1.11.3.12\DiagnosticsPlugin.log  
    ```
    ou
    ```
    c:\WindowsAzure\logs\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\1.11.3.12\DiagnosticsPlugin.log
    ```

1. No ficheiro, pode procurar a cadeia **WadCfg** para encontrar as definições que foram passadas para o VM para configurar o Azure Diagnostics. Pode verificar se o iKey utilizado pela pia do Profiler está correto.

1. Verifique a linha de comando que é usada para iniciar o Profiler. Os argumentos que são usados para lançar o Profiler estão no ficheiro seguinte. (A unidade pode ser c: ou d:)

    ```
    D:\ProgramData\ApplicationInsightsProfiler\config.json
    ```

1. Certifique-se de que o iKey na linha de comando profiler está correto. 

1. Utilizando o caminho encontrado no ficheiro *config.json* anterior, verifique o ficheiro de registo do Profiler. Exibe as informações de depuração que indicam as definições que o Profiler está a utilizar. Também exibe mensagens de estado e erro do Profiler.  

    Se o Profiler estiver a funcionar enquanto a sua aplicação está a receber pedidos, é apresentada a seguinte mensagem: *Atividade detetada a partir do iKey*. 

    Quando o vestígio está a ser carregado, é apresentada a seguinte mensagem: *Comece a carregar o rastreio*. 


## <a name="edit-network-proxy-or-firewall-rules"></a>Editar regras de procuração de rede ou firewall

Se a sua aplicação se ligar à Internet através de um proxy ou de uma firewall, poderá ter de editar as regras para permitir que a sua aplicação se comunique com o serviço de Perfil de Insights de Aplicação. Os IPs utilizados pelo Perfilde Insights de Aplicação estão incluídos na etiqueta de serviço Do Monitor Azure.


[profiler-search-telemetry]:./media/profiler-troubleshooting/Profiler-Search-Telemetry.png
[perfilr-webjob]:./media/profiler-troubleshooting/Profiler-webjob.png
[profiler-webjob-log]:./media/profiler-troubleshooting/Profiler-webjob-log.png








