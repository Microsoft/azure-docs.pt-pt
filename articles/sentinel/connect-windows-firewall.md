---
title: Ligue os dados de firewall do Windows para a pré-visualização de sentinela do Azure | Documentos da Microsoft
description: Saiba como ligar os dados de firewall do Windows ao sentinela do Azure.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 0e41f896-8521-49b8-a244-71c78d469bc3
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/17/2019
ms.author: rkarlin
ms.openlocfilehash: 09e63612d6e0e70b1bb21c23b158f650d4c34080
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67190589"
---
# <a name="connect-windows-firewall"></a>Ligar à firewall do Windows

> [!IMPORTANT]
> Sentinel do Azure está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

O conector de firewall do Windows permite-lhe ligar facilmente os seus registos de firewalls do Windows, se estiverem ligados à sua área de trabalho de sentinela do Azure. Esta ligação permite-lhe ver os dashboards e criar alertas personalizados e a melhorar a investigação. Isso dá-lhe mais informações sobre a rede da sua organização e melhora as capacidades de operação de segurança. A solução recolhe os eventos de firewall do Windows das máquinas Windows no qual está instalado um agente do Log Analytics. 


> [!NOTE]
> Os dados serão armazenados na localização geográfica da área de trabalho no qual está a executar sentinela do Azure.

## <a name="enable-the-connector"></a>Ativar o conector 

1. No portal do Azure sentinela, selecione **conectores de dados** e, em seguida, clique nas **firewall do Windows** mosaico. 
1.  Se forem suas máquinas do Windows no Azure:
    1. Clique em **instalar o agente na máquina de virtual do Windows Azure**.
    1. Na **máquinas virtuais** , selecione a máquina do Windows que pretende transmitir para o Azure sentinela. Certifique-se de que esta é uma VM do Windows.
    1. Na janela que se abre para essa VM, clique em **Connect**.  
    1. Clique em **habilitar** no **conector de firewall do Windows** janela. 

2. Se o seu computador Windows não é uma VM do Azure:
    1. Clique em **instalar o agente em máquinas não Azure**.
    1. Na **agente direto** janela, selecione **agente de transferir o Windows (64 bits)** ou **agente de transferir o Windows (32 bits)** .
    1. Instale o agente no seu computador Windows. Copiar o **ID da área de trabalho**, **chave primária**, e **chave secundária** e utilizá-los quando lhe for pedido durante a instalação.

4. Selecione os tipos de dados que pretende transmitir em fluxo.
5. Clique em **instalar a solução**.
6. Para utilizar o esquema relevante no Log Analytics para o firewall do Windows, procure **SecurityEvent**.

## <a name="validate-connectivity"></a>Validar a conectividade

Pode demorar mais de 20 minutos até que os seus registos começam a aparecer no Log Analytics. 



## <a name="next-steps"></a>Passos Seguintes
Neste documento, aprendeu a ligar-se a firewall do Windows para sentinela do Azure. Para saber mais sobre sentinela do Azure, veja os artigos seguintes:
- Saiba como [Obtenha visibilidade sobre os seus dados e a potenciais ameaças](quickstart-get-visibility.md).
- Começar a utilizar [deteção de ameaças com Azure sentinela](tutorial-detect-threats.md).

