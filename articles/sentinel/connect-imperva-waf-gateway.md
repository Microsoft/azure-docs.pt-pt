---
title: Ligue o seu aparelho Imperva WAF Gateway ao Azure Sentinel | Microsoft Docs
description: Aprenda a utilizar o conector Imperva WAF Gateway para puxar os registos da Waf do Imperva para o Azure Sentinel. Veja os dados da Imperva WAF nos livros, crie alertas e melhore a investigação.
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
ms.openlocfilehash: a37abf369d1f34dc8f4a27802dfad88dab79be44
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101698438"
---
# <a name="connect-your-imperva-waf-gateway-appliance-to-azure-sentinel"></a>Ligue o seu aparelho Imperva WAF Gateway ao Azure Sentinel

> [!IMPORTANT]
> O conector Imperva WAF Gateway encontra-se atualmente em **PREVIEW**. Consulte os [Termos Complementares de Utilização para o Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para termos legais adicionais aplicáveis às funcionalidades do Azure que estejam em versão beta, pré-visualização ou ainda não lançadas em disponibilidade geral.

Este artigo explica como ligar o seu aparelho Imperva WAF Gateway ao Azure Sentinel. O conector de dados Imperva WAF Gateway permite-lhe ligar facilmente os seus registos Imperva WAF Gateway com o Azure Sentinel, para que possa ver os dados nos livros, usá-los para criar alertas personalizados e incorporá-los para melhorar a investigação. A integração entre o Imperva WAF Gateway e o Azure Sentinel faz uso do Syslog formatado pelo CEF, um reencaminhador baseado em Linux e do agente Log Analytics.

> [!NOTE]
> Os dados serão armazenados na localização geográfica do espaço de trabalho em que está a executar o Azure Sentinel.

## <a name="prerequisites"></a>Pré-requisitos

- Deve ter lido e escrito permissões no seu espaço de trabalho Azure Sentinel.

- Deve ter lido permissões para partilhar chaves para o espaço de trabalho. [Saiba mais sobre as teclas do espaço de trabalho.](../azure-monitor/agents/log-analytics-agent.md#workspace-id-and-key)

## <a name="send-imperva-waf-gateway-logs-to-azure-sentinel"></a>Enviar registos do Portal Imperva WAF para Azure Sentinel

Para obter os seus logins no Azure Sentinel, configuure o seu aparelho Imperva WAF Gateway para enviar mensagens Syslog em formato CEF para um servidor de reencaminhamento de log baseado em Linux (executando rsyslog ou syslog-ng). Este servidor terá o agente Log Analytics instalado nele, e o agente encaminha os registos para o seu espaço de trabalho Azure Sentinel.

1. No menu de navegação Azure Sentinel, selecione **Conectores de dados**.

1. Na galeria **de conectores de dados,** selecione **Imperva WAF Gateway (Preview)** e, em seguida, **Abra a página do conector**.

1. Siga as instruções no separador **Instruções,** na **Configuração:**

    1. Menos **de 1. Configuração do agente Linux Syslog** - Faça este passo se ainda não tiver um reencaminhador de log em execução, ou se precisar de outro. Ver [PASSO 1: Desloque o reencaminhador](connect-cef-agent.md) de registo na documentação do Sentinela Azure para obter instruções e explicações mais detalhadas.

    1. Menos **de 2. Forward Common Event Format (CEF) registra para o agente Syslog** - Este conector requer uma interface de **ação** e **conjunto de ação** para ser criado na consola de gestão **Imperva SecureSphere MX.** Siga as instruções da Imperva para [permitir o alerta imperva WAF Gateway para o Azure Sentinel](https://community.imperva.com/blogs/craig-burlingame1/2020/11/13/steps-for-enabling-imperva-waf-gateway-alert). Esta configuração deve incluir os seguintes elementos:
        - Destino de registo - o nome de anfitrião e/ou endereço IP do seu servidor de reencaminhamento de registo
        - Protocolo e porta - TCP 514
        - Formato de registo – CEF
        - Tipos de registos - todos disponíveis

    1. Menos **de 3. Validar a ligação** - Verifique a ingestão de dados copiando o comando na página do conector e executando-o no seu reencaminhador de registo. Ver [PASSO 3: Validar a conectividade](connect-cef-verify.md) na documentação do Sentinela Azure para obter instruções e explicações mais detalhadas.

        Pode demorar até que os seus registos comecem a aparecer no Log Analytics.

## <a name="find-your-data"></a>Encontre os seus dados

Após a criação de uma ligação bem sucedida, os dados aparecem em **Logs,** sob a secção **Azure Sentinel,** na tabela *CommonSecurityLog.*

Para consultar os dados do Imperva WAF Gateway no Log Analytics, copie o seguinte na janela de consulta, aplicando outros filtros à sua escolha:

```kusto
CommonSecurityLog 
| where DeviceVendor == "Imperva Inc." 
| where DeviceProduct == "WAF Gateway" 
| where TimeGenerated == ago(5m)
```

Consulte o separador **passos seguintes** na página do conector para obter amostras de consulta mais úteis.

## <a name="next-steps"></a>Passos seguintes
Neste documento, aprendeu a ligar o Portal Imperva WAF ao Azure Sentinel. Para saber mais sobre Azure Sentinel, consulte os seguintes artigos:

- Saiba como [obter visibilidade nos seus dados e ameaças potenciais.](quickstart-get-visibility.md)
- Começa [a detetar ameaças com o Azure Sentinel.](tutorial-detect-threats-built-in.md)
- [Utilize livros para](tutorial-monitor-your-data.md) monitorizar os seus dados.