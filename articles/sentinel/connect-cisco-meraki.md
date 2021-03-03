---
title: Ligue os dados da Cisco Meraki à | do Azure Sentinel Microsoft Docs
description: Aprenda a usar o conector Cisco Meraki para puxar os troncos da Cisco Meraki para o Azure Sentinel. Veja os dados da Cisco Meraki nos livros, crie alertas e melhore a investigação.
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
ms.openlocfilehash: dc2d2a0724f18a02a5b667eb1004963a326ec360
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101747088"
---
# <a name="connect-your-cisco-meraki-to-azure-sentinel"></a>Ligue o seu Cisco Meraki ao Azure Sentinel

> [!IMPORTANT]
> O conector Cisco Meraki está atualmente em **PREVIEW**. Consulte os [Termos Complementares de Utilização para o Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para termos legais adicionais aplicáveis às funcionalidades do Azure que estejam em versão beta, pré-visualização ou ainda não lançadas em disponibilidade geral.

Este artigo explica como ligar o seu aparelho Cisco Meraki (MX/MS/MR) ao Azure Sentinel. O conector de dados Cisco Meraki permite-lhe conectar facilmente os seus registos Cisco Meraki com a Azure Sentinel, para ver dashboards, criar alertas personalizados e melhorar a investigação. A integração entre Cisco Meraki e Azure Sentinel faz uso de um servidor Syslog com o agente Log Analytics instalado. Também usa um parser de log personalizado baseado numa função Kusto.

> [!NOTE]
> Os dados serão armazenados na localização geográfica do espaço de trabalho em que está a executar o Azure Sentinel.

## <a name="prerequisites"></a>Pré-requisitos

- Deve ter lido e escrito permissão no espaço de trabalho do Azure Sentinel.

- A sua solução Cisco Meraki deve ser configurada para exportar registos via Syslog.

## <a name="send-cisco-meraki-logs-to-azure-sentinel-via-the-syslog-agent"></a>Envie registos da Cisco Meraki para Azure Sentinel através do agente Syslog  

Configure a Cisco Meraki para encaminhar mensagens Syslog para o seu espaço de trabalho Azure Sentinel através do agente Syslog.

1. No menu de navegação Azure Sentinel, selecione **Conectores de dados**.

1. Na galeria **de conectores de dados,** selecione o conector **Cisco Meraki (Preview)** e, em seguida, **Abra a página do conector**.

1. Siga as instruções na página do conector **Cisco Meraki:**

    1. Instale e embarque no agente para Linux

        - Escolha um Azure Linux VM ou uma máquina linux não-Azure (física ou virtual).

    1. Configure os registos a recolher

        - Selecione as instalações e as severidades na **configuração** dos agentes do espaço de trabalho .

    1. Configure e ligue o ou os dispositivos Cisco Meraki

        - Siga [estas instruções](https://documentation.meraki.com/General_Administration/Monitoring_and_Reporting/Meraki_Device_Reporting_-_Syslog%2C_SNMP_and_API) para configurar o dispositivo Cisco Meraki para encaminhar o syslog. Para o servidor remoto, utilize o endereço IP da máquina Linux em que instalou o agente Linux.

## <a name="validate-connectivity-and-find-your-data"></a>Validar a conectividade e encontrar os seus dados

Pode demorar até que os seus registos comecem a aparecer no Azure Sentinel. 

Após a criação de uma ligação bem sucedida, os dados aparecem em **Logs**, sob a secção *Gestão* de Registos, na tabela *Syslog.*

Este conector de dados depende de um parser baseado numa Função Kusto para funcionar como esperado. [Siga estes passos](https://aka.ms/sentinel-ciscomeraki-parser) para criar o pseudónimo da função **CiscoMeraki** Kusto. Em seguida, pode digitar `CiscoMeraki` a janela de consulta para consultar os dados.

Consulte o separador **passos seguintes** na página do conector para obter algumas amostras de consulta úteis.

## <a name="next-steps"></a>Passos seguintes

Neste documento, aprendeu a ligar Cisco Meraki a Azure Sentinel. Para saber mais sobre Azure Sentinel, consulte os seguintes artigos:

- Saiba como [obter visibilidade nos seus dados e ameaças potenciais.](quickstart-get-visibility.md)
- Começa [a detetar ameaças com o Azure Sentinel.](tutorial-detect-threats-built-in.md)
- [Utilize livros para](tutorial-monitor-your-data.md) monitorizar os seus dados.
