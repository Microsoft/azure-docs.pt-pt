---
title: Utilização de CDN Azure com CORS / Microsoft Docs
description: Saiba como utilizar a Rede de Entrega de Conteúdos Azure (CDN) para com a Partilha de Recursos de Origem Cruzada (CORS).
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
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 169de21b6dbdafaaeff64e315daa104f3b6faadd
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74278115"
---
# <a name="using-azure-cdn-with-cors"></a>Utilização de CDN Azure com CORS
## <a name="what-is-cors"></a>O que é o CORS?
CORS (Cross Origin Resource Sharing) é uma funcionalidade HTTP que permite que uma aplicação web que funciona sob um domínio aceda a recursos noutro domínio. A fim de reduzir a possibilidade de ataques de scripts transversais, todos os navegadores web modernos implementam uma restrição de segurança conhecida como [política de mesma origem.](https://www.w3.org/Security/wiki/Same_Origin_Policy)  Isto impede que uma página web chame APIs num domínio diferente.  O CORS fornece uma forma segura de permitir que uma origem (o domínio de origem) chame APIs de outra origem.

## <a name="how-it-works"></a>Como funciona
Existem dois tipos de pedidos cors, *pedidos simples* e *pedidos complexos.*

### <a name="for-simple-requests"></a>Para pedidos simples:

1. O navegador envia o pedido cors com um cabeçalho adicional de pedido **origin** HTTP. O valor deste cabeçalho é a origem que serviu a página-mãe, que é definida como a combinação de *protocolo,* *domínio* e *porta.*  Quando uma página\:de https/www.contoso.com tenta aceder aos dados de um utilizador na origem fabrikam.com, o seguinte cabeçalho de pedido seria enviado para fabrikam.com:

   `Origin: https://www.contoso.com`

2. O servidor pode responder com qualquer um dos seguintes:

   * Um cabeçalho de **acesso-controlo-acesso-acesso-origem** na sua resposta indicando qual o local de origem permitido. Por exemplo:

     `Access-Control-Allow-Origin: https://www.contoso.com`

   * Um código de erro HTTP, como o 403, se o servidor não permitir o pedido de origem cruzada após verificar o cabeçalho Origin

   * Um cabeçalho de **acesso-controlo-acesso-acesso-origem** com um wildcard que permite todas as origens:

     `Access-Control-Allow-Origin: *`

### <a name="for-complex-requests"></a>Para pedidos complexos:

Um pedido complexo é um pedido CORS onde o navegador é obrigado a enviar um pedido de *pré-voo* (isto é, uma sonda preliminar) antes de enviar o pedido real cors. O pedido de pré-voo pede permissão ao servidor `OPTIONS` se o pedido original do CORS pode prosseguir e é um pedido para o mesmo URL.

> [!TIP]
> Para mais detalhes sobre os fluxos cors e armadilhas comuns, consulte o Guia para O [CORS para APIs REST](https://www.moesif.com/blog/technical/cors/Authoritative-Guide-to-CORS-Cross-Origin-Resource-Sharing-for-REST-APIs/).
>
>

## <a name="wildcard-or-single-origin-scenarios"></a>Cenários wildcard ou de origem única
CorS no Azure CDN funcionará automaticamente sem configuração adicional quando o cabeçalho **access-Control-Allow-Origin** estiver definido para wildcard (*) ou uma única origem.  O CDN cache a primeira resposta e os pedidos subsequentes usarão o mesmo cabeçalho.

Se já foram feitos pedidos ao CDN antes de o CORS ser definido na sua origem, terá de expurgar o conteúdo do seu conteúdo final para recarregar o conteúdo com o cabeçalho **access-Control-Allow-Origin.**

## <a name="multiple-origin-scenarios"></a>Cenários de origem múltipla
Se precisar de permitir que uma lista específica de origens seja permitida para o CORS, as coisas ficam um pouco mais complicadas. O problema ocorre quando o CDN coloca o cabeçalho de **acesso-controlo-acesso-acesso-origem** para a primeira origem CORS.  Quando uma origem CORS diferente faz um pedido subsequente, o CDN servirá o cabeçalho de **acesso-controlo-acesso-origem** em cache, que não corresponderá.  Há várias maneiras de corrigir isto.

### <a name="azure-cdn-standard-profiles"></a>Perfis padrão Azure CDN
No Azure CDN Standard da Microsoft, pode criar uma regra no [motor standard](cdn-standard-rules-engine-reference.md) regras para verificar o cabeçalho **Origin** no pedido. Se for uma origem válida, a sua regra definirá o cabeçalho de **acesso-controlo-acesso-acesso-origem** com o valor desejado. Neste caso, o cabeçalho de **acesso-controlo-acesso-acesso-origem** do servidor de origem do ficheiro é ignorado e o motor de regras do CDN gere completamente as origens cors permitidas.

![Regras exemplo com motor de regras padrão](./media/cdn-cors/cdn-standard-cors.png)

> [!TIP]
> Pode adicionar ações adicionais à sua regra para modificar cabeçalhos de resposta adicionais, tais como métodos de **acesso-controlo-permitir-métodos**.
> 

No **Azure CDN Standard da Akamai,** o único mecanismo que permite múltiplas origens sem a utilização da origem wildcard é utilizar o [cache de cordas](cdn-query-string.md)de consulta . Ative a definição de corda de consulta para o ponto final do CDN e, em seguida, use uma corda de consulta única para pedidos de cada domínio permitido. Fazê-lo resultará na cdn cache um objeto separado para cada corda de consulta única. Esta abordagem não é, no entanto, ideal, pois resultará em várias cópias do mesmo ficheiro em cache no CDN.  

### <a name="azure-cdn-premium-from-verizon"></a>Azure CDN Premium de Verizon
Utilizando o motor de regras Verizon Premium, terá de [criar uma regra](cdn-rules-engine.md) para verificar o cabeçalho **Origin** no pedido.  Se for uma origem válida, a sua regra definirá o cabeçalho de **acesso-controlo-acesso-acesso-origem** com a origem fornecida no pedido.  Se a origem especificada no cabeçalho **Origin** não for permitida, a sua regra deve omitir o cabeçalho **access-Control-Allow-Origin,** o que fará com que o navegador rejeite o pedido. 

Há duas maneiras de fazê-lo com o motor premium regras. Em ambos os casos, o cabeçalho de **acesso-controlo-acesso-acesso-origem** do servidor de origem do ficheiro é ignorado e o motor de regras do CDN gere completamente as origens cors permitidas.

#### <a name="one-regular-expression-with-all-valid-origins"></a>Uma expressão regular com todas as origens válidas
Neste caso, criará uma expressão regular que inclui todas as origens que pretende permitir: 

    https?:\/\/(www\.contoso\.com|contoso\.com|www\.microsoft\.com|microsoft.com\.com)$

> [!TIP]
> **O Azure CDN Premium da Verizon** utiliza [expressões regulares perl compatíveis](https://pcre.org/) como motor para expressões regulares.  Pode utilizar uma ferramenta como [Expressões Regulares 101](https://regex101.com/) para validar a sua expressão regular.  Note que o personagem "/" é válido em expressões regulares e não precisa de ser escapado, no entanto, escapar a esse personagem é considerado uma melhor prática e é esperado por alguns validadores regex.
> 
> 

Se a expressão regular corresponder, a sua regra substituirá o cabeçalho de **acesso-controlo-acesso-acesso-origem** (se houver) da origem com a origem que enviou o pedido.  Também pode adicionar cabeçalhos CORS adicionais, tais como métodos de **acesso-controlo-de-acesso.**

![Regras exemplo com expressão regular](./media/cdn-cors/cdn-cors-regex.png)

#### <a name="request-header-rule-for-each-origin"></a>Peça regra do cabeçalho para cada origem.
Em vez de expressões regulares, pode, em vez disso, criar uma regra separada para cada origem que deseja permitir utilizando a condição de [jogo](/previous-versions/azure/mt757336(v=azure.100)#match-conditions) **do 'Request Header Wildcard'.** Tal como acontece com o método de expressão regular, o motor de regras por si só define os cabeçalhos CORS. 

![Exemplo de regras sem expressão regular](./media/cdn-cors/cdn-cors-no-regex.png)

> [!TIP]
> No exemplo acima, a utilização do personagem wildcard * diz ao motor de regras para combinar tanto HTTP como HTTPS.
> 
> 



