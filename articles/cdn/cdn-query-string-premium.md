---
title: Controle O comportamento de cache do Azure CDN com cordas de consulta - nível premium
description: O cache de cordas de consulta Azure CDN controla a forma como os ficheiros são cacheed quando um pedido web contém uma corda de consulta. Este artigo descreve o cache de cordas de consulta no Azure CDN Premium do produto Verizon.
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
ms.topic: article
ms.date: 06/11/2018
ms.author: allensu
ms.openlocfilehash: 94949a31db5321929a3440281cebd01712c79bb8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81260144"
---
# <a name="control-azure-cdn-caching-behavior-with-query-strings---premium-tier"></a>Controle O comportamento de cache do Azure CDN com cordas de consulta - nível premium
> [!div class="op_single_selector"]
> * [Escalão Standard](cdn-query-string.md)
> * [Escalão Premium](cdn-query-string-premium.md)
> 

## <a name="overview"></a>Descrição geral
Com a Rede de Entrega de ConteúdoS Azure (CDN), pode controlar como os ficheiros são cached para um pedido web que contém uma cadeia de consulta. Num pedido web com uma corda de consulta, a corda de consulta é essa parte do pedido que ocorre após um ponto de interrogação (?). Uma corda de consulta pode conter um ou mais pares de valor-chave, em que o nome de campo e o seu valor são separados por um sinal igual (=). Cada par de valor-chave é separado por um ampersand (&). Por exemplo,\/http: /www.contoso.com/content.mov?field1=value1&campo2=valor2. Se houver mais de um par de valor-chave numa sequência de consulta de um pedido, a sua ordem não importa. 

> [!IMPORTANT]
> Os produtos CDN standard e premium fornecem a mesma funcionalidade de cache de corda de consulta, mas a interface do utilizador é diferente. Este artigo descreve a interface para **Azure CDN Premium da Verizon**. Para consulta de cordas com produtos padrão Azure CDN, consulte [Control Azure CDN comportamento](cdn-query-string.md)de cache com cordas de consulta - nível padrão .
>


Estão disponíveis três modos de corda de consulta:

- **cache padrão**: Modo predefinido. Neste modo, o nó de ponto de presença CDN (POP) passa as cordas de consulta do solicitador para o servidor de origem no primeiro pedido e caches o ativo. Todos os pedidos subsequentes para o ativo que são servidos a partir do servidor POP ignoram as cordas de consulta até que o ativo em cache expire.

    >[!IMPORTANT] 
    > Se a autorização de ficha estiver ativada para qualquer caminho nesta conta, o modo de cache padrão é o único modo que pode ser utilizado. 

- **no-cache**: Neste modo, os pedidos com cordas de consulta não são cached no nó Pop CDN. O nó POP recupera o ativo diretamente do servidor de origem e passa-o ao solicitador com cada pedido.

- **cache único**: Neste modo, cada pedido com um URL único, incluindo a corda de consulta, é tratado como um ativo único com a sua própria cache. Por exemplo, a resposta do servidor de origem para um pedido por exemplo.ashx?q=test1 é emcache no nó POP e devolvida para caches subsequentes com a mesma corda de consulta. Um pedido, por exemplo.ashx?q=test2 é cached como um ativo separado com a sua própria configuração de tempo para viver.
   
    >[!IMPORTANT] 
    > Não utilize este modo quando a cadeia de consulta contiver parâmetros que mudarão a cada pedido, como um ID de sessão ou um nome de utilizador, porque resultará numa relação de cache-hit baixa.

## <a name="changing-query-string-caching-settings-for-premium-cdn-profiles"></a>Alterar as definições de cache de cordas de consulta para perfis CDN premium
1. Abra um perfil CDN e, em seguida, clique em **Gerir**.
   
    ![Perfil CDN Gerir botão](./media/cdn-query-string-premium/cdn-manage-btn.png)
   
    Abre o portal de gestão da CDN.
2. Paire sobre o separador **HTTP Grande** e, em seguida, paire sobre o menu de flyout **De Definições cache.** Clique em **Caching De Cordas de Consulta**.
   
    São apresentadas opções de cache de corda de consulta.
   
    ![Opções de cache de corda de consulta CDN](./media/cdn-query-string-premium/cdn-query-string.png)
3. Selecione um modo de corda de consulta e, em seguida, clique em **Atualizar**.

> [!IMPORTANT]
> Uma vez que o registo demora tempo a propagar-se através do CDN, as alterações das definições de cadeias de cache podem não ser imediatamente visíveis. A propagação geralmente completa em 10 minutos.
 

