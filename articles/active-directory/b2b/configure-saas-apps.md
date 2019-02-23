---
title: Configurar aplicações de SaaS para colaboração B2B - Azure Active Directory | Documentos da Microsoft
description: Exemplos do PowerShell e de código para a colaboração do Azure Active Directory B2B
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 05/23/2017
ms.author: mimart
author: msmimart
manager: daveba
ms.reviewer: sasubram
ms.collection: M365-identity-device-management
ms.openlocfilehash: 98126c6459c77ec4580665b0d157d532a1fe636c
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/22/2019
ms.locfileid: "56672258"
---
# <a name="configure-saas-apps-for-b2b-collaboration"></a>Configurar aplicações de SaaS para colaboração B2B

Colaboração do Azure Active Directory (Azure AD) B2B funciona com a maioria das aplicações que se integram com o Azure AD. Nesta secção, vamos analisar as instruções para configurar algumas aplicações SaaS populares para utilização com o Azure AD B2B.

Antes de examinar instruções específicas da aplicação, aqui estão algumas regras básicas:

* Para a maioria das aplicações, a configuração do utilizador tem de acontecer manualmente. Ou seja, os utilizadores devem ser criados manualmente na aplicação também.

* Para aplicações que suportam a configuração automática, como o Dropbox, convites separados são criados a partir das aplicações. Os utilizadores não deixe de aceitar cada convite.

* Nos atributos de utilizador, para atenuar problemas com o disco do perfil de utilizador mangled (UDP) em utilizadores convidados, sempre definido **identificador de utilizador** ao **user.mail**.


## <a name="dropbox-business"></a>Dropbox Business

Para permitir que os utilizadores iniciem sessão com a respetiva conta de organização, tem de configurar manualmente o negócio do Dropbox para utilizar o Azure AD como fornecedor de identidade de Security Assertion Markup Language (SAML). Se não tiver sido configurado comercial do Dropbox para fazer isso, não é possível solicitar ou caso contrário, permitir que os utilizadores iniciem sessão com o Azure AD.

1. Para adicionar a aplicação de negócio do Dropbox para o Azure AD, selecione **aplicações empresariais** no painel esquerdo e, em seguida, clique **Add**.

  ![O botão "Adicionar" na página de aplicações empresariais](media/configure-saas-apps/add-dropbox.png)

2. Na **adicionar uma aplicação** janela, introduza **dropbox** na caixa de pesquisa e, em seguida, selecione **o Dropbox for Business** na lista de resultados.

  ![Procure "dropbox" no adicionar uma página de aplicativo](media/configure-saas-apps/add-app-dialog.png)

3. Sobre o **início de sessão único** página, selecione **início de sessão único** no painel esquerdo e, em seguida, introduza **user.mail** no **identificador de utilizador** caixa. (Ele é definido como o UPN por predefinição.)

  ![Configurar o início de sessão único da aplicação](media/configure-saas-apps/configure-app-sso.png)

4. Para transferir o certificado a utilizar para a configuração do Dropbox, selecione **configurar DropBox**e, em seguida, selecione **SAML único início de sessão no URL do serviço** na lista.

  ![Transferir o certificado para a configuração do Dropbox](media/configure-saas-apps/download-certificate.png)

5. Inicie sessão no Dropbox com o URL de início de sessão do **início de sessão único** página.

  ![A página de início de sessão do Dropbox](media/configure-saas-apps/sign-in-to-dropbox.png)

6. No menu, selecione **consola de administração**.

  ![A ligação de "Consola de administração" no menu do Dropbox](media/configure-saas-apps/dropbox-menu.png)

7. Na **autenticação** caixa de diálogo, selecione **mais**, carregue o certificado e, em seguida, no **iniciar sessão no URL** , introduza o SAML URL single sign-on.

  ![A ligação "Mais" na caixa de diálogo de autenticação fechada](media/configure-saas-apps/dropbox-auth-01.png)

  ![O "iniciar sessão no URL" na caixa de diálogo de autenticação expandida](media/configure-saas-apps/paste-single-sign-on-URL.png)

8. Para configurar o programa de configuração automática de utilizador no portal do Azure, selecione **aprovisionamento** no painel esquerdo, selecione **automática** no **modo de aprovisionamento** caixa e, em seguida, selecione  **Autorizar**.

  ![Configurar o aprovisionamento automático de utilizadores no portal do Azure](media/configure-saas-apps/set-up-automatic-provisioning.png)

Depois dos utilizadores convidados ou membro foram definidos na aplicação do Dropbox, eles recebem um convite separado do Dropbox. Para utilizar o Dropbox início de sessão único, convidados têm de aceitar o convite ao clicar num link no mesmo.

## <a name="box"></a>Box
Poderá habilitá-los autenticar os utilizadores convidados de caixa com a respetiva conta do Azure AD com Federação baseia-se no protocolo SAML. Neste procedimento, vai carregar metadados para Box.com.

1. Adicione a aplicação de caixa das aplicações empresariais.

2. Configure o início de sessão único na seguinte ordem:

  ![Configurar caixa início de sessão único](media/configure-saas-apps/configure-box-sso.png)

 a. Na **iniciar sessão no URL** caixa, certifique-se de que o URL de início de sessão é definido adequadamente para caixa no portal do Azure. Este URL é o URL do seu inquilino de Box.com. Deve seguir a Convenção de nomenclatura *https://.box.com*.  
 O **identificador** não é aplicável a esta aplicação, mas ainda é apresentado como um campo obrigatório.

 b. Na **identificador de utilizador** , introduza **user.mail** (para SSO para contas de convidado).

 c. Sob **certificado de assinatura SAML**, clique em **criar novo certificado**.

 d. Para começar a configurar o seu inquilino de Box.com para utilizar o Azure AD como fornecedor de identidade, transfira o ficheiro de metadados e, em seguida, guarde-o em seu disco local.

 e. Reencaminhar o ficheiro de metadados para a caixa de equipa de suporte, que configura o início de sessão único para.

3. Para a configuração de utilizadores automático do Azure AD, no painel esquerdo, selecione **aprovisionamento**e, em seguida, selecione **autorizar**.

  ![Autorizar o Azure AD para ligar à caixa](media/configure-saas-apps/auth-azure-ad-to-connect-to-box.png)

Como o Dropbox convidados, convidados caixa têm de resgatar o convite a partir da aplicação de caixa.

## <a name="next-steps"></a>Passos Seguintes

Veja os artigos seguintes na colaboração B2B do Azure AD:

- [O que é a colaboração B2B do Azure AD?](what-is-b2b.md)
- [Grupos dinâmicos e de colaboração B2B](use-dynamic-groups.md)
- [Mapeamento de afirmações de utilizador de colaboração de B2B](claims-mapping.md)
- [Partilha externa do Office 365](o365-external-user.md)

