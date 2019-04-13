---
title: Opções no token SAML para aplicações previamente integradas no Azure Active Directory de assinatura de certificado avançada | Documentos da Microsoft
description: Saiba como utilizar as opções no token SAML para aplicações previamente integradas no Azure Active Directory de assinatura de certificado avançado
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: celested
ms.reviewer: jeedes
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: bede53ef2bc05750be21f831fc0cb790a001c6c5
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2019
ms.locfileid: "59549342"
---
# <a name="advanced-certificate-signing-options-in-the-saml-token-for-gallery-apps-in-azure-active-directory"></a>Advanced opções no token SAML para aplicações de galeria no Azure Active Directory de assinatura de certificado

Atualmente o Azure Active Directory (Azure AD) suporta milhares de aplicações previamente integradas na Galeria de aplicações do Azure Active Directory. Mais de 500 das aplicações suportam início de sessão único com o [Security Assertion Markup Language](https://wikipedia.org/wiki/Security_Assertion_Markup_Language) (SAML) 2.0 protocolo, como o [NetSuite](https://azuremarketplace.microsoft.com/marketplace/apps/aad.netsuite) aplicação. Quando um cliente efetua a autenticação para uma aplicação através do Azure AD com o SAML, o Azure AD envia um token para a aplicação (através de um HTTP POST). O aplicativo, em seguida, valida e utiliza o token para iniciar sessão no cliente em vez de solicitar um nome de utilizador e palavra-passe. Estes tokens SAML são assinadas com o certificado exclusivo que é gerado no Azure AD e pelos algoritmos padrão específicos.

Azure AD utiliza algumas das configurações padrão para as aplicações de galeria. Os valores predefinidos são configurados com base nos requisitos da aplicação.

No Azure AD, pode configurar opções de assinatura de certificado e o algoritmo de assinatura de certificado.

## <a name="certificate-signing-options"></a>Opções de assinatura de certificado

O Azure AD suporta três opções de assinatura de certificado:

* **Assinar asserção SAML**. Esta opção de predefinição está definida para a maioria das aplicações de galeria. Se selecionar esta opção, o Azure AD como fornecedor de identidade (IdP) inicia a asserção de SAML e o certificado com o [X.509](https://wikipedia.org/wiki/X.509) certificado da aplicação.

* **Assinar resposta SAML**. Se selecionar esta opção, o Azure AD como um IdP inicia sessão com a resposta SAML com o certificado X.509 da aplicação.

* **Assinar asserção e resposta SAML**. Se selecionar esta opção, o Azure AD como um IdP assina o token SAML todo com o certificado X.509 da aplicação.

## <a name="certificate-signing-algorithms"></a>Algoritmos de assinatura de certificado

O Azure AD suporta dois algoritmos de assinatura ou algoritmos de hash seguro (SHAs), para assinar resposta SAML:

* **SHA-256**. Azure AD utiliza esse algoritmo padrão para assinar resposta SAML. Ele é o algoritmo mais recente e é mais seguro que SHA-1. A maioria dos aplicativos de suportar o algoritmo SHA-256. Se uma aplicação suporta apenas SHA-1, como o algoritmo de assinatura, pode alterá-lo. Caso contrário, recomendamos que utilize o algoritmo SHA-256 para assinar resposta SAML.

* **SHA-1**. Esse algoritmo é mais antigo, e ela é tratada como menos seguro do que o SHA-256. Se uma aplicação suportar apenas este algoritmo de assinatura, pode selecionar esta opção no **algoritmo de assinatura** na lista pendente. O Azure AD, em seguida, inicia a resposta SAML com o algoritmo SHA-1.

## <a name="change-certificate-signing-options-and-signing-algorithm"></a>Alterar opções de assinatura e algoritmo de assinatura de certificado

Para alterar as opções de assinatura de certificado SAML de um aplicativo e o algoritmo de assinatura de certificado, selecione a aplicação em questão:

1. Na [portal do Azure Active Directory](https://aad.portal.azure.com/), inicie sessão na sua conta. O **Centro de administração do Azure Active Directory** é apresentada a página.
1. No painel esquerdo, selecione **Aplicações empresariais**. É apresentada uma lista de aplicações empresariais na sua conta.
1. Selecione uma aplicação. É apresentada uma página de descrição geral para a aplicação.

   ![Página de descrição geral da aplicação](./media/certificate-signing-options/application-overview-page.png)

Em seguida, altere o certificado de assinatura de opções no token SAML para essa aplicação:

1. No painel esquerdo da página de descrição geral do aplicativo, selecione **início de sessão único**.

2. Se o **definir a segurança de início de sessão único com o SAML - pré-visualização** é apresentada a página, avance para o passo 5.

3. Se o **selecionar um método de início de sessão único** página não for apresentado, selecione **alterar modos de início de sessão únicos** para apresentar uma página.

4. Na **selecionar um método de início de sessão único** , selecione **SAML** se estiver disponível. (Se **SAML** não estiver disponível, a aplicação não suporta SAML, e pode ignorar o resto deste procedimento e artigo.)

5. Na **definir a segurança de início de sessão único com o SAML - pré-visualização** página, encontre o **certificado de assinatura SAML** cabeçalho e selecione o **editar** ícone (um lápis). O **certificado de assinatura SAML** é apresentada a página.

   ![Página de assinatura de SAML](./media/certificate-signing-options/saml-signing-page.png)

6. Na **opção assinatura** pendente lista, escolha **resposta SAML de início de sessão**, **asserção de SAML de início de sessão**, ou **asserção e resposta SAML de início de sessão**. Descrições dessas opções são apresentados no início deste artigo no [opções de assinatura de certificado](#certificate-signing-options).

7. Na **algoritmo de assinatura** pendente lista, escolha **SHA-1** ou **SHA-256**. Descrições dessas opções são apresentados no início deste artigo no [algoritmos de assinatura de certificado](#certificate-signing-algorithms) secção.

8. Se estiver satisfeito com as suas opções, selecione **guardar** para aplicar o definições de certificado de assinatura de SAML novo. Caso contrário, selecione o **X** para eliminar as alterações.

## <a name="next-steps"></a>Passos Seguintes

* [Configurar o início de sessão único para aplicações que não estão na Galeria de aplicações do Azure Active Directory](configure-federated-single-sign-on-non-gallery-applications.md)
* [Resolver problemas com base em SAML início de sessão único](../develop/howto-v1-debug-saml-sso-issues.md)
