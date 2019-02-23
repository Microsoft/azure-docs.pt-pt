---
title: Análise de aplicação Web em Java com o Application Insights do Azure | Microsoft Docs
description: 'Monitorização do Desempenho de Aplicações para aplicações Web Java com o Application Insights. '
services: application-insights
documentationcenter: java
author: lgayhardt
manager: carmonm
ms.assetid: 051d4285-f38a-45d8-ad8a-45c3be828d91
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: lagayhar
ms.openlocfilehash: 224da9285ab0aef312688e4dfa1da49451abfa5a
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/22/2019
ms.locfileid: "56674655"
---
# <a name="get-started-with-application-insights-in-a-java-web-project"></a>Introdução ao Application Insights num projeto Web em Java


O [Application Insights](https://azure.microsoft.com/services/application-insights/) é um serviço de análise extensível para programadores Web que os ajudam a compreender o desempenho e a utilização da aplicação em direto. Utilize-o para [automaticamente os pedidos de instrumento, dependências de controle e recolher contadores de desempenho](auto-collect-dependencies.md#java), [diagnosticar problemas de desempenho e exceções](../../azure-monitor/app/detect-triage-diagnose.md), e [escrever código] [ api] para controlar o que os utilizadores fazem com a sua aplicação. 

![Captura de ecrã dos dados de exemplo de descrição geral](./media/java-get-started/overview-graphs.png)

O Application Insights suporta aplicações em Java em execução no Linux, Unix ou Windows.

É necessário:

* JRE versão 1.7 ou 1.8
* Uma subscrição do [Microsoft Azure](https://azure.microsoft.com/).

Se preferir o framework Spring, tente [configurar uma aplicação de inicializador do Spring Boot para utilizar o guia Application Insights](https://docs.microsoft.com/java/azure/spring-framework/configure-spring-boot-java-applicationinsights).

## <a name="1-get-an-application-insights-instrumentation-key"></a>1. Obter uma chave de instrumentação do Application Insights
1. Inicie sessão no [Portal do Microsoft Azure](https://portal.azure.com).
2. Crie um recurso do Application Insights. Defina o tipo de aplicação para aplicação Web em Java.

    ![Preencher um nome, escolher aplicação Web em Java e clicar em criar](./media/java-get-started/02-create.png)
3. Localize a chave de instrumentação do novo recurso. Terá de colar esta chave no seu projeto de código em breve.

    ![Na descrição geral do novo recurso, clique em Propriedades e copie a Chave de Instrumentação](./media/java-get-started/03-key.png)

## <a name="2-add-the-application-insights-sdk-for-java-to-your-project"></a>2. Adicionar o Application Insights SDK para Java ao projeto
*Escolha a forma adequada para o seu projeto.*

#### <a name="if-youre-using-maven-a-namemaven-setup-"></a>Se estiver a utilizar Maven... <a name="maven-setup" />
Se o seu projeto já estiver configurado para utilizar Maven para a compilação, intercale o código seguinte no ficheiro pom.xml.

Em seguida, atualize as dependências do projeto para obter os binários transferidos.

```XML

    <repositories>
       <repository>
          <id>central</id>
          <name>Central</name>
          <url>http://repo1.maven.org/maven2</url>
       </repository>
    </repositories>

    <dependencies>
      <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>applicationinsights-web</artifactId>
        <!-- or applicationinsights-core for bare API -->
        <version>[2.0,)</version>
      </dependency>
    </dependencies>
```

* *Fazer a compilação ou a soma de verificação dos erros de validação?* Tente utilizar uma versão específica, como: `<version>2.0.n</version>`. Pode encontrar a versão mais recente nas [notas de versão do SDK](https://github.com/Microsoft/ApplicationInsights-Java#release-notes) ou nos [artefactos Maven](https://search.maven.org/#search%7Cga%7C1%7Capplicationinsights).
* *Tem de atualizar para um novo SDK?* Atualize as dependências do seu projeto.

#### <a name="if-youre-using-gradle-a-namegradle-setup-"></a>Se estiver a utilizar o Gradle... <a name="gradle-setup" />
Se o seu projeto já estiver configurado para utilizar Gradle para a compilação, intercale o código seguinte no ficheiro build.gradle.

Em seguida, atualize as dependências do projeto para obter os binários transferidos.

```gradle

    repositories {
      mavenCentral()
    }

    dependencies {
      compile group: 'com.microsoft.azure', name: 'applicationinsights-web', version: '2.+'
      // or applicationinsights-core for bare API
    }
```

#### <a name="if-youre-using-eclipse-to-create-a-dynamic-web-project-"></a>Se estiver a utilizar o Eclipse para criar um projeto Web Dinâmico...
Utilize o [Application Insights SDK para o Plug-in Java][eclipse]. Nota: Apesar de utilizar este plug-in irá ajudá-lo com uma execução do Application Insights mais rápida (partindo do princípio que não está a utilizar Maven/Gradle), não é um sistema de gestão de dependência. Como tal, atualizar o plug-in não atualizará automaticamente as bibliotecas do Application Insights no seu projeto.

* *Fazer a compilação ou a soma de verificação dos erros de validação?* Tente utilizar uma versão específica, como: `version:'2.0.n'`. Pode encontrar a versão mais recente nas [notas de versão do SDK](https://github.com/Microsoft/ApplicationInsights-Java#release-notes) ou nos [artefactos Maven](https://search.maven.org/#search%7Cga%7C1%7Capplicationinsights).
* *Para atualizar para um SDK novo* Atualize as dependências do seu projeto.

#### <a name="otherwise-if-you-are-manually-managing-dependencies-"></a>Caso contrário, se estiver a gerir as dependências manualmente...
Transfira a [versão mais recente](https://github.com/Microsoft/ApplicationInsights-Java/releases/latest) e copie os ficheiros necessários para o seu projeto, o que substitui as versões anteriores.

### <a name="questions"></a>Perguntas...
* *Qual é a relação entre os componentes `-core` e `-web`?*
  * `applicationinsights-core` dá-lhe a versão simples da API. Precisa sempre deste componente.
  * `applicationinsights-web` fornece métricas que controlam as contagens de pedidos HTTP e os tempos de resposta. Pode omitir este componente, se não pretender que esta telemetria seja recolhida automaticamente. Por exemplo, se pretender escrever a sua.
  
* *Como posso atualizar o SDK para a versão mais recente?*
  * Se estiver a utilizar o Gradle ou o Maven...
    * Atualize o ficheiro de compilação para especificar a versão mais recente ou utilize a sintaxe universal do Gradle/Maven para incluí-la automaticamente. Em seguida, atualize as dependências do seu projeto. A sintaxe universal pode ser vista nos exemplos acima para [Gradle](#gradle-setup) ou [Maven](#maven-setup).
  * Se estiver a gerir as dependências manualmente...
    * Transfira a versão mais recente de [Application Insights SDK para Java](https://github.com/Microsoft/ApplicationInsights-Java/releases/latest) e substitua as anteriores. As alterações são descritas nas [notas de versão do SDK](https://github.com/Microsoft/ApplicationInsights-Java#release-notes).

## <a name="3-add-an-applicationinsightsxml-file"></a>3. Adicionar um ficheiro ApplicationInsights.xml
Adicione ApplicationInsights.xml à pasta de recursos do projeto ou certifique-se de que é adicionado ao caminho da classe de implementação do projeto. Copie o seguinte XML para a mesma.

Substitua a chave de instrumentação que recebeu do portal do Azure.

```XML

    <?xml version="1.0" encoding="utf-8"?>
    <ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings" schemaVersion="2014-05-30">


      <!-- The key from the portal: -->
      <InstrumentationKey>** Your instrumentation key **</InstrumentationKey>


      <!-- HTTP request component (not required for bare API) -->
      <TelemetryModules>
        <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebRequestTrackingTelemetryModule"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebSessionTrackingTelemetryModule"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebUserTrackingTelemetryModule"/>
      </TelemetryModules>

      <!-- Events correlation (not required for bare API) -->
      <!-- These initializers add context data to each event -->

      <TelemetryInitializers>
        <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationIdTelemetryInitializer"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationNameTelemetryInitializer"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebSessionTelemetryInitializer"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserTelemetryInitializer"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserAgentTelemetryInitializer"/>

      </TelemetryInitializers>
    </ApplicationInsights>
```

Opcionalmente, o ficheiro de configuração pode residir em qualquer localização à qual a sua aplicação possa aceder.  A propriedade do sistema `-Dapplicationinsights.configurationDirectory` especifica o diretório que contém ApplicationInsights.xml. Por exemplo, um ficheiro de configuração localizado em `E:\myconfigs\appinsights\ApplicationInsights.xml` seria configurado com a propriedade `-Dapplicationinsights.configurationDirectory="E:\myconfigs\appinsights"`.

* A chave de instrumentação é enviada juntamente com todos os itens de telemetria e diz ao Application Insights para apresentá-la no seu recurso.
* O componente de Pedido HTTP é opcional. Envia automaticamente telemetria sobre pedidos e tempos de resposta para o portal.
* A correlação de eventos é uma adição ao componente de pedidos HTTP. Atribui um identificador a cada pedido recebido pelo servidor e adiciona este identificador como uma propriedade a todos os itens de telemetria como a propriedade "Operation.Id". Permite-lhe correlacionar a telemetria associada a cada pedido, definindo um filtro em [pesquisa de diagnóstico][diagnostic].

### <a name="alternative-ways-to-set-the-instrumentation-key"></a>Maneiras alternativas de definir a chave de instrumentação
O SDK do Application Insights procura a chave pela seguinte ordem:

1. Propriedade de sistema: -DAPPLICATION_INSIGHTS_IKEY=your_ikey
2. Variável de ambiente: APPLICATION_INSIGHTS_IKEY
3. Ficheiro de configuração: ApplicationInsights.xml

Também pode [defini-lo no código](../../azure-monitor/app/api-custom-events-metrics.md#ikey):

```Java
    TelemetryConfiguration.getActive().setInstrumentationKey(iKey);
```

## <a name="4-add-an-http-filter"></a>4. Adicionar um filtro HTTP
O último passo da configuração permite que o componente de pedido HTTP registe cada pedido Web. (Não é necessário se pretender apenas a versão simples da API.)

### <a name="spring-boot-applications"></a>Aplicações do Spring Boot
Registar o Application Insights `WebRequestTrackingFilter` na sua classe de Configuração:

```Java
package <yourpackagename>.configurations;

import javax.servlet.Filter;

import org.springframework.boot.autoconfigure.condition.ConditionalOnMissingBean;
import org.springframework.boot.web.servlet.FilterRegistrationBean;
import org.springframework.context.annotation.Bean;
import org.springframework.core.Ordered;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.context.annotation.Configuration;
import com.microsoft.applicationinsights.TelemetryConfiguration;
import com.microsoft.applicationinsights.web.internal.WebRequestTrackingFilter;

@Configuration
public class AppInsightsConfig {

    @Bean
    public String telemetryConfig() {
        String telemetryKey = System.getenv("<instrumentation key>");
        if (telemetryKey != null) {
            TelemetryConfiguration.getActive().setInstrumentationKey(telemetryKey);
        }
        return telemetryKey;
    }

    /**
     * Programmatically registers a FilterRegistrationBean to register WebRequestTrackingFilter
     * @param webRequestTrackingFilter
     * @return Bean of type {@link FilterRegistrationBean}
     */
    @Bean
    public FilterRegistrationBean webRequestTrackingFilterRegistrationBean(WebRequestTrackingFilter webRequestTrackingFilter) {
        FilterRegistrationBean registration = new FilterRegistrationBean();
        registration.setFilter(webRequestTrackingFilter);
        registration.addUrlPatterns("/*");
        registration.setOrder(Ordered.HIGHEST_PRECEDENCE + 10);
        return registration;
    }


    /**
     * Creates bean of type WebRequestTrackingFilter for request tracking
     * @param applicationName Name of the application to bind filter to
     * @return {@link Bean} of type {@link WebRequestTrackingFilter}
     */
    @Bean
    @ConditionalOnMissingBean

    public WebRequestTrackingFilter webRequestTrackingFilter(@Value("${spring.application.name:application}") String applicationName) {
        return new WebRequestTrackingFilter(applicationName);
    }


}
```

> [!NOTE]
> Se estiver a utilizar o Spring Boot 1.3.8 ou anterior, substitua o FilterRegistrationBean pela linha abaixo

```Java
    import org.springframework.boot.context.embedded.FilterRegistrationBean;
```

Esta classe irá configurar o `WebRequestTrackingFilter` para ser o primeiro filtro na cadeia de filtro de http. Também vai solicitar a chave de instrumentação na variável de ambiente de sistema operativo, se estiver disponível.

> Estamos a utilizar a configuração de filtro de http da Web em vez da configuração Spring MVC porque esta é uma aplicação Spring Boot e tem a suas própria configuração Spring MVC. Consulte as secções abaixo para uma configuração especial Spring MVC.

### <a name="applications-using-webxml"></a>Aplicações que Utilizam Web.xml
Localize e abra o ficheiro web.xml no seu projeto e intercale o seguinte código no nó da aplicação Web, onde os filtros da aplicação estão configurados.

Para obter resultados mais exatos, o filtro deve ser mapeado antes de todos os outros filtros.

```XML

    <filter>
      <filter-name>ApplicationInsightsWebFilter</filter-name>
      <filter-class>
        com.microsoft.applicationinsights.web.internal.WebRequestTrackingFilter
      </filter-class>
    </filter>
    <filter-mapping>
       <filter-name>ApplicationInsightsWebFilter</filter-name>
       <url-pattern>/*</url-pattern>
    </filter-mapping>

   <!-- This listener handles shutting down the TelemetryClient when an application/servlet is undeployed. -->
    <listener>
      <listener-class>com.microsoft.applicationinsights.web.internal.ApplicationInsightsServletContextListener</listener-class>
    </listener>
```

#### <a name="if-youre-using-spring-web-mvc-31-or-later"></a>Se estiver a utilizar o Spring Web MVC 3.1 ou posterior
Edite estes elementos em *-servlet.xml para incluir o pacote do Application Insights:

```XML

    <context:component-scan base-package=" com.springapp.mvc, com.microsoft.applicationinsights.web.spring"/>

    <mvc:interceptors>
        <mvc:interceptor>
            <mvc:mapping path="/**"/>
            <bean class="com.microsoft.applicationinsights.web.spring.RequestNameHandlerInterceptorAdapter" />
        </mvc:interceptor>
    </mvc:interceptors>
```

#### <a name="if-youre-using-struts-2"></a>Se estiver a utilizar Struts 2
Adicione este item ao ficheiro de configuração de Struts (normalmente designado struts.xml ou struts-default.xml):

```XML

     <interceptors>
       <interceptor name="ApplicationInsightsRequestNameInterceptor" class="com.microsoft.applicationinsights.web.struts.RequestNameInterceptor" />
     </interceptors>
     <default-interceptor-ref name="ApplicationInsightsRequestNameInterceptor" />
```

Se tiver intercetores definidos numa pilha predefinida, o intercetor pode ser adicionado a essa pilha.

## <a name="5-run-your-application"></a>5. Executar a aplicação
Execute-a no modo de depuração no seu computador de desenvolvimento ou publique-a no seu servidor.

## <a name="6-view-your-telemetry-in-application-insights"></a>6. Ver a telemetria no Application Insights
Regresse ao seu recurso do Application Insights no [Portal do Microsoft Azure](https://portal.azure.com).

Os dados de pedidos HTTP aparecem no painel de descrição geral. (Se não aparecerem, aguarde alguns segundos e, em seguida, clique em Atualizar.)

![dados de exemplo](./media/java-get-started/5-results.png)

[Saiba mais sobre métricas.][metrics]

Clique em qualquer gráfico para ver métricas agregadas mais detalhadas.

![](./media/java-get-started/6-barchart.png)

> O Application Insights pressupõe que o formato de pedidos HTTP para aplicações MVC é: `VERB controller/action`. Por exemplo, `GET Home/Product/f9anuh81`, `GET Home/Product/2dffwrf5` e `GET Home/Product/sdf96vws` são agrupados em `GET Home/Product`. Este agrupamento permite agregações significativas de pedidos, como o número de pedidos e o tempo de execução médio dos pedidos.
>
>

### <a name="instance-data"></a>Dados de instâncias
Clique num tipo de pedido específico para ver instâncias individuais.

Dois tipos de dados são apresentados no Application Insights: dados agregados, armazenados e apresentados como médias, contagens e somas; e dados de instâncias - relatórios individuais de pedidos HTTP, exceções, vistas de página ou eventos personalizados.

Ao visualizar as propriedades de um pedido, pode ver os eventos de telemetria associados, como os pedidos e as exceções.

![](./media/java-get-started/7-instance.png)

### <a name="analytics-powerful-query-language"></a>Análise: Poderosa linguagem de consultas
À medida que se acumulam mais dados, pode executar consultas para agregar dados e localizar instâncias individuais.  A [análise](../../azure-monitor/app/analytics.md) é uma ferramenta poderosa para compreender o desempenho e a utilização, e para fins de diagnóstico.

![Exemplo de Análise](./media/java-get-started/025.png)

## <a name="7-install-your-app-on-the-server"></a>7. Instalar a aplicação no servidor
Agora publique a aplicação no servidor, permita que as pessoas a utilizem e veja a telemetria a ser mostrada no portal.

* Certifique-se que a firewall permite à aplicação enviar telemetria para estas portas:

  * dc.services.visualstudio.com:443
  * f5.services.visualstudio.com:443

* Se o tráfego de saída tiver de ser encaminhado através de uma firewall, defina as propriedades do sistema `http.proxyHost` e `http.proxyPort`.

* Nos servidores do Windows, instale:

  * [Microsoft Visual C++ Redistributable](https://www.microsoft.com/download/details.aspx?id=40784)

    (Este componente ativa os contadores de desempenho.)


## <a name="exceptions-and-request-failures"></a>Exceções e falhas de pedido
As exceções não processadas são recolhidas automaticamente:

![Abrir Definições, Falhas](./media/java-get-started/21-exceptions.png)

Para recolher dados de outras exceções, tem duas opções:

* [Inserir chamadas para trackException() no seu código][apiexceptions].
* [Instalar o Agente Java no servidor](java-agent.md). Especifique os métodos que pretende ver.

## <a name="monitor-method-calls-and-external-dependencies"></a>Monitorizar chamadas de método e dependências externas
[Instale o Agente Java](java-agent.md) para registar métodos internos especificados e as chamadas efetuadas através de JDBC, com dados de temporização.

## <a name="w3c-distributed-tracing"></a>Rastreio distribuído do W3C

O SDK de Java do Application Insights suporta agora [W3C distribuído rastreamento](https://w3c.github.io/trace-context/).

A configuração do SDK de entrada é explicada mais no nosso artigo sobre [correlação](correlation.md#w3c-distributed-tracing).

Configuração do SDK de saída está definida no [IA Agent.xml](java-agent.md) ficheiro.

## <a name="performance-counters"></a>Contadores de desempenho
Abra **Definições**, **Servidores** para ver uma gama de contadores de desempenho.

![](./media/java-get-started/11-perf-counters.png)

### <a name="customize-performance-counter-collection"></a>Personalizar a recolha do contador de desempenho
Para desativar a recolha do conjunto padrão de contadores de desempenho, adicione o seguinte código ao nó de raiz do ficheiro ApplicationInsights.xml:

```XML
    <PerformanceCounters>
       <UseBuiltIn>False</UseBuiltIn>
    </PerformanceCounters>
```

### <a name="collect-additional-performance-counters"></a>Recolher contadores de desempenho adicionais
Pode especificar contadores de desempenho adicionais para recolha.

#### <a name="jmx-counters-exposed-by-the-java-virtual-machine"></a>Contadores JMX (expostos pela Máquina Virtual Java)

```XML
    <PerformanceCounters>
      <Jmx>
        <Add objectName="java.lang:type=ClassLoading" attribute="TotalLoadedClassCount" displayName="Loaded Class Count"/>
        <Add objectName="java.lang:type=Memory" attribute="HeapMemoryUsage.used" displayName="Heap Memory Usage-used" type="composite"/>
      </Jmx>
    </PerformanceCounters>
```

* `displayName` – O nome apresentado no portal do Application Insights.
* `objectName` – O nome do objeto JMX.
* `attribute` – O atributo do nome do objeto JMX a obter
* `type` (opcional) - O tipo de atributo do objeto JMX:
  * Predefinição: um tipo simples, como int ou long.
  * `composite`: os dados de contador de desempenho estão no formato “Attribute.Data”
  * `tabular`: os dados de contador de desempenho estão no formato de uma linha de tabela

#### <a name="windows-performance-counters"></a>Contadores de desempenho do Windows
Cada [contador de desempenho do Windows](https://msdn.microsoft.com/library/windows/desktop/aa373083.aspx) é membro de uma categoria (da mesma forma que um campo é membro de uma classe). As categorias podem ser globais ou podem ter instâncias numeradas ou nomeadas.

```XML
    <PerformanceCounters>
      <Windows>
        <Add displayName="Process User Time" categoryName="Process" counterName="%User Time" instanceName="__SELF__" />
        <Add displayName="Bytes Printed per Second" categoryName="Print Queue" counterName="Bytes Printed/sec" instanceName="Fax" />
      </Windows>
    </PerformanceCounters>
```

* displayName – o nome apresentado no portal do Application Insights.
* categoryName – a categoria do contador de desempenho (objeto de desempenho) à qual este contador de desempenho está associado.
* counterName – o nome do contador de desempenho.
* instanceName – o nome da instância da categoria do contador de desempenho ou uma cadeia vazia (""), se a categoria contiver uma única instância. Se o categoryName for Processo e o contador de desempenho que pretende recolher é do processo JVM atual no qual a aplicação está em execução, especifique `"__SELF__"`.

Os contadores de desempenho ficam visíveis como métricas personalizadas no [Explorador de Métricas][metrics].

![](./media/java-get-started/12-custom-perfs.png)

### <a name="unix-performance-counters"></a>Contadores de desempenho Unix
* [Instale collectd com o plug-in do Application Insights](java-collectd.md) para obter uma ampla variedade de dados de sistema e de rede.

## <a name="local-forwarder"></a>Reencaminhador local

[Local reencaminhador](https://docs.microsoft.com/azure/application-insights/local-forwarder) é um agente que recolhe Application Insights ou [OpenCensus](https://opencensus.io/) telemetria a partir de uma variedade de SDKs e estruturas e a encaminha para o Application Insights. Ele é capaz de em execução no Windows e Linux.

```xml
<Channel type="com.microsoft.applicationinsights.channel.concrete.localforwarder.LocalForwarderTelemetryChannel">
<DeveloperMode>false</DeveloperMode>
<EndpointAddress><!-- put the hostname:port of your LocalForwarder instance here --></EndpointAddress>
<!-- The properties below are optional. The values shown are the defaults for each property -->
<FlushIntervalInSeconds>5</FlushIntervalInSeconds><!-- must be between [1, 500]. values outside the bound will be rounded to nearest bound -->
<MaxTelemetryBufferCapacity>500</MaxTelemetryBufferCapacity><!-- units=number of telemetry items; must be between [1, 1000] -->
</Channel>
```

Se estiver a utilizar SpringBoot starter, adicione o seguinte ao ficheiro de configuração (Application):

```yml
azure.application-insights.channel.local-forwarder.endpoint-address=<!--put the hostname:port of your LocalForwarder instance here-->
azure.application-insights.channel.local-forwarder.flush-interval-in-seconds=<!--optional-->
azure.application-insights.channel.local-forwarder.max-telemetry-buffer-capacity=<!--optional-->
```

Valores predefinidos são os mesmos para a configuração de SpringBoot Application e o applicationinsights. XML.

## <a name="get-user-and-session-data"></a>Obter dados de utilizador e de sessão
OK, está a enviar telemetria a partir do seu servidor Web. Para obter a vista completa em 360 graus da sua aplicação, agora pode adicionar mais monitorização:

* [Adicione a telemetria às suas páginas Web][usage] para monitorizar vistas de página e métricas de utilizador.
* [Configure testes Web][availability] para certificar-se de que a aplicação permanece em direto e reativa.

## <a name="capture-log-traces"></a>Capturar rastreios de registo
Pode utilizar o Application Insights para examinar registos de Log4J, Logback ou outras arquiteturas de registo. Pode correlacionar os registos com pedidos HTTP e outra telemetria. [Saiba como][javalogs].

## <a name="send-your-own-telemetry"></a>Enviar a sua própria telemetria
Agora que instalou o SDK, pode utilizar a API para enviar a sua própria telemetria.

* [Controle eventos personalizados e métricas][api] para saber o que os utilizadores estão a fazer com a sua aplicação.
* [Pesquise eventos e registos][diagnostic] para ajudar a diagnosticar problemas.

## <a name="availability-web-tests"></a>Testes Web de disponibilidade
O Application Insights pode testar o seu site em intervalos regulares para verificar se está a funcionar e a responder bem. [Para configurar][availability], clique em testes Web.

![Clique em Testes Web e, em seguida, em Adicionar teste Web](./media/java-get-started/31-config-web-test.png)

Irá obter gráficos de tempos de resposta e notificações por e-mail, se o seu site ficar inativo.

![Exemplo de teste Web](./media/java-get-started/appinsights-10webtestresult.png)

[Saiba mais sobre testes Web de disponibilidade.][availability]

## <a name="questions-problems"></a>Tem dúvidas? Problemas?
[Resolução de problemas de Java](java-troubleshoot.md)

## <a name="video"></a>Vídeo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]

## <a name="next-steps"></a>Passos seguintes
* [Chamadas de dependência do monitor](java-agent.md)
* [Contadores de desempenho Unix do monitor](java-collectd.md)
* Adicione [monitorização das suas páginas Web](javascript.md), para monitorizar os tempos de carregamento da página, as chamadas AJAX e as exceções de browser.
* Escreva [telemetria personalizada](../../azure-monitor/app/api-custom-events-metrics.md) para controlar a utilização no browser ou no servidor.
* Crie [dashboards](../../azure-monitor/app/app-insights-dashboards.md) para reunir os gráficos principais para monitorização do seu sistema.
* Utilize o [Analytics](../../azure-monitor/app/analytics.md) para consultas eficientes através de telemetria a partir da aplicação
* Para obter mais informações, visite [Azure para programadores Java](/java/azure).

<!--Link references-->

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[apiexceptions]: ../../azure-monitor/app/api-custom-events-metrics.md#trackexception
[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[eclipse]: ../../azure-monitor/learn/java-quick-start.md
[javalogs]: java-trace-logs.md
[metrics]: ../../azure-monitor/app/metrics-explorer.md
[usage]: javascript.md
