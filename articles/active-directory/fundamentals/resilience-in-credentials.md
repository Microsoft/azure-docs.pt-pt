---
title: Construir resiliência com gestão credencial no Azure Ative Directory
description: Um guia para arquitetos e administradores de TI na construção de uma estratégia de credencial resiliente.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 11/30/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 399d2f71fa20d63dce89cf3be5c12ffd63264895
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98724715"
---
# <a name="build-resilience-with-credential-management"></a>Construir resiliência com gestão credencial

Quando uma credencial é apresentada à Azure AD num pedido simbólico, existem múltiplas dependências que devem estar disponíveis para validação. O primeiro fator de autenticação baseia-se na autenticação AZure AD e, em alguns casos, na infraestrutura no local. Para obter mais informações sobre arquiteturas de autenticação híbrida, consulte [Construir resiliência na sua infraestrutura híbrida.](resilience-in-hybrid.md) 

Se implementar um segundo fator, as dependências do segundo fator são adicionadas às dependências para o primeiro. Por exemplo, se o seu primeiro fator for através de PTA, e o seu segundo fator for SMS, as suas dependências são:

* Serviços de autenticação AZURE AD

* Serviço Azure MFA

* Infraestrutura no local

* Operador telefónico

* O dispositivo do utilizador (não na imagem)

 
![Imagem de métodos de autenticação e dependências](./media/resilience-in-credentials/admin-resilience-credentials.png)

A sua estratégia de credencial deve considerar as dependências de cada tipo de autenticação, e métodos de provisão que evitem um único ponto de falha. 

Como os métodos de autenticação têm dependências diferentes, é uma boa ideia permitir que os utilizadores se registem para o maior número possível de opções de segundo fator. Certifique-se de incluir segundos fatores com diferentes dependências, se possível. Por exemplo, a chamada de voz e o SMS como segundo fatores partilham as mesmas dependências, pelo que tê-los como as únicas opções não atenua o risco.

A estratégia de credencial mais resiliente é usar a autenticação sem palavras-passe. As chaves de segurança Windows Hello for Business e FIDO 2.0 têm menos dependências do que a autenticação forte com dois fatores distintos. A aplicação Microsoft Authenticator, o Windows Hello for Business e o Fido 2.0 são as teclas de segurança mais seguras. 

Para segundos fatores, a aplicação Microsoft Authenticator ou outras aplicações autenticadoras que usam uma senha de tempo (TOTP) ou tokens de hardware OATH têm menos dependências e, portanto, são mais resistentes.

## <a name="how-do-multiple-credentials-help-resilience"></a>Como é que várias credenciais ajudam a resiliência?

O fornecimento de múltiplos tipos de credenciais dá aos utilizadores opções que acomodam as suas preferências e constrangimentos ambientais. Como resultado, a autenticação interativa onde os utilizadores são solicitados para a autenticação multi-factor será mais resistente às dependências específicas não estando disponível no momento do pedido. Pode [otimizar pedidos de reautorização para a autenticação de vários fatores.](../authentication/concepts-azure-multi-factor-authentication-prompts-session-lifetime.md)

Para além da resiliência individual dos utilizadores acima descrita, as empresas devem planear contingências para perturbações em larga escala, tais como erros operacionais que introduzam uma configuração errada, uma catástrofe natural ou uma interrupção de recursos em toda a empresa para um serviço de federação no local (especialmente quando utilizado para a autenticação multi-factor). 

## <a name="how-do-i-implement-resilient-credentials"></a>Como implemento credenciais resilientes?

* Implemente [credenciais sem palavras-passe](../authentication/howto-authentication-passwordless-deployment.md) como o Windows Hello for Business, Phone Authentication e FIDO2 para reduzir as dependências.

* Implemente a [App autenticador da Microsoft](../user-help/user-help-auth-app-overview.md) como um segundo fator.

* Ligue a [sincronização de hash de palavra-passe](../hybrid/whatis-phs.md) para contas híbridas sincronizadas a partir do Windows Server Ative Directory. Esta opção pode ser ativada juntamente com serviços da federação, como a AD FS, e proporciona uma redução no caso de o serviço da federação falhar.

* [Analise o uso de métodos de autenticação de vários fatores](/samples/azure-samples/azure-mfa-authentication-method-analysis/azure-mfa-authentication-method-analysis/) para melhorar a experiência dos utilizadores.

* [Implementar uma estratégia resiliente de controlo de acessos](../authentication/concept-resilient-controls.md)

## <a name="next-steps"></a>Passos seguintes
Recursos de resiliência para administradores e arquitetos
 
* [Construir resiliência com estados de dispositivo](resilience-with-device-states.md)

* [Construa resiliência utilizando a Avaliação contínua de Acesso (CAE)](resilience-with-continuous-access-evaluation.md)

* [Construir resiliência na autenticação externa do utilizador](resilience-b2b-authentication.md)

* [Construa resiliência na sua autenticação híbrida](resilience-in-hybrid.md)

* [Criar resiliência no acesso à aplicação com Aplicação Proxy](resilience-on-premises-access.md)

Recursos de resiliência para desenvolvedores

* [Construa resiliência do IAM nas suas aplicações](resilience-app-development-overview.md)

* [Construa resiliência nos seus sistemas CIAM](resilience-b2c.md)