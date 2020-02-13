---
title: Opções avançadas de assinatura de certificado saml para aplicações da AD Azure
description: Saiba como utilizar opções avançadas de assinatura de certificados no token SAML para aplicações pré-integradas no Diretório Ativo do Azure
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
ms.openlocfilehash: dc911ff06208b1fd0af7651c8274a45c958bf0cd
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77159204"
---
# <a name="advanced-certificate-signing-options-in-the-saml-token-for-gallery-apps-in-azure-active-directory"></a>Opções avançadas de assinatura de certificados no token SAML para apps de galeria em Diretório Ativo Azure

Hoje, o Azure Ative Directory (Azure AD) apoia milhares de aplicações pré-integradas na Galeria de Aplicações de Diretório Ativo Azure. Mais de 500 aplicações suportam um único sign-on utilizando o protocolo De Marcação de [Afirmação](https://wikipedia.org/wiki/Security_Assertion_Markup_Language) de Segurança (SAML) 2.0, como a aplicação [NetSuite.](https://azuremarketplace.microsoft.com/marketplace/apps/aad.netsuite) Quando um cliente autentica uma aplicação através da AD Azure utilizando a SAML, a Azure AD envia um sinal para a aplicação (através de um POST HTTP). A aplicação valida e utiliza o símbolo para assinar no cliente em vez de pedir um nome de utilizador e senha. Estes tokens SAML são assinados com o certificado único que é gerado em Azure AD e por algoritmos padrão específicos.

A Azure AD utiliza algumas das definições padrão para as aplicações da galeria. Os valores predefinidos são criados com base nos requisitos da aplicação.

Em Azure AD, pode configurar opções de assinatura de certificadoe o algoritmo de assinatura de certificado.

## <a name="certificate-signing-options"></a>Opções de assinatura de certificado

A Azure AD suporta três opções de assinatura de certificados:

* **Assine a afirmação do SAML.** Esta opção por defeito está definida para a maioria das aplicações da galeria. Se selecionar esta opção, a Azure AD como Fornecedor de Identidade (IdP) assina a afirmação e o certificado SAML com o certificado [X.509](https://wikipedia.org/wiki/X.509) da aplicação.

* **Assinar resposta SAML**. Se selecionar esta opção, o Azure AD como um IDP assina a resposta SAML com o certificado X.509 da aplicação.

* **Assine a resposta e a afirmação da SAML.** Se selecionar esta opção, o Azure AD como um IDP assina todo o token SAML com o certificado X.509 da aplicação.

## <a name="certificate-signing-algorithms"></a>Algoritmos de assinatura de certificados

A Azure AD suporta dois algoritmos de assinatura, ou algoritmos de hash seguros (SHAs), para assinar a resposta SAML:

* **SHA-256.** A Azure AD usa este algoritmo padrão para assinar a resposta SAML. É o algoritmo mais novo e é mais seguro que sha-1. A maioria das aplicações suporta maquete supor o algoritmo SHA-256. Se uma aplicação suporta apenas SHA-1 como algoritmo de assinatura, pode alterá-la. Caso contrário, recomendamos que utilize o algoritmo SHA-256 para assinar a resposta SAML.

* **SHA-1.** Este algoritmo é mais antigo, e é tratado como menos seguro que sha-256. Se uma aplicação suportar apenas este algoritmo de assinatura, pode selecionar esta opção na lista de drop-down do **Algoritmo de Assinatura.** A AD Azure assina então a resposta SAML com o algoritmo SHA-1.

## <a name="change-certificate-signing-options-and-signing-algorithm"></a>Alterar opções de assinatura de certificado e algoritmo de assinatura

Para alterar as opções de assinatura de certificado SAML de uma aplicação e o algoritmo de assinatura de certificado, selecione a aplicação em questão:

1. No [portal azure Ative Diretório,](https://aad.portal.azure.com/)inscreva-se na sua conta. Aparece a página do centro de **administração do Azure Ative Directory.**
1. No painel esquerdo, selecione **Aplicações empresariais**. Aparece uma lista dos pedidos da empresa na sua conta.
1. Selecione uma aplicação. Aparece uma página geral da aplicação.

   ![Exemplo: Página geral da aplicação](./media/certificate-signing-options/application-overview-page.png)

Em seguida, altere as opções de assinatura de certificado no token SAML para esse pedido:

1. No painel esquerdo da página geral da aplicação, selecione **Single sign-on**.
1. Se aparecer a configuração do único sinal com a página **SAML - Pré-visualização,** vá para o passo 5.
1. Se a página de método **de gravação select** não aparecer, selecione os modos de **inscrição individuais** para visualizar essa página.
1. Na página Select uma única página de método de **sinalização,** selecione **SAML** se disponível. (Se a **SAML** não estiver disponível, o pedido não suporta a SAML, podendo ignorar o resto deste procedimento e artigo.)
1. Na configuração de um único sign-on com a página **SAML - Pré-visualização,** encontre a rubrica do Certificado de **Assinatura SAML** e selecione o ícone **Editar** (um lápis). Aparece a página do Certificado de **Assinatura SAML.**

   ![Exemplo: Página de certificado de assinatura SAML](./media/certificate-signing-options/saml-signing-page.png)

1. Na lista de recusa da **Opção de Assinatura,** escolha **a resposta Sign SAML,** **a afirmação do Sign SAML,** ou **a resposta e afirmação do Sign SAML**. As descrições destas opções aparecem anteriormente neste artigo nas opções de assinatura do [Certificado.](#certificate-signing-options)
1. Na lista de lançamento do **Algoritmo de Assinatura,** escolha **SHA-1** ou **SHA-256**. As descrições destas opções aparecem mais cedo neste artigo na secção de algoritmos de assinatura de [Certificado.](#certificate-signing-algorithms)
1. Se estiver satisfeito com as suas escolhas, selecione **Guardar** para aplicar as novas definições de certificado de assinatura SAML. Caso contrário, selecione o **X** para descartar as alterações.

## <a name="next-steps"></a>Passos seguintes

* [Configure um único sinal para aplicações que não estejam na Galeria de Aplicações de Diretório Ativo Azure](configure-federated-single-sign-on-non-gallery-applications.md)
* [Srams de problemas saml único sign-on](../azuread-dev/howto-v1-debug-saml-sso-issues.md)
