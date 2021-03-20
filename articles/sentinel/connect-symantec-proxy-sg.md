---
title: Ligue os dados da Symantec ProxySG ao Azure Sentinel| Microsoft Docs
description: Saiba como ligar os dados da Symantec ProxySG ao Azure Sentinel.
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
ms.openlocfilehash: 902475ae3e60761fb30620c5ba2fa8cbd905c916
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100099188"
---
# <a name="connect-your-symantec-proxysg-to-azure-sentinel"></a>Ligue o seu Symantec ProxySG ao Azure Sentinel

> [!IMPORTANT]
> O conector de dados Symantec ProxySG em Azure Sentinel está atualmente em pré-visualização pública.
> Esta funcionalidade é fornecida sem um contrato de nível de serviço, e não é recomendado para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Este artigo explica como ligar o seu aparelho [Symantec ProxySG](https://www.broadcom.com/products/cyber-security/network/gateway/proxy-sg-and-advanced-secure-gateway) ao Azure Sentinel. O conector de dados Symantec ProxySG permite-lhe ligar facilmente os seus registos Symantec ProxySG com o Azure Sentinel, para ver dashboards, criar alertas personalizados e melhorar a investigação. A integração entre a Symantec ProxySG e a Azure Sentinel faz uso do Syslog.

> [!NOTE]
> Os dados serão armazenados na localização geográfica do espaço de trabalho em que está a executar o Azure Sentinel.

## <a name="forward-symantec-proxysg-logs-to-the-syslog-agent"></a>Registos Symantec ProxySG avançados para o agente Syslog  

Configure a Symantec ProxySG para encaminhar mensagens Syslog para o seu espaço de trabalho Azure através do agente Syslog.

1. No portal Azure Sentinel, clique nos **conectores de dados** e selecione o conector **Symantec ProxySG.**

1. Selecione **Abrir a página do conector**.

1. Siga as instruções na página **Symantec ProxySG.**

## <a name="find-your-data"></a>Encontre os seus dados

Após a criação de uma ligação bem sucedida, os dados aparecem no Log Analytics no âmbito do Syslog.

## <a name="validate-connectivity"></a>Validar conectividade

Pode demorar até que os seus registos comecem a aparecer no Log Analytics. 

## <a name="next-steps"></a>Passos seguintes

Neste documento, aprendeu a ligar a Symantec ProxySG ao Azure Sentinel. Para saber mais sobre Azure Sentinel, consulte os seguintes artigos:

- Saiba como [obter visibilidade nos seus dados e potenciais ameaças.](quickstart-get-visibility.md)
- Começa [a detetar ameaças com o Azure Sentinel.](tutorial-detect-threats-built-in.md)
- [Utilize livros para](tutorial-monitor-your-data.md) monitorizar os seus dados.