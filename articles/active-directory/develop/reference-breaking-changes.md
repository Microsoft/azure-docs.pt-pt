---
title: Referência de alterações de última hora de Active Directory do Azure | Documentos da Microsoft
description: Saiba mais sobre as alterações feitas para os protocolos do Azure AD que podem afetar a sua aplicação.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 68517c83-1279-4cc7-a7c1-c7ccc3dbe146
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/02/2018
ms.author: celested
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2fcc400f952cc89f5fb4bf6e8d6f0f331483868e
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/25/2019
ms.locfileid: "58401303"
---
# <a name="whats-new-for-authentication"></a>O que há de novo para a autenticação? 

>Seja notificado sobre as atualizações para esta página. Simplesmente adicione [este URL](https://docs.microsoft.com/api/search/rss?search=%22whats%20new%20for%20authentication%22&locale=en-us) para seu RSS feed leitor.

O sistema de autenticação altera e adiciona recursos de forma contínua para melhorar a segurança e conformidade com os padrões. Para se manter atualizado com os desenvolvimentos mais recentes, este artigo fornece informações sobre os seguintes detalhes:

- Funcionalidades mais recentes
- Problemas conhecidos
- Alterações de protocolo
- Funcionalidades preteridas

> [!TIP] 
> Esta página é atualizada regularmente, por isso, visite com frequência. Salvo indicação em contrário, estas alterações apenas são colocadas no local para aplicações recentemente registadas.  

## <a name="upcoming-changes"></a>Alterações futuras

Nenhum agendada neste momento. 

## <a name="march-2019"></a>Março de 2019

### <a name="looping-clients-will-be-interrupted"></a>Os clientes de loop será interrompido

**Data efetiva**: 25 de Março de 2019

**Pontos finais afetados**: V1.0 e v2.0

**Protocolo afetado**: Todos os fluxos

Aplicativos cliente, às vezes, podem misbehave, emitir centenas do pedido de início de sessão mesmo durante um curto período de tempo.  Estes pedidos podem ou não ser concluída com êxito, mas todos eles contribuem para a experiência de usuário insatisfatória e o cliente de cargas de trabalho para o IDP, aumentando a latência para todos os utilizadores e reduzindo a disponibilidade do IDP.  Estas aplicações estão a funcionar fora dos limites de utilização normal e devem ser atualizadas para se comportar corretamente.  

Os clientes que emitem pedidos de duplicação várias vezes serão enviados um `invalid_grant` erro: `AADSTS50196: The server terminated an operation because it encountered a loop while processing a request`. 

A maioria dos clientes não terá de alterar o comportamento para evitar este erro.  Apenas a clientes mal configurada (aquelas sem tokens em cache ou aqueles que já apresentam loops de linha de comandos) vão ser afetada por este erro.  Os clientes são controlados numa base por instância localmente (por meio de cookie) nos seguintes fatores:

* Sugestão de utilizador, se aplicável

* Âmbitos ou recurso que está a ser pedido

* ID de Cliente

* URI de Redirecionamento

* Tipo de resposta e o modo

Aplicações fazer várias solicitações (15 +) num curto período de tempo (5 minutos) receberão uma `invalid_grant` erro explicando o que eles se estiver fazendo loop.  Os tokens sendo solicitados têm durações suficientemente longa duração (mínimo de 10 minutos, 60 minutos por predefinição), então, repetido pedidos durante este período de tempo são desnecessários.  

Todas as aplicações devem processar `invalid_grant` por que mostra uma linha de comandos interativa, em vez de pedir um token a silenciosamente.  Para evitar este erro, os clientes devem certificar-se de que eles são corretamente a colocação em cache os tokens que recebem.


## <a name="october-2018"></a>Outubro de 2018

### <a name="authorization-codes-can-no-longer-be-reused"></a>Códigos de autorização já não podem ser reutilizados

**Data efetiva**: 15 de Novembro de 2018

**Pontos finais afetados**: V1.0 e v2.0

**Protocolo afetado**: [Fluxo de código](v2-oauth2-auth-code-flow.md)

A partir de 15 de Novembro de 2018, do Azure AD irá parar abertos ao recebimento de códigos de autenticação utilizados anteriormente para aplicações. Esta alteração de segurança ajuda a trazer do Azure AD em conformidade com a especificação de OAuth e será imposta em pontos finais de ambos o v1 e v2.

Se a sua aplicação reutiliza os códigos de autorização para obter os tokens de vários recursos, recomendamos que utilize o código para obter um token de atualização e, em seguida, utilizar esse token de atualização para adquirir tokens adicionais para outros recursos. Códigos de autorização só podem ser utilizados uma vez, mas os tokens de atualização podem ser utilizadas várias vezes em vários recursos. Os novos aplicativos que tentam reutilizar um código de autenticação durante o fluxo de código do OAuth obterá um erro de invalid_grant.

Para obter mais informações sobre os tokens de atualização, consulte [atualizar os tokens de acesso](v1-protocols-oauth-code.md#refreshing-the-access-tokens).  Se utilizar a ADAL ou MSAL, isso é feito automaticamente pela biblioteca - substituir a segunda instância do AcquireTokenByAuthorizationCodeAsync com 'AcquireTokenSilentAsync'. 

## <a name="may-2018"></a>Maio de 2018

### <a name="id-tokens-cannot-be-used-for-the-obo-flow"></a>Tokens de ID não podem ser utilizados para o fluxo OBO

**Data**: 1 de Maio de 2018

**Pontos finais afetados**: V1.0 e v2.0

**Protocolos afetados**: Fluxo implícito e [fluxo OBO](v1-oauth2-on-behalf-of-flow.md)

Após 1 de Maio de 2018, id_tokens não pode ser utilizado como a asserção num fluxo de OBO novos aplicativos. Tokens de acesso devem ser utilizados em vez disso, para proteger APIs, até mesmo entre um cliente e a camada intermediária do mesmo aplicativo. Aplicações registadas antes de 1 de Maio de 2018 continuarão a funcionar e ser capaz de id_tokens para um token de acesso do exchange No entanto, esse padrão não é considerado uma prática recomendada.

Para contornar esta alteração, pode fazer o seguinte:

1. Crie uma API Web para a sua aplicação, com um ou mais âmbitos. Este ponto de entrada explícito permite melhor controlo detalhado e segurança.
1. No manifesto da sua aplicação, no [portal do Azure](https://portal.azure.com) ou o [portal de registo de aplicação](https://apps.dev.microsoft.com), certifique-se de que a aplicação tem permissões para emitir tokens de acesso via o fluxo implícito. Isso é controlado por meio do `oauth2AllowImplicitFlow` chave.
1. Quando a aplicação cliente solicita um id_token via `response_type=id_token`, também, solicitar um token de acesso (`response_type=token`) para a API Web criado acima. Portanto, ao utilizar o ponto final v2.0 a `scope` parâmetro deve ser semelhante à `api://GUID/SCOPE`. No ponto de extremidade v1.0, o `resource` parâmetro deve ser a URI da web API de aplicação.
1. Passe esse token de acesso para a camada intermediária em vez do id_token.  
