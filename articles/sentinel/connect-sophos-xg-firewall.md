---
title: Ligue os dados da Sophos XG Firewall ao Azure Sentinel. Microsoft Docs
description: Saiba como ligar os dados da Sophos XG Firewall ao Azure Sentinel.
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
ms.date: 07/17/2020
ms.author: yelevin
ms.openlocfilehash: b48773272e050b47af73b197d6f1c8156318fd71
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87099341"
---
# <a name="connect-your-sophos-xg-firewall-to-azure-sentinel"></a>Ligue a sua Firewall Sophos XG ao Azure Sentinel

> [!IMPORTANT]
> O conector de dados Sophos XG Firewall em Azure Sentinel está atualmente em pré-visualização pública.
> Esta funcionalidade é fornecida sem um contrato de nível de serviço, e não é recomendado para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, consulte [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Este artigo explica como ligar o seu aparelho [Sophos XG Firewall](https://www.sophos.com/products/next-gen-firewall.aspx) ao Azure Sentinel. O conector de dados Sophos XG Firewall permite-lhe ligar facilmente os seus registos Sophos XG Firewall com Azure Sentinel, para ver dashboards, criar alertas personalizados e melhorar a investigação. A integração entre Sophos XG Firewall e Azure Sentinel faz uso do Syslog.

> [!NOTE]
> Os dados serão armazenados na localização geográfica do espaço de trabalho em que está a executar o Azure Sentinel.

## <a name="forward-sophos-xg-firewall-logs-to-the-syslog-agent"></a>Avanço Sophos XG Firewall regista para o agente Syslog  

Configure a Sophos XG Firewall para encaminhar mensagens Syslog para o seu espaço de trabalho Azure através do agente Syslog.

1. No portal Azure Sentinel, clique nos **conectores de dados** e selecione o conector **Sophos XG Firewall.**

1. Selecione **Abrir a página do conector**.

1. Siga as instruções na página **Sophos XG Firewall.**

## <a name="find-your-data"></a>Encontre os seus dados

Após a criação de uma ligação bem sucedida, os dados aparecem no Log Analytics no âmbito do Syslog.

## <a name="validate-connectivity"></a>Validar conectividade

Pode demorar mais de 20 minutos até que os seus registos comecem a aparecer no Log Analytics.

## <a name="next-steps"></a>Passos seguintes

Neste documento, aprendeu a ligar a Sophos XG Firewall ao Azure Sentinel. Para saber mais sobre Azure Sentinel, consulte os seguintes artigos:

- Saiba como [obter visibilidade nos seus dados e potenciais ameaças.](quickstart-get-visibility.md)
- Começa [a detetar ameaças com o Azure Sentinel.](tutorial-detect-threats-built-in.md)
- [Utilize livros para](tutorial-monitor-your-data.md) monitorizar os seus dados.