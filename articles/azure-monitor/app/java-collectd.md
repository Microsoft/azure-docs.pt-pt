---
title: Monitorizar o desempenho de aplicações web Java no Linux - Azure | Documentos da Microsoft
description: Estendido a monitorização de desempenho do seu site de Java com o plug-in de recolhidos para o Application Insights.
services: application-insights
documentationcenter: java
author: mrbullwinkle
manager: carmonm
ms.assetid: 40c68f45-197a-4624-bf89-541eb7323002
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 08/24/2016
ms.author: mbullwin
ms.openlocfilehash: 783cef6ff4e107838bb3ff7502fb4a8e9189ec3d
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58011185"
---
# <a name="collectd-linux-performance-metrics-in-application-insights"></a>recolhidos: Métricas de desempenho do Linux no Application Insights


Para explorar métricas de desempenho do sistema Linux no [Application Insights](../../azure-monitor/app/app-insights-overview.md), instale [recolhidos](https://collectd.org/), juntamente com o seu plug-in do Application Insights. Esta solução de código-fonte aberto reúne várias estatísticas de sistema e de rede.

Normalmente, irá utilizar recolhidos se já tiver [instrumentada seu serviço web em Java com o Application Insights][java]. Ele dá-lhe mais dados para ajudar a melhorar o desempenho da sua aplicação ou diagnosticar problemas. 

## <a name="get-your-instrumentation-key"></a>Obtenha a chave de instrumentação
Na [portal do Microsoft Azure](https://portal.azure.com), abra o [Application Insights](../../azure-monitor/app/app-insights-overview.md) recurso onde pretende que os dados a aparecer. (Ou [criar um novo recurso](../../azure-monitor/app/create-new-resource.md ).)

Fazer uma cópia da chave de instrumentação, que identifica o recurso.

![Procurar tudo, abra o recurso e, em seguida, no Essentials lista pendente, selecione e copie a chave de instrumentação](./media/java-collectd/instrumentation-key-001.png)

## <a name="install-collectd-and-the-plug-in"></a>Instalar recolhidos e o plug-in
Nas suas máquinas de servidor Linux:

1. Instale [recolhidos](https://collectd.org/) versão 5.4.0 ou posterior.
2. Transfira o [Plug-in do Application Insights recolhidos escritor](https://aka.ms/aijavasdk). Tenha em atenção o número de versão.
3. Copiar o plug-in JAR em `/usr/share/collectd/java`.
4. Editar `/etc/collectd/collectd.conf`:
   * Certifique-se de que [o plug-in de Java](https://collectd.org/wiki/index.php/Plugin:Java) está ativada.
   * Atualize o JVMArg para o java.class.path para incluir o seguinte JAR. Atualize o número de versão corresponde ao que transferiu:
   * `/usr/share/collectd/java/applicationinsights-collectd-1.0.5.jar`
   * Adicione este trecho de código, usando a chave de instrumentação do seu recurso:

```XML

     LoadPlugin "com.microsoft.applicationinsights.collectd.ApplicationInsightsWriter"
     <Plugin ApplicationInsightsWriter>
        InstrumentationKey "Your key"
     </Plugin>
```

Segue-se parte de um ficheiro de configuração de exemplo:

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

Configurar outro [plug-ins de recolhidos](https://collectd.org/wiki/index.php/Table_of_Plugins), que pode recolher dados de várias origens diferentes.

Reiniciar recolhidos de acordo com a respetiva [manual](https://collectd.org/wiki/index.php/First_steps).

## <a name="view-the-data-in-application-insights"></a>Ver os dados no Application Insights
No recurso do Application Insights, abra [métricas e adição de gráficos][metrics], selecionando as métricas que deseja ver da categoria personalizada.

Por predefinição, as métricas são agregadas entre todos os computadores anfitrião a partir dos quais as métricas foram recolhidas. Para ver as métricas por anfitrião, no painel de detalhes do gráfico, ative agrupamento e, em seguida, optar por agrupar por anfitrião recolhidos.

## <a name="to-exclude-upload-of-specific-statistics"></a>Para excluir o carregamento das estatísticas específicas
Por predefinição, o plug-in do Application Insights envia todos os dados recolhidos por todos os ativado recolhidos em "ler" plug-ins. 

Para excluir os dados de origens de dados ou de plug-ins específicas:

* Edite o ficheiro de configuração. 
* No `<Plugin ApplicationInsightsWriter>`, adicionar a diretivas linhas como esta:

| Diretiva | Efeito |
| --- | --- |
| `Exclude disk` |Excluir todos os dados recolhidos pelo `disk` Plug-in |
| `Exclude disk:read,write` |Excluir origens com o nome `read` e `write` partir o `disk` Plug-in. |

Diretivas separadas com uma nova linha.

## <a name="problems"></a>Problemas?
*Não vejo a dados no portal*

* Open [pesquisa] [ diagnostic] para ver se os eventos não processados chegaram. Por vezes, demorar mais tempo a aparecer no Explorador de métricas.
* Poderá ter de [definir exceções de firewall para dados de saída](../../azure-monitor/app/ip-addresses.md)
* Ative o rastreio no plug-in do Application Insights. Adicione esta linha no `<Plugin ApplicationInsightsWriter>`:
  * `SDKLogger true`
* Abra um terminal e iniciar recolhidos no modo verboso, para ver todos os problemas que está a comunicar:
  * `sudo collectd -f`

## <a name="known-issue"></a>Problema conhecido

O plug-in de escrita do Application Insights é incompatível com determinados plug-ins de leitura. Alguns plug-ins, às vezes, enviam "NaN", onde o plug-in do Application Insights espera um número de vírgula flutuante.

Sintoma: O registo de recolhidos mostra erros que incluem "IA:... SyntaxError: Token inesperado N".

Solução: Exclua os dados recolhidos pelos plug-ins de escrita do problema. 

<!--Link references-->

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[apiexceptions]: ../../azure-monitor/app/api-custom-events-metrics.md#track-exception
[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[eclipse]: app-insights-java-eclipse.md
[java]: java-get-started.md
[javalogs]: java-trace-logs.md
[metrics]: ../../azure-monitor/app/metrics-explorer.md


