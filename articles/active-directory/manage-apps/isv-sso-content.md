---
title: Ativar o SSO para a sua aplicação multi-inquilino
description: Documentação de orientação para fornecedores independentes de software sobre como integrar com o Azure active Directory
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
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67795187"
---
# <a name="enable-single-sign-on-for-your-multi-tenant-application"></a>Ativar o início de sessão único para a sua aplicação multi-inquilino  

Ao oferecer a sua aplicação para utilização por outras empresas através de uma compra ou subscrição, é disponibilizar a aplicação para os clientes a partir de seus próprios inquilinos do Azure. Isso é conhecido como criar uma aplicação multi-inquilino. Para a descrição desse conceito, veja [aplicações de multi-inquilino no Azure](https://docs.microsoft.com/azure/dotnet-develop-multitenant-applications) e [inquilinos no Azure Active Directory](../develop/single-and-multi-tenant-apps.md).

## <a name="what-is-single-sign-on"></a>O que é o início de sessão único

Início de sessão único (SSO) adiciona segurança e conveniência, quando os utilizadores iniciam sessão aplicações com o Azure Active Directory e identidades de outras. Quando uma aplicação SSO ativado, os utilizadores não tenham de introduzir diferentes credenciais para aceder essa aplicação. Para obter uma explicação completa de início de sessão único. [Veja o início de sessão único para aplicações no Azure Active Directory](what-is-single-sign-on.md).

## <a name="why-enable-single-sign-on-in-your-application"></a>Por que motivo ativar início de sessão único na sua aplicação?

Há muitas vantagens para ativar o SSO em seu aplicativo de multi-inquilino. Quando ativar o SSO para a sua aplicação:

* Seu aplicativo pode ser apresentado no Azure Marketplace, onde a sua aplicação é detetável por milhões de organizações que utilizam o Azure Active Directory.
  * Permite aos clientes configurar rapidamente a aplicação com o Azure AD.

* O aplicativo puder ser detetável na Galeria de aplicações do Office 365, o iniciador de aplicações do Office 365 e dentro do Microsoft Search em Office.com

* A aplicação pode utilizar a API de REST do Microsoft Graph para acessar os dados que impulsionam a produtividade do utilizador que está disponível no Microsoft Graph.

* Reduzir os custos de suporte, tornando mais fácil para os seus clientes.
  * Documentação específica do aplicativo coproduced com a equipa do Azure AD para a adoção de facilita um pouco de clientes mútuos.
  * Se estiver ativada a SSO num único clique, os administradores de TI dos seus clientes não é preciso aprender a configurar a sua aplicação para utilização na sua organização.

* Ofereça aos clientes a capacidade de gerenciar totalmente a dos seus funcionários e convidados as identidades autenticação e autorização.

  * Colocar todos os conta gestão e conformidade responsabilidade com o proprietário do cliente dessas identidades.

  * Fornecendo capacidade de ativar ou desativar o SSO para fornecedores de identidade específicas, grupos ou utilizadores às suas necessidades de negócio.

* Aumente sua presença e adoptability. Muitas organizações grandes exigem que (ou aspirar) aos seus funcionários tem experiências SSO totalmente integradas em todas as aplicações. Facilitando o SSO é importante.

* Reduza o atrito de utilizador final, o que pode aumentar a utilização do utilizador final e aumentar a sua receita.

## <a name="how-to-enable-single-sign-on-in-your-published-application"></a>Como ativar o início de sessão único na sua aplicação publicada

1. [Selecione o protocolo de Federação certo para a sua aplicação multi-inquilino](isv-choose-multi-tenant-federation.md).
1. Implementar SSO em seu aplicativo
   - Consulte [documentação de orientação sobre padrões de autenticação](../develop/v2-app-types.md)
   - Ver [do Azure Active Directory exemplos de código de diretório](../develop/sample-v2-code.md) para protocolos OIDC e OAuth
1. [Criar o inquilino do Azure](isv-tenant-multi-tenant-app.md) e testar seu aplicativo
1. [Criar e publicar a documentação do SSO no seu site](isv-create-sso-documentation.md).
1. [Submeter a sua listagem de aplicativo](https://microsoft.sharepoint.com/teams/apponboarding/Apps/SitePages/Default.aspx) e de parceiros com a Microsoft para criar documentação no site da Microsoft.
1. [Junte-se a rede de parceiros da Microsoft (gratuito) e criar o go no mercado plano](https://partner.microsoft.com/en-us/explore/commercial#gtm).
