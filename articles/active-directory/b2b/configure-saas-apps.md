---
title: Configure aplicativos SaaS para colaboração B2B - Azure AD
description: Exemplos do PowerShell e de código para a colaboração do Azure Active Directory B2B
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 05/23/2017
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7c73a14c2a8cd063672bd0998368ca660f52cd5a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74272943"
---
# <a name="configure-saas-apps-for-b2b-collaboration"></a>Configurar aplicações SaaS para colaboração B2B

A colaboração azure Ative Directory (Azure AD) B2B trabalha com a maioria das aplicações que se integram com a Azure AD. Nesta secção, passamos por instruções para configurar algumas aplicações populares do SaaS para utilização com o Azure AD B2B.

Antes de olhar para instruções específicas da aplicação, aqui estão algumas regras do polegar:

* Para a maioria das aplicações, a configuração do utilizador tem de acontecer manualmente. Ou seja, os utilizadores também devem ser criados manualmente na aplicação.

* Para aplicações que suportam configuração automática, como o Dropbox, são criados convites separados a partir das apps. Os utilizadores devem ter a certeza de aceitar cada convite.

* Nos atributos do utilizador, para mitigar quaisquer problemas com o disco de perfil do utilizador mutilado (UPD) nos utilizadores convidados, configurar sempre o **Utilizador Identificador** para **user.mail**.


## <a name="dropbox-business"></a>Negócio de dropbox

Para permitir que os utilizadores assinem utilizando a sua conta de organização, tem de configurar manualmente o Dropbox Business para utilizar o Azure AD como fornecedor de identidade de Linguagem de Marcação de Afirmação de Segurança (SAML). Se o Dropbox Business não tiver sido configurado para o fazer, não pode solicitar ou permitir que os utilizadores assinem utilizando o Azure AD.

1. Para adicionar a aplicação Dropbox Business em Azure AD, selecione **aplicações Enterprise** no painel esquerdo e, em seguida, clique em **Adicionar**.

   ![O botão "Adicionar" na página de aplicações da Enterprise](media/configure-saas-apps/add-dropbox.png)

2. Na janela Adicionar uma janela de **aplicação,** introduza o **dropbox** na caixa de pesquisa e, em seguida, selecione **Dropbox para Negócios** na lista de resultados.

   ![Procure "dropbox" na página Adicionar uma página de aplicação](media/configure-saas-apps/add-app-dialog.png)

3. Na página **de início de sessão single,** selecione **um único sinal** no painel esquerdo e, em seguida, introduza **user.mail** na caixa **de identificação** do utilizador. (É definido como UPN por padrão.)

   ![Configurar um único sinal para a aplicação](media/configure-saas-apps/configure-app-sso.png)

4. Para descarregar o certificado para utilizar para a configuração dropbox, **selecione Configure DropBox**, e, em seguida, selecione o URL de **Serviço de Sinal Único SAML** na lista.

   ![Descarregar o certificado para configuração Dropbox](media/configure-saas-apps/download-certificate.png)

5. Inscreva-se no Dropbox com o URL de entrada na página **single.**

   ![Screenshot mostrando a página de entrada de dropbox](media/configure-saas-apps/sign-in-to-dropbox.png)

6. No menu, selecione **Admin Console**.

   ![O link "Admin Console" no menu Dropbox](media/configure-saas-apps/dropbox-menu.png)

7. Na caixa de diálogo de **autenticação,** selecione **Mais**, faça upload do certificado e, em seguida, na caixa **URL de Sinal,** introduza o URL de inscrição individual SAML.

   ![O link "Mais" na caixa de diálogo de autenticação colapsada](media/configure-saas-apps/dropbox-auth-01.png)

   ![O "Sign in URL" na caixa de diálogo de autenticação expandida](media/configure-saas-apps/paste-single-sign-on-URL.png)

8. Para configurar a configuração automática do utilizador no portal Azure, **selecione o provisionamento** no painel esquerdo, selecione **Automático** na caixa modo de **provisionamento** e, em seguida, selecione **Autorizar**.

   ![Configurar o fornecimento automático de utilizadores no portal Azure](media/configure-saas-apps/set-up-automatic-provisioning.png)

Depois de os utilizadores convidados ou membros terem sido configurados na aplicação Dropbox, recebem um convite separado do Dropbox. Para utilizar o dropbox single sign-on, os convites devem aceitar o convite clicando num link no mesmo.

## <a name="box"></a>Box
Pode permitir que os utilizadores autentiquem os utilizadores da Caixa com a sua conta Azure AD utilizando a federação baseada no protocolo SAML. Neste procedimento, faz o upload de metadados para Box.com.

1. Adicione a aplicação Box das aplicações da empresa.

2. Configure um único sinal na seguinte ordem:

   ![Screenshot mostrando as definições de configuração de sinal único](media/configure-saas-apps/configure-box-sso.png)

   a. Na caixa **de URL Sign on,** certifique-se de que o URL de entrada de sinal está definido adequadamente para caixa no portal Azure. Este URL é o URL do seu Box.com inquilino. Deve seguir a *https://.box.com*convenção de nomeação.  
   O **Identificador** não se aplica a esta aplicação, mas continua a aparecer como um campo obrigatório.

   b. Na caixa **de identificação utilizador,** introduza **user.mail** (para SSO para as contas dos hóspedes).

   c. Em **certificado de assinatura SAML,** clique em Criar novo **certificado**.

   d. Para começar a configurar o seu Box.com inquilino para usar o Azure AD como fornecedor de identidade, descarregue o ficheiro de metadados e, em seguida, guarde-o para a sua unidade local.

   e. Endere o ficheiro de metadados para a equipa de suporte box, que configura um único sinal para si.

3. Para a configuração automática do utilizador Azure AD, no painel esquerdo, selecione **Provisioning**, e, em seguida, **selecione Autorizar**.

   ![Autorizar a AD Azure a ligar-se à Box](media/configure-saas-apps/auth-azure-ad-to-connect-to-box.png)

Tal como o Dropbox convida, os convites da Box devem resgatar o convite da aplicação Box.

## <a name="next-steps"></a>Passos seguintes

Consulte os seguintes artigos sobre a colaboração Azure AD B2B:

- [O que é a colaboração B2B do Azure AD?](what-is-b2b.md)
- [Grupos dinâmicos e colaboração B2B](use-dynamic-groups.md)
- [B2B colaboração utilizador reclama mapeamento](claims-mapping.md)
- [Partilha externa do Office 365](o365-external-user.md)

