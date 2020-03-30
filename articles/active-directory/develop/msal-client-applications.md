---
title: Aplicativos de clientes públicos e confidenciais (MSAL) / Azure
titleSuffix: Microsoft identity platform
description: Conheça as aplicações confidenciais de clientes públicos e clientes confidenciais na Biblioteca de Autenticação da Microsoft (MSAL).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/25/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: d59819c0ab614b0f6cc102c7ebe8c760fb851599
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77084129"
---
# <a name="public-client-and-confidential-client-applications"></a>Pedidos de cliente público e confidenciais
A Microsoft Authentication Library (MSAL) define dois tipos de clientes: clientes públicos e clientes confidenciais. Os dois tipos de clientes distinguem-se pela sua capacidade de autenticar de forma segura com o servidor de autorização e manter a confidencialidade das suas credenciais de cliente. Em contraste, a Biblioteca de Autenticação AD Azure (ADAL) utiliza o chamado contexto de *autenticação* (que é uma ligação ao Azure AD).

- **Aplicações confidenciais** de clientes são aplicações que funcionam em servidores (aplicações web, aplicações Web API ou até aplicações de serviço/daemon). São considerados de difícil acesso, e por isso são capazes de manter uma aplicação em segredo. Os clientes confidenciais podem guardar segredos de tempo de configuração. Cada instância do cliente tem uma configuração distinta (incluindo id do cliente e segredo do cliente). Estes valores são difíceis de extrair pelos utilizadores finais. Uma aplicação web é o cliente confidencial mais comum. O ID do cliente é exposto através do navegador web, mas o segredo é passado apenas no canal traseiro e nunca diretamente exposto.

    Aplicativos confidenciais de clientes: <BR>
    ![Web](media/msal-client-applications/web-app.png) ![app Web](media/msal-client-applications/web-api.png) ![API Daemon/serviço](media/msal-client-applications/daemon-service.png)

- **As aplicações públicas** do cliente são aplicações que funcionam em dispositivos ou computadores de secretária ou num navegador web. Não são de confiança para guardar em segurança os segredos da aplicação, pelo que apenas acedem a APIs web em nome do utilizador. (Apoiam apenas os fluxos públicos de clientes.) Os clientes públicos não podem guardar segredos de tempo de configuração, por isso não têm segredos de clientes.

    Aplicativos de clientes públicos: <BR>
    ![Aplicativo](media/msal-client-applications/desktop-app.png) ![de ambiente](media/msal-client-applications/browserless-app.png) ![de trabalho Aplicação API Mobile sem navegador](media/msal-client-applications/mobile-app.png)

> [!NOTE]
> Em MSAL.js, não há separação de aplicações públicas e confidenciais de clientes.  MSAL.js representa aplicações de clientes como aplicações baseadas em agentes de utilizador, clientes públicos em que o código do cliente é executado num agente de utilizador como um navegador web. Estes clientes não armazenam segredos porque o contexto do navegador é abertamente acessível.

## <a name="comparing-the-client-types"></a>Comparando os tipos de clientes
Aqui ficam algumas semelhanças e diferenças entre clientes públicos e aplicações confidenciais de clientes:

- Ambos os tipos de aplicações mantêm uma cache simbólica do utilizador e podem adquirir um símbolo silenciosamente (quando o token já está na cache token). As aplicações confidenciais do cliente também têm uma cache de token de aplicação para tokens que são para a própria app.
- Ambos os tipos de aplicações gerem as contas dos utilizadores e podem obter uma conta do cache token do utilizador, obter uma conta do seu identificador ou remover uma conta.
- As aplicações de clientes públicos têm quatro formas de adquirir um símbolo (quatro fluxos de autenticação). As aplicações confidenciais do cliente têm três formas de adquirir um símbolo (e uma forma de calcular o URL do fornecedor de identidade autorizar endpoint). Para mais informações, consulte [fichas de aquisição.](msal-acquire-cache-tokens.md)

Se utilizou o ADAL, poderá notar que, ao contrário do contexto de autenticação da ADAL, na MSAL o ID do cliente (também chamado de ID de *aplicação* ou ID da *aplicação)* é passado uma vez na construção da aplicação. Não precisa de ser retransmitida quando a app adquire um símbolo. Isto é verdade tanto para aplicações públicas como confidenciais para clientes. Os construtores de aplicações confidenciais de clientes também são aprovados credenciais de cliente: o segredo que partilham com o fornecedor de identidade.

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre:
- [Opções de configuração de aplicação de cliente](msal-client-application-configuration.md)
- [Aplicações instantâneas do cliente utilizando MSAL.NET](msal-net-initializing-client-applications.md)
- [Aplicações instantâneas de clientes utilizando MSAL.js](msal-js-initializing-client-applications.md)
