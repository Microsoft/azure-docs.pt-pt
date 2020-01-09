---
title: Conectar o firewall CloudGen do Barracuda ao Azure Sentinel | Microsoft Docs
description: Saiba como conectar o firewall CloudGen do Barracuda ao Azure Sentinel.
services: sentinel
documentationcenter: na
author: cabailey
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: cabailey
ms.openlocfilehash: f7d5531c8aba3e2e7abb56d82342bfda79dbc71b
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/02/2020
ms.locfileid: "75610731"
---
# <a name="connect-barracuda-cloudgen-firewall"></a>Ligar a Barracuda CloudGen Firewall

O conector CGFW (Barracuda CloudGen firewall) permite que você conecte facilmente seus logs de CGFW do Barracuda com o Azure Sentinel, exiba painéis, crie alertas personalizados e melhore a investigação. Isso lhe dá mais informações sobre a rede da sua organização e aprimora seus recursos de operação de segurança.




## <a name="prerequisites"></a>Pré-requisitos

- Permissões de leitura e gravação para o espaço de trabalho do Azure Sentinel.

- O firewall CloudGen Barracuda deve ser configurado para exportar logs via syslog.

## <a name="connect-azure-sentinel-to-barracuda-cloudgen-firewall"></a>Conectar o Azure Sentinel ao firewall CloudGen Barracuda

1. Na portal do Azure, navegue até **Azure Sentinel** > **conectores de dados** e selecione o conector de **Firewall do Barracuda CloudGen** .

2. Selecione a **página abrir conector**.

3. Siga as instruções na página do **Firewall do Barracuda CloudGen** .


## <a name="next-steps"></a>Passos seguintes
Neste documento, você aprendeu a conectar o firewall CloudGen do Barracuda ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade de seus dados e ameaças potenciais](quickstart-get-visibility.md).
- Comece [a detectar ameaças com o Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Use pastas de trabalho](tutorial-monitor-your-data.md) para monitorar seus dados.


