---
title: Opções de assinatura de certificado de token SAML avançado para aplicativos do Azure AD previamente integrados | Microsoft Docs
description: Saiba como usar as opções avançadas de assinatura de certificado no token SAML para aplicativos previamente integrados no Azure Active Directory
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: mimart
ms.reviewer: jeedes
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: ea99344399dbbbc17f0d7381e54ab68ae4bcc78f
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/22/2019
ms.locfileid: "68381277"
---
# <a name="advanced-certificate-signing-options-in-the-saml-token-for-gallery-apps-in-azure-active-directory"></a>Opções avançadas de assinatura de certificado no token SAML para aplicativos da galeria no Azure Active Directory

Hoje Azure Active Directory (Azure AD) dá suporte a milhares de aplicativos previamente integrados na Galeria de aplicativos Azure Active Directory. Mais de 500 dos aplicativos dão suporte ao logon único usando o protocolo [Security Assertion Markup Language](https://wikipedia.org/wiki/Security_Assertion_Markup_Language) (SAML) 2,0, como o aplicativo [NetSuite](https://azuremarketplace.microsoft.com/marketplace/apps/aad.netsuite) . Quando um cliente é autenticado em um aplicativo por meio do AD do Azure usando SAML, o Azure AD envia um token para o aplicativo (por meio de um HTTP POST). Em seguida, o aplicativo valida e usa o token para entrar no cliente, em vez de solicitar um nome de usuário e uma senha. Esses tokens SAML são assinados com o certificado exclusivo gerado no Azure AD e por algoritmos padrão específicos.

O Azure AD usa algumas das configurações padrão para os aplicativos da galeria. Os valores padrão são configurados com base nos requisitos do aplicativo.

No Azure AD, você pode configurar opções de assinatura de certificado e o algoritmo de assinatura de certificado.

## <a name="certificate-signing-options"></a>Opções de assinatura de certificado

O Azure AD dá suporte a três opções de assinatura de certificado:

* **Assinar ASSERÇÃO SAML**. Essa opção padrão é definida para a maioria dos aplicativos da galeria. Se você selecionar essa opção, o AD do Azure como um IdP (provedor de identidade) assinará a Asserção SAML e o certificado com o certificado [X. 509](https://wikipedia.org/wiki/X.509) do aplicativo.

* **Assinar resposta SAML**. Se você selecionar essa opção, o Azure AD como um IdP assinará a resposta SAML com o certificado X. 509 do aplicativo.

* **Assinar resposta SAML e asserção**. Se você selecionar essa opção, o Azure AD como um IdP assinará o token SAML inteiro com o certificado X. 509 do aplicativo.

## <a name="certificate-signing-algorithms"></a>Algoritmos de assinatura de certificado

O Azure AD dá suporte a dois algoritmos de assinatura, ou SHAs (algoritmos de hash seguro), para assinar a resposta SAML:

* **SHA-256**. O Azure AD usa esse algoritmo padrão para assinar a resposta SAML. É o algoritmo mais recente e é mais seguro do que o SHA-1. A maioria dos aplicativos dá suporte ao algoritmo SHA-256. Se um aplicativo der suporte apenas ao SHA-1 como o algoritmo de assinatura, você poderá alterá-lo. Caso contrário, recomendamos que você use o algoritmo SHA-256 para assinar a resposta SAML.

* **SHA-1**. Esse algoritmo é mais antigo e é tratado como menos seguro do que o SHA-256. Se um aplicativo oferecer suporte apenas a esse algoritmo de assinatura, você poderá selecionar essa opção na lista suspensa **algoritmo de assinatura** . Em seguida, o Azure AD assina a resposta SAML com o algoritmo SHA-1.

## <a name="change-certificate-signing-options-and-signing-algorithm"></a>Alterar as opções de assinatura de certificado e o algoritmo de assinatura

Para alterar as opções de assinatura de certificado SAML de um aplicativo e o algoritmo de assinatura de certificado, selecione o aplicativo em questão:

1. No [portal de Azure Active Directory](https://aad.portal.azure.com/), entre em sua conta. A página **Azure Active Directory centro de administração** é exibida.
1. No painel esquerdo, selecione **Aplicações empresariais**. É exibida uma lista dos aplicativos empresariais em sua conta.
1. Selecione um aplicativo. Uma página de visão geral do aplicativo é exibida.

   ![Exemplo: Página Visão geral do aplicativo](./media/certificate-signing-options/application-overview-page.png)

Em seguida, altere as opções de assinatura de certificado no token SAML para esse aplicativo:

1. No painel esquerdo da página Visão geral do aplicativo, selecione **logon único**.
1. Se a página **Configurar logon único com SAML-Preview** for exibida, vá para a etapa 5.
1. Se a página **selecionar um método de logon único** não for exibida, selecione **Alterar modos de logon único** para exibir essa página.
1. Na página **selecionar um método de logon único** , selecione **SAML** , se disponível. (Se o **SAML** não estiver disponível, o aplicativo não oferece suporte a SAML e você poderá ignorar o restante deste procedimento e artigo.)
1. Na página **Configurar logon único com SAML-Preview** , localize o cabeçalho do certificado de **autenticação SAML** e selecione o ícone de **edição** (um lápis). A página **certificado de autenticação SAML** é exibida.

   ![Exemplo: Página certificado de autenticação SAML](./media/certificate-signing-options/saml-signing-page.png)

1. Na lista **suspensa opção de assinatura** , escolha **assinar resposta SAML**, **assinar Asserção SAML**ou **assinar resposta e Asserção SAML**. As descrições dessas opções aparecem anteriormente neste artigo nas opções de [assinatura de certificado](#certificate-signing-options).
1. Na lista suspensa **algoritmo de assinatura** , escolha **SHA-1** ou **SHA-256**. As descrições dessas opções aparecem anteriormente neste artigo na seção algoritmos de [assinatura de certificado](#certificate-signing-algorithms) .
1. Se estiver satisfeito com suas escolhas, selecione **salvar** para aplicar as novas configurações de certificado de autenticação SAML. Caso contrário, selecione o **X** para descartar as alterações.

## <a name="next-steps"></a>Passos Seguintes

* [Configurar o logon único para aplicativos que não estão na Galeria de aplicativos Azure Active Directory](configure-federated-single-sign-on-non-gallery-applications.md)
* [Solucionar problemas de logon único baseado em SAML](../develop/howto-v1-debug-saml-sso-issues.md)
