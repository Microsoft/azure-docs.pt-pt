---
title: Termos de Serviço e declaração de privacidade para apps | Rio Azure
description: Saiba como pode configurar os termos de serviço e declaração de privacidade para aplicações registadas para usar Azure AD.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 05/22/2019
ms.author: ryanwi
ms.reviewer: lenalepa, sureshja
ms.custom: aaddev
ms.openlocfilehash: 311dfd976610c392909a0ec3d91fecaa4d733539
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100104200"
---
# <a name="how-to-configure-terms-of-service-and-privacy-statement-for-an-app"></a>Como: Configurar termos de serviço e declaração de privacidade para uma aplicação

Os desenvolvedores que constroem e gerem aplicações que se integram com o Azure Ative Directory (Azure AD) e as contas da Microsoft devem incluir links para os termos de serviço e declaração de privacidade da aplicação. Os termos de serviço e declaração de privacidade são divulgados aos utilizadores através da experiência de consentimento do utilizador. Ajudam os seus utilizadores a saber que podem confiar na sua aplicação. Os termos de serviço e declaração de privacidade são especialmente críticos para aplicações multi-inquilinos viradas para o utilizador-- aplicações que são usadas por vários diretórios ou estão disponíveis para qualquer conta da Microsoft.

É responsável pela criação dos termos de serviço e documentos de declaração de privacidade para a sua aplicação e por fornecer os URLs a estes documentos. Para aplicações multi-arrendadas que não forneçam estes links, a experiência de consentimento do utilizador para a sua aplicação mostrará um alerta, o que poderá desencorajar os utilizadores de consentirem com a sua aplicação.

> [!NOTE]
> * Aplicativos de inquilino único não mostrarão um alerta.
> * Se faltar um ou ambos os links, a sua aplicação mostrará um alerta.

## <a name="user-consent-experience"></a>Experiência de consentimento do utilizador

Os exemplos a seguir mostram a experiência de consentimento do utilizador quando os termos de serviço e declaração de privacidade são configurados e quando estes links não estão configurados.

![Screenshots com e sem declaração de privacidade e termos de serviço fornecidos](./media/howto-add-terms-of-service-privacy-statement/user-consent-exp-privacy-statement-terms-service.png)

## <a name="formatting-links-to-the-terms-of-service-and-privacy-statement-documents"></a>Formatação links para os termos de serviço e documentos de declaração de privacidade

Antes de adicionar links aos termos de serviço e documentos de declaração de privacidade da sua aplicação, certifique-se de que os URLs seguem estas diretrizes.

| Diretriz     | Descrição                           |
|---------------|---------------------------------------|
| Formato        | URL válido                             |
| Esquemas válidos | HTTP e HTTPS<br/>Recomendamos HTTPS |
| Comprimento máximo    | 2048 caracteres                       |

Exemplos: `https://myapp.com/terms-of-service` e `https://myapp.com/privacy-statement`

## <a name="adding-links-to-the-terms-of-service-and-privacy-statement"></a>Adicionar links aos termos de serviço e declaração de privacidade

Quando os termos de serviço e declaração de privacidade estiverem prontos, pode adicionar links a estes documentos na sua aplicação utilizando um destes métodos:

* [Através do portal Azure](#azure-portal)
* [Usando o objeto de aplicação JSON](#app-object-json)
* [Utilização da API do Gráfico microsoft](#msgraph-rest-api)

### <a name="using-the-azure-portal"></a><a name="azure-portal"></a>Utilizar o portal do Azure
Siga estes passos no portal Azure.

1. Inscreva-se no <a href="https://portal.azure.com/" target="_blank">portal Azure</a> e selecione o inquilino AD AD correto (não B2C).
2. Navegue na secção **de registos** da App e selecione a sua aplicação.
3. Under **Manage**, selecione **Branding**.
4. Preencha os termos **de URL de serviço** e url de Declaração de **Privacidade.**
5. Selecione **Guardar**.

    ![As propriedades da aplicação contêm termos de serviço e declaração de privacidade URLs](./media/howto-add-terms-of-service-privacy-statement/azure-portal-terms-service-privacy-statement-urls.png)

### <a name="using-the-app-object-json"></a><a name="app-object-json"></a>Usando o objeto de aplicação JSON

Se preferir modificar diretamente o objeto da aplicação JSON, pode utilizar o editor manifesto no portal Azure ou no Portal de Registo de Aplicações para incluir links para os termos de serviço e declaração de privacidade da sua aplicação.

1. Navegando na secção registos de **aplicações** e selecione a sua aplicação.
2. Abra o **painel manifesto.**
3. Ctrl+F, Procura por "informationalUrls". Preencha a informação.
4. Guarde as alterações.

```json
    "informationalUrls": { 
        "termsOfService": "<your_terms_of_service_url>", 
        "privacy": "<your_privacy_statement_url>" 
    }
```

### <a name="using-the-microsoft-graph-api"></a><a name="msgraph-rest-api"></a>Utilização da API do Gráfico microsoft

Para atualizar programaticamente todas as suas aplicações, pode utilizar a API do Microsoft Graph para atualizar todas as suas aplicações para incluir links para os termos de serviço e documentos de declaração de privacidade.

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
> * Tenha cuidado para não substituir os valores pré-existentes que atribuiu a qualquer um destes `supportUrl` campos: `marketingUrl``logoUrl`
> * A API do Microsoft Graph só funciona quando faz súpido com uma conta AD Azure. As contas pessoais da Microsoft não são suportadas.
