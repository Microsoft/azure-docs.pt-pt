---
title: Ligue os dados do Sistema de Computação Unificada da Cisco (UCS) ao Azure Sentinel| Microsoft Docs
description: Saiba como utilizar o conector de dados cisco UCS para puxar os registos da Cisco UCS para o Azure Sentinel. Veja os dados da Cisco UCS nos livros, crie alertas e melhore a investigação.
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
ms.openlocfilehash: 15e31b8dc5ac6db5861e3ea0fb5390ddf0a8c945
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100530676"
---
# <a name="connect-your-cisco-unified-computing-system-ucs-to-azure-sentinel"></a>Ligue o seu Sistema de Computação Unificada Cisco (UCS) ao Azure Sentinel

> [!IMPORTANT]
> O conector Cisco UCS encontra-se atualmente em **PREVIEW**. Consulte os [Termos Complementares de Utilização para o Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para termos legais adicionais aplicáveis às funcionalidades do Azure que estejam em versão beta, pré-visualização ou ainda não lançadas em disponibilidade geral.

Este artigo explica como ligar o seu aparelho Cisco Uniified Computing System (UCS) ao Azure Sentinel. O conector de dados cisco UCS permite-lhe conectar facilmente os seus registos UCS com o Azure Sentinel, para que possa ver os dados nos livros, usá-lo para criar alertas personalizados e incorporá-lo para melhorar a investigação. A integração entre a Cisco UCS e a Azure Sentinel faz uso do Syslog.

> [!NOTE]
> Os dados serão armazenados na localização geográfica do espaço de trabalho em que está a executar o Azure Sentinel.

## <a name="prerequisites"></a>Pré-requisitos

- Deve ter lido e escrito permissão no espaço de trabalho do Azure Sentinel.

- A sua solução Cisco UCS deve ser configurada para exportar registos via Syslog.

## <a name="forward-cisco-ucs-logs-to-the-syslog-agent"></a>Avançados Cisco UCS registam o agente Syslog  

Configure a Cisco UCS para encaminhar mensagens Syslog para o seu espaço de trabalho Azure Sentinel através do agente Syslog.

1. No menu de navegação Azure Sentinel, selecione **Conectores de dados**.

1. A partir da galeria **de conectores de dados,** selecione o conector **Cisco UCS (Preview)** e, em seguida, **Abra a página do conector**.

1. Siga as instruções na página do conector **Cisco UCS:**

    1. Instale e embarque no agente para Linux

        - Escolha um Azure Linux VM ou uma máquina linux não-Azure (física ou virtual).

    1. Configure os registos a recolher

        - Selecione as instalações e as severidades na configuração dos agentes do espaço de trabalho.

    1. Configure e ligue o Cisco UCS

        - Siga [estas instruções](https://www.cisco.com/c/en/us/support/docs/servers-unified-computing/ucs-manager/110265-setup-syslog-for-ucs.html#configsremotesyslog) para configurar o Cisco UCS para encaminhar o syslog. Para o servidor remoto, utilize o endereço IP da máquina Linux em que instalou o agente Linux.

## <a name="find-your-data"></a>Encontre os seus dados

Após a criação de uma ligação bem sucedida, os dados aparecem no Log Analytics no âmbito do Syslog.

Consulte o separador **passos seguintes** na página do conector para obter algumas consultas úteis de amostras.

## <a name="validate-connectivity"></a>Validar conectividade

Pode demorar até que os seus registos comecem a aparecer no Log Analytics.

## <a name="next-steps"></a>Passos seguintes

Neste documento, aprendeu a ligar a Cisco UCS ao Azure Sentinel. Para saber mais sobre Azure Sentinel, consulte os seguintes artigos:

- Saiba como [obter visibilidade nos seus dados e potenciais ameaças.](quickstart-get-visibility.md)
- Começa [a detetar ameaças com o Azure Sentinel.](tutorial-detect-threats-built-in.md)
- [Utilize livros para](tutorial-monitor-your-data.md) monitorizar os seus dados.
