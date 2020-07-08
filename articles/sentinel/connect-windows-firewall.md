---
title: Ligue os dados da firewall do Windows ao Azure Sentinel. Microsoft Docs
description: Ativar o conector de firewall do Windows no Azure Sentinel para transmitir facilmente eventos de firewall a partir de máquinas windows que tenham agentes do Log Analytics instalados.
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
ms.openlocfilehash: d33ba0dbb62cd7206829ed9ae580ea2aa3334bcf
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85559484"
---
# <a name="connect-windows-firewall"></a>Ligar à firewall do Windows



O conector de firewall do Windows permite-lhe ligar facilmente os registos de firewalls do Windows, se estiver ligado ao seu espaço de trabalho Azure Sentinel. Esta ligação permite-lhe visualizar dashboards, criar alertas personalizados e melhorar a investigação. Isto dá-lhe mais informações sobre a rede da sua organização e melhora as suas capacidades de operação de segurança. A solução recolhe eventos de firewall do Windows a partir das máquinas do Windows nas quais está instalado um agente Log Analytics. 


> [!NOTE]
> - Os dados serão armazenados na localização geográfica do espaço de trabalho em que está a executar o Azure Sentinel.
> - Se o Azure Sentinel e o Azure Security Center forem recolhidos para o mesmo espaço de trabalho, não há necessidade de ativar a solução Windows Firewall através deste conector. Se o ativou de qualquer forma, não causará dados duplicados. 

## <a name="enable-the-connector"></a>Ativar o conector 

1. No portal Azure Sentinel, selecione **os conectores de dados** e, em seguida, clique no azulejo da firewall do **Windows.** 
1.  Se as suas máquinas Windows estiverem em Azure:
    1. Clique **em Instalar o agente na máquina virtual Azure Windows**.
    1. Na lista **de máquinas Virtuais,** selecione a máquina Windows que pretende transmitir para o Azure Sentinel. Certifique-se de que este é um VM Windows.
    1. Na janela que se abre para o VM, clique **em Connect**.  
    1. Clique em **Ativar** na janela do **conector de firewall** do Windows. 

2. Se a sua máquina Windows não for um Azure VM:
    1. Clique **em Instalar o agente em máquinas não-Azure**.
    1. Na janela **do agente Direct,** selecione **o agente do Windows (64 bit)** ou **o agente do Windows de descarregamento (32 bits).**
    1. Instale o agente na sua máquina Windows. Copie o ID do **espaço de trabalho,** **a tecla primária**e a **tecla secundária** e use-as quando solicitada durante a instalação.

4. Selecione quais os tipos de dados que pretende transmitir.
5. Clique **na solução de instalação**.
6. Para utilizar o esquema relevante no Log Analytics para a firewall do Windows, procure o **SecurityEvent**.

## <a name="validate-connectivity"></a>Validar conectividade

Pode demorar mais de 20 minutos até que os seus registos comecem a aparecer no Log Analytics. 



## <a name="next-steps"></a>Próximos passos
Neste documento, aprendeu a ligar firewall do Windows ao Azure Sentinel. Para saber mais sobre Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade nos seus dados e potenciais ameaças.](quickstart-get-visibility.md)
- Começa [a detetar ameaças com o Azure Sentinel.](tutorial-detect-threats-built-in.md)

