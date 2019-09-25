---
title: Conectar dados do firewall do Windows ao Azure Sentinel | Microsoft Docs
description: Saiba como conectar dados do firewall do Windows ao Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
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
ms.author: rkarlin
ms.openlocfilehash: 840e8b3bc86281a8c42689b1cb68917741ef2bd9
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/24/2019
ms.locfileid: "71240633"
---
# <a name="connect-windows-firewall"></a>Ligar à firewall do Windows



O conector do firewall do Windows permite que você conecte seus logs do firewall do Windows com facilidade, se eles estiverem conectados ao seu espaço de trabalho do Azure Sentinel. Essa conexão permite que você exiba painéis, crie alertas personalizados e melhore a investigação. Isso lhe dá mais informações sobre a rede da sua organização e aprimora seus recursos de operação de segurança. A solução coleta eventos de firewall do Windows dos computadores Windows nos quais um agente de Log Analytics está instalado. 


> [!NOTE]
> Os dados serão armazenados na localização geográfica do espaço de trabalho no qual você está executando o Azure Sentinel.

## <a name="enable-the-connector"></a>Habilitar o conector 

1. No portal do Azure Sentinel, selecione **conectores de dados** e clique no bloco **Firewall do Windows** . 
1.  Se seus computadores Windows estiverem no Azure:
    1. Clique em **instalar agente na máquina virtual do Windows do Azure**.
    1. Na lista **máquinas virtuais** , selecione o computador Windows que você deseja transmitir para o Azure Sentinel. Verifique se esta é uma VM do Windows.
    1. Na janela que é aberta para essa VM, clique em **conectar**.  
    1. Clique em **habilitar** na janela **conector do firewall do Windows** . 

2. Se seu computador Windows não for uma VM do Azure:
    1. Clique em **instalar agente em computadores não Azure**.
    1. Na janela **agente direto** , selecione baixar o **agente do Windows (64 bits)** ou **baixar o agente do Windows (32 bits)** .
    1. Instale o agente em seu computador Windows. Copie a **ID do espaço de trabalho**, a **chave primária**e a **chave secundária** e use-as quando solicitado durante a instalação.

4. Selecione os tipos de dados que você deseja transmitir.
5. Clique em **instalar solução**.
6. Para usar o esquema relevante no Log Analytics para o Firewall do Windows, procure por **SecurityEvent**.

## <a name="validate-connectivity"></a>Validar conectividade

Pode levar até 20 minutos até que os logs comecem a aparecer na Log Analytics. 



## <a name="next-steps"></a>Passos seguintes
Neste documento, você aprendeu a conectar o Firewall do Windows ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade de seus dados e ameaças potenciais](quickstart-get-visibility.md).
- Comece [a detectar ameaças com o Azure Sentinel](tutorial-detect-threats-built-in.md).

