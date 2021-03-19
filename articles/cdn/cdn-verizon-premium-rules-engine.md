---
title: Anular comportamento HTTP com Azure CDN - Verizon Premium regras motor
description: O motor de regras permite-lhe personalizar a forma como os pedidos HTTP são tratados pela Azure CDN da Verizon Premium, tais como bloquear a entrega de determinados tipos de conteúdo, definir uma política de caching e modificar os cabeçalhos HTTP.
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: how-to
ms.date: 05/31/2019
ms.author: allensu
ms.openlocfilehash: a49912bc2275e478d657f06587c4ddc830210d3a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "87040205"
---
# <a name="override-http-behavior-using-the-azure-cdn-from-verizon-premium-rules-engine"></a>Anular o comportamento http usando o Azure CDN do motor de regras Verizon Premium

[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Descrição Geral

O motor de regras Azure CDN permite-lhe personalizar a forma como os pedidos HTTP são tratados. Por exemplo, bloquear a entrega de certos tipos de conteúdo, definir uma política de caching ou modificar um cabeçalho HTTP. Este tutorial demonstra como criar uma regra que altera o comportamento de caching dos ativos da CDN. Para obter mais informações sobre a sintaxe do motor de regras, consulte [a referência do motor das regras do Azure CDN](cdn-verizon-premium-rules-engine-reference.md).

## <a name="access"></a>Access

Para aceder ao motor de regras, tem primeiro de selecionar **Gerir** a partir da parte superior da página de perfil do **CDN** para aceder à página de gestão do Azure CDN. Dependendo se o seu ponto final está otimizado para a aceleração dinâmica do site (DSA), aceda ao motor de regras com o conjunto de regras adequadas para o seu tipo de ponto final:

- Pontos finais otimizados para entrega geral da web ou outra otimização não-DSA:
    
    Selecione o separador **HTTP Large** e, em seguida, selecione **Rules Engine**.

    ![Motor de regras para HTTP](./media/cdn-rules-engine/cdn-http-rules-engine.png)

- Pontos finais otimizados para a DSA:
    
    Selecione o **separador ADN** e, em seguida, selecione **O Motor de Regras**.
    
    ADN é um termo usado pela Verizon para especificar o conteúdo da DSA. Quaisquer regras que crie aqui são ignoradas por quaisquer pontos finais no seu perfil que não estejam otimizadas para a DSA.

    ![Motor de regras para a DSA](./media/cdn-rules-engine/cdn-dsa-rules-engine.png)

## <a name="tutorial"></a>Tutorial

1. Na página de perfil do **CDN,** **selecione Gerir**.
   
    ![Botão de gestão de perfil CDN](./media/cdn-rules-engine/cdn-manage-btn.png)
   
    O portal de gestão cdn abre.

2. Selecione o separador **HTTP Large** e, em seguida, selecione **Rules Engine**.
   
    As opções para uma nova regra são apresentadas.
   
    ![Novas opções de regras da CDN](./media/cdn-rules-engine/cdn-new-rule.png)
   
   > [!IMPORTANT]
   > A ordem pela qual são listadas várias regras afeta a forma como são tratadas. Uma regra subsequente pode anular as ações especificadas por uma regra anterior. Por exemplo, se tiver uma regra que permite o acesso a um recurso baseado num imóvel de pedido e uma regra que nega o acesso a todos os pedidos, a segunda regra substitui a primeira. As regras só se sobrepõem a regras anteriores se interagirem com as mesmas propriedades.
   >

3. Introduza um nome na caixa de texto **Nome / Descrição.**

4. Identifique o tipo de pedidos a que a regra se aplica. Utilize a condição de correspondência **predefinido, Sempre**.
   
   ![Condição de correspondência de regras CDN](./media/cdn-rules-engine/cdn-request-type.png)
   
   > [!NOTE]
   > Várias condições de correspondência estão disponíveis na lista de espera. Para obter informações sobre a condição de correspondência atualmente selecionada, selecione o ícone informativo azul à sua esquerda.
   >
   >  Para obter uma lista detalhada de expressões condicionais, consulte [as expressões condicionais do motor rules](cdn-verizon-premium-rules-engine-reference-match-conditions.md).
   >  
   > Para obter uma lista detalhada das condições de jogo, consulte [as condições de correspondência do motor regras](cdn-verizon-premium-rules-engine-reference-match-conditions.md).
   >
   >

5. Para adicionar uma nova funcionalidade, selecione o **+** botão ao lado **de Funcionalidades**.  No dropdown à esquerda, selecione **Force Internal Max-Age**.  Na caixa de texto que aparece, introduza **300**. Não altere os valores predefinidos restantes.
   
   ![Função de regra CDN](./media/cdn-rules-engine/cdn-new-feature.png)
   
   > [!NOTE]
   > Várias funcionalidades estão disponíveis na lista de dropdown. Para obter informações sobre a funcionalidade atualmente selecionada, selecione o ícone informativo azul à sua esquerda.
   >
   > Para **a Força Interna Max-Age**, os ativos e os `Cache-Control` `Expires` cabeçalhos são ultrapassados para controlar quando o nó de borda CDN atualiza o ativo a partir da origem. Neste exemplo, o nó de borda CDN caches o ativo por 300 segundos, ou 5 minutos, antes de refrescar o ativo da sua origem.
   >
   > Para obter uma lista detalhada de funcionalidades, consulte [as características do motor Rules](cdn-verizon-premium-rules-engine-reference-features.md).
   >
   >

6. **Selecione Adicionar** para guardar a nova regra.  A nova regra aguarda agora a aprovação. Depois de aprovado, o estado muda de **XML pendente** para **Ative XML**.
   
   > [!IMPORTANT]
   > As alterações de regras podem demorar até 10 minutos a propagar-se através do Azure CDN.
   >
   >

## <a name="see-also"></a>Ver também

- [Visão geral do Azure CDN](cdn-overview.md)
- [Referência do motor de regras](cdn-verizon-premium-rules-engine-reference.md)
- [Condições de correspondência do motor de regras](cdn-verizon-premium-rules-engine-reference-match-conditions.md)
- [Expressões condicionais do motor de regras](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [Funcionalidades do motor de regras](cdn-verizon-premium-rules-engine-reference-features.md)
- [Azure Fridays: As poderosas novas funcionalidades premium da Azure CDN](https://azure.microsoft.com/documentation/videos/azure-cdns-powerful-new-premium-features/) (vídeo)