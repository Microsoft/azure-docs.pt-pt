---
title: Criar um inquilino do Azure para uma aplicação multi-inquilino
description: Documentação de orientação para fornecedores independentes de software sobre como integrar com o Azure Active Directory
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
ms.openlocfilehash: 69cc625500af60a753ad8e7db0363954088f3307
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/08/2019
ms.locfileid: "67659583"
---
# <a name="create-an-azure-tenant-for-a-multi-tenant-application"></a>Criar um inquilino do Azure para uma aplicação multi-inquilino  

Para fornecer acesso à sua aplicação multi-inquilino tem de criar um inquilino do Azure Active Directory para registar a aplicação e ativar a Federação de identidades do seu cliente. Ver [escolher o protocolo de Federação certo para a sua aplicação multi-inquilino](isv-choose-multi-tenant-federation.md). Este inquilino permitirá que teste seu aplicativo e a Federação num ambiente que é semelhante aos seus ambientes do Azure AD de clientes.

## <a name="costs-of-hosting-a-multi-tenant-application"></a>Custos de hospedagem de uma aplicação multi-inquilino

O Azure Active Directory está disponível em três SKUs, gratuito, Basic e Premium. [Veja a comparação detalhada das funcionalidades](https://azure.microsoft.com/pricing/details/active-directory/).

Pode criar a sua subscrição do Azure e o Azure active directory gratuito e utilizar as funcionalidades básicas.

## <a name="create-your-tenant"></a>Criar o inquilino

1. Crie o inquilino. Ver [configurar um ambiente de desenvolvimento](../develop/quickstart-create-new-tenant.md).

2. Ativar e testar o acesso de início de sessão único para seu aplicativo,

   a. **Para aplicativos OIDC ou Oath**, [registar a sua aplicação](../develop/quickstart-register-app.md) como uma aplicação multi-inquilino. Selecione as contas em qualquer diretório organizacional e a opção de contas Microsoft pessoa em tipos de conta suportada

   b. **Para aplicações SAML e WS-Fed-com base na**, [baseado em SAML configurar início de sessão único](configure-single-sign-on-non-gallery-applications.md) aplicativos usando um modelo SAML genérico no Azure AD.

Também pode [converta um aplicativo de inquilino único em multi-inquilino](../develop/howto-convert-app-to-be-multi-tenant.md) se necessário.

## <a name="next-steps"></a>Próximos Passos

[Integrar o SSO em seu aplicativo](isv-sso-content.md)
