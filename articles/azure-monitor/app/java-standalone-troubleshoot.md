---
title: Resolução de problemas - Azure Monitor Application Insights Java
description: Resolução de problemas Azure Monitor Application Insights Java
ms.topic: conceptual
ms.date: 11/30/2020
ms.custom: devx-track-java
ms.openlocfilehash: cf27763f857cc1fd1aad5256d0c6cecf91251caf
ms.sourcegitcommit: 48cb2b7d4022a85175309cf3573e72c4e67288f5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2020
ms.locfileid: "96855660"
---
# <a name="troubleshooting-azure-monitor-application-insights-java"></a>Resolução de problemas Azure Monitor Application Insights Java

Neste artigo, abordámos algumas das questões comuns que um utilizador pode enfrentar ao instrumentor a aplicação java utilizando o agente java juntamente com as medidas para resolver estes problemas.

## <a name="self-diagnostic-log-file"></a>Arquivo de registo de autodiagnósto

Por predefinição, a Application Insights Java 3.0 produzirá um ficheiro de registo nomeado `applicationinsights.log` no mesmo diretório onde o `applicationinsights-agent-3.0.0.jar` ficheiro está localizado.

Este ficheiro de registo é o primeiro local para verificar se existem pistas sobre quaisquer problemas que possa estar a ter.

## <a name="upgrade-from-application-insights-java-2x-sdk"></a>Upgrade a partir de Application Insights Java 2.x SDK

Ver [Upgrade a partir de 2.x SDK](./java-standalone-upgrade-from-2x.md).

## <a name="upgrade-from-30-preview"></a>Atualização a partir de 3.0 Pré-visualização

Se atualizar a partir de 3.0 Preview, por favor reveja cuidadosamente todas as opções de [configuração,](./java-standalone-config.md) uma vez que a estrutura json mudou completamente na versão 3.0 GA.

Estas alterações incluem:

1.  O próprio nome do ficheiro de configuração mudou de `ApplicationInsights.json` `applicationinsights.json` .
2.  O `instrumentationSettings` nó já não está presente. Todo o conteúdo `instrumentationSettings` é movido para o nível de raiz. 
3.  Os nós de configuração são `sampling` `jmxMetrics` `instrumentation` `heartbeat` movidos para fora do nível de `preview` raiz.

## <a name="ssl-certificate-issues"></a>Emissões de certificados SSL

Se estiver a utilizar a teclas Java padrão, já terá todos os certificados de raiz da AC e não deverá precisar de importar mais certificados SSL.

Se estiver a utilizar uma loja java personalizada, poderá ter de importar os certificados SSL SSL do Ponto Final da Aplicação.

### <a name="some-key-terminology"></a>Alguma terminologia chave:
*Keystore* é um repositório de certificados, chaves públicas e privadas. Normalmente, as distribuições JDK têm um executável para geri-las – `keytool` .

O exemplo a seguir é um simples comando para importar um certificado SSL para a teclastore:

`keytool -importcert -alias your_ssl_certificate -file "your downloaded SSL certificate name".cer -keystore "Your KeyStore name" -storepass "Your keystore password" -noprompt`

### <a name="steps-to-download-and-add-the-ssl-certificate"></a>Passos para descarregar e adicionar o Certificado SSL:

1.  Abra o seu navegador favorito e vá para o `IngestionEndpoint` url presente na Cadeia de Conexão usada para instrumentar a sua aplicação como mostrado abaixo

    :::image type="content" source="media/java-ipa/troubleshooting/ingestion-endpoint-url.png" alt-text="Cadeia de conexão de insights de aplicação":::

2.  Clique no ícone 'Ver informações do site' (bloqueio) no navegador e clique na opção 'Certificate', como mostra abaixo

    :::image type="content" source="media/java-ipa/troubleshooting/certificate-icon-capture.png" alt-text="Captura de certificado SSL":::

3.  Vá ao separador detalhes e clique em copiar para arquivar.
4.  Clique no botão seguinte e selecione "Base-64 codificado X.509 (. CER)" formato e selecione a seguir.

    :::image type="content" source="media/java-ipa/troubleshooting/certificate-export-wizard.png" alt-text="Certificado SSL ExportWizard":::

5.  Especifique o ficheiro para o qual pretende guardar o certificado SSL. Finalmente clique em seguida e termine. Devias ver a mensagem "A exportação foi bem sucedida".
6.  Uma vez que tenha o certificado, é hora de importar o certificado numa loja java. Utilize o [comando](#some-key-terminology) acima para importar certificados.

> [!WARNING]
> Terá de repetir estes passos para obter o novo certificado antes de expirar o certificado atual. Pode encontrar as informações de expiração no separador "Detalhes" do popup certificado, conforme mostrado abaixo

:::image type="content" source="media/java-ipa/troubleshooting/certificate-details.png" alt-text="Detalhes do certificado SSL":::
