---
title: Controle O comportamento de cache do Azure CDN com cordas de consulta - nível padrão
description: O cache de cordas de consulta Azure CDN controla a forma como os ficheiros são cacheed quando um pedido web contém uma corda de consulta. Este artigo descreve o cache de cordas de consulta em produtos padrão Azure CDN.
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
ms.topic: article
ms.date: 06/11/2018
ms.author: allensu
ms.openlocfilehash: a0df9cecc4ccd09db3f6b07fa6fd4c5283753aa2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81260212"
---
# <a name="control-azure-cdn-caching-behavior-with-query-strings---standard-tier"></a>Controle O comportamento de cache do Azure CDN com cordas de consulta - nível padrão
> [!div class="op_single_selector"]
> * [Escalão Standard](cdn-query-string.md)
> * [Escalão Premium](cdn-query-string-premium.md)
> 

## <a name="overview"></a>Descrição geral
Com a Rede de Entrega de ConteúdoS Azure (CDN), pode controlar como os ficheiros são cached para um pedido web que contém uma cadeia de consulta. Num pedido web com uma corda de consulta, a corda de consulta é essa parte do pedido que ocorre após um ponto de interrogação (?). Uma corda de consulta pode conter um ou mais pares de valor-chave, em que o nome de campo e o seu valor são separados por um sinal igual (=). Cada par de valor-chave é separado por um ampersand (&). Por exemplo,\/http: /www.contoso.com/content.mov?field1=value1&campo2=valor2. Se houver mais de um par de valor-chave numa sequência de consulta de um pedido, a sua ordem não importa. 

> [!IMPORTANT]
> Os produtos standard e premium Azure CDN fornecem a mesma funcionalidade de cache de cordas de consulta, mas a interface do utilizador é diferente. Este artigo descreve a interface para **o Azure CDN Standard da Microsoft,** **Azure CDN Standard da Akamai** e **Azure CDN Standard da Verizon**. Para consulta de cordas com **Azure CDN Premium de Verizon,** consulte [Control Azure CDN comportamento de cache com cordas](cdn-query-string-premium.md)de consulta - nível premium .

Estão disponíveis três modos de corda de consulta:

- **Ignore as cordas de consulta**: Modo padrão. Neste modo, o nó de ponto de presença CDN (POP) passa as cordas de consulta do solicitador para o servidor de origem no primeiro pedido e caches o ativo. Todos os pedidos subsequentes para o ativo que são servidos pelo POP ignoram as cordas de consulta até que o ativo em cache expire.

- **Bypass caching para cordas de consulta**: Neste modo, os pedidos com cordas de consulta não são cached no nó Pop CDN. O nó POP recupera o ativo diretamente do servidor de origem e passa-o ao solicitador com cada pedido.

- **Cache cada URL único**: Neste modo, cada pedido com um URL único, incluindo a corda de consulta, é tratado como um ativo único com a sua própria cache. Por exemplo, a resposta do servidor de origem para um pedido por exemplo.ashx?q=test1 é emcache no nó POP e devolvida para caches subsequentes com a mesma corda de consulta. Um pedido, por exemplo.ashx?q=test2 é cached como um ativo separado com a sua própria configuração de tempo para viver.
   
    >[!IMPORTANT] 
    > Não utilize este modo quando a cadeia de consulta contiver parâmetros que mudarão a cada pedido, como um ID de sessão ou um nome de utilizador, porque resultará numa relação de cache-hit baixa.

## <a name="changing-query-string-caching-settings-for-standard-cdn-profiles"></a>Alterar as definições de cache de cordas de consulta para perfis padrão de CDN
1. Abra um perfil CDN e, em seguida, selecione o ponto final do CDN que pretende gerir.
   
   ![Pontos finais do perfil da CDN](./media/cdn-query-string/cdn-endpoints.png)
   
2. No painel esquerdo em Definições, clique em **regras de caching**.
   
    ![Botão Regras de colocação em cache da CDN](./media/cdn-query-string/cdn-caching-rules-btn.png)
   
3. Na lista de comportamento de cache de **cordas de consulta,** selecione um modo de corda de consulta e, em seguida, clique em **Guardar**.
   
   ![Opções de cache de corda de consulta CDN](./media/cdn-query-string/cdn-query-string.png)

> [!IMPORTANT]
> Uma vez que o registo demora algum tempo a propagar-se através do Azure CDN, as alterações das definições de cadeias de cache podem não ser imediatamente visíveis:
> - Para os perfis **CDN do Azure Standard da Microsoft**, a propagação normalmente fica concluída em 10 minutos. 
> - Para os perfis **CDN do Azure Standard da Akamai**, a propagação normalmente fica concluída num minuto. 
> - Para os perfis **CDN do Azure Standard da Verizon** e **CDN do Azure Premium da Verizon**, a propagação normalmente fica concluída em 10 minutos. 



