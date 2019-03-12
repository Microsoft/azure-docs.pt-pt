---
title: Utilizar a CDN do Azure com CORS | Documentos da Microsoft
description: Saiba como utilizar o Azure entrega rede conteúdos (CDN) para com Cross-Origin Resource Sharing (CORS).
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: 86740a96-4269-4060-aba3-a69f00e6f14e
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 3c8fab85d71f5f81bbf81bc3dd7a22d6c0b7f11b
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57551845"
---
# <a name="using-azure-cdn-with-cors"></a>Utilizar a CDN do Azure com CORS
## <a name="what-is-cors"></a>O que é o CORS?
CORS (Cross Origin Resource partilha) é uma funcionalidade HTTP que permite que uma aplicação web em execução num domínio aceder a recursos em outro domínio. Para reduzir a possibilidade de ataques de script entre sites, todos os browsers modernos implementam uma restrição de segurança, conhecida como [política de mesma origem](https://www.w3.org/Security/wiki/Same_Origin_Policy).  Isto impede que uma página da web de chamar APIs num domínio diferente.  CORS fornece uma forma segura para permitir que uma origem (o domínio de origem) chame APIs de outra origem.

## <a name="how-it-works"></a>Como funciona
Existem dois tipos de solicitações CORS *solicitações simples* e *pedidos complexos.*

### <a name="for-simple-requests"></a>Para pedidos simples:

1. O browser envia o pedido CORS com adicional **origem** cabeçalho do pedido HTTP. O valor do cabeçalho é a origem que executou a página principal, que é definida como a combinação de *protocolo,* *domínio,* e *porta.*  Quando uma página de https://www.contoso.com tentativas de acesso a dados de um utilizador na origem fabrikam.com, o cabeçalho do pedido seguinte seriam enviadas para fabrikam.com:

   `Origin: https://www.contoso.com`

2. O servidor pode responder com qualquer um dos seguintes:

   * Uma **Access-Control-Allow-Origin** cabeçalho na respetiva resposta que indica qual site de origem é permitida. Por exemplo:

     `Access-Control-Allow-Origin: https://www.contoso.com`

   * Um código de erro HTTP, tais como 403, se o servidor não permite que o pedido de várias origens depois de verificar o cabeçalho de origem

   * Uma **Access-Control-Allow-Origin** cabeçalho com um caráter universal que permite que todas as origens:

     `Access-Control-Allow-Origin: *`

### <a name="for-complex-requests"></a>Para pedidos complexos:

Um pedido de complexo é um pedido CORS em que o navegador é necessário para enviar um *solicitação de simulação* (ou seja, uma pesquisa Preliminar) antes de enviar o pedido CORS real. Solicitação de simulação solicita a permissão de servidor se o CORS originais solicitar possa continuar e é um `OPTIONS` pedido para o mesmo URL.

> [!TIP]
> Para obter mais detalhes sobre fluxos CORS e armadilhas comuns, consulte a [guia para CORS para as APIs REST](https://www.moesif.com/blog/technical/cors/Authoritative-Guide-to-CORS-Cross-Origin-Resource-Sharing-for-REST-APIs/).
>
>

## <a name="wildcard-or-single-origin-scenarios"></a>Carateres universais ou cenários de origem única
CORS na CDN do Azure irá funcionar automaticamente sem qualquer configuração adicional quando o **Access-Control-Allow-Origin** cabeçalho está definido como caráter universal (*) ou uma origem única.  A CDN colocarão em cache a resposta inicial e as solicitações subseqüentes irão utilizar o mesmo cabeçalho.

Se já tiverem sido efetuados pedidos para a CDN antes de CORS ser definido na origem, terá de limpar o conteúdo no seu conteúdo do ponto final para recarregar o conteúdo com o **Access-Control-Allow-Origin** cabeçalho.

## <a name="multiple-origin-scenarios"></a>Vários cenários de origem
Se tiver de permitir uma lista específica de origens a permissão para CORS, as coisas ficam um pouco mais complicadas. O problema ocorre quando a CDN coloca em cache o **Access-Control-Allow-Origin** cabeçalho para a origem CORS primeiro.  Quando uma origem diferente do CORS faz um pedido subsequente, a CDN irá servir armazenadas em cache **Access-Control-Allow-Origin** cabeçalho, que não corresponde.  Existem várias formas para corrigir este problema.

### <a name="azure-cdn-premium-from-verizon"></a>CDN Premium do Azure da Verizon
A melhor forma de ativar esta opção é usar **CDN do Azure Premium da Verizon**, que expõe algumas funcionalidade avançada. 

Precisará [criar uma regra](cdn-rules-engine.md) para verificar a **origem** cabeçalho no pedido.  Se for uma origem válida, a regra irá definir o **Access-Control-Allow-Origin** cabeçalho com a origem fornecido no pedido.  Se a origem especificada na **Origin** cabeçalho não é permitido, sua regra deve omitir a **Access-Control-Allow-Origin** cabeçalho, o que fará com que o navegador rejeitar o pedido. 

Existem duas formas de fazer isso com o mecanismo de regras. Em ambos os casos, o **Access-Control-Allow-Origin** cabeçalho a partir do servidor de origem do ficheiro é ignorado e o motor de regras a CDN faz toda a gestão de origens permitidas do CORS.

#### <a name="one-regular-expression-with-all-valid-origins"></a>Uma expressão regular com todas as origens válidas
Neste caso, criará uma expressão regular que inclui todas as origens que pretende permitir: 

    https?:\/\/(www\.contoso\.com|contoso\.com|www\.microsoft\.com|microsoft.com\.com)$

> [!TIP]
> **O Azure CDN Premium da Verizon** usa [expressões regulares do Perl compatível](https://pcre.org/) como mecanismo para expressões regulares.  Pode usar uma ferramenta como o [101 de expressões regulares](https://regex101.com/) para validar a sua expressão regular.  Tenha em atenção que o caráter "/" é válido em expressões regulares e não precisa de caracteres de escape, no entanto, carateres de escape esse caractere é considerada uma prática recomendada e prevê-se por algum validadores de regex.
> 
> 

Se corresponder à expressão regular, sua regra irá substituir a **Access-Control-Allow-Origin** cabeçalho (se houver) a partir da origem com a origem que enviou o pedido.  Também pode adicionar cabeçalhos CORS adicionais, como **acesso--permitir o-métodos de controle**.

![Exemplo de regras com expressões regulares](./media/cdn-cors/cdn-cors-regex.png)

#### <a name="request-header-rule-for-each-origin"></a>Regra de cabeçalho de pedido para cada origem.
Em vez de expressões regulares, em vez disso, pode criar uma regra separada para cada origem que pretende permitir a utilizar o **curinga de cabeçalho do pedido** [corresponde à condição](https://msdn.microsoft.com/library/mt757336.aspx#Anchor_1). Tal como acontece com o método de expressão regular, o mecanismo de regras autónomo define os cabeçalhos CORS. 

![Exemplo de regras sem expressão regular](./media/cdn-cors/cdn-cors-no-regex.png)

> [!TIP]
> No exemplo acima, a utilização do caráter universal * informa o mecanismo de regras de acordo com HTTP e HTTPS.
> 
> 

### <a name="azure-cdn-standard-profiles"></a>Perfis de standard da CDN do Azure
Nos perfis standard da CDN do Azure (**CDN Standard do Microsoft Azure**, **CDN do Azure Standard da Akamai**, e **CDN do Azure Standard da Verizon**), o único mecanismo para permitir para várias origens, sem a utilização da origem de caráter universal é usar [colocação em cache de cadeia de caracteres de consulta](cdn-query-string.md). Ativar a definição da cadeia de caracteres de consulta para o ponto final CDN e, em seguida, utilize uma cadeia de caracteres de consulta exclusivas para pedidos de cada domínio permitido. Se o fizer, irá resultar na CDN colocação em cache um objeto separado para cada cadeia de consulta exclusivas. Essa abordagem não é ideal, no entanto, pois irá resultar em várias cópias do mesmo arquivo em cache na CDN.  

