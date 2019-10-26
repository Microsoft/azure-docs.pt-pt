---
title: Perguntas frequentes sobre o Aplicativo Azure insights | Microsoft Docs
description: Perguntas frequentes sobre Application Insights.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 09/16/2019
ms.openlocfilehash: 55a096cd4971664e55bb2cfd17f9f8927d7c32f5
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72899512"
---
# <a name="application-insights-frequently-asked-questions"></a>Application Insights: perguntas frequentes

## <a name="configuration-problems"></a>Problemas de configuração
*Estou tendo problemas para configurar meu:*

* [Aplicação .NET](asp-net-troubleshoot-no-data.md)
* [Monitorando um aplicativo já em execução](monitor-performance-live-website-now.md#troubleshoot)
* [Diagnóstico do Azure](../../azure-monitor/platform/diagnostics-extension-to-application-insights.md)
* [Aplicação Web Java](java-troubleshoot.md)

*Não obtenho dados do meu servidor*

* [Definir exceções de firewall](ip-addresses.md)
* [Configurar um servidor ASP.NET](monitor-performance-live-website-now.md)
* [Configurar um servidor Java](java-agent.md)

## <a name="can-i-use-application-insights-with-"></a>Posso usar Application Insights com...?

* [Aplicativos Web em um servidor IIS na VM do Azure ou no conjunto de dimensionamento de máquinas virtuais do Azure](azure-vm-vmss-apps.md)
* [Aplicativos Web em um servidor IIS-local ou em uma VM](asp-net.md)
* [Aplicativos Web Java](java-get-started.md)
* [Aplicações Node.js](nodejs.md)
* [Aplicativos Web no Azure](azure-web-apps.md)
* [Serviços de nuvem no Azure](cloudservices.md)
* [Servidores de aplicativos em execução no Docker](docker.md)
* [Aplicativos Web de página única](javascript.md)
* [SharePoint](sharepoint.md)
* [Aplicativo de área de trabalho do Windows](windows-desktop.md)
* [Outras plataformas](platforms.md)

## <a name="is-it-free"></a>É gratuito?

Sim, para uso experimental. No plano de preços básico, seu aplicativo pode enviar uma determinada concessão de dados a cada mês gratuitamente. A concessão gratuita é grande o suficiente para abranger o desenvolvimento e publicar um aplicativo para um número pequeno de usuários. Você pode definir um limite para evitar que mais de uma quantidade especificada de dados seja processada.

Volumes maiores de telemetria são cobrados por GB. Fornecemos algumas dicas sobre como [limitar seus encargos](pricing.md).

O plano Enterprise incorre em um encargo para cada dia em que cada nó do servidor Web envia telemetria. É adequado se você quiser usar a exportação contínua em uma grande escala.

[Leia o plano de preços](https://azure.microsoft.com/pricing/details/application-insights/).

## <a name="how-much-does-it-cost"></a>Quanto custa?

* Abra a **página uso e custos estimados** em um recurso de Application insights. Há um gráfico de uso recente. Você pode definir um limite de volume de dados, se desejar.
* Abra a [folha de cobrança do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/BillingBlade/Overview) para ver suas listas em todos os recursos.

## <a name="q14"></a>O que Application Insights modificar em meu projeto?
Os detalhes dependem do tipo de projeto. Para um aplicativo Web:

* Adiciona esses arquivos ao seu projeto:

  * ApplicationInsights. config.
  * Ai. js
* Instala estes pacotes NuGet:

  * *API de Application insights* -a API principal
  * *API de Application insights para aplicativos Web* -usada para enviar telemetria do servidor
  * *API de Application insights para aplicativos JavaScript* – usada para enviar telemetria do cliente

    Os pacotes incluem estes assemblies:
  * Microsoft. ApplicationInsights
  * Microsoft. ApplicationInsights. Platform
* Insere itens em:

  * Web.config
  * Packages. config
* (Somente novos projetos – se você [adicionar Application insights a um projeto existente][start], precisará fazer isso manualmente.) Insere trechos no código do cliente e do servidor para inicializá-los com a ID de recurso Application Insights. Por exemplo, em um aplicativo MVC, o código é inserido na página mestra views/Shared/_ layout. cshtml

## <a name="how-do-i-upgrade-from-older-sdk-versions"></a>Como fazer atualizar de versões mais antigas do SDK?
Consulte as [notas de versão](release-notes.md) do SDK apropriado para seu tipo de aplicativo.

## <a name="update"></a>Como posso alterar a qual recurso do Azure meu projeto envia dados?
Em Gerenciador de Soluções, clique com o botão direito do mouse em `ApplicationInsights.config` e escolha **atualizar Application insights**. Você pode enviar os dados para um recurso novo ou existente no Azure. O assistente de atualização altera a chave de instrumentação em ApplicationInsights. config, que determina onde o SDK do servidor envia seus dados. A menos que você desmarque "atualizar tudo", ele também alterará a chave onde ele aparece em suas páginas da Web.

## <a name="what-is-status-monitor"></a>O que é o Monitor de Estado?

Um aplicativo de área de trabalho que você pode usar em seu servidor Web IIS para ajudar a configurar Application Insights em aplicativos Web. Ele não coleta telemetria: você pode interrompê-lo quando não estiver configurando um aplicativo. 

[Saiba mais](monitor-performance-live-website-now.md#questions).

## <a name="what-telemetry-is-collected-by-application-insights"></a>Qual telemetria é coletada por Application Insights?

De aplicativos Web do servidor:

* Pedidos HTTP
* [Dependências](asp-net-dependencies.md). Chamadas para: bancos de dados SQL; Chamadas HTTP para serviços externos; Azure Cosmos DB, tabela, armazenamento de BLOBs e fila. 
* [Exceções](asp-net-exceptions.md) e rastreamentos de pilha.
* [Contadores de desempenho](performance-counters.md) -se você [usar status monitor](monitor-performance-live-website-now.md), [monitoramento do Azure para serviços de aplicativos](azure-web-apps.md), [monitoramento do Azure para VM ou conjunto de dimensionamento de máquinas virtuais](azure-vm-vmss-apps.md)ou o [gravador Application insights coletado](java-collectd.md).
* [Métricas e eventos personalizados](api-custom-events-metrics.md) que você codifica.
* [Logs de rastreamento](asp-net-trace-logs.md) se você configurar o coletor apropriado.

De [páginas da Web do cliente](javascript.md):

* [Contagens de exibição de página](usage-overview.md)
* [Chamadas AJAX](asp-net-dependencies.md) Solicitações feitas de um script em execução.
* Dados de carregamento de exibição de página
* Contagens de usuário e sessão
* [IDs de usuário autenticado](api-custom-events-metrics.md#authenticated-users)

De outras fontes, se você configurá-las:

* [Diagnóstico do Azure](../platform/diagnostics-extension-to-application-insights.md)
* [Importar para o Analytics](../platform/data-collector-api.md)
* [Log Analytics](../platform/data-collector-api.md)
* [Logstash](../platform/data-collector-api.md)

## <a name="can-i-filter-out-or-modify-some-telemetry"></a>Posso filtrar ou modificar alguma telemetria?

Sim, no servidor que você pode escrever:

* Processador de telemetria para filtrar ou adicionar propriedades aos itens de telemetria selecionados antes que eles sejam enviados do seu aplicativo.
* Inicializador de telemetria para adicionar propriedades a todos os itens de telemetria.

Saiba mais sobre o [ASP.net](api-filtering-sampling.md) ou o [Java](java-filter-telemetry.md).

## <a name="how-are-city-countryregion-and-other-geo-location-data-calculated"></a>Como a cidade, o país/região e outros dados de localização geográfica são calculados?

Procuramos o endereço IP (IPv4 ou IPv6) do cliente Web usando [GeoLite2](https://dev.maxmind.com/geoip/geoip2/geolite2/).

* Telemetria do navegador: coletamos o endereço IP do remetente.
* Telemetria do servidor: o módulo Application Insights coleta o endereço IP do cliente. Ele não será coletado se `X-Forwarded-For` estiver definido.
* Para saber mais sobre como o endereço IP e os dados de localização geográfica são coletados no Application Insights consulte este [artigo](https://docs.microsoft.com/azure/azure-monitor/app/ip-collection).


Você pode configurar o `ClientIpHeaderTelemetryInitializer` para obter o endereço IP de um cabeçalho diferente. Em alguns sistemas, por exemplo, ele é movido por um proxy, balanceador de carga ou CDN para `X-Originating-IP`. [Saiba mais](https://apmtips.com/blog/2016/07/05/client-ip-address/).

Você pode [usar Power bi](export-power-bi.md ) para exibir a telemetria de solicitação em um mapa.


## <a name="data"></a>Por quanto tempo os dados são retidos no portal? É seguro?
Dê uma olhada na [retenção e na privacidade dos dados][data].

## <a name="could-personal-data-be-sent-in-the-telemetry"></a>Os dados pessoais podem ser enviados na telemetria?

Isso será possível se o seu código enviar esses dados. Isso também pode acontecer se as variáveis nos rastreamentos de pilha incluírem dados pessoais. Sua equipe de desenvolvimento deve realizar avaliações de risco para garantir que os dados pessoais sejam tratados adequadamente. [Saiba mais sobre a retenção e a privacidade dos dados](data-retention-privacy.md).

**Todos os** octetos do endereço Web do cliente são sempre definidos como 0 depois que os atributos de localização geográfica são pesquisados.

## <a name="my-instrumentation-key-is-visible-in-my-web-page-source"></a>Minha chave de instrumentação está visível na minha origem da página da Web. 

* Essa é uma prática comum em soluções de monitoramento.
* Ele não pode ser usado para roubar seus dados.
* Ele pode ser usado para distorcer seus dados ou disparar alertas.
* Não ouvimos que qualquer cliente tenha tido tais problemas.

Você pode:

* Use duas chaves de instrumentação separadas (recursos de Application Insights separados) para dados de cliente e servidor. Ou
* Gravar um proxy que é executado em seu servidor e fazer com que o cliente Web envie dados por meio desse proxy.

## <a name="post"></a>Como fazer ver dados de POSTAgem na pesquisa de diagnóstico?
Não registramos dados de POSTAgem automaticamente, mas você pode usar uma chamada TrackTrace: Coloque os dados no parâmetro Message. Isso tem um limite de tamanho maior do que os limites em Propriedades de cadeia de caracteres, embora você não possa filtrá-los.

## <a name="should-i-use-single-or-multiple-application-insights-resources"></a>Devo usar um ou vários recursos do Application Insights?

Use um único recurso para todos os componentes ou funções em um único sistema de negócios. Use recursos separados para desenvolvimento, teste e versões de lançamento e para aplicativos independentes.

* [Veja a discussão aqui](separate-resources.md)
* [Exemplo – serviço de nuvem com funções Web e de trabalho](cloudservices.md)

## <a name="how-do-i-dynamically-change-the-instrumentation-key"></a>Como fazer alterar dinamicamente a chave de instrumentação?

* [Discussão aqui](separate-resources.md)
* [Exemplo – serviço de nuvem com funções Web e de trabalho](cloudservices.md)

## <a name="what-are-the-user-and-session-counts"></a>Quais são as contagens de usuário e sessão?

* O SDK do JavaScript define um cookie de usuário no cliente Web, para identificar usuários que retornam e um cookie de sessão para agrupar atividades.
* Se não houver nenhum script do lado do cliente, você poderá [definir cookies no servidor](https://apmtips.com/blog/2016/07/09/tracking-users-in-api-apps/).
* Se um usuário real usa seu site em navegadores diferentes ou usando a navegação no-Private/Incognito ou em computadores diferentes, eles serão contados mais de uma vez.
* Para identificar um usuário conectado entre computadores e navegadores, adicione uma chamada para [setAuthenticatedUserContext ()](api-custom-events-metrics.md#authenticated-users).

## <a name="q17"></a>Habilitei tudo em Application Insights?
| O que você deve ver | Como obtê-lo | Por que você deseja |
| --- | --- | --- |
| Gráficos de disponibilidade |[Testes na Web](monitor-web-app-availability.md) |Sabe que seu aplicativo Web está ativo |
| Desempenho do aplicativo do servidor: tempos de resposta,... |[Adicione Application insights ao seu projeto](asp-net.md) ou [instale o ai status monitor no servidor](monitor-performance-live-website-now.md) (ou escreva seu próprio código para [acompanhar as dependências](api-custom-events-metrics.md#trackdependency)) |Detectar problemas de desempenho |
| Telemetria de dependência |[Instalar o ia Status Monitor no servidor](monitor-performance-live-website-now.md) |Diagnosticar problemas com bancos de dados ou outros componentes externos |
| Obter rastreamentos de pilha de exceções |[Inserir chamadas trackexception em seu código](asp-net-exceptions.md) (mas algumas são relatadas automaticamente) |Detectar e diagnosticar exceções |
| Pesquisar rastreamentos de log |[Adicionar um adaptador de log](asp-net-trace-logs.md) |Diagnosticar exceções, problemas de desempenho |
| Noções básicas de uso do cliente: exibições de página, sessões,... |[Inicializador de JavaScript em páginas da Web](javascript.md) |Análise de utilização |
| Métricas personalizadas do cliente |[Acompanhamento de chamadas em páginas da Web](api-custom-events-metrics.md) |Aperfeiçoar a experiência de utilizador |
| Métricas personalizadas do servidor |[Acompanhamento de chamadas no servidor](api-custom-events-metrics.md) |Business intelligence |

## <a name="why-are-the-counts-in-search-and-metrics-charts-unequal"></a>Por que as contagens em gráficos de pesquisa e métricas são desiguais?

A [amostragem](sampling.md) reduz o número de itens de telemetria (solicitações, eventos personalizados e assim por diante) que são realmente enviados de seu aplicativo para o Portal. Em Pesquisar, você verá o número de itens realmente recebidos. Em gráficos de métricas que exibem uma contagem de eventos, você verá o número de eventos originais que ocorreram. 

Cada item transmitido contém uma propriedade `itemCount` que mostra quantos eventos originais o item representa. Para observar a amostragem na operação, você pode executar esta consulta na análise:

```
    requests | summarize original_events = sum(itemCount), transmitted_events = count()
```


## <a name="automation"></a>Automatização

### <a name="configuring-application-insights"></a>Configurando Application Insights

Você pode [escrever scripts do PowerShell](powershell.md) usando o Azure monitor de recursos para:

* Criar e atualizar Application Insights recursos.
* Defina o plano de preços.
* Obtenha a chave de instrumentação.
* Adicione um alerta de métrica.
* Adicione um teste de disponibilidade.

Você não pode configurar um relatório do Gerenciador de métricas ou configurar a exportação contínua.

### <a name="querying-the-telemetry"></a>Consultando a telemetria

Use a [API REST](https://dev.applicationinsights.io/) para executar consultas de [análise](analytics.md) .

## <a name="how-can-i-set-an-alert-on-an-event"></a>Como posso definir um alerta em um evento?

Os alertas do Azure são apenas em métricas. Crie uma métrica personalizada que cruze um limite de valor sempre que o evento ocorrer. Em seguida, defina um alerta na métrica. Observe que: você receberá uma notificação sempre que a métrica cruzar o limite em qualquer direção; Você não receberá uma notificação até o primeiro cruzamento, não importa se o valor inicial é alto ou baixo; sempre há uma latência de alguns minutos.

## <a name="are-there-data-transfer-charges-between-an-azure-web-app-and-application-insights"></a>Há encargos de transferência de dados entre um aplicativo Web do Azure e Application Insights?

* Se seu aplicativo Web do Azure estiver hospedado em um data center em que há um ponto de extremidade de coleção Application Insights, não haverá encargos. 
* Se não houver nenhum ponto de extremidade de coleta no data center do host, a telemetria do aplicativo incorrerá em [encargos de saída do Azure](https://azure.microsoft.com/pricing/details/bandwidth/).

Isso não depende de onde o recurso de Application Insights está hospedado. Depende apenas da distribuição de nossos pontos de extremidade.

## <a name="can-i-send-telemetry-to-the-application-insights-portal"></a>Posso enviar telemetria para o portal de Application Insights?

Recomendamos que você use nossos SDKs e use a [API do SDK](api-custom-events-metrics.md)do. Há variantes do SDK para várias [plataformas](platforms.md). Esses SDKs manipulam o buffer, a compactação, a limitação, as repetições e assim por diante. No entanto, o [esquema de ingestão](https://github.com/Microsoft/ApplicationInsights-dotnet/tree/develop/Schema/PublicSchema) e o [protocolo de ponto de extremidade](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/ENDPOINT-PROTOCOL.md) são públicos.

## <a name="can-i-monitor-an-intranet-web-server"></a>Posso monitorar um servidor Web de intranet?

Sim, mas será necessário permitir o tráfego para nossos serviços por meio de exceções de firewall ou redirecionamentos de proxy.
- `https://rt.services.visualstudio.com:443` QuickPulse 
- ApplicationIdprovider `https://dc.services.visualstudio.com:443` 
- `https://dc.services.visualstudio.com:443` TelemetryChannel 


Examine nossa lista completa de serviços e endereços IP [aqui](../../azure-monitor/app/ip-addresses.md).

### <a name="firewall-exception"></a>Exceção de firewall

Permitir que seu servidor Web envie telemetria para nossos pontos de extremidade. 

### <a name="gateway-redirect"></a>Redirecionamento de gateway

Encaminhar o tráfego do servidor para um gateway em sua intranet, substituindo pontos de extremidade em sua configuração.
Se essas propriedades de "ponto de extremidade" não estiverem presentes em sua configuração, essas classes usarão os valores padrão mostrados abaixo no exemplo ApplicationInsights. config. 

O gateway deve rotear o tráfego para o endereço base do nosso ponto de extremidade. Em sua configuração, substitua os valores padrão por `http://<your.gateway.address>/<relative path>`.


#### <a name="example-applicationinsightsconfig-with-default-endpoints"></a>Exemplo de ApplicationInsights. config com pontos de extremidade padrão:
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

_Observação ApplicationIdprovider está disponível a partir de v 2.6.0_

### <a name="proxy-passthrough"></a>Passagem de proxy

A passagem de proxy pode ser obtida com a configuração de um nível de máquina ou proxy de nível de aplicativo.
Para obter mais informações, consulte o artigo do dotnet em [defaultProxy](https://docs.microsoft.com/dotnet/framework/configure-apps/file-schema/network/defaultproxy-element-network-settings).
 
 Exemplo de Web. config:
 ```xml
<system.net>
    <defaultProxy>
      <proxy proxyaddress="http://xx.xx.xx.xx:yyyy" bypassonlocal="true"/>
    </defaultProxy>
</system.net>
```
 

## <a name="can-i-run-availability-web-tests-on-an-intranet-server"></a>Posso executar testes da Web de disponibilidade em um servidor de intranet?

Nossos [testes na Web](monitor-web-app-availability.md) são executados em pontos de presença que são distribuídos em todo o mundo. Há duas soluções:

* Porta do firewall-permita solicitações ao seu servidor [a partir da lista longa e alterada de agentes de teste na Web](ip-addresses.md).
* Escreva seu próprio código para enviar solicitações periódicas ao seu servidor de dentro de sua intranet. Você pode executar testes do Visual Studio Web para essa finalidade. O testador pode enviar os resultados para Application Insights usando a API API trackavailability ().

## <a name="how-long-does-it-take-for-telemetry-to-be-collected"></a>Quanto tempo leva para que a telemetria seja coletada?

A maioria dos dados Application Insights tem uma latência de menos de 5 minutos. Alguns dados podem levar mais tempo; geralmente arquivos de log maiores. Para obter mais informações, consulte o [SLA de Application insights](https://azure.microsoft.com/support/legal/sla/application-insights/v1_2/).

## <a name="more-answers"></a>Mais respostas
* [Fórum de Application Insights](https://social.msdn.microsoft.com/Forums/vstudio/en-US/home?forum=ApplicationInsights)

<!--Link references-->

[data]: data-retention-privacy.md
[platforms]: platforms.md
[start]: app-insights-overview.md
[windows]: app-insights-windows-get-started.md
