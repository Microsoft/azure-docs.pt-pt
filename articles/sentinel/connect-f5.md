---
title: Ligue os dados da F5 ASM ao Azure Sentinel. Microsoft Docs
description: Aprenda a utilizar o conector de dados F5 ASM para puxar os registos DE F5 ASM para o Azure Sentinel. Ver dados de F5 ASM em livros de trabalho, criar alertas e melhorar a investigação.
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
ms.openlocfilehash: 81a0f0e01827c48518f2eb1fba062e9e6536d9df
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94655702"
---
# <a name="connect-f5-asm-to-azure-sentinel"></a>Ligue f5 ASM a Azure Sentinel

Este artigo explica como utilizar o conector de dados F5 ASM para puxar facilmente os seus registos DE F5 ASM para o Azure Sentinel. Isto permite-lhe ver os dados DE F5 ASM em livros de trabalho, usá-lo para criar alertas personalizados e incorporá-lo para melhorar a investigação. Ter dados de F5 ASM no Azure Sentinel irá fornecer-lhe mais informações sobre a segurança da aplicação web da sua organização, e irá melhorar as suas capacidades de operações de segurança. 

## <a name="configure-your-f5-asm-to-send-cef-messages"></a>Configure o seu F5 ASM para enviar mensagens CEF

1. Siga as instruções em [F5 Configurar](https://techdocs.f5.com/kb/en-us/products/big-ip_asm/manuals/product/asm-implementations-11-5-0/12.html) o Registo de Eventos de Segurança da Aplicação para configurar a registo remoto, utilizando as seguintes orientações:
   - Desaceia o **tipo de armazenamento remoto** ao **CEF**.
   - Desa cos **para** a **TCP.**
   - Desaponuse o **endereço IP** do servidor Syslog.
   - Desaça o número da **porta** para **514,** ou a porta que definir o seu agente para utilizar.
   - Pode definir o **tamanho máximo da corda de consulta** para o tamanho que definiu no seu agente.

1. Para utilizar o esquema relevante no Log Analytics para eventos CEF, procure `CommonSecurityLog` .

1. Continuar a [PASSO 3: Validar a conectividade](connect-cef-verify.md).


## <a name="next-steps"></a>Próximos passos
Neste documento, aprendeu a ligar F5 ASM ao Azure Sentinel. Para saber mais sobre Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade nos seus dados e potenciais ameaças.](quickstart-get-visibility.md)
- Começa [a detetar ameaças com o Azure Sentinel.](./tutorial-detect-threats-built-in.md)
- [Utilize livros para](tutorial-monitor-your-data.md) monitorizar os seus dados.