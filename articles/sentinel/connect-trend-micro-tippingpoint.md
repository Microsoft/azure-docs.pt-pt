---
title: Ligue o Micro TippingPoint de Tendência ao | Sentinela de Azure Microsoft Docs
description: Saiba como utilizar o conector de dados Trend Micro TippingPoint para puxar os registos SMS do TippingPoint no Azure Sentinel. Ver dados do TippingPoint nos livros de trabalho, criar alertas e melhorar a investigação.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 0001cad6-699c-4ca9-b66c-80c194e439a5
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/12/2021
ms.author: yelevin
ms.openlocfilehash: 5c7491a0e0ba2a3bf604988c613e1fd8937f277d
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/25/2021
ms.locfileid: "98752168"
---
# <a name="connect-your-trend-micro-tippingpoint-solution-to-azure-sentinel"></a>Ligue a sua solução Trend Micro TippingPoint ao Azure Sentinel

> [!IMPORTANT]
> O conector Trend Micro TippingPoint encontra-se atualmente em **PREVIEW**. Consulte os [Termos Complementares de Utilização para o Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para termos legais adicionais aplicáveis às funcionalidades do Azure que estejam em versão beta, pré-visualização ou ainda não lançadas em disponibilidade geral.

Este artigo explica como ligar a solução do Sistema de Proteção de Ameaças de Micro TippingPoint de Tendência ao Azure Sentinel. O conector de dados Trend Micro TippingPoint permite-lhe ligar facilmente os registos do seu Sistema de Gestão de Segurança tippingPoint (SMS) com o Azure Sentinel, para que possa ver os dados nos livros, usá-lo para criar alertas personalizados e incorporá-lo para melhorar a investigação.

> [!NOTE]
> Os dados serão armazenados na localização geográfica do espaço de trabalho em que está a executar o Azure Sentinel.

## <a name="prerequisites"></a>Pré-requisitos

- Deve ter lido e escrito permissões no seu espaço de trabalho Azure Sentinel.

- Deve ter lido permissões para partilhar chaves para o espaço de trabalho.

## <a name="send-trend-micro-tippingpoint-logs-to-azure-sentinel"></a>Envie registos de Micro TippingPoint de tendência para Azure Sentinel

Para obter os seus logins no Azure Sentinel, configuure a sua solução TipPoint TPS para enviar mensagens Syslog em formato CEF para um servidor de reencaminhamento de log baseado em Linux (executando rsyslog ou syslog-ng). Este servidor terá o agente Log Analytics instalado nele, e o agente encaminha os registos para o seu espaço de trabalho Azure Sentinel. O conector utiliza uma função de parser para converter os dados que recebe num esquema normalizado. 

1. No menu de navegação Azure Sentinel, selecione **Conectores de dados**.

1. A partir da galeria **de conectores de dados,** selecione **Trend Micro TippingPoint (Preview)** e, em seguida, Abra a página do **conector**.

1. Siga as instruções no separador **Instruções,** na **Configuração:**

    1. Menos **de 1. Configuração do agente Linux Syslog** - Faça este passo se ainda não tiver um reencaminhador de log em execução, ou se precisar de outro. Ver [PASSO 1: Desloque o reencaminhador](connect-cef-agent.md) de registo na documentação do Sentinela Azure para obter instruções e explicações mais detalhadas.

    1. Menos **de 2. Tendência avançada Micro TippingPoint registos SMS para o agente Syslog** - Esta configuração deve incluir os seguintes elementos:
        - Destino de registo - o nome de anfitrião e/ou endereço IP do seu servidor de reencaminhamento de registo
        - Protocolo e porta – **TCP 514** (se recomendado de outra forma, certifique-se de fazer a alteração paralela no daemon do syslog no seu servidor de reencaminhamento de log)
        - Formato de registo – **Formato CEF ArcSight v4.2**
        - Tipos de registos - todos disponíveis

    1. Menos **de 3. Validar a ligação** - Verifique a ingestão de dados copiando o comando na página do conector e executando-o no seu reencaminhador de registo. Ver [PASSO 3: Validar a conectividade](connect-cef-verify.md) na documentação do Sentinela Azure para obter instruções e explicações mais detalhadas.

        Pode demorar até que os seus registos comecem a aparecer no Log Analytics.

## <a name="find-your-data"></a>Encontre os seus dados

Após a criação de uma ligação bem sucedida, os dados aparecem em **Logs,** sob a secção **Azure Sentinel,** na tabela *CommonSecurityLog.*

Para obter dados do Trend Micro TippingPoint no Log Analytics, irá consultar a função parser em vez da tabela. Copie o seguinte na janela de consulta, aplicando outros filtros à sua escolha:

```kusto
TrendMicroTippingPoint
| sort by TimeGenerated
```

Consulte o separador **passos seguintes** na página do conector para obter mais amostras de consulta.

## <a name="next-steps"></a>Próximos passos

Neste documento, aprendeu a ligar o Trend Micro TippingPoint ao Azure Sentinel. Para saber mais sobre Azure Sentinel, consulte os seguintes artigos:

- Saiba como [obter visibilidade nos seus dados e potenciais ameaças.](quickstart-get-visibility.md)
- Começa [a detetar ameaças com o Azure Sentinel.](tutorial-detect-threats-built-in.md)
- [Utilize livros para](tutorial-monitor-your-data.md) monitorizar os seus dados.
