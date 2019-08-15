---
title: Serviço de porta frontal do Azure-regravação de URL | Microsoft Docs
description: Este artigo ajuda você a entender como o serviço de porta frontal do Azure faz a regravação da URL para suas rotas, se configurada.
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
ms.openlocfilehash: dc2126276e3e8e0d35ce8ed1f835544386659eff
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/12/2019
ms.locfileid: "60736191"
---
# <a name="url-rewrite-custom-forwarding-path"></a>Regravação de URL (caminho de encaminhamento personalizado)
O serviço de porta frontal do Azure dá suporte à regravação de URL, permitindo que você configure um **caminho de encaminhamento personalizado** opcional para usar ao construir a solicitação para encaminhar para o back-end. Por predefinição, se não for fornecido nenhum caminho de reencaminhamento, o Front Door copiará o caminho do URL de entrada para o URL utilizado no pedido reencaminhado. O cabeçalho Host (Anfitrião) utilizado no pedido reencaminhado é igual ao configurado para o back-end selecionado. Leia o [cabeçalho de host de back-end](front-door-backend-pool.md#hostheader) para saber o que ele faz e como você pode configurá-lo.

A parte poderosa da reescrita de URL usando o caminho de encaminhamento personalizado é que ela copiará qualquer parte do caminho de entrada que corresponde a um caminho curinga para o caminho encaminhado (esses segmentos de caminho são os segmentos **verdes** no exemplo abaixo):
</br>
![Regravação da URL da porta frontal do Azure][1]

## <a name="url-rewrite-example"></a>Exemplo de reescrita de URL
Considere uma regra de roteamento com os seguintes hosts front-end e caminhos configurados:

| Anfitriões      | Caminhos       |
|------------|-------------|
| contoso.com\.www | /\*         |
|            | /Foo        |
|            | /Foo\*     |
|            | /foo/bar/\* |

A primeira coluna da tabela abaixo mostra exemplos de solicitações de entrada e a segunda coluna mostra o que seria o "caminho" mais específico "da rota correspondente.  A terceira e as colunas subsequentes da primeira linha da tabela são exemplos de **caminhos de encaminhamento personalizados**configurados, com o restante das linhas nessas colunas que representam exemplos do que o caminho de solicitação encaminhado seria se ele coincidisse com a solicitação naquela fila.

Por exemplo, se lermos pela segunda linha, ele está dizendo que, para a solicitação `www.contoso.com/sub`de entrada, se o caminho de encaminhamento personalizado fosse `/`, o caminho `/sub`encaminhado seria. Se o caminho de encaminhamento personalizado `/fwd/`era, o caminho encaminhado `/fwd/sub`seria. E assim por diante, para as colunas restantes. As partes enfatizadas dos caminhos abaixo representam as partes que fazem parte da correspondência curinga.


| Solicitação de entrada       | Caminho de correspondência mais específico | /          | FWD          | /Foo          | /foo/bar/          |
|------------------------|--------------------------|------------|----------------|----------------|--------------------|
| contoso.com/\.www            | /\*                      | /          | FWD          | /Foo          | /foo/bar/          |
| sub\.contoso.com/ da Web     | /\*                      | /**sub**   | /fwd/**sub**   | /foo/**sub**   | /foo/bar/**sub**   |
| www\.contoso.com/**a/b/c**   | /\*                      | /**a/b/c** | /fwd/**a/b/c** | /Foo/**a/b/c** | /foo/bar/**a/b/c** |
| contoso.com/foo\.www         | /Foo                     | /          | FWD          | /Foo          | /foo/bar/          |
| contoso.com/foo/\.www        | /Foo\*                  | /          | FWD          | /Foo          | /foo/bar/          |
| barra\.de contoso.com/foo/da Web | /Foo\*                  | /**bar**   | /fwd/**bar**   | /foo/**bar**   | /foo/bar/**bar**   |


## <a name="optional-settings"></a>Definições opcionais
Há configurações adicionais opcionais que você também pode especificar para qualquer configuração de regra de roteamento específica:

* **Configuração de cache** -se desabilitada ou não especificada, as solicitações que correspondem a essa regra de roteamento não tentarão usar o conteúdo em cache e, em vez disso, sempre serão buscadas do back-end. Leia mais sobre [caching com a porta frontal](front-door-caching.md).



## <a name="next-steps"></a>Passos Seguintes

- Saiba como [criar um Front Door](quickstart-create-front-door.md).
- Saiba [como funciona o Front Door](front-door-routing-architecture.md).

<!--Image references-->
[1]: ./media/front-door-url-rewrite/front-door-url-rewrite-example.jpg