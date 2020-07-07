---
title: 'Tutorial: Azure Ative Directy integração única (SSO) com o Conector Google Cloud (G Suite) Microsoft Docs'
description: Saiba como configurar um único sinal de insuflação entre o Azure Ative Directory e o Google Cloud (G Suite) Connector.
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
ms.date: 05/06/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 091129d1fbca4141e9841af6a9991d44a6663801
ms.sourcegitcommit: a989fb89cc5172ddd825556e45359bac15893ab7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/01/2020
ms.locfileid: "85799195"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-google-cloud-g-suite-connector"></a>Tutorial: Azure Ative Directy integração única (SSO) com o Conector Google Cloud (G Suite)

Neste tutorial, você vai aprender a integrar o Conector Google Cloud (G Suite) com Azure Ative Directory (Azure AD). Quando integrar o Conector Google Cloud (G Suite) com Azure AD, pode:

* Controle em Azure AD que tem acesso ao Conector Google Cloud (G Suite).
* Permita que os seus utilizadores sejam automaticamente inscritos no Conector Google Cloud (G Suite) com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

- Uma assinatura AD Azure.
- Google Cloud (G Suite) Connector única assinatura ativada (SSO).
- Uma subscrição do Google Apps ou uma subscrição da Plataforma Google Cloud.

> [!NOTE]
> Para testar os passos neste tutorial, não recomendamos a utilização de um ambiente de produção. Este documento foi criado utilizando a experiência single-sign-on do novo utilizador. Se ainda estiver a usar o antigo, a configuração será diferente. Pode ativar a nova experiência nas definições de Sign-on única da aplicação G-Suite. Vá ao **Azure AD, aplicações enterprise**, selecione **Google Cloud (G Suite) Connector**, selecione **Single Sign-on** e, em seguida, clique em **Experimentar a nossa nova experiência**.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a não ser que seja necessário.
- Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)

## <a name="frequently-asked-questions"></a>Perguntas Mais Frequentes

1. **P: Esta integração suporta a integração SSO da Plataforma Google Cloud com a AZure AD?**

    R: Sim. A Plataforma Google Cloud e as Aplicações google partilham a mesma plataforma de autenticação. Assim, para fazer a integração do GCP é necessário configurar o SSO com as Aplicações google.

2. **P: Os Chromebooks e outros dispositivos Chrome são compatíveis com um único sinal de Azure AD?**
  
    R: Sim, os utilizadores são capazes de iniciar súms nos seus dispositivos Chromebook usando as suas credenciais AZure AD. Consulte este [artigo de suporte do Conector Google Cloud (G Suite)](https://support.google.com/chrome/a/answer/6060880) para obter informações sobre o motivo pelo qual os utilizadores podem ser solicitados para obter credenciais duas vezes.

3. **P: Se eu permitir um único sign-on, os utilizadores serão capazes de usar as suas credenciais AD AZure para assinar em qualquer produto da Google, como Google Classroom, GMail, Google Drive, YouTube, e assim por diante?**

    R: Sim, dependendo do [conector do Google Cloud (G Suite)](https://support.google.com/a/answer/182442?hl=en&ref_topic=1227583) que escolher para ativar ou desativar para a sua organização.

4. **P: Posso ativar um único sinal de entrada para apenas um subconjunto dos meus utilizadores do Google Cloud (G Suite) Connector?**

    R: Não, ligar um único sinal imediatamente requer que todos os utilizadores do Conector Google Cloud (G Suite) autentem com as suas credenciais AD Azure. Como o Google Cloud (G Suite) Connector não suporta ter vários fornecedores de identidade, o fornecedor de identidade para o ambiente do Conector Google Cloud (G Suite) pode ser Azure AD ou Google -- mas não ambos ao mesmo tempo.

5. **P: Se um utilizador for assinado através do Windows, será que autentica automaticamente o Conector Google Cloud (G Suite) sem ser solicitado para uma senha?**

    R: Existem duas opções para permitir este cenário. Em primeiro lugar, os utilizadores podem iniciar súmitos nos dispositivos windows 10 através [do Azure Ative Directory Join](../device-management-introduction.md). Em alternativa, os utilizadores poderiam assinar em dispositivos Windows que se unem a um Diretório Ativo no local que foi ativado para um único sinal de entrada no AD AD através de uma implementação [de Serviços da Federação de Diretórios Ativos (AD FS).](../hybrid/plan-connect-user-signin.md) Ambas as opções requerem que execute os passos no seguinte tutorial para ativar um único sinal de acesso entre O AD AD e Google Cloud (G Suite) Connector.

6. **P: O que devo fazer quando recebo uma mensagem de erro "e-mail inválida"?**

    R: Para esta configuração, o atributo de e-mail é necessário para que os utilizadores possam iniciar sação. Este atributo não pode ser definido manualmente.

    O atributo de e-mail é autopovoado para qualquer utilizador com uma licença de Troca válida. Se o utilizador não estiver habilitado para o e-mail, este erro será recebido, uma vez que a aplicação necessita de obter este atributo para dar acesso.

    Pode ir a portal.office.com com uma conta Admin, depois clicar no centro de Administração, faturação, subscrições, selecionar a subscrição do Office 365 e clicar em atribuir aos utilizadores, selecionar os utilizadores que pretende verificar a sua subscrição e no painel direito, clicar nas licenças de edição.

    Uma vez atribuída a licença O365, pode levar alguns minutos a ser aplicada. Depois disso, o atributo user.mail será automaticamente povoado e o problema deve ser resolvido.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Conector Google Cloud (G Suite) suporta **SSO** iniciado SP

* O Conector Google Cloud (G Suite) suporta o fornecimento [ **automatizado** de utilizadores](https://docs.microsoft.com/azure/active-directory/saas-apps/google-apps-provisioning-tutorial)
* Assim que configurar o Conector Google Cloud (G Suite) pode impor o Controlo de Sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O Controlo de Sessão estende-se a partir do Acesso Condicional. [Saiba como impor o controlo da sessão com a Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-google-cloud-g-suite-connector-from-the-gallery"></a>Adicionar conector Google Cloud (G Suite) da galeria

Para configurar a integração do Conector Google Cloud (G Suite) em AZure AD, é necessário adicionar o Conector Google Cloud (G Suite) da galeria à sua lista de aplicações geridas para o SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria,** digite o **Conector Google Cloud (Suíte G)** na caixa de pesquisa.
1. Selecione **o Conector Google Cloud (G Suite)** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-google-cloud-g-suite-connector"></a>Configurar e testar o Azure AD um único sinal de inscrição para o Conector Google Cloud (G Suite)

Configure e teste Azure AD SSO com o Conector Google Cloud (G Suite) usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado no Conector Google Cloud (G Suite).

Para configurar e testar o Azure AD SSO com o Conector Google Cloud (G Suite), complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure o Conector SSO do Google Cloud (G Suite)](#configure-google-cloud-g-suite-connector-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. Crie o utilizador de **[teste do Conector Google Cloud (G Suite)](#create-google-cloud-g-suite-connector-test-user)** - para ter uma contraparte de B.Simon no Google Cloud (G Suite) Connector que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração da aplicação **do Conector Google Cloud (G Suite),** encontre a secção **Gerir** e selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **de Configuração Básica SAML,** se pretender configurar para o **Gmail,** execute os seguintes passos:

    a. Na caixa de texto **url de entrada de sinais,** digite um URL utilizando o seguinte padrão:`https://www.google.com/a/<yourdomain.com>/ServiceLogin?continue=https://mail.google.com`

    b. Na caixa de texto **identifier,** digite um URL utilizando o seguinte padrão:

    ```http
    google.com/a/<yourdomain.com>
    google.com
    https://google.com
    https://google.com/a/<yourdomain.com>
    ```

    c. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: 

    ```http
    https://www.google.com
    https://www.google.com/a/<yourdomain.com>
    ```

1. Na secção **De Configuração Básica SAML,** se pretender configurar para a **Plataforma Google Cloud** execute os seguintes passos:

    a. Na caixa de texto **url de entrada de sinais,** digite um URL utilizando o seguinte padrão:`https://www.google.com/a/<yourdomain.com>/ServiceLogin?continue=https://console.cloud.google.com`

    b. Na caixa de texto **identifier,** digite um URL utilizando o seguinte padrão:
    
    ```http
    google.com/a/<yourdomain.com>
    google.com
    https://google.com
    https://google.com/a/<yourdomain.com>
    ```
    
    c. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: 
    
    ```http
    https://www.google.com
    https://www.google.com/a/<yourdomain.com>
    ```

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL e o identificador de inscrição real. O Conector Google Cloud (Suíte G) não fornece o valor de ID/Identificador de Entidade na configuração de sinal único, pelo que quando desmarque a opção **de emitente específico** de domínio, o valor do identificador será `google.com` . Se verificar a opção **de emitente específico** de domínio, será `google.com/a/<yourdomainname.com>` . Para verificar/desmarcar a opção **de emitente específico** de domínio, tem de ir à secção **Configure Google Cloud (G Suite) Connector SSO,** que é explicada mais tarde no tutorial. Para mais informações contacte a equipa de [suporte do Cliente do Conector Google Cloud (G Suite).](https://www.google.com/contact/)

1. A sua aplicação de conector Google Cloud (G Suite) espera as afirmações SAML num formato específico, o que requer que adicione mapeamentos de atributos personalizados à configuração de atributos de ficha SAML. A imagem que se segue mostra um exemplo para isto. O valor predefinido do **Identificador de Utilizador Exclusivo** é **user.userprincipalname,** mas o Conector Google Cloud (G Suite) espera que este seja mapeado com o endereço de e-mail do utilizador. Para isso pode utilizar o atributo **user.mail** da lista ou utilizar o valor de atributo apropriado com base na configuração da sua organização.

    ![image](common/default-attributes.png)


1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre **o Certificado (Base64)** e selecione **Descarregamento** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

1. Na secção **de conector Do Google Cloud (G Suite) da Configuração, copie** os URL(s) apropriados com base no seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD

Nesta secção, irá criar um utilizador de teste no portal Azure chamado B.Simon.

1. A partir do painel esquerdo no portal Azure, selecione **Azure Ative Directory**, selecione **Utilizadores**, e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo **nome do utilizador,** insira o username@companydomain.extension . Por exemplo, `B.Simon@contoso.com`.
   1. Selecione a caixa **de verificação de palavra-passe Show** e, em seguida, anote o valor que é apresentado na caixa **palavra-passe.**
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permitirá que B.Simon use o único sinal de Azure, concedendo acesso ao Conector Google Cloud (G Suite).

1. No portal Azure, selecione **Aplicações empresariais**e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Google Cloud (G Suite) Connector**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-google-cloud-g-suite-connector-sso"></a>Configurar o Conector Google Cloud (G Suite) SSO

1. Abra um novo separador no seu navegador e inscreva-se na [Consola de Administração do Conector Google Cloud (G Suite)](https://admin.google.com/) utilizando a sua conta de administrador.

2. Clique **em Segurança**. Se não vir a ligação, pode estar escondido no menu **Mais Controlos** na parte inferior do ecrã.

    ![Clique em Segurança.][10]

3. Na página **'Segurança',** clique **em Configurar um único sinal de inscrição (SSO).**

    ![Clique em SSO.][11]

4. Execute as seguintes alterações de configuração:

    ![Configurar SSO][12]

    a. Selecione **Setup SSO com fornecedor de identidade de terceiros**.

    b. No campo **URL de página de entrada** no Conector Google Cloud (G Suite), cole o valor do URL de **Login** que copiou do portal Azure.

    c. No campo **URL da página de sign-out** no Conector Google Cloud (G Suite), cole o valor do URL **logout** que copiou do portal Azure.

    d. No campo URL de **palavra-passe Change** no Conector Google Cloud (G Suite), cole o valor do URL de **palavra-passe Change** que copiou do portal Azure.

    e. No Conector Google Cloud (G Suite), para o **certificado de Verificação,** faça o upload do certificado que descarregou a partir do portal Azure.

    f. Verifique/desmarque a utilização de uma opção **de emitente específica** de domínio, de acordo com a nota mencionada na secção **de Configuração BÁSICA SAML** acima no AD Azure.

    exemplo, Clique em **Guardar Alterações**.

### <a name="create-google-cloud-g-suite-connector-test-user"></a>Criar o utilizador de teste do conector Google Cloud (G Suite)

O objetivo desta secção é [criar um utilizador no Conector Google Cloud (G Suite)](https://support.google.com/a/answer/33310?hl=en) chamado B.Simon. Depois de o utilizador ter sido criado manualmente no Conector Google Cloud (G Suite), o utilizador poderá agora iniciar sessão utilizando as suas credenciais de login do Office 365.

O Conector Google Cloud (G Suite) também suporta o fornecimento automático do utilizador. Para configurar o fornecimento automático do utilizador, tem primeiro de configurar o [Conector Google Cloud (G Suite) para o fornecimento automático do utilizador](https://docs.microsoft.com/azure/active-directory/saas-apps/google-apps-provisioning-tutorial).

> [!NOTE]
> Certifique-se de que o seu utilizador já existe no Conector Google Cloud (G Suite) se o provisionamento no AD Azure não tiver sido ligado antes de testar o Single Sign-on.

> [!NOTE]
> Se precisar de criar um utilizador manualmente, contacte a [equipa de suporte](https://www.google.com/contact/)da Google .

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo do Conector Google Cloud (G Suite) no Painel de Acesso, deverá ser automaticamente inscrito no Conector Google Cloud (G Suite) para o qual configura o SSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é acesso à aplicação e um único acesso com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Configurar o Provisionamento do Utilizador](https://docs.microsoft.com/azure/active-directory/saas-apps/google-apps-provisioning-tutorial)

- [Experimente o Conector Google Cloud (G Suite) com Azure AD](https://aad.portal.azure.com/)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Como proteger o Conector Google Cloud (G Suite) com visibilidade e controlos avançados](https://docs.microsoft.com/cloud-app-security/protect-gsuite)

<!--Image references-->

[10]: ./media/google-apps-tutorial/gapps-security.png
[11]: ./media/google-apps-tutorial/security-gapps.png
[12]: ./media/google-apps-tutorial/gapps-sso-config.png