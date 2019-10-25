---
title: Monitorar o desempenho do aplicativo Web Java no Linux – Azure | Microsoft Docs
description: Monitoramento de desempenho de aplicativo estendido do seu site Java com o plug-in coletado para Application Insights.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 03/14/2019
ms.openlocfilehash: 6c74684ac45a040be154a1e6406c1e7a5e0dd253
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72817146"
---
# <a name="collectd-linux-performance-metrics-in-application-insights"></a>coletados: métricas de desempenho do Linux no Application Insights


Para explorar as métricas de desempenho do sistema Linux no [Application insights](../../azure-monitor/app/app-insights-overview.md), instale o [collectd](https://collectd.org/), junto com seu plug-in de Application insights. Essa solução de software livre reúne várias estatísticas de rede e de sistema.

Normalmente, você usará collectd se já tiver [instrumentado seu serviço Web Java com Application insights][java]. Ele fornece mais dados para ajudá-lo a melhorar o desempenho do seu aplicativo ou diagnosticar problemas. 

## <a name="get-your-instrumentation-key"></a>Obter sua chave de instrumentação
Na [portal do Microsoft Azure](https://portal.azure.com), abra o recurso [Application insights](../../azure-monitor/app/app-insights-overview.md) em que você deseja que os dados sejam exibidos. (Ou [criar um novo recurso](../../azure-monitor/app/create-new-resource.md ).)

Faça uma cópia da chave de instrumentação, que identifica o recurso.

![Procurar tudo, abrir o recurso e, em seguida, na lista suspensa Essentials, selecionar e copiar a chave de instrumentação](./media/java-collectd/instrumentation-key-001.png)

## <a name="install-collectd-and-the-plug-in"></a>Instalar coletados e o plug-in
Em seus computadores de servidor Linux:

1. Instale a versão do [Collected](https://collectd.org/) 5.4.0 ou posterior.
2. Baixe o [plug-in de gravador coletado Application insights](https://aka.ms/aijavasdk). Observe o número de versão.
3. Copie o JAR do plug-in em `/usr/share/collectd/java`.
4. Editar `/etc/collectd/collectd.conf`:
   * Verifique se [o plug-in Java](https://collectd.org/wiki/index.php/Plugin:Java) está habilitado.
   * Atualize o JVMArg para o Java. class. Path para incluir o JAR a seguir. Atualize o número de versão para corresponder ao que você baixou:
   * `/usr/share/collectd/java/applicationinsights-collectd-1.0.5.jar`
   * Adicione este trecho de código usando a chave de instrumentação do seu recurso:

```XML

     LoadPlugin "com.microsoft.applicationinsights.collectd.ApplicationInsightsWriter"
     <Plugin ApplicationInsightsWriter>
        InstrumentationKey "Your key"
     </Plugin>
```

Aqui está parte de um arquivo de configuração de exemplo:

```XML

    ...
    # collectd plugins
    LoadPlugin cpu
    LoadPlugin disk
    LoadPlugin load
    ...

    # Enable Java Plugin
    LoadPlugin "java"

    # Configure Java Plugin
    <Plugin "java">
      JVMArg "-verbose:jni"
      JVMArg "-Djava.class.path=/usr/share/collectd/java/applicationinsights-collectd-1.0.5.jar:/usr/share/collectd/java/collectd-api.jar"

      # Enabling Application Insights plugin
      LoadPlugin "com.microsoft.applicationinsights.collectd.ApplicationInsightsWriter"

      # Configuring Application Insights plugin
      <Plugin ApplicationInsightsWriter>
        InstrumentationKey "12345678-1234-1234-1234-123456781234"
      </Plugin>

      # Other plugin configurations ...
      ...
    </Plugin>
    ...
```

Configure outros [plug-ins coletados](https://collectd.org/wiki/index.php/Table_of_Plugins), que podem coletar vários dados de fontes diferentes.

Reinicialização coletada de acordo com seu [manual](https://collectd.org/wiki/index.php/First_steps).

## <a name="view-the-data-in-application-insights"></a>Exibir os dados em Application Insights
No recurso Application Insights, abra [métricas e adicione gráficos][metrics], selecionando as métricas que você deseja ver na categoria personalizada.

Por padrão, as métricas são agregadas em todos os computadores host dos quais as métricas foram coletadas. Para exibir as métricas por host, na folha detalhes do gráfico, ative o agrupamento e, em seguida, escolha agrupar por coletar-host.

## <a name="to-exclude-upload-of-specific-statistics"></a>Para excluir o carregamento de estatísticas específicas
Por padrão, o plug-in Application Insights envia todos os dados coletados por todos os plug-ins de ' leitura ' conunidos habilitados. 

Para excluir dados de plugins ou fontes de dados específicas:

* Edite o arquivo de configuração. 
* Em `<Plugin ApplicationInsightsWriter>`, adicione linhas de diretiva como esta:

| Register | Efeito |
| --- | --- |
| `Exclude disk` |Excluir todos os dados coletados pelo plug-in `disk` |
| `Exclude disk:read,write` |Exclua as fontes chamadas `read` e `write` do plug-in `disk`. |

Diretivas separadas com uma nova linha.

## <a name="problems"></a>Problemas?
*Não vejo dados no portal*

* Abra a [pesquisa][diagnostic] para ver se os eventos brutos chegaram. Às vezes, eles demoram mais para aparecer no Metrics Explorer.
* Talvez seja necessário [definir exceções de firewall para dados de saída](../../azure-monitor/app/ip-addresses.md)
* Habilite o rastreamento no plug-in Application Insights. Adicione esta linha dentro do `<Plugin ApplicationInsightsWriter>`:
  * `SDKLogger true`
* Abra um terminal e comece a ser coletado no modo detalhado para ver os problemas que ele está relatando:
  * `sudo collectd -f`

## <a name="known-issue"></a>Problema conhecido

O plug-in de Application Insights gravação é incompatível com determinados plug-ins de leitura. Alguns plugins às vezes enviam "NaN" onde o plug-in de Application Insights espera um número de ponto flutuante.

Sintoma: o log coletado mostra erros que incluem "ia:... SyntaxError: token N inesperado ".

Solução alternativa: exclua os dados coletados pelo problema gravar plug-ins. 

<!--Link references-->

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[apiexceptions]: ../../azure-monitor/app/api-custom-events-metrics.md#track-exception
[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[eclipse]: app-insights-java-eclipse.md
[java]: java-get-started.md
[javalogs]: java-trace-logs.md
[metrics]: ../../azure-monitor/app/metrics-explorer.md


