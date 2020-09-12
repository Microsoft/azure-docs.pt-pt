---
title: Azure Front Door - Monitorização da regra de encaminhamento / Microsoft Docs
description: Este artigo ajuda-o a entender como a Porta Frontal Azure corresponde à regra de encaminhamento para um pedido de entrada
services: front-door
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: duau
ms.openlocfilehash: 9593a6c4fa45d9810aabb2bbb3123428930c5891
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/02/2020
ms.locfileid: "89401576"
---
# <a name="how-front-door-matches-requests-to-a-routing-rule"></a>Como o Front Door faz a correspondência entre os pedidos e uma regra de encaminhamento

Depois de estabelecer uma ligação e fazer um aperto de mão TLS, quando um pedido aterra num ambiente da Porta da Frente uma das primeiras coisas que a Porta Frontal faz é determinar a partir de todas as configurações, qual a regra de encaminhamento particular para corresponder ao pedido e, em seguida, tomar a ação definida. O documento que se segue explica como a Porta Frontal determina qual a configuração da Rota a utilizar ao processar um pedido HTTP.

## <a name="structure-of-a-front-door-route-configuration"></a>Estrutura de uma configuração de rota da porta da frente
Uma configuração da regra de encaminhamento da porta frontal é composta por duas partes principais: um "lado esquerdo" e um "lado direito". Correspondemos ao pedido de entrada para o lado esquerdo da rota enquanto o lado direito define como processamos o pedido.

### <a name="incoming-match-left-hand-side"></a>Jogo de entrada (lado esquerdo)
As seguintes propriedades determinam se o pedido de entrada corresponde à regra de encaminhamento (ou do lado esquerdo):

* **PROTOCOLOS HTTP** (HTTP/HTTPS)
* **Anfitriões** (por exemplo, www \. foo.com, \* .bar.com)
* **Caminhos** (por exemplo, / \* / / utilizadores/ \* / /file.gif)

Estas propriedades são expandidas internamente para que cada combinação de Protocolo/Anfitrião/Caminho seja um conjunto de correspondência potencial.

### <a name="route-data-right-hand-side"></a>Dados da rota (do lado direito)
A decisão de como processar o pedido depende se o caching está ou não habilitado para a rota específica. Então, se não tivermos uma resposta em cache para o pedido, vamos reencamgar o pedido para o backend apropriado na piscina de backend configurada.

## <a name="route-matching"></a>Correspondência de rota
Esta secção concentrar-se-á na forma como nos correspondemos a uma determinada regra de encaminhamento da Porta frontal. O conceito básico é que sempre correspondemos ao **jogo mais específico primeiro** olhando apenas para o "lado esquerdo".  Primeiro combinamos com base no protocolo HTTP, depois no Frontend Host, depois no Caminho.

### <a name="frontend-host-matching"></a>Frontend host matching
Ao combinar anfitriões frontend, usamos a lógica como abaixo:

1. Procure por qualquer encaminhamento com uma correspondência exata no anfitrião.
2. Se nenhum anfitrião de frontend corresponder, rejeite o pedido e envie um erro de 400 Bad Request.

Para explicar mais aprofundadamente este processo, vamos olhar para uma configuração de exemplo das rotas da Porta Frontal (apenas do lado esquerdo):

| Regra de encaminhamento | Anfitriões frontend | Caminho |
|-------|--------------------|-------|
| A | foo.contoso.com | /\* |
| B | foo.contoso.com | /utilizadores/\* |
| C | www \. fabrikam.com, foo.adventure-works.com  | /\*, /imagens/\* |

Se os seguintes pedidos de entrada fossem enviados para a Porta da Frente, corresponderiam às seguintes regras de encaminhamento a partir de cima:

| Anfitrião frontend incoming | Regras de encaminhamento combinados |
|---------------------|---------------|
| foo.contoso.com | A, B |
| www \. fabrikam.com | C |
| images.fabrikam.com | Erro 400: Mau Pedido |
| foo.adventure-works.com | C |
| contoso.com | Erro 400: Mau Pedido |
| www \. adventure-works.com | Erro 400: Mau Pedido |
| www \. northwindtraders.com | Erro 400: Mau Pedido |

### <a name="path-matching"></a>Correspondência de caminhos
Depois de determinar o anfitrião frontal específico e filtrar possíveis regras de encaminhamento apenas para as rotas com o anfitrião frontal, a Porta frontal filtra então as regras de encaminhamento com base no caminho do pedido. Usamos uma lógica semelhante à dos anfitriões frontend:

1. Procure qualquer regra de encaminhamento com uma correspondência exata no Caminho
2. Se não houver um match Paths exato, procure regras de encaminhamento com um caminho wildcard que corresponda
3. Se não forem encontradas regras de encaminhamento com um Caminho correspondente, rejeitar o pedido e devolver uma resposta HTTP de erro de pedido de erro 400: Erro de pedido de mau pedido.

>[!NOTE]
> Quaisquer Caminhos sem wildcard são considerados caminhos exatos. Mesmo que o Caminho termine num corte, ainda é considerado um jogo exato.

Para explicar mais, vamos olhar para outro conjunto de exemplos:

| Regra de encaminhamento | Anfitrião frontend    | Caminho     |
|-------|---------|----------|
| A     | www \. contoso.com | /        |
| B     | www \. contoso.com | /\*      |
| C     | www \. contoso.com | /ab      |
| D     | www \. contoso.com | /abc     |
| E     | www \. contoso.com | /abc/    |
| F     | www \. contoso.com | /abc/\*  |
| G     | www \. contoso.com | /abc/def |
| H     | www \. contoso.com | /caminho/   |

Tendo em conta esta configuração, resultaria na seguinte tabela de correspondência de exemplo:

| Pedido de Entrada    | Rota Correspondida |
|---------------------|---------------|
| www \. contoso.com/            | A             |
| www \. contoso.com/a           | B             |
| www \. contoso.com/ab          | C             |
| www \. contoso.com/abc         | D             |
| www \. contoso.com/abzzz       | B             |
| www \. contoso.com/abc/        | E             |
| www \. contoso.com/abc/d       | F             |
| www \. contoso.com/abc/def     | G             |
| www \. contoso.com/abc/defzzz  | F             |
| www \. contoso.com/abc/def/ghi | F             |
| www \. contoso.com/path        | B             |
| www \. contoso.com/path/       | H             |
| www \. contoso.com/path/zzz    | B             |

>[!WARNING]
> </br> Se não houver regras de encaminhamento para um anfitrião frontal de jogo exato com um caminho de rota catch-all `/*` (), então não haverá correspondência com nenhuma regra de encaminhamento.
>
> Configuração de exemplo:
>
> | Rota | Anfitrião             | Caminho    |
> |-------|------------------|---------|
> | A     | profile.contoso.com | /api/\* |
>
> Tabela correspondente:
>
> | Pedido de entrada       | Rota Correspondida |
> |------------------------|---------------|
> | profile.domain.com/other | Nenhum. Erro 400: Mau Pedido |

### <a name="routing-decision"></a>Decisão de encaminhamento
Uma vez que tenhamos uma regra de encaminhamento da Porta da Frente, precisamos escolher como processar o pedido. Se para a regra de encaminhamento combinado, a Porta frontal tem uma resposta em cache disponível, então o mesmo é servido de volta ao cliente. Caso contrário, a próxima coisa que é avaliada é se você tem [CONFIGURADO URL Rewrite (caminho de encaminhamento personalizado)](front-door-url-rewrite.md) para a regra de encaminhamento combinado ou não. Se não houver um caminho de encaminhamento personalizado definido, então o pedido é reencaminhado para o backend apropriado na piscina de backend configurada como está. Caso contrário, o caminho do pedido é atualizado de acordo com o [caminho de encaminhamento personalizado](front-door-url-rewrite.md) definido e, em seguida, encaminhar para o backend.

## <a name="next-steps"></a>Próximos passos

- Saiba como [criar um Front Door](quickstart-create-front-door.md).
- Saiba [como funciona o Front Door](front-door-routing-architecture.md).
