---
title: FAQ do Azure Application Insights | Documentos da Microsoft
description: Perguntas mais frequentes sobre o Application Insights.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 0e3b103c-6e2a-4634-9e8c-8b85cf5e9c84
ms.service: application-insights
ms.workload: mobile
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 12/17/2018
ms.author: mbullwin
ms.openlocfilehash: 400583f50e898bfc750a387bf0ee83a3147e5006
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57905070"
---
# <a name="application-insights-frequently-asked-questions"></a>O Application Insights: Perguntas Mais Frequentes

## <a name="configuration-problems"></a>Problemas de configuração
*Estou a ter a definição de problemas na minha:*

* [Aplicação .NET](asp-net-troubleshoot-no-data.md)
* [Monitorização de uma aplicação já em execução](monitor-performance-live-website-now.md#troubleshoot)
* [Diagnóstico do Azure](../../azure-monitor/platform/diagnostics-extension-to-application-insights.md)
* [Aplicação Web Java](java-troubleshoot.md)

*Posso obter não existem dados de meu servidor*

* [Exceções de firewall de conjunto](ip-addresses.md)
* [Configurar um servidor do ASP.NET](monitor-performance-live-website-now.md)
* [Configurar um servidor de Java](java-agent.md)

## <a name="can-i-use-application-insights-with-"></a>Posso utilizar o Application Insights com...?

* [Aplicações Web num servidor IIS - no local ou numa VM](asp-net.md)
* [Aplicações web Java](java-get-started.md)
* [Aplicações Node.js](nodejs.md)
* [Aplicações Web no Azure](azure-web-apps.md)
* [Serviços cloud no Azure](cloudservices.md)
* [Servidores de aplicações em execução no Docker](docker.md)
* [Aplicações web de página única](javascript.md)
* [SharePoint](sharepoint.md)
* [Aplicação de ambiente de trabalho do Windows](windows-desktop.md)
* [Outras plataformas](platforms.md)

## <a name="is-it-free"></a>É gratuito?

Sim, para utilização experimental. O plano de preços básico, a aplicação pode enviar um determinado montante de dados por mês sem encargos. A concessão gratuita é suficiente grande para o desenvolvimento de capa e publicar uma aplicação para um pequeno número de utilizadores. Pode definir um limite para impedir que mais do que uma determinada quantidade de dados a ser processado.

Maiores volumes de telemetria são cobrados pelos Gb. Fornecemos algumas dicas sobre como [limitar as cobranças](pricing.md).

O plano Enterprise incorre um encargo para cada dia em que cada nó do servidor web envia telemetria. É adequado se pretender utilizar a exportação contínua de mensagens em fila em grande escala.

[Leia o plano de preços](https://azure.microsoft.com/pricing/details/application-insights/).

## <a name="how-much-is-it-costing"></a>Quanto é ele custos?

* Abra o **utilização e a página de custos estimados** num recurso do Application Insights. Existe um gráfico de utilização recente. Pode definir um limite de volume de dados, se desejar.
* Abra o [painel de faturação do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/BillingBlade/Overview) para ver as faturas em todos os recursos.

## <a name="q14"></a>O que modificar o Application Insights no meu projeto?
Os detalhes dependem do tipo de projeto. Para uma aplicação web:

* Adiciona estes ficheiros ao seu projeto:

  * ApplicationInsights.config.
  * ai.js
* Instala esses pacotes de NuGet:

  * *API do Application Insights* -API principal
  * *API do Application Insights para aplicações Web* - utilizado para enviar telemetria a partir do servidor
  * *API do Application Insights para aplicações JavaScript* - utilizado para enviar telemetria a partir do cliente

    Os pacotes incluem esses assemblies:
  * Microsoft.ApplicationInsights
  * Microsoft.ApplicationInsights.Platform
* Insere itens em:

  * Web.config
  * packages.config
* (Novo projetos apenas - se [adicionar o Application Insights para um projeto existente][start], terá de fazer isso manualmente.) Insere trechos de código o código de cliente e servidor para inicializá-los com o ID de recurso do Application Insights. Por exemplo, numa aplicação MVC, o código é inserido na página mestra Views/Shared/_Layout.cshtml

## <a name="how-do-i-upgrade-from-older-sdk-versions"></a>Como posso atualizar de versões mais antigas do SDK?
Consulte a [notas de versão](release-notes.md) para o SDK adequado ao seu tipo de aplicativo.

## <a name="update"></a>Como posso alterar qual meu projeto envia os dados para o recurso do Azure?
No Solution Explorer, clique com botão direito `ApplicationInsights.config` e escolha **atualização Application Insights**. Pode enviar os dados a um recurso novo ou existente no Azure. O Assistente de atualização altera a chave de instrumentação no applicationinsights. config, que determina onde o servidor SDK envia os dados. A menos que desmarcar "Atualizar tudo", a chave de onde aparece nas suas páginas web também serão alterados.

## <a name="what-is-status-monitor"></a>O que é o Monitor de Estado?

Uma aplicação de ambiente de trabalho que pode utilizar no seu servidor web do IIS para o ajudar a configurar o Application Insights nas aplicações web. Não recolhe telemetria: pode impedi-lo quando não estiver a configurar uma aplicação. 

[Saiba mais](monitor-performance-live-website-now.md#questions).

## <a name="what-telemetry-is-collected-by-application-insights"></a>Que telemetria é recolhida pelo Application Insights?

A partir de aplicações do servidor web:

* Pedidos HTTP
* [Dependências](asp-net-dependencies.md). Chamadas para: Bases de dados do SQL; Chamadas HTTP para serviços externos; Azure Cosmos DB, tabela, armazenamento de BLOBs e filas. 
* [Exceções](asp-net-exceptions.md) e rastreios de pilha.
* [Contadores de desempenho](performance-counters.md) - se de que utiliza [Monitor de estado](monitor-performance-live-website-now.md), [monitorização do Azure](azure-web-apps.md), ou o [escritor do Application Insights recolhidos](java-collectd.md).
* [Métricas e eventos personalizados](api-custom-events-metrics.md) que o código.
* [Registos de rastreio](asp-net-trace-logs.md) se configurar o recoletor apropriado.

Partir [páginas da web cliente](javascript.md):

* [Contagens de visualizações de página](usage-overview.md)
* [Chamadas AJAX](asp-net-dependencies.md) pedidos efetuados a partir de um script em execução.
* Dados de carga de vista de página
* Contagens de utilizadores e de sessão
* [IDs de utilizador autenticado](api-custom-events-metrics.md#authenticated-users)

De outras origens, se configurá-las:

* [Diagnóstico do Azure](../platform/diagnostics-extension-to-application-insights.md)
* [Importá-la para análise](../platform/data-collector-api.md)
* [Log Analytics](../platform/data-collector-api.md)
* [Logstash](../platform/data-collector-api.md)

## <a name="can-i-filter-out-or-modify-some-telemetry"></a>Pode filtrar ou modificar alguma telemetria?

Sim, no servidor de podem escrever:

* Processador de telemetria para filtrar ou adicionar propriedades a itens de telemetria selecionado antes de serem enviados a partir da sua aplicação.
* Inicializador de telemetria para adicionar propriedades a todos os itens de telemetria.

Saiba mais para [ASP.NET](api-filtering-sampling.md) ou [Java](java-filter-telemetry.md).

## <a name="how-are-city-country-and-other-geo-location-data-calculated"></a>Como são calculados as cidade, país e outros dados de localização geográfica?

Verificar o endereço IP (IPv4 ou IPv6) do cliente web usando [GeoLite2](http://dev.maxmind.com/geoip/geoip2/geolite2/).

* Telemetria do browser: Recolhemos o endereço IP do remetente.
* Telemetria do servidor: O módulo do Application Insights recolhe o endereço IP do cliente. Não são coletada se `X-Forwarded-For` está definido.

Pode configurar o `ClientIpHeaderTelemetryInitializer` para tirar o endereço IP a partir de um cabeçalho diferente. Em alguns sistemas, por exemplo, ele será movido por um proxy, CDN para ou Balanceador de carga `X-Originating-IP`. [Saiba mais](https://apmtips.com/blog/2016/07/05/client-ip-address/).

Pode [utilizar o Power BI](export-power-bi.md ) para apresentar a telemetria de pedido num mapa.


## <a name="data"></a>O período de tempo são retidos os dados no portal do? É seguro?
Dê uma olhada [retenção de dados e privacidade][data].

## <a name="could-personal-data-be-sent-in-the-telemetry"></a>Foi possível enviar os dados pessoais na telemetria?

Isto pode acontecer se o código envia esses dados. Também pode acontecer se as variáveis no rastreamentos de pilha incluem dados pessoais. Sua equipe de desenvolvimento deve realizar avaliações de riscos para garantir que os dados pessoais é corretamente processados. [Saiba mais sobre a retenção de dados e privacidade](data-retention-privacy.md).

**Todos os** octetos do endereço de web cliente estão sempre definidos como 0 depois dos atributos de localização geográfica estão a ser pesquisados.

## <a name="my-ikey-is-visible-in-my-web-page-source"></a>Meu iKey é visível na minha página da web de origem. 

* Esta é uma prática comum em soluções de monitorização.
* Não pode ser utilizado para roubar os seus dados.
* Ele poderia ser usado para distorcer os alertas de dados ou acionador.
* Não tivemos que qualquer cliente apresentou esses problemas.

Pode:

* Utilize dois separadas as iKeys (separar recursos do Application Insights), para dados de cliente e servidor. Ou
* Escrever um proxy que é executado no seu servidor e ter o cliente web enviar dados por meio de proxy.

## <a name="post"></a>Como posso ver os dados de POSTAGEM na pesquisa de diagnósticos?
Nós não iniciar sessão automaticamente dados de POSTAGEM, mas pode usar uma chamada de TrackTrace: colocar os dados no parâmetro de mensagem. Isso tem um limite de tamanho maior do que o limite em Propriedades de cadeia de caracteres, embora não pode filtrá-la.

## <a name="should-i-use-single-or-multiple-application-insights-resources"></a>Deve utilizar um ou vários recursos do Application Insights?

Utilize um único recurso para todos os componentes ou funções num sistema único de negócios. Utilize recursos separados para o desenvolvimento, teste e versões de lançamento e para aplicativos independentes.

* [Consulte a discussão aqui](separate-resources.md)
* [Exemplo - serviço cloud com as funções de trabalho e web](cloudservices.md)

## <a name="how-do-i-dynamically-change-the-instrumentation-key"></a>Como posso alterar dinamicamente a chave de instrumentação?

* [Discussão aqui](separate-resources.md)
* [Exemplo - serviço cloud com as funções de trabalho e web](cloudservices.md)

## <a name="what-are-the-user-and-session-counts"></a>Quais são os utilizadores e a sessão de conta?

* O JavaScript SDK define um cookie de utilizador do cliente da web, para identificar os utilizadores de devolução e um cookie de sessão para atividades de grupo.
* Se não houver nenhum script do lado do cliente, pode [definir cookies no servidor](https://apmtips.com/blog/2016/07/09/tracking-users-in-api-apps/).
* Se um utilizador real utiliza o seu site em diferentes navegadores ou ao utilizar a navegação em privado/incognito ou computadores diferentes, em seguida, eles serão contadas mais de uma vez.
* Para identificar um utilizador com sessão iniciada em máquinas e navegadores, adicione uma chamada para [setAuthenticatedUserContext()](api-custom-events-metrics.md#authenticated-users).

## <a name="q17"></a> Eu ativou tudo no Application Insights?
| O que verá | Como obtê-la | Por que deseja que ele |
| --- | --- | --- |
| Gráficos de disponibilidade |[Testes Web](monitor-web-app-availability.md) |Saber a que sua aplicação web está ativo |
| Desempenho de aplicações de servidor: tempos de resposta,... |[Adicionar o Application Insights ao seu projeto](asp-net.md) ou [instalar Monitor de estado de IA no servidor](monitor-performance-live-website-now.md) (ou escrever seu próprio código para [controlar as dependências](api-custom-events-metrics.md#trackdependency)) |Detetar problemas de desempenho |
| Telemetria de dependência |[Instale o Monitor de estado de IA no servidor](monitor-performance-live-website-now.md) |Diagnosticar problemas com bancos de dados ou outros componentes externos |
| Obter rastreamentos de pilha de exceções |[Inserir chamadas de TrackException no seu código](asp-net-exceptions.md) (mas algumas são reportadas automaticamente) |Detetar e diagnosticar exceções |
| Rastreios de registos de pesquisa |[Adicionar uma placa de registo](asp-net-trace-logs.md) |Diagnosticar exceções, problemas de desempenho |
| Noções básicas de utilização do cliente: as vistas de página, sessões,... |[Inicializador de JavaScript em páginas da web](javascript.md) |Análise de utilização |
| Métricas personalizadas de cliente |[Controlo de chamadas em páginas da web](api-custom-events-metrics.md) |Melhorar a experiência do utilizador |
| Métricas personalizadas do servidor |[Chamadas de controlo no servidor](api-custom-events-metrics.md) |Business intelligence |

## <a name="why-are-the-counts-in-search-and-metrics-charts-unequal"></a>Por que as contagens em gráficos de métricas e pesquisa são desiguais?

[Amostragem](sampling.md) reduz o número de itens de telemetria (pedidos, eventos personalizados e assim por diante) que, na verdade, são enviados da sua aplicação para o portal. Na pesquisa, pode ver o número de itens, na verdade, recebidos. Gráficos de métricas que mostra uma contagem de eventos, verá o número de eventos originais que ocorreram. 

Cada item que é transmitida executa um `itemCount` propriedade que mostra o número de eventos original esse item representa. Para observar a amostragem em operação, pode executar esta consulta do Analytics:

```
    requests | summarize original_events = sum(itemCount), transmitted_events = count()
```


## <a name="automation"></a>Automatização

### <a name="configuring-application-insights"></a>Configuração do Application Insights

Pode [escrever PowerShell scripts](powershell.md) usando o Monitor de recursos do Azure para:

* Criar e atualizar recursos do Application Insights.
* Defina o plano de preços.
* Obtenha a chave de instrumentação.
* Adicione um alerta de métrica.
* Adicione um teste de disponibilidade.

Não é possível configurar um relatório do Explorador de métricas ou configurar a exportação contínua.

### <a name="querying-the-telemetry"></a>Consultar a telemetria

Utilize o [REST API](https://dev.applicationinsights.io/) para ser executado [Analytics](analytics.md) consultas.

## <a name="how-can-i-set-an-alert-on-an-event"></a>Como definir um alerta num evento?

Alertas do Azure são apenas em métricas. Crie uma métrica personalizada que ultrapassar um limiar de valor sempre que o seu evento ocorre. Em seguida, defina um alerta a métrica. Tenha em atenção que: vai receber uma notificação sempre que a métrica ultrapassa o limiar em ambas as direções; não receberá uma notificação até o cruzamento primeiro, não importa se o valor inicial é alta ou baixa; há sempre uma latência de alguns minutos.

## <a name="are-there-data-transfer-charges-between-an-azure-web-app-and-application-insights"></a>Existem custos de transferência de dados entre uma aplicação web do Azure e o Application Insights?

* Se a aplicação web do Azure estiver alojada num centro de dados onde existe um ponto de final de recolha do Application Insights, não existe nenhum custo associado. 
* Se não existe nenhum ponto de final de recolha no seu centro de dados do anfitrião, telemetria da sua aplicação estará sujeita [Azure custos de saída](https://azure.microsoft.com/pricing/details/bandwidth/).

Isso não depende de onde está alojado o recurso do Application Insights. Depende apenas a distribuição dos nossos pontos de extremidade.

## <a name="can-i-send-telemetry-to-the-application-insights-portal"></a>Pode enviar telemetria ao portal do Application Insights?

Recomendamos que utilize os nossos SDKs e utiliza o [API do SDK](api-custom-events-metrics.md). Há variantes do SDK para várias [plataformas](platforms.md). Estes SDKs lidar com colocação em memória intermédia, compressão, limitação, repetições e assim por diante. No entanto, o [esquema de ingestão](https://github.com/Microsoft/ApplicationInsights-dotnet/tree/develop/Schema/PublicSchema) e [protocolo do ponto de extremidade](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/ENDPOINT-PROTOCOL.md) sejam públicos.

## <a name="can-i-monitor-an-intranet-web-server"></a>Pode monitorizar um servidor de web da intranet?

Sim, mas será necessário permitir o tráfego para os nossos serviços por exceções de firewall ou proxy redirecionamentos.
- QuickPulse `https://rt.services.visualstudio.com:443` 
- ApplicationIdProvider `https://dc.services.visualstudio.com:443` 
- TelemetryChannel `https://dc.services.visualstudio.com:443` 


Consulte a nossa lista completa de serviços e endereços IP [aqui](../../azure-monitor/app/ip-addresses.md).

### <a name="firewall-exception"></a>Exceção de firewall

Permitir que o seu servidor web para enviar telemetria para nossos pontos de extremidade. 

### <a name="gateway-redirect"></a>Redirecionamento de gateway

Encaminhar o tráfego do seu servidor para um gateway na sua intranet, substituindo os pontos finais na sua configuração.
Se estas propriedades de "Endpoint" não estão presentes na sua configuração, essas classes irão utilizar os valores predefinidos mostrados no exemplo applicationinsights. config. 

O gateway deve encaminhar o tráfego para o endereço base do nosso ponto de extremidade. Na sua configuração, substitua os valores predefinidos com `http://<your.gateway.address>/<relative path>`.


#### <a name="example-applicationinsightsconfig-with-default-endpoints"></a>Applicationinsights de exemplo com pontos de extremidade padrão:
```xml
<ApplicationInsights>
  ...
  <TelemetryModules>
    <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse.QuickPulseTelemetryModule, Microsoft.AI.PerfCounterCollector">
      <QuickPulseServiceEndpoint>https://rt.services.visualstudio.com/QuickPulseService.svc</QuickPulseServiceEndpoint>
    </Add>
  </TelemetryModules>
    ...
  <TelemetryChannel>
     <EndpointAddress>https://dc.services.visualstudio.com/v2/track</EndpointAddress>
  </TelemetryChannel>
  ...
  <ApplicationIdProvider Type="Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId.ApplicationInsightsApplicationIdProvider, Microsoft.ApplicationInsights">
    <ProfileQueryEndpoint>https://dc.services.visualstudio.com/api/profiles/{0}/appId</ProfileQueryEndpoint>
  </ApplicationIdProvider>
  ...
</ApplicationInsights>
```

_Nota ApplicationIdProvider está disponível a partir de v2.6.0_

### <a name="proxy-passthrough"></a>Pass-through do proxy

Pass-through do proxy pode ser alcançado ao configurar um nível de máquina ou o nível de aplicativo proxy.
Para obter mais informações consulte o artigo do dotnet sobre [DefaultProxy](https://docs.microsoft.com/dotnet/framework/configure-apps/file-schema/network/defaultproxy-element-network-settings).
 
 Web. config de exemplo:
 ```xml
<system.net>
    <defaultProxy>
      <proxy proxyaddress="http://xx.xx.xx.xx:yyyy" bypassonlocal="true"/>
    </defaultProxy>
</system.net>
```
 

## <a name="can-i-run-availability-web-tests-on-an-intranet-server"></a>Pode executar testes web de disponibilidade num servidor de intranet?

Nosso [testes web](monitor-web-app-availability.md) executar em pontos de presença que são distribuídos em todo o mundo. Existem duas soluções:

* Porta de firewall - permitir que os pedidos para o servidor em [longo e alterável lista de agentes de teste web](ip-addresses.md).
* Escreva seu próprio código para enviar pedidos periódicos para o servidor em dentro de sua intranet. Pode executar testes web do Visual Studio para esta finalidade. O testador pode enviar os resultados para o Application Insights com a API de trackavailability ().

## <a name="how-long-does-it-take-for-telemetry-to-be-collected"></a>Quanto tempo demora para telemetria recolha?

A maioria dos dados do Application Insights tem uma latência de menos de 5 minutos. Alguns dados podem demorar mais tempo; Normalmente, ficheiros de registo maiores. Para obter mais informações, consulte a [SLA do Application Insights](https://azure.microsoft.com/support/legal/sla/application-insights/v1_2/).

## <a name="more-answers"></a>Obter mais respostas
* [Fórum do Application Insights](https://social.msdn.microsoft.com/Forums/vstudio/en-US/home?forum=ApplicationInsights)

<!--Link references-->

[data]: data-retention-privacy.md
[platforms]: platforms.md
[start]: app-insights-overview.md
[windows]: app-insights-windows-get-started.md
