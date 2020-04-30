---
title: Monitor java web performance em Linux - Azure Microsoft Docs
description: Monitorização alargada do desempenho da aplicação do seu website java com o plug-in CollectD para Insights de Aplicação.
ms.topic: conceptual
ms.date: 03/14/2019
ms.openlocfilehash: 62a723dad7e9f6c2bfdabde159968d507d2d5d41
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81537530"
---
# <a name="collectd-linux-performance-metrics-in-application-insights"></a>colecionado: Métricas de desempenho do Linux em Insights de Aplicação


Para explorar as métricas de desempenho do sistema Linux em [Application Insights,](../../azure-monitor/app/app-insights-overview.md)instale-se [colecionado,](https://collectd.org/)juntamente com o plug-in application Insights. Esta solução de código aberto reúne várias estatísticas de sistemas e redes.

Normalmente, utilizará o recolhido se já [tiver instrumentado o seu serviço web Java com Insights][java]de Aplicação . Dá-lhe mais dados para o ajudar a melhorar o desempenho da sua aplicação ou diagnosticar problemas. 

## <a name="get-your-instrumentation-key"></a>Obtenha a sua chave de instrumentação
No [portal Microsoft Azure,](https://portal.azure.com)abra o recurso [Application Insights](../../azure-monitor/app/app-insights-overview.md) onde pretende que os dados apareçam. (Ou [criar um novo recurso](../../azure-monitor/app/create-new-resource.md ).)

Pegue uma cópia da chave de instrumentação, que identifica o recurso.

![Navegue por todos, abra o seu recurso e, em seguida, no Essencial drop-down, selecione e copie a Chave de Instrumentação](./media/java-collectd/instrumentation-key-001.png)

## <a name="install-collectd-and-the-plug-in"></a>Instale colecionado e o plug-in
Nas suas máquinas de servidorLinux:

1. Instale a versão 5.4.0 [ou](https://collectd.org/) posterior.
2. Descarregue o [plugin de escritor colecionado Application Insights](https://github.com/microsoft/ApplicationInsights-Java/tree/master/collectd/src/main/java/com/microsoft/applicationinsights/collectd/internal). Repare maqueo o número da versão.
3. Copie o PLUGIN JAR em `/usr/share/collectd/java`.
4. Editar: `/etc/collectd/collectd.conf`
   * Certifique-se de que [o plugin Java](https://collectd.org/wiki/index.php/Plugin:Java) está ativado.
   * Atualize o JVMArg para o caminho java.class.para incluir o seguinte JAR. Atualize o número da versão para corresponder ao que descarregou:
   * `/usr/share/collectd/java/applicationinsights-collectd-1.0.5.jar`
   * Adicione este corte, utilizando a Chave de Instrumentação do seu recurso:

```XML

     LoadPlugin "com.microsoft.applicationinsights.collectd.ApplicationInsightsWriter"
     <Plugin ApplicationInsightsWriter>
        InstrumentationKey "Your key"
     </Plugin>
```

Aqui está uma parte de um ficheiro de configuração de amostra:

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

Configure outros [plugins recolhidos,](https://collectd.org/wiki/index.php/Table_of_Plugins)que podem recolher vários dados de diferentes fontes.

Reiniciar recolhido de acordo com o seu [manual](https://collectd.org/wiki/index.php/First_steps).

## <a name="view-the-data-in-application-insights"></a>Ver os dados em Insights de Aplicação
No seu recurso Application Insights, abra [métricas e adicione gráficos,][metrics]selecionando as métricas que pretende ver na categoria Custom.

Por padrão, as métricas são agregadas em todas as máquinas hospedeiras a partir das quais as métricas foram recolhidas. Para ver as métricas por anfitrião, na lâmina de detalhes do Gráfico, ligue o Agrupamento e, em seguida, escolha agrupar-se por CollectD-Host.

## <a name="to-exclude-upload-of-specific-statistics"></a>Excluir o upload de estatísticas específicas
Por predefinição, o plugin Application Insights envia todos os dados recolhidos por todos os plugins 'read' 'read' recolhidos ativados. 

Para excluir dados de plugins ou fontes de dados específicas:

* Editar o ficheiro de configuração. 
* Em `<Plugin ApplicationInsightsWriter>`, adicione linhas diretivas como esta:

| Diretiva | Efeito |
| --- | --- |
| `Exclude disk` |Excluir todos os `disk` dados recolhidos pelo plugin |
| `Exclude disk:read,write` |Excluir as `read` fontes nomeadas e `write` do `disk` plugin. |

Diretivas separadas com uma nova linha.

## <a name="problems"></a>Problemas?
*Não vejo dados no portal.*

* Procura [Search][diagnostic] aberta para ver se os eventos brutos chegaram. Às vezes demoram mais tempo a aparecer no explorador de métricas.
* Você pode precisar [definir exceções](../../azure-monitor/app/ip-addresses.md) de firewall para dados de saída
* Ative o rastreio no plugin Application Insights. Adicione esta `<Plugin ApplicationInsightsWriter>`linha dentro:
  * `SDKLogger true`
* Abra um terminal e comece a ser recolhido em modo verboso, para ver quaisquer problemas que esteja a reportar:
  * `sudo collectd -f`

## <a name="known-issue"></a>Problema conhecido

O plugin de escrita de Insights de Aplicação é incompatível com certos plugins de leitura. Alguns plugins por vezes enviam "NaN" onde o plugin Application Insights espera um número de ponto flutuante.

Sintoma: O registo recolhido mostra erros que incluem "IA: ... SyntaxError: Inesperado token N".

Supor: Excluir os dados recolhidos pelo problema Escrever plugins. 

<!--Link references-->

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[apiexceptions]: ../../azure-monitor/app/api-custom-events-metrics.md#track-exception
[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[eclipse]: app-insights-java-eclipse.md
[java]: java-get-started.md
[javalogs]: java-trace-logs.md
[metrics]: ../../azure-monitor/platform/metrics-charts.md


