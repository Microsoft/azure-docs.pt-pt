---
title: Use a caça livestream em Azure Sentinel para detetar ameaças Microsoft Docs
description: Este artigo descreve como usar a caça Livestream em Azure Sentinel para acompanhar os dados.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/06/2019
ms.author: yelevin
ms.openlocfilehash: b392644e504fa8187e637278bef8718c9c2caa3f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77582131"
---
# <a name="use-hunting-livestream-in-azure-sentinel-to-detect-threats"></a>Use o livestream da caça em Azure Sentinel para detetar ameaças

> [!IMPORTANT]
> O livestream de caça em Azure Sentinel está atualmente em pré-visualização pública e gradualmente a ser lançado para os inquilinos.
> Esta funcionalidade é fornecida sem um acordo de nível de serviço, e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para mais informações, consulte [os Termos Suplementares de Utilização para pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)do Microsoft Azure .


Use o livestream de caça para criar sessões interativas que lhe permitam testar consultas recém-criadas à medida que os eventos ocorrem, receber notificações das sessões quando um jogo é encontrado, e lançar investigações se necessário. Pode criar rapidamente uma sessão de livestream utilizando qualquer consulta de Log Analytics.

- **Teste de consultas recém-criadas à medida que os eventos ocorrem**
    
    Pode testar e ajustar consultas sem quaisquer conflitos às regras atuais que estão a ser aplicadas ativamente aos eventos. Depois de confirmar que estas novas consultas funcionam como esperado, é fácil promovê-las a regras de alerta personalizadas, selecionando uma opção que eleva a sessão a um alerta.

- **Seja notificado quando ocorrerem ameaças**
    
    Pode comparar os feeds de dados de ameaças com dados de registo agregados e ser notificado quando ocorre uma correspondência. Os feeds de dados de ameaça são fluxos contínuos de dados que estão relacionados com ameaças potenciais ou atuais, pelo que a notificação pode indicar uma potencial ameaça para a sua organização. Crie uma sessão livestream em vez de uma regra de alerta personalizado quando quiser ser notificado de um problema potencial sem as despesas gerais de manter uma regra de alerta personalizado.

- **Iniciar investigações**
    
    Se houver uma investigação ativa que envolva um ativo como um anfitrião ou utilizador, pode ver uma atividade específica (ou qualquer) nos dados de registo tal como ocorre nesse ativo. Pode ser notificado quando essa atividade ocorrer.


## <a name="create-a-livestream-session"></a>Criar uma sessão de livestream

Você pode criar uma sessão livestream a partir de uma consulta de caça existente, ou criar sua sessão de raiz.

1. No portal Azure, navegue para a**caça à****caça** > de ameaça **sentinela.** > 

2. Para criar uma sessão livestream a partir de uma consulta de caça:
    
    1. Do separador **Consultas,** localize a consulta de caça a usar.
    2. Clique na consulta e selecione **Adicionar ao livestream**. Por exemplo:
    
    > [!div class="mx-imgBorder"]
    > ![criar sessão livestream a partir de consulta de caça Azure Sentinel](./media/livestream/livestream-from-query.png)

3. Para criar uma sessão livestream do zero: 
    
    1. Selecione o separador **Livestream**
    2. Selecione **Ir ao livestream**.
    
4. No painel **Livestream:**
    
    - Se começou em livestream a partir de uma consulta, reveja a consulta e faça quaisquer alterações que queira fazer.
    - Se começaste a livestream do zero, cria a tua consulta. 

5. Selecione **Reproduzir** a partir da barra de comando.
    
    A barra de estado sob a barra de comando indica se a sua sessão de livestream está a decorrer ou fez uma pausa. No exemplo seguinte, a sessão está a decorrer:
    
    > [!div class="mx-imgBorder"]
    > ![criar sessão livestream da caça Azure Sentinel](./media/livestream/livestream-session.png)

6. Selecione **Guardar** a partir da barra de comando.
    
    A menos que selecione **Pause,** a sessão continua a decorrer até ser assinada pelo portal Azure.

## <a name="view-your-livestream-sessions"></a>Veja as suas sessões de livestream

1. No portal Azure, navegue para o separador**Hunting** > **Livestream,** **gestão** > de ameaças **sentinelas.** > 

2. Selecione a sessão livestream que pretende ver ou editar. Por exemplo:
    
    > [!div class="mx-imgBorder"]
    > ![criar sessão livestream a partir de consulta de caça Azure Sentinel](./media/livestream/livestream-tab.png)
    
    A sua sessão de livestream selecionada abre para que possa reproduzir, pausar, editar, e assim por diante.

## <a name="receive-notifications-when-new-events-occur"></a>Receba notificações quando ocorrerem novos eventos

Como as notificações em livestream para novos eventos utilizam notificações do portal Azure, vê estas notificações sempre que utiliza o portal Azure. Por exemplo:

![Notificação do portal Azure para livestream](./media/livestream/notification.png)

Selecione a notificação para abrir o painel **Livestream.**
 
## <a name="elevate-a-livestream-session-to-an-alert"></a>Elevar uma sessão livestream para um alerta

Você pode promover uma sessão livestream para um novo alerta, selecionando **Elevate para alertar** a partir da barra de comando na sessão livestream relevante:

> [!div class="mx-imgBorder"]
> ![Elevar a sessão livestream para um alerta](./media/livestream/elevate-to-alert.png)

Esta ação abre o feiticeiro de criação de regras, que é prepovoado com a consulta que está associada à sessão livestream.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a usar o livestream de caça em Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:

- [Caça proativamente por ameaças](hunting.md)
- [Use cadernos para executar campanhas de caça automatizadas](notebooks.md)
