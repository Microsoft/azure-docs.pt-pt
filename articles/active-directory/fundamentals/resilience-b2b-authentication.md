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
ms.openlocfilehash: ae8e24341c321fbfffb84d9b072abc4cf925aff3
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "95920066"
---
# <a name="build-resilience-in-external-user-authentication"></a>Construir resiliência na autenticação externa do utilizador

[A colaboração Azure Ative Directory B2B](https://docs.microsoft.com/azure/active-directory/external-identities/what-is-b2b) (Azure AD B2B) é uma característica de [Identidades Externas](https://docs.microsoft.com/azure/active-directory/external-identities/delegate-invitations) que permite a colaboração com outras organizações e indivíduos. Permite o embarque seguro dos utilizadores convidados no seu inquilino AZure AD sem ter de gerir as suas credenciais. Os utilizadores externos trazem consigo a sua identidade e credenciais de um fornecedor externo de identidade (IdP), para que não tenham de se lembrar de uma nova credencial. 

## <a name="ways-to-authenticate-external-users"></a>Formas de autenticar utilizadores externos

Pode escolher os métodos de autenticação externa do utilizador para o seu diretório. Pode utilizar idPs da Microsoft ou outros IdPs.

Com cada IdP externo, você assume uma dependência da disponibilidade desse IdP. Com alguns métodos de ligação aos IDPs, há coisas que pode fazer para aumentar a sua resiliência.

> [!NOTE] 
> O Azure AD B2B tem a capacidade incorporada de autenticar qualquer utilizador de qualquer inquilino do [Azure Ative Directory,](https://docs.microsoft.com/azure/active-directory) ou com uma [Conta Microsoft](https://account.microsoft.com/account)pessoal. Não é preciso fazer nenhuma configuração com estas opções incorporadas.

### <a name="considerations-for-resilience-with-other-idps"></a>Considerações de resiliência com outros IDPs

Ao utilizar idPs externos para autenticação do utilizador convidado, existem determinadas configurações que deve garantir que mantém para evitar interrupções.

| Método de autenticação| Considerações de resiliência |
| - | - |
| Federação com IDPs sociais como [Facebook](https://docs.microsoft.com/azure/active-directory/external-identities/facebook-federation) ou [Google.](https://docs.microsoft.com/azure/active-directory/external-identities/google-federation)| Deve manter a sua conta com o IdP e configurar o seu ID de Cliente e o Segredo do Cliente. |
| [Federação Direta com SAML e Fornecedores de Identidade WS-Federation](https://docs.microsoft.com/azure/active-directory/external-identities/direct-federation)| Deve colaborar com o proprietário do IdP para ter acesso aos seus pontos finais, dos quais está dependente. <br>Deve manter os metadados que contêm os certificados e pontos finais. |
| [E-mail código de acesso único](https://docs.microsoft.com/azure/active-directory/external-identities/one-time-passcode)| Com este método está dependente do sistema de e-mail da Microsoft, do sistema de e-mail do utilizador e do cliente de e-mail do utilizador. |


 

## <a name="self-service-sign-up-preview"></a>Inscrição de self-service (pré-visualização)

Como alternativa ao envio de convites ou links, pode ativar [a inscrição de self-service](https://docs.microsoft.com/azure/active-directory/external-identities/self-service-sign-up-overview).  Isto permite que utilizadores externos solicitem acesso a uma aplicação. Deve criar um [conector API](https://docs.microsoft.com/azure/active-directory/external-identities/self-service-sign-up-add-api-connector) e associá-lo a um fluxo de utilizador. Associa fluxos de utilizador que definem a experiência do utilizador com uma ou mais aplicações. 

É possível utilizar [conectores API](https://docs.microsoft.com/azure/active-directory/external-identities/api-connectors-overview) para integrar o fluxo de utilizador de inscrição de autosserviço com APIs de sistemas externos. Esta integração API pode ser usada para [fluxos de trabalho de aprovação personalizadas,](https://docs.microsoft.com/azure/active-directory/external-identities/self-service-sign-up-add-approvals) [realizando verificação de identidade,](https://docs.microsoft.com/azure/active-directory/external-identities/code-samples-self-service-sign-up)e outras tarefas, tais como sobre-escrever atributos do utilizador. A utilização de APIs requer que gere as seguintes dependências.

* **Autenticação do conector API**: A configuração de um conector requer um URL de ponto final, um nome de utilizador e uma palavra-passe. Crie um processo pelo qual estas credenciais são mantidas, e trabalhe com o proprietário da API para garantir que conhece qualquer prazo de validade.

* **Resposta do Conector API**: Desenhe conectores API no fluxo de inscrição para falhar graciosamente se a API não estiver disponível. Examine e forneça aos seus desenvolvedores de API estas [respostas de exemplo da API](https://docs.microsoft.com/azure/active-directory/external-identities/self-service-sign-up-add-api-connector) e as [melhores práticas para a resolução de problemas.](https://docs.microsoft.com/azure/active-directory/external-identities/self-service-sign-up-add-api-connector) Trabalhe com a equipa de desenvolvimento da API para testar todos os cenários de resposta possíveis, incluindo continuação, erro de validação e respostas de bloqueio. 

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
 
