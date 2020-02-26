---
title: Implementar o agente para ligar os dados do CEF à Pré-visualização do Azure Sentinel. Microsoft Docs
description: Aprenda a implementar o agente para ligar os dados do CEF ao Azure Sentinel.
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
ms.date: 11/26/2019
ms.author: yelevin
ms.openlocfilehash: b0c9335357cb793ea76e1dbe68575f716a50372a
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77588472"
---
# <a name="step-1-deploy-the-agent"></a>Passo 1: Implementar o agente


Neste passo, você precisa selecionar a máquina Linux que funcionará como um proxy entre O Azure Sentinel e a sua solução de segurança. Terá de executar um guião na máquina de procuração que:
- Instala o agente Log Analytics e configura-o conforme necessário para ouvir as mensagens Syslog.
- Configura o daemon Syslog para ouvir mensagens Syslog usando a porta TCP 514 e, em seguida, reencaminha apenas as mensagens CEF para o agente Log Analytics usando a porta TCP 25226.
- Configura o agente Syslog para recolher os dados e enviá-lo de forma segura para o Azure Sentinel, onde é analisado e enriquecido.
 
## <a name="deploy-the-agent"></a>Implementar o agente
 
1. No portal Azure Sentinel, clique em **conectores** de dados e selecione **Formato de Evento Comum (CEF)** e, em seguida, abra a **página do conector**. 

1. Em **instalação e configurar o agente Syslog,** selecione o seu tipo de máquina, seja Azure, outra nuvem ou no local. 
   > [!NOTE]
   > Uma vez que o script no próximo passo instala o agente Log Analytics e liga a máquina ao seu espaço de trabalho Azure Sentinel, certifique-se de que esta máquina não está ligada a qualquer outro espaço de trabalho.
1. Deve ter permissões elevadas (sudo) na sua máquina. Certifique-se de que tem Python na sua máquina utilizando o seguinte comando: `python –version`

1. Execute o seguinte guião na sua máquina de procuração.
   `sudo wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_installer.py&&sudo python cef_installer.py [WorkspaceID] [Workspace Primary Key]`
1. Enquanto o script estiver em execução, verifique se não obtém nenhum erro ou mensagens de aviso.

Continuar a [PASSO 2: Configure a sua solução de segurança para transmitir mensagens CEF](connect-cef-solution-config.md) .


## <a name="next-steps"></a>Passos seguintes
Neste documento, aprendeu a ligar os aparelhos CEF ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Aprenda a [obter visibilidade nos seus dados e ameaças potenciais.](quickstart-get-visibility.md)
- Começar [a detetar ameaças com o Azure Sentinel.](tutorial-detect-threats.md)

