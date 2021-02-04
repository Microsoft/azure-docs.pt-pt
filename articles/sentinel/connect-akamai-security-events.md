---
title: Ligue o seu colecionador de Eventos de Segurança Akamai ao Azure Sentinel | Microsoft Docs
description: Saiba como usar o conector Akamai Security Events para puxar os registos de segurança das soluções Daacas para o Azure Sentinel. Ver dados da Akamai em livros de trabalho, criar alertas e melhorar a investigação.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/03/2021
ms.author: yelevin
ms.openlocfilehash: c833d87b8d85c75c4f050f0130ddfd74342f4c52
ms.sourcegitcommit: f82e290076298b25a85e979a101753f9f16b720c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/04/2021
ms.locfileid: "99566872"
---
# <a name="connect-your-akamai-security-events-collector-to-azure-sentinel"></a>Ligue o seu colecionador de Eventos de Segurança Akamai ao Azure Sentinel

> [!IMPORTANT]
> O conector de eventos de segurança akamai está atualmente em **PREVIEW**. Consulte os [Termos Complementares de Utilização para o Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para termos legais adicionais aplicáveis às funcionalidades do Azure que estejam em versão beta, pré-visualização ou ainda não lançadas em disponibilidade geral.

Este artigo explica como ligar o seu colecionador de Eventos de Segurança Akamai ao Azure Sentinel. O conector de dados da Akamai Security Events permite-lhe conectar facilmente os seus registos Akamai com o Azure Sentinel, para que possa ver os dados nos livros, consultar-nos para criar alertas personalizados e incorporá-lo para melhorar a investigação. A integração entre o colecionador de Eventos de Segurança akamai e o Azure Sentinel faz uso do Syslog formatado pelo CEF, um reencaminhador baseado em Linux e do agente Log Analytics. Também usa um parser de log personalizado baseado numa função Kusto.

> [!NOTE]
> Os dados serão armazenados na localização geográfica do espaço de trabalho em que está a executar o Azure Sentinel.

## <a name="prerequisites"></a>Pré-requisitos

- Deve ter lido e escrito permissões no seu espaço de trabalho Azure Sentinel.

- Deve ter lido permissões para partilhar chaves para o espaço de trabalho. [Saiba mais sobre as teclas do espaço de trabalho.](../azure-monitor/platform/log-analytics-agent.md#workspace-id-and-key)

## <a name="send-akamai-security-events-logs-to-azure-sentinel"></a>Envie registos de eventos de segurança da Akamai para Azure Sentinel

Para obter os seus logins no Azure Sentinel, configura o seu colecionador de Eventos de Segurança Akamai para enviar mensagens Syslog em formato CEF para um servidor de reencaminhamento de log baseado em Linux (executando rsyslog ou syslog-ng). Este servidor terá o agente Log Analytics instalado nele, e o agente encaminha os registos para o seu espaço de trabalho Azure Sentinel.

1. No menu de navegação Azure Sentinel, selecione **Conectores de dados**.

1. Na galeria **de conectores de dados,** selecione **Eventos de Segurança da Akamai (Pré-visualização)** e, em seguida, **Abra a página do conector**.

1. Siga as instruções no separador **Instruções,** na **Configuração:**

    1. Menos **de 1. Configuração do agente Linux Syslog** - Faça este passo se ainda não tiver um reencaminhador de log em execução, ou se precisar de outro. Ver [PASSO 1: Desloque o reencaminhador](connect-cef-agent.md) de registo na documentação do Sentinela Azure para obter informações de dimensionamento, instruções mais detalhadas e explicação aprofundada.

    1. Menos **de 2. Forward Common Event Format (CEF) registra para o agente Syslog** - Siga as instruções da Akamai para [configurar](https://developer.akamai.com/tools/integrations/siem) a integração do SIEM e [para configurar um conector CEF](https://developer.akamai.com/tools/integrations/siem/siem-cef-connector). Este conector recebe eventos de segurança das suas soluções Akamai em quase tempo real utilizando a API SIEM OPEN e converte-os de JSON em formato CEF.
    
        Esta configuração deve incluir os seguintes elementos:
    
        - Destino de registo - o nome de anfitrião e/ou endereço IP do seu servidor de reencaminhamento de registo
        - Protocolo e porta – TCP 514 (se recomendado de outra forma, certifique-se de fazer a alteração paralela no daemon do syslog no seu servidor de reencaminhamento de log)
        - Formato de registo – CEF
        - Tipos de registos - todos disponíveis

    1. Menos **de 3. Validar a ligação** - Verifique a ingestão de dados copiando o comando na página do conector e executando-o no seu reencaminhador de registo. Ver [PASSO 3: Validar a conectividade](connect-cef-verify.md) na documentação do Sentinela Azure para obter instruções e explicações mais detalhadas.

        Pode demorar até que os seus registos comecem a aparecer no Log Analytics.

## <a name="find-your-data"></a>Encontre os seus dados

Após a criação de uma ligação bem sucedida, os dados aparecem em **Logs,** sob a secção **Azure Sentinel,** na tabela *CommonSecurityLog.*

Este conector de dados depende de um parser baseado numa Função Kusto para funcionar como esperado. Utilize os seguintes passos para configurar a Função **AkamaiSIEMEvent** Kusto para utilizar em consultas e livros de trabalho.

1. A partir do menu de navegação Azure Sentinel, selecione **Logs**.

1. Copie a seguinte consulta e cole-a na janela de consulta.
    ```kusto
    CommonSecurityLog 
    | where DeviceVendor == 'Akamai'
    | where DeviceProduct == 'akamai_siem'
    | extend EventVendor = 'Akamai'
    | extend EventProduct = 'akamai_siem'
    | extend EventProductVersion = '1.0'
    | extend EventId = DeviceEventClassID
    | extend EventCategory = Activity
    | extend EventSeverity = LogSeverity
    | extend DvcAction = DeviceAction
    | extend NetworkApplicationProtocol = ApplicationProtocol
    | extend Ipv6Src = DeviceCustomIPv6Address2
    | extend RuleName = DeviceCustomString1
    | extend RuleMessages = DeviceCustomString2
    | extend RuleData = DeviceCustomString3
    | extend RuleSelectors = DeviceCustomString4
    | extend ClientReputation = DeviceCustomString5
    | extend ApiId = DeviceCustomString6
    | extend RequestId = DevicePayloadId
    | extend DstDvcHostname = DestinationHostName
    | extend DstPortNumber = DestinationPort
    | extend ConfigId = FlexString1
    | extend PolicyId = FlexString2
    | extend NetworkBytes = SentBytes
    | extend UrlOriginal = RequestURL
    | extend HttpRequestMethod = RequestMethod
    | extend SrcIpAddr = SourceIP
    | extend EventStartTime = datetime(1970-01-01) + tolong(extract(@'.*start=(.*?);', 1, AdditionalExtensions)) * 1s 
    | extend SlowPostAction = extract(@'.*AkamaiSiemSlowPostAction=(.*?);', 1, AdditionalExtensions)
    | extend SlowPostRate = extract(@'.*AkamaiSiemSlowPostRate=(.*?);', 1, AdditionalExtensions)
    | extend RuleVersions = extract(@'.*AkamaiSiemRuleVersions=,?(.*?);', 1, AdditionalExtensions)
    | extend RuleTags = extract(@'.*AkamaiSiemRuleTags=(.*?);', 1, AdditionalExtensions)
    | extend ApiKey = extract(@'.*AkamaiSiemApiKey=(.*?);', 1, AdditionalExtensions)
    | extend Tls = extract(@'.*AkamaiSiemTLSVersion=(.*?);', 1, AdditionalExtensions)
    | extend RequestHeaders = extract(@'.*AkamaiSiemRequestHeaders=;?(.*?);', 1, AdditionalExtensions)
    | extend ResponseHeaders = extract(@'.*AkamaiSiemResponseHeaders=(.*?);', 1, AdditionalExtensions)
    | extend HttpStatusCode = extract(@'.*AkamaiSiemResponseStatus=(.*?);', 1, AdditionalExtensions)
    | extend GeoContinent = extract(@'.*AkamaiSiemContinent=(.*?);', 1, AdditionalExtensions)
    | extend SrcGeoCountry = extract(@'.*AkamaiSiemCountry=(.*?);', 1, AdditionalExtensions)
    | extend SrcGeoCity = extract(@'.*AkamaiSiemCity=(.*?);', 1, AdditionalExtensions)
    | extend SrcGeoRegion = extract(@'.*AkamaiSiemRegion=(.*?);', 1, AdditionalExtensions)
    | extend GeoAsn = extract(@'.*AkamaiSiemASN=(\d+)', 1, AdditionalExtensions)
    | extend Custom = extract(@'.*AkamaiSiemCusomData=(.*?)', 1, AdditionalExtensions)
    | project TimeGenerated
            , EventVendor
            , EventProduct
            , EventProductVersion
            , EventStartTime
            , EventId
            , EventCategory
            , EventSeverity
            , DvcAction
            , NetworkApplicationProtocol
            , Ipv6Src
            , RuleName
            , RuleMessages
            , RuleData
            , RuleSelectors
            , ClientReputation
            , ApiId
            , RequestId
            , DstDvcHostname
            , DstPortNumber
            , ConfigId
            , PolicyId
            , NetworkBytes
            , UrlOriginal
            , HttpRequestMethod
            , SrcIpAddr
            , SlowPostAction
            , SlowPostRate
            , RuleVersions
            , RuleTags
            , ApiKey
            , Tls
            , RequestHeaders
            , ResponseHeaders
            , HttpStatusCode
            , GeoContinent
            , SrcGeoCountry
            , SrcGeoCity
            , SrcGeoRegion
            , GeoAsn
            , Custom
    ```

1. Clique no **Drop-down Save** e clique em **Guardar**. No painel **Save,**

    1. Under **Name**, **insiemEvent akamaiSIEMEvent**.

    1. Em **Guardar como**, escolha **Função**.

    1. Em **Alias de Função,** insira **a AkamaiSIEMEvent**.

    1. Na **categoria**, insira **funções**.

    1. Clique em **Guardar**.

    As aplicações de função normalmente demoram entre 10 e 15 minutos a ser ativadas.

Agora está pronto para consultar os dados da Akamai, introduzindo `AkamaiSIEMEvent` na linha superior da janela de consulta.

Consulte o separador **passos seguintes** na página do conector para obter mais amostras de consulta.

## <a name="next-steps"></a>Passos seguintes

Neste documento, aprendeu a ligar os Eventos de Segurança da Akamai ao Azure Sentinel. Para saber mais sobre Azure Sentinel, consulte os seguintes artigos:

- Saiba como [obter visibilidade nos seus dados e ameaças potenciais.](quickstart-get-visibility.md)
- Começa [a detetar ameaças com o Azure Sentinel.](tutorial-detect-threats-built-in.md)
- [Utilize livros para](tutorial-monitor-your-data.md) monitorizar os seus dados.
