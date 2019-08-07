---
title: Solucionar problemas Application Insights em um projeto Web Java
description: Guia de solução de problemas-monitoramento de aplicativos Java em tempo real com Application Insights.
services: application-insights
documentationcenter: java
author: mrbullwinkle
manager: carmonm
ms.assetid: ef602767-18f2-44d2-b7ef-42b404edd0e9
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: mbullwin
ms.openlocfilehash: a26302b0c0b4361fe3e7aae6aba798f433c72ade
ms.sourcegitcommit: d060947aae93728169b035fd54beef044dbe9480
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/02/2019
ms.locfileid: "68742189"
---
# <a name="troubleshooting-and-q-and-a-for-application-insights-for-java"></a>Resolução de problemas e Perguntas e Respostas para o Application Insights para Java
Perguntas ou problemas com o [aplicativo Azure insights em Java][java]? Aqui estão algumas dicas.

## <a name="build-errors"></a>Erros de compilação
**No Eclipse ou no IntelliJ IDEA, ao adicionar o SDK do Application Insights via Maven ou gradle, obtenho erros de validação de compilação ou de soma de verificação.**

* Se o elemento `<version>` Dependency estiver usando um padrão com caracteres curinga (por exemplo, Maven ou (gradle) `version:'2.0.+'`), tente especificar uma versão específica, como `2.0.1`. `<version>[2.0,)</version>` Consulte as [notas de versão](https://github.com/Microsoft/ApplicationInsights-Java/releases) para obter a versão mais recente.

## <a name="no-data"></a>Nenhuns dados
**Adicionei Application Insights com êxito e executei meu aplicativo, mas nunca vi dados no Portal.**

* Aguarde um minuto e clique em atualizar. Os gráficos se atualizam periodicamente, mas você também pode atualizar manualmente. O intervalo de atualização depende do intervalo de tempo do gráfico.
* Verifique se você tem uma chave de instrumentação definida no arquivo ApplicationInsights. XML (na pasta recursos em seu projeto) ou configurada como variável de ambiente.
* Verifique se não há nenhum `<DisableTelemetry>true</DisableTelemetry>` nó no arquivo XML.
* No firewall, talvez seja necessário abrir as portas TCP 80 e 443 para o tráfego de saída para o dc.services.visualstudio.com. Consulte a [lista completa de exceções de firewall](../../azure-monitor/app/ip-addresses.md)
* Na Microsoft Azure quadro inicial, examine o mapa de status do serviço. Se houver algumas indicações de alerta, aguarde até que elas sejam retornadas para OK e, em seguida, feche e abra novamente sua folha de aplicativo Application Insights.
* [Ative o registro em log](#debug-data-from-the-sdk) adicionando `<SDKLogger />` um elemento sob o nó raiz no arquivo ApplicationInsights. XML (na pasta recursos em seu projeto) e verifique se há entradas precedidas com ia: INFORMAÇÕES/aviso/erro para quaisquer logs suspeitos. 
* Verifique se o arquivo ApplicationInsights. xml correto foi carregado com êxito pelo SDK do Java, examinando as mensagens de saída do console para uma instrução "arquivo de configuração foi encontrado com êxito".
* Se o arquivo de configuração não for encontrado, verifique as mensagens de saída para ver onde o arquivo de configuração está sendo pesquisado e verifique se o ApplicationInsights. xml está localizado em um desses locais de pesquisa. Como regra geral, você pode posicionar o arquivo de configuração perto dos JARs Application Insights SDK. Por exemplo: no Tomcat, isso significa a pasta WEB-INF/classes. Durante o desenvolvimento, você pode posicionar o ApplicationInsights. xml na pasta de recursos do seu projeto Web.
* Consulte também a [página de problemas do GitHub](https://github.com/Microsoft/ApplicationInsights-Java/issues) para problemas conhecidos com o SDK.
* Certifique-se de usar a mesma versão dos acrescentadores de Application Insights Core, Web, Agent e Logging para evitar problemas de conflito de versão.

#### <a name="i-used-to-see-data-but-it-has-stopped"></a>Eu costumava ver os dados, mas ele foi interrompido
* Verifique o [blog de status](https://blogs.msdn.com/b/applicationinsights-status/).
* Você atingiu sua cota mensal de pontos de dados? Abra configurações/cota e preços para descobrir. Nesse caso, você pode atualizar seu plano ou pagar por capacidade adicional. Consulte o [esquema de preços](https://azure.microsoft.com/pricing/details/application-insights/).
* Você atualizou seu SDK recentemente? Verifique se apenas os jars do SDK exclusivos estão presentes no diretório do projeto. Não deve haver duas versões diferentes do SDK presentes.
* Você está olhando para o recurso de ia correto? Faça a correspondência entre o iKey do seu aplicativo e o recurso em que você espera a telemetria. Eles devem ser iguais.

#### <a name="i-dont-see-all-the-data-im-expecting"></a>Não vejo todos os dados que estou esperando
* Abra a página uso e custo estimado e verifique se a [amostragem](../../azure-monitor/app/sampling.md) está em operação. (100% de transmissão significa que a amostragem não está em operação.) O serviço de Application Insights pode ser definido para aceitar apenas uma fração da telemetria que chega do seu aplicativo. Isso ajuda você a manter em sua cota mensal de telemetria.
* Você tem a amostragem do SDK ativada? Em caso afirmativo, os dados seriam amostrados na taxa especificada para todos os tipos aplicáveis.
* Você está executando uma versão mais antiga do SDK do Java? A partir da versão 2.0.1, apresentamos um mecanismo de tolerância a falhas para lidar com falhas intermitentes de rede e back-end, bem como persistência de dados em unidades locais.
* Você está ficando limitado devido à telemetria excessiva? Se você ativar o registro de informações, verá uma mensagem de log "o aplicativo está limitado". Nosso limite atual são os itens de telemetria de 32K/segundo.

### <a name="java-agent-cannot-capture-dependency-data"></a>O agente Java não pode capturar dados de dependência
* Você configurou o agente Java seguindo [Configurar o agente Java](java-agent.md) ?
* Certifique-se de que tanto o JAR do agente Java quanto o arquivo AI-Agent. XML sejam colocados na mesma pasta.
* Verifique se a dependência que você está tentando coletar automaticamente tem suporte para a coleta automática. Atualmente, há suporte apenas para MySQL, MsSQL, Oracle DB e cache do Azure para coleta de dependência Redis.
* Você está usando o JDK 1,7 ou o 1,8? No momento, não há suporte para a coleta de dependência no JDK 9.

## <a name="no-usage-data"></a>Nenhum dado de uso
**Vejo dados sobre solicitações e tempos de resposta, mas nenhum modo de exibição de página, navegador ou dados de usuário.**

Você configurou com êxito seu aplicativo para enviar telemetria do servidor. Agora, a próxima etapa é [configurar suas páginas da Web para enviar telemetria do navegador da Web][usage].

Como alternativa, se o cliente for um aplicativo em um [telefone ou outro dispositivo][platforms], você poderá enviar telemetria a partir daí.

Use a mesma chave de instrumentação para configurar a telemetria do cliente e do servidor. Os dados aparecerão no mesmo Application Insights recurso, e você poderá correlacionar eventos do cliente e do servidor.


## <a name="disabling-telemetry"></a>Desabilitando a telemetria
**Como posso desabilitar a coleta de telemetria?**

No código:

```Java

    TelemetryConfiguration config = TelemetryConfiguration.getActive();
    config.setTrackingIsDisabled(true);
```

**ou**

Atualize ApplicationInsights. XML (na pasta recursos em seu projeto). Adicione o seguinte sob o nó raiz:

```XML

    <DisableTelemetry>true</DisableTelemetry>
```

Usando o método XML, é necessário reiniciar o aplicativo quando você altera o valor.

## <a name="changing-the-target"></a>Alterando o destino
**Como posso alterar a qual recurso do Azure meu projeto envia dados?**

* [Obtenha a chave de instrumentação do novo recurso.][java]
* Se você adicionou Application Insights ao seu projeto usando o Azure Toolkit for Eclipse, clique com o botão direito do mouse no projeto Web, selecione **Azure**, **Configurar Application insights**e altere a chave.
* Se você configurou a chave de instrumentação como variável de ambiente, atualize o valor da variável de ambiente com New iKey.
* Caso contrário, atualize a chave em ApplicationInsights. xml na pasta recursos em seu projeto.

## <a name="debug-data-from-the-sdk"></a>Depurar dados do SDK

**Como posso descobrir o que o SDK está fazendo?**

Para obter mais informações sobre o que está acontecendo na API, adicione `<SDKLogger/>` sob o nó raiz do arquivo de configuração ApplicationInsights. xml.

### <a name="applicationinsightsxml"></a>ApplicationInsights.xml

Você também pode instruir o agente a gerar uma saída para um arquivo:

```XML
  <SDKLogger type="FILE"><!-- or "CONSOLE" to print to stderr -->
    <Level>TRACE</Level>
    <UniquePrefix>AI</UniquePrefix>
    <BaseFolderPath>C:/agent/AISDK</BaseFolderPath>
</SDKLogger>
```

### <a name="spring-boot-starter"></a>Iniciador do Spring boot

Para habilitar o log do SDK com aplicativos Spring boot usando o iniciador do Spring boot Application insights, adicione o `application.properties` seguinte ao arquivo:

```yaml
azure.application-insights.logger.type=file
azure.application-insights.logger.base-folder-path=C:/agent/AISDK
azure.application-insights.logger.level=trace
```

ou para imprimir para o erro padrão:

```yaml
azure.application-insights.logger.type=console
azure.application-insights.logger.level=trace
```

### <a name="java-agent"></a>Agente Java

Para habilitar o log do agente JVM, atualize o [arquivo ai-Agent. xml](java-agent.md):

```xml
<AgentLogger type="FILE"><!-- or "CONSOLE" to print to stderr -->
    <Level>TRACE</Level>
    <UniquePrefix>AI</UniquePrefix>
    <BaseFolderPath>C:/agent/AIAGENT</BaseFolderPath>
</AgentLogger>
```

### <a name="java-command-line-properties"></a>Propriedades de linha de comando Java
_Desde a versão 2.4.0_

Para habilitar o registro em log usando opções de linha de comando, sem alterar os arquivos de configuração:

```
java -Dapplicationinsights.logger.file.level=trace -Dapplicationinsights.logger.file.uniquePrefix=AI -Dapplicationinsights.logger.baseFolderPath="C:/my/log/dir" -jar MyApp.jar
```

ou para imprimir para o erro padrão:

```
java -Dapplicationinsights.logger.console.level=trace -jar MyApp.jar
```

## <a name="the-azure-start-screen"></a>A tela inicial do Azure
**Estou olhando para [o portal do Azure](https://portal.azure.com). O mapa me diz algo sobre meu aplicativo?**

Não, ele mostra a integridade dos servidores do Azure em todo o mundo.

*No quadro inicial do Azure (tela inicial), como encontro dados sobre meu aplicativo?*

Supondo que você [configurou seu aplicativo para Application insights][java], clique em procurar, selecione Application insights e selecione o recurso de aplicativo que você criou para seu aplicativo. Para obter mais rápido no futuro, você pode fixar seu aplicativo no quadro inicial.

## <a name="intranet-servers"></a>Servidores de intranet
**Posso monitorar um servidor em minha intranet?**

Sim, desde que o servidor possa enviar telemetria para o portal de Application Insights por meio da Internet pública.

No firewall, talvez seja necessário abrir as portas TCP 80 e 443 para o tráfego de saída para dc.services.visualstudio.com e f5.services.visualstudio.com.

## <a name="data-retention"></a>Retenção de dados
**Por quanto tempo os dados são retidos no portal? É seguro?**

Consulte [retenção e privacidade de dados][data].

## <a name="debug-logging"></a>Log de depuração
O Application Insights `org.apache.http`usa. Isso é realocado dentro de Application Insights jars principais no `com.microsoft.applicationinsights.core.dependencies.http`namespace. Isso permite que Application insights manipulem cenários em que diferentes versões do `org.apache.http` mesmo existam em uma base de código.

>[!NOTE]
>Se você habilitar o log de nível de depuração para todos os namespaces no aplicativo, ele será respeitado por todos `org.apache.http` os módulos em execução `com.microsoft.applicationinsights.core.dependencies.http`, incluindo renomeado como. Application Insights não poderá aplicar a filtragem para essas chamadas porque a chamada de log está sendo feita pela biblioteca do Apache. O log de nível de depuração produz uma quantidade considerável de dados de log e não é recomendado para instâncias de produção ao vivo.


## <a name="next-steps"></a>Passos seguintes
**Configurei Application Insights para meu aplicativo de servidor Java. O que mais posso fazer?**

* [Monitorar a disponibilidade de suas páginas da Web][availability]
* [Monitorar o uso de páginas da Web][usage]
* [Acompanhar o uso e diagnosticar problemas em seus aplicativos de dispositivo][platforms]
* [Escrever código para acompanhar o uso de seu aplicativo][track]
* [Capturar logs de diagnóstico][javalogs]

## <a name="get-help"></a>Obter ajuda
* [Stack Overflow](https://stackoverflow.com/questions/tagged/ms-application-insights)
* [Arquivo de um problema no GitHub](https://github.com/Microsoft/ApplicationInsights-Java/issues)

<!--Link references-->

[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[data]: ../../azure-monitor/app/data-retention-privacy.md
[java]: java-get-started.md
[javalogs]: java-trace-logs.md
[platforms]: ../../azure-monitor/app/platforms.md
[track]: ../../azure-monitor/app/api-custom-events-metrics.md
[usage]: javascript.md

