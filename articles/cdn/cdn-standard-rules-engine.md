---
title: Utilize um motor de regras para impor HTTPS no Standard Azure CDN [ Microsoft Docs
description: Utilize o motor de regras para a Microsoft Standard Azure Content Delivery Network (Azure CDN) para personalizar a forma como o Azure CDN lida com os pedidos http, incluindo bloquear a entrega de determinados tipos de conteúdo, definir uma política de cache e modificar os cabeçalhos HTTP. Neste artigo, aprenda a criar uma regra para redirecionar os utilizadores para HTTPS.
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 11/01/2019
ms.author: magattus
ms.openlocfilehash: 724861305d7a25db409072200ac2bc3bd83f0682
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74171579"
---
# <a name="set-up-the-standard-rules-engine-for-azure-cdn"></a>Configurar o motor standard regras para O CDN Azure

Este artigo descreve como configurar e utilizar o motor standard regras para a Rede de Entrega de Conteúdos Azure (Azure CDN).

## <a name="standard-rules-engine"></a>Motor de regras padrão

Pode utilizar o motor Standard rules para o Azure CDN para personalizar a forma como os pedidos http são tratados. Por exemplo, pode utilizar o motor de regras para impor a entrega de conteúdos para utilizar protocolos específicos, para definir uma política de cache, ou para modificar um cabeçalho HTTP. Este artigo demonstra como criar uma regra que redireciona automaticamente os utilizadores para HTTPS. 

> [!NOTE]
> O motor de regras descrito neste artigo está disponível apenas para o Standard Azure CDN da Microsoft. 

## <a name="redirect-users-to-https"></a>Redirecione os utilizadores para HTTPS

1. Nos seus perfis da Microsoft, vá à Rede de Entrega de Conteúdos Azure.

1. Na página de perfil da **CDN,** selecione o ponto final para o que pretende criar regras.
  
1. Selecione o separador **Regras Motor.**
   
    O painel **do Motor de Regras** abre e exibe a lista das regras globais disponíveis. 
   
    [![Página de novas regras do Azure CDN](./media/cdn-standard-rules-engine/cdn-new-rule.png)](./media/cdn-standard-rules-engine/cdn-new-rule.png#lightbox)
   
   > [!IMPORTANT]
   > A ordem em que são enumeradas várias regras afeta a forma como as regras são tratadas. As ações especificadas numa regra podem ser substituídas por uma regra posterior.
   >

1. Selecione **Adicionar regra** e introduza um nome de regra. Os nomes das regras devem começar com uma letra e podem conter apenas números e letras.

1. Para identificar o tipo de pedidos a que a regra se aplica, crie uma condição de correspondência:
    1. Selecione **Adicionar condição**e, em seguida, selecione a condição de jogo do **protocolo solicitar.**
    1. Para **Operator** (Operador), selecione **Equals** (É igual a).
    1. Para **Valor,** selecione **HTTP**.
   
   [![Condição de correspondência de regras azure CDN](./media/cdn-standard-rules-engine/cdn-match-condition.png)](./media/cdn-standard-rules-engine/cdn-match-condition.png#lightbox)
   
   > [!NOTE]
   > Pode selecionar entre várias condições de jogo na lista de desistência da **condição add.** Para obter uma lista detalhada das condições de jogo, consulte [as condições](cdn-standard-rules-engine-match-conditions.md)de jogo no motor de regras Standard .
   
1. Selecione a ação a aplicar aos pedidos que satisfaçam a condição da partida:
   1. **Selecione Adicionar ação**, e, em seguida, selecione **redirecionamento de URL**.
   1. Para **Tipo,** selecione **Found (302)**.
   1. Para **Protocolo,** selecione **HTTPS**.
   1. Deixe todos os outros campos em branco para utilizar valores de entrada.
   
   [![Ação de regras do CDN azure](./media/cdn-standard-rules-engine/cdn-action.png)](./media/cdn-standard-rules-engine/cdn-action.png#lightbox)
   
   > [!NOTE]
   > Pode selecionar entre várias ações na lista de lançamentos de **ação Add.** Para obter uma lista detalhada de ações, consulte [Ações no motor standard rules](cdn-standard-rules-engine-actions.md).

6. Selecione **Guardar** para salvar a nova regra. A regra está agora disponível para ser usada.
   
   > [!IMPORTANT]
   > As alterações de regras podem demorar até 15 minutos a propagar-se através do Azure CDN.
   >
   

## <a name="next-steps"></a>Passos seguintes

- [Visão geral do CDN azure](cdn-overview.md)
- [Referência do Motor de regras standard](cdn-standard-rules-engine-reference.md)
- [Condições de jogo no motor de regras Standard](cdn-standard-rules-engine-match-conditions.md)
- [Ações no motor standard regras](cdn-standard-rules-engine-actions.md)
