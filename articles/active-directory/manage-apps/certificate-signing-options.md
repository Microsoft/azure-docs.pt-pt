---
title: Opções avançadas de assinatura de certificado SAML para aplicações AD Azure
description: Saiba como utilizar opções avançadas de assinatura de certificados no token SAML para aplicações pré-integradas no Azure Ative Directory
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: kenwith
ms.reviewer: jeedes
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9dd6a7a34ac231f8af9d08aab99e3d552f33ea1d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99259626"
---
# <a name="advanced-certificate-signing-options-in-the-saml-token-for-gallery-apps-in-azure-active-directory"></a>Opções avançadas de assinatura de certificado no token SAML para apps de galeria em Azure Ative Directory

Hoje, o Azure Ative Directory (Azure AD) suporta milhares de aplicações pré-integradas na Galeria de Aplicações do Diretório Ativo Azure. Mais de 500 aplicações suportam um único sign-on utilizando o protocolo [Desatalamento de Afirmação](https://wikipedia.org/wiki/Security_Assertion_Markup_Language) de Segurança (SAML) 2.0, como a aplicação [NetSuite.](https://azuremarketplace.microsoft.com/marketplace/apps/aad.netsuite) Quando um cliente autentica uma aplicação através da Azure AD através da UTILIZAÇÃO SAML, a Azure AD envia um símbolo para a aplicação (através de um HTTP POST). A aplicação valida e utiliza o token para assinar no cliente em vez de solicitar um nome de utilizador e senha. Estes tokens SAML são assinados com o certificado único que é gerado em Azure AD e por algoritmos padrão específicos.

A Azure AD utiliza algumas das definições predefinidas para as aplicações da galeria. Os valores predefinidos são configurado com base nos requisitos da aplicação.

No Azure AD, pode configurar opções de assinatura de certificado e o algoritmo de assinatura de certificado.

## <a name="certificate-signing-options"></a>Opções de assinatura de certificado

A Azure AD suporta três opções de assinatura de certificado:

* **Assine a afirmação da SAML**. Esta opção por defeito está definida para a maioria das aplicações da galeria. Se selecionar esta opção, a Azure AD como Fornecedor de Identidade (IdP) assina a afirmação e certificado SAML com o certificado [X.509](https://wikipedia.org/wiki/X.509) do pedido.

* **Assine a resposta DA SAML**. Se selecionar esta opção, a Azure AD como IdP assina a resposta SAML com o certificado X.509 do pedido.

* **Assine a resposta e a afirmação da SAML**. Se selecionar esta opção, a Azure AD como IdP assina todo o token SAML com o certificado X.509 do pedido.

## <a name="certificate-signing-algorithms"></a>Algoritmos de assinatura de certificado

A Azure AD suporta dois algoritmos de assinatura, ou algoritmos de haxixe seguros (SHAs), para assinar a resposta SAML:

* **SHA-256.** A Azure AD usa este algoritmo padrão para assinar a resposta SAML. É o algoritmo mais recente e é mais seguro que SHA-1. A maioria das aplicações suporta o algoritmo SHA-256. Se uma aplicação suporta apenas SHA-1 como algoritmo de assinatura, pode alterá-lo. Caso contrário, recomendamos que utilize o algoritmo SHA-256 para assinar a resposta SAML.

* **SHA-1**. Este algoritmo é mais antigo, e é tratado como menos seguro que SHA-256. Se uma aplicação suportar apenas este algoritmo de assinatura, pode selecionar esta opção na lista de drop-down do Algoritmo de **Assinatura.** Azure AD assina então a resposta SAML com o algoritmo SHA-1.

## <a name="change-certificate-signing-options-and-signing-algorithm"></a>Alterar opções de assinatura de certificado e algoritmo de assinatura

Para alterar as opções de assinatura de certificado SAML de uma aplicação e o algoritmo de assinatura de certificado, selecione a aplicação em questão:

1. No [portal Azure Ative Directory,](https://aad.portal.azure.com/)inscreva-se na sua conta. Aparece a página **do centro de administração Azure Ative Directory.**
1. No painel esquerdo, selecione **Aplicações empresariais**. Aparece uma lista das aplicações empresariais na sua conta.
1. Selecione uma aplicação. Aparece uma página geral para a aplicação.

   ![Exemplo: Página geral da aplicação](./media/certificate-signing-options/application-overview-page.png)

Em seguida, altere as opções de assinatura do certificado no token SAML para esse pedido:

1. No painel esquerdo da página geral da aplicação, selecione **Single sign-on**.
1. Se aparecer a Sign-On Única configurada com a página **DE pré-visualização SAML,** vá para o passo 5.
1. Se a página **de método de sômposição** não aparecer, selecione **Alterar os modos de inscrição única** para exibir essa página.
1. Na página **de método de inscrição** única, selecione **SAML** se disponível. (Se **a SAML** não estiver disponível, a aplicação não suporta a SAML, e pode ignorar o resto deste procedimento e artigo.)
1. Na **configuração single Sign-On com página DE pré-visualização SAML,** encontre o título **do Certificado de Assinatura SAML** e selecione o ícone **editar** (um lápis). Aparece a página **do Certificado de Assinatura SAML.**

   ![Exemplo: página de certificado de assinatura SAML](./media/certificate-signing-options/saml-signing-page.png)

1. Na lista de drop-down **da Opção** de Assinatura, escolha **a resposta Do Sinal SAML,** **Assine a afirmação SAML,** ou **assine a resposta e afirmação do SAML**. As descrições destas opções aparecem anteriormente neste artigo nas opções de assinatura do [Certificado.](#certificate-signing-options)
1. Na lista de drop-down do Algoritmo de **Assinatura,** escolha **SHA-1** ou **SHA-256**. Descrições destas opções aparecem mais cedo neste artigo na secção [de algoritmos de assinatura de certificado.](#certificate-signing-algorithms)
1. Se estiver satisfeito com as suas escolhas, **selecione Guardar** para aplicar as novas definições de certificado de assinatura SAML. Caso contrário, selecione o **X** para descartar as alterações.

## <a name="next-steps"></a>Passos seguintes

* [Configure um único sign-on para aplicações que não estão na Galeria de Aplicações do Diretório Ativo Azure](./configure-saml-single-sign-on.md)
* [Resolver problemas de início de sessão único baseado em SAML](./debug-saml-sso-issues.md)