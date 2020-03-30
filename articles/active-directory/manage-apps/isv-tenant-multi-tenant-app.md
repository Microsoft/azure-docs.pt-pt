---
title: Criar um inquilino Azure para uma aplicação multi-inquilino
description: Orientação para fornecedores independentes de software sobre integração com o Diretório Ativo Azure
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
ms.openlocfilehash: 637adba89445e6974e83486f0641576225ccd268
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "70812607"
---
# <a name="create-an-azure-tenant-for-a-multi-tenant-application"></a>Criar um inquilino Azure para uma aplicação multi-inquilino  

Para fornecer acesso à sua aplicação multi-arrendatária, deve criar um inquilino do Azure Ative Directory para registar a aplicação e ativar a federação das identidades do seu cliente. Consulte [a Escolha do protocolo da federação certa para a sua aplicação multi-inquilino.](isv-choose-multi-tenant-federation.md) Este inquilino permitir-lhe-á testar a sua aplicação e a federação num ambiente semelhante aos dos seus clientes, ambientes AD Azure.

## <a name="costs-of-hosting-a-multi-tenant-application"></a>Custos de hospedagem de uma aplicação multi-inquilino

O Azure Ative Directory está disponível em várias edições. [Consulte a comparação de características detalhadas.](https://azure.microsoft.com/pricing/details/active-directory/)

Pode criar gratuitamente a sua subscrição Azure e o diretório ativo Azure e utilizar funcionalidades básicas.

## <a name="create-your-tenant"></a>Crie o seu inquilino

1. Crie o seu Inquilino. Consulte [A configuração de um ambiente de dev](../develop/quickstart-create-new-tenant.md).

2. Ativar e testar o acesso único à sua aplicação,

   a. **Para pedidos oIDC ou Juramento,** [registe a sua candidatura](../develop/quickstart-register-app.md) como pedido de multi-inquilinos. Selecione as Contas em qualquer diretório organizacional e opção pessoal de contas microsoft em tipos de Conta Suportada

   b. **Para aplicações baseadas em SAML e WS-Fed,** configurar aplicações [de assinatura única baseadas em SAML](configure-single-sign-on-non-gallery-applications.md) utilizando um modelo saml genérico em Azure AD.

Você também pode [converter um pedido de inquilino único para multi-inquilino,](../develop/howto-convert-app-to-be-multi-tenant.md) se necessário.

## <a name="next-steps"></a>Passos Seguintes

[Integrar o SSO na sua aplicação](isv-sso-content.md)
