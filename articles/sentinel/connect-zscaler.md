---
title: Ligue os dados da Zscaler ao Azure Sentinel. Microsoft Docs
description: Saiba como ligar os dados do Zscaler ao Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: cc784afe5db64ccc4aad13fae7a2fa748e4befa3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "77587996"
---
# <a name="connect-zscaler-internet-access-to-azure-sentinel"></a>Ligue o acesso à Internet Zscaler ao Azure Sentinel

> [!IMPORTANT]
> O conector de dados Zscaler em Azure Sentinel está atualmente em pré-visualização pública.
> Esta funcionalidade é fornecida sem um contrato de nível de serviço, e não é recomendado para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, consulte [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Este artigo explica como ligar o seu aparelho Zscaler Internet Access ao Azure Sentinel. O conector de dados Zscaler permite-lhe ligar facilmente os seus registos Zscaler Internet Access (ZIA) com o Azure Sentinel, para visualizar dashboards, criar alertas personalizados e melhorar a investigação. A utilização do Zscaler no Azure Sentinel irá fornecer-lhe mais informações sobre o uso da Internet da sua organização e aumentará as suas capacidades de operação de segurança. 


## <a name="configure-your-zscaler-to-send-cef-messages"></a>Configure o seu Zscaler para enviar mensagens CEF

1. No aparelho Zscaler é necessário definir estes valores de modo a que o aparelho envie os registos necessários no formato necessário ao agente Syslog Azure Sentinel, com base no agente Log Analytics. Pode modificar estes parâmetros no seu aparelho, desde que os modifique também no daemon Syslog no agente Azure Sentinel.
    - Protocolo = TCP
    - Porta = 514
    - Formato = CEF
    - Endereço IP - certifique-se de enviar as mensagens CEF para o endereço IP da máquina virtual que dedicou para este fim.
 Para mais informações, consulte o Guia de [Implantação do Zscaler e do Azure Sentinel.](https://aka.ms/ZscalerCEFInstructions)
 
   > [!NOTE]
   > Esta solução suporta o Syslog RFC 3164 ou o RFC 5424.


1. Para utilizar o esquema relevante no Log Analytics para os eventos CEF, procure `CommonSecurityLog` .
1. Continuar a [PASSO 3: Validar a conectividade](connect-cef-verify.md).


## <a name="next-steps"></a>Passos seguintes
Neste documento, aprendeu a ligar o Zscaler Internet Access ao Azure Sentinel. Para saber mais sobre Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade nos seus dados e potenciais ameaças.](quickstart-get-visibility.md)
- Começa [a detetar ameaças com o Azure Sentinel.](tutorial-detect-threats.md)
- [Utilize livros para](tutorial-monitor-your-data.md) monitorizar os seus dados.


