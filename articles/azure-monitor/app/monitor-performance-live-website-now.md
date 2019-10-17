---
title: Monitorizar uma aplicação Web ASP.NET com o Application Insights do Azure | Microsoft Docs
description: Monitorize o desempenho de um site sem o reimplementar. Funciona com aplicativos Web ASP.NET hospedados localmente ou em VMs.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 769a5ea4-a8c6-4c18-b46c-657e864e24de
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 08/26/2019
ms.author: mbullwin
ms.openlocfilehash: e7d69e2258036318b736f245f9e3aec3cf5f54de
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2019
ms.locfileid: "72389930"
---
# <a name="instrument-web-apps-at-runtime-with-application-insights-codeless-attach"></a>Instrumentar aplicativos Web em tempo de execução com Application Insights anexação sem código

> [!IMPORTANT]
> O Status Monitor não é mais recomendado para uso. Ele foi substituído pelo agente de Application Insights de Azure Monitor (anteriormente denominado Status Monitor v2). Consulte nossa documentação para [implantações de servidor local](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-overview) ou [máquinas virtuais do Azure e implantações do conjunto de dimensionamento de máquinas virtuais](https://docs.microsoft.com/azure/azure-monitor/app/azure-vm-vmss-apps).

Pode instrumentar uma aplicação Web em direto com o Azure Application Insights, sem ter de modificar ou voltar a implementar o seu código. Precisará de uma subscrição do [Microsoft Azure](https://azure.com).

O Status Monitor é usado para instrumentar um aplicativo .NET hospedado no IIS localmente ou em uma VM.

- Se seu aplicativo for implantado na VM do Azure ou no conjunto de dimensionamento de máquinas virtuais do Azure, siga [estas instruções](azure-vm-vmss-apps.md).
- Se seu aplicativo for implantado nos serviços de aplicativo do Azure, siga [estas instruções](azure-web-apps.md).
- Se seu aplicativo for implantado em uma VM do Azure, você poderá alternar Application Insights monitoramento no painel de controle do Azure.
- (Também há artigos separados sobre instrumentação de [serviços de nuvem do Azure](../../azure-monitor/app/cloudservices.md).)


![Captura de tela dos grafos de visão geral do App insights contendo informações sobre solicitações com falha, tempo de resposta do servidor e solicitações do servidor](./media/monitor-performance-live-website-now/overview-graphs.png)

Você tem a opção de duas rotas para aplicar Application Insights aos seus aplicativos Web .NET:

* **Tempo de compilação:** [adicione o SDK do Application insights][greenbrown] ao código do aplicativo Web.
* **Tempo de execução:** instrumente a sua aplicação Web no servidor, conforme descrito abaixo, sem a reconstruir e implementar novamente o código.

> [!NOTE]
> Se você usar a instrumentação de tempo de compilação, a instrumentação de tempo de execução não funcionará mesmo se estiver ativada.

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
2. Se o Monitor de Estado do Application Insights ainda não estiver instalado, [transfira e execute o instalador](#download)
3. No Monitor de Estado, selecione a aplicação Web instalada ou o Web site que pretende monitorizar. Inicie sessão com as credenciais do Azure.

    Configure o recurso onde pretende ver os resultados no portal do Application Insights. (Normalmente, é melhor criar um novo recurso. Selecione um recurso existente se você já tiver [testes da Web][availability] ou [monitoramento de cliente][client] para este aplicativo.) 

    ![Escolha uma aplicação e um recurso.](./media/monitor-performance-live-website-now/appinsights-036-configAIC.png)

4. Reinicie o IIS.

    ![Escolha Reiniciar na parte superior da caixa de diálogo.](./media/monitor-performance-live-website-now/appinsights-036-restart.png)

    O serviço Web é interrompido durante um curto período.

## <a name="customize-monitoring-options"></a>Personalizar opções de monitorização

Ativar o Application Insights adiciona DLLs e o Applicationinsights.config à sua aplicação Web. Pode [editar o ficheiro .config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) para alterar algumas das opções.

## <a name="when-you-re-publish-your-app-re-enable-application-insights"></a>Quando voltar a publicar a aplicação, volte a ativar o Application Insights

Antes de publicar novamente seu aplicativo, considere [adicionar Application insights ao código no Visual Studio][greenbrown]. Irá obter telemetria mais detalhada e a capacidade para escrever telemetria personalizada.

Se pretender voltar a publicar sem adicionar o Application Insights ao código, tenha em atenção de que o processo de implementação poderá eliminar os DLLs e o ApplicationInsights.config do site publicado. Desta forma:

1. Se editou o ApplicationInsights.config, efetue uma cópia do mesmo antes de voltar a publicar a aplicação.
2. Voltar a publicar a aplicação.
3. Voltar a ativar a monitorização do Application Insights. (Utilize o método adequado: o painel de controlo da aplicação Web do Azure ou o Monitor de Estado num anfitrião IIS.)
4. Restabeleça as alterações efetuadas no ficheiro .config.


## <a name="troubleshoot"></a>Resolução de Problemas

### <a name="confirm-a-valid-installation"></a>Confirmar uma instalação válida 

Essas são algumas etapas que você pode executar para confirmar se a instalação foi bem-sucedida.

- Confirme se o arquivo applicationInsights. config está presente no diretório do aplicativo de destino e contém seu iKey.

- Se você suspeitar de que os dados estão ausentes, poderá executar uma consulta simples no [Analytics](../log-query/get-started-portal.md) para listar todas as funções de nuvem que atualmente estão enviando telemetria.
  ```Kusto
  union * | summarize count() by cloud_RoleName, cloud_RoleInstance
  ```

- Se você precisar confirmar se o Application Insights foi anexado com êxito, poderá executar o [identificador do Sysinternals](https://docs.microsoft.com/sysinternals/downloads/handle) em uma janela de comando para confirmar que o applicationinsights. dll foi carregado pelo IIS.
  ```cmd
  handle.exe /p w3wp.exe
  ```


### <a name="cant-connect-no-telemetry"></a>Não consegue ligar? Sem telemetria?

* Abra [as portas de envio necessárias](../../azure-monitor/app/ip-addresses.md#outgoing-ports) na firewall do seu servidor para permitir que o Monitor de Estado funcione.

### <a name="unable-to-login"></a>Não é possível fazer logon

* Se Status Monitor não puder fazer logon, faça uma instalação de linha de comando em vez disso. Status Monitor tenta fazer logon para coletar seu iKey, mas você pode fornecer isso manualmente usando o comando:

```powershell
Import-Module 'C:\Program Files\Microsoft Application Insights\Status Monitor\PowerShell\Microsoft.Diagnostics.Agent.StatusMonitor.PowerShell.dll'
Start-ApplicationInsightsMonitoring -Name appName -InstrumentationKey 00000000-000-000-000-0000000
```

### <a name="could-not-load-file-or-assembly-systemdiagnosticsdiagnosticsource"></a>Não foi possível carregar o arquivo ou o assembly ' System. Diagnostics. Diagnosticname '

Você pode receber esse erro depois de habilitar Application Insights. Isso ocorre porque o instalador substitui essa dll no diretório bin.
Para corrigir a atualização de seu Web. config:

```xml
<dependentAssembly>
    <assemblyIdentity name="System.Diagnostics.DiagnosticSource" publicKeyToken="cc7b13ffcd2ddd51"/>
    <bindingRedirect oldVersion="0.0.0.0-4.*.*.*" newVersion="4.0.2.1"/>
</dependentAssembly>
```

Estamos acompanhando esse problema [aqui](https://github.com/Microsoft/ApplicationInsights-Home/issues/301).


### <a name="application-diagnostic-messages"></a>Mensagens de diagnóstico de aplicativo

* Abra o Monitor de Estado e selecione a aplicação no painel esquerdo. Verifique se existem quaisquer mensagens de diagnóstico para esta aplicação na secção "Notificações de configuração":

  ![Abra o painel Desempenho para ver o pedido, o tempo de resposta, a dependência e outros dados](./media/monitor-performance-live-website-now/appinsights-status-monitor-diagnostics-message.png)
  
### <a name="detailed-logs"></a>Registos detalhados

* Por padrão Status Monitor gerará logs de diagnóstico em: `C:\Program Files\Microsoft Application Insights\Status Monitor\diagnostics.log`

* Para gerar logs detalhados, modifique o arquivo de configuração: `C:\Program Files\Microsoft Application Insights\Status Monitor\Microsoft.Diagnostics.Agent.StatusMonitor.exe.config` e adicione `<add key="TraceLevel" value="All" />` ao `appsettings`.
Em seguida, reinicie o monitor de status.

* Como Status Monitor é um aplicativo .NET, você também pode habilitar [o rastreamento do .net adicionando o diagnóstico apropriado ao arquivo de configuração](https://docs.microsoft.com/dotnet/framework/configure-apps/file-schema/trace-debug/system-diagnostics-element). Por exemplo, em alguns cenários, pode ser útil ver o que está acontecendo no nível da rede [Configurando o rastreamento de rede](https://docs.microsoft.com/dotnet/framework/network-programming/how-to-configure-network-tracing)

### <a name="insufficient-permissions"></a>Permissões insuficientes
  
* No servidor, se vir uma mensagem sobre "permissões insuficientes", experimente o seguinte:
  * No Gestor de IIS, selecione o conjunto de aplicações, abra **Definições Avançadas**, e, em **Modelo de Processos** tenha em atenção a identidade.
  * No painel de controlo de gestão do Computador, adicione esta identidade ao grupo de Utilizadores do Monitor de Desempenho.

### <a name="conflict-with-systems-center-operations-manager"></a>Conflito com o System Center Operations Manager

* Se tiver MMA/SCOM (Systems Center Operations Manager) instalado no servidor, algumas versões podem entrar em conflito. Desinstale o SCOM e o Monitor de Estado, e reinstale as versões mais recentes.

### <a name="failed-or-incomplete-installation"></a>Instalação com falha ou incompleta

Se Status Monitor falhar durante uma instalação, você poderá ter uma instalação incompleta da qual Status Monitor não pode se recuperar. Isso exigirá uma redefinição manual.

Exclua qualquer um desses arquivos encontrados no diretório do aplicativo:
- Qualquer DLL no diretório bin, começando com "Microsoft.AI". ou "Microsoft. ApplicationInsights.".
- Essa DLL no diretório bin "Microsoft. Web. Infrastructure. dll"
- Essa DLL no diretório bin "System. Diagnostics. Diagnosticname. dll"
- No diretório do aplicativo, remova "App_Data\packages"
- No diretório do aplicativo, remova "applicationinsights. config"


### <a name="additional-troubleshooting"></a>Resolução de Problemas Adicional

* Consulte [solução de problemas][qna]adicional.

## <a name="system-requirements"></a>Requisitos de Sistema
Suporte de SO para o Monitor de Estado do Application Insights no Servidor:

* Windows Server 2008
* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

com o SP mais recente e o .NET Framework 4,5 (Status Monitor é criado nesta versão da estrutura)

No lado do cliente: Windows 7, 8, 8.1 e 10, novamente com o .NET Framework 4.5

O Suporte do IIS é: 7, IIS 7.5, 8, 8.5 (o IIS é necessário)

## <a name="automation-with-powershell"></a>Automatização com o PowerShell
Pode iniciar e parar a monitorização com o PowerShell no servidor de IIS.

Primeiro, importe o módulo do Application Insights:

`Import-Module 'C:\Program Files\Microsoft Application Insights\Status Monitor\PowerShell\Microsoft.Diagnostics.Agent.StatusMonitor.PowerShell.dll'`

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

* `-Name`: o nome de uma aplicação Web no IIS.
* `-InstrumentationKey` (opcional.) Use isso para alterar o recurso para o qual a telemetria do aplicativo é enviada.
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

* Baixa e coloca o Application Insights assemblies e o arquivo ApplicationInsights. config na pasta de binários do aplicativo Web.
* Permite a criação de perfis CLR para recolher chamadas de dependência.

### <a name="what-version-of-application-insights-sdk-does-status-monitor-install"></a>Qual versão do SDK do Application Insights Status Monitor instalar?

A partir de agora, Status Monitor só pode instalar Application Insights versões 2,3 ou 2,4 do SDK. 

O SDK do Application Insights versão 2,4 é a [última versão para dar suporte ao .net 4,0](https://github.com/microsoft/ApplicationInsights-dotnet/releases/tag/v2.5.0-beta1) , que foi o [EOL de janeiro de 2016](https://devblogs.microsoft.com/dotnet/support-ending-for-the-net-framework-4-4-5-and-4-5-1/). Portanto, a partir de agora Status Monitor pode ser usado para instrumentar um aplicativo .NET 4,0. 

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

## <a name="download"></a>Baixar Status Monitor

- Usar o novo [módulo do PowerShell](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-overview)
- Baixar e executar o [instalador do status monitor](https://go.microsoft.com/fwlink/?LinkId=506648)
- Ou execute [Web Platform Installer](https://www.microsoft.com/web/downloads/platform.aspx) e pesquise por Application insights status monitor.

## <a name="next"></a>Passos seguintes

Ver a telemetria:

* [Explore as métricas](../../azure-monitor/app/metrics-explorer.md) para monitorizar o desempenho e a utilização
* [Pesquisar eventos e logs][diagnostic] para diagnosticar problemas
* [Análise](../../azure-monitor/app/analytics.md) para obter mais informações avançadas consultas

Adicionar mais telemetria:

* [Crie testes da Web][availability] para garantir que seu site permaneça ativo.
* [Adicione telemetria de cliente Web][usage] para ver exceções do código de página da Web e para permitir que você insira chamadas de rastreamento.
* [Adicione Application insights SDK ao seu código][greenbrown] para que você possa inserir chamadas de rastreamento e log

<!--Link references-->

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[availability]: monitor-web-app-availability.md
[client]: ../../azure-monitor/app/javascript.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[greenbrown]: ../../azure-monitor/app/asp-net.md
[qna]: ../../azure-monitor/app/troubleshoot-faq.md
[roles]: ../../azure-monitor/app/resources-roles-access-control.md
[usage]: ../../azure-monitor/app/javascript.md
