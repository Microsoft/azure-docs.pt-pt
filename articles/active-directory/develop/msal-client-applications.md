---
title: Aplicações de cliente (biblioteca de autenticação da Microsoft) | Azure
description: Saiba mais sobre o cliente público e confidencial cliente aplicações no Microsoft Authentication Library (MSAL).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/25/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9d09436b9a2ac38e7b07a51f01d65769ed19d08e
ms.sourcegitcommit: 087ee51483b7180f9e897431e83f37b08ec890ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/31/2019
ms.locfileid: "66430822"
---
# <a name="public-client-and-confidential-client-applications"></a>Aplicativos de cliente confidencial e cliente público
Biblioteca de autenticação da Microsoft (MSAL) define dois tipos de clientes: os clientes públicos e confidencial. Os tipos de cliente de dois são distinguidos pela sua capacidade de autenticar de forma segura com o servidor de autorização e manter a confidencialidade das respetivas credenciais de cliente. Por outro lado, o Azure AD Authentication Library (ADAL) usa o que é chamado *contexto de autenticação* (que é uma ligação para o Azure AD).

- **Aplicações de cliente confidencial** são aplicações que são executadas em servidores (aplicações web, aplicações de Web API ou até mesmo as aplicações de serviço/daemon). Que sejam considerados difícil de acesso e por esse motivo com capacidade de manter um segredo de aplicação. Clientes confidenciais podem armazenar segredos de tempo de configuração. Cada instância do cliente tem uma configuração distinta (incluindo o ID de cliente e segredo do cliente). Estes valores são difícil para os utilizadores finais extrair. Uma aplicação web é o cliente confidencial mais comuns. O ID de cliente é exposto por meio do navegador da web, mas o segredo é transmitido apenas no canal back e nunca diretamente exposto.

    Aplicações de cliente confidencial: <BR>
    ![Aplicação Web](media/msal-client-applications/web-app.png) ![Web API](media/msal-client-applications/web-api.png)  ![ /serviço de Daemon](media/msal-client-applications/daemon-service.png)

- **Aplicativos cliente público** são aplicações que são executadas em dispositivos ou computadores de secretária ou num navegador da web. Eles não são confiáveis para manter com segurança segredos da aplicação, para que eles apenas aceder às APIs da Web em nome do utilizador. (Que suportam apenas os fluxos de cliente público). Os clientes públicos não é possível armazenar segredos de tempo de configuração, para que não tenham segredos do cliente.

    Aplicações de cliente público: <BR>
    ![Aplicação de ambiente de trabalho](media/msal-client-applications/desktop-app.png) ![Browserless API](media/msal-client-applications/browserless-app.png) ![aplicação móvel](media/msal-client-applications/mobile-app.png)

> [!NOTE]
> Em msal, não existe nenhuma separação de aplicações de cliente públicos e confidencial.  Msal representa as aplicações de cliente, como aplicações de utilizadores com base em agente, os clientes públicos em que o código de cliente é executado num agente de usuário, como um navegador da web. Estes clientes não armazenam segredos, porque o contexto do navegador é acessível abertamente.

## <a name="comparing-the-client-types"></a>Comparar os tipos de cliente
Aqui estão algumas semelhanças e diferenças entre o cliente público e confidencial cliente aplicações:

- Os dois tipos de aplicação mantém um cache de token de utilizador e podem adquirir um token silenciosamente (quando o token já está a ser o cache de tokens). Aplicações de cliente confidencial também têm uma cache de tokens de aplicação para os tokens destinam-se a aplicação em si.
- Ambos os tipos de aplicação gerir contas de utilizador e podem obter uma conta a partir da cache de token de utilizador, obter uma conta do seu identificador ou remover uma conta.
- Aplicações de cliente público têm quatro formas de adquirir um token (quatro fluxos de autenticação). Aplicações de cliente confidencial têm três formas de adquirir um token (e uma forma para computar o URL do fornecedor de identidade de autorizar o ponto final). Para obter mais informações, consulte [aquisição de tokens](msal-acquire-cache-tokens.md).

Se utilizou a ADAL, pode observar que, ao contrário do contexto de autenticação da ADAL, no MSAL o ID de cliente (também chamado do *ID da aplicação* ou *ID de aplicação*) é passado, uma vez, na construção do aplicativo. Não tem de ser transmitido novamente quando a aplicação recebe um token. Isso é verdadeiro para ambos para aplicações de cliente públicos e confidencial. Construtores de aplicações de cliente confidencial também são transmitidas credenciais de cliente: o segredo que partilham com o fornecedor de identidade.

## <a name="next-steps"></a>Passos Seguintes
Saiba mais sobre:
- [Opções de configuração de aplicação de cliente](msal-client-application-configuration.md)
- [Criar uma instância de aplicativos cliente usando MSAL.NET](msal-net-initializing-client-applications.md)
- [Criar uma instância de aplicativos de cliente através de msal](msal-js-initializing-client-applications.md)
