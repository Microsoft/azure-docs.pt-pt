---
title: Porta da Frente Azure - URL Reescrever Microsoft Docs
description: Este artigo ajuda-o a entender como a Porta Frontal Azure escreve url para as suas rotas, se configurado.
services: front-door
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: 1e5bd565be7a1cabf08ddf33c65eb12b5294249f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79471477"
---
# <a name="url-rewrite-custom-forwarding-path"></a>Reescrita de URL (caminho de encaminhamento personalizado)
A Porta Frontal Azure suporta a reescrita do URL, permitindo-lhe configurar um caminho opcional de **encaminhamento personalizado** para utilizar ao construir o pedido para encaminhar para o backend. Por predefinição, se não for fornecido nenhum caminho de reencaminhamento, o Front Door copiará o caminho do URL de entrada para o URL utilizado no pedido reencaminhado. O cabeçalho Host (Anfitrião) utilizado no pedido reencaminhado é igual ao configurado para o back-end selecionado. Leia o Cabeçalho do [Anfitrião backend](front-door-backend-pool.md#hostheader) para saber o que faz e como pode configurá-lo.

A parte poderosa da reescrita de URL usando o caminho de encaminhamento personalizado é que irá copiar qualquer parte do caminho que se aproxima de um caminho wildcard para o caminho encaminhado (estes segmentos de caminho são os segmentos **verdes** no exemplo abaixo):
</br>
![URL da porta da frente azure Reescrever][1]

## <a name="url-rewrite-example"></a>Url reescrever exemplo
Considere uma regra de encaminhamento com os seguintes anfitriões frontendas e caminhos configurados:

| Anfitriões      | Caminhos       |
|------------|-------------|
| www\.contoso.com | /\*         |
|            | /foo        |
|            | /foo/\*     |
|            | /foo/bar/\* |

A primeira coluna da tabela abaixo apresenta exemplos de pedidos de entrada e a segunda coluna mostra qual seria a rota "mais específica" que combina "Caminho".  As terceirae seguintes colunas da primeira linha da tabela são exemplos de caminhos de **encaminhamento personalizado**configurados, com o resto das linhas nessas colunas representando exemplos do que seria o caminho de pedido encaminhada se correspondesse ao pedido nessa linha.

Por exemplo, se lermos através da segunda linha, está `www.contoso.com/sub`a dizer que, `/`para o pedido de `/sub`entrada , se o caminho de encaminhamento personalizado fosse , então o caminho encaminhado seria . Se o caminho de `/fwd/`encaminhamento personalizado fosse `/fwd/sub`, então o caminho encaminhado seria . E assim por diante, para as restantes colunas. As partes **enfatizadas** dos caminhos abaixo representam as porções que fazem parte da partida wildcard.


| Pedido de entrada       | Caminho de jogo mais específico | /          | /fwd/          | /foo/          | /foo/bar/          |
|------------------------|--------------------------|------------|----------------|----------------|--------------------|
| www\.contoso.com/            | /\*                      | /          | /fwd/          | /foo/          | /foo/bar/          |
| www\.contoso.com/**sub**     | /\*                      | /**sub**   | /fwd/**sub**   | /foo/**sub**   | /foo/bar/**sub**   |
| www\.contoso.com/**a/b/c**   | /\*                      | /**a/b/c** | /fwd/**a/b/c** | /foo/**a/b/c** | /foo/bar/**a/b/c** |
| www\.contoso.com/foo         | /foo                     | /          | /fwd/          | /foo/          | /foo/bar/          |
| www\.contoso.com/foo/        | /foo/\*                  | /          | /fwd/          | /foo/          | /foo/bar/          |
| www\.contoso.com/foo/**bar** | /foo/\*                  | /**bar**   | /fwd/**bar**   | /foo/**bar**   | /foo/bar/**bar**   |


## <a name="optional-settings"></a>Configurações opcionais
Existem configurações opcionais adicionais que também pode especificar para qualquer definição da regra de encaminhamento:

* **Configuração cache** - Se desativado ou não especificado, então os pedidos que correspondam a esta regra de encaminhamento não tentarão utilizar conteúdo em cache e, em vez disso, obterão sempre do backend. Leia mais sobre [Caching com porta da frente.](front-door-caching.md)



## <a name="next-steps"></a>Passos seguintes

- Saiba como [criar um Front Door](quickstart-create-front-door.md).
- Saiba [como funciona o Front Door](front-door-routing-architecture.md).

<!--Image references-->
[1]: ./media/front-door-url-rewrite/front-door-url-rewrite-example.jpg