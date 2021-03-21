---
title: Ligue os dados da Broadcom Symantec Data Loss Prevention (DLP) ao Azure Sentinel | Microsoft Docs
description: Aprenda a usar o conector de dados DLP da Broadcom Symantec para puxar os registos DLP da Symantec para o Azure Sentinel. Veja os dados do DLP da Symantec nos livros, crie alertas e melhore a investigação.
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
ms.date: 03/02/2021
ms.author: yelevin
ms.openlocfilehash: 7f89780f2ed440898f5a28d78ec541c48a958b90
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101700951"
---
# <a name="connect-your-broadcom-symantec-data-loss-prevention-dlp-to-azure-sentinel"></a>Ligue a sua Broadcom Symantec Data Loss Prevention (DLP) a Azure Sentinel

> [!IMPORTANT]
> O conector DLP da Broadcom Symantec está atualmente em **PREVIEW**. Consulte os [Termos Complementares de Utilização para o Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para termos legais adicionais aplicáveis às funcionalidades do Azure que estejam em versão beta, pré-visualização ou ainda não lançadas em disponibilidade geral.

Este artigo explica como ligar o seu aparelho Broadcom Symantec DLP ao Azure Sentinel. O conector de dados DLP da Broadcom Symantec permite-lhe ligar facilmente os seus registos Symantec DLP com o Azure Sentinel, para ver dashboards, criar alertas personalizados e melhorar a investigação. Esta capacidade dá-lhe mais informações sobre as informações da sua organização, para onde viaja e melhora as suas capacidades de operação de segurança. A integração entre a Broadcom Symantec DLP e a Azure Sentinel faz uso do Syslog formatado pelo CEF, um reencaminhador baseado em Linux e do agente Log Analytics. Também usa um parser de log personalizado baseado numa função Kusto.

> [!NOTE]
> Os dados serão armazenados na localização geográfica do espaço de trabalho em que está a executar o Azure Sentinel.

## <a name="prerequisites"></a>Pré-requisitos

- Deve ter lido e escrito permissões no seu espaço de trabalho Azure Sentinel.

- Deve ter lido permissões para partilhar chaves para o espaço de trabalho. [Saiba mais sobre as teclas do espaço de trabalho.](../azure-monitor/platform/log-analytics-agent.md#workspace-id-and-key)

## <a name="send-broadcom-symantec-dlp-logs-to-azure-sentinel"></a>Envie registos DLP da Broadcom Symantec para Azure Sentinel

Para obter os seus logins no Azure Sentinel, configuure o seu aparelho Broadcom Symantec DLP para enviar mensagens Syslog em formato CEF para um servidor de reencaminhamento de log baseado em Linux (executando rsyslog ou syslog-ng). Este servidor terá o agente Log Analytics instalado nele, e o agente encaminha os registos para o seu espaço de trabalho Azure Sentinel.

1. No menu de navegação Azure Sentinel, selecione **Conectores de dados**.

1. A partir da galeria **de conectores de dados,** selecione o conector **DLP (Pré-visualização) da Broadcom Symantec** e, em seguida, **Abra a página do conector**.

1. Siga as instruções no separador **Instruções,** na **Configuração:**

    1. Menos **de 1. Configuração do agente Linux Syslog** - Faça este passo se ainda não tiver um reencaminhador de log em execução, ou se precisar de outro. Ver [PASSO 1: Desloque o reencaminhador](connect-cef-agent.md) de registo na documentação do Sentinela Azure para obter instruções e explicações mais detalhadas.

    1. Menos **de 2. Forward Symantec DLP registra a um agente Syslog** - Siga as instruções da Broadcom para [configurar o DLP Symantec](https://help.symantec.com/cs/DLP15.7/DLP/v27591174_v133697641/Configuring-the-Log-to-a-Syslog-Server-action?locale=EN_US). Esta configuração deve incluir os seguintes elementos:
        - Destino de registo - o nome de anfitrião e/ou endereço IP do seu servidor de reencaminhamento de registo
        - Protocolo e porta – TCP 514 (se recomendado de outra forma, certifique-se de fazer a alteração paralela no daemon do syslog no seu servidor de reencaminhamento de log)
        - Formato de registo – CEF
        - Tipos de registos - todos disponíveis ou todos apropriados

    1. Menos **de 3. Validar a ligação** - Verifique a ingestão de dados copiando o comando na página do conector e executando-o no seu reencaminhador de registo. Ver [PASSO 3: Validar a conectividade](connect-cef-verify.md) na documentação do Sentinela Azure para obter instruções e explicações mais detalhadas.

        Pode demorar até que os seus registos comecem a aparecer no Log Analytics.

## <a name="find-your-data"></a>Encontre os seus dados

Após a criação de uma ligação bem sucedida, os dados aparecem em **Logs,** sob a secção **Azure Sentinel,** na tabela *CommonSecurityLog.*

Este conector de dados depende de um parser baseado numa Função Kusto para funcionar como esperado. [Siga estes passos](https://aka.ms/sentinel-symantecdlp-parser) para criar o pseudónimo da função **SymantecDLP** Kusto.

Em seguida, para consultar os dados da Broadcom Symantec DLP no Log Analytics, `SymantecDLP` insira no topo da janela de consulta.

Consulte o separador **passos seguintes** na página do conector para obter algumas amostras de consulta úteis.

## <a name="next-steps"></a>Passos seguintes

Neste documento, aprendeu a ligar a Symantec DLP ao Azure Sentinel. Para saber mais sobre Azure Sentinel, consulte os seguintes artigos:

- Saiba como [obter visibilidade nos seus dados e ameaças potenciais.](quickstart-get-visibility.md)
- Começa [a detetar ameaças com o Azure Sentinel.](tutorial-detect-threats-built-in.md)
- [Utilize livros para](tutorial-monitor-your-data.md) monitorizar os seus dados.
