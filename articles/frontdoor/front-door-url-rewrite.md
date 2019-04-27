---
title: Frente do Azure de porta de serviço - reescrever URL | Documentos da Microsoft
description: Este artigo ajuda-o a compreender como serviço de porta de entrada do Azure efetua uma reescrita de URLs para as rotas, se configurado.
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
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60736191"
---
# <a name="url-rewrite-custom-forwarding-path"></a>Reescrever URL (caminho de encaminhamento personalizado)
Serviço de porta de entrada do Azure suporta reescrita de URL, permitindo-lhe configurar opcional **caminho de encaminhamento personalizado** a utilizar ao construir o pedido para reencaminhar para o back-end. Por predefinição, se não for fornecido nenhum caminho de reencaminhamento, o Front Door copiará o caminho do URL de entrada para o URL utilizado no pedido reencaminhado. O cabeçalho Host (Anfitrião) utilizado no pedido reencaminhado é igual ao configurado para o back-end selecionado. Leia [cabeçalho de anfitrião de back-end](front-door-backend-pool.md#hostheader) para saber o que ele faz e como pode configurá-lo.

A parte poderosas de reescrita de URL através do caminho de encaminhamento personalizado é que ele copiará qualquer parte do caminho de entrada que corresponde a um caminho de carateres universais para o caminho reencaminhado (esses segmentos de caminho são os **verde** segmentos no exemplo abaixo):
</br>
![Reescrita de URL de porta de entrada do Azure][1]

## <a name="url-rewrite-example"></a>Exemplo de reescrita de URL
Considere uma regra de roteamento com os seguintes anfitriões de front-end e os caminhos configurados:

| Anfitriões      | Caminhos       |
|------------|-------------|
| www\.contoso.com | /\*         |
|            | /foo        |
|            | /foo/\*     |
|            | /foo/bar/\* |

A primeira coluna da tabela abaixo mostra exemplos de pedidos recebidos e a segunda coluna mostra o que seria a rota correspondente "mais específica" de "Caminho".  As colunas de terceiro e subsequentes da primeira linha da tabela são exemplos de configurado **caminhos de reencaminhamento personalizado**, com o restante das linhas existentes nessas colunas que representam os exemplos de qual o caminho da solicitação reencaminhada seria se correspondentes para o pedido nessa linha.

Por exemplo, se Lemos na segunda linha, o código está dizendo que para solicitação de entrada `www.contoso.com/sub`, se o caminho de encaminhamento personalizado era `/`, em seguida, o caminho reencaminhado seria `/sub`. Se o caminho de encaminhamento personalizado era `/fwd/`, em seguida, o caminho reencaminhado seria `/fwd/sub`. E assim por diante, para as restantes colunas. O **enfatizadas** partes de caminhos abaixo representam as partes que fazem parte da correspondência de carateres universais.


| Solicitação de entrada       | Caminho de correspondência de mais específica | /          | /FWD/          | /foo/          | /foo/barra /          |
|------------------------|--------------------------|------------|----------------|----------------|--------------------|
| www\.contoso.com/            | /\*                      | /          | /FWD/          | /foo/          | /foo/barra /          |
| www\.contoso.com/**sub**     | /\*                      | /**sub**   | /fwd/**sub**   | /foo/**sub**   | /foo/bar/**sub**   |
| www\.contoso.com/**a/b/c**   | /\*                      | /**a/b/c** | /fwd/**a/b/c** | /foo/**a/b/c** | /foo/bar/**a/b/c** |
| www\.contoso.com/foo         | /foo                     | /          | /FWD/          | /foo/          | /foo/barra /          |
| www\.contoso.com/foo/        | /foo/\*                  | /          | /FWD/          | /foo/          | /foo/barra /          |
| www\.contoso.com/foo/**bar** | /foo/\*                  | /**bar**   | /fwd/**bar**   | /foo/**bar**   | /foo/bar/**bar**   |


## <a name="optional-settings"></a>Definições opcionais
Existem definições opcionais adicionais, que também pode especificar para as definições de regra de encaminhamento determinado:

* **Configuração da cache** - se desativada ou não especificada, em seguida, pedidos que correspondam a esta regra de encaminhamento não irão tentar utilizar conteúdo em cache e em vez disso, serão sempre buscar o back-end. Leia mais sobre [colocação em cache com a porta da frente](front-door-caching.md).



## <a name="next-steps"></a>Passos Seguintes

- Saiba como [criar um Front Door](quickstart-create-front-door.md).
- Saiba [como funciona o Front Door](front-door-routing-architecture.md).

<!--Image references-->
[1]: ./media/front-door-url-rewrite/front-door-url-rewrite-example.jpg