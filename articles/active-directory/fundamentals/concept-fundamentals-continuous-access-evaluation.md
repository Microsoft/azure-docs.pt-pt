---
title: Avaliação contínua de acesso em Azure AD
description: Responder às alterações no estado do utilizador mais rapidamente com avaliação contínua de acesso em Azure AD
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
ms.openlocfilehash: bdf904bb2c0d133ea07cd32274fad5b6601da5d9
ms.sourcegitcommit: 2721b8d1ffe203226829958bee5c52699e1d2116
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/28/2020
ms.locfileid: "84148128"
---
# <a name="continuous-access-evaluation"></a>Avaliação contínua de acesso

Os serviços da Microsoft, como o Azure Ative Directory (Azure AD) e o Office 365, utilizam padrões e protocolos abertos para maximizar a interoperabilidade. Um dos mais críticos é o Open ID Connect (OIDC). Quando uma aplicação de clientes como o Outlook se conecta a um serviço como o Exchange Online, os pedidos da API são autorizados a usar fichas de acesso OAuth 2.0. Por padrão, esses tokens de acesso são válidos por uma hora. Quando expiram, o cliente é redirecionado de volta para a Azure AD para refrescê-los. Isto também oferece uma oportunidade para reavaliar políticas de acesso ao utilizador – podemos optar por não atualizar o token por causa de uma política de Acesso Condicional, ou porque o utilizador foi desativado no diretório. 

A expiração e a atualização de token são um mecanismo padrão na indústria. Dito isto, os clientes têm manifestado preocupação com o desfasamento entre quando as condições de risco mudam para o utilizador (por exemplo: a mudança do escritório da empresa para o café local, ou as credenciais de utilizador descobertas no mercado negro) e quando as políticas podem ser aplicadas relacionadas com essa mudança. Experimentámos a abordagem "objeto contundente" de vidas reduzidas, mas descobrimos que podem degradar as experiências e a fiabilidade dos utilizadores sem eliminar os riscos.

Uma resposta oportuna a violações de políticas ou questões de segurança requer realmente uma "conversa" entre o emitente simbólico, como a Azure AD, e a parte que conta, como o Exchange Online. Esta conversa bidirecionais dá-nos duas capacidades importantes. A parte que conta pode notar quando as coisas mudaram, como um cliente vindo de um novo local, e dizer ao emitente simbólico. Também dá ao emitente token uma forma de dizer à parte que conta para parar de respeitar as fichas para um determinado utilizador devido a compromisso de conta, desativação ou outras preocupações. O mecanismo para esta conversação é a Avaliação contínua de Acesso (CAE).

A Microsoft tem participado precocemente na iniciativa Do Protocolo de Avaliação de Acesso Contínuo (CAEP) como parte do grupo de trabalho [de Sinais e Eventos Partilhados](https://openid.net/wg/sse/) da Fundação OpenID. Os fornecedores de identidade e as partes dependentes poderão aproveitar os eventos e sinais de segurança definidos pelo grupo de trabalho para reautorizar ou terminar o acesso. É um trabalho emocionante e irá melhorar a segurança em muitas plataformas e aplicações.

Como os benefícios de segurança são tão grandes, estamos a lançar uma implementação inicial específica da Microsoft em paralelo com o nosso trabalho continuado dentro dos órgãos de padrões. À medida que trabalhamos para implementar estas capacidades de avaliação contínua de acesso (CAE) em todos os serviços da Microsoft, aprendemos muito e estamos a partilhar esta informação com a comunidade de padrões. Esperamos que a nossa experiência de implantação possa ajudar a informar um padrão ainda melhor da indústria e estamos empenhados em implementar essa norma uma vez ratificada, permitindo que todos os serviços participantes beneficiem.

## <a name="how-does-cae-work-in-microsoft-services"></a>Como funciona o CAE nos serviços da Microsoft?

Estamos a centrar a nossa implementação inicial de avaliação contínua de acesso a Intercâmbio e Equipas. Esperamos expandir o suporte a outros serviços da Microsoft no futuro. Começaremos a permitir uma avaliação contínua de acesso apenas para inquilinos sem políticas de Acesso Condicional. Usaremos as nossas aprendizagens a partir desta fase do CAE para informar o nosso lançamento contínuo do CAE.

## <a name="service-side-requirements"></a>Requisitos do lado do serviço

A avaliação contínua do acesso é implementada através da capacitação dos serviços (fornecedores de recursos) para subscrever eventos críticos em AZure AD para que esses eventos possam ser avaliados e aplicados perto de tempo real. Os seguintes eventos serão aplicados neste lançamento inicial do CAE:

- A Conta de Utilizador é eliminada ou desativada
- A palavra-passe para um utilizador é alterada ou reiniciada
- Administração revoga explicitamente todos os tokens de atualização para um utilizador
- Risco elevado de utilizador detetado pela Azure AD Identity Protection

No futuro, esperamos adicionar mais eventos, incluindo eventos como localização e alterações do estado do dispositivo. **Embora o nosso objetivo seja que a aplicação da lei seja instantânea, em alguns casos a latência de até 15 minutos pode ser observada devido ao tempo de propagação do evento**. 

## <a name="client-side-claim-challenge"></a>Desafio de reclamação do lado do cliente

Antes da avaliação contínua do acesso, os clientes tentavam sempre reproduzir o token de acesso a partir da sua cache, desde que não expirasse. Com a CAE, estamos a introduzir um novo caso que um fornecedor de recursos pode rejeitar um símbolo mesmo quando não está expirado. A fim de informar os clientes para contornarem a sua cache, mesmo que os tokens em cache não tenham expirado, introduzimos um mecanismo chamado **desafio de reclamação**. O CAE requer uma atualização do cliente para compreender o desafio da reclamação. A versão mais recente das seguintes aplicações abaixo do desafio de reivindicação de suporte:

- Perspetivas para Windows 
- Perspetivas para iOS 
- Perspetivas para Android 
- Outlook para Mac 
- Equipas para Windows
- Equipas para iOS 
- Equipas para Android 
- Equipas para Mac 

## <a name="token-lifetime"></a>Duração do Token

Como o risco e a política são avaliados em tempo real, os clientes que negoceiam sessões de avaliação contínua de acessos conscientes dependerão da CAE em vez das políticas de vida de acesso estático existentes, o que significa que a política de vida de token configurável não será mais honrada para clientes capazes de CAE que negoceiam sessões conscientes do CAE.

Aumentaremos o acesso ao token life para 24 horas nas sessões cae. A revogação é impulsionada por acontecimentos críticos e avaliação de políticas, e não por um período de tempo arbitrário. Esta alteração aumenta a estabilidade das suas aplicações sem afetar a sua postura de segurança. 

## <a name="example-flows"></a>Fluxos de exemplo

### <a name="user-revocation-event-flow"></a>Fluxo de evento de revogação do utilizador:

![Fluxo de evento de revogação do utilizador](./media/concept-fundamentals-continuous-access-evaluation/user-revocation-event-flow.png)

1. Um cliente com capacidade para cae apresenta credenciais ou um token de atualização para a AAD pedindo um sinal de acesso para algum recurso.
1. Um sinal de acesso é devolvido juntamente com outros artefactos ao cliente.
1. Um administrador revoga explicitamente [todos os tokens de atualização para o utilizador](https://docs.microsoft.com/powershell/module/azuread/revoke-azureaduserallrefreshtoken?view=azureadps-2.0). Será enviado um evento de revogação ao fornecedor de recursos da Azure AD.
1. Um sinal de acesso é apresentado ao fornecedor de recursos. O fornecedor de recursos avalia a validade do token e verifica se existe algum evento de revogação para o utilizador. O fornecedor de recursos utiliza esta informação para decidir conceder ou não acesso ao recurso.
1. Neste caso, o fornecedor de recursos nega o acesso, e envia um desafio de reclamação de 401+ de volta ao cliente
1. O cliente capaz de CAE compreende o desafio de reclamação de 401+. Contorna os caches e volta ao passo 1, enviando o seu token de atualização juntamente com o desafio de reivindicação de volta para Azure AD. O Azure AD reavaliará todas as condições e solicitará ao utilizador que reautore neste caso.
 
## <a name="faqs"></a>FAQs

### <a name="what-is-the-lifetime-of-my-access-token"></a>Qual é a vida do meu Token de Acesso?

Se não estiver a utilizar clientes capazes de CAE, a sua vida útil de Access Token por defeito ainda será de 1 hora, a menos que tenha configurado a sua vida útil do Access Token com a funcionalidade de pré-visualização [de Vida Útil (CTL) configurada.](../develop/active-directory-configurable-token-lifetimes.md)

Se estiver a utilizar clientes capazes de CAE que negoceiem sessões conscientes do CAE, as suas definições CTL para a vida útil do Access Token serão substituídas e a vida útil do Access Token será de 24 horas.

### <a name="how-quick-is-enforcement"></a>Quão rápido é a aplicação?

Embora o nosso objetivo seja que a aplicação seja instantânea, em alguns casos a latência de até 15 minutos pode ser observada devido ao tempo de propagação do evento.

### <a name="how-will-cae-work-with-sign-in-frequency"></a>Como é que o CAE vai funcionar com a frequência de inscrição?

A frequência de inscrição será honrada com ou sem CAE.

## <a name="next-steps"></a>Próximos passos

[Anunciando a avaliação contínua do acesso](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/moving-towards-real-time-policy-and-security-enforcement/ba-p/1276933)
