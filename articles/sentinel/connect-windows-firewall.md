---
title: Ligue os dados da firewall do Windows ao Azure Sentinel. Microsoft Docs
description: Saiba como ligar os dados de firewall do Windows ao Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 0e41f896-8521-49b8-a244-71c78d469bc3
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: 5d2f68261143c3fc5bbcda0b739af17251eeee63
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77588064"
---
# <a name="connect-windows-firewall"></a>Ligar à firewall do Windows



O conector de firewall windows permite-lhe ligar facilmente os registos de firewalls do Windows, caso estejam ligados ao seu espaço de trabalho Azure Sentinel. Esta ligação permite-lhe visualizar dashboards, criar alertas personalizados e melhorar a investigação. Isto dá-lhe mais informações sobre a rede da sua organização e melhora as suas capacidades de operação de segurança. A solução recolhe eventos de firewall do Windows a partir das máquinas Windows nas quais está instalado um agente Log Analytics. 


> [!NOTE]
> - Os dados serão armazenados na localização geográfica do espaço de trabalho em que está a executar o Azure Sentinel.
> - Se o Azure Sentinel e o Azure Security Center forem recolhidos para o mesmo espaço de trabalho, não é necessário ativar a solução Windows Firewall através deste conector. Se o ativar de qualquer forma, não causará dados duplicados. 

## <a name="enable-the-connector"></a>Ativar o conector 

1. No portal Azure Sentinel, selecione **conectores de dados** e, em seguida, clique no azulejo **da firewall do Windows.** 
1.  Se as suas máquinas Windows estiverem em Azure:
    1. Clique **em instalar o agente na máquina virtual Do Windows .**
    1. Na lista de **máquinas Virtuais,** selecione a máquina Windows que pretende transmitir para o Azure Sentinel. Certifique-se de que este é um VM do Windows.
    1. Na janela que se abre para esse VM, clique em **Connect**.  
    1. Clique em **ativar** na janela do **conector de firewall do Windows.** 

2. Se a sua máquina Windows não for um VM Azure:
    1. Clique **em instalar o agente em máquinas não-Azure**.
    1. Na janela **do agente Direct,** selecione o **agente Descarregamento do Windows (64 bits)** ou **o agente Dowindows (32 bits)**.
    1. Instale o agente na sua máquina Windows. Copie o **ID workspace,** **a chave primária**e a tecla **secundária** e utilize-os quando solicitado durante a instalação.

4. Selecione quais os tipos de dados que pretende transmitir.
5. Clique em **instalar a solução**.
6. Para utilizar o esquema relevante no Log Analytics para a firewall do Windows, procure **SecurityEvent**.

## <a name="validate-connectivity"></a>Validar a conectividade

Pode demorar mais de 20 minutos até que os seus registos comecem a aparecer no Log Analytics. 



## <a name="next-steps"></a>Passos seguintes
Neste documento, aprendeu a ligar a firewall do Windows ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Aprenda a [obter visibilidade nos seus dados e ameaças potenciais.](quickstart-get-visibility.md)
- Começar [a detetar ameaças com o Azure Sentinel.](tutorial-detect-threats-built-in.md)

