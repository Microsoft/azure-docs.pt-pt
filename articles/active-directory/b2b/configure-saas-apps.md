---
title: Configurar aplicativos SaaS para colaboração B2B – Azure AD
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
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74272943"
---
# <a name="configure-saas-apps-for-b2b-collaboration"></a>Configurar aplicações SaaS para colaboração B2B

A colaboração B2B do Azure Active Directory (Azure AD) funciona com a maioria dos aplicativos que se integram ao Azure AD. Nesta seção, percorreremos instruções para configurar alguns aplicativos SaaS populares para uso com o Azure AD B2B.

Antes de examinar as instruções específicas do aplicativo, aqui estão algumas regras gerais:

* Para a maioria dos aplicativos, a instalação do usuário precisa ocorrer manualmente. Ou seja, os usuários também devem ser criados manualmente no aplicativo.

* Para aplicativos que dão suporte à configuração automática, como o Dropbox, são criados convites separados a partir dos aplicativos. Os usuários devem ter certeza de aceitar cada convite.

* Nos atributos de usuário, para atenuar quaisquer problemas com o UPD (disco de perfil de usuário) desconfigurado em usuários convidados, sempre defina o **identificador de usuário** como **User. mail**.


## <a name="dropbox-business"></a>Negócios do Dropbox

Para permitir que os usuários entrem usando sua conta da organização, você deve configurar manualmente o Dropbox Business para usar o Azure AD como um provedor de identidade Security Assertion Markup Language (SAML). Se o Dropbox Business não tiver sido configurado para fazer isso, ele não poderá solicitar ou, de outra forma, permitir que os usuários entrem usando o Azure AD.

1. Para adicionar o aplicativo de negócios do Dropbox ao Azure AD, selecione **aplicativos empresariais** no painel esquerdo e clique em **Adicionar**.

   ![O botão "Adicionar" na página aplicativos empresariais](media/configure-saas-apps/add-dropbox.png)

2. Na janela **Adicionar um aplicativo** , digite **Dropbox** na caixa de pesquisa e, em seguida, selecione **Dropbox for Business** na lista de resultados.

   ![Pesquise "Dropbox" na página Adicionar um aplicativo](media/configure-saas-apps/add-app-dialog.png)

3. Na página **logon único** , selecione **logon único** no painel esquerdo e, em seguida, insira **User. mail** na caixa **identificador de usuário** . (Ele é definido como UPN por padrão.)

   ![Configurando o logon único para o aplicativo](media/configure-saas-apps/configure-app-sso.png)

4. Para baixar o certificado a ser usado para a configuração do Dropbox, selecione **Configurar Dropbox**e, em seguida, selecione **URL do serviço de logon único do SAML** na lista.

   ![Baixando o certificado para a configuração do Dropbox](media/configure-saas-apps/download-certificate.png)

5. Entre no Dropbox com a URL de logon da página de **logon único** .

   ![Captura de tela mostrando a página de entrada do Dropbox](media/configure-saas-apps/sign-in-to-dropbox.png)

6. No menu, selecione **console do administrador**.

   ![O link "console do administrador" no menu do Dropbox](media/configure-saas-apps/dropbox-menu.png)

7. Na caixa de diálogo **autenticação** , selecione **mais**, carregue o certificado e, em seguida, na caixa **URL de entrada** , insira a URL de logon único do SAML.

   ![O link "mais" na caixa de diálogo de autenticação recolhida](media/configure-saas-apps/dropbox-auth-01.png)

   ![A "URL de entrada" na caixa de diálogo de autenticação expandida](media/configure-saas-apps/paste-single-sign-on-URL.png)

8. Para configurar a configuração de usuário automática no portal do Azure, selecione **provisionamento** no painel esquerdo, selecione **automático** na caixa modo de **provisionamento** e, em seguida, selecione **autorizar**.

   ![Configurando o provisionamento automático de usuário no portal do Azure](media/configure-saas-apps/set-up-automatic-provisioning.png)

Depois que os usuários convidados ou membros tiverem sido configurados no aplicativo Dropbox, eles receberão um convite separado do dropbox. Para usar o logon único do Dropbox, os convidados devem aceitar o convite clicando em um link nele.

## <a name="box"></a>Box
Você pode permitir que os usuários autentiquem os usuários convidados da caixa com sua conta do Azure AD usando a Federação baseada no protocolo SAML. Neste procedimento, você carrega metadados para Box.com.

1. Adicione o aplicativo de caixa dos aplicativos empresariais.

2. Configure o logon único na seguinte ordem:

   ![Captura de tela mostrando as definições de configuração de logon único](media/configure-saas-apps/configure-box-sso.png)

   a. Na caixa **URL de logon** , verifique se a URL de logon está definida corretamente para o box na portal do Azure. Essa URL é a URL do seu locatário do Box.com. Ele deve seguir a Convenção de nomenclatura *https://.box.com* .  
   O **identificador** não se aplica a este aplicativo, mas ainda aparece como um campo obrigatório.

   b. Na caixa **identificador de usuário** , insira **User. mail** (para SSO para contas de convidado).

   c. Em **certificado de autenticação SAML**, clique em **criar novo certificado**.

   d. Para começar a configurar seu locatário do Box.com para usar o Azure AD como um provedor de identidade, baixe o arquivo de metadados e, em seguida, salve-o em sua unidade local.

   e. Encaminhe o arquivo de metadados para a equipe de suporte do box, que configura o logon único para você.

3. Para configuração automática de usuário do Azure AD, no painel esquerdo, selecione **provisionamento**e, em seguida, selecione **autorizar**.

   ![Autorizar o Azure AD a se conectar ao box](media/configure-saas-apps/auth-azure-ad-to-connect-to-box.png)

Como os convidados do Dropbox, os convidados do box devem resgatar seu convite do aplicativo box.

## <a name="next-steps"></a>Passos seguintes

Consulte os seguintes artigos sobre a colaboração B2B do Azure AD:

- [O que é a colaboração B2B do Azure AD?](what-is-b2b.md)
- [Grupos dinâmicos e colaboração B2B](use-dynamic-groups.md)
- [Mapeamento de declarações do usuário de colaboração B2B](claims-mapping.md)
- [Partilha externa do Office 365](o365-external-user.md)

