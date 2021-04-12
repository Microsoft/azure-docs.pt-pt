---
title: Aplicativos de clientes públicos e confidenciais (MSAL) | Rio Azure
titleSuffix: Microsoft identity platform
description: Conheça as aplicações de cliente público e confidencial na Biblioteca de Autenticação da Microsoft (MSAL).
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
ms.openlocfilehash: 2ef29f6fe3403809d01fcea382474c514319b7c0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98063761"
---
# <a name="public-client-and-confidential-client-applications"></a>Aplicações de cliente público e confidencial
A Microsoft Authentication Library (MSAL) define dois tipos de clientes: clientes públicos e clientes confidenciais. Os dois tipos de cliente distinguem-se pela sua capacidade de autenticar de forma segura com o servidor de autorização e manter a confidencialidade das suas credenciais de cliente. Em contraste, a Azure AD Authentication Library (ADAL) utiliza o chamado contexto de *autenticação* (que é uma ligação ao Azure AD).

- **As aplicações confidenciais de clientes** são aplicações que funcionam em servidores (aplicações web, aplicações API web ou até mesmo aplicações de serviço/daemon). São considerados difíceis de aceder, e por isso são capazes de manter uma aplicação em segredo. Os clientes confidenciais podem guardar segredos de tempo de configuração. Cada instância do cliente tem uma configuração distinta (incluindo iD do cliente e segredo de cliente). Estes valores são difíceis de extrair pelos utilizadores finais. Uma aplicação web é o cliente confidencial mais comum. O ID do cliente é exposto através do navegador web, mas o segredo é passado apenas no canal de trás e nunca diretamente exposto.

    Aplicativos confidenciais de clientes: <BR>
    ![Web app ](media/msal-client-applications/web-app.png) ![ Web API ](media/msal-client-applications/web-api.png) ![ Daemon/serviço](media/msal-client-applications/daemon-service.png)

- **As aplicações de clientes públicos** são aplicações que funcionam em dispositivos ou computadores de secretária ou num navegador web. Não são de confiança para manter em segurança os segredos da aplicação, por isso só acedem a APIs web em nome do utilizador. (Suportam apenas fluxos de clientes públicos.) Os clientes públicos não podem guardar segredos do tempo de configuração, por isso não têm segredos de clientes.

    Aplicativos de cliente público: <BR>
    ![Aplicativo de desktop ](media/msal-client-applications/desktop-app.png) ![ Browserless API ](media/msal-client-applications/browserless-app.png) ![ Mobile](media/msal-client-applications/mobile-app.png)

> [!NOTE]
> Em MSAL.js, não existe separação de aplicações de clientes públicos e confidenciais.  MSAL.js representa as aplicações do cliente como aplicações baseadas em agentes de utilizadores, clientes públicos em que o código do cliente é executado num agente utilizador como um navegador web. Estes clientes não armazenam segredos porque o contexto do navegador é abertamente acessível.

## <a name="comparing-the-client-types"></a>Comparando os tipos de clientes
Aqui estão algumas semelhanças e diferenças entre cliente público e aplicações confidenciais de clientes:

- Ambos os tipos de aplicações mantêm uma cache de token do utilizador e podem adquirir um símbolo silenciosamente (quando o símbolo já está na cache simbólica). As aplicações confidenciais de clientes também têm uma cache de fichas de aplicação para tokens que são para a própria app.
- Ambos os tipos de aplicações gerem contas de utilizadores e podem obter uma conta a partir da cache do token do utilizador, obter uma conta do seu identificador ou remover uma conta.
- As aplicações de clientes públicos têm quatro formas de adquirir um token (quatro fluxos de autenticação). As aplicações confidenciais de clientes têm três formas de adquirir um token (e uma maneira de calcular o URL do fornecedor de identidade autorizar o ponto final). Para mais informações, consulte [a Aquisição de fichas.](msal-acquire-cache-tokens.md)

Se utilizou o ADAL, poderá notar que, ao contrário do contexto de autenticação da ADAL, no MSAL o ID do cliente (também chamado *de ID* de aplicação ou *ID de aplicação)* é aprovado uma vez na construção da aplicação. Não precisa de ser passado novamente quando a app adquire um token. Isto é verdade tanto para aplicações de clientes públicos como confidenciais. Os construtores de aplicações confidenciais de clientes também são passados credenciais de cliente: o segredo que partilham com o fornecedor de identidade.

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre:
- [Opções de configuração de aplicação de cliente](msal-client-application-configuration.md)
- [Instantiing aplicações de clientes utilizando MSAL.NET](msal-net-initializing-client-applications.md)
- [Instantiing aplicações de clientes utilizando MSAL.js](msal-js-initializing-client-applications.md)
