---
title: Porta da Frente Azure - Regra de encaminhamento que combina com a monitorização [ Microsoft Docs
description: Este artigo ajuda-o a entender como a Porta da Frente Azure corresponde à regra de encaminhamento para um pedido de entrada
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
ms.openlocfilehash: 420aa52293da14a0dfe8fbdfe681440ee4309e6b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80878600"
---
# <a name="how-front-door-matches-requests-to-a-routing-rule"></a>Como a Porta da Frente corresponde aos pedidos de uma regra de encaminhamento

Depois de estabelecer uma ligação e fazer um aperto de mão TLS, quando um pedido aterra num ambiente da Porta da Frente uma das primeiras coisas que a Porta da Frente faz é determinar a partir de todas as configurações, que regra de encaminhamento particular para corresponder ao pedido e, em seguida, tomar a ação definida. O seguinte documento explica como a Porta Frontal determina qual a configuração da Rota a utilizar ao processar um pedido http.

## <a name="structure-of-a-front-door-route-configuration"></a>Estrutura de uma configuração de rota da porta da frente
Uma configuração de regra de encaminhamento da Porta Frontal é composta por duas partes principais: um "lado esquerdo" e um "lado direito". Combinamos com o pedido de entrada para o lado esquerdo da rota, enquanto o lado direito define como processamos o pedido.

### <a name="incoming-match-left-hand-side"></a>Jogo de entrada (lado esquerdo)
As seguintes propriedades determinam se o pedido de entrada corresponde à regra de encaminhamento (ou lado esquerdo):

* **PROTOCOLOS HTTP** (HTTP/HTTPS)
* **Anfitriões** (por\.exemplo, \*www foo.com, .bar.com)
* **Caminhos** (por exemplo, /\*\*/ /utilizadores/ /file.gif)

Estas propriedades são expandidas internamente de modo que cada combinação de Protocolo/Anfitrião/Caminho é um conjunto de correspondência potencial.

### <a name="route-data-right-hand-side"></a>Dados de rota (lado direito)
A decisão de como processar o pedido depende de se o cache está ou não habilitado para a rota específica. Então, se não tivermos uma resposta em cache para o pedido, vamos reencaminhar o pedido para o backend apropriado na piscina configurada backend.

## <a name="route-matching"></a>Correspondência de rotas
Esta secção vai focar-se na forma como combinamos com uma regra de encaminhamento da Porta da Frente. O conceito básico é que sempre nos coatemos com o **jogo mais específico, primeiro** olhando apenas para o "lado esquerdo".  Nós primeiro combinamos com base no protocolo HTTP, depois frontend host, em seguida, o Caminho.

### <a name="frontend-host-matching"></a>Correspondência do anfitrião frontend
Ao combinar anfitriões Frontend, usamos a lógica como abaixo:

1. Procure qualquer encaminhamento com uma correspondência exata no hospedeiro.
2. Se não houver anfitriões frontais exatos, rejeite o pedido e envie um erro de 400 Bad Request.

Para explicar mais este processo, vamos olhar para uma configuração exemplo das rotas da Porta Da Frente (apenas do lado esquerdo):

| Regra de encaminhamento | Anfitriões frontend | Caminho |
|-------|--------------------|-------|
| A | foo.contoso.com | /\* |
| B | foo.contoso.com | /utilizadores/\* |
| C | www\.fabrikam.com, foo.adventure-works.com  | /\*, /imagens/\* |

Se os seguintes pedidos de entrada fossem enviados para a Porta da Frente, corresponderiam às seguintes regras de encaminhamento de cima:

| Anfitrião de frontend | Regra de encaminhamento compatível |
|---------------------|---------------|
| foo.contoso.com | A, B |
| www\.fabrikam.com | C |
| images.fabrikam.com | Erro 400: Pedido mau |
| foo.adventure-works.com | C |
| contoso.com | Erro 400: Pedido mau |
| www\.adventure-works.com | Erro 400: Pedido mau |
| www\.northwindtraders.com | Erro 400: Pedido mau |

### <a name="path-matching"></a>Correspondência de caminho
Depois de determinar o anfitrião frontal específico e filtrar possíveis regras de encaminhamento apenas para as rotas com o anfitrião frontal, a Porta frontal filtra as regras de encaminhamento com base na trajetória de pedido. Usamos uma lógica semelhante como os anfitriões frontendas:

1. Procure qualquer regra de encaminhamento com uma correspondência exata no Caminho
2. Se não combinar exatamente Caminhos, procure regras de encaminhamento com um caminho wildcard que corresponda
3. Se não forem encontradas regras de encaminhamento com um Caminho correspondente, então rejeite o pedido e devolva uma resposta HTTP de erro de pedido de mau pedido.

>[!NOTE]
> Quaisquer Caminhos sem wildcard são considerados caminhos exatamente compatíveis. Mesmo que o Caminho termine num corte, ainda é considerado compatível.

Para explicar mais, vamos olhar para outro conjunto de exemplos:

| Regra de encaminhamento | Anfitrião frontend    | Caminho     |
|-------|---------|----------|
| A     | www\.contoso.com | /        |
| B     | www\.contoso.com | /\*      |
| C     | www\.contoso.com | /ab      |
| D     | www\.contoso.com | /abc     |
| E     | www\.contoso.com | /abc/    |
| F     | www\.contoso.com | /abc/\*  |
| G     | www\.contoso.com | /abc/def |
| H     | www\.contoso.com | /caminho/   |

Tendo em conta esta configuração, resultaria o seguinte exemplo de quadro correspondente:

| Pedido de Entrada    | Rota igualada |
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
> </br> Se não houver regras de encaminhamento para um anfitrião frontal`/*`de correspondência exata com um caminho catch-all ( caminho de captura) então não haverá uma correspondência com qualquer regra de encaminhamento.
>
> Configuração do exemplo:
>
> | Encaminhar | Anfitrião             | Caminho    |
> |-------|------------------|---------|
> | A     | profile.contoso.com | /api/\* |
>
> Tabela correspondente:
>
> | Pedido de entrada       | Rota igualada |
> |------------------------|---------------|
> | profile.domain.com/other | Nenhum. Erro 400: Pedido mau |

### <a name="routing-decision"></a>Decisão de encaminhamento
Uma vez que correspondamos a uma única regra de encaminhamento da Porta Da Frente, precisamos escolher como processar o pedido. Se para a regra de encaminhamento compatível, a Porta da Frente tem uma resposta em cache disponível, então o mesmo é servido de volta para o cliente. Caso contrário, a próxima coisa que é avaliada é se você configurado [URL Rewrite (caminho de encaminhamento personalizado)](front-door-url-rewrite.md) para a regra de encaminhamento combinado ou não. Se não houver um caminho de encaminhamento personalizado definido, então o pedido é encaminhado para o backend apropriado na piscina de backend configurada como está. Caso contrário, o caminho de pedido é atualizado de acordo com o [caminho de encaminhamento personalizado](front-door-url-rewrite.md) definido e, em seguida, encaminhado para o backend.

## <a name="next-steps"></a>Passos seguintes

- Saiba como [criar um Front Door](quickstart-create-front-door.md).
- Saiba [como funciona o Front Door](front-door-routing-architecture.md).
