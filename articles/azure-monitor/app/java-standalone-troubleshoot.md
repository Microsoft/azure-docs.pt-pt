---
title: Resolução de problemas Azure Monitor Application Insights for Java
description: Saiba como resolver problemas com o agente Java para Azure Monitor Application Insights
ms.topic: conceptual
ms.date: 11/30/2020
ms.custom: devx-track-java
ms.openlocfilehash: 90e0ceb6ba9d696eb446d607ed2f2f134733618e
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/27/2021
ms.locfileid: "98881141"
---
# <a name="troubleshooting-guide-azure-monitor-application-insights-for-java"></a>Guia de resolução de problemas: Azure Monitor Application Insights for Java

Neste artigo, cobrimos algumas das questões comuns que pode enfrentar ao instrumentar uma aplicação Java utilizando o agente Java para Insights de Aplicação. Também cobrimos os passos para resolver estas questões. Application Insights é uma característica do serviço de plataforma Azure Monitor.

## <a name="check-the-self-diagnostic-log-file"></a>Verifique o ficheiro de registo de autodiagnósto

Por predefinição, o agente Java 3.0 para Application Insights produz um ficheiro de registo nomeado `applicationinsights.log` no mesmo diretório que detém o `applicationinsights-agent-3.0.2.jar` ficheiro.

Este ficheiro de registo é o primeiro local para verificar se existem pistas sobre quaisquer problemas que possa estar a passar.

## <a name="jvm-fails-to-start"></a>JVM falha no arranque

Se o JVM não começar com "Error opening zip file ou JAR manifest missing", tente refivelar o ficheiro do frasco do agente porque pode ter sido corrompido durante a transferência de ficheiros.

## <a name="upgrade-from-the-application-insights-java-2x-sdk"></a>Upgrade a partir da Aplicação Insights Java 2.x SDK

Se já estiver a utilizar o Application Insights Java 2.x SDK na sua aplicação, pode continuar a ucímis. O agente Java 3.0 vai detetá-lo. Para mais informações, consulte [upgrade a partir do Java 2.x SDK](./java-standalone-upgrade-from-2x.md).

## <a name="upgrade-from-application-insights-java-30-preview"></a>Upgrade a partir de Insights de Aplicação Java 3.0 Pré-visualização

Se estiver a atualizar o agente de pré-visualização Java 3.0, reveja cuidadosamente todas as opções de [configuração.](./java-standalone-config.md) A estrutura JSON mudou completamente na versão 3.0 de disponibilidade geral (GA).

Estas alterações incluem:

-  O nome do ficheiro de configuração mudou de `ApplicationInsights.json` `applicationinsights.json` .
-  O `instrumentationSettings` nó já não está presente. Todo o conteúdo `instrumentationSettings` é movido para o nível de raiz. 
-  Os nós de configuração são `sampling` `jmxMetrics` `instrumentation` `heartbeat` movidos para fora do nível de `preview` raiz.

## <a name="some-logging-is-not-auto-collected"></a>Alguns registos não são recolhidos automaticamente

A exploração madeireira só é capturada se cumprir primeiro o limiar configurado dos quadros de registo, e em segundo lugar também cumpre o limiar configurado de Insights de Aplicação.

A melhor maneira de saber se uma determinada declaração de registo satisfaz o limiar configurado dos quadros de registo é confirmar que está a aparecer no seu registo normal de aplicações (por exemplo, ficheiro ou consola).

Consulte a [configuração de registo de registos recolhidos automaticamente](./java-standalone-config.md#auto-collected-logging) para obter mais detalhes.

## <a name="import-ssl-certificates"></a>Certificados SSL de importação

Esta secção ajuda-o a resolver problemas e possivelmente a corrigir as exceções relacionadas com os certificados SSL ao utilizar o agente Java.

Há dois caminhos diferentes para resolver esta questão.

### <a name="if-using-a-default-java-keystore"></a>Se utilizar uma Loja Java padrão:

Normalmente, a teclas java padrão já terá todos os certificados de raiz da AC. No entanto, pode haver algumas exceções, como o certificado de ponto final de ingestão pode ser assinado por um certificado de raiz diferente. Assim, recomendamos os três passos seguintes para resolver esta questão:

1.  Verifique se o certificado de raiz utilizado para assinar o ponto final do Application Insights já está presente na teclas padrão. Os certificados de CA fidedignos, por padrão, são armazenados em `$JAVA_HOME/jre/lib/security/cacerts` . Para listar certificados numa loja java, utilize o seguinte comando:
    > `keytool -list -v -keystore $PATH_TO_KEYSTORE_FILE`
 
    Pode redirecionar a saída para um ficheiro temporário como este (será fácil de pesquisar mais tarde)
    > `keytool -list -v -keystore $JAVA_HOME/jre/lib/security/cacerts > temp.txt`

2. Assim que tiver a lista de certificados, siga estes [passos](#steps-to-download-ssl-certificate) para descarregar o certificado raiz que foi usado para assinar o ponto final do Application Insights.

    Uma vez descarregado o certificado, gere um hash SHA-1 no certificado utilizando o comando abaixo:
    > `keytool -printcert -v -file "your_downloaded_root_certificate.cer"`
 
    Copie o valor SHA-1 e verifique se este valor está presente no ficheiro "temp.txt" que guardou anteriormente.  Se não conseguir encontrar o valor SHA-1 no ficheiro temporário, indica que o cert raiz descarregado está em falta na Loja Java Keystore predefinida.


3. Importar o certificado de raiz para a loja java padrão utilizando o seguinte comando:
    >   `keytool -import -file "the cert file" -alias "some meaningful name" -keystore "path to cacerts file"`
 
    Neste caso, será
 
    > `keytool -import -file "your downloaded root cert file" -alias "some meaningful name" $JAVA_HOME/jre/lib/security/cacerts`


### <a name="if-using-a-custom-java-keystore"></a>Se utilizar um Java Keystore personalizado:

Se estiver a utilizar uma loja java personalizada, poderá ter de importar os certificados SSL de raiz de raiz do Ponto final do Application Insights.
Recomendamos os dois passos seguintes para resolver esta questão:
1. Siga estes [passos](#steps-to-download-ssl-certificate) para descarregar o certificado raiz do ponto final do Application Insights.
2. Utilize o seguinte comando para importar o certificado SSL raiz para a loja de chaves Java personalizada:
    > `keytool -importcert -alias your_ssl_certificate -file "your downloaded SSL certificate name.cer" -keystore "Your KeyStore name" -storepass "Your keystore password" -noprompt`

### <a name="steps-to-download-ssl-certificate"></a>Passos para descarregar certificado SSL

1.  Abra o seu navegador favorito e vá para o `IngestionEndpoint` URL presente na cadeia de ligação que é usada para instrumentar a sua aplicação.

    :::image type="content" source="media/java-ipa/troubleshooting/ingestion-endpoint-snippet.png" alt-text="Screenshot que mostra uma cadeia de ligação Application Insights." lightbox="media/java-ipa/troubleshooting/ingestion-endpoint-snippet.png":::

2.  Selecione o ícone **de informações do site (bloqueio)** no navegador e, em seguida, selecione a opção **Certificado.**

    :::image type="content" source="media/java-ipa/troubleshooting/certificate-icon-capture.png" alt-text="Screenshot da opção Certificado nas informações do site." lightbox="media/java-ipa/troubleshooting/certificate-icon-capture.png":::

3.  Em vez de descarregar o certificado 'folha' deve descarregar o certificado 'raiz', conforme mostrado abaixo. Mais tarde, tem de clicar no "Caminho do Certificado" -> Selecione o Certificado raiz -> Clique em 'Ver Certificado'. Isto irá aparecer um novo menu de certificados e você pode baixar o certificado, a partir do novo menu.

    :::image type="content" source="media/java-ipa/troubleshooting/root-certificate-selection.png" alt-text="Screenshot de como selecionar o certificado de raiz." lightbox="media/java-ipa/troubleshooting/root-certificate-selection.png":::

4.  Vá ao separador **Detalhes** e selecione **Copy para arquivar.**
5.  Selecione o botão **Seguinte,** selecione **Base-64 codificado X.509 (. Formato CER)** e, em seguida, selecione **Next** again.

    :::image type="content" source="media/java-ipa/troubleshooting/certificate-export-wizard.png" alt-text="Screenshot do Assistente de Exportação de Certificados, com um formato selecionado." lightbox="media/java-ipa/troubleshooting/certificate-export-wizard.png":::

6.  Especifique o ficheiro onde pretende guardar o certificado SSL. Em seguida, selecione **Next**  >  **Finish**. Devias ver uma mensagem de "A exportação foi bem sucedida".

> [!WARNING]
> Terá de repetir estes passos para obter o novo certificado antes que o certificado atual expire. Pode encontrar as informações de expiração no separador **Detalhes** da caixa de diálogo **certificate.**
>
> :::image type="content" source="media/java-ipa/troubleshooting/certificate-details.png" alt-text="Screenshot que mostra detalhes do certificado SSL." lightbox="media/java-ipa/troubleshooting/certificate-details.png":::
