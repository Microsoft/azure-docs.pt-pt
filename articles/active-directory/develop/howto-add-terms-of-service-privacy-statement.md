---
title: Termos de serviço e política de privacidade para aplicativos | Azure
description: Saiba como você pode configurar os termos de serviço e a política de privacidade para aplicativos registrados para usar o Azure AD.
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9ca59120bcfadc6c75e3687be84a0e70f97fc3c2
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74842816"
---
# <a name="how-to-configure-terms-of-service-and-privacy-statement-for-an-app"></a>Como: configurar os termos de serviço e a política de privacidade para um aplicativo

Os desenvolvedores que criam e gerenciam aplicativos que se integram ao Azure Active Directory (Azure AD) e às contas da Microsoft devem incluir links para os termos de serviço e a política de privacidade do aplicativo. Os termos de serviço e a política de privacidade são exibidos para os usuários por meio da experiência de consentimento do usuário. Eles ajudam os usuários a saber que eles podem confiar em seu aplicativo. Os termos de serviço e a declaração de privacidade são especialmente críticos para aplicativos multilocatário voltados para o usuário – aplicativos que são usados por vários diretórios ou estão disponíveis para qualquer conta Microsoft.

Você é responsável por criar os termos de serviço e documentos de política de privacidade para seu aplicativo e para fornecer as URLs a esses documentos. Para aplicativos multilocatários que não fornecem esses links, a experiência de consentimento do usuário para seu aplicativo mostrará um alerta, o que pode desencorajar os usuários de consentirem o seu aplicativo.

> [!NOTE]
> * Os aplicativos de locatário único não mostrarão um alerta.
> * Se um ou ambos os dois links estiverem ausentes, seu aplicativo mostrará um alerta.

## <a name="user-consent-experience"></a>Experiência de consentimento do usuário

Os exemplos a seguir mostram a experiência de consentimento do usuário quando os termos de serviço e a política de privacidade são configurados e quando esses links não são configurados.

![Capturas de tela com e sem uma declaração de privacidade e os termos de serviço fornecidos](./media/howto-add-terms-of-service-privacy-statement/user-consent-exp-privacy-statement-terms-service.png)

## <a name="formatting-links-to-the-terms-of-service-and-privacy-statement-documents"></a>Links de formatação para os termos de serviço e documentos de política de privacidade

Antes de adicionar links aos termos de serviço e documentos de política de privacidade do seu aplicativo, verifique se as URLs seguem estas diretrizes.

| Diretriz     | Descrição                           |
|---------------|---------------------------------------|
| Formato        | URL válida                             |
| Esquemas válidos | HTTP e HTTPS<br/>Recomendamos HTTPS |
| Comprimento máximo    | 2048 caracteres                       |

Exemplos: `https://myapp.com/terms-of-service` e `https://myapp.com/privacy-statement`

## <a name="adding-links-to-the-terms-of-service-and-privacy-statement"></a>Adicionando links aos termos de serviço e à política de privacidade

Quando os termos de serviço e a política de privacidade estiverem prontos, você poderá adicionar links a esses documentos em seu aplicativo usando um destes métodos:

* [Por meio do portal do Azure](#azure-portal)
* [Usando o JSON do objeto de aplicativo](#app-object-json)
* [Usando a API REST do MSGraph beta](#msgraph-beta-rest-api)

### <a name="azure-portal"></a>Usando o portal do Azure
Siga estas etapas na portal do Azure.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Navegue até a seção **registros do aplicativo** e selecione seu aplicativo.
3. Abra o painel de **identidade visual** .
4. Preencha os campos **de URL dos termos de serviço e de política de** **privacidade** .
5. Guarde as alterações.

    ![As propriedades do aplicativo contêm termos de serviço e URLs de política de privacidade](./media/howto-add-terms-of-service-privacy-statement/azure-portal-terms-service-privacy-statement-urls.png)

### <a name="app-object-json"></a>Usando o JSON do objeto de aplicativo

Se você preferir modificar o objeto de aplicativo JSON diretamente, poderá usar o editor de manifesto no portal do Azure ou no portal de registro de aplicativo para incluir links para os termos de serviço e a política de privacidade do seu aplicativo.

```json
    "informationalUrls": { 
        "termsOfService": "<your_terms_of_service_url>", 
        "privacy": "<your_privacy_statement_url>" 
    }
```

### <a name="msgraph-beta-rest-api"></a>Usando a API REST do MSGraph beta

Para atualizar todos os seus aplicativos programaticamente, você pode usar a API REST do MSGraph beta para atualizar todos os seus aplicativos a fim de incluir links para os documentos de termos de serviço e de política de privacidade.

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
> * Tenha cuidado para não substituir os valores preexistentes que você atribuiu a qualquer um destes campos: `supportUrl`, `marketingUrl`e `logoUrl`
> * A API REST do MSGraph beta só funcionará quando você entrar com uma conta do Azure AD. Não há suporte para contas pessoais da Microsoft.
