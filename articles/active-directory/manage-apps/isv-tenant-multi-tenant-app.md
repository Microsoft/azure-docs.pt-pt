---
title: Criar um inquilino Azure para uma aplicação multi-inquilino
description: Orientação para fornecedores independentes de software na integração com o Azure Ative Directory
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
ms.openlocfilehash: 1a0b63c130d7d1e72bd3320e40213ae3cb1069a6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84763249"
---
# <a name="create-an-azure-tenant-for-a-multi-tenant-application"></a>Criar um inquilino Azure para uma aplicação multi-inquilino  

Para fornecer acesso à sua aplicação multi-arrendatário, deve criar um inquilino do Azure Ative Directory para registar o pedido e ativar a federação das identidades do seu cliente. Consulte [a escolha do protocolo da federação certa para a sua aplicação multi-inquilino.](isv-choose-multi-tenant-federation.md) Este inquilino permitir-lhe-á testar a sua aplicação e a federação num ambiente semelhante aos ambientes AZure AD dos seus clientes.

## <a name="costs-of-hosting-a-multi-tenant-application"></a>Custos de hospedagem de uma aplicação multi-arrendatário

O Azure Ative Directory está disponível em várias edições. [Consulte a comparação detalhada de recursos.](https://azure.microsoft.com/pricing/details/active-directory/)

Pode criar gratuitamente a sua subscrição Azure e o diretório ativo Azure e utilizar funcionalidades básicas.

## <a name="create-your-tenant"></a>Crie o seu inquilino

1. Crie o seu Inquilino. Ver [Configurar um ambiente dev](../develop/quickstart-create-new-tenant.md).

2. Ative e teste o acesso único à sua aplicação,

   a. **Para aplicações OIDC ou Juramento,** [Registe a sua candidatura](../develop/quickstart-register-app.md) como uma aplicação multi-arrendatário. Selecione as Contas em qualquer diretório organizacional e opção de contas pessoais da Microsoft em tipos de Conta Suportada

   b. **Para aplicações baseadas em SAML e WS-Fed,** você configura aplicações [únicas baseadas em SAML](configure-single-sign-on-non-gallery-applications.md) usando um modelo de SAML genérico em Azure AD.

Você também pode [converter um pedido de inquilino único para multi-inquilino,](../develop/howto-convert-app-to-be-multi-tenant.md) se necessário.

## <a name="next-steps"></a>Passos Seguintes

[Integre sSO na sua aplicação](isv-sso-content.md)
