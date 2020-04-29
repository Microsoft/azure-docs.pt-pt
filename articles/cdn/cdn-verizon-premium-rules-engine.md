---
title: Sobrepor o comportamento do HTTP com o Azure CDN - Motor de regras Verizon Premium
description: O motor de regras permite-lhe personalizar a forma como os pedidos de HTTP são tratados pelo Azure CDN da Verizon Premium, tais como bloquear a entrega de certos tipos de conteúdo, definir uma política de cache e modificar os cabeçalhos HTTP.
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: article
ms.date: 05/31/2019
ms.author: allensu
ms.openlocfilehash: 2e5e4265f30631f8e68d8f9e7156ea578ae85e7a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81253480"
---
# <a name="override-http-behavior-using-the-azure-cdn-from-verizon-premium-rules-engine"></a>Sobrepor o comportamento http usando o CDN Azure do motor de regras Verizon Premium

[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Descrição geral

O motor de regras do CDN Azure permite-lhe personalizar a forma como os pedidos http são tratados. Por exemplo, bloquear a entrega de certos tipos de conteúdo, definir uma política de cache ou modificar um cabeçalho HTTP. Este tutorial demonstra como criar uma regra que altera o comportamento de cache dos ativos da CDN. Para obter mais informações sobre as regras da sintaxe do motor, consulte a [referência do motor de regras do Azure CDN](cdn-verizon-premium-rules-engine-reference.md).

## <a name="access"></a>Acesso

Para aceder ao motor de regras, tem primeiro de selecionar **Gerir** a partir do topo da página de **perfil CDN** para aceder à página de gestão do CDN Azure. Dependendo se o seu ponto final está otimizado para a aceleração dinâmica do site (DSA), aceda ao motor de regras com o conjunto de regras apropriadas para o seu tipo de ponto final:

- Pontos finais otimizados para entrega geral web ou outra otimização não-DSA:
    
    Selecione o separador **HTTP Large** e, em seguida, selecione **O Motor de Regras**.

    ![Regras motor para HTTP](./media/cdn-rules-engine/cdn-http-rules-engine.png)

- Pontos finais otimizados para AD:
    
    Selecione o separador **ADN** e, em seguida, selecione **O Motor de Regras**.
    
    ADN é um termo usado pela Verizon para especificar o conteúdo da DSA. Quaisquer regras que crie aqui são ignoradas por quaisquer pontos finais do seu perfil que não estejam otimizados para a DSA.

    ![Motor de regras para DSA](./media/cdn-rules-engine/cdn-dsa-rules-engine.png)

## <a name="tutorial"></a>Tutorial

1. A partir da página de **perfil cdN,** selecione **Gerir**.
   
    ![Perfil CDN Gerir botão](./media/cdn-rules-engine/cdn-manage-btn.png)
   
    Abre o portal de gestão da CDN.

2. Selecione o separador **HTTP Large** e, em seguida, selecione **O Motor de Regras**.
   
    As opções para uma nova regra são apresentadas.
   
    ![Novas opções de regras da CDN](./media/cdn-rules-engine/cdn-new-rule.png)
   
   > [!IMPORTANT]
   > A ordem em que várias regras são listadas afeta a forma como são tratadas. Uma regra subsequente pode anular as ações especificadas por uma regra anterior.
   >

3. Introduza um nome no **nome / Descrição** da caixa de texto.

4. Identifique o tipo de pedidos a que a regra se aplica. Utilize a condição de correspondência predefinida, **sempre**.
   
   ![Condição de correspondência da regra cdn](./media/cdn-rules-engine/cdn-request-type.png)
   
   > [!NOTE]
   > Várias condições de jogo estão disponíveis na lista de dropdown. Para obter informações sobre a condição de jogo atualmente selecionada, selecione o ícone informativo azul à sua esquerda.
   >
   >  Para obter uma lista detalhada de expressões condicionais, consulte [as expressões condicionais](cdn-verizon-premium-rules-engine-reference-match-conditions.md)do motor.
   >  
   > Para obter uma lista detalhada das condições de jogo, consulte Regras de [condições](cdn-verizon-premium-rules-engine-reference-match-conditions.md)de correspondência do motor .
   >
   >

5. Para adicionar uma nova **+** funcionalidade, selecione o botão ao lado das **Funcionalidades**.  Na descida à esquerda, selecione **Force Internal Max-Age**.  Na caixa de texto que aparece, introduza **300**. Não altere os restantes valores predefinidos.
   
   ![Recurso de regra CDN](./media/cdn-rules-engine/cdn-new-feature.png)
   
   > [!NOTE]
   > Várias funcionalidades estão disponíveis na lista de dropdown. Para obter informações sobre a funcionalidade atualmente selecionada, selecione o ícone informativo azul à sua esquerda.
   >
   > Para a **Força Internal Max-Age,** os ativos `Cache-Control` e `Expires` os cabeçalhos são sobressados para controlar quando o nó de borda CDN refresca o ativo a partir da origem. Neste exemplo, o nó de borda CDN caches o ativo durante 300 segundos, ou 5 minutos, antes de refrescar o ativo a partir da sua origem.
   >
   > Para obter uma lista detalhada de funcionalidades, consulte as [funcionalidades](cdn-verizon-premium-rules-engine-reference-features.md)do motor Rules .
   >
   >

6. Selecione **Adicionar** para salvar a nova regra.  A nova regra aguarda agora aprovação. Depois de aprovado, o estado muda de **Pendente XML** para **Ative XML**.
   
   > [!IMPORTANT]
   > As alterações de regras podem demorar até 10 minutos a propagar-se através do Azure CDN.
   >
   >

## <a name="see-also"></a>Consulte também

- [Visão geral do CDN azure](cdn-overview.md)
- [Referência do motor de regras](cdn-verizon-premium-rules-engine-reference.md)
- [Condições de correspondência do motor de regras](cdn-verizon-premium-rules-engine-reference-match-conditions.md)
- [Expressões condicionais do motor de regras](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [Funcionalidades do motor de regras](cdn-verizon-premium-rules-engine-reference-features.md)
- [Azure Fridays: As poderosas novas funcionalidades premium da Azure CDN](https://azure.microsoft.com/documentation/videos/azure-cdns-powerful-new-premium-features/) (vídeo)