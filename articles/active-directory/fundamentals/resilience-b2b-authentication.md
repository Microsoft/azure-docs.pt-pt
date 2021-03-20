---
title: Construir resiliência na autenticação externa do utilizador com o Azure Ative Directory
description: Um guia para administradores e arquitetos de TI para a construção de autenticação resiliente para utilizadores externos
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
ms.openlocfilehash: 487efce1fe57413dda740c42a7fd3d5ea91cfa49
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98724964"
---
# <a name="build-resilience-in-external-user-authentication"></a>Construir resiliência na autenticação externa do utilizador

[A colaboração Azure Ative Directory B2B](../external-identities/what-is-b2b.md) (Azure AD B2B) é uma característica de [Identidades Externas](../external-identities/delegate-invitations.md) que permite a colaboração com outras organizações e indivíduos. Permite o embarque seguro dos utilizadores convidados no seu inquilino AZure AD sem ter de gerir as suas credenciais. Os utilizadores externos trazem consigo a sua identidade e credenciais de um fornecedor externo de identidade (IdP), para que não tenham de se lembrar de uma nova credencial. 

## <a name="ways-to-authenticate-external-users"></a>Formas de autenticar utilizadores externos

Pode escolher os métodos de autenticação externa do utilizador para o seu diretório. Pode utilizar idPs da Microsoft ou outros IdPs.

Com cada IdP externo, você assume uma dependência da disponibilidade desse IdP. Com alguns métodos de ligação aos IDPs, há coisas que pode fazer para aumentar a sua resiliência.

> [!NOTE] 
> O Azure AD B2B tem a capacidade incorporada de autenticar qualquer utilizador de qualquer inquilino do [Azure Ative Directory,](../index.yml) ou com uma [Conta Microsoft](https://account.microsoft.com/account)pessoal. Não é preciso fazer nenhuma configuração com estas opções incorporadas.

### <a name="considerations-for-resilience-with-other-idps"></a>Considerações de resiliência com outros IDPs

Ao utilizar idPs externos para autenticação do utilizador convidado, existem determinadas configurações que deve garantir que mantém para evitar interrupções.

| Método de autenticação| Considerações de resiliência |
| - | - |
| Federação com IDPs sociais como [Facebook](../external-identities/facebook-federation.md) ou [Google.](../external-identities/google-federation.md)| Deve manter a sua conta com o IdP e configurar o seu ID de Cliente e o Segredo do Cliente. |
| [Federação Direta com SAML e Fornecedores de Identidade WS-Federation](../external-identities/direct-federation.md)| Deve colaborar com o proprietário do IdP para ter acesso aos seus pontos finais, dos quais está dependente. <br>Deve manter os metadados que contêm os certificados e pontos finais. |
| [E-mail código de acesso único](../external-identities/one-time-passcode.md)| Com este método está dependente do sistema de e-mail da Microsoft, do sistema de e-mail do utilizador e do cliente de e-mail do utilizador. |


 

## <a name="self-service-sign-up-preview"></a>Inscrição de self-service (pré-visualização)

Como alternativa ao envio de convites ou links, pode ativar [a inscrição de self-service](../external-identities/self-service-sign-up-overview.md).  Isto permite que utilizadores externos solicitem acesso a uma aplicação. Deve criar um [conector API](../external-identities/self-service-sign-up-add-api-connector.md) e associá-lo a um fluxo de utilizador. Associa fluxos de utilizador que definem a experiência do utilizador com uma ou mais aplicações. 

É possível utilizar [conectores API](../external-identities/api-connectors-overview.md) para integrar o fluxo de utilizador de inscrição de autosserviço com APIs de sistemas externos. Esta integração API pode ser usada para [fluxos de trabalho de aprovação personalizadas,](../external-identities/self-service-sign-up-add-approvals.md) [realizando verificação de identidade,](../external-identities/code-samples-self-service-sign-up.md)e outras tarefas, tais como sobre-escrever atributos do utilizador. A utilização de APIs requer que gere as seguintes dependências.

* **Autenticação do conector API**: A configuração de um conector requer um URL de ponto final, um nome de utilizador e uma palavra-passe. Crie um processo pelo qual estas credenciais são mantidas, e trabalhe com o proprietário da API para garantir que conhece qualquer prazo de validade.

* **Resposta do Conector API**: Desenhe conectores API no fluxo de inscrição para falhar graciosamente se a API não estiver disponível. Examine e forneça aos seus desenvolvedores de API estas [respostas de exemplo da API](../external-identities/self-service-sign-up-add-api-connector.md) e as [melhores práticas para a resolução de problemas.](../external-identities/self-service-sign-up-add-api-connector.md) Trabalhe com a equipa de desenvolvimento da API para testar todos os cenários de resposta possíveis, incluindo continuação, erro de validação e respostas de bloqueio. 

## <a name="next-steps"></a>Passos seguintes
Recursos de resiliência para administradores e arquitetos
 
* [Construir resiliência com gestão credencial](resilience-in-credentials.md)

* [Construir resiliência com estados de dispositivo](resilience-with-device-states.md)

* [Construa resiliência utilizando a Avaliação contínua de Acesso (CAE)](resilience-with-continuous-access-evaluation.md)

* [Construa resiliência na sua autenticação híbrida](resilience-in-hybrid.md)

* [Criar resiliência no acesso à aplicação com Aplicação Proxy](resilience-on-premises-access.md)

Recursos de resiliência para desenvolvedores

* [Construa resiliência do IAM nas suas aplicações](resilience-app-development-overview.md)

* [Construa resiliência nos seus sistemas CIAM](resilience-b2c.md)
