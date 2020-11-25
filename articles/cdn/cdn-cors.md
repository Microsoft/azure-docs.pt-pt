---
title: Usando Azure CDN com CORS / Microsoft Docs
description: Saiba como utilizar a Rede de Entrega de Conteúdos Azure (CDN) com a Partilha de Recursos de Origem Cruzada (CORS).
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: 86740a96-4269-4060-aba3-a69f00e6f14e
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: f7edf790e526329dd285d03a31137a26220e52ee
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96018652"
---
# <a name="using-azure-cdn-with-cors"></a>Utilização de Azure CDN com CORS
## <a name="what-is-cors"></a>O que é o CORS?
O CORS (Partilha de Recursos de Várias Origens) é uma funcionalidade HTTP que permite a execução de uma aplicação Web num domínio para aceder a recursos noutro domínio. De forma a reduzir a possibilidade de ataques de scripts cross-site, todos os navegadores web modernos implementam uma restrição de segurança conhecida como [política de origem .](https://www.w3.org/Security/wiki/Same_Origin_Policy)  Isto impede que uma página web chame APIs em um domínio diferente.  O CORS fornece uma forma segura de permitir que uma origem (o domínio de origem) chame APIs noutra origem.

## <a name="how-it-works"></a>Como funciona
Existem dois tipos de pedidos de CORS, *pedidos simples* e *pedidos complexos.*

### <a name="for-simple-requests"></a>Para pedidos simples:

1. O navegador envia o pedido CORS com um cabeçalho adicional de pedido **Origin** HTTP. O valor deste cabeçalho é a origem que serviu a página-mãe, que é definida como a combinação de *protocolo,* *domínio* e *porta.*  Quando uma página de https \: //www.contoso.com tenta aceder aos dados de um utilizador na origem fabrikam.com, o seguinte cabeçalho de pedido seria enviado para fabrikam.com:

   `Origin: https://www.contoso.com`

2. O servidor pode responder com qualquer um dos seguintes:

   * Um **cabeçalho access-Control-Allow-Origin** na sua resposta indicando qual o local de origem permitido. Por exemplo:

     `Access-Control-Allow-Origin: https://www.contoso.com`

   * Um código de erro HTTP, como o 403, se o servidor não permitir o pedido de origem cruzada após verificar o cabeçalho Origin

   * Um **cabeçalho Access-Control-Allow-Origin** com um wildcard que permite todas as origens:

     `Access-Control-Allow-Origin: *`

### <a name="for-complex-requests"></a>Para pedidos complexos:

Um pedido complexo é um pedido de CORS onde o navegador é obrigado a enviar um *pedido de pré-voo* (isto é, uma sonda preliminar) antes de enviar o pedido real de CORS. O pedido de pré-voo solicita a permissão do servidor se o pedido original do CORS pode prosseguir e é um `OPTIONS` pedido para o mesmo URL.

> [!TIP]
> Para obter mais detalhes sobre os fluxos CORS e armadilhas comuns, consulte o [Guia do CORS para REST APIs](https://www.moesif.com/blog/technical/cors/Authoritative-Guide-to-CORS-Cross-Origin-Resource-Sharing-for-REST-APIs/).
>
>

## <a name="wildcard-or-single-origin-scenarios"></a>Cenários de origem ou wildcard ou de origem única
O CORS no Azure CDN funcionará automaticamente sem configuração adicional quando o cabeçalho **Access-Control-Allow-Origin** estiver definido para wildcard (*) ou uma única origem.  O CDN cache a primeira resposta e os pedidos subsequentes usarão o mesmo cabeçalho.

Se já foram feitos pedidos ao CDN antes de o CORS ser definido na sua origem, terá de expurgar o conteúdo do seu conteúdo de ponto final para recarregar o conteúdo com o cabeçalho **Access-Control-Allow-Origin.**

## <a name="multiple-origin-scenarios"></a>Cenários de origem múltipla
Se você precisa permitir uma lista específica de origens para CORS, as coisas ficam um pouco mais complicadas. O problema ocorre quando o CDN caches o cabeçalho **Access-Control-Allow-Origin** para a primeira origem CORS.  Quando uma origem CORS diferente fizer um pedido subsequente, o CDN servirá o cabeçalho cached **Access-Control-Allow-Origin,** que não corresponde.  Há várias formas de corrigir isto.

### <a name="azure-cdn-standard-profiles"></a>Perfis padrão Azure CDN
No Azure CDN Standard da Microsoft, pode criar uma regra no [motor de regras Standard](cdn-standard-rules-engine-reference.md) para verificar o cabeçalho **Origin** no pedido. Se for uma origem válida, a sua regra definirá o cabeçalho **Access-Control-Allow-Origin** com o valor pretendido. Neste caso, o cabeçalho **Access-Control-Allow-Origin** do servidor de origem do ficheiro é ignorado e o motor de regras do CDN gere completamente as origens permitidas do CORS.

![Exemplo de regras com motor de regras padrão](./media/cdn-cors/cdn-standard-cors.png)

> [!TIP]
> Pode adicionar ações adicionais à sua regra para modificar cabeçalhos de resposta adicionais, tais como **Acesso-Control-Allow-Methods**.
> 

No **Azure CDN Standard da Akamai,** o único mecanismo que permite múltiplas origens sem a utilização da origem wildcard é utilizar o [caching](cdn-query-string.md)de cordas de consulta . Ativar a definição de cadeia de consulta para o ponto final do CDN e, em seguida, utilizar uma cadeia de consulta única para pedidos de cada domínio permitido. Ao fazê-lo, o CDN irá caching um objeto separado para cada cadeia de consulta única. Esta abordagem não é, no entanto, ideal, uma vez que resultará em múltiplas cópias do mesmo ficheiro em cache no CDN.  

### <a name="azure-cdn-premium-from-verizon"></a>Azure CDN Premium de Verizon
Utilizando o motor de regras Verizon Premium, terá de [criar uma regra](./cdn-verizon-premium-rules-engine.md) para verificar o cabeçalho **Origin** a pedido.  Se for uma origem válida, a sua regra definirá o cabeçalho **Access-Control-Allow-Origin** com a origem fornecida no pedido.  Se a origem especificada no cabeçalho **Origin** não for permitida, a sua regra deverá omitir o cabeçalho **Access-Control-Allow-Origin,** o que fará com que o navegador rejeite o pedido. 

Há duas maneiras de o fazer com o motor de regras Premium. Em ambos os casos, o cabeçalho **Access-Control-Allow-Origin** do servidor de origem do ficheiro é ignorado e o motor de regras do CDN gere completamente as origens permitidas do CORS.

#### <a name="one-regular-expression-with-all-valid-origins"></a>Uma expressão regular com todas as origens válidas
Neste caso, irá criar uma expressão regular que inclui todas as origens que pretende permitir: 

```http
https?:\/\/(www\.contoso\.com|contoso\.com|www\.microsoft\.com|microsoft.com\.com)$
```

> [!TIP]
> **O Azure CDN Premium da Verizon** utiliza [expressões regulares perl compatíveis](https://pcre.org/) como motor para expressões regulares.  Pode utilizar uma ferramenta como [As Expressões Regulares 101](https://regex101.com/) para validar a sua expressão regular.  Note que o caráter "/" é válido em expressões regulares e não precisa de ser escapado, no entanto, escapar a esse personagem é considerado uma boa prática e é esperado por alguns validadores da Regex.
> 
> 

Se a expressão regular corresponder, a sua regra substituirá o cabeçalho **Access-Control-Allow-Origin** (se houver) da origem com a origem que enviou o pedido.  Também pode adicionar cabeçalhos CORS adicionais, tais como **Acesso-Control-Allow-Methods**.

![Exemplo de regras com expressão regular](./media/cdn-cors/cdn-cors-regex.png)

#### <a name="request-header-rule-for-each-origin"></a>Solicite a regra do cabeçalho para cada origem.
Em vez de expressões regulares, pode, em vez disso, criar uma regra separada para cada origem que pretende permitir utilizar a condição de [correspondência](/previous-versions/azure/mt757336(v=azure.100)#match-conditions) **do Número de Pedidos Wildcard** . Tal como acontece com o método de expressão regular, o motor de regras define apenas os cabeçalhos CORS. 

![Exemplo de regras sem expressão regular](./media/cdn-cors/cdn-cors-no-regex.png)

> [!TIP]
> No exemplo acima, a utilização do caractere wildcard * diz ao motor de regras para combinar tanto COM HTTP como HTTPS.
> 
>