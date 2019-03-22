---
title: Resolver problemas relacionados com o Application Insights num projeto de web de Java
description: Guia de resolução de problemas - monitorização de aplicações de Java em direto com o Application Insights.
services: application-insights
documentationcenter: java
author: mrbullwinkle
manager: carmonm
ms.assetid: ef602767-18f2-44d2-b7ef-42b404edd0e9
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 01/14/2018
ms.author: mbullwin
ms.openlocfilehash: eaade5f9ec9db7e8d224305147dafc264916d9c5
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57995596"
---
# <a name="troubleshooting-and-q-and-a-for-application-insights-for-java"></a>Resolução de problemas e Perguntas e Respostas para o Application Insights para Java
Perguntas ou problemas com [do Azure Application Insights no Java][java]? Eis algumas sugestões.

## <a name="build-errors"></a>Erros de compilação
**No Eclipse ou o Intellij Idea, ao adicionar o Application Insights SDK através de Maven ou Gradle, obtenho a compilação ou a soma de verificação de erros de validação.**

* Se a dependência `<version>` elemento é usar um padrão com carateres universais (por exemplo, (Maven) `<version>[2.0,)</version>` ou (Gradle) `version:'2.0.+'`), experimente especificar uma versão específica em vez disso, como `2.0.1`. Consulte a [notas de versão](https://github.com/Microsoft/ApplicationInsights-Java/releases) para a versão mais recente.

## <a name="no-data"></a>Nenhuns dados
**Adicionei o Application Insights com êxito e Executei meu aplicativo, mas jamais vi dados no portal.**

* Espere um minuto e clique em Atualizar. Os gráficos atualizam-se periodicamente, mas também pode atualizar manualmente. O intervalo de atualização depende o intervalo de tempo do gráfico.
* Verifique se tem uma chave de instrumentação definidas no ficheiro applicationinsights. XML (na pasta de recursos no seu projeto) ou configurados como a variável de ambiente.
* Certifique-se de que não há nenhum `<DisableTelemetry>true</DisableTelemetry>` nó no arquivo xml.
* Na firewall, poderá ter de abrir as portas TCP 80 e 443 para tráfego de saída para dc.services.visualstudio.com. Consulte o [lista completa de exceções de firewall](../../azure-monitor/app/ip-addresses.md)
* O Microsoft Azure começa quadro, examinar o mapa de estado do serviço. Se existirem algumas indicações de alerta, aguarde até que eles tem devolvido para OK e, em seguida, feche e volte a abrir o painel de aplicações do Application Insights.
* Ativar o registo para a janela de consola do IDE, adicionando um `<SDKLogger />` elemento sob o nó de raiz no ficheiro Applicationinsights XML (na pasta de recursos no seu projeto) e procurar entradas prefaciados com IA: INFORMAÇÕES/aviso/erro para quaisquer registos suspeitos.
* Certifique-se de que o ficheiro applicationinsights. XML correto foi com êxito carregado pelo SDK do Java, observando mensagens de saída da consola para uma instrução "ficheiro de configuração tem sido encontrado com sucesso".
* Se não for encontrado o ficheiro de configuração, verifique as mensagens de saída para ver onde o ficheiro de configuração está a ser procurado para e certifique-se de que o applicationinsights. xml está localizado em um desses locais de pesquisa. Como regra prática, pode colocar o ficheiro de configuração perto o JARs do SDK do Application Insights. Por exemplo: no Tomcat, isso poderia significar que a pasta do WEB-INF/classes. Durante o desenvolvimento, pode colocar applicationinsights. xml na pasta de recursos do seu projeto de web.
* Também examine [página de problemas do GitHub](https://github.com/Microsoft/ApplicationInsights-Java/issues) para problemas conhecidos com o SDK.
* Certifique-se de utilizar a mesma versão do Application Insights core, web, agente e logs appenders para evitar quaisquer problemas de conflito de versão.

#### <a name="i-used-to-see-data-but-it-has-stopped"></a>Usei para ver os dados, mas foi parado
* Verifique os [blog de estado](https://blogs.msdn.com/b/applicationinsights-status/).
* Ter atingido a quota mensal de pontos de dados? Abra Definições/quotas e preços para descobrir. Nesse caso, pode atualizar o seu plano ou pagar por capacidade adicional. Consulte a [esquema de preços](https://azure.microsoft.com/pricing/details/application-insights/).
* Atualizou recentemente o SDK? Certifique-se de que existem apenas SDK exclusivo jars dentro do diretório de projeto. Não deve haver duas versões diferentes do SDK do presente.
* Está à procura no recurso de IA correto? Corresponde, a iKey do seu aplicativo para o recurso onde está esperando a telemetria. Deve ser o mesmo.

#### <a name="i-dont-see-all-the-data-im-expecting"></a>Não vejo todos os dados que eu estou esperando
* Abra a utilização e estimado se a página de custo e de verificação [amostragem](../../azure-monitor/app/sampling.md) está em operação. (transmissão de 100% significa que a amostragem não está em operação). O serviço Application Insights pode ser definido para aceitar apenas uma fração de telemetria que são recebidos a partir da sua aplicação. Isto ajuda a manter-se dentro da sua quota mensal de telemetria.
* Tem amostragem de SDK ativada? Se Sim, os dados seriam amostragem a taxa especificada para todos os tipos de aplicável.
* Está executando uma versão mais antiga do SDK de Java? A partir da versão 2.0.1, introduzimos mecanismo de tolerância a falhas para lidar com intermitente da rede e falhas de back-end, bem como a persistência de dados em unidades locais.
* Obter limitados devido a excesso telemetria? Se ativar o registo de informações, verá um log de mensagem "A aplicação é limitada". Nosso limite atual é 32 telemetria de mil itens/segundo.

### <a name="java-agent-cannot-capture-dependency-data"></a>Agente Java não é possível capturar dados de dependência
* Ter que configurou o agente Java seguindo [configurar o agente de Java](java-agent.md) ?
* Certifique-se de que o jar do agente de java e o ficheiro de IA Agent.xml são colocados na mesma pasta.
* Certifique-se de que a dependência que está a tentar a recolher automática é suportada para a recolha automática. Atualmente só suportamos MySQL, MsSQL, Oracle DB e Cache do Azure para a recolha de dependência de Redis.
* Está a utilizar JDK 1.7 ou 1.8? Atualmente não suportamos a coleção de dependência JDK 9.

## <a name="no-usage-data"></a>Não existem dados de utilização
**Vejo que os dados sobre pedidos e tempos de resposta, mas nenhuma vista de página, browser ou dados de utilizador.**

Configure com êxito a sua aplicação para enviar telemetria a partir do servidor. Agora, o passo seguinte é [configurar suas páginas da web para enviar telemetria a partir do browser][usage].

Em alternativa, se o cliente é uma aplicação num [telemóvel ou outro dispositivo][platforms], pode enviar telemetria a partir daí.

Utilize a mesma chave de instrumentação para configurar a telemetria do seu cliente e o servidor. Os dados serão apresentados no mesmo recurso do Application Insights, e será capaz de correlacionar eventos de cliente e servidor.


## <a name="disabling-telemetry"></a>Desativar a telemetria
**Como posso desativar a recolha de telemetria?**

No código:

```Java

    TelemetryConfiguration config = TelemetryConfiguration.getActive();
    config.setTrackingIsDisabled(true);
```

**ou**

Atualize o applicationinsights. XML (na pasta de recursos no seu projeto). Adicione o seguinte no nó raiz:

```XML

    <DisableTelemetry>true</DisableTelemetry>
```

Usando o método XML, terá de reiniciar a aplicação quando altera o valor.

## <a name="changing-the-target"></a>Alterar o destino
**Como posso alterar qual meu projeto envia os dados para o recurso do Azure?**

* [Obtenha a chave de instrumentação do novo recurso.][java]
* Se tiver adicionado o Application Insights ao seu projeto com o Azure Toolkit para Eclipse, clique com o botão direito do rato em seu projeto da web, selecione **Azure**, **configurar o Application Insights**e alterar a chave.
* Se tiver configurado a chave de instrumentação como variável de ambiente, atualize o valor da variável de ambiente com iKey novo.
* Caso contrário, atualize a chave no applicationinsights. xml na pasta de recursos no seu projeto.

## <a name="debug-data-from-the-sdk"></a>Depurar os dados do SDK

**Como posso descobrir o que está fazendo o SDK?**

Para obter mais informações sobre o que está acontecendo na API, adicione `<SDKLogger/>` sob o nó de raiz do ficheiro de configuração applicationinsights. XML.

### <a name="applicationinsightsxml"></a>ApplicationInsights.xml

Também pode instruir o agente de log de saída para um ficheiro:

```XML
  <SDKLogger type="FILE">
    <Level>TRACE</Level>
    <UniquePrefix>AI</UniquePrefix>
    <BaseFolderPath>C:/agent/AISDK</BaseFolderPath>
</SDKLogger>
```

### <a name="spring-boot-starter"></a>Spring Boot Starter

Para ativar o registo do SDK com Spring Boot aplicações que utilizam o Application Insights Spring Boot Starter, adicione o seguinte para o `application.properties` ficheiro.:

```yaml
azure.application-insights.logger.type=file
azure.application-insights.logger.base-folder-path=C:/agent/AISDK
azure.application-insights.logger.level=trace
```

### <a name="java-agent"></a>Agente de Java

Para ativar o registo do agente de JVM update a [ficheiros de IA Agent.xml](java-agent.md).

```xml
<AgentLogger type="FILE">
    <Level>TRACE</Level>
    <UniquePrefix>AI</UniquePrefix>
    <BaseFolderPath>C:/agent/AIAGENT</BaseFolderPath>
</AgentLogger>
```

## <a name="the-azure-start-screen"></a>O ecrã de início do Azure
**Estou a olhar [do portal do Azure](https://portal.azure.com). O mapa me dizer algo sobre a minha aplicação?**

Não, ela mostra o estado de funcionamento dos servidores do Azure em todo o mundo.

*No painel de início do Azure (ecrã principal), como faço para localizar dados sobre a minha aplicação?*

Supondo que [configure a sua aplicação para o Application Insights][java], clique em Procurar, selecione o Application Insights e selecione o recurso de aplicação que criou para a sua aplicação. Para obter há mais rapidamente no futuro, pode afixar a aplicação para o quadro de início.

## <a name="intranet-servers"></a>Servidores de intranet
**Pode monitorizar um servidor na minha intranet?**

Sim, fornecido que o servidor pode enviar a telemetria para o portal do Application Insights através da internet pública.

Na firewall, poderá ter de abrir as portas TCP 80 e 443 para tráfego de saída para dc.services.visualstudio.com e f5.services.visualstudio.com.

## <a name="data-retention"></a>Retenção de dados
**O período de tempo são retidos os dados no portal do? É seguro?**

Ver [retenção de dados e privacidade][data].

## <a name="debug-logging"></a>O registo de depuração
O Application Insights utiliza `org.apache.http`. Isso é realocado dentro do Application Insights core jars sob o espaço de nomes `com.microsoft.applicationinsights.core.dependencies.http`. Isto permite-Application Insights para manipular os cenários em que diferentes versões do mesmo `org.apache.http` existe numa base de código.

>[!NOTE]
>Se ativar o registo de nível de depuração para todos os espaços de nomes na aplicação, serão cumprida por todos os módulos em execução, incluindo `org.apache.http` renomeado como `com.microsoft.applicationinsights.core.dependencies.http`. O Application Insights não poderá aplicar a filtragem para essas chamadas, uma vez que a chamada de registo está a ser efetuada pela biblioteca Apache. Registo de nível de depuração produzir uma quantidade considerável de dados de registo e não é recomendada para instâncias de produção em direto.


## <a name="next-steps"></a>Passos Seguintes
**Posso configurar o Application Insights para a minha aplicação de servidor de Java. O que mais posso fazer?**

* [Monitorizar a disponibilidade das páginas da web][availability]
* [Monitorizar a utilização de página da web][usage]
* [Controlar a utilização e diagnosticar problemas nas suas aplicações de dispositivo][platforms]
* [Escrever código para controlar a utilização da sua aplicação][track]
* [Capturar os registos de diagnóstico][javalogs]

## <a name="get-help"></a>Obter ajuda
* [Stack Overflow](https://stackoverflow.com/questions/tagged/ms-application-insights)
* [Enviar um problema no GitHub](https://github.com/Microsoft/ApplicationInsights-Java/issues)

<!--Link references-->

[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[data]: ../../azure-monitor/app/data-retention-privacy.md
[java]: java-get-started.md
[javalogs]: java-trace-logs.md
[platforms]: ../../azure-monitor/app/platforms.md
[track]: ../../azure-monitor/app/api-custom-events-metrics.md
[usage]: javascript.md

