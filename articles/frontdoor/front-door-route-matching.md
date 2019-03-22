---
title: Serviço de porta de entrada do Azure - encaminhamento da regra de monitorização correspondentes | Documentos da Microsoft
description: Este artigo ajuda-o a compreender como o serviço de porta de entrada do Azure corresponde à regra de encaminhamento que a utilizar para uma solicitação de entrada
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
ms.openlocfilehash: eec99bde0ea73a99a9dc1345f938b821a95a7c05
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58111842"
---
# <a name="how-front-door-matches-requests-to-a-routing-rule"></a>Como porta de entrada corresponde à pedidos para uma regra de encaminhamento

Depois de estabelecer uma ligação e fazer um handshake SSL, quando um pedido pousar num ambiente de porta de entrada, uma das primeiras coisas que faz de porta de entrada é determinar de todas as configurações, que regra de encaminhamento específica para corresponder o pedido para e, em seguida, colocar a ação definida. O documento seguinte explica como a porta de entrada determina que configuração de rota para utilizar ao processar um pedido HTTP.

## <a name="structure-of-a-front-door-route-configuration"></a>Estrutura de uma configuração de rota de porta de entrada
Porta de Front uma configuração de regra de encaminhamento é composta por duas partes principais: um "lado esquerdo" e "lado direito". Podemos corresponder a solicitação de entrada para o lado esquerdo da rota, enquanto o lado direito define como conseguimos processar o pedido.

### <a name="incoming-match-left-hand-side"></a>Correspondência de entrada (lado esquerdo)
As seguintes propriedades de determinam se a solicitação de entrada coincide com a regra de encaminhamento (ou o lado esquerdo):

* **Protocolos HTTP** (HTTP/HTTPS)
* **Hosts** (por exemplo, www\.foo.com, \*. bar.com)
* **Caminhos** (por exemplo, /\*, /users/\*, /file.gif)

Estas propriedades são expandidas horizontalmente internamente, para que cada combinação de protocolo/anfitrião/caminho é um conjunto de correspondência de potenciais.

### <a name="route-data-right-hand-side"></a>Dados da rota (direita)
A decisão de como processar o pedido, depende se a colocação em cache está ativada ou não para a rota específica. Então, se não tivermos uma resposta em cache para o pedido, podemos irá reencaminhar o pedido para o back-end apropriado no conjunto de back-end configurado.

## <a name="route-matching"></a>Encaminhar correspondência
Esta secção irá focar-se em como podemos corresponder a uma determinada regra de encaminhamento de porta de entrada. O conceito básico é que podemos corresponder sempre para o **mais específica corresponde ao primeiro** apenas verificando "esquerda".  Podemos primeiro corresponder com base no protocolo HTTP, em seguida, o anfitrião de front-end, em seguida, o caminho.

### <a name="frontend-host-matching"></a>Anfitrião de front-end correspondentes
Quando os anfitriões de front-end de correspondência, utilizamos a lógica como abaixo:

1. Procure qualquer roteamento com uma correspondência exata no anfitrião.
2. Se não existem anfitriões de front-end exatamente iguais, rejeitar a solicitação e enviar um erro de 400 pedido inválido.

Para explicar esse processo ainda mais, vamos examinar um exemplo de configuração de rotas de porta de entrada (apenas no lado esquerdo):

| Regra de encaminhamento | Anfitriões de front-end | Caminho |
|-------|--------------------|-------|
| A | foo.contoso.com | /\* |
| B | foo.contoso.com | /users/\* |
| C | www\.fabrikam.com, foo.adventure-works.com  | /\*, /images/\* |

Se os seguintes pedidos de entrada foram enviados para a porta da frente, eles seriam correspondência com as seguintes regras de encaminhamento acima:

| Anfitrião de front-end recebidos | Corresponde a regra ou regras de encaminhamento |
|---------------------|---------------|
| foo.contoso.com | A, B |
| www\.fabrikam.com | C |
| images.fabrikam.com | Erro 400: Pedido Inválido |
| foo.adventure-works.com | C |
| contoso.com | Erro 400: Pedido Inválido |
| www\.adventure-works.com | Erro 400: Pedido Inválido |
| www\.northwindtraders.com | Erro 400: Pedido Inválido |

### <a name="path-matching"></a>Correspondência de caminho
Depois de determinar o anfitrião de front-end específicas e filtragem de possíveis regras de encaminhamento para apenas as rotas com esse anfitrião de front-end, porta de entrada, em seguida, filtra as regras de encaminhamento com base no caminho de pedido. Podemos usar uma lógica semelhante a como anfitriões de front-end:

1. Procure qualquer regra de encaminhamento com uma correspondência exata no caminho
2. Se nenhum caminho de correspondência exata, procure as regras de encaminhamento com o caminho que corresponde a caráter universal
3. Se não existem regras de encaminhamento encontram-se com um caminho correspondente, em seguida, rejeitar a solicitação e retornar um 400: Erro de pedido incorreto resposta HTTP.

>[!NOTE]
> Qualquer caminhos sem um caráter universal são considerados como caminhos com correspondência exacta. Mesmo que o caminho termina dentro de uma barra, ainda é considerada como correspondência exata.

Explicar mais detalhes, vamos examinar outro conjunto de exemplos:

| Regra de encaminhamento | Anfitrião de front-end    | Caminho     |
|-------|---------|----------|
| A     | www\.contoso.com | /        |
| B     | www\.contoso.com | /\*      |
| C     | www\.contoso.com | /ab      |
| D     | www\.contoso.com | /abc     |
| E     | www\.contoso.com | /abc/    |
| F     | www\.contoso.com | /abc/\*  |
| G     | www\.contoso.com | /abc/def |
| H     | www\.contoso.com | /path/   |

Tendo em conta que a configuração, resultaria-se a tabela correspondente de exemplo seguinte:

| Solicitação de entrada    | Rotas correspondentes |
|---------------------|---------------|
| www\.contoso.com/            | A             |
| www\.contoso.com/a           | B             |
| www\.contoso.com/ab          | C             |
| www\.contoso.com/abc         | D             |
| www\.contoso.com/abzzz       | B             |
| www\.contoso.com/abc/        | E             |
| www\.contoso.com/abc/d       | F             |
| www\.contoso.com/abc/def     | G             |
| www\.contoso.com/abc/defzzz  | F             |
| www\.contoso.com/abc/def/ghi | F             |
| www\.contoso.com/path        | B             |
| www\.contoso.com/path/       | H             |
| www\.contoso.com/path/zzz    | B             |

>[!WARNING]
> </br> Se existirem não existem regras de encaminhamento para um anfitrião de front-end de com correspondência exacta com uma grande detectora encaminhar caminho (`/*`), em seguida, não haverá uma correspondência para qualquer regra de encaminhamento.
>
> Exemplo de configuração:
>
> | Encaminhar | Anfitrião             | Caminho    |
> |-------|------------------|---------|
> | A     | profile.contoso.com | /api/\* |
>
> Tabela correspondente:
>
> | Solicitação de entrada       | Rotas correspondentes |
> |------------------------|---------------|
> | profile.domain.com/other | Nenhum. Erro 400: Pedido Inválido |

### <a name="routing-decision"></a>Decisão de encaminhamento
Assim que podemos ter correspondido a uma única regra de encaminhamento de porta de entrada, em seguida, precisamos escolha como pretende processar o pedido. Se para a regra de encaminhamento correspondente, porta de entrada tem uma resposta em cache disponível, em seguida, o mesmo obtém fornecido ao cliente. Caso contrário, a próxima coisa que obtém valores é se configurou [(caminho de encaminhamento personalizado) de reescrita de URLs](front-door-url-rewrite.md) para o encaminhamento correspondentes da regra ou não. Se não existir um caminho de encaminhamento personalizado definido, a solicitação é encaminhada para o back-end apropriado no conjunto de back-end configurado, conforme é. Caso contrário, o caminho da solicitação é atualizado, de acordo a [caminho de encaminhamento personalizado](front-door-url-rewrite.md) definido e, em seguida, encaminhar para o back-end.

## <a name="next-steps"></a>Passos Seguintes

- Saiba como [criar um Front Door](quickstart-create-front-door.md).
- Saiba [como funciona o Front Door](front-door-routing-architecture.md).
