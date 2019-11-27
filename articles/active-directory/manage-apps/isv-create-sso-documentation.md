---
title: Criar & publicar a documentação de logon único para seu aplicativo
description: Diretrizes para fornecedores independentes de software na integração com o Azure Active Directory
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
ms.openlocfilehash: cb223ec8ab7b5c053136c78d3b4ca30ad4da4e18
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74232271"
---
# <a name="create-and-publish-single-sign-on-documentation-for-your-application"></a>Criar e publicar a documentação de logon único para seu aplicativo   

## <a name="documentation-on-your-site"></a>Documentação no seu site

A facilidade de adoção é um fator significativo nas decisões de software empresarial. A documentação clara e fácil de seguir dá suporte a seus clientes em sua jornada de adoção e reduz os custos de suporte. Trabalhando com milhares de fornecedores de software, a Microsoft observou o que funciona.

É recomendável que sua documentação em seu site, no mínimo, inclua os itens a seguir.

* Introdução à sua funcionalidade de SSO

  * Protocolos com suporte

  * Versão e SKU

  * Lista de provedores de identidade com suporte com links de documentação

* Informações de licenciamento para seu aplicativo

* Controle de acesso baseado em função para configurar o SSO

* Etapas de configuração de SSO

  * Elementos de configuração da interface do usuário para SAML com valores esperados do provedor

  * Informações do provedor de serviços a serem passadas para provedores de identidade

* Se OIDC/OAuth

  * Lista de permissões necessárias para o consentimento com justificativas de negócios

* Etapas de teste para usuários piloto

* Informações de solução de problemas, incluindo códigos de erro e mensagens

* Mecanismos de suporte para clientes

## <a name="documentation-on-the-microsoft-site"></a>Documentação no site da Microsoft

Quando você listar seu aplicativo com a Galeria de aplicativos Azure Active Directory, que também publica seu aplicativo no Azure Marketplace, a Microsoft gerará documentação para nossos clientes mútuos explicando o processo passo a passo. Você pode ver um exemplo [aqui](https://aka.ms/appstutorial). Esta documentação é criada com base no seu envio para a galeria e você pode atualizá-la facilmente se fizer alterações em seu aplicativo usando sua conta do GitHub.

## <a name="next-steps"></a>Próximos Passos

[Listar seu aplicativo na Galeria de aplicativos do Azure AD](https://microsoft.sharepoint.com/teams/apponboarding/Apps/SitePages/Default.aspx)