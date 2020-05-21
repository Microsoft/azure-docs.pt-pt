---
title: Ligue os dados da F5 ASM ao Azure Sentinel. Microsoft Docs
description: Saiba como ligar dados f5 ASM ao Azure Sentinel.
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
ms.date: 05/20/2020
ms.author: yelevin
ms.openlocfilehash: bee8ec9b6532042659d54d590a29d49e7ce07c92
ms.sourcegitcommit: 958f086136f10903c44c92463845b9f3a6a5275f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83715839"
---
# <a name="connect-f5-asm-to-azure-sentinel"></a>Ligue F5 ASM a Azure Sentinel

Este artigo explica como utilizar o conector de dados F5 ASM para facilmente puxar os seus registos F5 ASM para o Azure Sentinel. Isto permite-lhe visualizar dados F5 ASM em livros de trabalho, usá-lo para criar alertas personalizados e incorporá-lo para melhorar a investigação. Ter dados F5 ASM no Azure Sentinel irá fornecer-lhe mais informações sobre a segurança da aplicação web da sua organização, e irá melhorar as suas capacidades de operações de segurança. 

## <a name="configure-your-f5-asm-to-send-cef-messages"></a>Configure o seu F5 ASM para enviar mensagens CEF

1. Siga as instruções em [F5 Configurando](https://techdocs.f5.com/kb/en-us/products/big-ip_asm/manuals/product/asm-implementations-11-5-0/12.html) o registo do evento de segurança da aplicação para configurar a exploração remota, utilizando as seguintes diretrizes:
   - Delineie o tipo de **armazenamento remoto** para **CEF**.
   - Definir o **Protocolo** para **a TCP**.
   - Detete o **endereço IP** para o endereço IP do servidor Syslog.
   - Detete o número da **porta** para **514**, ou a porta que definiu o seu agente para utilizar.
   - Pode definir o tamanho máximo da **corda de consulta** para o tamanho que se define no seu agente.

1. Para utilizar o esquema relevante no Log Analytics para eventos CEF, procure `CommonSecurityLog` .

1. Continuar a [PASSO 3: Validar a conectividade.](connect-cef-verify.md)


## <a name="next-steps"></a>Próximos passos
Neste documento, aprendeu a ligar o F5 ASM ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Aprenda a [obter visibilidade nos seus dados e ameaças potenciais.](quickstart-get-visibility.md)
- Começar [a detetar ameaças com o Azure Sentinel.](tutorial-detect-threats.md)
- [Utilize livros](tutorial-monitor-your-data.md) de trabalho para monitorizar os seus dados.

