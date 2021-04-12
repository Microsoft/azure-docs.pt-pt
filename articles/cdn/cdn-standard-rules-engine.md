---
title: Utilize um motor de regras para impor HTTPS na Standard Azure CDN | Microsoft Docs
description: Utilize o motor de regras da Microsoft Standard Azure Content Delivery Network (Azure CDN) para personalizar a forma como o Azure CDN lida com pedidos HTTP, incluindo bloquear a entrega de certos tipos de conteúdo, definir uma política de caching e modificar os cabeçalhos HTTP. Neste artigo, aprenda a criar uma regra para redirecionar os utilizadores para HTTPS.
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: how-to
ms.date: 11/01/2019
ms.author: allensu
ms.openlocfilehash: b94798580b2d4ba746c152486c0de753cf64d2c9
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102564905"
---
# <a name="set-up-the-standard-rules-engine-for-azure-cdn"></a>Configurar o motor de regras Standard para Azure CDN

Este artigo descreve como configurar e utilizar o motor de regras Standard para a Rede de Entrega de Conteúdos Azure (Azure CDN).

## <a name="standard-rules-engine"></a>Motor de regras padrão

Pode utilizar o motor de regras Standard para a Azure CDN para personalizar a forma como os pedidos HTTP são tratados. Por exemplo, pode utilizar o motor de regras para impor a entrega de conteúdos para utilizar protocolos específicos, para definir uma política de caching ou para modificar um cabeçalho HTTP. Este artigo demonstra como criar uma regra que redireciona automaticamente os utilizadores para HTTPS. 

> [!NOTE]
> O motor de regras descrito neste artigo está disponível apenas para o Standard Azure CDN da Microsoft. 

## <a name="redirect-users-to-https"></a>Redirecionar os utilizadores para HTTPS

1. Nos seus perfis microsoft, vá à Rede de Entrega de Conteúdos Azure.

1. Na página de perfil do **CDN,** selecione o ponto final para o quais pretende criar regras.
  
1. Selecione o **separador 'Rules Engine'.**
   
    O **painel do Motor das Regras** abre e apresenta a lista das regras globais disponíveis. 
   
    [![Página de novas regras do Azure CDN](./media/cdn-standard-rules-engine/cdn-new-rule.png)](./media/cdn-standard-rules-engine/cdn-new-rule.png#lightbox)
   
   > [!IMPORTANT]
   > A ordem pela qual são listadas várias regras afeta a forma como as regras são tratadas. As ações especificadas numa regra podem ser substituídas por uma regra subsequente.
   >

1. **Selecione Adicionar regra** e introduzir um nome de regra. Os nomes das regras devem começar com uma letra e conter apenas números e letras.

1. Para identificar o tipo de pedidos a que a regra se aplica, crie uma condição de correspondência:
    1. **Selecione Adicionar a condição** e, em seguida, selecione a condição de correspondência do protocolo **'Pedido'.**
    1. Para **Operator** (Operador), selecione **Equals** (É igual a).
    1. Para **Valor**, selecione **HTTP**.
   
   [![Condição de correspondência de regras do Azure CDN](./media/cdn-standard-rules-engine/cdn-match-condition.png)](./media/cdn-standard-rules-engine/cdn-match-condition.png#lightbox)
   
   > [!NOTE]
   > Pode selecionar a partir de várias condições de correspondência na lista de down-down do **estado de adicionar.** Para obter uma lista detalhada das condições de jogo, consulte [as condições de jogo no motor de regras Standard](cdn-standard-rules-engine-match-conditions.md).
   
1. Selecione a ação para aplicar aos pedidos que satisfaçam a condição de jogo:
   1. **Selecione Adicionar a ação** e, em seguida, selecione o **reorientação de URL**.
   1. Para **tipo**, selecione **Found (302)**.
   1. Para **protocolo**, selecione **HTTPS**.
   1. Deixe todos os outros campos em branco para utilizar valores de entrada.
   
   [![Ação de regra da Azure CDN](./media/cdn-standard-rules-engine/cdn-action.png)](./media/cdn-standard-rules-engine/cdn-action.png#lightbox)
   
   > [!NOTE]
   > Pode selecionar a partir de várias ações na lista de drop-down de **ação Adicionar.** Para obter uma lista detalhada de ações, consulte [Ações no motor de regras Standard](cdn-standard-rules-engine-actions.md).

6. **Selecione Guardar** para guardar a nova regra. A regra está agora disponível para ser utilizada.
   
   > [!IMPORTANT]
   > As alterações de regras podem demorar até 15 minutos a propagar-se através do Azure CDN.
   >
   

## <a name="next-steps"></a>Passos seguintes

- [Visão geral do Azure CDN](cdn-overview.md)
- [Referência do Motor de regras standard](cdn-standard-rules-engine-reference.md)
- [Condições de jogo no motor de regras standard](cdn-standard-rules-engine-match-conditions.md)
- [Ações no motor de regras Standard](cdn-standard-rules-engine-actions.md)
