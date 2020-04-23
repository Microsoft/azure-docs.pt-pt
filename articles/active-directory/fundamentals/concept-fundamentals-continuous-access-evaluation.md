---
title: Avaliação contínua de acesso em Azure AD
description: Responder às mudanças no estado de utilizador mais rapidamente com avaliação contínua de acesso em Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 04/21/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jlu
ms.collection: M365-identity-device-management
ms.openlocfilehash: e5b70c11cd6bc24f945b437decf22586cfb97557
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81873306"
---
# <a name="continuous-access-evaluation"></a>Avaliação contínua de acesso

Os serviços da Microsoft, como o Azure Ative Directory (Azure AD) e o Office 365, utilizam padrões e protocolos abertos para maximizar a interoperabilidade. Um dos mais críticos é o Open ID Connect (OIDC). Quando uma aplicação de cliente como o Outlook se conecta a um serviço como o Exchange Online, os pedidos da API são autorizados usando fichas de acesso OAuth 2.0. Por padrão, as fichas de acesso são válidas por uma hora. Quando expiram, o cliente é redirecionado para a Azure AD para os refrescar. Isto também proporciona uma oportunidade para reavaliar as políticas de acesso ao utilizador – podemos optar por não refrescar o símbolo por causa de uma política de Acesso Condicional, ou porque o utilizador foi desativado no diretório. 

Ouvimos o feedback esmagador dos nossos clientes: um atraso de uma hora devido ao tempo de vida simbólico para reaplicar as políticas de Acesso Condicional e as alterações no estado do utilizador (por exemplo: deficientes devido a despedimentos) não é suficiente.

A Microsoft tem participado precocemente na iniciativa Do Protocolo de Avaliação de Acesso Contínuo (CAEP) no âmbito do grupo de trabalho [De Sinais e Eventos Partilhados](https://openid.net/wg/sse/) da Fundação OpenID. Os fornecedores de identidade e as partes que dependem poderão alavancar os eventos e sinais de segurança definidos pelo grupo de trabalho para reautorizar ou encerrar o acesso. É um trabalho emocionante e melhorará a segurança em muitas plataformas e aplicações.

Como os benefícios de segurança são tão grandes, estamos a implementar uma implementação inicial específica da Microsoft paralelamente ao nosso trabalho contínuo dentro dos organismos de normalização. Enquanto trabalhamos para implementar estas capacidades de avaliação contínua de acesso (CAE) através dos serviços da Microsoft, aprendemos muito e estamos a partilhar esta informação com a comunidade de padrões. Esperamos que a nossa experiência em implantação possa ajudar a informar um padrão ainda melhor da indústria e estamos empenhados em implementar essa norma uma vez ratificada, permitindo que todos os serviços participantes beneficiem.

## <a name="how-does-cae-work-in-microsoft-services"></a>Como funciona o CAE nos serviços da Microsoft?

Estamos a focar a nossa implementação inicial de avaliação contínua de acesso ao Exchange and Teams. Esperamos expandir o suporte a outros serviços da Microsoft no futuro. Começaremos a permitir uma avaliação contínua de acesso apenas para inquilinos sem políticas de Acesso Condicional. Usaremos as nossas aprendizagens a partir desta fase do CAE para informar o nosso lançamento em curso de CAE.

## <a name="service-side-requirements"></a>Requisitos do lado do serviço

A avaliação contínua do acesso é implementada permitindo que os serviços (fornecedores de recursos) subscrevam eventos críticos em Azure AD para que esses eventos possam ser avaliados e aplicados em tempo real. Os seguintes eventos serão aplicados neste lançamento inicial do CAE:

- A Conta do Utilizador é eliminada ou desativada
- A palavra-passe para um utilizador é alterada ou redefinida
- Administrador revoga explicitamente todos os tokens de atualização para um utilizador
- Risco elevado de utilizador detetado pela Azure AD Identity Protection

No futuro esperamos adicionar mais eventos, incluindo eventos como localização e alterações de estado de dispositivo. **Embora o nosso objetivo seja que a aplicação seja instantânea, em alguns casos, a latência de até 15 minutos pode ser observada devido ao tempo**de propagação do evento . 

## <a name="client-side-claim-challenge"></a>Desafio de reivindicação do lado do cliente

Antes da avaliação contínua do acesso, os clientes tentavam sempre reproduzir o token de acesso da sua cache desde que não expirassem. Com a CAE, estamos a introduzir um novo caso de que um fornecedor de recursos pode rejeitar um símbolo mesmo quando não está expirado. A fim de informar os clientes para contornarem a sua cache, mesmo que as fichas em cache não tenham expirado, introduzimos um mecanismo chamado desafio de **reclamação.** CaE requer uma atualização do cliente para entender o desafio de reclamação. A versão mais recente das seguintes aplicações abaixo do desafio de reclamação de suporte:

- Perspetivas para Windows 
- Perspetivas iOS 
- Outlook Android 
- Outlook Mac 
- Equipas para Windows
- Equipas iOS 
- Equipas Android 
- Equipas Mac 

## <a name="token-lifetime"></a>Duração do Token

Como o risco e a política são avaliados em tempo real, os clientes que negoceiam sessões de avaliação contínua de acesso dependerão do CAE em vez das políticas de acesso estático existentes, o que significa que a política de vida token configurável não será mais honrada para clientes capazes de CAE que negoceiam sessões conscientes do CAE.

Aumentaremos o acesso ao tempo útil a 24 horas nas sessões cae. A revogação é impulsionada por acontecimentos críticos e avaliação de políticas, e não por um período de tempo arbitrário. Esta alteração aumenta a estabilidade das suas aplicações sem afetar a sua postura de segurança. 

## <a name="example-flows"></a>Fluxos de exemplo

### <a name="user-revocation-event-flow"></a>Fluxo de eventos de revogação do utilizador:

![Fluxo de evento de revogação do utilizador](./media/concept-fundamentals-continuous-access-evaluation/user-revocation-event-flow.png)

1. Um cliente capaz de CAE apresenta credenciais ou um token refrescante para a AAD pedindo um sinal de acesso para algum recurso.
1. Um sinal de acesso é devolvido juntamente com outros artefactos ao cliente.
1. Um Administrador revoga explicitamente todas as fichas de [atualização para o utilizador](https://docs.microsoft.com/powershell/module/azuread/revoke-azureaduserallrefreshtoken?view=azureadps-2.0). Será enviado um evento de revogação ao fornecedor de recursos da Azure AD.
1. É apresentado um sinal de acesso ao fornecedor de recursos. O fornecedor de recursos avalia a validade do símbolo e verifica se existe algum evento de revogação para o utilizador. O fornecedor de recursos utiliza estas informações para decidir conceder ou não acesso ao recurso.
1. Neste caso, o fornecedor de recursos nega o acesso e envia um desafio de reclamação de 401+ ao cliente
1. O cliente capaz de CAE compreende o desafio de reivindicação de 401+ Contorna as caches e volta ao passo 1, enviando o seu token refrescante juntamente com o desafio de reivindicação de volta para AD Azure. A Azure AD irá então reavaliar todas as condições e levar o utilizador a reautenticar neste caso.
 
## <a name="faqs"></a>FAQs

### <a name="what-is-the-lifetime-of-my-access-token"></a>Qual é a vida do meu Access Token?

Se não estiver a utilizar clientes capazes de CAE, o seu tempo de vida padrão access Token ainda será de 1 hora, a menos que tenha configurado o seu tempo de vida access Token com a função de pré-visualização [Configurável Token Lifetime (CTL).](../develop/active-directory-configurable-token-lifetimes.md)

Se estiver a utilizar clientes capazes de CAE que negoceiem sessões conscientes do CAE, as suas definições CTL para o tempo de vida do Access Token serão substituídas e o tempo de vida do Access Token será de 24 horas.

### <a name="how-quick-is-enforcement"></a>Qual é a rapidez com que a aplicação da lei é?

Embora o nosso objetivo seja que a aplicação seja instantânea, em alguns casos, a latência de até 15 minutos pode ser observada devido ao tempo de propagação do evento.

### <a name="how-will-cae-work-with-sign-in-frequency"></a>Como é que o CAE vai funcionar com a Frequência de Entrada?

A Frequência de Entrada será honrada com ou sem CAE.

## <a name="next-steps"></a>Passos seguintes

[Anunciando avaliação contínua de acesso](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/moving-towards-real-time-policy-and-security-enforcement/ba-p/1276933)
