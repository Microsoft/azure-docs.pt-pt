---
title: Validar a conectividade com o Azure Sentinel. Microsoft Docs
description: Valide a conectividade da sua solução de segurança para garantir que as mensagens CEF estão a ser reencaminhadas para o Azure Sentinel.
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
ms.openlocfilehash: e224f6d5cfd82dfc6cb1ce107d111ee0e031247b
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77588438"
---
# <a name="step-3-validate-connectivity"></a>PASSO 3: Validar a conectividade



Depois de ter implementado o agente e configurado a sua solução de segurança para enviar mensagens CEF, use este artigo para entender como verificar a conectividade entre o Azure Sentinel e a sua solução de segurança. 

## <a name="how-to-validate-connectivity"></a>Como validar a conectividade

1. Abra o Log Analytics para se certificar de que os registos são recebidos utilizando o esquema CommonSecurityLog.<br> Pode demorar mais de 20 minutos até que os seus registos comecem a aparecer no Log Analytics. 

1. Antes de executar o script, recomendamos que envie mensagens da sua solução de segurança para se certificar de que estão a ser reencaminhadas para a máquina de procuração Syslog que configura. 
1. Deve ter permissões elevadas (sudo) na sua máquina. Certifique-se de que tem Python na sua máquina utilizando o seguinte comando: `python –version`
1. Execute o seguinte script para verificar a conectividade entre o agente, Azure Sentinel, e a sua solução de segurança. Verifica se o encaminhamento da daemon está devidamente configurado, escuta as portas corretas, e que nada está bloqueando a comunicação entre o daemon e o agente Log Analytics. O script também envia mensagens falsas 'TestCommonEventFormat' para verificar a conectividade de ponta a ponta. <br>
 `sudo wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_troubleshoot.py&&sudo python cef_troubleshoot.py [WorkspaceID]`


## <a name="next-steps"></a>Passos seguintes
Neste documento, aprendeu a ligar os aparelhos CEF ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Aprenda a [obter visibilidade nos seus dados e ameaças potenciais.](quickstart-get-visibility.md)
- Começar [a detetar ameaças com o Azure Sentinel.](tutorial-detect-threats.md)
- [Utilize livros](tutorial-monitor-your-data.md) de trabalho para monitorizar os seus dados.

