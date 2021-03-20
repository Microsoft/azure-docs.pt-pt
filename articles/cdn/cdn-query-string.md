---
title: Controle O comportamento de caching do Azure CDN com cordas de consulta - nível padrão
description: A azure CDN consulta caching de cadeia controla como os ficheiros são cached quando um pedido web contém uma cadeia de consulta. Este artigo descreve a caching de cordas de consulta em produtos padrão Azure CDN.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: 17410e4f-130e-489c-834e-7ca6d6f9778d
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 06/11/2018
ms.author: allensu
ms.openlocfilehash: 1521d08ef9d431bbe8b3fd3a578297d440ed56b3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96018584"
---
# <a name="control-azure-cdn-caching-behavior-with-query-strings---standard-tier"></a>Controle O comportamento de caching do Azure CDN com cordas de consulta - nível padrão
> [!div class="op_single_selector"]
> * [Escalão standard](cdn-query-string.md)
> * [Escalão Premium](cdn-query-string-premium.md)
> 

## <a name="overview"></a>Descrição geral
Com a Rede de Entrega de Conteúdos Azure (CDN), pode controlar como os ficheiros são cached para um pedido web que contém uma cadeia de consulta. Num pedido web com uma cadeia de consulta, a cadeia de consulta é essa parte do pedido que ocorre após um ponto de interrogação (?). Uma cadeia de consulta pode conter um ou mais pares de valor-chave, nos quais o nome do campo e o seu valor são separados por um sinal igual (=). Cada par de valores-chave é separado por um ampersand (&). Por exemplo, http: \/ /www.contoso.com/content.mov?field1=value1&field2=value2. Se houver mais de um par de valores-chave numa sequência de consulta de um pedido, a sua encomenda não importa. 

> [!IMPORTANT]
> Os produtos standard e premium Azure CDN fornecem a mesma funcionalidade de caching de cordas de consulta, mas a interface do utilizador é diferente. Este artigo descreve a interface para **Azure CDN Standard da Microsoft**, **Azure CDN Standard da Akamai** e **Azure CDN Standard da Verizon**. Para consulta de caching de cordas com **Azure CDN Premium de Verizon,** consulte [o comportamento de caching do Control Azure CDN com cordas de consulta - nível premium](cdn-query-string-premium.md).

Estão disponíveis três modos de cadeia de consulta:

- **Ignore as cadeias de consulta**: Modo padrão. Neste modo, o nó ponto de presença (POP) do CDN passa as cadeias de consulta do solicitador para o servidor de origem no primeiro pedido e caches do ativo. Todos os pedidos subsequentes para o ativo que são servidos do POP ignoram as cordas de consulta até que o ativo em cache expire.

- **Bypass de cache para as cordas de consulta**: Neste modo, os pedidos com cordas de consulta não estão em cache no nó POP CDN. O nó POP recupera o ativo diretamente do servidor de origem e passa-o para o solicitador a cada pedido.

- **Cache cada URL único**: Neste modo, cada pedido com um URL único, incluindo a cadeia de consulta, é tratado como um ativo único com a sua própria cache. Por exemplo, a resposta do servidor de origem para um pedido por exemplo.ashx?q=test1 é cached no nó POP e devolvida para caches subsequentes com a mesma cadeia de consulta. Um pedido, por exemplo,ashx?q=test2 é cached como um ativo separado com a sua própria definição de tempo-a-vida.
   
    >[!IMPORTANT] 
    > Não utilize este modo quando a cadeia de consulta contém parâmetros que mudarão a cada pedido, como um ID de sessão ou um nome de utilizador, pois resultará numa relação de cache baixa.

## <a name="changing-query-string-caching-settings-for-standard-cdn-profiles"></a>Alteração das definições de caching de cordas de consulta para perfis padrão de CDN
1. Abra um perfil CDN e, em seguida, selecione o ponto final do CDN que pretende gerir.
   
   ![Pontos finais de perfil cdn](./media/cdn-query-string/cdn-endpoints.png)
   
2. No painel esquerdo em Definições, clique nas **regras de Caching**.
   
    ![Botão Regras de colocação em cache da CDN](./media/cdn-query-string/cdn-caching-rules-btn.png)
   
3. Na lista **de comportamento de caching de cordas de consulta,** selecione um modo de cadeia de consulta e, em seguida, clique em **Guardar**.
   
   ![Opções de caching de cordas de consulta CDN](./media/cdn-query-string/cdn-query-string.png)

> [!IMPORTANT]
> Como o registo leva tempo para que o registo se propague através do Azure CDN, as alterações nas definições das cordas de cache podem não ser imediatamente visíveis:
> - Para os perfis **CDN do Azure Standard da Microsoft**, a propagação normalmente fica concluída em 10 minutos. 
> - Para os perfis **CDN do Azure Standard da Akamai**, a propagação normalmente fica concluída num minuto. 
> - Para os perfis **CDN do Azure Standard da Verizon** e **CDN do Azure Premium da Verizon**, a propagação normalmente fica concluída em 10 minutos. 



