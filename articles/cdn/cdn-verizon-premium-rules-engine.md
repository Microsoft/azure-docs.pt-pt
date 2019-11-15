---
title: Substituir comportamento HTTP com a CDN do Azure-mecanismo de regras Premium da Verizon
description: O mecanismo de regras permite que você personalize como as solicitações HTTP são tratadas pela CDN do Azure da Verizon Premium, como o bloqueio da entrega de determinados tipos de conteúdo, a definição de uma política de cache e a modificação de cabeçalhos HTTP.
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 05/31/2019
ms.author: magattus
ms.openlocfilehash: aa0606eafb8fe4c517b0c18e0137058a120115ba
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74082960"
---
# <a name="override-http-behavior-using-the-azure-cdn-from-verizon-premium-rules-engine"></a>Substituir o comportamento HTTP usando o mecanismo de regras da CDN do Azure da Verizon Premium

[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Descrição geral

O mecanismo de regras da CDN do Azure permite que você personalize como as solicitações HTTP são manipuladas. Por exemplo, bloquear a entrega de determinados tipos de conteúdo, definir uma política de cache ou modificar um cabeçalho HTTP. Este tutorial demonstra como criar uma regra que altera o comportamento de cache dos ativos da CDN. Para obter mais informações sobre a sintaxe do mecanismo de regras, consulte [referência do mecanismo de regras da CDN do Azure](cdn-verizon-premium-rules-engine-reference.md).

## <a name="access"></a>Acesso

Para acessar o mecanismo de regras, primeiro você deve selecionar **gerenciar** na parte superior da página de **perfil CDN** para acessar a página de gerenciamento da CDN do Azure. Dependendo se o ponto de extremidade é otimizado para a aceleração de site dinâmico (DSA), você acessa o mecanismo de regras com o conjunto de regras apropriado para o tipo de ponto de extremidade:

- Pontos de extremidade otimizados para entrega da Web geral ou outra otimização não-DSA:
    
    Selecione a guia **http grande** e, em seguida, selecione **mecanismo de regras**.

    ![Mecanismo de regras para HTTP](./media/cdn-rules-engine/cdn-http-rules-engine.png)

- Pontos de extremidade otimizados para DSA:
    
    Selecione a guia **ADN** e, em seguida, selecione **mecanismo de regras**.
    
    ADN é um termo usado pela Verizon para especificar o conteúdo de DSA. Todas as regras que você criar aqui serão ignoradas por quaisquer pontos de extremidade em seu perfil que não são otimizadas para o DSA.

    ![Mecanismo de regras para DSA](./media/cdn-rules-engine/cdn-dsa-rules-engine.png)

## <a name="tutorial"></a>Tutorial

1. Na página **perfil CDN** , selecione **gerenciar**.
   
    ![Botão Gerenciar perfil CDN](./media/cdn-rules-engine/cdn-manage-btn.png)
   
    O portal de gerenciamento da CDN é aberto.

2. Selecione a guia **http grande** e, em seguida, selecione **mecanismo de regras**.
   
    As opções para uma nova regra são exibidas.
   
    ![Opções da nova regra da CDN](./media/cdn-rules-engine/cdn-new-rule.png)
   
   > [!IMPORTANT]
   > A ordem na qual várias regras são listadas afeta como elas são tratadas. Uma regra subsequente pode substituir as ações especificadas por uma regra anterior.
   >

3. Insira um nome na caixa de texto **nome/descrição** .

4. Identifique o tipo de solicitações às quais a regra se aplica. Use a condição de correspondência padrão, **sempre**.
   
   ![Condição de correspondência de regra CDN](./media/cdn-rules-engine/cdn-request-type.png)
   
   > [!NOTE]
   > Várias condições de correspondência estão disponíveis na lista suspensa. Para obter informações sobre a condição de correspondência selecionada no momento, selecione o ícone informativo azul à esquerda.
   >
   >  Para obter uma lista detalhada de expressões condicionais, consulte [expressões condicionais do mecanismo de regras](cdn-verizon-premium-rules-engine-reference-match-conditions.md).
   >  
   > Para obter uma lista detalhada das condições de correspondência, consulte [condições de correspondência do mecanismo de regras](cdn-verizon-premium-rules-engine-reference-match-conditions.md).
   >
   >

5. Para adicionar um novo recurso, selecione o botão de **+** ao lado de **recursos**.  Na lista suspensa à esquerda, selecione **forçar Max-age interna**.  Na caixa de texto exibida, insira **300**. Não altere os valores padrão restantes.
   
   ![Recurso de regra CDN](./media/cdn-rules-engine/cdn-new-feature.png)
   
   > [!NOTE]
   > Vários recursos estão disponíveis na lista suspensa. Para obter informações sobre o recurso selecionado no momento, selecione o ícone informativo azul à esquerda.
   >
   > Para o **Max-age interno forçado**, os cabeçalhos de `Expires` e de `Cache-Control` do ativo são substituídos para controlar quando o nó de borda da CDN atualiza o ativo da origem. Neste exemplo, o nó de borda da CDN armazena em cache o ativo por 300 segundos ou 5 minutos antes de atualizar o ativo de sua origem.
   >
   > Para obter uma lista detalhada de recursos, consulte [recursos do mecanismo de regras](cdn-verizon-premium-rules-engine-reference-features.md).
   >
   >

6. Selecione **Adicionar** para salvar a nova regra.  A nova regra agora está aguardando aprovação. Depois de aprovado, o status muda de **XML pendente** para **XML ativo**.
   
   > [!IMPORTANT]
   > As alterações de regras podem levar até 10 minutos para serem propagadas por meio da CDN do Azure.
   >
   >

## <a name="see-also"></a>Consulte também

- [Visão geral da CDN do Azure](cdn-overview.md)
- [Referência do motor de regras](cdn-verizon-premium-rules-engine-reference.md)
- [Condições de correspondência do motor de regras](cdn-verizon-premium-rules-engine-reference-match-conditions.md)
- [Expressões condicionais do motor de regras](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [Funcionalidades do motor de regras](cdn-verizon-premium-rules-engine-reference-features.md)
- [Sexta-feira do Azure: novos recursos premium avançados da CDN do Azure](https://azure.microsoft.com/documentation/videos/azure-cdns-powerful-new-premium-features/) (vídeo)