---
title: Monitorizar uma aplicação Web ASP.NET com o Application Insights do Azure | Microsoft Docs
description: Monitorize o desempenho de um site sem o reimplementar. Funciona com ASP.NET aplicações web hospedadas no local ou em VMs.
ms.topic: conceptual
ms.date: 08/26/2019
ms.custom: devx-track-dotnet
ms.openlocfilehash: 6c27c78bf8e3f3b8af342a14a38c9be3821d781a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91758668"
---
# <a name="instrument-web-apps-at-runtime-with-application-insights-codeless-attach"></a>Aplicações web de instrumentos em tempo de execução com App Insights Codeless Attach

> [!IMPORTANT]
> O Status Monitor já não é recomendado para utilização, e a partir de 1 de junho de **2021** esta versão do Status monitor não será suportada. Foi substituído pelo Agente de Insights de Aplicação do Monitor Azure (anteriormente denominado Status Monitor v2). Consulte a nossa documentação para [implementações de servidores no local](./status-monitor-v2-overview.md) ou [para a máquina virtual Azure e para as implementações de conjuntos de escalas de máquinas virtuais](./azure-vm-vmss-apps.md).

Pode instrumentar uma aplicação Web em direto com o Azure Application Insights, sem ter de modificar ou voltar a implementar o seu código. Precisará de uma subscrição do [Microsoft Azure](https://azure.com).

O Status Monitor é utilizado para instrumentar uma aplicação .NET hospedada no IIS no local ou num VM.

- Se a sua aplicação for implantada no conjunto de escala de máquina virtual Azure VM ou Azure, siga [estas instruções](azure-vm-vmss-apps.md).
- Se a sua aplicação for implantada nos serviços de aplicações Azure, siga [estas instruções.](azure-web-apps.md)
- Se a sua aplicação for implantada num VM Azure, pode ligar a monitorização de Insights de Aplicação a partir do painel de controlo Azure.
- (Existem também artigos separados sobre instrumentação dos [Serviços Azure Cloud](./cloudservices.md).)


![Screenshot de gráficos de visão geral do App Insights que contêm informações sobre pedidos falhados, tempo de resposta do servidor e pedidos de servidor](./media/monitor-performance-live-website-now/overview-graphs.png)

Tem uma escolha de duas rotas para aplicar Insights de Aplicação às suas aplicações web .NET:

* **Tempo de compilação:** [adicione o Application Insights SDK][greenbrown] ao código da sua aplicação Web.
* **Tempo de execução:** instrumente a sua aplicação Web no servidor, conforme descrito abaixo, sem a reconstruir e implementar novamente o código.

> [!NOTE]
> Se utilizar a instrumentação do tempo de construção, a instrumentação do tempo de funcionação não funcionará mesmo que esteja ligada.

Segue-se um resumo do que pode usufruir:

|  | Hora da compilação | Tempo de execução |
| --- | --- | --- |
| **Pedidos & exceções** |Sim |Sim |
| **[Exceções mais detalhadas](./asp-net-exceptions.md)** | |Sim |
| **[Diagnóstico de dependência](./asp-net-dependencies.md)** |Em .NET 4.6+, mas com menos detalhe |Sim, detalhe completo: códigos de resultado, texto do comando do SQL, verbo HTTP|
| **[Contadores de desempenho do sistema](./performance-counters.md)** |Sim |Sim |
| **[API para telemetria personalizada][api]** |Sim |Não |
| **[Integração de registo de rastreio](./asp-net-trace-logs.md)** |Sim |Não |
| **[Visualização da página & dados do utilizador](./javascript.md)** |Sim |Não |
| **É necessário reprogramar o código** |Sim | Não |



## <a name="monitor-a-live-iis-web-app"></a>Monitorizar uma aplicação Web IIS em direto

Se a aplicação estiver alojada num servidor de IIS, ative o Application Insights com o Monitor de Estado.

1. No seu servidor Web de IIS, inicie sessão com credenciais de administrador.
2. Se o Monitor de Estado do Application Insights ainda não estiver instalado, [transfira e execute o instalador](#download)
3. No Monitor de Estado, selecione a aplicação Web instalada ou o Web site que pretende monitorizar. Inicie sessão com as credenciais do Azure.

    Configure o recurso onde pretende ver os resultados no portal do Application Insights. (Normalmente, é melhor criar um novo recurso. Selecione um recurso existente se já tiver [testes Web][availability] ou [monitorização de cliente][client] para esta aplicação.) 

    ![Escolha uma aplicação e um recurso.](./media/monitor-performance-live-website-now/appinsights-036-configAIC.png)

4. Reinicie o IIS.

    ![Escolha Reiniciar na parte superior da caixa de diálogo.](./media/monitor-performance-live-website-now/appinsights-036-restart.png)

    O serviço Web é interrompido durante um curto período.

## <a name="customize-monitoring-options"></a>Personalizar opções de monitorização

Ativar o Application Insights adiciona DLLs e o Applicationinsights.config à sua aplicação Web. Pode [editar o ficheiro .config](./configuration-with-applicationinsights-config.md) para alterar algumas das opções.

## <a name="when-you-re-publish-your-app-re-enable-application-insights"></a>Quando voltar a publicar a aplicação, volte a ativar o Application Insights

Antes de voltar a publicar a aplicação, considere [adicionar o Application Insights ao código no Visual Studio][greenbrown]. Irá obter telemetria mais detalhada e a capacidade para escrever telemetria personalizada.

Se pretender voltar a publicar sem adicionar o Application Insights ao código, tenha em atenção de que o processo de implementação poderá eliminar os DLLs e o ApplicationInsights.config do site publicado. Desta forma:

1. Se editou o ApplicationInsights.config, efetue uma cópia do mesmo antes de voltar a publicar a aplicação.
2. Voltar a publicar a aplicação.
3. Voltar a ativar a monitorização do Application Insights. (Utilize o método adequado: o painel de controlo da aplicação Web do Azure ou o Monitor de Estado num anfitrião IIS.)
4. Restabeleça as alterações efetuadas no ficheiro .config.


## <a name="troubleshooting"></a><a name="troubleshoot"></a>Resolução de problemas

### <a name="confirm-a-valid-installation"></a>Confirmar uma instalação válida 

Estes são alguns passos que pode executar para confirmar que a sua instalação foi bem sucedida.

- Confirme que o ficheiro applicationInsights.config está presente no diretório de aplicações-alvo e contém o seu ikey.

- Se suspeitar que os dados estão em falta, pode fazer uma consulta no [Analytics](../log-query/get-started-portal.md) para listar todas as funções em nuvem que estão atualmente a enviar telemetria.
  ```Kusto
  union * | summarize count() by cloud_RoleName, cloud_RoleInstance
  ```

- Se precisar de confirmar que o Application Insights está ligado com sucesso, pode executar [o Cabo Sysinternals](/sysinternals/downloads/handle) numa janela de comando para confirmar que applicationinsights.dll foi carregado pelo IIS.

  ```console
  handle.exe /p w3wp.exe
  ```


### <a name="cant-connect-no-telemetry"></a>Não consegue ligar? Sem telemetria?

* Abra [as portas de envio necessárias](./ip-addresses.md#outgoing-ports) na firewall do seu servidor para permitir que o Monitor de Estado funcione.

### <a name="unable-to-login"></a>Incapaz de iniciar sessão

Se o Status Monitor não conseguir iniciar sessão, faça uma instalação de linha de comando. O Status Monitor tenta iniciar sessão para recolher o seu ikey, mas pode fornecer isto manualmente utilizando o comando:

```powershell
Import-Module 'C:\Program Files\Microsoft Application Insights\Status Monitor\PowerShell\Microsoft.Diagnostics.Agent.StatusMonitor.PowerShell.dll'
Start-ApplicationInsightsMonitoring -Name appName -InstrumentationKey 00000000-000-000-000-0000000
```

### <a name="could-not-load-file-or-assembly-systemdiagnosticsdiagnosticsource"></a>Não foi possível carregar ficheiro ou montagem 'System.Diagnostics.DiagnosticSource'

Pode obter este erro depois de ativar o Application Insights. Isto porque o instalador substitui este dll no seu diretório de contentores.
Para corrigir a atualização do seu web.config:

```xml
<dependentAssembly>
    <assemblyIdentity name="System.Diagnostics.DiagnosticSource" publicKeyToken="cc7b13ffcd2ddd51"/>
    <bindingRedirect oldVersion="0.0.0.0-4.*.*.*" newVersion="4.0.2.1"/>
</dependentAssembly>
```

Estamos a acompanhar esta [questão.](https://github.com/MohanGsk/ApplicationInsights-Home)


### <a name="application-diagnostic-messages"></a>Mensagens de diagnóstico de aplicações

* Abra o Monitor de Estado e selecione a aplicação no painel esquerdo. Verifique se existem quaisquer mensagens de diagnóstico para esta aplicação na secção "Notificações de configuração":

  ![Abra o painel Desempenho para ver o pedido, o tempo de resposta, a dependência e outros dados](./media/monitor-performance-live-website-now/appinsights-status-monitor-diagnostics-message.png)
  
### <a name="detailed-logs"></a>Registos detalhados

* Por predefinição, o Monitor de Estado irá fazer registos de diagnóstico em: `C:\Program Files\Microsoft Application Insights\Status Monitor\diagnostics.log`

* Para obter registos verbosos, modifique o ficheiro config: `C:\Program Files\Microsoft Application Insights\Status Monitor\Microsoft.Diagnostics.Agent.StatusMonitor.exe.config` e adicione `<add key="TraceLevel" value="All" />` ao `appsettings` .
Em seguida, reinicie o monitor de estado.

* Como Status Monitor é uma aplicação .NET, também pode [ativar o rastreio .net adicionando os diagnósticos adequados ao ficheiro config](/dotnet/framework/configure-apps/file-schema/trace-debug/system-diagnostics-element). Por exemplo, em alguns cenários pode ser útil ver o que está a acontecer ao nível da [rede, configurando o rastreio](/dotnet/framework/network-programming/how-to-configure-network-tracing) da rede

### <a name="insufficient-permissions"></a>Permissões insuficientes
  
* No servidor, se vir uma mensagem sobre "permissões insuficientes", experimente o seguinte:
  * No Gestor de IIS, selecione o conjunto de aplicações, abra **Definições Avançadas**, e, em **Modelo de Processos** tenha em atenção a identidade.
  * No painel de controlo de gestão do Computador, adicione esta identidade ao grupo de Utilizadores do Monitor de Desempenho.

### <a name="conflict-with-systems-center-operations-manager"></a>Conflito com o Gestor de Operações do Centro de Sistemas

* Se tiver MMA/SCOM (Systems Center Operations Manager) instalado no servidor, algumas versões podem entrar em conflito. Desinstale o SCOM e o Monitor de Estado, e reinstale as versões mais recentes.

### <a name="failed-or-incomplete-installation"></a>Instalação falhada ou incompleta

Se o Monitor de Estado falhar durante uma instalação, poderá ficar com uma instalação incompleta da qual o Status Monitor não conseguirá recuperar. Isto requer um reset manual.

Elimine qualquer um destes ficheiros encontrados no seu diretório de aplicações:
- Quaisquer DLLs no seu diretório de caixotes começando com "Microsoft.AI". ou "Microsoft.ApplicationInsights".
- Este DLL no seu diretório de caixotes "Microsoft.Web.Infrastructure.dll"
- Este DLL no seu diretório de caixotes "System.Diagnostics.DiagnosticSource.dll"
- No seu diretório de candidaturas remova "App_Data\pacotes"
- No seu diretório de candidaturas remova "applicationinsights.config"


### <a name="additional-troubleshooting"></a>Resolução de Problemas Adicional

* Consulte [a resolução adicional de problemas.][qna]

## <a name="system-requirements"></a>Requisitos de Sistema
Suporte de SO para o Monitor de Estado do Application Insights no Servidor:

* Windows Server 2008
* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

com o mais recente QUADRO SP e .NET 4.5 (Status Monitor é construído nesta versão do quadro)

No lado do cliente: Windows 7, 8, 8.1 e 10, novamente com o .NET Framework 4.5

O Suporte do IIS é: 7, IIS 7.5, 8, 8.5 (o IIS é necessário)

## <a name="automation-with-powershell"></a>Automatização com o PowerShell
Pode iniciar e parar a monitorização com o PowerShell no servidor de IIS.

Primeiro, importe o módulo do Application Insights:

```powershell
Import-Module 'C:\Program Files\Microsoft Application Insights\Status Monitor\PowerShell\Microsoft.Diagnostics.Agent.StatusMonitor.PowerShell.dll'
```

Descubra que aplicações estão a ser monitorizadas:

`Get-ApplicationInsightsMonitoringStatus [-Name appName]`

* `-Name` (Opcional) O nome de uma aplicação Web.
* Apresenta o Estado de Monitorização do Application Insights de cada aplicação Web (ou a aplicação com nome) neste servidor IIS.
* Devolve `ApplicationInsightsApplication` para cada aplicação:

  * `SdkState==EnabledAfterDeployment`: a aplicação está a ser monitorizada e foi instrumentada no momento de execução pela ferramenta Monitor de Estado ou por `Start-ApplicationInsightsMonitoring`.
  * `SdkState==Disabled`: a aplicação não foi instrumentada para o Application Insights. Ou nunca foi instrumentada ou a monitorização no momento de execução foi desativada com a ferramenta Monitor de Estado ou com o `Stop-ApplicationInsightsMonitoring`.
  * `SdkState==EnabledByCodeInstrumentation`: a aplicação foi instrumentada ao adicionar o SDK ao código de origem. O SDK não pode ser atualizado ou parado.
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

   ```output
   Name                      : Default Web Site/WebApp1
   InstrumentationKey        : 00000000-0000-0000-0000-000000000000
   ProfilerState             : ApplicationInsights
   SdkState                  : EnabledAfterDeployment
   SdkVersion                : 1.2.1
   LatestAvailableSdkVersion : 1.2.3
   ```

`Stop-ApplicationInsightsMonitoring [-Name appName | -All]`

* `-Name` O nome de uma aplicação no IIS
* `-All` Para a monitorização de todas as aplicações neste servidor IIS para o qual `SdkState==EnabledAfterDeployment`
* Interrompe a monitorização de aplicações especificadas e remove a instrumentação. Só funciona para aplicações que foram instrumentadas durante a execução com a ferramenta Monitorização de Estado ou Start-ApplicationInsightsApplication. (`SdkState==EnabledAfterDeployment`)
* Devolve ApplicationInsightsApplication.

`Update-ApplicationInsightsMonitoring -Name appName [-InstrumentationKey "0000000-0000-000-000-0000"`]

* `-Name`: o nome de uma aplicação Web no IIS.
* `-InstrumentationKey` (Opcional.) Utilize isto para alterar o recurso para o qual a telemetria da aplicação é enviada.
* Este cmdlet:
  * Atualiza a aplicação nomeada para a versão do SDK mais recentemente transferida para esta máquina. (Só funciona se `SdkState==EnabledAfterDeployment`)
  * Se fornecer uma chave de instrumentação, a aplicação nomeada é reconfigurada para enviar a telemetria para o recurso com essa chave. (Funciona se `SdkState != Disabled`)

`Update-ApplicationInsightsVersion`

* Transfere o SDK mais recente do Application Insights para o servidor.

## <a name="questions-about-status-monitor"></a><a name="questions"></a>Perguntas sobre o Monitor de Estado

### <a name="what-is-status-monitor"></a>O que é o Monitor de Estado?

Uma aplicação de ambiente de trabalho que instala no seu servidor Web do IIS. Ajuda-o a instrumentar e a configurar as aplicações Web. 

### <a name="when-do-i-use-status-monitor"></a>Quando utilizo o Monitor de Estado?

* Para instrumentar qualquer aplicação Web que esteja em execução no seu servidor IIS - mesmo se já estiver em execução.
* Para ativar telemetria adicional para aplicações Web que tenham sido [criadas com o SDK do Application Insights](./asp-net.md) no momento da compilação. 

### <a name="can-i-close-it-after-it-runs"></a>Pode fechá-lo depois de a tarefa ser executada?

Sim. Depois de ter instrumentado os sites que selecionou, pode fechá-lo.

Não recolhe telemetria por si só. Só configura as aplicações Web e define algumas permissões.

### <a name="what-does-status-monitor-do"></a>O que faz o Monitor de Estado?

Quando seleciona uma aplicação Web para o Monitor de Estado instrumentar:

* Descarrega e coloca os conjuntos de Insights de Aplicação e ApplicationInsights.config ficheiro na pasta de binários da aplicação web.
* Permite a criação de perfis CLR para recolher chamadas de dependência.

### <a name="what-version-of-application-insights-sdk-does-status-monitor-install"></a>Que versão do Application Insights SDK instala o Status Monitor?

A partir de agora, o Status Monitor só pode instalar as versões SDK do Application Insights 2.3 ou 2.4. 

A Versão 2.4 do Application Insights SDK é a [última versão a suportar .NET 4.0,](https://github.com/microsoft/ApplicationInsights-dotnet/releases/tag/v2.5.0-beta1) que foi [EOL janeiro de 2016](https://devblogs.microsoft.com/dotnet/support-ending-for-the-net-framework-4-4-5-and-4-5-1/). Portanto, a partir de agora, o Status Monitor pode ser utilizado para instrumentar uma aplicação .NET 4.0. 

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

[Saiba mais](https://apmtips.com/posts/2016-11-18-how-application-insights-status-monitor-not-monitors-dependencies/)

## <a name="video"></a>Vídeo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]

## <a name="download-status-monitor"></a><a name="download"></a>Monitor de Estado de descarregamento

- Utilize o novo [Módulo PowerShell](./status-monitor-v2-overview.md)
- Descarregue e execute o [instalador status Monitor](https://go.microsoft.com/fwlink/?LinkId=506648)
- Ou executar [o Instalador de Plataforma Web](https://www.microsoft.com/web/downloads/platform.aspx) e pesquisar nele para o Monitor de Estado de Insights de Aplicação.

## <a name="next-steps"></a><a name="next"></a>Passos seguintes

Ver a telemetria:

* [Explore as métricas](../platform/metrics-charts.md) para monitorizar o desempenho e a utilização
* [Pesquise eventos e registos][diagnostic] para diagnosticar problemas
* [Análise](../log-query/log-query-overview.md) para obter mais informações avançadas consultas

Adicionar mais telemetria:

* [Criar testes Web][availability] para se certificar de que mantém o seu site em direto.
* [Adicionar telemetria de cliente Web][usage] para ver as exceções de código da página Web e permitir a inserção de chamadas de rastreio.
* [Adicionar o Application Insights SDK ao código][greenbrown] para que possa inserir chamadas de rastreio e de registo

<!--Link references-->

[api]: ./api-custom-events-metrics.md
[availability]: monitor-web-app-availability.md
[client]: ./javascript.md
[diagnostic]: ./diagnostic-search.md
[greenbrown]: ./asp-net.md
[qna]: ../faq.md
[roles]: ./resources-roles-access-control.md
[usage]: ./javascript.md

