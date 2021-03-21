---
title: Configurar aplicativos SaaS para colaboração B2B - Azure AD
description: Saiba como configurar aplicações SaaS para a colaboração do Azure Ative Directory B2B e ver recursos disponíveis adicionais.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 05/23/2017
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: e0db5e609a5e6b7d74809810a50f86d72ae85675
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "90705515"
---
# <a name="configure-saas-apps-for-b2b-collaboration"></a>Configurar aplicações SaaS para colaboração B2B

A colaboração B2B do Azure Ative Directory (Azure AD) trabalha com a maioria das aplicações que se integram com a Azure AD. Nesta secção, passamos por instruções para configurar algumas aplicações saaS populares para uso com Azure AD B2B.

Antes de olhar para instruções específicas da aplicação, aqui estão algumas regras do polegar:

* Para a maioria das aplicações, a configuração do utilizador tem de ser manualmente. Ou seja, os utilizadores também devem ser criados manualmente na aplicação.

* Para aplicações que suportam configuração automática, como o Dropbox, são criados convites separados a partir das aplicações. Os utilizadores devem ter a certeza de aceitar cada convite.

* Nos atributos do utilizador, para mitigar quaisquer problemas com o disco de perfil de utilizador mutilado (UPD) nos utilizadores convidados, coloque sempre **o Identificador do Utilizador** no **user.mail**.


## <a name="dropbox-business"></a>Dropbox Business

Para permitir que os utilizadores entrem a trabalhar na sua conta de organização, tem de configurar manualmente o Dropbox Business para utilizar o Azure AD como fornecedor de identidade de marcação de afirmação de segurança (SAML). Se o Dropbox Business não tiver sido configurado para o fazer, não pode solicitar ou permitir que os utilizadores entrem a trabalhar através do Azure AD.

1. Para adicionar a aplicação Dropbox Business no AD Azure, selecione **aplicações Enterprise** no painel esquerdo e, em seguida, clique em **Adicionar**.

   ![O botão "Adicionar" na página de aplicações da Enterprise](media/configure-saas-apps/add-dropbox.png)

2. Na janela **De adicionar uma aplicação,** **introduza** a caixa de espera na caixa de pesquisa e, em seguida, selecione **Dropbox para Negócios** na lista de resultados.

   ![Procure por "dropbox" na página de aplicação Adicionar](media/configure-saas-apps/add-app-dialog.png)

3. Na página **de inscrição única,** selecione **'Único' no** painel esquerdo e, em seguida, introduza **user.mail** na caixa **'Identificador do Utilizador'.** (É configurado como UPN por padrão.)

   ![Configurar um único sinal para a aplicação](media/configure-saas-apps/configure-app-sso.png)

4. Para descarregar o certificado para utilizar para a configuração dropbox, selecione **Configure DropBox** e, em seguida, selecione **SAML Single Sign On Service URL** na lista.

   ![Descarregar o certificado para configuração Dropbox](media/configure-saas-apps/download-certificate.png)

5. Inscreva-se no Dropbox com o URL de inscrição da página **de assinatura única.**

   ![Screenshot mostrando a página de entrada do Dropbox](media/configure-saas-apps/sign-in-to-dropbox.png)

6. No menu, selecione **Admin Console**.

   ![A ligação "Admin Console" no menu Dropbox](media/configure-saas-apps/dropbox-menu.png)

7. Na caixa de diálogo **de autenticação,** selecione **Mais,** carregue o certificado e, em seguida, na caixa **de url sinal,** introduza o URL de inscrição único SAML.

   ![A ligação "Mais" na caixa de diálogo de autenticação em colapso](media/configure-saas-apps/dropbox-auth-01.png)

   ![O "Sign in URL" na caixa de diálogo de autenticação expandida](media/configure-saas-apps/paste-single-sign-on-URL.png)

8. Para configurar a configuração automática do utilizador no portal Azure, selecione **Provisioning** no painel esquerdo, selecione **Automático** na caixa **de Modo de Provisionamento** e, em seguida, selecione **Authorize**.

   ![Configurar o fornecimento automático de utilizadores no portal Azure](media/configure-saas-apps/set-up-automatic-provisioning.png)

Depois de os utilizadores convidados ou membros terem sido criados na aplicação Dropbox, recebem um convite separado do Dropbox. Para utilizar o dropbox single sign-on, os convidados devem aceitar o convite clicando num link nele.

## <a name="box"></a>Box
Pode permitir que os utilizadores autentiquem os utilizadores da Box com a sua conta AD Azure utilizando a federação que se baseia no protocolo SAML. Neste procedimento, faça o upload de metadados para Box.com.

1. Adicione a aplicação Box das aplicações da empresa.

2. Configure um único sinal na seguinte ordem:

   ![Screenshot mostrando as definições de configuração de sinal de inscrição única](media/configure-saas-apps/configure-box-sso.png)

   a. Na **placa de sinalização na** caixa URL, certifique-se de que o URL de inscrição está definido adequadamente para caixa no portal Azure. Esta URL é a URL do seu inquilino Box.com. Deve seguir a convenção de *https://.box.com* nomeação.  
   O **Identificador** não se aplica a esta aplicação, mas ainda assim aparece como um campo obrigatório.

   b. Na caixa **de identificação** do Utilizador, insira **user.mail** (para SSO para contas de hóspedes).

   c. No **Certificado de Assinatura SAML,** clique em Criar novo **certificado.**

   d. Para começar a configurar o seu Box.com inquilino para usar o Azure AD como fornecedor de identidade, descarregue o ficheiro de metadados e guarde-o para a sua unidade local.

   e. Encaminhe o ficheiro de metadados para a equipa de suporte da Caixa, que configura um único sinal para si.

3. Para configuração automática do utilizador Azure AD, no painel esquerdo, selecione **Provisioning** e, em seguida, **selecione Authorize**.

   ![Autorizar a Azure AD a ligar-se à Box](media/configure-saas-apps/auth-azure-ad-to-connect-to-box.png)

Tal como os convites do Dropbox, os convites da Box devem resgatar o seu convite da aplicação Box.

## <a name="next-steps"></a>Passos seguintes

Consulte os seguintes artigos sobre a colaboração Azure AD B2B:

- [O que é a colaboração B2B do Azure AD?](what-is-b2b.md)
- [Grupos dinâmicos e colaboração B2B](use-dynamic-groups.md)
- [Utilizador de colaboração B2B reivindica mapeamento](claims-mapping.md)
- [Partilha externa microsoft 365](o365-external-user.md)

