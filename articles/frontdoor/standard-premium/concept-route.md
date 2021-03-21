---
title: O que é Azure Front Door Standard/Premium Route?
description: Este artigo ajuda-o a entender como o Azure Front Door Standard/Premium corresponde à regra de encaminhamento para um pedido de entrada.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/18/2021
ms.author: duau
ms.openlocfilehash: db026c4903aa30a0a4c8154af8ad6eeb4b72b706
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101100052"
---
# <a name="what-is-azure-front-door-standardpremium-preview-route"></a>O que é Azure Front Door Standard/Premium (Preview) Route?

> [!Note]
> Esta documentação destina-se ao Azure Front Door Standard/Premium (Preview). À procura de informações sobre a Porta da Frente Azure? Ver [aqui](../front-door-overview.md).

A rota Azure Front Door Standard/Premium Route define como o tráfego é tratado quando o pedido de entrada chega ao ambiente da Porta Frontal Azure. Através das definições de Rota, uma associação é definida entre um domínio e um grupo de origem backend. Ao ligar as funcionalidades avançadas como Pattern to Mach, Rule set, um maior controlo granular sobre o tráfego é alcançável.

Uma configuração de encaminhamento Standard/Premium da porta da frente é composta por duas partes principais: "lado esquerdo" e "lado direito". Correspondemos ao pedido de entrada para o lado esquerdo da rota e o lado direito define como processamos o pedido.

> [!IMPORTANT]
> Azure Front Door Standard/Premium (Preview) está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

### <a name="incoming-match-left-hand-side"></a>Jogo de entrada (lado esquerdo)

As seguintes propriedades determinam se o pedido de entrada corresponde à regra de encaminhamento (ou do lado esquerdo):

* **PROTOCOLOS HTTP** (HTTP/HTTPS)
* **Anfitriões** (por exemplo, www \. foo.com, \* .bar.com)
* **Caminhos** (por exemplo, / \* / / utilizadores/ \* / /file.gif)

Estas propriedades são expandidas internamente para que cada combinação de Protocolo/Anfitrião/Caminho seja um conjunto de correspondência potencial.

### <a name="route-data-right-hand-side"></a>Dados da rota (do lado direito)

A decisão de como processar o pedido depende se o caching está ou não habilitado para a Rota. Se uma resposta em cache não estiver disponível, então o pedido é reencaminhado para o backend apropriado.

## <a name="route-matching"></a>Correspondência de rota

Esta secção concentrar-se-á na forma como nos correspondemos a uma determinada regra de encaminhamento da Porta frontal. O conceito básico é que sempre correspondemos ao **jogo mais específico primeiro** olhando apenas para o "lado esquerdo".  Primeiro combinamos com base no protocolo HTTP, depois no Frontend Host, depois no Caminho.

### <a name="frontend-host-matching"></a>Frontend host matching

Ao combinar anfitriões frontend, usamos a lógica definida abaixo:

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

Depois de Azure Front Door Standard/Premium determinar o anfitrião frontal específico e filtrar possíveis regras de encaminhamento apenas para as rotas com o anfitrião frontend. A Porta frontal filtra então as regras de encaminhamento com base no caminho do pedido. Usamos uma lógica semelhante à dos anfitriões frontend:

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

Uma vez que o Azure Front Door Standard/Premium tenha correspondido a uma única regra de encaminhamento, tem de escolher como processar o pedido. Se o Azure Front Door Standard/Premium tiver uma resposta em cache disponível para a regra de encaminhamento combinado, então o pedido é devolvido ao cliente. A próxima coisa que o Azure Front Door Standard/Premium avalia é se tem ou não uma Regra Definida para a regra de encaminhamento combinado. Se não houver um Conjunto de Regras definido, então o pedido é reencaminhado para o pool de backend como está. Caso contrário, o Conjunto de Regras é executado na ordem tal como está configurado.

## <a name="next-steps"></a>Passos seguintes

Saiba como [criar um Padrão/Premium da Porta da Frente.](create-front-door-portal.md)
