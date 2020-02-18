---
title: 'Tutorial: integração de SSO (logon único) Azure Active Directory com o G Suite | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o G Suite.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 38a6ca75-7fd0-4cdc-9b9f-fae080c5a016
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 02/14/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d5ef5816759074073c57ef0f616ddea4a159956f
ms.sourcegitcommit: f255f869c1dc451fd71e0cab340af629a1b5fb6b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/16/2020
ms.locfileid: "77370341"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-g-suite"></a>Tutorial: integração de SSO (logon único) do Azure Active Directory com o G Suite

Neste tutorial, você aprenderá a integrar o G Suite ao Azure Active Directory (Azure AD). Ao integrar o G Suite ao Azure AD, você pode:

* Controle no Azure AD quem tem acesso ao G Suite.
* Habilite seus usuários a serem automaticamente conectados ao G Suite com suas contas do Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

- Uma subscrição da AD Azure.
- Assinatura habilitada para SSO (logon único) do G Suite.
- Uma assinatura do Google Apps ou Google Cloud Platform assinatura.

> [!NOTE]
> Para testar os passos neste tutorial, recomendamos que não utilize um ambiente de produção. Este documento foi criado usando a nova experiência de logon único de usuário. Se você ainda estiver usando o antigo, a instalação terá uma aparência diferente. Você pode habilitar a nova experiência nas configurações de logon único do aplicativo G-Suite. Vá ao **Azure AD, aplicações Enterprise,** selecione **G Suite,** selecione **Single Sign-on** e, em seguida, clique em Experimentar a **nossa nova experiência**.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)

## <a name="frequently-asked-questions"></a>Perguntas Mais Frequentes

1. **P: Esta integração suporta a integração do Google Cloud Platform SSO com o Azure AD?**

    R: Sim. Google Cloud Platform e Google Apps compartilham a mesma plataforma de autenticação. Portanto, para fazer a integração do GCP, você precisa configurar o SSO com o Google Apps.

2. **P: Os Chromebooks e outros dispositivos Chrome são compatíveis com o único sinal de entrada da Azure AD?**
  
    R: Sim, os usuários podem entrar em seus dispositivos Chromebook usando suas credenciais do Azure AD. Consulte este artigo de [suporte da G Suite](https://support.google.com/chrome/a/answer/6060880) para obter informações sobre o porquê de os utilizadores poderem ser solicitados duas vezes para credenciais.

3. **P: Se eu permitir um único sign-on, os utilizadores poderão usar as suas credenciais De AD Azure para assinar em qualquer produto da Google, como Google Classroom, GMail, Google Drive, YouTube, e assim por diante?**

    R: Sim, dependendo da [G Suite](https://support.google.com/a/answer/182442?hl=en&ref_topic=1227583) que escolher para ativar ou desativar para a sua organização.

4. **P: Posso ativar um único sinal para apenas um subconjunto dos meus utilizadores da G Suite?**

    R: não, ativar o logon único requer imediatamente que todos os usuários do G Suite se autentiquem com suas credenciais do Azure AD. Como o G Suite não dá suporte a vários provedores de identidade, o provedor de identidade do seu ambiente do G Suite pode ser o Azure AD ou o Google, mas não ambos ao mesmo tempo.

5. **P: Se um utilizador é inscrito através do Windows, será automaticamente autenticado para a G Suite sem ser solicitado por uma palavra-passe?**

    R: há duas opções para habilitar esse cenário. Em primeiro lugar, os utilizadores podem inscrever-se em dispositivos Windows 10 através do [Azure Ative Directory Join](../device-management-introduction.md). Em alternativa, os utilizadores podem inscrever-se em dispositivos Windows que estejam ligados ao domínio de um Diretório Ativo no local que foi ativado para uma única inscrição no Azure AD através de uma implementação de Serviços da Federação de [Diretórios Ativos (AD FS).](../hybrid/plan-connect-user-signin.md) As duas opções exigem que você execute as etapas no tutorial a seguir para habilitar o logon único entre o Azure AD e o G Suite.

6. **P: O que devo fazer quando receber uma mensagem de erro de "e-mail inválido"?**

    R: para essa configuração, o atributo de email é necessário para que os usuários possam entrar. Este atributo não pode ser definido manualmente.

    O atributo de email é preenchido automaticamente para qualquer usuário com uma licença do Exchange válida. Se o usuário não estiver habilitado para email, esse erro será recebido, pois o aplicativo precisa obter esse atributo para conceder acesso.

    Você pode ir para portal.office.com com uma conta de administrador e clicar no centro de administração, cobrança, assinaturas, selecionar sua assinatura do Office 365 e, em seguida, clicar em atribuir a usuários, selecionar os usuários que deseja verificar sua assinatura e no painel direito, clicar em editar licenças.

    Depois que a licença do O365 é atribuída, pode levar alguns minutos para ser aplicada. Depois disso, o atributo User. mail será preenchido automaticamente e o problema deverá ser resolvido.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.

* G Suite suporta **SP** iniciado SSO

* G Suite suporta fornecimento [ **automatizado** de utilizadores](https://docs.microsoft.com/azure/active-directory/saas-apps/google-apps-provisioning-tutorial)
* Assim que configurar a G Suite, pode impor o Controlo de Sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. Controlo de Sessão estende-se a partir de Acesso Condicional. [Saiba como impor o controlo de sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-g-suite-from-the-gallery"></a>Adicionando o G Suite da Galeria

Para configurar a integração do G Suite ao Azure AD, você precisa adicionar o G Suite da Galeria à sua lista de aplicativos SaaS gerenciados.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No Add da secção **galeria,** digite **g suite** na caixa de pesquisa.
1. Selecione **G Suite** a partir do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-g-suite"></a>Configurar e testar o logon único do Azure AD para o G Suite

Configure e teste Azure AD SSO com G Suite utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no G Suite.

Para configurar e testar o SSO do Azure AD com o G Suite, conclua os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
    1. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
1. **[Configure G Suite SSO](#configure-g-suite-sso)** - para configurar as definições de inscrição únicas no lado da aplicação.
    1. **[Crie o utilizador](#create-g-suite-test-user)** de teste G Suite - para ter uma contrapartida de B.Simon na G Suite que esteja ligada à representação do utilizador da AD Azure.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configure Azure AD SSO

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **G Suite,** encontre a secção **Gerir** e selecione **um único sinal.**
1. Na página **de método de inscrição, selecione** **SAML**.
1. No **set single sign-on com** a página SAML, clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na secção **Basic SAML Configuration,** se pretender configurar para o **Gmail** executar os seguintes passos:

    a. Na caixa de texto **de URL sign-on,** escreva um URL utilizando o seguinte padrão: `https://www.google.com/a/<yourdomain.com>/ServiceLogin?continue=https://mail.google.com`

    b. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão:

    | |
    |--|
    | `google.com/a/<yourdomain.com>` |
    | `google.com` |
    | `https://google.com` |
    | `https://google.com/a/<yourdomain.com>` |

    c. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: 

    | |
    |--|
    | `https://google.com` |
    | `https://google.com/a/<yourdomain.com>` |


1. Na secção **basic SAML Configuration,** se pretender configurar para a Plataforma Cloud do **Google,** execute os seguintes passos:

    a. Na caixa de texto **de URL sign-on,** escreva um URL utilizando o seguinte padrão: `https://www.google.com/a/<yourdomain.com>/ServiceLogin?continue=https://console.cloud.google.com`

    b. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão:
    
    | |
    |--|
    | `google.com/a/<yourdomain.com>` |
    | `google.com` |
    | `https://google.com` |
    | `https://google.com/a/<yourdomain.com>` |
    
    c. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: 
    
    | |
    |--|
    | `https://google.com` |
    | `https://google.com/a/<yourdomain.com>` |

    > [!NOTE]
    > Estes valores não são reais. Atualize esses valores com a URL de entrada e o identificador reais. G Suite não fornece valor id/identificador de entidade na configuração single sign em modo que quando desverificar a opção **emitente específica** do domínio o valor do identificador será `google.com`. Se verificar a opção específica do **emitente** de domínio, será `google.com/a/<yourdomainname.com>`. Para verificar/desverificar a opção **emitente específica** do domínio, precisa de ir à secção **Configure G Suite SSO,** que é explicada mais tarde no tutorial. Para mais informações contacte a equipa de suporte do [Cliente G Suite.](https://www.google.com/contact/)

1. Seu aplicativo G Suite espera as asserções SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados à sua configuração de atributos de token SAML. A captura de tela a seguir mostra um exemplo disso. O valor padrão do **Identificador** de Utilizador Único é **user.userprincipalname,** mas o G Suite espera que este seja mapeado com o endereço de e-mail do utilizador. Para isso pode utilizar o atributo **user.mail** da lista ou utilizar o valor de atributo apropriado com base na configuração da sua organização.

    ![image](common/default-attributes.png)


1. Na configuração de um único sessão com a página **SAML,** na secção Certificado de **Assinatura SAML,** encontre **o Certificado (Base64)** e selecione **Descarregar** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de download de certificado](common/certificatebase64.png)

1. Na secção **Configurar g Suite,** copie os URL(s) adequados com base no seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

Nesta secção, você vai criar um utilizador de teste no portal Azure chamado B.Simon.

1. A partir do painel esquerdo no portal Azure, **selecione Azure Ative Directory**, selecione **Utilizadores**e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo **de nome do utilizador,** introduza o username@companydomain.extension. Por exemplo, `B.Simon@contoso.com`.
   1. Selecione a caixa de verificação de **palavra-passe do Show** e, em seguida, escreva o valor que está apresentado na caixa **password.**
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta seção, você habilitará B. Simon para usar o logon único do Azure concedendo acesso ao G Suite.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **G Suite**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

## <a name="configure-g-suite-sso"></a>Configurar SSO do G Suite

1. Abra um novo separador no seu navegador e inscreva-se na [Consola De Administrador G Suite](https://admin.google.com/) usando a sua conta de administrador.

2. Clique em **Segurança**. Se não vir o link, pode estar escondido sob o menu **Mais Controlos** na parte inferior do ecrã.

    ![Clique em Segurança.][10]

3. Na página **de Segurança,** clique em **Configurar um único sinal (SSO).**

    ![Clique em SSO.][11]

4. Execute as seguintes alterações de configuração:

    ![Configurar SSO][12]

    a. **Selecione Configurar SSO com fornecedor de identidade de terceiros**.

    b. No campo URL da **página sign-in** em G Suite, colá-lo o valor do URL de **Login** que copiou do portal Azure.

    c. No campo URL da **página Sign-out** em G Suite, colá-lo o valor do URL de **Logout** que copiou do portal Azure.

    d. No campo URL da **palavra-passe Change** em G Suite, colá-lo o valor do URL de **palavra-passe Change** que copiou do portal Azure.

    e. Na G Suite, para o **certificado de Verificação,** faça upload do certificado que descarregou do portal Azure.

    f. Verifique/desmarque a opção **de emitente específico do domínio,** de acordo com a nota mencionada na secção de **configuração SAML básica** acima no AD Azure.

    g. Clique em **Guardar Alterações**.

### <a name="create-g-suite-test-user"></a>Criar usuário de teste do G Suite

O objetivo desta secção é [criar um utilizador na G Suite](https://support.google.com/a/answer/33310?hl=en) chamado B.Simon. Depois que o usuário tiver sido criado manualmente no G Suite, o usuário poderá entrar usando suas credenciais de logon do Office 365.

O G Suite também dá suporte ao provisionamento automático de usuários. Para configurar o fornecimento automático de utilizadores, tem primeiro de [configurar](https://docs.microsoft.com/azure/active-directory/saas-apps/google-apps-provisioning-tutorial)o G Suite para o fornecimento automático do utilizador .

> [!NOTE]
> Verifique se o usuário já existe no G Suite se o provisionamento no Azure AD não foi ativado antes de testar o logon único.

> [!NOTE]
> Se precisar de criar um utilizador manualmente, contacte a equipa de suporte da [Google](https://www.google.com/contact/).

## <a name="test-sso"></a>Teste SSO 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Ao clicar no bloco do G Suite no painel de acesso, você deverá ser conectado automaticamente ao G Suite para o qual você configurou o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e o único registo com o Diretório Ativo azure.](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [O que é o acesso condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Configurar o fornecimento de utilizadores](https://docs.microsoft.com/azure/active-directory/saas-apps/google-apps-provisioning-tutorial)

- [Experimente g suite com Azure AD](https://aad.portal.azure.com/)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Como proteger a G Suite com visibilidade e controlos avançados](https://docs.microsoft.com/cloud-app-security/protect-gsuite)

<!--Image references-->

[10]: ./media/google-apps-tutorial/gapps-security.png
[11]: ./media/google-apps-tutorial/security-gapps.png
[12]: ./media/google-apps-tutorial/gapps-sso-config.png