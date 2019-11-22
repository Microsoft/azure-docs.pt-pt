---
title: Usando a CDN do Azure com CORS | Microsoft Docs
description: Saiba como usar a CDN (rede de distribuição de conteúdo) do Azure com o CORS (compartilhamento de recursos entre origens).
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
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74278115"
---
# <a name="using-azure-cdn-with-cors"></a>Usando a CDN do Azure com CORS
## <a name="what-is-cors"></a>O que é CORS?
O CORS (compartilhamento de recursos entre origens) é um recurso HTTP que permite que um aplicativo Web em execução em um domínio acesse recursos em outro domínio. Para reduzir a possibilidade de ataques de script entre sites, todos os navegadores da Web modernos implementam uma restrição de segurança conhecida como [política de mesma origem](https://www.w3.org/Security/wiki/Same_Origin_Policy).  Isso impede que uma página da Web chame APIs em um domínio diferente.  O CORS fornece uma maneira segura de permitir que uma origem (o domínio de origem) chame APIs em outra origem.

## <a name="how-it-works"></a>Como funciona
Há dois tipos de solicitações CORS, *solicitações simples* e *solicitações complexas.*

### <a name="for-simple-requests"></a>Para solicitações simples:

1. O navegador envia a solicitação de CORS com um cabeçalho de solicitação HTTP de **origem** adicional. O valor desse cabeçalho é a origem que servia a página pai, que é definida como a combinação de *protocolo,* *domínio* e *porta.*  Quando uma página de HTTPS\://www.contoso.com tenta acessar os dados de um usuário na origem fabrikam.com, o seguinte cabeçalho de solicitação é enviado para fabrikam.com:

   `Origin: https://www.contoso.com`

2. O servidor pode responder com qualquer um dos seguintes:

   * Um cabeçalho **Access-Control-Allow-Origin** em sua resposta indicando qual site de origem é permitido. Por exemplo:

     `Access-Control-Allow-Origin: https://www.contoso.com`

   * Um código de erro HTTP, como 403, se o servidor não permitir a solicitação entre origens depois de verificar o cabeçalho de origem

   * Um cabeçalho **Access-Control-Allow-Origin** com um curinga que permite todas as origens:

     `Access-Control-Allow-Origin: *`

### <a name="for-complex-requests"></a>Para solicitações complexas:

Uma solicitação complexa é uma solicitação CORS em que o navegador é necessário para enviar uma *solicitação de simulação* (ou seja, uma investigação preliminar) antes de enviar a solicitação de CORS real. A solicitação de simulação solicita a permissão do servidor se a solicitação CORS original pode continuar e é uma `OPTIONS` solicitação para a mesma URL.

> [!TIP]
> Para obter mais detalhes sobre fluxos de CORS e armadilhas comuns, exiba o [guia para CORS para APIs REST](https://www.moesif.com/blog/technical/cors/Authoritative-Guide-to-CORS-Cross-Origin-Resource-Sharing-for-REST-APIs/).
>
>

## <a name="wildcard-or-single-origin-scenarios"></a>Cenários de curingas ou de origem única
O CORS na CDN do Azure funcionará automaticamente sem nenhuma configuração adicional quando o cabeçalho **Access-Control-permitir-Origin** for definido como curinga (*) ou uma única origem.  A CDN armazenará em cache a primeira resposta e as solicitações subsequentes usarão o mesmo cabeçalho.

Se já tiverem sido feitas solicitações à CDN antes da definição do CORS em sua origem, você precisará limpar o conteúdo do seu conteúdo do ponto de extremidade para recarregar o conteúdo com o cabeçalho **Access-Control-Allow-Origin** .

## <a name="multiple-origin-scenarios"></a>Cenários de várias origens
Se você precisar permitir que uma lista específica de origens seja permitida para CORS, as coisas ficarão um pouco mais complicadas. O problema ocorre quando a CDN armazena em cache o cabeçalho **Access-Control-Allow-Origin** da primeira origem CORS.  Quando uma origem CORS diferente fizer uma solicitação subsequente, a CDN servirá para o cabeçalho **Access-Control-Allow-Origin** armazenado em cache, que não corresponderá.  Há várias maneiras de corrigir isso.

### <a name="azure-cdn-standard-profiles"></a>Perfis padrão da CDN do Azure
No Azure CDN Standard da Microsoft, você pode criar uma regra no [mecanismo de regras padrão](cdn-standard-rules-engine-reference.md) para verificar o cabeçalho de **origem** na solicitação. Se for uma origem válida, sua regra definirá o cabeçalho **Access-Control-Allow-Origin** com o valor desejado. Nesse caso, o cabeçalho **acesso-controle-permitir-Origin** do servidor de origem do arquivo é ignorado e o mecanismo de regras da CDN gerencia completamente as origens CORS permitidas.

![Exemplo de regras com o mecanismo de regras padrão](./media/cdn-cors/cdn-standard-cors.png)

> [!TIP]
> Você pode adicionar ações adicionais à sua regra para modificar cabeçalhos de resposta adicionais, como **Access-Control-Allow-Methods**.
> 

No **Azure CDN Standard da Akamai**, o único mecanismo para permitir várias origens sem o uso da origem curinga é usar o cache de [cadeia de caracteres de consulta](cdn-query-string.md). Habilite a configuração de cadeia de caracteres de consulta para o ponto de extremidade CDN e, em seguida, use uma cadeia de consulta exclusiva para solicitações de cada domínio permitido. Isso fará com que o CDN faça o cache de um objeto separado para cada cadeia de caracteres de consulta exclusiva. No entanto, essa abordagem não é ideal, pois resultará em várias cópias do mesmo arquivo em cache na CDN.  

### <a name="azure-cdn-premium-from-verizon"></a>CDN Premium do Azure da Verizon
Usando o mecanismo de regras do Verizon Premium, você precisará [criar uma regra](cdn-rules-engine.md) para verificar o cabeçalho de **origem** na solicitação.  Se for uma origem válida, sua regra definirá o cabeçalho **Access-Control-Allow-Origin** com a origem fornecida na solicitação.  Se a origem especificada no cabeçalho de **origem** não for permitida, a regra deverá omitir o cabeçalho **Access-Control-Allow-Origin** , que fará com que o navegador rejeite a solicitação. 

Há duas maneiras de fazer isso com o mecanismo de regras Premium. Em ambos os casos, o cabeçalho **acesso-controle-permitir-Origin** do servidor de origem do arquivo é ignorado e o mecanismo de regras da CDN gerencia completamente as origens CORS permitidas.

#### <a name="one-regular-expression-with-all-valid-origins"></a>Uma expressão regular com todas as origens válidas
Nesse caso, você criará uma expressão regular que inclui todas as origens que você deseja permitir: 

    https?:\/\/(www\.contoso\.com|contoso\.com|www\.microsoft\.com|microsoft.com\.com)$

> [!TIP]
> A **CDN Premium do Azure da Verizon** usa [expressões regulares compatíveis com Perl](https://pcre.org/) como seu mecanismo para expressões regulares.  Você pode usar uma ferramenta como [expressões regulares 101](https://regex101.com/) para validar sua expressão regular.  Observe que o caractere "/" é válido em expressões regulares e não precisa ser escapado, no entanto, escapar esse caractere é considerado uma prática recomendada e é esperado por alguns validadores de Regex.
> 
> 

Se a expressão regular for correspondente, a regra substituirá o cabeçalho **Access-Control-Allow-Origin** (se houver) da origem pela origem que enviou a solicitação.  Você também pode adicionar cabeçalhos CORS adicionais, como **Access-Control-Allow-Methods**.

![Exemplo de regras com expressão regular](./media/cdn-cors/cdn-cors-regex.png)

#### <a name="request-header-rule-for-each-origin"></a>Regra de cabeçalho de solicitação para cada origem.
Em vez de expressões regulares, você pode criar uma regra separada para cada origem que você deseja permitir usando a [condição de correspondência](/previous-versions/azure/mt757336(v=azure.100)#match-conditions)curinga do cabeçalho de solicitação. Assim como acontece com o método de expressão regular, o mecanismo de regras apenas define os cabeçalhos CORS. 

![Exemplo de regras sem expressão regular](./media/cdn-cors/cdn-cors-no-regex.png)

> [!TIP]
> No exemplo acima, o uso do caractere curinga * diz ao mecanismo de regras para corresponder a HTTP e HTTPS.
> 
> 



