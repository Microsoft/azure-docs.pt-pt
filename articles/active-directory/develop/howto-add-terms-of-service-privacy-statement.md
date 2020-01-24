---
title: Termos de Serviço e declaração de privacidade para apps Azure
description: Saiba como pode configurar os termos de serviço e declaração de privacidade para aplicações registadas para utilizar o Azure AD.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/22/2019
ms.author: ryanwi
ms.reviwer: lenalepa, sureshja
ms.custom: aaddev
ms.openlocfilehash: f28c33f20556825d84edda34752ac64714327526
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76697341"
---
# <a name="how-to-configure-terms-of-service-and-privacy-statement-for-an-app"></a>Como configurar termos de serviço e declaração de privacidade para uma aplicação

Os desenvolvedores que constroem e gerem aplicações que se integrem com o Azure Ative Directory (Azure AD) e as contas da Microsoft devem incluir links para os termos de serviço e declaração de privacidade da aplicação. Os termos de serviço e a política de privacidade são exibidos para os usuários por meio da experiência de consentimento do usuário. Ajudam os seus utilizadores a saber que podem confiar na sua aplicação. Os termos de serviço e declaração de privacidade são especialmente críticos para aplicações multi-inquilinos viradas para o utilizador -- aplicações que são usadas por vários diretórios ou estão disponíveis para qualquer conta microsoft.

É responsável pela criação dos termos de documentos de declaração de serviço e privacidade para a sua aplicação e pelo fornecimento dos URLs a estes documentos. Para aplicações multi-inquilinos que não forneçam estes links, a experiência de consentimento do utilizador para a sua aplicação mostrará um alerta, o que poderá desencorajar os utilizadores de consentirem a sua aplicação.

> [!NOTE]
> * Aplicativos de inquilino único não mostrarão um alerta.
> * Se faltar um ou ambos os links, a sua aplicação mostrará um alerta.

## <a name="user-consent-experience"></a>Experiência de consentimento do utilizador

Os seguintes exemplos mostram a experiência de consentimento do utilizador quando os termos de serviço e declaração de privacidade estão configurados e quando estes links não estão configurados.

![Screenshots com e sem declaração de privacidade e termos de serviço fornecidos](./media/howto-add-terms-of-service-privacy-statement/user-consent-exp-privacy-statement-terms-service.png)

## <a name="formatting-links-to-the-terms-of-service-and-privacy-statement-documents"></a>Formatação de links para os termos de documentos de declaração de serviço e privacidade

Antes de adicionar links aos termos de serviço e documentos de declaração de privacidade da sua aplicação, certifique-se de que os URLs seguem estas orientações.

| Diretriz     | Descrição                           |
|---------------|---------------------------------------|
| Formato        | URL válido                             |
| Schemas válidos | HTTP e HTTPS<br/>Recomendamos HTTPS |
| Comprimento máximo    | Caracteres de 2048                       |

Exemplos: `https://myapp.com/terms-of-service` e `https://myapp.com/privacy-statement`

## <a name="adding-links-to-the-terms-of-service-and-privacy-statement"></a>Adicionar links aos termos de serviço e declaração de privacidade

Quando os termos de serviço e declaração de privacidade estiverem prontos, pode adicionar links a estes documentos na sua aplicação utilizando um destes métodos:

* [Através do portal Azure](#azure-portal)
* [Usando o objeto de aplicação JSON](#app-object-json)
* [Usando a MSGraph beta REST API](#msgraph-beta-rest-api)

### <a name="azure-portal"></a>Usando o portal Azure
Siga estes passos no portal Azure.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).
2. Navegue na secção registos de **aplicações** e selecione a sua aplicação.
3. Abra o painel **de branding.**
4. Preencha os **termos dos termos de URL de serviço** e dos campos de URL da Declaração de **Privacidade.**
5. Guarde as alterações.

    ![As propriedades da aplicação contêm termos de serviço e declaração de privacidade URLs](./media/howto-add-terms-of-service-privacy-statement/azure-portal-terms-service-privacy-statement-urls.png)

### <a name="app-object-json"></a>Usando o objeto de aplicação JSON

Se preferir modificar diretamente o objeto da aplicação JSON, pode utilizar o manifesto editor no portal Azure ou portal de registo de aplicações para incluir links para os termos de serviço e declaração de privacidade da sua aplicação.

```json
    "informationalUrls": { 
        "termsOfService": "<your_terms_of_service_url>", 
        "privacy": "<your_privacy_statement_url>" 
    }
```

### <a name="msgraph-beta-rest-api"></a>Usando a MSGraph beta REST API

Para atualizar programáticamente todas as suas aplicações, pode utilizar a API beta do MSGraph para atualizar todas as suas aplicações para incluir links para os termos de serviço e documentos de declaração de privacidade.

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
> * Tenha cuidado para não sobrepor quaisquer valores pré-existentes que tenha atribuído a nenhum destes campos: `supportUrl`, `marketingUrl`e `logoUrl`
> * A API beta do MSGraph só funcionará quando iniciar sessão com uma conta Azure AD. As contas pessoais da Microsoft não são suportadas.
