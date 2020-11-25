---
title: Controle O comportamento de caching do Azure CDN com cordas de consulta - nível premium
description: A azure CDN consulta caching de cadeia controla como os ficheiros são cached quando um pedido web contém uma cadeia de consulta. Este artigo descreve a caching de cordas de consulta no Azure CDN Premium do produto Verizon.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: 99db4a85-4f5f-431f-ac3a-69e05518c997
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 06/11/2018
ms.author: allensu
ms.openlocfilehash: a799309b6e5d00db3b6c206187eec7097c9dc11a
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96018601"
---
# <a name="control-azure-cdn-caching-behavior-with-query-strings---premium-tier"></a>Controle O comportamento de caching do Azure CDN com cordas de consulta - nível premium
> [!div class="op_single_selector"]
> * [Escalão standard](cdn-query-string.md)
> * [Escalão Premium](cdn-query-string-premium.md)
> 

## <a name="overview"></a>Descrição geral
Com a Rede de Entrega de Conteúdos Azure (CDN), pode controlar como os ficheiros são cached para um pedido web que contém uma cadeia de consulta. Num pedido web com uma cadeia de consulta, a cadeia de consulta é essa parte do pedido que ocorre após um ponto de interrogação (?). Uma cadeia de consulta pode conter um ou mais pares de valor-chave, nos quais o nome do campo e o seu valor são separados por um sinal igual (=). Cada par de valores-chave é separado por um ampersand (&). Por exemplo, http: \/ /www.contoso.com/content.mov?field1=value1&field2=value2. Se houver mais de um par de valores-chave numa sequência de consulta de um pedido, a sua encomenda não importa. 

> [!IMPORTANT]
> Os produtos CDN standard e premium fornecem a mesma funcionalidade de caching de cadeia de consulta, mas a interface do utilizador é diferente. Este artigo descreve a interface para **Azure CDN Premium da Verizon**. Para consultar o caching de cordas com produtos padrão Azure CDN, consulte [o comportamento de caching do Control Azure CDN com cordas de consulta - nível padrão](cdn-query-string.md).
>


Estão disponíveis três modos de cadeia de consulta:

- **cache padrão**: Modo padrão. Neste modo, o nó ponto de presença (POP) do CDN passa as cadeias de consulta do solicitador para o servidor de origem no primeiro pedido e caches do ativo. Todos os pedidos subsequentes para o ativo que são servidos a partir do servidor POP ignoram as cadeias de consulta até que o ativo em cache expire.

    >[!IMPORTANT] 
    > Se a autorização de token estiver ativada para qualquer caminho nesta conta, o modo cache padrão é o único modo que pode ser utilizado. 

- **no-cache**: Neste modo, os pedidos com cordas de consulta não estão em cache no nó POP CDN. O nó POP recupera o ativo diretamente do servidor de origem e passa-o para o solicitador a cada pedido.

- **cache único**: Neste modo, cada pedido com um URL único, incluindo a cadeia de consulta, é tratado como um ativo único com a sua própria cache. Por exemplo, a resposta do servidor de origem para um pedido por exemplo.ashx?q=test1 é cached no nó POP e devolvida para caches subsequentes com a mesma cadeia de consulta. Um pedido, por exemplo,ashx?q=test2 é cached como um ativo separado com a sua própria definição de tempo-a-vida.
   
    >[!IMPORTANT] 
    > Não utilize este modo quando a cadeia de consulta contém parâmetros que mudarão a cada pedido, como um ID de sessão ou um nome de utilizador, pois resultará numa relação de cache baixa.

## <a name="changing-query-string-caching-settings-for-premium-cdn-profiles"></a>Alteração das definições de caching de cordas de consulta para perfis cdn premium
1. Abrir um perfil CDN e, em seguida, clicar em **Gerir**.
   
    ![Botão de gestão de perfil CDN](./media/cdn-query-string-premium/cdn-manage-btn.png)
   
    O portal de gestão cdn abre.
2. Passe por cima do separador **HTTP Large** e, em seguida, paire sobre o menu de voo das **Definições cache.** Clique **em Caching de cadeia de consulta.**
   
    São apresentadas opções de caching de cordas de consulta.
   
    ![Opções de caching de cordas de consulta CDN](./media/cdn-query-string-premium/cdn-query-string.png)
3. Selecione um modo de cadeia de consulta e, em seguida, clique em **Update**.

> [!IMPORTANT]
> Como o registo leva tempo para que o registo se propague através do CDN, as alterações nas definições das cordas de cache podem não ser imediatamente visíveis. A propagação geralmente termina em 10 minutos.
 

