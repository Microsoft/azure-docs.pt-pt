---
title: SAML início de sessão único para aplicações no local com o Proxy de aplicações de diretório Active Directory do Azure (pré-visualização) | Documentos da Microsoft
description: Saiba como fornecer início de sessão único para on-premises aplicações publicadas através do Proxy de aplicações que estão protegidos por autenticação SAML.
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
ms.date: 03/12/2019
ms.author: celested
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: ef5679ba2a6a62955f5402e8bfaa4f1884df722d
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57840601"
---
# <a name="saml-single-sign-on-for-on-premises-applications-with-application-proxy-preview"></a>SAML início de sessão único para aplicações no local com o Proxy de aplicações (pré-visualização)

Pode fornecer início de sessão único (SSO) para aplicações no local que estão protegidas com autenticação SAML e fornecem acesso remoto a estas aplicações através do Proxy de aplicações. Com o SAML início de sessão único, o Azure Active Directory (Azure AD) autentica para o aplicativo utilizando a conta de utilizador do Azure AD. O Azure AD comunica as informações de início de sessão para a aplicação através de um protocolo de ligação. Também pode mapear os utilizadores a funções de aplicação específica, com base nas regras que definir nas afirmações SAML. Ao ativar o Proxy de aplicações, além de SAML SSO, os utilizadores terão acesso externo para o aplicativo e uma experiência SSO totalmente integrada.

Os aplicativos tem de ser capazes de consumir tokens SAML emitidos pelo **do Azure Active Directory**. Esta configuração não se aplica a aplicações através de um fornecedor de identidade no local. Para esses cenários é recomendável rever [recursos para a migração de aplicativos para o Azure AD](migration-resources.md).

SAML SSO com o Proxy de aplicações também funciona com a funcionalidade de encriptação de tokens SAML. Para mais informações, veja [encriptação de tokens de configurar o Azure AD SAML](howto-saml-token-encryption.md).

## <a name="publish-the-on-premises-application-with-application-proxy"></a>Publicar a aplicação no local com o Proxy de aplicações

Antes de poder fornecer SSO para aplicações no local, certifique-se de que ativou o Proxy de aplicações e tem um conector instalado. Ver [adicionar uma aplicação no local para acesso remoto através do Proxy de aplicações no Azure AD](application-proxy-add-on-premises-application.md) para saber como.

Tenha em atenção o seguinte quando vai o tutorial:

* Publicar a aplicação, de acordo com as instruções no tutorial. Verifique se seleciona **do Azure Active Directory** como o **pré-autenticação** método para a sua aplicação (etapa 4 no [adicionar uma aplicação no local ao Azure AD](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad
)).
* Copiar o **URL externo** para a aplicação.
* Como melhor prática, utilize domínios personalizados, sempre que possível para uma experiência de utilizador otimizada. Saiba mais sobre [trabalhar com domínios personalizados no Proxy de aplicações do Azure AD](application-proxy-configure-custom-domain.md).
* Adicione pelo menos um utilizador para a aplicação e certificar-se de que a conta de teste tem acesso à aplicação no local.

## <a name="set-up-saml-sso"></a>Configurar o SAML SSO

1. No portal do Azure, selecione **do Azure Active Directory > aplicações empresariais** e selecionar a aplicação a partir da lista.
1. A partir da aplicação **descrição geral** página, selecione **início de sessão único**.
1. Selecione **SAML** como o método de início de sessão único.
1. No **definido no início de sessão único com o SAML** página, edite a **configuração básica de SAML** dados e siga os passos [Enter básicas de configuração de SAML](configure-single-sign-on-non-gallery-applications.md#saml-based-single-sign-on) configurar baseado em SAML autenticação da aplicação.

   * Certifique-se de que o **URL de resposta** raiz corresponda ao ou é um caminho sob a **URL externo** para a aplicação no local que adicionou para acesso remoto através do Proxy de aplicações no Azure AD.

     ![Introduzir dados de configuração básicos do SAML](./media/application-proxy-configure-single-sign-on-on-premises-apps/basic-saml-configuration.png)

     > [!NOTE]
     > Se a aplicação de back-end espera que o **URL de resposta** para ser o URL interno, terá de instalar a extensão de-de início de sessão segura das minhas aplicações nos dispositivos dos utilizadores. Esta extensão será automaticamente redirecionada para o serviço de Proxy de aplicação apropriado. Para instalar a extensão, consulte [extensão de início de sessão de proteger as minhas aplicações](../user-help/active-directory-saas-access-panel-introduction.md#my-apps-secure-sign-in-extension).

## <a name="test-your-app"></a>Testar a aplicação

Quando tiver concluído todas essas etapas, a aplicação deve estar em execução. Para testar a aplicação:

1. Abra um browser e navegue para o URL externo que criou quando publicado a aplicação. 
1. Inicie sessão com a conta de teste que atribuiu à aplicação.

## <a name="next-steps"></a>Passos Seguintes

- [Como o Proxy de aplicações do Azure AD fornece início de sessão único?](application-proxy-single-sign-on.md)
- [Resolver problemas de Proxy de aplicações](application-proxy-troubleshoot.md)
