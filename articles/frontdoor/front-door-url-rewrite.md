---
title: Porta frontal Azure - URL Rewrite / Microsoft Docs
description: Este artigo ajuda-o a entender como a Porta Frontal Azure faz a REescrita de URL para as suas rotas, se configurada.
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "79471477"
---
# <a name="url-rewrite-custom-forwarding-path"></a>Reescrita de URLs (caminho de reencaminhamento personalizado)
Azure Front Door suporta a reescrita do URL, permitindo-lhe configurar um Caminho de **Encaminhamento Personalizado** opcional para usar ao construir o pedido de encaminhamento para o backend. Por predefinição, se não for fornecido nenhum caminho de reencaminhamento, o Front Door copiará o caminho do URL de entrada para o URL utilizado no pedido reencaminhado. O cabeçalho Host (Anfitrião) utilizado no pedido reencaminhado é igual ao configurado para o back-end selecionado. Leia [o Cabeçalho do Anfitrião Backend](front-door-backend-pool.md#hostheader) para saber o que faz e como pode configugá-lo.

A parte poderosa da reescrita de URL usando o caminho de encaminhamento personalizado é que irá copiar qualquer parte do caminho de entrada que corresponda a um caminho wildcard para o caminho reencaminhado (estes segmentos de caminho são os segmentos **verdes** no exemplo abaixo):
</br>
![Reescrita de URL da porta da frente Azure][1]

## <a name="url-rewrite-example"></a>EXEMPLO de reescrita de URL
Considere uma regra de encaminhamento com os seguintes anfitriões frontend e caminhos configurados:

| Anfitriões      | Caminhos       |
|------------|-------------|
| www \. contoso.com | /\*         |
|            | /foo        |
|            | /foo/\*     |
|            | /foo/bar/\* |

A primeira coluna da tabela abaixo mostra exemplos de pedidos de entrada e a segunda coluna mostra qual seria a rota de correspondência "mais específica".  As terceiras e posteriores colunas da primeira linha da tabela são exemplos de caminhos de **encaminhamento personalizados**configurados, com o resto das linhas nessas colunas representando exemplos do que seria o caminho de pedido reencaminhado se correspondesse ao pedido nessa linha.

Por exemplo, se lermos através da segunda fila, está a dizer que para pedido de entrada `www.contoso.com/sub` , se o caminho de encaminhamento personalizado fosse , `/` então o caminho reencaminhado seria `/sub` . Se o caminho de encaminhamento personalizado `/fwd/` fosse, então o caminho reencaminhado seria `/fwd/sub` . E assim por diante, para as colunas restantes. As partes **enfatizadas** dos caminhos abaixo representam as porções que fazem parte do jogo wildcard.


| Pedido de entrada       | Caminho de correspondência mais específico | /          | /fwd/          | /foo/          | /foo/bar/          |
|------------------------|--------------------------|------------|----------------|----------------|--------------------|
| www \. contoso.com/            | /\*                      | /          | /fwd/          | /foo/          | /foo/bar/          |
| www \. contoso.com/**sub**     | /\*                      | /**sub**   | /fwd/**sub**   | /foo/**sub**   | /foo/bar/**sub**   |
| www \. contoso.com/**a/b/c**   | /\*                      | /**a/b/c** | /fwd/**a/b/c** | /foo/**a/b/c** | /foo/bar/**a/b/c** |
| www \. contoso.com/foo         | /foo                     | /          | /fwd/          | /foo/          | /foo/bar/          |
| www \. contoso.com/foo/        | /foo/\*                  | /          | /fwd/          | /foo/          | /foo/bar/          |
| www \. contoso.com/foo/**bar** | /foo/\*                  | /**bar**   | /fwd/**bar**   | /foo/**bar**   | /foo/bar/**bar**   |


## <a name="optional-settings"></a>Configurações opcionais
Existem configurações opcionais adicionais que também pode especificar para quaisquer definições de regra de encaminhamento:

* **Configuração cache** - Se desativado ou não especificado, então solicita que corresponda a esta regra de encaminhamento não tentará usar conteúdo em cache e, em vez disso, irá sempre buscar a partir do backend. Leia mais sobre [Caching com Porta frontal.](front-door-caching.md)



## <a name="next-steps"></a>Próximos passos

- Saiba como [criar um Front Door](quickstart-create-front-door.md).
- Saiba [como funciona o Front Door](front-door-routing-architecture.md).

<!--Image references-->
[1]: ./media/front-door-url-rewrite/front-door-url-rewrite-example.jpg