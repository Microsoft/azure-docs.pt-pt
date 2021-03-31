---
title: Porta frontal Azure - URL Reescreve | Microsoft Docs
description: Este artigo ajuda-o a entender como a Porta Frontal Azure faz a REescrita de URL para as suas rotas, se configurada.
services: front-door
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2020
ms.author: duau
ms.openlocfilehash: eb5b4ab8a23a374aec54d65dd5390ab3fec3e905
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "91445486"
---
# <a name="url-rewrite-custom-forwarding-path"></a>Reescrita de URLs (caminho de reencaminhamento personalizado)
A porta frontal Azure suporta a reescrita do URL configurando um Caminho de **Encaminhamento Personalizado** opcional para usar ao construir o pedido para encaminhar para o backend. Por predefinição, se não for fornecido um caminho de encaminhamento personalizado, a Porta frontal copiará o caminho URL de entrada para o URL utilizado no pedido reencaminhado. O cabeçalho Host (Anfitrião) utilizado no pedido reencaminhado é igual ao configurado para o back-end selecionado. Leia [o Cabeçalho do Anfitrião Backend](front-door-backend-pool.md#hostheader) para saber o que faz e como pode configugá-lo.

A parte poderosa da reescrita do URL é que o caminho de encaminhamento personalizado irá copiar qualquer parte do caminho de entrada que corresponda a um caminho wildcard para o caminho reencaminhado (estes segmentos de caminho são os segmentos **verdes** no exemplo abaixo):
</br>

:::image type="content" source="./media/front-door-url-rewrite/front-door-url-rewrite-example.jpg" alt-text="Reescrita de URL da porta da frente Azure":::

## <a name="url-rewrite-example"></a>EXEMPLO de reescrita de URL
Considere uma regra de encaminhamento com a seguinte combinação de anfitriões frontend e caminhos configurados:

| Anfitriões      | Caminhos       |
|------------|-------------|
| www \. contoso.com | /\*   |
|            | /foo        |
|            | /foo/\*     |
|            | /foo/bar/\* |

A primeira coluna da tabela abaixo mostra exemplos de pedidos de entrada e a segunda coluna mostra qual seria a rota de correspondência "mais específica".  As terceiras colunas da tabela são exemplos de caminhos de **encaminhamento personalizados** configurados.

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

* **Configuração cache** - Se desativado ou não especificado, os pedidos que correspondam a esta regra de encaminhamento não tentarão utilizar o conteúdo em cache e, em vez disso, irão sempre buscar a partir do backend. Leia mais sobre [Caching com Porta frontal.](front-door-caching.md)

## <a name="next-steps"></a>Passos seguintes

- Saiba como [criar um Front Door](quickstart-create-front-door.md).
- Saiba [como funciona o Front Door](front-door-routing-architecture.md).
