---
title: Aplicações de cliente (biblioteca de autenticação da Microsoft) | Azure
description: Saiba mais sobre o cliente público e confidencial cliente aplicações no Microsoft Authentication Library (MSAL).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: celested
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
ms.openlocfilehash: f21aa62bae7599cf586ccf6f885ad9f58c148d1f
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65077144"
---
# <a name="public-client-and-confidential-client-applications"></a>Aplicativos de cliente confidencial e cliente público
Biblioteca de autenticação da Microsoft (MSAL) define dois tipos de clientes: os clientes públicos e confidencial. Os tipos de cliente de dois são distinguidos pela sua capacidade de autenticar de forma segura com o servidor de autorização e manter a confidencialidade das respetivas credenciais de cliente.  Por outro lado, o Azure AD Authentication Library (ADAL) tem o conceito de contexto de autenticação (que é uma ligação para o Azure AD).

- **Aplicações de cliente confidencial** são aplicativos, que é executado em servidores (aplicações Web, Web API ou até mesmo os aplicativos de serviço/daemon). Eles são considerados difíceis de acesso e, portanto, com capacidade de manter um segredo de aplicação. Clientes confidenciais são capazes de suportar a configuração de segredos Monouso. Cada instância do cliente tem uma configuração distinta (incluindo o ID de cliente e segredo). Estes valores são difícil para os utilizadores finais extrair. Uma aplicação web é o cliente confidencial mais comuns. O ID de cliente é exposto por meio do navegador da web, mas o segredo é transmitido apenas no canal back e nunca diretamente exposto.

    Aplicações de cliente confidencial: <BR>
    ![Aplicação Web](media/msal-client-applications/web-app.png) ![Web API](media/msal-client-applications/web-api.png)  ![ /serviço de Daemon](media/msal-client-applications/daemon-service.png)

- **Aplicativos cliente público** são aplicativos, que são executadas em dispositivos ou máquinas desktop ou num navegador da web. Não são confiáveis para manter com segurança segredos da aplicação e, por conseguinte, só aceder a Web APIs em nome do utilizador (eles somente oferecem suporte fluxos de cliente público). Os clientes públicos são não é possível armazenar segredos de tempo de configuração e assim não ter nenhum segredo do cliente.

    Aplicações de cliente pública: <BR>
    ![Aplicação de ambiente de trabalho](media/msal-client-applications/desktop-app.png) ![Browserless API](media/msal-client-applications/browserless-app.png) ![aplicação móvel](media/msal-client-applications/mobile-app.png)

> [!NOTE]
> Em msal, não existe nenhuma separação de aplicações de cliente públicos e confidencial.  Msal representa as aplicações de cliente como aplicativos baseados no agente de utilizador, um cliente público no qual o código de cliente é executado num agente de usuário, como um navegador da web.  Estes clientes não armazenar segredos, uma vez que o contexto do navegador é acessível abertamente.

## <a name="comparing-the-client-types"></a>Comparar os tipos de cliente
Há alguns aspectos comuns e as diferenças entre o cliente público e confidencial cliente aplicativos:

- Ambos os tipos de aplicativos mantém um cache de token de utilizador e podem adquirir um token silenciosamente (nos casos em que o token já está a ser o cache de tokens). As aplicações de cliente confidencial também têm uma cache de tokens de aplicação para os tokens, o que são para a aplicação em si.
- Ambos gerir contas de utilizador e podem obter as contas da cache de token de utilizador, obter uma conta do seu identificador ou remover uma conta.
- As aplicações de cliente público têm quatro formas de adquirir um token (quatro fluxos de autenticação), ao passo que as aplicações de cliente confidencial têm três (e um método para computar o URL do fornecedor de identidade de autorizar o ponto final). Para obter mais informações, consulte cenários e aquisição de tokens.

Se utilizou a ADAL no passado, pode observar que, ao contrário do contexto de autenticação da ADAL, no MSAL o cliente ID (também denominada ID da aplicação ou ID da aplicação) é passada uma vez na construção do aplicativo e já não precisa ser repetido ao adquirir um token. Esse é o caso tanto para aplicativos cliente públicos e confidencial. Construtores de aplicativos clientes confidenciais também são transmitidas credenciais de cliente: o segredo que partilham com o fornecedor de identidade.

## <a name="next-steps"></a>Passos Seguintes
Saiba mais sobre:
- [Opções de configuração de aplicação de cliente](msal-client-application-configuration.md)
- [Criar uma instância de aplicações de cliente com MSAL.NET](msal-net-initializing-client-applications.md).
- [Criação de instâncias de aplicações de cliente com msal](msal-js-initializing-client-applications.md).
