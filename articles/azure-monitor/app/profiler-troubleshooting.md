---
title: Problemas de resolução de problemas com o perfil de insights de aplicação Azure
description: Este artigo apresenta etapas e informações de resolução de problemas para ajudar os desenvolvedores que estão com dificuldades em ativar ou usar o Profiler De Insights de Aplicação.
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 7c9dd20aea410aecb34811ca6e08e0f641be292b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84148349"
---
# <a name="troubleshoot-problems-enabling-or-viewing-application-insights-profiler"></a>Problemas de resolução de problemas que permitem ou visualizam o perfil de insights de aplicações

## <a name="active-issues"></a>Questões ativas

* O perfil para aplicações core 3.x ASP.NET é suportado agora nos Serviços de Aplicações Azure.

## <a name="general-troubleshooting"></a><a id="troubleshooting"></a>Resolução geral de problemas

### <a name="profiles-are-uploaded-only-if-there-are-requests-to-your-application-while-profiler-is-running"></a>Os perfis são carregados apenas se houver pedidos para a sua aplicação enquanto o Profiler está em execução

O Azure Application Insights Profiler recolhe dados de perfis durante dois minutos por hora. Também recolhe dados quando seleciona o botão **Profile Now** no painel de perfis **de aplicações configure.** Mas os dados de perfis só são carregados quando podem ser anexados a um pedido que aconteceu enquanto o Profiler estava em execução. 

Profiler escreve mensagens de rastreador e eventos personalizados para o seu recurso Application Insights. Podes usar estes eventos para ver como o Profiler está a funcionar. Se acha que o Profiler deve estar a executar e a capturar vestígios, mas não são apresentados no painel **de desempenho,** pode verificar como o Profiler está a funcionar:

1. Procure mensagens de rastreio e eventos personalizados enviados pelo Profiler para o seu recurso Application Insights. Pode utilizar este cordão de pesquisa para encontrar os dados relevantes:

    ```
    stopprofiler OR startprofiler OR upload OR ServiceProfilerSample
    ```
    A imagem a seguir exibe dois exemplos de pesquisas de dois recursos de IA: 
    
   * À esquerda, a candidatura não está a receber pedidos enquanto o Profiler está a funcionar. A mensagem explica que o upload foi cancelado por falta de atividade. 

   * À direita, o Profiler começou e enviou eventos personalizados quando detetou pedidos que ocorreram enquanto o Profiler estava em execução. Se o evento personalizado ServiceProfilerSample for apresentado, significa que o Profiler anexou um traço a um pedido e pode ver o traço no painel de desempenho do **Application Insights.**

     Se não for apresentada telemetria, o Profiler não está a funcionar. Para resolver problemas, consulte as secções de resolução de problemas para o seu tipo de aplicação específico mais tarde neste artigo.  

     ![Telemetria do Perfil de Pesquisa][profiler-search-telemetry]

1. Se houve pedidos enquanto o Profiler correu, certifique-se de que os pedidos são tratados pela parte da sua aplicação que tem o Profiler ativado. Embora as aplicações consistam, por vezes, em múltiplos componentes, o Profiler está ativado apenas para alguns dos componentes. O painel **de perfis de aplicações configurar** apresenta os componentes que carregaram vestígios.

### <a name="other-things-to-check"></a>Outras coisas para verificar
* Certifique-se de que a sua aplicação está em funcionamento no .NET Framework 4.6.
* Se a sua aplicação web for uma aplicação Core ASP.NET, deve estar a funcionar pelo menos ASP.NET Core 2.0.
* Se os dados que está a tentar visualizar forem mais antigos do que algumas semanas, tente limitar o seu filtro de tempo e tente novamente. Os vestígios são apagados após sete dias.
* Certifique-se de que os proxies ou uma firewall não bloquearam o acesso a https://gateway.azureserviceprofiler.net .
* O profiler não é suportado em planos de serviço de aplicações gratuitas ou partilhados. Se estiver a usar um desses planos, tente aumentar para um dos planos básicos e profiler deve começar a trabalhar.

### <a name="double-counting-in-parallel-threads"></a><a id="double-counting"></a>Contagem dupla em fios paralelos

Em alguns casos, a métrica total do tempo no espectador da pilha é mais do que a duração do pedido.

Esta situação pode ocorrer quando dois ou mais fios estão associados a um pedido e estão a funcionar em paralelo. Nesse caso, o tempo total de rosca é mais do que o tempo decorrido. Um fio pode estar à espera que o outro seja concluído. O espectador tenta detetar esta situação e omite a espera desinteressante. Ao fazê-lo, erra do lado de exibir demasiada informação em vez de omitir o que pode ser informação crítica.

Quando vir fios paralelos nos seus vestígios, determine quais os fios que estão à espera para que possa determinar o caminho crítico para o pedido. Normalmente, o fio que rapidamente entra num estado de espera está simplesmente à espera dos outros fios. Concentre-se nos outros fios e ignore o tempo nos fios de espera.

### <a name="error-report-in-the-profile-viewer"></a>Relatório de erro no visualizador de perfil
Envie um bilhete de apoio no portal. Certifique-se de que inclui o ID de correlação da mensagem de erro.

## <a name="troubleshoot-profiler-on-azure-app-service"></a>Profiler de resolução de problemas no Serviço de Aplicações Azure
Para que o Profiler funcione corretamente:
* O seu plano de serviço de aplicações web deve ser básico ou superior.
* A sua aplicação web deve ter o Application Insights ativado.
* A sua aplicação web deve ter as seguintes definições de aplicação:

    |Definição da Aplicação    | Valor    |
    |---------------|----------|
    |APPINSIGHTS_INSTRUMENTATIONKEY         | iKey para o seu recurso Application Insights    |
    |APPINSIGHTS_PROFILERFEATURE_VERSION | 1.0.0 |
    |DiagnosticServices_EXTENSION_VERSION | ~3 |


* O trabalho web **ApplicationInsightsProfiler3** deve estar em execução. Para verificar o trabalho web:
   1. Vá a [Kudu.](https://blogs.msdn.microsoft.com/cdndevs/2015/04/01/the-kudu-debug-console-azure-websites-best-kept-secret/)
   1. No menu **Ferramentas,** selecione **WebJobs Dashboard**.  
      O **painel WebJobs** abre. 
   
      ![profiler-webjob]   
   
   1. Para ver os detalhes do trabalho web, incluindo o registo, selecione o link **ApplicationInsightsProfiler3.**  
     Abre-se o painel **de detalhes contínuo do WebJob.**

      ![profiler-webjob-log]

Se não consegue perceber porque é que o Profiler não está a trabalhar para si, pode descarregar o registo e enviá-lo para a nossa equipa para obter serviceprofilerhelp@microsoft.com assistência. 
    
### <a name="manual-installation"></a>Instalação manual

Quando configurar o Profiler, são feitas atualizações para as definições da aplicação web. Se o seu ambiente o exigir, pode aplicar as atualizações manualmente. Um exemplo pode ser que a sua aplicação esteja a ser executada num ambiente de Aplicações Web para PowerApps. Para aplicar atualizações manualmente:

1. No painel **de controlo da aplicação** web, abra **as definições**.

1. Definir **a versão .NET Framework** para **v4.6**.

1. Definir **sempre ligado** **.**
1. Crie estas definições de aplicações:

    |Definição da Aplicação    | Valor    |
    |---------------|----------|
    |APPINSIGHTS_INSTRUMENTATIONKEY         | iKey para o seu recurso Application Insights    |
    |APPINSIGHTS_PROFILERFEATURE_VERSION | 1.0.0 |
    |DiagnosticServices_EXTENSION_VERSION | ~3 |

### <a name="too-many-active-profiling-sessions"></a>Demasiadas sessões de perfis ativos

Atualmente, pode ativar profiler num máximo de quatro aplicações web Azure e slots de implementação que estão a ser executadas no mesmo plano de serviço. Se tiver mais de quatro aplicações web em execução num plano de serviço de aplicações, o Profiler poderá lançar um *Microsoft.ServiceProfiler.Exceptions.TooManyETWSessionException*. Profiler funciona separadamente para cada aplicação web e tenta iniciar uma sessão de Rastreio de Eventos para Windows (ETW) para cada aplicação. Mas um número limitado de sessões de ETW pode ser ativo de uma só vez. Se o Profiler webjob reportar demasiadas sessões de perfis ativos, mova algumas aplicações web para um plano de serviço diferente.

### <a name="deployment-error-directory-not-empty-dhomesitewwwrootapp_datajobs"></a>Erro de implantação: Diretório Não Vazio 'D: \\ site doméstico \\ \\ wwwroot \\ App_Data \\ empregos'

Se estiver a recolocar a sua aplicação web num recurso de Aplicações Web com perfilador ativado, poderá ver a seguinte mensagem:

*Diretório Não Vazio 'D: \\ site doméstico \\ \\ wwwroot \\ App_Data \\ empregos'*

Este erro ocorre se executar web Deploy a partir de scripts ou do pipeline de implementação Azure DevOps. A solução consiste em adicionar os seguintes parâmetros de implementação adicionais à tarefa de implementação da Web:

```
-skip:Directory='.*\\App_Data\\jobs\\continuous\\ApplicationInsightsProfiler.*' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs\\continuous$' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs$'  -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data$'
```

Estes parâmetros eliminam a pasta utilizada pelo Application Insights Profiler e desbloqueiam o processo de reimplantação. Não afetam a instância do Profiler que está a ser em execução.

### <a name="how-do-i-determine-whether-application-insights-profiler-is-running"></a>Como determino se o Profiler application insights está a funcionar?

Profiler funciona como um trabalho web contínuo na aplicação web. Pode abrir o recurso da aplicação web no [portal Azure.](https://portal.azure.com) No painel **WebJobs,** verifique o estado do **ApplicationInsightsProfiler**. Se não estiver a funcionar, abra **os Registos** para obter mais informações.

## <a name="troubleshoot-vms-and-cloud-services"></a>VMs de resolução de problemas e serviços de nuvem

>**O bug no perfil que envia no WAD para serviços cloud foi corrigido.** A versão mais recente do WAD (1.12.2.0) para Serviços cloud funciona com todas as versões recentes da App Insights SDK. Os anfitriões do Cloud Service atualizarão o WAD automaticamente, mas não é imediato. Para forçar uma atualização, pode recolocar o seu serviço ou reiniciar o nó.

Para ver se o Profiler está configurado corretamente pela Azure Diagnostics, faça as seguintes três coisas: 
1. Em primeiro lugar, verifique se o conteúdo da configuração Azure Diagnostics que está implantada é o que espera. 

1. Em segundo lugar, certifique-se de que o Azure Diagnostics passa o iKey adequado na linha de comando do Profiler. 

1. Em terceiro lugar, verifique o ficheiro de registo do Profiler para ver se o Profiler correu, mas devolveu um erro. 

Para verificar as definições que foram usadas para configurar o Azure Diagnostics:

1. Inicie sessão na máquina virtual (VM) e, em seguida, abra o ficheiro de registo neste local. A versão plugin pode ser mais recente na sua máquina.
    
    Para VMs:
    ```
    c:\WindowsAzure\logs\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\1.11.3.12\DiagnosticsPlugin.log
    ```
    
    Para serviços em nuvem:
    ```
    c:\logs\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\1.11.3.12\DiagnosticsPlugin.log  
    ```

1. No ficheiro, pode pesquisar a cadeia **WadCfg** para encontrar as definições que foram passadas para o VM para configurar o Azure Diagnostics. Pode verificar se o iKey utilizado pela pia profiler está correto.

1. Verifique a linha de comando que é usada para iniciar o Profiler. Os argumentos utilizados para lançar o Profiler estão no ficheiro seguinte. (A unidade pode ser c: ou d: e o diretório pode ser escondido.)

    Para VMs:
    ```
    C:\ProgramData\ApplicationInsightsProfiler\config.json
    ```
    
    para serviços cloud:
    ```
    D:\ProgramData\ApplicationInsightsProfiler\config.json
    ```

1. Certifique-se de que o iKey na linha de comando do Profiler está correto. 

1. Utilizando o caminho encontrado noconfig.jsanterior *no* ficheiro, verifique o ficheiro de registo do Profiler, chamado **BootstrapN.log**. Apresenta as informações de depurador que indicam as definições que o Profiler está a utilizar. Também apresenta mensagens de estado e erro do Profiler.  

    Para os VMs, o ficheiro costuma estar aqui:
    ```
    C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\1.17.0.6\ApplicationInsightsProfiler
    ```

    Para serviços em nuvem:
    ```
    C:\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\1.17.0.6\ApplicationInsightsProfiler
    ```

    Se o Profiler estiver em execução enquanto a sua aplicação está a receber pedidos, é apresentada a seguinte mensagem: *Atividade detetada a partir do iKey*. 

    Quando o rastreio está a ser carregado, é apresentada a seguinte mensagem: *Comece a carregar vestígios*. 


## <a name="edit-network-proxy-or-firewall-rules"></a>Editar regras de procuração de rede ou firewall

Se a sua aplicação se ligar à Internet através de um proxy ou de uma firewall, poderá ter de editar as regras para permitir que a sua aplicação comunique com o serviço de perfis de aplicações. Os IPs utilizados pelo Application Insights Profiler estão incluídos na etiqueta de serviço do Monitor Azure.


[profiler-search-telemetry]:./media/profiler-troubleshooting/Profiler-Search-Telemetry.png
[profiler-webjob]:./media/profiler-troubleshooting/Profiler-webjob.png
[profiler-webjob-log]:./media/profiler-troubleshooting/Profiler-webjob-log.png








