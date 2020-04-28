---
title: Ativar o SSO para a sua aplicação multi-inquilino
description: Orientação para fornecedores independentes de software sobre integração com Diretório Ativo Azure
services: active-directory
author: barbaraselden
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 05/22/2019
ms.author: baselden
ms.reviewer: jeeds
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4c89a83ade6305579e700afb86f0b9e3aca2695e
ms.sourcegitcommit: b1e25a8a442656e98343463aca706f4fde629867
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "67795187"
---
# <a name="enable-single-sign-on-for-your-multi-tenant-application"></a>Ativar o Single Sign-on para a sua aplicação multi-inquilino  

Ao oferecer o seu pedido de utilização por outras empresas através de uma compra ou subscrição, disponibiliza a sua aplicação aos clientes dentro dos seus próprios inquilinos Azure. Isto é conhecido como criar uma aplicação multi-inquilino. Para uma visão geral deste conceito, consulte [Aplicações Multitenant em Azure](https://docs.microsoft.com/azure/dotnet-develop-multitenant-applications) e [Arrendamento em Diretório Ativo Azure.](../develop/single-and-multi-tenant-apps.md)

## <a name="what-is-single-sign-on"></a>O que é um único sign-on

O único sinal de inscrição (SSO) adiciona segurança e comodidade quando os utilizadores assinam aplicações utilizando o Diretório Ativo Azure e outras identidades. Quando uma aplicação está ativada por SSO, os utilizadores não têm de introduzir credenciais separadas para aceder a essa aplicação. Para uma explicação completa do single sign-on. [Consulte o único sinal de inscrição para aplicações no Diretório Ativo Azure](what-is-single-sign-on.md).

## <a name="why-enable-single-sign-on-in-your-application"></a>Por que ativar o sign-on único na sua aplicação?

Existem muitas vantagens em permitir o SSO na sua aplicação multi-arrendatária. Quando ativa o SSO para a sua aplicação:

* A sua aplicação pode ser listada no Azure Marketplace, onde a sua aplicação é detetável por milhões de organizações que utilizam o Azure Ative Directory.
  * Permite que os clientes configurem rapidamente a aplicação com a Azure AD.

* A sua aplicação pode ser detetável na Galeria de Aplicações do Office 365, no Office 365 App Launcher e no Microsoft Search em Office.com

* A sua aplicação pode utilizar a API do Microsoft Graph REST para aceder aos dados que impulsionam a produtividade do utilizador que está disponível a partir do Microsoft Graph.

* Reduz os custos de suporte facilitando os custos de apoio, facilitando os seus clientes.
  * Documentação específica para aplicações coproduzida com a equipa da Azure AD para os nossos clientes mútuos facilita a adoção.
  * Se o SSO de um clique estiver ativado, os Administradores de TI dos seus clientes não têm de aprender a configurar a sua aplicação para utilização na sua organização.

* Fornece aos seus clientes a capacidade de gerir completamente a autenticação e autorização das identidades dos seus colaboradores e convidados.

  * Colocando toda a responsabilidade de gestão de conta e conformidade com o cliente proprietário dessas identidades.

  * Fornecer capacidade de ativar ou desativar o SSO para fornecedores de identidade, grupos ou utilizadores específicos para satisfazer as suas necessidades de negócio.

* Aumenta a sua capacidade de comercialização e asuaado. Muitas grandes organizações exigem que (ou aspiram) os seus colaboradores tenham experiências SSO perfeitas em todas as aplicações. Tornar o SSO fácil é importante.

* Reduz a fricção do utilizador final, o que pode aumentar o uso do utilizador final e aumentar as suas receitas.

## <a name="how-to-enable-single-sign-on-in-your-published-application"></a>Como ativar o Single Sign-on na sua aplicação publicada

1. [Escolha o protocolo da federação certa para a sua aplicação multi-inquilino.](isv-choose-multi-tenant-federation.md)
1. Implementar SSO na sua aplicação
   - Consulte [orientação sobre padrões de autenticação](../develop/v2-app-types.md)
   - Consulte [as amostras de código de diretório ativo azure](../develop/sample-v2-code.md) para protocolos OIDC e OAuth
1. [Crie o seu Inquilino Azure](isv-tenant-multi-tenant-app.md) e teste a sua candidatura
1. [Crie e publique documentação SSO no seu site.](isv-create-sso-documentation.md)
1. [Envie a sua listagem](https://microsoft.sharepoint.com/teams/apponboarding/Apps/SitePages/Default.aspx) de aplicações e parceiro com a Microsoft para criar documentação no site da Microsoft.
1. [Junte-se à Microsoft Partner Network (grátis) e crie o seu plano de mercado.](https://partner.microsoft.com/en-us/explore/commercial#gtm)
