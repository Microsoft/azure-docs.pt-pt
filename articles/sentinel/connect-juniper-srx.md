---
title: Ligue os dados SRX da Juniper Networks ao Azure Sentinel| Microsoft Docs
description: Saiba como utilizar o conector de dados Juniper SRX para puxar os registos Juniper SRX para o Azure Sentinel. Veja os dados do Juniper SRX nos livros, crie alertas e melhore a investigação.
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
ms.date: 01/17/2021
ms.author: yelevin
ms.openlocfilehash: b10c47a31bf1be10c278d4d9e0dce633bc7bff6c
ms.sourcegitcommit: 7ec45b7325e36debadb960bae4cf33164176bc24
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/16/2021
ms.locfileid: "100530642"
---
# <a name="connect-your-juniper-srx-firewall-to-azure-sentinel"></a>Ligue a sua firewall Juniper SRX ao Azure Sentinel

> [!IMPORTANT]
> O conector Juniper SRX encontra-se atualmente em **PREVIEW**. Consulte os [Termos Complementares de Utilização para o Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para termos legais adicionais aplicáveis às funcionalidades do Azure que estejam em versão beta, pré-visualização ou ainda não lançadas em disponibilidade geral.

Este artigo explica como ligar o seu aparelho de firewall Juniper SRX ao Azure Sentinel. O conector de dados Juniper SRX permite-lhe ligar facilmente os seus registos SRX com o Azure Sentinel, para que possa ver os dados nos livros, usá-lo para criar alertas personalizados e incorporá-lo para melhorar a investigação. A integração entre o Juniper SRX e o Azure Sentinel faz uso do Syslog.

> [!NOTE]
> Os dados serão armazenados na localização geográfica do espaço de trabalho em que está a executar o Azure Sentinel.

## <a name="prerequisites"></a>Pré-requisitos

- Deve ter lido e escrito permissão no espaço de trabalho do Azure Sentinel.

- A sua solução Juniper SRX deve ser configurada para exportar registos via Syslog.

## <a name="forward-juniper-srx-logs-to-the-syslog-agent"></a>Diário de Juniper SRX para o agente Syslog  

Configure o Juniper SRX para encaminhar mensagens Syslog para o seu espaço de trabalho Azure Sentinel através do agente Syslog.

1. No menu de navegação Azure Sentinel, selecione **Conectores de dados**.

1. A partir da galeria **de conectores de dados,** selecione o conector **Juniper SRX (Preview)** e, em seguida, **Abra a página do conector**.

1. Siga as instruções na página do conector **Juniper SRX:**

    1. Instale e embarque no agente para Linux

        - Escolha um Azure Linux VM ou uma máquina linux não-Azure (física ou virtual).

    1. Configure os registos a recolher

        - Selecione as instalações e as severidades na configuração dos agentes do espaço de trabalho.

    1. Configure e ligue o Zimbro SRX

        - Siga estas instruções para configurar o Juniper SRX para encaminhar o syslog.
            - [Registos de tráfego (registos de política de segurança)](https://kb.juniper.net/InfoCenter/index?page=content&id=KB16509&actp=METADATA)
            - [Registos do sistema](https://kb.juniper.net/InfoCenter/index?page=content&id=kb16502)
        - Para o servidor remoto, utilize o endereço IP da máquina Linux em que instalou o agente Linux.

## <a name="find-your-data"></a>Encontre os seus dados

Após a criação de uma ligação bem sucedida, os dados aparecem no Log Analytics no âmbito do Syslog.

Consulte o separador **passos seguintes** na página do conector para obter algumas consultas úteis de amostras.

## <a name="validate-connectivity"></a>Validar conectividade

Pode demorar até que os seus registos comecem a aparecer no Log Analytics.

## <a name="next-steps"></a>Passos seguintes

Neste documento, aprendeu a ligar o Juniper SRX ao Azure Sentinel. Para saber mais sobre Azure Sentinel, consulte os seguintes artigos:

- Saiba como [obter visibilidade nos seus dados e potenciais ameaças.](quickstart-get-visibility.md)
- Começa [a detetar ameaças com o Azure Sentinel.](tutorial-detect-threats-built-in.md)
- [Utilize livros para](tutorial-monitor-your-data.md) monitorizar os seus dados.
