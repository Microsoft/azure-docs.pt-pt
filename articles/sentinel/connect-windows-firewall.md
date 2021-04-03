---
title: Ligue os dados do Windows Defender Firewall ao | do Azure Sentinel Microsoft Docs
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
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/05/2020
ms.author: yelevin
ms.openlocfilehash: cf7e389fc4a8a8dfa88691dc034611cae3471731
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "94655345"
---
# <a name="connect-windows-defender-firewall-with-advanced-security-to-azure-sentinel"></a>Ligue firewall do Windows Defender com Segurança Avançada a Azure Sentinel

O [Windows Defender Firewall com](/windows/security/threat-protection/windows-firewall/windows-firewall-with-advanced-security) conector de segurança avançada permite que o Azure Sentinel ingere facilmente firewall do Windows Defender com registos de Segurança Avançada de quaisquer máquinas windows no seu espaço de trabalho. Esta ligação permite-lhe visualizar e analisar eventos do Windows Firewall nos seus livros de trabalho, usá-los na criação de alertas personalizados e incorporá-los nas suas investigações de segurança, dando-lhe mais informações sobre a rede da sua organização e melhorando as suas capacidades de operações de segurança. 

A solução recolhe eventos de firewall do Windows a partir das máquinas do Windows nas quais está instalado um agente Log Analytics. 

> [!NOTE]
> - Os dados serão armazenados na localização geográfica do espaço de trabalho em que está a executar o Azure Sentinel.
>
> - Se os alertas do Azure Defender do Azure Security Center já forem recolhidos para o espaço de trabalho Azure Sentinel, não há necessidade de ativar a solução Windows Firewall através deste conector. No entanto, se o ativou, não irá causar dados duplicados. 

## <a name="prerequisites"></a>Pré-requisitos

- Deve ter lido e escrito permissões no espaço de trabalho ao qual as máquinas que deseja monitorizar estão ligadas.

- Deve ser-lhe atribuída a função **de Colaborador de Log Analytics** na solução SecurityInsights nesse espaço de trabalho, para além de quaisquer funções do **Azure Sentinel.** [Saiba mais](../role-based-access-control/built-in-roles.md#log-analytics-contributor)

## <a name="enable-the-connector"></a>Ativar o conector 

1. No portal Azure Sentinel, selecione **conectores** de dados do menu de navegação.

1. Selecione **o Windows Firewall** na galeria de conectores e clique na página do **conector Open**.

### <a name="instructions-tab"></a>Separador de instruções

- **Se as suas máquinas Windows estiverem em Azure:**

    1. Selecione **Instalar o agente na Máquina Virtual Azure Windows**.

    1. Clique no **Agente de instalação de descarregamento & para máquinas virtuais Azure Windows >** link que aparece.

    1. Na lista **de máquinas Virtuais,** selecione a máquina Windows que pretende transmitir para o Azure Sentinel. (Pode selecionar o **Windows** no filtro da coluna OS para garantir que apenas são apresentados VMs do Windows).

    1. Na janela que se abre para o VM, clique **em Connect**.

    1. Volte ao painel **de Máquinas Virtuais** e repita os dois passos anteriores para quaisquer outros VMs que pretenda ligar. Quando terminar, volte ao painel de firewall do **Windows.**

- **Se a sua máquina Windows não for um Azure VM:**

    1. Selecione **Instalar o agente na máquina do Windows não-Azure**.

    1. Clique no **Agente de instalação de descarregamento & para máquinas não-Azure Windows >** link que aparece.

    1. No painel **de gestão** de agentes, selecione **download Windows Agent (64 bit)** ou **Download Windows Agent (32 bit)**, conforme necessário.

    1. Copie o ID do **espaço de trabalho,** **a tecla primária** e as cordas de **teclas secundárias** num ficheiro de texto. Copie esse ficheiro e o ficheiro de instalação descarregado para a sua máquina Windows. Executar o ficheiro de instalação e, quando solicitado, introduza o ID e as cordas das chaves no ficheiro de texto durante a instalação.

    1. Volte ao painel de firewall do **Windows.**

1. Clique **na solução de instalação**.

### <a name="next-steps-tab"></a>Separador de passos seguintes

- Consulte os livros de trabalho recomendados disponíveis e as amostras de consulta agregadas ao conector de dados do **Windows Firewall** para obter informações sobre os dados de registo do Windows Firewall.

- Para consultar os dados de firewall do Windows em **Logs,** **digite o WindowsFirewall** na janela de consulta.

## <a name="validate-connectivity"></a>Validar conectividade
 
Uma vez que os registos do Windows Firewall são enviados para o Azure Sentinel apenas quando o ficheiro de registo local atingir a capacidade, deixando o registo no seu tamanho padrão de 4096 KB provavelmente resultará em alta latência de recolha. Pode baixar a latência baixando o tamanho do ficheiro de registo. Consulte as instruções para [configurar o registo do Windows Firewall](/windows/security/threat-protection/windows-firewall/configure-the-windows-firewall-log). Note que ao definir o tamanho mínimo possível de log (1 KB) irá praticamente eliminar a latência da recolha, podendo também afetar negativamente o desempenho da máquina local. 

## <a name="next-steps"></a>Passos seguintes
Neste documento, aprendeu a ligar firewall do Windows ao Azure Sentinel. Para saber mais sobre Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade nos seus dados e potenciais ameaças.](quickstart-get-visibility.md)
- Começa [a detetar ameaças com o Azure Sentinel.](tutorial-detect-threats-built-in.md)