---
title: Ativar o SSO para a sua aplicação multi-inquilino
description: Orientação para fornecedores de software independentes na integração com o Azure ative Directory
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 05/22/2019
ms.author: kenwith
ms.reviewer: jeeds
ms.collection: M365-identity-device-management
ms.openlocfilehash: cabcaacbdc7e3e94e62c8fb45274c4d4f9390ddd
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87273410"
---
# <a name="enable-single-sign-on-for-your-multi-tenant-application"></a>Ativar o Sign-on único para a sua aplicação multi-inquilino  

Quando oferece o seu pedido de utilização por outras empresas através de uma compra ou subscrição, disponibiliza a sua aplicação aos clientes dentro dos seus próprios inquilinos Azure. Isto é conhecido como criar uma aplicação multi-inquilino. Para uma visão geral deste conceito, consulte [Aplicações Multitenant em Azure](https://docs.microsoft.com/azure/dotnet-develop-multitenant-applications) e [Tenancy no Azure Ative Directory](../develop/single-and-multi-tenant-apps.md).

## <a name="what-is-single-sign-on"></a>O que é single sign-on

O sign-on único (SSO) adiciona segurança e comodidade quando os utilizadores se inscrevem nas aplicações utilizando o Azure Ative Directory e outras identidades. Quando uma aplicação está ativada por SSO, os utilizadores não têm de introduzir credenciais separadas para aceder a essa aplicação. Para uma explicação completa do single sign-on. [Consulte o único sinal de inscrição nas aplicações no Diretório Ativo Azure](what-is-single-sign-on.md).

## <a name="why-enable-single-sign-on-in-your-application"></a>Porquê Ativar o Único S-on na sua aplicação?

Existem muitas vantagens em permitir sSO na sua aplicação multi-inquilino. Quando ativa sSO para a sua aplicação:

* A sua aplicação pode ser listada no Azure Marketplace, onde a sua aplicação é detetável por milhões de organizações que utilizam o Azure Ative Directory.
  * Permite que os clientes configuram rapidamente a aplicação com a Azure AD.

* A sua aplicação pode ser detetada na Galeria de Aplicações do Office 365, no Launcher de Aplicações office 365 e no Microsoft Search on Office.com

* A sua aplicação pode utilizar a API do Microsoft Graph REST para aceder aos dados que impulsionam a produtividade do utilizador que está disponível a partir do Gráfico do Microsoft.

* Reduz os custos de suporte facilitando os custos para os seus clientes.
  * Documentação específica da aplicação coproduzida com a equipa Azure AD para os nossos clientes mútuos facilita a adoção.
  * Se o SSO de um clique estiver ativado, os Administradores de TI dos seus clientes não têm de aprender a configurar a sua aplicação para utilização na sua organização.

* Fornece aos seus clientes a capacidade de gerir completamente a autenticação e autorização das identidades dos seus colaboradores e convidados.

  * Colocar toda a gestão de conta e conformidade com o proprietário dessas identidades.

  * Fornecendo capacidade para permitir ou desativar SSO para fornecedores de identidade específicos, grupos ou utilizadores para atender às suas necessidades de negócio.

* Aumenta a sua capacidade de comercialização e adobilidade. Muitas grandes organizações exigem que (ou aspiram) os seus colaboradores têm experiências SSO perfeitas em todas as aplicações. Tornar o SSO fácil é importante.

* Reduz o atrito do utilizador final, o que pode aumentar o uso do utilizador final e aumentar as suas receitas.

## <a name="how-to-enable-single-sign-on-in-your-published-application"></a>Como ativar o Único S-on na sua aplicação publicada

1. [Escolha o protocolo da federação certa para a sua aplicação multi-inquilino.](isv-choose-multi-tenant-federation.md)
1. Implementar SSO na sua aplicação
   - Consulte [orientação sobre padrões de autenticação](../develop/v2-app-types.md)
   - Consulte [as amostras de código de diretório ativo do Azure](../develop/sample-v2-code.md) para os protocolos OIDC e OAuth
1. [Crie o seu Inquilino Azure](isv-tenant-multi-tenant-app.md) e teste a sua aplicação
1. [Crie e publique documentação SSO no seu site.](isv-create-sso-documentation.md)
1. [Submeta a sua lista de aplicações](https://microsoft.sharepoint.com/teams/apponboarding/Apps/SitePages/Default.aspx) e parceiro com a Microsoft para criar documentação no site da Microsoft.
1. [Junte-se à Microsoft Partner Network (gratuita) e crie o seu plano de mercado.](https://partner.microsoft.com/explore/commercial#gtm)
