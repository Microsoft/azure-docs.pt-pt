---
title: Troubleshoot Application Insights em um projeto web java
description: Guia de resolução de problemas - monitorização de aplicações java ao vivo com Insights de Aplicação.
ms.topic: conceptual
ms.date: 03/14/2019
ms.openlocfilehash: 04e98938bc5dd17816ae873f122073212275a414
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77657185"
---
# <a name="troubleshooting-and-q-and-a-for-application-insights-for-java"></a>Resolução de problemas e Perguntas e Respostas para o Application Insights para Java
Questões ou problemas com insights de [aplicação azure em Java?][java] Abaixo temos algumas dicas.

## <a name="build-errors"></a>Construir erros
**No Eclipse ou intellij Idea, ao adicionar o SDK de Insights de Aplicação via Maven ou Gradle, recebo erros de validação de construção ou verificação.**

* Se o `<version>` elemento de dependência estiver a utilizar um padrão com `<version>[2.0,)</version>` caracteres wildcard (por exemplo (Maven) ou (Gradle), `version:'2.0.+'`tente especificar uma versão específica como `2.0.1`. Consulte as notas de [lançamento](https://github.com/Microsoft/ApplicationInsights-Java/releases) para a versão mais recente.

## <a name="no-data"></a>Sem dados
**Adicionei application Insights com sucesso e geri a minha aplicação, mas nunca vi dados no portal.**

* Espere um minuto e clique em Refresh. Os gráficos refrescam-se periodicamente, mas também pode refrescar-se manualmente. O intervalo de atualização depende da faixa hordo.
* Verifique se tem uma chave de instrumentação definida no ficheiro ApplicationInsights.xml (na pasta de recursos do seu projeto) ou configurada como variável Ambiente.
* Verifique se não `<DisableTelemetry>true</DisableTelemetry>` há nó no ficheiro xml.
* Na sua firewall, poderá ter de abrir as portas TCP 80 e 443 para tráfego de saída para dc.services.visualstudio.com. Consulte a [lista completa de exceções à firewall](../../azure-monitor/app/ip-addresses.md)
* Na placa inicial do Microsoft Azure, veja o mapa do estado do serviço. Se houver algumas indicações de alerta, aguarde até que voltem ao OK e, em seguida, feche e reabra a sua lâmina de aplicação Application Insights.
* [Ligue o registo](#debug-data-from-the-sdk) `<SDKLogger />` adicionando um elemento sob o nó raiz no ficheiro ApplicationInsights.xml (na pasta de recursos do seu projeto) e verifique se há entradas pré-faciais com IA: INFO/WARN/ERROR para quaisquer registos suspeitos. 
* Certifique-se de que o ficheiro ApplicationInsights.xml correto foi carregado com sucesso pelo Java SDK, analisando as mensagens de saída da consola para uma declaração de "Configuração foi encontrada com sucesso".
* Se o ficheiro config não for encontrado, verifique as mensagens de saída para ver onde o ficheiro config está a ser procurado e certifique-se de que o ApplicationInsights.xml está localizado num desses locais de pesquisa. Como regra geral, pode colocar o ficheiro config perto dos SDK JARs de Aplicação Insights. Por exemplo: em Tomcat, isto significaria a pasta WEB-INF/classes. Durante o desenvolvimento pode colocar ApplicationInsights.xml na pasta de recursos do seu projeto web.
* Por favor, consulte também a página de problemas do [GitHub](https://github.com/Microsoft/ApplicationInsights-Java/issues) para problemas conhecidos com o SDK.
* Certifique-se de que utiliza a mesma versão do núcleo, web, agente e apendedes de registo de aplicações para evitar quaisquer problemas de conflito de versão.

#### <a name="i-used-to-see-data-but-it-has-stopped"></a>Costumava ver dados, mas parou.
* Verifique o blog de [estado](https://blogs.msdn.com/b/applicationinsights-status/).
* Atingiu a sua quota mensal de pontos de dados? Abrir Definições/Quotas e Preços para descobrir. Em caso afirmativo, pode atualizar o seu plano ou pagar por uma capacidade adicional. Consulte o [regime de preços](https://azure.microsoft.com/pricing/details/application-insights/).
* Atualizou recentemente o seu SDK? Certifique-se de que apenas os frascos Exclusivos SDK estão presentes dentro do diretório do projeto. Não deve haver duas versões diferentes do SDK presente.
* Estás a olhar para o recurso correto da IA? Por favor, combine o iKey da sua aplicação com o recurso onde espera telemetria. Devem ser os mesmos.

#### <a name="i-dont-see-all-the-data-im-expecting"></a>Não vejo todos os dados que estou à espera.
* Abra a página de utilização e o custo estimado e verifique se a [amostragem](../../azure-monitor/app/sampling.md) está em funcionamento. (100% de transmissão significa que a amostragem não está em funcionamento.) O serviço Application Insights pode ser definido para aceitar apenas uma fração da telemetria que chega da sua aplicação. Isto ajuda-o a manter dentro da sua quota mensal de telemetria.
* Tem amostragem SDK ligada? Se sim, os dados seriam amostrados à taxa especificada para todos os tipos aplicáveis.
* Estás a executar uma versão mais antiga do Java SDK? Começando pela versão 2.0.1, introduzimos mecanismo sinuoso de tolerância a falhas na rede intermitente e backend, bem como a persistência de dados nas unidades locais.
* Estás a ser estrangulado devido a telemetria excessiva? Se ligar o registo de INFORMAÇÃO, verá uma mensagem de log "A aplicação está estrangulada". O nosso limite atual é de artigos de telemetria de 32k/segundo.

### <a name="java-agent-cannot-capture-dependency-data"></a>O Agente Java não pode capturar dados de dependência
* Já configuraste o agente Java seguindo o [Agente Configure Java?](java-agent.md)
* Certifique-se de que tanto o frasco do agente java como o ficheiro AI-Agent.xml são colocados na mesma pasta.
* Certifique-se de que a dependência que está a tentar recolher automaticamente é suportada para a recolha automática. Atualmente apenas apoiamos mySQL, MsSQL, Oracle DB e Azure Cache para a coleção de dependência redis.

## <a name="no-usage-data"></a>Sem dados de utilização
**Vejo dados sobre pedidos e tempos de resposta, mas nenhuma visualização de página, navegador ou dados do utilizador.**

Configura com sucesso a sua aplicação para enviar telemetria do servidor. Agora o seu próximo passo é [configurar as suas páginas web para enviar telemetria do navegador web][usage].

Em alternativa, se o seu cliente for uma aplicação num [telefone ou outro dispositivo,][platforms]pode enviar telemetria a partir daí.

Utilize a mesma chave de instrumentação para configurar a telemetria do cliente e do servidor. Os dados aparecerão no mesmo recurso Desinformação de Aplicações, e poderá correlacionar eventos a partir de clientes e servidores.


## <a name="disabling-telemetry"></a>Telemetria incapacitante
**Como posso desativar a coleção de telemetria?**

Em código:

```Java

    TelemetryConfiguration config = TelemetryConfiguration.getActive();
    config.setTrackingIsDisabled(true);
```

**Ou**

Atualizar ApplicationInsights.xml (na pasta de recursos do seu projeto). Adicione o seguinte sob o nó raiz:

```XML

    <DisableTelemetry>true</DisableTelemetry>
```

Utilizando o método XML, tem de reiniciar a aplicação quando alterar o valor.

## <a name="changing-the-target"></a>Mudar o alvo
**Como posso alterar o recurso azure para o qual o meu projeto envia dados?**

* [Obtenha a chave de instrumentação do novo recurso.][java]
* Se adicionou insights de aplicação ao seu projeto utilizando o Kit de Ferramentas Azure para Eclipse, clique no seu projeto web, **selecione Azure,** **Configure Application Insights**e altere a chave.
* Se tiver configurado a Chave de Instrumentação como variável ambiental, por favor atualize o valor da variável ambiental com o novo iKey.
* Caso contrário, atualize a chave em ApplicationInsights.xml na pasta de recursos do seu projeto.

## <a name="debug-data-from-the-sdk"></a>Dados de depuração do SDK

**Como posso descobrir o que o SDK está a fazer?**

Para obter mais informações sobre o que `<SDKLogger/>` está a acontecer na API, adicione sob o nó raiz do ficheiro de configuração ApplicationInsights.xml.

### <a name="applicationinsightsxml"></a>ApplicationInsights.xml

Também pode instruir o madeireiro a saída para um ficheiro:

```XML
  <SDKLogger type="FILE"><!-- or "CONSOLE" to print to stderr -->
    <Level>TRACE</Level>
    <UniquePrefix>AI</UniquePrefix>
    <BaseFolderPath>C:/agent/AISDK</BaseFolderPath>
</SDKLogger>
```

### <a name="spring-boot-starter"></a>Arranque de bota de primavera

Para ativar o login SDK com apps de arranque de `application.properties` mola utilizando o Arranque de Arranque de Arranque de Mola Insights da Aplicação Insights, adicione o seguinte ao ficheiro:

```yaml
azure.application-insights.logger.type=file
azure.application-insights.logger.base-folder-path=C:/agent/AISDK
azure.application-insights.logger.level=trace
```

ou imprimir para erro padrão:

```yaml
azure.application-insights.logger.type=console
azure.application-insights.logger.level=trace
```

### <a name="java-agent"></a>Agente Java

Para ativar a atualização do agente JVM Logging o [ficheiro AI-Agent.xml:](java-agent.md)

```xml
<AgentLogger type="FILE"><!-- or "CONSOLE" to print to stderr -->
    <Level>TRACE</Level>
    <UniquePrefix>AI</UniquePrefix>
    <BaseFolderPath>C:/agent/AIAGENT</BaseFolderPath>
</AgentLogger>
```

### <a name="java-command-line-properties"></a>Propriedades da Linha de Comando Java
_Desde a versão 2.4.0_

Para ativar o registo utilizando opções de linha de comando, sem alterar ficheiros de configuração:

```
java -Dapplicationinsights.logger.file.level=trace -Dapplicationinsights.logger.file.uniquePrefix=AI -Dapplicationinsights.logger.baseFolderPath="C:/my/log/dir" -jar MyApp.jar
```

ou imprimir para erro padrão:

```
java -Dapplicationinsights.logger.console.level=trace -jar MyApp.jar
```

## <a name="the-azure-start-screen"></a>A tela de arranque do Azure
**Estou a olhar para [o portal Azure.](https://portal.azure.com) O mapa diz-me alguma coisa sobre a minha aplicação?**

Não, mostra a saúde dos servidores Azure em todo o mundo.

*A partir do quadro de arranque do Azure (ecrã principal), como encontro dados sobre a minha aplicação?*

Assumindo que configura a sua aplicação para Insights de [Aplicação,][java]clique em Navegar, selecione Insights de Aplicação e selecione o recurso de aplicação que criou para a sua aplicação. Para chegar lá mais rápido no futuro, pode fixar a sua app na placa de partida.

## <a name="intranet-servers"></a>Servidores intranet
**Posso monitorizar um servidor na minha intranet?**

Sim, desde que o seu servidor possa enviar telemetria para o portal Application Insights através da internet pública.

Na sua firewall, poderá ter de abrir as portas TCP 80 e 443 para tráfego de saída para dc.services.visualstudio.com e f5.services.visualstudio.com.

## <a name="data-retention"></a>Retenção de dados
**Quanto tempo os dados são retidos no portal? É seguro?**

Ver [Retenção de Dados e privacidade.][data]

## <a name="debug-logging"></a>Depuração de madeira
Aplicação `org.apache.http`Insights utiliza . Isto é recolocado dentro de frascos `com.microsoft.applicationinsights.core.dependencies.http`centrais de Application Insights sob o espaço de nome . Isto permite que os Insights de Aplicação lidem com cenários onde existem diferentes versões do mesmo `org.apache.http` numa base de código.

>[!NOTE]
>Se ativar o registo de nivelamento de DEBUG para todos os `org.apache.http` espaços de `com.microsoft.applicationinsights.core.dependencies.http`nome na aplicação, será homenageado por todos os módulos de execução, incluindo renomeadocomo . Aplicação Insights não será capaz de aplicar filtragem para estas chamadas porque a chamada de log está sendo feita pela biblioteca Apache. O nível de depuração de madeira produz uma quantidade considerável de dados de registo e não é recomendado para casos de produção ao vivo.


## <a name="next-steps"></a>Passos seguintes
**Estarei configurando as Informações de Aplicação para a minha aplicação do servidor Java. O que mais posso fazer?**

* [Monitorize a disponibilidade das suas páginas web][availability]
* [Monitorizar o uso da página web][usage]
* [Acompanhe os problemas de utilização e diagnóstico nas aplicações do seu dispositivo][platforms]
* [Escreva código para rastrear o uso da sua aplicação][track]
* [Capturar registos de diagnóstico][javalogs]

## <a name="get-help"></a>Obter ajuda
* [Stack Overflow](https://stackoverflow.com/questions/tagged/ms-application-insights)
* [Arquivar um problema no GitHub](https://github.com/Microsoft/ApplicationInsights-Java/issues)

<!--Link references-->

[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[data]: ../../azure-monitor/app/data-retention-privacy.md
[java]: java-get-started.md
[javalogs]: java-trace-logs.md
[platforms]: ../../azure-monitor/app/platforms.md
[track]: ../../azure-monitor/app/api-custom-events-metrics.md
[usage]: javascript.md

