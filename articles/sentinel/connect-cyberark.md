---
title: Ligue os dados do CYBERArk EPV ao Azure Sentinel| Microsoft Docs
description: Saiba como utilizar o conector de dados CyberArk Enterprise Password Vault (EPV) para puxar os seus registos para o Azure Sentinel. Veja os dados do CYBERArk EPV nos livros, crie alertas e melhore a investigação.
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
ms.date: 10/25/2020
ms.author: yelevin
ms.openlocfilehash: aa57963ce369e4c8f84f4aae5f99fe343181ff6b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100530506"
---
# <a name="connect-cyberark-enterprise-password-vault-epv-to-azure-sentinel"></a>Ligue o Cofre de Passwords da Empresa CyberArk (EPV) a Azure Sentinel

> [!IMPORTANT]
> O conector de dados CyberArk Enterprise Password Vault (EPV) em Azure Sentinel está atualmente em pré-visualização pública. Esta funcionalidade é fornecida sem um contrato de nível de serviço. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

O conector CyberArk Syslog permite-lhe ligar facilmente todos os seus registos de solução de segurança CyberArk com o seu Azure Sentinel, para ver dashboards, criar alertas personalizados e melhorar a investigação. A integração entre CyberArk e Azure Sentinel faz uso do Conector de Dados CEF para analisar e exibir corretamente mensagens CyberArk Syslog.

> [!NOTE]
> Os dados serão armazenados na localização geográfica do espaço de trabalho em que está a executar o Azure Sentinel.

## <a name="configure-and-connect-cyberark-epv"></a>Configure e ligue o CYBERArk EPV

Os registos DE EPV cyberArk são enviados do Cofre para um servidor de reencaminhamento de log baseado em Linux (executando rsyslog ou syslog-ng) com o agente Log Analytics instalado nele, que exporta os registos para Azure Sentinel. Se não tiver um servidor de reencaminhamento de registos, consulte [estas instruções](connect-cef-agent.md) para pôr uma a funcionar.

1. No portal Azure Sentinel, clique nos **conectores de dados,** selecione **CyberArk Enterprise Password Vault (EPV) Events (EPV) e,** em seguida, **Abra a página do conector**.

1. Siga as [instruções do CyberArk EPV](https://docs.cyberark.com/Product-Doc/OnlineHelp/PAS/Latest/en/Content/PASIMP/DV-Integrating-with-SIEM-Applications.htm) para configurar o envio de dados de syslog para o servidor de reencaminhamento de registos.

1. Valide a sua ligação e verifique a ingestão de dados utilizando [estas instruções](connect-cef-verify.md). Pode demorar até que os seus registos comecem a aparecer no Log Analytics.

## <a name="find-your-data"></a>Encontre os seus dados

Após a criação de uma ligação bem sucedida, os dados aparecem em **Logs,** sob a secção **Azure Sentinel,** na tabela *CommonSecurityLog.*

Para consultar os registos DOPV CyberArk no Log Analytics, introduza `CommonSecurityLog` na parte superior da janela de consulta.

## <a name="next-steps"></a>Passos seguintes

Neste documento, aprendeu a ligar os registos do Cofre de Passwords da CyberArk Enterprise ao Azure Sentinel. Para saber mais sobre Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade nos seus dados e potenciais ameaças.](quickstart-get-visibility.md)
- Começa [a detetar ameaças com o Azure Sentinel.](tutorial-detect-threats-built-in.md)
- [Utilize livros para](tutorial-monitor-your-data.md) monitorizar os seus dados.
