---
title: Use a caça Livestream em Azure Sentinel para detetar ameaças | Microsoft Docs
description: Este artigo descreve como usar a caça Livestream em Azure Sentinel para acompanhar os dados.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/14/2020
ms.author: yelevin
ms.openlocfilehash: 14928d3c94ced8d1cd0c12e76428be73b68b91d9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "84783168"
---
# <a name="use-hunting-livestream-in-azure-sentinel-to-detect-threats"></a>Utilizar a transmissão em direto da investigação no Azure Sentinel para detetar ameaças

Use livestream de caça para criar sessões interativas que lhe permitam testar consultas recém-criadas à medida que os eventos ocorrem, obter notificações das sessões quando um jogo é encontrado, e lançar investigações se necessário. Pode criar rapidamente uma sessão livestream utilizando qualquer consulta de Log Analytics.

- **Teste consultas recém-criadas à medida que os eventos ocorrem**
    
    Pode testar e ajustar consultas sem quaisquer conflitos às regras atuais que estão a ser aplicadas ativamente aos eventos. Depois de confirmar que estas novas consultas funcionam como esperado, é fácil promovê-las a regras de alerta personalizadas, selecionando uma opção que eleva a sessão a um alerta.

- **Ser notificado quando as ameaças ocorrem**
    
    Pode comparar feeds de dados de ameaça com dados de registo agregados e ser notificado quando ocorre uma correspondência. Os feeds de dados de ameaça são fluxos contínuos de dados que estão relacionados com ameaças potenciais ou atuais, pelo que a notificação pode indicar uma potencial ameaça para a sua organização. Crie uma sessão livestream em vez de uma regra de alerta personalizado quando pretender ser notificado de um problema potencial sem as despesas gerais de manter uma regra de alerta personalizado.

- **Iniciar investigações**
    
    Se houver uma investigação ativa que envolva um ativo como um hospedeiro ou utilizador, pode ver atividade específica (ou qualquer) nos dados de registo à medida que ocorre nesse ativo. Pode ser notificado quando essa atividade ocorrer.


## <a name="create-a-livestream-session"></a>Criar uma sessão livestream

Você pode criar uma sessão livestream a partir de uma consulta de caça existente, ou criar a sua sessão do zero.

1. No portal Azure, navegue para a  >  **Sentinel Threat Management**  >  **Hunting**.

1. Para criar uma sessão livestream a partir de uma consulta de caça:
    
    1. A partir do separador **Consultas,** localize a consulta de caça para usar.
    1. Clique com o botão direito na consulta e **selecione Adicionar ao livestream**. Por exemplo:
    
    > [!div class="mx-imgBorder"]
    > ![criar sessão Livestream a partir de consulta de caça Azure Sentinel](./media/livestream/livestream-from-query.png)

1. Para criar uma sessão livestream do zero: 
    
    1. Selecione o **separador Livestream**
    1. Clique **+ Novo livestream**.
    
1. No **painel Livestream:**
    
    - Se começou a transmitir a partir de uma consulta, reveja a consulta e faça quaisquer alterações que queira fazer.
    - Se começou a transmitir do zero, crie a sua consulta. 

1. Selecione **Reproduzir** a partir da barra de comando.
    
    A barra de estado sob a barra de comando indica se a sessão livestream está em funcionamento ou pausa. No seguinte exemplo, a sessão está em execução:
    
    > [!div class="mx-imgBorder"]
    > ![criar sessão livestream da caça Azure Sentinel](./media/livestream/livestream-session.png)

1. **Selecione Salvar** da barra de comando.
    
    A menos que **selecione Pause,** a sessão continua a decorrer até ser assinada a partir do portal Azure.

## <a name="view-your-livestream-sessions"></a>Veja as suas sessões livestream

1. No portal Azure, navegue para a  >  **gestão da** Sentinel Threat  >  **Hunting**  >  **Livestream.**

1. Selecione a sessão livestream que deseja visualizar ou editar. Por exemplo:
    
    > [!div class="mx-imgBorder"]
    > ![criar sessão livestream a partir de consulta de caça Azure Sentinel](./media/livestream/livestream-tab.png)
    
    A sua sessão de livestream selecionada abre para que você toque, faça uma pausa, edite e assim por diante.

## <a name="receive-notifications-when-new-events-occur"></a>Receber notificações quando ocorrerem novos eventos

Como as notificações livestream para novos eventos utilizam notificações do portal Azure, vê estas notificações sempre que utilizar o portal Azure. Por exemplo:

![Notificação do portal Azure para livestream](./media/livestream/notification.png)

Selecione a notificação para abrir o **painel Livestream.**
 
## <a name="elevate-a-livestream-session-to-an-alert"></a>Elevar uma sessão de livestream para um alerta

Pode promover uma sessão livestream para um novo alerta selecionando **Elevate para alertar** a partir da barra de comando na sessão livestream relevante:

> [!div class="mx-imgBorder"]
> ![Elevar a sessão livestream para um alerta](./media/livestream/elevate-to-alert.png)

Esta ação abre o assistente de criação de regras, que é pré-voizado com a consulta que está associada à sessão livestream.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a usar o livestream de caça em Azure Sentinel. Para saber mais sobre Azure Sentinel, consulte os seguintes artigos:

- [Caça proativamente por ameaças](hunting.md)
- [Use cadernos para executar campanhas de caça automatizadas](notebooks.md)
