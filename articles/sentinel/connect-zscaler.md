---
title: Conectar dados do Zscaler ao Azure Sentinel | Microsoft Docs
description: Saiba como conectar dados do Zscaler ao Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: rkarlin
ms.openlocfilehash: d64ec43c255813b99d7ceefc1e526884c99f1873
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/02/2020
ms.locfileid: "75610391"
---
# <a name="connect-zscaler-internet-access-to-azure-sentinel"></a>Conectar o acesso à Internet do Zscaler ao Azure Sentinel

> [!IMPORTANT]
> O conector de dados do Zscaler no Azure Sentinel está atualmente em visualização pública.
> Esse recurso é fornecido sem um contrato de nível de serviço e não é recomendado para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Este artigo explica como conectar seu dispositivo de acesso à Internet do Zscaler ao Azure Sentinel. O conector de dados do Zscaler permite que você conecte facilmente seus logs do ZIA (Zscaler Internet Access) com o Azure Sentinel, exiba painéis, crie alertas personalizados e melhore a investigação. Usar o Zscaler no Azure Sentinel fornecerá mais informações sobre o uso da Internet da sua organização e aprimorará seus recursos de operação de segurança. 


## <a name="configure-your-zscaler-to-send-cef-messages"></a>Configurar seu Zscaler para enviar mensagens CEF

1. No dispositivo Zscaler, você precisa definir esses valores para que o dispositivo envie os logs necessários no formato necessário para o agente de syslog do Azure Sentinel, com base no agente de Log Analytics. Você pode modificar esses parâmetros em seu dispositivo, contanto que também os modifique no daemon do syslog no agente do Azure Sentinel.
    - Protocolo = TCP
    - Porta = 514
    - Formato = CEF
    - Endereço IP-certifique-se de enviar as mensagens CEF para o endereço IP da máquina virtual que você dedicaram para essa finalidade.
 Para obter mais informações, consulte o [Guia de implantação do Zscaler e do Azure Sentinel](https://aka.ms/ZscalerCEFInstructions).
 
   > [!NOTE]
   > Esta solução oferece suporte a RFC 3164 ou a RFC 5424 do syslog.


1. Para usar o esquema relevante em Log Analytics para os eventos CEF, procure `CommonSecurityLog`.
1. Continue na [etapa 3: validar a conectividade](connect-cef-verify.md).


## <a name="next-steps"></a>Passos seguintes
Neste documento, você aprendeu a conectar o acesso à Internet do Zscaler ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade de seus dados e ameaças potenciais](quickstart-get-visibility.md).
- Comece [a detectar ameaças com o Azure Sentinel](tutorial-detect-threats.md).
- [Use pastas de trabalho](tutorial-monitor-your-data.md) para monitorar seus dados.


