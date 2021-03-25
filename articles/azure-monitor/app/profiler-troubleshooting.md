---
title: Problemas de resolução de problemas com o perfil de insights de aplicação Azure
description: Este artigo apresenta etapas e informações de resolução de problemas para ajudar os desenvolvedores a ativar e utilizar o Profiler Application Insights.
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 2c80362c3407f1404c6657997de89c2741264909
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2021
ms.locfileid: "105026560"
---
# <a name="troubleshoot-problems-enabling-or-viewing-application-insights-profiler"></a>Problemas de resolução de problemas que permitem ou visualizam o perfil de insights de aplicações

## <a name="general-troubleshooting"></a><a id="troubleshooting"></a>Resolução geral de problemas

### <a name="make-sure-youre-using-the-appropriate-profiler-endpoint"></a>Certifique-se de que está a utilizar o ponto final do profiler apropriado

Atualmente, as únicas regiões que necessitam de modificações no ponto final são [o Governo de Azure](../../azure-government/compare-azure-government-global-azure.md#application-insights) e [a Azure China.](/azure/china/resources-developer-guide)

|Definição da Aplicação    | Nuvem do Governo dos EUA | Nuvem da China |   
|---------------|---------------------|-------------|
|ApplicationInsightsProfilerEndpoint         | `https://profiler.monitor.azure.us`    | `https://profiler.monitor.azure.cn` |
|ApplicationInsightsEndpoint | `https://dc.applicationinsights.us` | `https://dc.applicationinsights.azure.cn` |

### <a name="profiles-are-uploaded-only-if-there-are-requests-to-your-application-while-profiler-is-running"></a>Os perfis são carregados apenas se houver pedidos para a sua aplicação enquanto o Profiler está em execução

O Azure Application Insights Profiler recolhe dados durante dois minutos por hora. Também pode recolher dados quando selecionar o botão **Profile Now** no painel de perfis **de aplicações configure.**

> [!NOTE]
> Os dados de perfis são enviados apenas quando podem ser anexados a um pedido que aconteceu enquanto o Profiler estava em execução. 

Profiler escreve mensagens de rastreador e eventos personalizados para o seu recurso Application Insights. Você pode usar estes eventos para ver como o Profiler está funcionando:

1. Procure mensagens de rastreio e eventos personalizados enviados pelo Profiler para o seu recurso Application Insights. Pode utilizar este cordão de pesquisa para encontrar os dados relevantes:

    ```
    stopprofiler OR startprofiler OR upload OR ServiceProfilerSample
    ```
    A imagem a seguir exibe dois exemplos de pesquisas de dois recursos de IA: 
    
   * À esquerda, a candidatura não está a receber pedidos enquanto o Profiler está a funcionar. A mensagem explica que o upload foi cancelado por falta de atividade. 

   * À direita, o Profiler começou e enviou eventos personalizados quando detetou pedidos que ocorreram enquanto o Profiler estava em execução. Se o `ServiceProfilerSample` evento personalizado for apresentado, significa que foi capturado um perfil e está disponível no painel de desempenho do **Application Insights.**

     Se não forem apresentados registos, o Profiler não está a funcionar. Para resolver problemas, consulte as secções de resolução de problemas para o seu tipo de aplicação específico mais tarde neste artigo.  

     ![Telemetria do Perfil de Pesquisa][profiler-search-telemetry]

### <a name="other-things-to-check"></a>Outras coisas para verificar
* Certifique-se de que a sua aplicação está em funcionamento no .NET Framework 4.6.
* Se a sua aplicação web for uma aplicação Core ASP.NET, deve estar a funcionar pelo menos ASP.NET Core 2.0.
* Se os dados que está a tentar visualizar forem mais antigos do que algumas semanas, tente limitar o seu filtro de tempo e tente novamente. Os vestígios são apagados após sete dias.
* Certifique-se de que os proxies ou uma firewall não bloquearam o acesso a https://gateway.azureserviceprofiler.net .
* O profiler não é suportado em planos de serviço de aplicações gratuitas ou partilhados. Se estiver a usar um desses planos, tente aumentar para um dos planos básicos e profiler deve começar a trabalhar.

### <a name="double-counting-in-parallel-threads"></a><a id="double-counting"></a>Contagem dupla em fios paralelos

Em alguns casos, a métrica total do tempo no espectador da pilha é mais do que a duração do pedido.

Esta situação pode ocorrer quando dois ou mais fios paralelos estão associados a um pedido. Nesse caso, o tempo total de rosca é mais do que o tempo decorrido.

Um fio pode estar à espera que o outro seja concluído. O espectador tenta detetar esta situação e omite a espera desinteressante. Ao fazê-lo, erra do lado de exibir demasiada informação em vez de omitir o que pode ser informação crítica.

Quando vir fios paralelos nos seus vestígios, determine quais os fios que estão à espera para que possa identificar o caminho quente para o pedido.

Normalmente, o fio que rapidamente entra num estado de espera está simplesmente à espera dos outros fios. Concentre-se nos outros fios e ignore o tempo nos fios de espera.

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
   1. Vá a [Kudu.](/archive/blogs/cdndevs/the-kudu-debug-console-azure-websites-best-kept-secret)
   1. No menu **Ferramentas,** selecione **WebJobs Dashboard**.  
      O **painel WebJobs** abre. 
   
      ![O screenshot mostra o painel WebJobs, que exibe o nome, estado e última hora de trabalho.][profiler-webjob]   
   
   1. Para ver os detalhes do trabalho web, incluindo o registo, selecione o link **ApplicationInsightsProfiler3.**  
     Abre-se o painel **de detalhes contínuo do WebJob.**

      ![A screenshot mostra o painel de detalhes contínuo do WebJob.][profiler-webjob-log]

Se o Profiler não estiver a trabalhar para si, pode descarregar o registo e enviá-lo para a nossa equipa para obter serviceprofilerhelp@microsoft.com assistência.

### <a name="check-the-diagnostic-services-site-extension-status-page"></a>Consulte a página de estado do site dos serviços de diagnóstico
Se o Profiler foi ativado através do painel de Insights de [Aplicação](profiler.md) no portal, foi ativado pela extensão do site dos Serviços de Diagnóstico.

> [!NOTE]
> A instalação sem código do Application Insights Profiler segue a política de suporte .NET Core.
> Para obter mais informações sobre os tempos de funcionação suportados, consulte [a Política de Suporte do Núcleo .NET](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).

Pode consultar a Página de Estado desta extensão indo para o seguinte url: `https://{site-name}.scm.azurewebsites.net/DiagnosticServices`

> [!NOTE]
> O domínio da ligação 'Página de Estado' variará consoante a nuvem.
Este domínio será o mesmo que o site de gestão kudu para o Serviço de Aplicações.

Esta página de estado mostra o estado de instalação dos agentes profiler e Snapshot Collector. Se houve um erro inesperado, será exibido e mostrará como corrigi-lo.

Pode utilizar o site de gestão Kudu para o Serviço de Aplicações para obter o url base desta página de estado:
1. Abra a sua aplicação de Serviço de Aplicações no portal Azure.
2. Selecione **Ferramentas Avançadas** ou procure **por Kudu**.
3. Selecione **Go**.
4. Uma vez que você está no site de gestão Kudu, no URL, **apece o seguinte `/DiagnosticServices` e pressione a entrada**.
 Vai acabar assim: `https://<kudu-url>/DiagnosticServices`

Apresentará uma Página de Estado semelhante à seguinte: ![ Página de Estado dos Serviços de Diagnóstico](./media/diagnostic-services-site-extension/status-page.png)
    
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

Pode ativar o Profiler num máximo de quatro Aplicações Web que estão a funcionar no mesmo plano de serviço. Se tiver mais de quatro, profiler pode lançar um *Microsoft.ServiceProfiler.Exceptions.TooManyETWSessionException*. Para resolvê-lo, mova algumas aplicações web para um plano de serviço diferente.

### <a name="deployment-error-directory-not-empty-dhomesitewwwrootapp_datajobs"></a>Erro de implantação: Diretório Não Vazio 'D: \\ site doméstico \\ \\ wwwroot \\ App_Data \\ empregos'

Se estiver a recolocar a sua aplicação web num recurso de Aplicações Web com perfilador ativado, poderá ver a seguinte mensagem:

*Diretório Não Vazio 'D: \\ site doméstico \\ \\ wwwroot \\ App_Data \\ empregos'*

Este erro ocorre se executar web Deploy a partir de scripts ou a partir dos Pipelines Azure. A solução consiste em adicionar os seguintes parâmetros de implementação à tarefa de implantação da Web:

```
-skip:Directory='.*\\App_Data\\jobs\\continuous\\ApplicationInsightsProfiler.*' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs\\continuous$' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs$'  -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data$'
```

Estes parâmetros eliminam a pasta utilizada pelo Application Insights Profiler e desbloqueiam o processo de reimplantação. Não afetam a instância do Profiler que está a ser em execução.

### <a name="how-do-i-determine-whether-application-insights-profiler-is-running"></a>Como determino se o Profiler application insights está a funcionar?

Profiler funciona como um trabalho web contínuo na aplicação web. Pode abrir o recurso da aplicação web no [portal Azure.](https://portal.azure.com) No painel **WebJobs,** verifique o estado do **ApplicationInsightsProfiler**. Se não estiver a funcionar, abra **os Registos** para obter mais informações.

## <a name="troubleshoot-vms-and-cloud-services"></a>VMs de resolução de problemas e serviços de nuvem

>**O bug no perfil que envia no WAD para serviços cloud foi corrigido.** A versão mais recente do WAD (1.12.2.0) para Serviços cloud funciona com todas as versões recentes da App Insights SDK. Os anfitriões do Cloud Service atualizarão o WAD automaticamente, mas não é imediato. Para forçar uma atualização, pode recolocar o seu serviço ou reiniciar o nó.

Para ver se o Profiler está configurado corretamente pela Azure Diagnostics, siga os passos abaixo: 
1. Verifique se o conteúdo da configuração Azure Diagnostics implementada é o que espera. 

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

    Para VMs, o ficheiro está aqui:
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

Se a sua aplicação se ligar à Internet através de um representante ou de uma firewall, poderá ter de atualizar as regras para comunicar com o serviço Profiler.

Os IPs utilizados pelo Application Insights Profiler estão incluídos na etiqueta de serviço do Monitor Azure. Para mais informações, consulte a [documentação de Etiquetas de Serviço.](../../virtual-network/service-tags-overview.md)


[profiler-search-telemetry]:./media/profiler-troubleshooting/Profiler-Search-Telemetry.png
[profiler-webjob]:./media/profiler-troubleshooting/Profiler-webjob.png
[profiler-webjob-log]:./media/profiler-troubleshooting/Profiler-webjob-log.png