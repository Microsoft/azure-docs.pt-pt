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
ms.openlocfilehash: 8fc85781f139b45e9e37f6e0f7cc36974041352d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78300014"
---
# <a name="how-to-configure-terms-of-service-and-privacy-statement-for-an-app"></a>Como configurar termos de serviço e declaração de privacidade para uma aplicação

Os desenvolvedores que constroem e gerem aplicações que se integrem com o Azure Ative Directory (Azure AD) e as contas da Microsoft devem incluir links para os termos de serviço e declaração de privacidade da aplicação. Os termos de serviço e declaração de privacidade são divulgados aos utilizadores através da experiência de consentimento do utilizador. Ajudam os seus utilizadores a saber que podem confiar na sua aplicação. Os termos de serviço e declaração de privacidade são especialmente críticos para aplicações multi-inquilinos viradas para o utilizador -- aplicações que são usadas por vários diretórios ou estão disponíveis para qualquer conta microsoft.

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

Exemplos: `https://myapp.com/terms-of-service` e`https://myapp.com/privacy-statement`

## <a name="adding-links-to-the-terms-of-service-and-privacy-statement"></a>Adicionar links aos termos de serviço e declaração de privacidade

Quando os termos de serviço e declaração de privacidade estiverem prontos, pode adicionar links a estes documentos na sua aplicação utilizando um destes métodos:

* [Através do portal Azure](#azure-portal)
* [Usando o objeto de aplicação JSON](#app-object-json)
* [Utilização da Microsoft Graph API](#msgraph-rest-api)

### <a name="using-the-azure-portal"></a><a name="azure-portal"></a>Usando o portal Azure
Siga estes passos no portal Azure.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).
2. Navegue na secção registos de **aplicações** e selecione a sua aplicação.
3. Abra o painel **de branding.**
4. Preencha os **termos dos termos de URL de serviço** e dos campos de URL da Declaração de **Privacidade.**
5. Guarde as alterações.

    ![As propriedades da aplicação contêm termos de serviço e declaração de privacidade URLs](./media/howto-add-terms-of-service-privacy-statement/azure-portal-terms-service-privacy-statement-urls.png)

### <a name="using-the-app-object-json"></a><a name="app-object-json"></a>Usando o objeto de aplicação JSON

Se preferir modificar diretamente o objeto da aplicação JSON, pode utilizar o manifesto editor no portal Azure ou portal de registo de aplicações para incluir links para os termos de serviço e declaração de privacidade da sua aplicação.

```json
    "informationalUrls": { 
        "termsOfService": "<your_terms_of_service_url>", 
        "privacy": "<your_privacy_statement_url>" 
    }
```

### <a name="using-the-microsoft-graph-api"></a><a name="msgraph-rest-api"></a>Utilização da Microsoft Graph API

Para atualizar programáticamente todas as suas aplicações, pode utilizar a API do Microsoft Graph para atualizar todas as suas aplicações para incluir links para os termos de serviço e documentos de declaração de privacidade.

```
PATCH https://graph.microsoft.com/v1.0/applications/{application id}
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
> * Tenha cuidado para não sobrepor quaisquer valores pré-existentes `supportUrl`que `marketingUrl`tenha atribuído a nenhum destes campos: , e`logoUrl`
> * O Microsoft Graph API só funciona quando se inscreveu numa conta Azure AD. As contas pessoais da Microsoft não são suportadas.
