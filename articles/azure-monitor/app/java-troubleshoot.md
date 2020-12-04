---
title: Troubleshoot Application Insights em um projeto web java
description: Guia de resolução de problemas - monitorizando aplicações java ao vivo com Insights de Aplicação.
ms.topic: conceptual
ms.date: 03/14/2019
author: MS-jgol
ms.custom: devx-track-java
ms.author: jgol
ms.openlocfilehash: dbc9e48987f527c9579decc3db84091a432a17e5
ms.sourcegitcommit: c4246c2b986c6f53b20b94d4e75ccc49ec768a9a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/04/2020
ms.locfileid: "96601021"
---
# <a name="troubleshooting-and-q-and-a-for-application-insights-for-java-sdk"></a>Resolução de problemas e Q e A para Insights de Aplicações para Java SDK

> [!IMPORTANT]
> A abordagem recomendada para monitorizar as aplicações java é utilizar a auto-instrumentação sem alterar o código. Siga as diretrizes para o [agente Application Insights Java 3.0](./java-in-process-agent.md).

Perguntas ou problemas com [a Azure Application Insights em Java?][java] Abaixo temos algumas dicas.

## <a name="build-errors"></a>Criar erros
**No Eclipse ou intellij Idea, ao adicionar o SDK de Insights de Aplicação via Maven ou Gradle, recebo erros de validação de construção ou verificação.**

* Se o elemento de dependência `<version>` estiver a utilizar um padrão com caracteres wildcard (por exemplo (Maven) ou `<version>[2.0,)</version>` (Gradle) `version:'2.0.+'` ), tente especificar uma versão específica como `2.0.1` . Consulte as [notas de lançamento](https://github.com/Microsoft/ApplicationInsights-Java/releases) para a versão mais recente.

## <a name="no-data"></a>Sem dados
**Adicionei o Application Insights com sucesso e corri a minha aplicação, mas nunca vi dados no portal.**

* Aguarde um minuto e clique em Refresh. Os gráficos refrescam-se periodicamente, mas também pode refrescar-se manualmente. O intervalo de atualização depende do intervalo de tempo do gráfico.
* Verifique se tem uma chave de instrumentação definida no ficheiro ApplicationInsights.xml (na pasta de recursos do seu projeto) ou configurada como variável Ambiente.
* Verifique se não há `<DisableTelemetry>true</DisableTelemetry>` nó no ficheiro xml.
* Na sua firewall, poderá ter de abrir as portas 80 e 443 para o tráfego de saída para dc.services.visualstudio.com. Veja a [lista completa de exceções de firewall](./ip-addresses.md)
* No tabuleiro inicial do Microsoft Azure, veja o mapa do estado do serviço. Se houver algumas indicações de alerta, aguarde até que voltem a OK e, em seguida, feche e volte a abrir a sua lâmina de aplicação Application Insights.
* [Ligue o registo](#debug-data-from-the-sdk) adicionando um `<SDKLogger />` elemento sob o nó raiz no ficheiro ApplicationInsights.xml (na pasta de recursos do seu projeto) e verifique se há entradas pré-atuais com IA: INFO/WARN/ERROR para quaisquer registos suspeitos. 
* Certifique-se de que o ficheiro ApplicationInsights.xml correto foi carregado com sucesso pelo SDK Java, olhando para as mensagens de saída da consola para uma declaração de "Configuração foi encontrado com sucesso".
* Se o ficheiro config não for encontrado, verifique as mensagens de saída para ver onde o ficheiro config está a ser procurado e certifique-se de que o ApplicationInsights.xml está localizado num desses locais de pesquisa. Como regra geral, pode colocar o ficheiro config perto dos JARs SDK do Application Insights. Por exemplo: em Tomcat, isto significaria a pasta WEB-INF/classes. Durante o desenvolvimento pode colocar ApplicationInsights.xml na pasta de recursos do seu projeto web.
* Por favor, consulte também a [página de problemas do GitHub](https://github.com/Microsoft/ApplicationInsights-Java/issues) para problemas conhecidos com o SDK.
* Certifique-se de utilizar a mesma versão dos appenders core, web, agent e logging para evitar qualquer problema de conflito de versão.

#### <a name="i-used-to-see-data-but-it-has-stopped"></a>Costumava ver dados, mas parou.
* Já atingiu a sua quota mensal de pontos de dados? Abrir definições/quota e preços para descobrir. Em caso afirmativo, pode atualizar o seu plano ou pagar por uma capacidade adicional. Consulte o [regime de preços.](https://azure.microsoft.com/pricing/details/application-insights/)
* Atualizou recentemente o seu SDK? Certifique-se de que apenas os frascos SDK exclusivos estão presentes dentro do diretório do projeto. Não deve haver duas versões diferentes da SDK presentes.
* Está a olhar para o recurso correto da IA? Por favor, combine o iKey da sua aplicação com o recurso onde está à espera de telemetria. Deviam ser os mesmos.

#### <a name="i-dont-see-all-the-data-im-expecting"></a>Não vejo todos os dados que espero.
* Abra a página de utilização e o custo estimado e verifique se [a amostragem](./sampling.md) está em funcionamento. (Transmissão a 100% significa que a amostragem não está em funcionamento.) O serviço Application Insights pode ser definido para aceitar apenas uma fração da telemetria que chega da sua aplicação. Isto ajuda-o a manter dentro da sua quota mensal de telemetria.
* Tem a Amostra SDK ligada? Se sim, os dados serão amostrados à taxa especificada para todos os tipos aplicáveis.
* Estás a publicar uma versão mais antiga da Java SDK? A partir da versão 2.0.1, introduzimos um mecanismo de tolerância a falhas para lidar com falhas intermitentes de rede e backend, bem como persistência de dados em unidades locais.
* Estás a ser estrangulado devido à telemetria excessiva? Se ligar o registo INFO, verá uma mensagem de registo "App está estrangulada". O nosso limite atual é 32k artigos de telemetria/segundo.

### <a name="java-agent-cannot-capture-dependency-data"></a>Java Agent não pode capturar dados de dependência
* Configuraste o agente Java seguindo o [Agente Configure Java?](java-agent.md)
* Certifique-se de que tanto o frasco do agente java como o ficheiro AI-Agent.xml estão colocados na mesma pasta.
* Certifique-se de que a dependência que está a tentar recolher automaticamente é suportada para a recolha automática de automóveis. Atualmente apenas apoiamos MySQL, MsSQL, Oracle DB e Azure Cache para a coleção de dependência redis.

## <a name="no-usage-data"></a>Sem dados de utilização
**Vejo dados sobre pedidos e tempos de resposta, mas sem visualização de página, navegador ou dados do utilizador.**

Criou com sucesso a sua aplicação para enviar telemetria a partir do servidor. Agora o seu próximo passo é [configurar as suas páginas web para enviar telemetria a partir do navegador web.][usage]

Em alternativa, se o seu cliente for uma aplicação num [telefone ou noutro dispositivo,][platforms]pode enviar telemetria a partir daí.

Utilize a mesma chave de instrumentação para configurar a telemetria do seu cliente e do servidor. Os dados aparecerão no mesmo recurso Application Insights, e poderá correlacionar eventos a partir de cliente e servidor.


## <a name="disabling-telemetry"></a>Telemetria incapacitante
**Como posso desativar a coleção de telemetria?**

Em código:

```Java

    TelemetryConfiguration config = TelemetryConfiguration.getActive();
    config.setTrackingIsDisabled(true);
```

**Ou**

Atualize ApplicationInsights.xml (na pasta de recursos do seu projeto). Adicione o seguinte sob o nó raiz:

```XML

    <DisableTelemetry>true</DisableTelemetry>
```

Utilizando o método XML, tem de reiniciar a aplicação quando alterar o valor.

## <a name="changing-the-target"></a>Mudar o alvo
**Como posso alterar para que recurso Azure o meu projeto envia dados?**

* [Obtenha a chave de instrumentação do novo recurso.][java]
* Se adicionou Insights de Aplicação ao seu projeto utilizando o Azure Toolkit para Eclipse, clique bem no seu projeto web, selecione **Azure**, **Configure Application Insights**, e altere a chave.
* Se tivesse configurado a Chave de Instrumentação como variável ambiental, por favor atualize o valor da variável ambiental com o novo iKey.
* Caso contrário, atualize a chave em ApplicationInsights.xml na pasta de recursos do seu projeto.

## <a name="debug-data-from-the-sdk"></a>Depurar dados do SDK

**Como posso descobrir o que o SDK está a fazer?**

Para obter mais informações sobre o que está a acontecer na API, adicione `<SDKLogger/>` sob o nó raiz do ficheiro de configuração ApplicationInsights.xml.

### <a name="applicationinsightsxml"></a>ApplicationInsights.xml

Também pode instruir o madeiriro a fazer a saída para um ficheiro:

```XML
  <SDKLogger type="FILE"><!-- or "CONSOLE" to print to stderr -->
    <Level>TRACE</Level>
    <UniquePrefix>AI</UniquePrefix>
    <BaseFolderPath>C:/agent/AISDK</BaseFolderPath>
</SDKLogger>
```

### <a name="spring-boot-starter"></a>Arranque de botas de primavera

Para ativar o registo SDK com as Aplicações de Arranque de primavera utilizando o Arranque de Arranque de Mola Do Application Insights, adicione o seguinte ao `application.properties` ficheiro:

```yaml
azure.application-insights.logger.type=file
azure.application-insights.logger.base-folder-path=C:/agent/AISDK
azure.application-insights.logger.level=trace
```

ou para imprimir para erro padrão:

```yaml
azure.application-insights.logger.type=console
azure.application-insights.logger.level=trace
```

### <a name="java-agent"></a>Agente Java

Para ativar a atualização do registo de [AI-Agent.xml do](java-agent.md)agente JVM:

```xml
<AgentLogger type="FILE"><!-- or "CONSOLE" to print to stderr -->
    <Level>TRACE</Level>
    <UniquePrefix>AI</UniquePrefix>
    <BaseFolderPath>C:/agent/AIAGENT</BaseFolderPath>
</AgentLogger>
```

### <a name="java-command-line-properties"></a>Propriedades da linha de comando de Java
_Desde a versão 2.4.0_

Para permitir o registo utilizando opções de linha de comando, sem alterar ficheiros de configuração:

```
java -Dapplicationinsights.logger.file.level=trace -Dapplicationinsights.logger.file.uniquePrefix=AI -Dapplicationinsights.logger.baseFolderPath="C:/my/log/dir" -jar MyApp.jar
```

ou para imprimir para erro padrão:

```
java -Dapplicationinsights.logger.console.level=trace -jar MyApp.jar
```

## <a name="the-azure-start-screen"></a>O ecrã de partida do Azure
**Estou a olhar para [o portal Azure.](https://portal.azure.com) O mapa diz-me alguma coisa sobre a minha aplicação?**

Não, mostra a saúde dos servidores Azure em todo o mundo.

*A partir do tabuleiro de partida do Azure (ecrã principal), como encontro dados sobre a minha aplicação?*

Assumindo que [criou a sua aplicação para Insights de Aplicação,][java]clique em Procurar, selecione Application Insights e selecione o recurso de aplicação que criou para a sua aplicação. Para chegar lá mais rápido no futuro, pode fixar a sua app no tabuleiro de partida.

## <a name="intranet-servers"></a>Servidores intranet
**Posso monitorizar um servidor na minha intranet?**

Sim, desde que o seu servidor possa enviar telemetria para o portal Application Insights através da internet pública.

Na sua firewall, poderá ter de abrir as portas 80 e 443 para o tráfego de saída para dc.services.visualstudio.com e f5.services.visualstudio.com.

## <a name="data-retention"></a>Retenção de dados
**Quanto tempo os dados são retidos no portal? É seguro?**

Consulte [a retenção de dados e a privacidade.][data]

## <a name="debug-logging"></a>Registo de depurar
O Application Insights utiliza `org.apache.http` . Isto é relocalizado dentro dos frascos centrais do Application Insights sob o espaço de `com.microsoft.applicationinsights.core.dependencies.http` nomes . Isto permite que o Application Insights lide com cenários onde existem diferentes versões do mesmo `org.apache.http` numa base de código.

>[!NOTE]
>Se ativar a marcação de nível DEBUG para todos os espaços de nome na aplicação, será honrada por todos os módulos de execução, incluindo `org.apache.http` renomeado como `com.microsoft.applicationinsights.core.dependencies.http` . O Application Insights não poderá aplicar a filtragem destas chamadas porque a chamada de registo está a ser feita pela biblioteca Apache. A exploração madeireira de nível DEBUG produz uma quantidade considerável de dados de registo e não é recomendada para casos de produção ao vivo.


## <a name="next-steps"></a>Próximos passos
**Criei insights de aplicação para a minha aplicação de servidor Java. O que mais posso fazer?**

* [Monitorize a disponibilidade das suas páginas web][availability]
* [Monitorize o uso da página web][usage]
* [Acompanhe os problemas de utilização e diagnóstico nas aplicações do seu dispositivo][platforms]
* [Escreva código para acompanhar o uso da sua app][track]
* [Captar registos de diagnóstico][javalogs]

## <a name="get-help"></a>Obter ajuda
* [Stack Overflow](https://stackoverflow.com/questions/tagged/ms-application-insights)
* [Arquivar um problema no GitHub](https://github.com/Microsoft/ApplicationInsights-Java/issues)

<!--Link references-->

[availability]: ./monitor-web-app-availability.md
[data]: ./data-retention-privacy.md
[java]: java-get-started.md
[javalogs]: java-trace-logs.md
[platforms]: ./platforms.md
[track]: ./api-custom-events-metrics.md
[usage]: javascript.md

