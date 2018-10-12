---
title: Substituir comportamento HTTP utilizando o mecanismo de regras do CDN do Azure | Documentos da Microsoft
description: O mecanismo de regras permite-lhe personalizar como os pedidos de HTTP são processados pela CDN do Azure, tais como bloquear a entrega de determinados tipos de conteúdo, definir uma política de colocação em cache e modificar os cabeçalhos HTTP.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: 625a912b-91f2-485d-8991-128cc194ee71
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/11/2018
ms.author: magattus
ms.openlocfilehash: 2ac43b472758f3403bc87bf3d64321eb97109f53
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2018
ms.locfileid: "49092403"
---
# <a name="override-http-behavior-using-the-azure-cdn-rules-engine"></a>Substituir comportamento HTTP utilizando o mecanismo de regras do CDN do Azure
[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Descrição geral
O mecanismo de regras do CDN do Azure permite-lhe personalizar a forma como os pedidos de HTTP são processados. Por exemplo, bloquear a entrega de certos tipos de conteúdo, definir uma política de colocação em cache ou modificando um cabeçalho HTTP. Este tutorial demonstra como criar uma regra que altera o comportamento de colocação em cache de ativos CDN. Para obter mais informações sobre a sintaxe do mecanismo de regras, consulte [referência do motor de regras de CDN do Azure](cdn-rules-engine-reference.md).

## <a name="access"></a>Access
Para acessar o mecanismo de regras, tem de selecionar primeiro **Manage** na parte superior do **perfil da CDN** página para acessar a página de gestão da CDN do Azure. Dependendo se o ponto final está otimizado para a aceleração de sites dinâmicos (DSA), em seguida, acessar o mecanismo de regras com o conjunto de regras apropriados para o seu tipo de ponto de extremidade:

- Pontos finais otimizados para entrega geral web ou outra otimização não DSA: 
    
    Selecione o **HTTP grandes** separador, em seguida, selecione **motor de regras**.

    ![Motor de regras para HTTP](./media/cdn-rules-engine/cdn-http-rules-engine.png)

- Pontos finais otimizados para o DSA: 
    
    Selecione o **ADN** separador, em seguida, selecione **motor de regras**. 
    
    ADN é um termo utilizado pelo Verizon para especificar o conteúdo DSA. Quaisquer regras que criar aqui são ignoradas pelos pontos finais no seu perfil que não estão otimizados para o DSA. 

    ![Motor de regras para o DSA](./media/cdn-rules-engine/cdn-dsa-rules-engine.png)

## <a name="tutorial"></a>Tutorial
1. Partir do **perfil da CDN** página, selecione **gerir**.
   
    ![Botão de gerir do perfil CDN](./media/cdn-rules-engine/cdn-manage-btn.png)
   
    É aberto o portal de gestão da CDN.

2. Selecione o **HTTP grandes** separador, em seguida, selecione **motor de regras**.
   
    São apresentadas as opções para uma nova regra.
   
    ![Novas opções de regra CDN](./media/cdn-rules-engine/cdn-new-rule.png)
   
   > [!IMPORTANT]
   > A ordem em que várias regras estão listadas afeta a forma como são processadas. Uma regra subsequente pode substituir as ações especificadas por uma regra anterior.
   > 

3. Introduza um nome na **nome / descrição** caixa de texto.

4. Identifica o tipo de pedidos que se aplica a regra. Utilizar a condição de correspondência de padrão **sempre**. 
   
   ![Condição de correspondência de regra CDN](./media/cdn-rules-engine/cdn-request-type.png)
   
   > [!NOTE]
   > Existem várias condições de correspondência na lista pendente. Para obter informações sobre a condição de correspondência atualmente selecionado, selecione o ícone de informativo azul à sua esquerda.
   > 
   >  Para obter uma lista detalhada das expressões condicionais, consulte [expressões condicionais do motor de regras](cdn-rules-engine-reference-match-conditions.md).
   >  
   > Para obter uma lista detalhada das condições de correspondência de mensagens em fila, consulte [condições de correspondência do motor de regras](cdn-rules-engine-reference-match-conditions.md).
   > 
   > 

5. Para adicionar uma nova funcionalidade, selecione o **+** junto a **funcionalidades**.  Na área suspensa à esquerda, selecione **força interno Max-Age**.  Na caixa de texto que aparece, introduza **300**. Não altere os valores predefinidos restantes.
   
   ![Recurso de regra CDN](./media/cdn-rules-engine/cdn-new-feature.png)
   
   > [!NOTE]
   > Várias funcionalidades estão disponíveis na lista pendente. Para obter informações sobre a funcionalidade selecionada atualmente, selecione o ícone de informativo azul à sua esquerda. 
   >
   > Para **força interno Max-Age**, o elemento `Cache-Control` e `Expires` cabeçalhos são substituídos para controlar quando o nó de extremidade CDN atualiza o recurso a partir da origem. Neste exemplo, o nó de extremidade da CDN armazena em cache o recurso para 300 segundos ou 5 minutos, antes de ele atualiza o recurso da sua origem.
   > 
   > Para obter uma lista detalhada das funcionalidades, consulte [funcionalidades do motor de regras](cdn-rules-engine-reference-features.md).
   > 
   > 

6. Selecione **adicionar** para guardar a nova regra.  A nova regra agora está aguardando aprovação. Depois que ele for aprovado, o estado é alterado de **XML pendente** ao **Active XML**.
   
   > [!IMPORTANT]
   > Alterações de regras podem demorar até 10 minutos para propagar através da CDN do Azure.
   > 
   > 

## <a name="see-also"></a>Consulte também
* [Descrição geral da CDN do Azure](cdn-overview.md)
* [Referência do motor de regras](cdn-rules-engine-reference.md)
* [Condições de correspondência do motor de regras](cdn-rules-engine-reference-match-conditions.md)
* [Expressões condicionais do motor de regras](cdn-rules-engine-reference-conditional-expressions.md)
* [Funcionalidades do motor de regras](cdn-rules-engine-reference-features.md)
* [Azure Fridays: Poderosas novas funcionalidades premium da CDN do Azure](https://azure.microsoft.com/documentation/videos/azure-cdns-powerful-new-premium-features/) (vídeo)