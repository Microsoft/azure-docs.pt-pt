---
title: Resolução de problemas Azure Monitor Application Insights for Java
description: Saiba como resolver problemas com o agente Java para Azure Monitor Application Insights
ms.topic: conceptual
ms.date: 11/30/2020
ms.custom: devx-track-java
ms.openlocfilehash: 1ccfd583b58d129268af2a94e3072200e58308cd
ms.sourcegitcommit: fa807e40d729bf066b9b81c76a0e8c5b1c03b536
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/11/2020
ms.locfileid: "97347835"
---
# <a name="troubleshooting-guide-azure-monitor-application-insights-for-java"></a>Guia de resolução de problemas: Azure Monitor Application Insights for Java

Neste artigo, cobrimos algumas das questões comuns que pode enfrentar ao instrumentar uma aplicação Java utilizando o agente Java para Insights de Aplicação. Também cobrimos os passos para resolver estas questões. Application Insights é uma característica do serviço de plataforma Azure Monitor.

## <a name="check-the-self-diagnostic-log-file"></a>Verifique o ficheiro de registo de autodiagnósto

Por predefinição, o agente Java 3.0 para Application Insights produz um ficheiro de registo nomeado `applicationinsights.log` no mesmo diretório que detém o `applicationinsights-agent-3.0.0.jar` ficheiro.

Este ficheiro de registo é o primeiro local para verificar se existem pistas sobre quaisquer problemas que possa estar a passar.

## <a name="upgrade-from-the-application-insights-java-2x-sdk"></a>Upgrade a partir da Aplicação Insights Java 2.x SDK

Se já estiver a utilizar o Application Insights Java 2.x SDK na sua aplicação, pode continuar a ucímis. O agente Java 3.0 vai detetá-lo. Para mais informações, consulte [upgrade a partir do Java 2.x SDK](./java-standalone-upgrade-from-2x.md).

## <a name="upgrade-from-application-insights-java-30-preview"></a>Upgrade a partir de Insights de Aplicação Java 3.0 Pré-visualização

Se estiver a atualizar o agente de pré-visualização Java 3.0, reveja cuidadosamente todas as opções de [configuração.](./java-standalone-config.md) A estrutura JSON mudou completamente na versão 3.0 de disponibilidade geral (GA).

Estas alterações incluem:

-  O nome do ficheiro de configuração mudou de `ApplicationInsights.json` `applicationinsights.json` .
-  O `instrumentationSettings` nó já não está presente. Todo o conteúdo `instrumentationSettings` é movido para o nível de raiz. 
-  Os nós de configuração são `sampling` `jmxMetrics` `instrumentation` `heartbeat` movidos para fora do nível de `preview` raiz.

## <a name="import-ssl-certificates"></a>Certificados SSL de importação

Se estiver a utilizar a loja java padrão, já terá todos os certificados de raiz da AC. Não devia importar mais certificados SSL.

Se estiver a utilizar uma loja java personalizada, poderá ter de importar os certificados SSL de ponto final da Aplicação Insights para o mesmo.

### <a name="key-terminology"></a>Terminologia chave
Uma *loja é* um repositório de certificados, chaves públicas e chaves privadas. Normalmente, as distribuições do Java Development Kit têm um executável para geri-los: `keytool` .

O exemplo a seguir é um simples comando para importar um certificado SSL para a teclastore:

`keytool -importcert -alias your_ssl_certificate -file "your downloaded SSL certificate name".cer -keystore "Your KeyStore name" -storepass "Your keystore password" -noprompt`

### <a name="steps-to-download-and-add-an-ssl-certificate"></a>Passos para descarregar e adicionar um certificado SSL

1.  Abra o seu navegador favorito e vá para o `IngestionEndpoint` URL presente na cadeia de ligação que é usada para instrumentar a sua aplicação.

    :::image type="content" source="media/java-ipa/troubleshooting/ingestion-endpoint-url.png" alt-text="Screenshot que mostra uma cadeia de ligação Application Insights.":::

2.  Selecione o ícone **de informações do site (bloqueio)** no navegador e, em seguida, selecione a opção **Certificado.**

    :::image type="content" source="media/java-ipa/troubleshooting/certificate-icon-capture.png" alt-text="Screenshot da opção Certificado nas informações do site.":::

3.  Vá ao separador **Detalhes** e selecione **Copy para arquivar.**
4.  Selecione o botão **Seguinte,** selecione **Base-64 codificado X.509 (. Formato CER)** e, em seguida, selecione **Next** again.

    :::image type="content" source="media/java-ipa/troubleshooting/certificate-export-wizard.png" alt-text="Screenshot do Assistente de Exportação de Certificados, com um formato selecionado.":::

5.  Especifique o ficheiro onde pretende guardar o certificado SSL. Em seguida, selecione **Next**  >  **Finish**. Devias ver uma mensagem de "A exportação foi bem sucedida".
6.  Depois de ter o certificado, é hora de importar o certificado numa loja java. Utilize o [comando anterior](#key-terminology) para importar certificados.

> [!WARNING]
> Terá de repetir estes passos para obter o novo certificado antes que o certificado atual expire. Pode encontrar as informações de expiração no separador **Detalhes** da caixa de diálogo **certificate.**
>
> :::image type="content" source="media/java-ipa/troubleshooting/certificate-details.png" alt-text="Screenshot que mostra detalhes do certificado SSL.":::
