---
title: Termos de serviço e declaração de privacidade para aplicações | Azure
description: Saiba como pode configurar os termos de serviço e declaração de privacidade de aplicações registadas para utilizar o Azure AD.
services: active-directory
documentationcenter: dev-center-name
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/24/2018
ms.author: celested
ms.reviwer: lenalepa, sureshja
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 97728fa70f0c5f58510e0e68d27a379b20887703
ms.sourcegitcommit: 41015688dc94593fd9662a7f0ba0e72f044915d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/11/2019
ms.locfileid: "59500308"
---
# <a name="how-to-configure-terms-of-service-and-privacy-statement-for-an-app"></a>Como: Configurar os termos de serviço e declaração de privacidade de uma aplicação

Os desenvolvedores que criam e gerir as aplicações que se integram com o Azure Active Directory (Azure AD) e contas da Microsoft deverá incluir ligações aos termos da aplicação de serviço e declaração de privacidade. Os termos de serviço e declaração de privacidade são apresentados aos utilizadores através da experiência de consentimento do utilizador. Ajudam os utilizadores que podem dar seu aplicativo. Os termos de serviço e declaração de privacidade são essenciais sobretudo para destinada ao utilizador multi-inquilinos aplicações – aplicações que são utilizadas por vários diretórios ou que estão disponíveis para qualquer conta Microsoft.

É responsável por criar os termos de serviço e a privacidade documentos de instrução para a sua aplicação e para fornecer os URLs a estes documentos. Para aplicações multi-inquilino que não conseguem fornecer esses links, a experiência de consentimento do usuário para a sua aplicação irá mostrar um alerta, o que pode desencorajar os utilizadores de consentir a aplicação.

> [!NOTE]
> * Aplicações de inquilino único não apresentará um alerta.
> * Se um ou ambos os links de dois estão em falta, a aplicação irá mostrar um alerta.

## <a name="user-consent-experience"></a>Experiência de consentimento do utilizador

Os exemplos seguintes mostram o utilizador consente experiência quando os termos de serviço e declaração de privacidade são configurados e quando não estiverem configuradas estas ligações.

![Capturas de ecrã com e sem uma declaração de privacidade e termos de serviço fornecido](./media/howto-add-terms-of-service-privacy-statement/user-consent-exp-privacy-statement-terms-service.png)

## <a name="formatting-links-to-the-terms-of-service-and-privacy-statement-documents"></a>Links para os termos de serviço e a privacidade documentos de instrução de formatação

Antes de adicionar links para os termos da sua aplicação de serviço e a privacidade documentos de instrução, certifique-se de que os URLs de seguir estas diretrizes.

| Diretriz     | Descrição                           |
|---------------|---------------------------------------|
| Formato        | URL válido                             |
| Esquemas válidas | HTTP e HTTPS<br/>Recomendamos que HTTPS |
| Comprimento máximo    | 2048 carateres                       |

Exemplos: `https://myapp.com/terms-of-service` e `https://myapp.com/privacy-statement`

## <a name="adding-links-to-the-terms-of-service-and-privacy-statement"></a>Adicionar ligações para os termos de serviço e declaração de privacidade

Quando os termos de serviço e declaração de privacidade encontram prontos, pode adicionar ligações a estes documentos na sua aplicação com um dos seguintes métodos:

* [Através do portal do Azure](#registered-in-azure-portal)
* [No Portal de registo de aplicação ou centro de desenvolvimento](#registered-in-app-reg-portal)
* [Usando o objeto de aplicação JSON](#app-object-json)
* [Usando a versão beta de MSGraph REST API](#msgraph-beta-rest-api)

### <a name="registered-in-azure-portal"></a>Se tiver registado a sua aplicação no portal do Azure

Se tiver registado a sua aplicação no portal do Azure, siga estes passos.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Navegue para o **registos das aplicações** secção e selecione a sua aplicação.
3. Abra o **propriedades** secção da aplicação.
4. Preencha os **URL de termos de serviço** e **URL de declaração de privacidade** campos.
5. Guarde as alterações.

    ![Secção de propriedades de aplicação com os termos de serviço e a privacidade URLs de instrução](./media/howto-add-terms-of-service-privacy-statement/azure-portal-terms-service-privacy-statement-urls.png)

### <a name="registered-in-app-reg-portal"></a>Se tiver registado a sua aplicação no Portal de registo de aplicação

Siga estes passos, se tiver registado a sua aplicação no Portal de registo de aplicação ou no Dev Center.

1. Inicie sessão para o [Portal de registo de aplicação](https://apps.dev.microsoft.com/).
2. Selecione a sua aplicação e desloque-se para o **perfil** secção.
3. Preencha os **URL de termos de serviço** e **URL de declaração de privacidade** campos.
4. Guarde as alterações.

    ![Seção de perfil de aplicação com os termos de URLs de declaração de serviço e de privacidade](./media/howto-add-terms-of-service-privacy-statement/app-registration-portal-profile-terms-service-privacy-statement-urls.png)

### <a name="app-object-json"></a>Usando o objeto de aplicação JSON

Se preferir modificar diretamente o objeto de aplicação JSON, pode utilizar o editor de manifesto no portal do Azure ou o Portal de registo de aplicação para incluir ligações para os termos da sua aplicação de serviço e declaração de privacidade.

```json
    "informationalUrls": { 
        "termsOfService": "<your_terms_of_service_url>", 
        "privacy": "<your_privacy_statement_url>" 
    }
```

### <a name="msgraph-beta-rest-api"></a>Usando a versão beta de MSGraph REST API

Para atualizar programaticamente todas as suas aplicações, pode utilizar a versão beta de MSGraph REST API para atualizar todas as suas aplicações para incluir ligações aos termos de serviço e a privacidade documentos de instrução.

```
PATCH https://graph.microsoft.com/beta/applications/{application id}
{ 
    "appId": "{your application id}", 
    "info": { 
        "termsOfServiceUrl": "<your_terms_of_service_url>", 
        "supportUrl": null, 
        "privacyStatementUrl": "<your_privacy_statement_url>", 
        "marketingUrl": null, 
        "logoUrl": null 
    }
}
```

> [!NOTE]
> * Tenha cuidado para não substituir quaisquer valores existentes se foi atribuído a qualquer um destes campos: `supportUrl`, `marketingUrl`, e `logoUrl`
> * A versão beta de MSGraph REST API só funcionará quando inicia sessão com uma conta do Azure AD. Contas Microsoft pessoais não são suportadas.
