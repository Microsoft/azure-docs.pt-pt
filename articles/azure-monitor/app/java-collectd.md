---
title: Monitor Java web app performance em Linux - Azure | Microsoft Docs
description: Monitorização alargada do desempenho da aplicação do seu website Java com o plug-in CollectD para Insights de Aplicações.
ms.topic: conceptual
ms.date: 03/14/2019
author: MS-jgol
ms.custom: devx-track-java
ms.author: jgol
ms.openlocfilehash: 08d8deb4c7769c4f8a202050d7b5515439d691f4
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100573855"
---
# <a name="collectd-linux-performance-metrics-in-application-insights-deprecated"></a>recolhido: Métricas de desempenho do Linux em Insights de Aplicação [Deprecado]

> [!IMPORTANT]
> A **abordagem recomendada** para monitorizar as aplicações java é utilizar a auto-instrumentação sem alterar o código. Siga as diretrizes para o **[agente Application Insights Java 3.0](./java-in-process-agent.md)**.

Para explorar as métricas de desempenho do sistema Linux no [Application Insights,](./app-insights-overview.md)instale [colecionada,](https://collectd.org/)juntamente com o plug-in Application Insights. Esta solução de código aberto reúne várias estatísticas de sistema e rede.

Normalmente, utilizará a recolha se já tiver [instrumentado o seu serviço web Java com Application Insights][java]. Dá-lhe mais dados para o ajudar a melhorar o desempenho da sua aplicação ou a diagnosticar problemas. 

## <a name="get-your-instrumentation-key"></a>Obtenha a sua chave de instrumentação
No [portal Microsoft Azure,](https://portal.azure.com)abra o recurso [Application Insights](./app-insights-overview.md) onde pretende que os dados apareçam. (Ou [criar um novo recurso](./create-new-resource.md).)

Pegue uma cópia da chave de instrumentação, que identifica o recurso.

![Navegue por todos, abra o seu recurso e, em seguida, no Essencial drop-down, selecione e copie a Chave de Instrumentação](./media/java-collectd/instrumentation-key-001.png)

## <a name="install-collectd-and-the-plug-in"></a>Instale colecionado e o plug-in
Nas suas máquinas de servidor Linux:

1. Instale [a](https://collectd.org/) versão 5.4.0 ou posterior.
2. Descarregue o [plugin de escritor colecionado Application Insights](https://github.com/microsoft/ApplicationInsights-Java/tree/master/core/src/main/java/com/microsoft/applicationinsights/internal). Note o número da versão.
3. Copie o plugin JAR em `/usr/share/collectd/java` .
4. `/etc/collectd/collectd.conf`Editar:
   * Certifique-se de que [o plugin Java](https://collectd.org/wiki/index.php/Plugin:Java) está ativado.
   * Atualize o JVMArg para o caminho java.class.para incluir o seguinte JAR. Atualize o número da versão para corresponder ao que descarregou:
   * `/usr/share/collectd/java/applicationinsights-collectd-1.0.5.jar`
   * Adicione este corte, utilizando a Chave de Instrumentação a partir do seu recurso:

```XML

     LoadPlugin "com.microsoft.applicationinsights.collectd.ApplicationInsightsWriter"
     <Plugin ApplicationInsightsWriter>
        InstrumentationKey "Your key"
     </Plugin>
```

Aqui está uma parte de um ficheiro de configuração da amostra:

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

Configure [outros plugins recolhidos,](https://collectd.org/wiki/index.php/Table_of_Plugins)que podem recolher vários dados de diferentes fontes.

Reiniciar recolhido de acordo com [o](https://collectd.org/wiki/index.php/First_steps)manual .

## <a name="view-the-data-in-application-insights"></a>Ver os dados em Insights de Aplicação
No seu recurso Application Insights, abra [métricas e adicione gráficos,][metrics]selecionando as métricas que deseja ver na categoria Custom.

Por predefinição, as métricas são agregadas em todas as máquinas hospedeiras a partir das quais as métricas foram recolhidas. Para ver as métricas por anfitrião, na lâmina de detalhes do Gráfico, ligue o Agrupamento e, em seguida, opte por agrupar-se por CollectD-Host.

## <a name="to-exclude-upload-of-specific-statistics"></a>Excluir o upload de estatísticas específicas
Por predefinição, o plugin Application Insights envia todos os dados recolhidos por todos os plugins 'read' recolhidos ativados. 

Excluir dados de plugins ou fontes de dados específicos:

* Editar o ficheiro de configuração. 
* Em `<Plugin ApplicationInsightsWriter>` , adicione linhas de diretiva como esta:

| Diretiva | Efeito |
| --- | --- |
| `Exclude disk` |Excluir todos os dados recolhidos pelo `disk` plugin |
| `Exclude disk:read,write` |Excluir as fontes nomeadas `read` e `write` do `disk` plugin. |

Diretivas separadas com uma nova linha.

## <a name="problems"></a>Problemas?
*Não vejo dados no portal.*

* [Pesquisa][diagnostic] aberta para ver se os eventos crus chegaram. Às vezes demoram mais tempo a aparecer no explorador de métricas.
* Pode ser necessário definir exceções de [firewall para dados de saída](./ip-addresses.md)
* Ativar o rastreio no plugin Application Insights. Adicione esta linha dentro `<Plugin ApplicationInsightsWriter>` de:
  * `SDKLogger true`
* Abra um terminal e comece a colecionar em modo verboso, para ver quaisquer problemas que esteja a relatar:
  * `sudo collectd -f`

## <a name="known-issue"></a>Problema conhecido

O plugin de escrita de informações de aplicação é incompatível com certos plugins de leitura. Alguns plugins às vezes enviam "NaN" onde o plugin Application Insights espera um número de ponto flutuante.

Sintoma: O registo recolhido mostra erros que incluem "IA: ... SyntaxError: Token Inesperado N".

Solução alternativa: Excluir dados recolhidos pelo problema Escreva plugins. 

<!--Link references-->

[api]: ./api-custom-events-metrics.md
[apiexceptions]: ./api-custom-events-metrics.md#track-exception
[availability]: ./monitor-web-app-availability.md
[diagnostic]: ./diagnostic-search.md
[eclipse]: app-insights-java-eclipse.md
[java]: java-get-started.md
[javalogs]: java-trace-logs.md
[metrics]: ../essentials/metrics-charts.md

