---
title: 'Tutorial: Integração do Diretório Ativo Azure com a Mitel Connect | Microsoft Docs'
description: Saiba como configurar um único sinal entre o Azure Ative Directory e o Mitel Connect.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/31/2020
ms.author: jeedes
ms.openlocfilehash: b89ae74a1f4ead568a5454c9494d4a82b0eeea3e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96015184"
---
# <a name="tutorial-azure-active-directory-integration-with-mitel-micloud-connect-or-cloudlink-platform"></a>Tutorial: Integração do Azure Ative Directory com Mitel MiCloud Connect ou CloudLink Platform

Neste tutorial, você aprenderá a usar a app Mitel Connect para integrar o Azure Ative Directory (Azure AD) com Mitel MiCloud Connect ou CloudLink Platform. A aplicação Mitel Connect está disponível na Galeria Azure. Integrar o Azure AD com a Plataforma MiCloud Connect ou CloudLink proporciona-lhe os seguintes benefícios:

* Pode controlar o acesso dos utilizadores às aplicações Do MiCloud Connect e às aplicações CloudLink em AZure AD utilizando as suas credenciais empresariais.
* Pode permitir que os utilizadores na sua conta sejam automaticamente inscritos no MiCloud Connect ou no CloudLink (súmido único) utilizando as suas contas AD Azure.

Para mais detalhes sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único acesso com o Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de iniciar a integração do AD AZure com o Mitel MiCloud Connect ou a CloudLink Platform.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AD do Azure com o MiCloud Connect, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Uma conta Mitel MiCloud Connect ou uma conta Mitel CloudLink, dependendo da aplicação que pretende configurar.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você vai configurar e testar Azure AD single sign-on (SSO).

* Mitel Connect suporta **SSO** iniciado SP
* Uma vez configurado o Mitel Connect, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="add-mitel-connect-from-the-gallery"></a>Adicione Mitel Connect da galeria

Para configurar a integração do Mitel Connect no Azure AD, é necessário adicionar o Mitel Connect da galeria à sua lista de aplicações geridas do SaaS no portal Azure.

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, selecione **Azure Ative Directory**.

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Selecione **Aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Selecione **Nova aplicação**.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Digite **Mitel Connect** no campo de pesquisa, selecione **Mitel Connect** do painel de resultados e, em seguida, selecione **Adicionar**.

     ![Mitel Connect na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, irá configurar e testar O SSO Azure AD com MiCloud Connect ou CloudLink Platform com base num utilizador de teste chamado **_Britta Simon_**. Para um único sinal de acesso ao trabalho, deve ser estabelecido um link entre o utilizador no portal AD Azure e o utilizador correspondente na plataforma Mitel. Consulte as seguintes secções para obter informações sobre a configuração e teste do Azure AD SSO com o MiCloud Connect ou a CloudLink Platform.
* Configure e teste Azure AD SSO com MiCloud Connect
* Configure e teste Azure AD SSO com plataforma CloudLink

## <a name="configure-and-test-azure-ad-sso-with-micloud-connect"></a>Configure e teste Azure AD SSO com MiCloud Connect

Para configurar e testar o Azure AD um único sinal de sação com o MiCloud Connect:

1. **[Configure o MiCloud Connect para SSO com AD AZure](#configure-micloud-connect-for-sso-with-azure-ad)** - para permitir que os seus utilizadores utilizem esta funcionalidade e para configurar as definições SSO no lado da aplicação.
2. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
3. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
4. Crie um utilizador de **[teste Mitel MiCloud Connect](#create-a-mitel-micloud-connect-test-user)** - para ter uma contraparte de Britta Simon na sua conta MiCloud Connect que esteja ligada à representação AD AZure do utilizador.
5. **[Teste um único sinal](#test-single-sign-on)** - para verificar se a configuração funciona.

## <a name="configure-micloud-connect-for-sso-with-azure-ad"></a>Configurar o MiCloud Connect para SSO com AZure AD

Nesta secção, irá ativar o único sinal de Azure AD para o MiCloud Connect no portal Azure e configurar a sua conta MiCloud Connect para permitir que o SSO utilize a Azure AD.

Para configurar o MiCloud Connect com o SSO para Azure AD, é mais fácil abrir o portal Azure e o portal da Conta Mitel lado a lado. Terá de copiar algumas informações do portal Azure para o portal da Conta Mitel e algumas do portal da Conta Mitel para o portal Azure.


1. Para abrir a página de configuração no [portal Azure:](https://portal.azure.com/)

    1. Na página de integração da aplicação **Mitel Connect,** selecione **Single sign-on**.

       ![Configurar link único de inscrição](common/select-sso.png)

    1. Na caixa de diálogo **de método de inscrição única,** selecione **SAML**.
    
       ![Único modo de seleção de s-on](common/select-saml-option.png)
    
       É apresentada a página de inscrição baseada em SAML.

2. Para abrir a caixa de diálogo de configuração no portal Da Conta Mitel:

    1. No menu **'Sistema telefónico',** selecione **Funcionalidades adicionais**.

    1. À direita do **Sign-On único,** selecione **Ativar** ou **Configurações**.
    
    Aparece a caixa de diálogo de definições de Sign-On única de ligação.
    
3. Selecione **a caixa de verificação 'Sign-On' única** ativada.
    
    ![Screenshot que mostra a página Configurações de Sign-On Únicas mitel Connect, com a caixa de verificação Enable Single Sign-On selecionada.](./media/mitel-connect-tutorial/mitel-connect-enable.png)

4. No portal Azure, selecione o ícone **Editar** na secção **Configuração Básica SAML.**
   
    ![A screenshot mostra o Conjunto Single Sign-On com a página SAML com o ícone de edição selecionado.](common/edit-urls.png)

    Aparece a caixa de diálogo de configuração BÁSICA SAML.

5.  Copie o URL do campo **Identificador De Mitel (Entity ID)** no portal da Conta Mitel e cole-o no campo **Identificador (ID da Entidade)** no portal Azure.

6. Copie o URL do campo URL de **resposta (URL de serviço ao consumidor de afirmação)** no portal da Conta Mitel e cole-o no campo **URL de resposta (URL do serviço de apoio ao consumidor de afirmação)** no portal Azure.

   ![O Screenshot mostra a Configuração Básica de SAML no portal Azure e a secção De Fornecedor de Identidade Configurado no portal da Conta Mitel com linhas que indicam a relação entre eles.](./media/mitel-connect-tutorial/mitel-azure-basic-configuration.png)

7. Na caixa de texto **URL de entrada de assinatura,** digite um dos seguintes URLs:

    1. **https://portal.shoretelsky.com** - utilizar o portal Da Conta Mitel como a sua aplicação Mitel padrão
    1. **https://teamwork.shoretel.com** - utilizar o Teamwork como a sua aplicação mitel padrão

    > [!NOTE]
    > A aplicação Mitel predefinida é a aplicação que é acedida quando um utilizador seleciona o azulejo Mitel Connect no Painel de Acesso. Esta é também a aplicação acedida ao fazer uma configuração de teste a partir do Azure AD.

8. **Selecione Guardar** na caixa de diálogo **de configuração BÁSICA SAML** no portal Azure.

9. Na secção **Certificado de Assinatura SAML** na página **de sinalização baseada em SAML** no portal Azure, selecione **Baixar** ao lado do **Certificado (Base64)** para descarregar o **Certificado de Assinatura** e guardá-lo para o seu computador.

    ![A screenshot mostra o painel de certificado de assinatura SAML onde pode descarregar um certificado.](./media/mitel-connect-tutorial/azure-signing-certificate.png)

10. Abra o ficheiro Certificado de Assinatura num editor de texto, copie todos os dados do ficheiro e, em seguida, cole os dados no campo **Certificado de Assinatura** no portal da Conta Mitel. 

      ![A screenshot mostra o campo de Certificado de Assinatura.](./media/mitel-connect-tutorial/mitel-connect-signing-certificate.png)

11. Na secção **Configuração Mitel Connect** na página **de sinalização baseada em SAML** do portal Azure:

     1. Copie o URL do campo URL de **login** e **cole-o no** campo URL de entrada no portal da Conta Mitel.

     1. Copie o URL do campo **Azure AD Identifier** e cole-o no campo **ID** da Entidade no portal da Conta Mitel.
         
         ![A screenshot mostra a relação entre a página de sign-on baseada em SAML do portal Azure e o portal da Conta Mitel.](./media/mitel-connect-tutorial/mitel-azure-set-up-connect.png)

12. **Selecione Guardar** na caixa de diálogo de **definições de Sign-On única** de ligação no portal da Conta Mitel.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD 

Nesta secção, irá criar um utilizador de teste chamado Britta Simon no portal Azure.

1. No portal Azure, no painel esquerdo, selecione **Azure Ative Directory**, selecione **Utilizadores**, e, em seguida, selecione **Todos os utilizadores**.

    ![Os links "Utilizadores e grupos" e "Todos os utilizadores"](common/users.png)

2. Selecione **Novo utilizador** na parte superior do ecrã.

    ![Novo botão de utilizador](common/new-user.png)

3. Na caixa de diálogo das propriedades do utilizador, faça os seguintes passos:

    ![A caixa de diálogo do utilizador](common/user-properties.png)

    1. No campo **Nome,** **escreva BrittaSimon.**
  
    1. No campo **nome do utilizador,** escreva brittasimon@ \<yourcompanydomain\> . \<extension\> .  Por exemplo, BrittaSimon@contoso.com.

    1. Selecione a caixa **de verificação de palavra-passe Show** e, em seguida, anote o valor que é apresentado na caixa **palavra-passe.**

    1. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permitirá que Britta Simon use a Azure single sign-on, concedendo acesso ao Mitel Connect.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Mitel Connect**.

    ![O link Mitel Connect na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** na caixa de diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição de adição](common/add-assign-user.png)

5. Na caixa de diálogo **de Utilizadores e grupos,** selecione **Britta Simon** na lista **de Utilizadores** e, em seguida, escolha **Selecione** na parte inferior do ecrã.

6. Se estiver à espera de qualquer valor de função na afirmação DE SAML, selecione a função adequada para o utilizador a partir da lista na caixa de diálogo **Select Role** e, em seguida, escolha **Selecione** na parte inferior do ecrã.

7. Na caixa de diálogo **'Adicionar Atribuição',** selecione **Atribuir**.

### <a name="create-a-mitel-micloud-connect-test-user"></a>Criar um utilizador de teste Mitel MiCloud Connect

Nesta secção, cria um utilizador chamado Britta Simon na sua conta MiCloud Connect. Os utilizadores devem ser criados e ativados antes de utilizarem uma única s ativação.

Para obter mais informações sobre a adição de utilizadores no portal da Conta Mitel, consulte o artigo [De Adicionar um Utilizador](https://shoretelcommunity.force.com/s/article/Adding-Users-092815) na Base de Conhecimento de Mitel.

Crie um utilizador na sua conta MiCloud Connect com os seguintes detalhes:

* **Nome:** Britta Simão
* **Endereço de e-mail de negócios:**`brittasimon@<yourcompanydomain>.<extension>`   
  (Exemplo: [brittasimon@contoso.com](mailto:brittasimon@contoso.com) )
* **Nome de utilizador:**`brittasimon@<yourcompanydomain>.<extension>`  
  (Exemplo: [brittasimon@contoso.com](mailto:brittasimon@contoso.com) ; o nome de utilizador do utilizador é tipicamente o mesmo que o endereço de e-mail do utilizador)

> [!NOTE]
> O nome de utilizador MiCloud Connect do utilizador deve ser idêntico ao endereço de e-mail do utilizador em Azure.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, irá testar a sua configuração de inscrição única Azure AD utilizando o Painel de Acesso.

Quando selecionar o azulejo Mitel Connect no Painel de Acesso, deverá ser automaticamente redirecionado para iniciar sôm na aplicação MiCloud Connect configurada como o seu padrão no campo **URL de acesso.** Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="configure-and-test-azure-ad-sso-with-cloudlink-platform"></a>Configure e teste Azure AD SSO com plataforma CloudLink

Esta secção descreve como ativar o Azure AD SSO para a plataforma CloudLink no portal Azure e como configurar a sua conta de plataforma CloudLink para permitir um único sinal de acesso utilizando a Azure AD.

Para configurar a plataforma CloudLink com um único sign-on para Azure AD, recomenda-se que abra o portal Azure e o portal cloudLink Accounts lado a lado, uma vez que terá de copiar algumas informações do portal Azure para o portal cloudLink Accounts e vice-versa.

1. Para abrir a página de configuração no [portal Azure:](https://portal.azure.com/)

    1. Na página de integração da aplicação **Mitel Connect,** selecione **Single sign-on**.

       ![Configurar link único de inscrição](common/select-sso.png)

    1. Na caixa de diálogo **de método de inscrição única,** selecione **SAML**.

       ![Único modo de seleção de s-on](common/select-saml-option.png)
    
       A página **de sign-on baseada em SAML** abre, exibindo a secção **de configuração básica saml.**

       ![A screenshot mostra a página de sign-on baseada em SAML com configuração SAML básica.](./media/mitel-connect-tutorial/mitel-azure-saml-settings.png)

2. Para aceder ao **sinal único AZure AD No** painel de configuração no portal contas CloudLink:

    1. Aceda à página **informação** da conta do cliente com a qual pretende ativar a integração.

    1. Na secção **Integrações,** selecione **+ Adicionar novo**. Um ecrã pop-up exibe o painel **Integrações.**

    1. Selecione o separador **3º da festa.** É apresentada uma lista de aplicações de terceiros apoiadas. Selecione o botão **Adicionar** associado ao **sinal único AD Ad Azure ligado** e selecione **Feito**.

       ![A screenshot mostra a página Integrações onde pode adicionar Azure A D Single Sign-On.](./media/mitel-connect-tutorial/mitel-cloudlink-integrations.png)

       O **Azure AD Single Sign On** está ativado para a conta do cliente e é adicionado à secção **integrações** da página Informações de **Conta.**   

   1. Selecione **configuração completa**.
    
      ![A screenshot mostra a opção configuração completa para Azure A D Single Sign-On.](./media/mitel-connect-tutorial/mitel-cloudlink-complete-setup.png)
      
      **Abre-se o sinal único Azure AD no** painel de configuração.
      
       ![A screenshot mostra Azure A D Single Sign-On configuração.](./media/mitel-connect-tutorial/mitel-cloudlink-sso-setup.png)
       
       A Mitel recomenda que não seja selecionada a caixa de verificação **Enable Mitel (Opcional)** na secção **de credenciais de Mitel Opcional.** Selecione esta caixa de verificação apenas se quiser que o utilizador faça sôs entrada na aplicação CloudLink utilizando as credenciais Mitel, além da opção de início de sação única.

3. No portal Azure, a partir da página **sign-on baseada em SAML,** selecione o ícone **Editar** na secção **Configuração Básica SAML.** Abre-se o painel **de configuração BÁSICO SAML.**

    ![A screenshot mostra o painel de configuração de SAML básico com o ícone editar selecionado.](./media/mitel-connect-tutorial/mitel-azure-saml-basic.png)
 
 4. Copie o URL do campo **Identificador De Mitel (Entity ID)** no portal de Contas CloudLink e cole-o no campo **Identificador (ID da Entidade)** no portal Azure.

 5. Copie o URL do campo URL de **resposta (URL de serviço ao consumidor de afirmação)** no portal cloudLink Accounts e cole-o no campo **URL de resposta (URL do serviço de apoio ao consumidor de afirmação)** no portal Azure.  
    
    ![O screenshot mostra a relação entre as páginas no portal cloudLink accounts e no portal Azure.](./media/mitel-connect-tutorial/mitel-cloudlink-saml-mapping.png) 

 6. Na caixa de texto **URL de acesso,** digite o URL `https://accounts.mitel.io` para utilizar o portal CloudLink Accounts como a sua aplicação Mitel padrão.
     
     ![A screenshot mostra o sinal na caixa de texto U R L.](./media/mitel-connect-tutorial/mitel-cloudlink-sign-on-url.png)
  
     > [!NOTE]
     > A aplicação Mitel predefinido é a aplicação que abre quando um utilizador seleciona o azulejo Mitel Connect no Painel de Acesso. Esta é também a aplicação acedida quando o utilizador configura uma configuração de teste a partir do Azure AD.

7. **Selecione Guardar** na caixa de diálogo **de configuração BÁSICA SAML.**

8. Na secção certificado de **assinatura SAML** na página **de sinalização baseada em SAML** no portal Azure, selecione **Baixar** ao lado do **Certificado (Base64)** para descarregar o **Certificado de Assinatura**. Guarde o certificado no seu computador.
  
    ![A screenshot mostra a secção de Certificado de Assinatura SAML onde pode descarregar um certificado Base64.](./media/mitel-connect-tutorial/mitel-cloudlink-save-certificate.png)

9. Abra o ficheiro Certificado de Assinatura num editor de texto, copie todos os dados do ficheiro e, em seguida, cole os dados no campo **Certificado de Assinatura** no portal contas CloudLink.  

    > [!NOTE]
    > Se tiver mais de um certificado, recomendamos que os cole um após o outro. 
       
    ![A screenshot mostra o passo dois do procedimento em que preenche os valores da sua integração Azure A D.](./media/mitel-connect-tutorial/mitel-cloudlink-enter-certificate.png)

10. Na secção **Configuração Mitel Connect** na página **de inscrição baseada em SAML** do portal Azure:

     1. Copie o URL do campo URL de **login** e **cole-o no** campo URL de entrada no portal cloudLink Accounts.

     1. Copie o URL do campo **Azure AD Identifier** e cole-o no campo **IDP Identifier (Entity ID)** no portal de Contas CloudLink.
     
        ![A screenshot mostra a fonte dos valores descritos aqui no Mintel Connect.](./media/mitel-connect-tutorial/mitel-cloudlink-copy-settings.png)

11. **Selecione Guardar** no **sinal único Azure AD No** painel no portal contas CloudLink.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD 

Nesta secção, irá criar um utilizador de teste chamado Britta Simon no portal Azure.

1. No portal Azure, no painel esquerdo, selecione **Azure Ative Directory**, selecione **Utilizadores**, e, em seguida, selecione **Todos os utilizadores**.

    ![Os links "Utilizadores e grupos" e "Todos os utilizadores"](common/users.png)

2. Selecione **Novo utilizador** na parte superior do ecrã.

    ![Novo botão de utilizador](common/new-user.png)

3. Na caixa de diálogo das propriedades do utilizador, faça os seguintes passos:

    ![A caixa de diálogo do utilizador](common/user-properties.png)

    1. No campo **Nome,** **escreva BrittaSimon.**
  
    1. No campo **nome do utilizador,** escreva brittasimon@ \<yourcompanydomain\> . \<extension\> .  Por exemplo, BrittaSimon@contoso.com.

    1. Selecione a caixa **de verificação de palavra-passe Show** e, em seguida, anote o valor que é apresentado na caixa **palavra-passe.**

    1. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permitirá que Britta Simon use a Azure single sign-on, concedendo acesso ao Mitel Connect.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Mitel Connect**.

    ![O link Mitel Connect na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** na caixa de diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição de adição](common/add-assign-user.png)

5. Na caixa de diálogo **de Utilizadores e grupos,** selecione **Britta Simon** na lista **de Utilizadores** e, em seguida, escolha **Selecione** na parte inferior do ecrã.

6. Se estiver à espera de qualquer valor de função na afirmação DE SAML, selecione a função adequada para o utilizador a partir da lista na caixa de diálogo **Select Role** e, em seguida, escolha **Selecione** na parte inferior do ecrã.

7. Na caixa de diálogo **'Adicionar Atribuição',** selecione **Atribuir**.

### <a name="create-a-cloudlink-test-user"></a>Criar um utilizador de teste CloudLink

Esta secção descreve como criar um utilizador de teste chamado **_Britta Simon_** na sua plataforma CloudLink. Os utilizadores devem ser criados e ativados antes de poderem utilizar uma única s ativação.

Para obter mais informações sobre a adição de utilizadores no portal contas CloudLink, consulte **_Utilizadores de Gestão_** na [documentação das Contas CloudLink.](https://www.mitel.com/document-center/technology/cloudlink/all-releases/en/cloudlink-accounts-html)

Crie um utilizador no seu portal de Contas CloudLink com os seguintes detalhes:

* Nome: Britta Simon
* Primeiro nome: Britta
* Apelido: Simão
* E-mail: BrittaSimon@contoso.com

> [!NOTE]
> O endereço de e-mail CloudLink do utilizador deve ser idêntico ao **Nome Principal** do Utilizador no portal Azure.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, irá testar a sua configuração Azure AD SSO utilizando o Painel de Acesso.

Quando selecionar o azulejo Mitel Connect no Painel de Acesso, será automaticamente redirecionado para iniciar sôm na aplicação CloudLink configurada como o seu padrão no campo **URL de acesso.** Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](../conditional-access/overview.md)