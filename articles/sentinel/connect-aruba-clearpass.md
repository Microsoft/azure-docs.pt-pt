---
title: Ligue os dados do Aruba ClearPass ao | do Azure Sentinel Microsoft Docs
description: Aprenda a usar o conector Aruba ClearPass para puxar os registos Aruba ClearPass para o Azure Sentinel. Veja os dados do Aruba ClearPass nos livros de trabalho, crie alertas e melhore a investigação.
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
ms.date: 02/28/2021
ms.author: yelevin
ms.openlocfilehash: 8050b4f173476d7af66cb858ff5f785e5a12af43
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2021
ms.locfileid: "105046577"
---
# <a name="connect-your-aruba-clearpass-to-azure-sentinel"></a>Ligue o seu Aruba ClearPass ao Azure Sentinel

> [!IMPORTANT]
> O conector Aruba ClearPass encontra-se atualmente em **PREVIEW**. Consulte os [Termos Complementares de Utilização para o Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para termos legais adicionais aplicáveis às funcionalidades do Azure que estejam em versão beta, pré-visualização ou ainda não lançadas em disponibilidade geral.

Este artigo explica como ligar o seu aparelho Aruba ClearPass ao Azure Sentinel. O conector de dados Aruba ClearPass permite-lhe ligar facilmente os seus registos Aruba ClearPass com o Azure Sentinel, para que possa ver os dados em livros, consultar-nos para criar alertas personalizados e incorporá-lo para melhorar a investigação. A integração entre Aruba ClearPass e Azure Sentinel faz uso do Syslog formatado pelo CEF, um reencaminhador baseado em Linux e do agente Log Analytics. Também usa um parser de log personalizado baseado numa função Kusto.

> [!NOTE]
> Os dados serão armazenados na localização geográfica do espaço de trabalho em que está a executar o Azure Sentinel.

## <a name="prerequisites"></a>Pré-requisitos

- Deve ter lido e escrito permissões no seu espaço de trabalho Azure Sentinel.

- Deve ter lido permissões para partilhar chaves para o espaço de trabalho. [Saiba mais sobre as teclas do espaço de trabalho.](../azure-monitor/agents/log-analytics-agent.md#workspace-id-and-key)

## <a name="send-aruba-clearpass-logs-to-azure-sentinel"></a>Enviar registos Aruba ClearPass para Azure Sentinel

Para obter os seus logins no Azure Sentinel, configura o seu aparelho Aruba ClearPass para enviar mensagens Syslog em formato CEF para um servidor de reencaminhamento de log baseado em Linux (executando rsyslog ou syslog-ng). Este servidor terá o agente Log Analytics instalado nele, e o agente encaminha os registos para o seu espaço de trabalho Azure Sentinel.

1. No menu de navegação Azure Sentinel, selecione **Conectores de dados**.

1. Na galeria **de conectores de dados,** selecione **Aruba ClearPass (Preview)** e, em seguida, **Abra a página do conector**.

1. Siga as instruções no separador **Instruções,** na **Configuração:**

    1. Menos **de 1. Configuração do agente Linux Syslog** - Faça este passo se ainda não tiver um reencaminhador de log em execução, ou se precisar de outro. Ver [PASSO 1: Desloque o reencaminhador](connect-cef-agent.md) de registo na documentação do Sentinela Azure para obter instruções e explicações mais detalhadas.

    1. Menos **de 2. Encaminhar os registos do Aruba ClearPass para um agente Syslog** - Siga as instruções de Aruba para [configurar](https://www.arubanetworks.com/techdocs/ClearPass/6.7/PolicyManager/Content/CPPM_UserGuide/Admin/syslogExportFilters_add_syslog_filter_general.htm)o ClearPass . Esta configuração deve incluir os seguintes elementos:
        - Destino de registo - o nome de anfitrião e/ou endereço IP do seu servidor de reencaminhamento de registo
        - Protocolo e porta – TCP 514 (se recomendado de outra forma, certifique-se de fazer a alteração paralela no daemon do syslog no seu servidor de reencaminhamento de log)
        - Formato de registo – CEF
        - Tipos de registos - todos disponíveis ou todos apropriados

    1. Menos **de 3. Validar a ligação** - Verifique a ingestão de dados copiando o comando na página do conector e executando-o no seu reencaminhador de registo. Ver [PASSO 3: Validar a conectividade](connect-cef-verify.md) na documentação do Sentinela Azure para obter instruções e explicações mais detalhadas.

        Pode demorar até que os seus registos comecem a aparecer no Log Analytics.

## <a name="find-your-data"></a>Encontre os seus dados

Após a criação de uma ligação bem sucedida, os dados aparecem em **Logs,** sob a secção **Azure Sentinel,** na tabela *CommonSecurityLog.*

Este conector de dados depende de um parser baseado numa Função Kusto para funcionar como esperado. [Siga estes passos](https://aka.ms/sentinel-arubaclearpass-parser) para criar o pseudónimo de função **ArubaClearPass** Kusto.

Em seguida, para consultar os dados do Aruba ClearPass no Log Analytics, introduza `ArubaClearPass` na parte superior da janela de consulta.

Consulte o separador **passos seguintes** na página do conector para obter algumas amostras de consulta úteis.

## <a name="next-steps"></a>Passos seguintes

Neste documento, aprendeu a ligar Aruba ClearPass ao Azure Sentinel. Para saber mais sobre Azure Sentinel, consulte os seguintes artigos:

- Saiba como [obter visibilidade nos seus dados e ameaças potenciais.](quickstart-get-visibility.md)
- Começa [a detetar ameaças com o Azure Sentinel.](tutorial-detect-threats-built-in.md)
- [Utilize livros para](tutorial-monitor-your-data.md) monitorizar os seus dados.