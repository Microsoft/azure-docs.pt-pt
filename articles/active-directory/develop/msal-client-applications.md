---
title: Aplicativos cliente (biblioteca de autenticação da Microsoft) | Azure
description: Saiba mais sobre o cliente público e os aplicativos cliente confidenciais na MSAL (biblioteca de autenticação da Microsoft).
services: active-directory
documentationcenter: dev-center-name
author: TylerMSFT
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/25/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8c854cc34a1ea50f37428cfc18146618d516de7d
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/18/2019
ms.locfileid: "69532968"
---
# <a name="public-client-and-confidential-client-applications"></a>Cliente público e aplicativos cliente confidenciais
A MSAL (biblioteca de autenticação da Microsoft) define dois tipos de clientes: clientes públicos e clientes confidenciais. Os dois tipos de cliente são diferenciados por sua capacidade de autenticar com segurança com o servidor de autorização e manter a confidencialidade de suas credenciais de cliente. Por outro lado, a ADAL (biblioteca de autenticação do Azure AD) usa o *contexto de autenticação* chamado (que é uma conexão com o Azure AD).

- **Aplicativos cliente confidenciais** são aplicativos que são executados em servidores (aplicativos Web, aplicativos de API Web ou até mesmo aplicativos de serviço/daemon). Eles são considerados difíceis de acessar e, por esse motivo, capazes de manter um segredo do aplicativo. Clientes confidenciais podem conter segredos de tempo de configuração. Cada instância do cliente tem uma configuração distinta (incluindo a ID do cliente e o segredo do cliente). Esses valores são difíceis para os usuários finais extraírem. Um aplicativo Web é o cliente confidencial mais comum. A ID do cliente é exposta por meio do navegador da Web, mas o segredo é passado apenas no canal de fundo e nunca exposto diretamente.

    Aplicativos cliente confidenciais: <BR>
    ![Web ](media/msal-client-applications/web-app.png) de API ![Web ](media/msal-client-applications/web-api.png) do aplicativo ![Daemon/Service ](media/msal-client-applications/daemon-service.png)

- **Aplicativos cliente públicos** são aplicativos que são executados em dispositivos ou computadores desktop ou em um navegador da Web. Eles não são confiáveis para manter os segredos do aplicativo com segurança e, portanto, acessam apenas APIs da Web em nome do usuário. (Eles dão suporte apenas a fluxos de cliente públicos.) Os clientes públicos não podem conter segredos de tempo de configuração, portanto, não têm segredos de cliente.

    Aplicativos cliente públicos: <BR>
    ![Desktop aplicativo ](media/msal-client-applications/desktop-app.png) API ![Browserless ](media/msal-client-applications/browserless-app.png) ![Mobile aplicativo ](media/msal-client-applications/mobile-app.png)

> [!NOTE]
> No MSAL. js, não há nenhuma separação de aplicativos cliente públicos e confidenciais.  O MSAL. js representa os aplicativos cliente como aplicativos baseados em agente do usuário, clientes públicos em que o código do cliente é executado em um agente do usuário como um navegador da Web. Esses clientes não armazenam segredos porque o contexto do navegador é acessível de aberto.

## <a name="comparing-the-client-types"></a>Comparando os tipos de cliente
Aqui estão algumas semelhanças e diferenças entre o cliente público e os aplicativos cliente confidenciais:

- Ambos os tipos de aplicativo mantêm um cache de token de usuário e podem adquirir um token silenciosamente (quando o token já está no cache de token). Os aplicativos cliente confidenciais também têm um cache de token de aplicativo para tokens que são para o próprio aplicativo.
- Os dois tipos de aplicativo gerenciam contas de usuário e podem obter uma conta do cache de token de usuário, obter uma conta de seu identificador ou remover uma conta.
- Os aplicativos cliente públicos têm quatro maneiras de adquirir um token (quatro fluxos de autenticação). Os aplicativos cliente confidenciais têm três maneiras de adquirir um token (e uma maneira de computar a URL do ponto de extremidade de autorização do provedor de identidade). Para obter mais informações, consulte [adquirindo tokens](msal-acquire-cache-tokens.md).

Se você já usou a ADAL, talvez perceba que, ao contrário do contexto de autenticação da ADAL, em MSAL, a ID do cliente (também chamada de *ID do aplicativo* ou *ID*do aplicativo) é passada uma vez na construção do aplicativo. Ele não precisa ser passado novamente quando o aplicativo adquire um token. Isso é verdadeiro para aplicativos cliente públicos e confidenciais. Os construtores de aplicativos cliente confidenciais também são aprovados nas credenciais do cliente: o segredo que eles compartilham com o provedor de identidade.

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre:
- [Opções de configuração de aplicativo cliente](msal-client-application-configuration.md)
- [Criando uma instância de aplicativos cliente usando MSAL.NET](msal-net-initializing-client-applications.md)
- [Criando uma instância de aplicativos cliente usando MSAL. js](msal-js-initializing-client-applications.md)
