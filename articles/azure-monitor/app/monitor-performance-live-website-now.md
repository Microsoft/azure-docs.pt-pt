---
title: Monitorizar uma aplicação Web ASP.NET com o Application Insights do Azure | Microsoft Docs
description: Monitorize o desempenho de um site sem o reimplementar. Funciona com ASP.NET web aplicações alojadas no local ou em VMs.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 769a5ea4-a8c6-4c18-b46c-657e864e24de
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/05/2018
ms.author: mbullwin
ms.openlocfilehash: 66410a0586fcdd10f7c350bd4bad5211be61a79a
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/22/2019
ms.locfileid: "56669351"
---
# <a name="instrument-web-apps-at-runtime-with-application-insights-status-monitor"></a>Instrumentar aplicações web no tempo de execução com o Monitor de estado do Application Insights

Pode instrumentar uma aplicação Web em direto com o Azure Application Insights, sem ter de modificar ou voltar a implementar o seu código. Precisará de uma subscrição do [Microsoft Azure](https://azure.com).

Monitor de estado é utilizado para instrumentar uma aplicação .NET hospedada no IIS no local ou numa VM.

- Se a aplicação é implementada para os serviços de aplicações do Azure, siga [estas instruções](azure-web-apps.md).
- Se a aplicação é implementada numa VM do Azure, pode alternar na monitorização do Application Insights do painel de controlo do Azure.
- (Existem artigos separados sobre instrumentação [serviços Cloud do Azure](../../azure-monitor/app/cloudservices.md).)


![Gráficos de descrição geral de captura de ecrã do App Insights que contém informações sobre pedidos com falhas, tempo de resposta do servidor e pedidos de servidor](./media/monitor-performance-live-website-now/overview-graphs.png)

Tem a opção de duas rotas de aplicar o Application Insights às suas aplicações web do .NET:

* **Tempo de compilação:** [Adicionar o Application Insights SDK] [ greenbrown] ao seu código de aplicação web.
* **Tempo de execução:** Instrumente a sua aplicação web no servidor, conforme descrito abaixo, sem reconstruir e Reimplementar o código.

> [!NOTE]
> Se usar a instrumentação do tempo de compilação, instrumention não funcionará, mesmo se está ligado de tempo de execução.

Segue-se um resumo do que pode usufruir:

|  | Hora da compilação | Tempo de execução |
| --- | --- | --- |
| Pedidos e exceções |Sim |Sim |
| [Exceções mais detalhadas](../../azure-monitor/app/asp-net-exceptions.md) | |Sim |
| [Diagnóstico de dependências](../../azure-monitor/app/asp-net-dependencies.md) |Em .NET 4.6+, mas com menos detalhe |Sim, detalhe completo: códigos de resultado, texto do comando do SQL, verbo HTTP|
| [Contadores de desempenho do sistema](../../azure-monitor/app/performance-counters.md) |Sim |Sim |
| [API para telemetria personalizada][api] |Sim |Não |
| [Integração de registos de rastreio](../../azure-monitor/app/asp-net-trace-logs.md) |Sim |Não |
| [Dados de utilizador e vista de página](../../azure-monitor/app/javascript.md) |Sim |Não |
| É necessário reprogramar o código |Sim | Não |



## <a name="monitor-a-live-iis-web-app"></a>Monitorizar uma aplicação Web IIS em direto

Se a aplicação estiver alojada num servidor de IIS, ative o Application Insights com o Monitor de Estado.

1. No seu servidor Web de IIS, inicie sessão com credenciais de administrador.
2. Se o Monitor de estado do Application Insights ainda não estiver instalado, [transfira e execute o instalador](#download)
3. No Monitor de Estado, selecione a aplicação Web instalada ou o Web site que pretende monitorizar. Inicie sessão com as credenciais do Azure.

    Configure o recurso onde pretende ver os resultados no portal do Application Insights. (Normalmente, é melhor criar um novo recurso. Selecione um recurso existente se já tiver [testes Web][availability] ou [monitorização de cliente][client] para esta aplicação.) 

    ![Escolha uma aplicação e um recurso.](./media/monitor-performance-live-website-now/appinsights-036-configAIC.png)

4. Reinicie o IIS.

    ![Escolha Reiniciar na parte superior da caixa de diálogo.](./media/monitor-performance-live-website-now/appinsights-036-restart.png)

    O serviço Web é interrompido durante um curto período.

## <a name="customize-monitoring-options"></a>Personalizar opções de monitorização

Ativar o Application Insights adiciona DLLs e o Applicationinsights.config à sua aplicação Web. Pode [editar o ficheiro .config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) para alterar algumas das opções.

## <a name="when-you-re-publish-your-app-re-enable-application-insights"></a>Quando voltar a publicar a aplicação, volte a ativar o Application Insights

Antes de voltar a publicar a aplicação, considere [adicionar o Application Insights ao código no Visual Studio][greenbrown]. Irá obter telemetria mais detalhada e a capacidade para escrever telemetria personalizada.

Se pretender voltar a publicar sem adicionar o Application Insights ao código, tenha em atenção de que o processo de implementação poderá eliminar os DLLs e o ApplicationInsights.config do site publicado. Desta forma:

1. Se editou o ApplicationInsights.config, efetue uma cópia do mesmo antes de voltar a publicar a aplicação.
2. Voltar a publicar a aplicação.
3. Voltar a ativar a monitorização do Application Insights. (Utilize o método adequado: o painel de controlo da aplicação Web do Azure ou o Monitor de Estado num anfitrião IIS.)
4. Restabeleça as alterações efetuadas no ficheiro .config.


## <a name="troubleshoot"></a>Resolução de Problemas

### <a name="confirm-a-valid-installation"></a>Confirmar uma instalação válida 

Estes são alguns passos que pode efetuar para confirmar que a instalação foi concluída com êxito.

- Confirme que o ficheiro applicationinsights. config está presente no diretório de aplicação de destino e de que contém a ikey.

- Se suspeitar de que os dados estão em falta pode executar uma consulta simples no [Analytics](../log-query/get-started-portal.md) para listar todas as funções de cloud atualmente a enviar telemetria.
```Kusto
union * | summarize count() by cloud_RoleName, cloud_RoleInstance
```

- Se tiver de confirmar que o Application Insights é anexado com êxito, pode executar [identificador de Sysinternals](https://docs.microsoft.com/sysinternals/downloads/handle) num comando janela para confirmar que applicationinsights.dll foi carregada pelo IIS.
```cmd
handle.exe /p w3wp.exe
```


### <a name="cant-connect-no-telemetry"></a>Não consegue ligar? Sem telemetria?

* Abra [as portas de envio necessárias](../../azure-monitor/app/ip-addresses.md#outgoing-ports) na firewall do seu servidor para permitir que o Monitor de Estado funcione.

### <a name="unable-to-login"></a>Não é possível iniciar sessão

* Se o Monitor de estado não é possível iniciar sessão, fazer uma instalação de linha de comandos, em vez disso. Monitor de estado tenta iniciar sessão para recolher a ikey, mas pode fornecer isso manualmente usando o comando:

```powershell
Import-Module 'C:\Program Files\Microsoft Application Insights\Status Monitor\PowerShell\Microsoft.Diagnostics.Agent.StatusMonitor.PowerShell.dll'
Start-ApplicationInsightsMonitoring -Name appName -InstrumentationKey 00000000-000-000-000-0000000
```

### <a name="could-not-load-file-or-assembly-systemdiagnosticsdiagnosticsource"></a>Não foi possível carregar o ficheiro ou a assemblagem 'System.Diagnostics.DiagnosticSource'

Poderá receber este erro depois de ativar o Application Insights. Isto acontece porque o instalador substitui essa dll no diretório bin.
Para corrigir atualizar Web. config:

```xml
<dependentAssembly>
    <assemblyIdentity name="System.Diagnostics.DiagnosticSource" publicKeyToken="cc7b13ffcd2ddd51"/>
    <bindingRedirect oldVersion="0.0.0.0-4.*.*.*" newVersion="4.0.2.1"/>
</dependentAssembly>
```

Estamos a acompanhar este problema [aqui](https://github.com/Microsoft/ApplicationInsights-Home/issues/301).


### <a name="application-diagnostic-messages"></a>Mensagens de diagnóstico de aplicação

* Abra o Monitor de Estado e selecione a aplicação no painel esquerdo. Verifique se existem quaisquer mensagens de diagnóstico para esta aplicação na secção "Notificações de configuração":

  ![Abra o painel Desempenho para ver o pedido, o tempo de resposta, a dependência e outros dados](./media/monitor-performance-live-website-now/appinsights-status-monitor-diagnostics-message.png)
  
### <a name="detailed-logs"></a>Registos detalhados

* Por predefinição Monitor de estado irá produzir registos de diagnóstico em: `C:\Program Files\Microsoft Application Insights\Status Monitor\diagnostics.log`

* Para a saída de registos verbosos, modifique o ficheiro de configuração: `C:\Program Files\Microsoft Application Insights\Status Monitor\Microsoft.Diagnostics.Agent.StatusMonitor.exe.config` e adicione `<add key="TraceLevel" value="All" />` para o `appsettings`.
Em seguida, reinicie o monitor de estado.

### <a name="insufficient-permissions"></a>Permissões insuficientes
  
* No servidor, se vir uma mensagem sobre "permissões insuficientes", experimente o seguinte:
  * No Gestor de IIS, selecione o conjunto de aplicações, abra **Definições Avançadas**, e, em **Modelo de Processos** tenha em atenção a identidade.
  * No painel de controlo de gestão do Computador, adicione esta identidade ao grupo de Utilizadores do Monitor de Desempenho.

### <a name="conflict-with-systems-center-operations-manager"></a>Conflito com o Systems Center Operations Manager

* Se tiver MMA/SCOM (Systems Center Operations Manager) instalado no servidor, algumas versões podem entrar em conflito. Desinstale o SCOM e o Monitor de Estado, e reinstale as versões mais recentes.

### <a name="failed-or-incomplete-installation"></a>Instalação falhada ou incompleta

Se o Monitor de estado falhar durante a instalação, poderia ficar com uma instalação incompleta que não é possível recuperar de Monitor de estado. Isso exigirá uma reposição manual.

Elimine qualquer um desses arquivos encontrados no diretório da aplicação:
- Quaisquer DLLs no diretório bin começando com qualquer um dos "Microsoft.AI". ou "Applicationinsights.".
- Essa DLL no diretório bin "Microsoft.Web.Infrastructure.dll"
- Essa DLL no diretório bin "System.Diagnostics.DiagnosticSource.dll"
- No diretório da aplicação remover "App_Data\packages"
- No diretório da aplicação remover "applicationinsights. config"


### <a name="additional-troubleshooting"></a>Resolução de Problemas Adicional

* Consulte adicional [resolução de problemas][qna].

## <a name="system-requirements"></a>Requisitos de Sistema
Suporte de SO para o Monitor de Estado do Application Insights no Servidor:

* Windows Server 2008
* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

com o .NET Framework 4.5 e o SP mais recente

No lado do cliente: Windows 7, 8, 8.1 e 10, novamente com o .NET Framework 4.5

Suporte do IIS é: IIS 7, 7.5, 8, 8.5 (o IIS é necessário)

## <a name="automation-with-powershell"></a>Automatização com o PowerShell
Pode iniciar e parar a monitorização com o PowerShell no servidor de IIS.

Primeiro, importe o módulo do Application Insights:

`Import-Module 'C:\Program Files\Microsoft Application Insights\Status Monitor\PowerShell\Microsoft.Diagnostics.Agent.StatusMonitor.PowerShell.dll'`

Descubra que aplicações estão a ser monitorizadas:

`Get-ApplicationInsightsMonitoringStatus [-Name appName]`

* `-Name` (Opcional) O nome de uma aplicação Web.
* Apresenta o Estado de Monitorização do Application Insights de cada aplicação Web (ou a aplicação com nome) neste servidor IIS.
* Devolve `ApplicationInsightsApplication` para cada aplicação:

  * `SdkState==EnabledAfterDeployment`: Aplicação está a ser monitorizada e foi instrumentada no momento de execução pela ferramenta Monitor de estado ou por `Start-ApplicationInsightsMonitoring`.
  * `SdkState==Disabled`: A aplicação não foi instrumentada para o Application Insights. Ou nunca foi instrumentada ou a monitorização no momento de execução foi desativada com a ferramenta Monitor de Estado ou com o `Stop-ApplicationInsightsMonitoring`.
  * `SdkState==EnabledByCodeInstrumentation`: A aplicação foi instrumentada adicionando o SDK ao código-fonte. O SDK não pode ser atualizado ou parado.
  * `SdkVersion` mostra a versão utilizada para monitorizar esta aplicação.
  * `LatestAvailableSdkVersion` mostra a versão atualmente disponível na galeria NuGet. Para atualizar a aplicação para esta versão, utilize `Update-ApplicationInsightsMonitoring`.

`Start-ApplicationInsightsMonitoring -Name appName -InstrumentationKey 00000000-000-000-000-0000000`

* `-Name` O nome da aplicação no IIS
* `-InstrumentationKey` A ikey do recurso Application Insights onde pretende que os resultados sejam apresentados.
* Este cmdlet afeta apenas as aplicações que ainda não foram instrumentadas - ou seja, SdkState==NotInstrumented.

    O cmdlet não afeta uma aplicação que já esteja instrumentada. É indiferente se a aplicação foi instrumentada no momento da compilação, adicionando o SDK ao código, ou no momento da execução, através de uma utilização anterior deste cmdlet.

    A versão SDK utilizada para instrumentar a aplicação é a versão mais recentemente transferida para este servidor.

    Para transferir a versão mais recente, utilize Update-ApplicationInsightsVersion.
* Devolve `ApplicationInsightsApplication` com êxito. Se falhar, regista um rastreio em stderr.

          Name                      : Default Web Site/WebApp1
          InstrumentationKey        : 00000000-0000-0000-0000-000000000000
          ProfilerState             : ApplicationInsights
          SdkState                  : EnabledAfterDeployment
          SdkVersion                : 1.2.1
          LatestAvailableSdkVersion : 1.2.3

`Stop-ApplicationInsightsMonitoring [-Name appName | -All]`

* `-Name` O nome de uma aplicação no IIS
* `-All` Para a monitorização de todas as aplicações neste servidor IIS para o qual `SdkState==EnabledAfterDeployment`
* Interrompe a monitorização de aplicações especificadas e remove a instrumentação. Só funciona para aplicações que foram instrumentadas durante a execução com a ferramenta Monitorização de Estado ou Start-ApplicationInsightsApplication. (`SdkState==EnabledAfterDeployment`)
* Devolve ApplicationInsightsApplication.

`Update-ApplicationInsightsMonitoring -Name appName [-InstrumentationKey "0000000-0000-000-000-0000"`]

* `-Name`: O nome de uma aplicação web no IIS.
* `-InstrumentationKey` (Opcional.) Utilize esta opção para alterar o recurso para o qual a telemetria da aplicação é enviada.
* Este cmdlet:
  * Atualiza a aplicação nomeada para a versão do SDK mais recentemente transferida para esta máquina. (Só funciona se `SdkState==EnabledAfterDeployment`)
  * Se fornecer uma chave de instrumentação, a aplicação nomeada é reconfigurada para enviar a telemetria para o recurso com essa chave. (Funciona se `SdkState != Disabled`)

`Update-ApplicationInsightsVersion`

* Transfere o SDK mais recente do Application Insights para o servidor.

## <a name="questions"></a>Perguntas sobre o Monitor de Estado

### <a name="what-is-status-monitor"></a>O que é o Monitor de Estado?

Uma aplicação de ambiente de trabalho que instala no seu servidor Web do IIS. Ajuda-o a instrumentar e a configurar as aplicações Web. 

### <a name="when-do-i-use-status-monitor"></a>Quando utilizo o Monitor de Estado?

* Para instrumentar qualquer aplicação Web que esteja em execução no seu servidor IIS - mesmo se já estiver em execução.
* Para ativar telemetria adicional para aplicações Web que tenham sido [criadas com o SDK do Application Insights](../../azure-monitor/app/asp-net.md) no momento da compilação. 

### <a name="can-i-close-it-after-it-runs"></a>Pode fechá-lo depois de a tarefa ser executada?

Sim. Depois de ter instrumentado os sites que selecionou, pode fechá-lo.

Não recolhe telemetria por si só. Só configura as aplicações Web e define algumas permissões.

### <a name="what-does-status-monitor-do"></a>O que faz o Monitor de Estado?

Quando seleciona uma aplicação Web para o Monitor de Estado instrumentar:

* Transfere e coloca os assemblagens do Application Insights e o ficheiro Applicationinsights config na pasta de binários da aplicação web.
* Permite a criação de perfis CLR para recolher chamadas de dependência.

### <a name="what-version-of-application-insights-sdk-does-status-monitor-install"></a>Qual versão do SDK do Application Insights instalar o Monitor de estado?

A partir de agora, o Monitor de estado só pode instalar as versões do Application Insights SDK 2.3 ou 2.4.

### <a name="do-i-need-to-run-status-monitor-whenever-i-update-the-app"></a>Preciso de executar o Monitor de Estado sempre que atualizar a aplicação?

Não, se voltar a implementar incrementalmente. 

Se selecionar a opção "eliminar ficheiros existentes" no processo de publicação, terá de voltar a executar o Monitor de Estado para configurar o Application Insights.

### <a name="what-telemetry-is-collected"></a>Que telemetria é recolhida?

Para aplicações que instrumenta apenas no runtime com o Monitor de Estado:

* Pedidos HTTP
* Chamadas para dependências
* Exceções
* Contadores de desempenho

Para aplicações já instrumentadas no momento da compilação:

 * Contadores de processamento.
 * Chamadas de dependência (.NET 4.5); valores de devolução em chamadas de dependência (.NET 4.6).
 * Valores de rastreio de pilha de exceção.

[Saiba mais](https://apmtips.com/blog/2016/11/18/how-application-insights-status-monitor-not-monitors-dependencies/)

## <a name="video"></a>Vídeo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]

## <a name="download"></a>Transferir o Monitor de estado

- Transfira e execute o [instalador de Monitor de estado](https://go.microsoft.com/fwlink/?LinkId=506648)
- Ou execute [instalador de plataforma Web](https://www.microsoft.com/web/downloads/platform.aspx) e pesquisa no mesmo para o Monitor de estado do Application Insights.

## <a name="next"></a>Passos seguintes

Ver a telemetria:

* [Explore as métricas](../../azure-monitor/app/metrics-explorer.md) para monitorizar o desempenho e a utilização
* [Pesquise eventos e registos][diagnostic] para diagnosticar problemas
* [Análise](../../azure-monitor/app/analytics.md) para obter mais informações avançadas consultas
* [Criar dashboards](../../azure-monitor/app/app-insights-dashboards.md)

Adicionar mais telemetria:

* [Criar testes Web][availability] para se certificar de que mantém o seu site em direto.
* [Adicionar telemetria de cliente Web][usage] para ver as exceções de código da página Web e permitir a inserção de chamadas de rastreio.
* [Adicionar o Application Insights SDK ao código][greenbrown] para que possa inserir chamadas de rastreio e de registo

<!--Link references-->

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[availability]: monitor-web-app-availability.md
[client]: ../../azure-monitor/app/javascript.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[greenbrown]: ../../azure-monitor/app/asp-net.md
[qna]: ../../azure-monitor/app/troubleshoot-faq.md
[roles]: ../../azure-monitor/app/resources-roles-access-control.md
[usage]: ../../azure-monitor/app/javascript.md
