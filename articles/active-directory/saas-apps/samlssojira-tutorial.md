---
title: 'Tutorial: Integração do Azure Ative Directory com a SAML SSO para jira pela Resolução GmbH Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o SAML SSO para a Jira por resolução GmbH.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/03/2018
ms.author: jeedes
ms.openlocfilehash: fe241a3fd74e1421f1bd3d39087fe776ee7b61d9
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92891623"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-jira-by-resolution-gmbh"></a>Tutorial: Integração do Azure Ative Directory com a SAML SSO para a Jira por resolução GmbH

Neste tutorial, você aprende a configurar SAML SSO para Jira por resolução GmbH com Azure Ative Directory (Azure AD).
Integrar o SSO SAML para jira por resolução A GmbH com Azure AD proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que pode iniciar súplicização em Jira com o plugin SSO SAML por resolução GmbH.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos na Jira com as suas contas AD Azure utilizando sSO SAML para Jira por resolução GmbH (Sign-On Único).
* Pode gerir as suas contas numa localização central - o portal Azure.

Se quiser saber mais detalhes sobre a integração da aplicação SaaS com o Azure AD, consulte o que é o acesso à [aplicação e o único acesso ao Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AD Azure e SAML SSO para jira por resolução GmbH, você precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode ter um julgamento de um mês [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* SAML SSO para Jira por resolução Assinatura ativada pela GmbH

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* SAML SSO para Jira por resolução GmbH apoia **SP** e **IDP** iniciado SSO

## <a name="adding-an-enterprise-application-for-single-sign-on"></a>Adicionar um pedido de empresa para um único sign-on

Para configurar um único sind on em Azure AD, você precisa adicionar uma nova aplicação da empresa. Na galeria, existe uma pré-configuração de aplicação pré-configurada para este, **SAML SSO para Jira por resolução GmbH** .

**Para adicionar SAML SSO para Jira por resolução GmbH da galeria, execute os seguintes passos:**

1. No **[portal Azure](https://portal.azure.com)** , no painel de navegação esquerdo, clique no ícone **Azure Ative Directory.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, clique em **Todas as Aplicações** .

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique no botão **de aplicação Novo** no topo do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **SAML SSO para Jira por resolução GmbH,** selecione **SAML SSO para Jira por resolução GmbH** do painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar a aplicação. Também pode alterar o nome da aplicação da empresa.

     ![SAML SSO para Jira por resolução GmbH na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-single-sign-on-with-the-saml-sso-plugin-and-azure-ad"></a>Configurar e testar um único sinal com o plugin SSO SAML e a AD AZure

Nesta secção, irá testar e configurar um único sinal de inscrição para jira para um utilizador AD Azure. Isto será feito para um utilizador de teste chamado **Britta Simon** .
Para um único sinal de trabalho, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado na SAML SSO para a Jira por resolução.

Para configurar e testar um único sinal, é necessário completar os seguintes passos:

1. **[Configure o pedido de empresa Azure AD para um único sign-on](#configure-the-azure-ad-enterprise-application-for-single-sign-on)** - Configure o pedido de empresa Azure AD para o único sign-on
2. **[Configure o plugin SAML SSO do seu exemplo Jira](#configure-the-saml-sso-plugin-of-your-jira-instance)** - Configure as definições de Sign-On única no lado da aplicação.
3. Criar um utilizador de **[teste AZure AD](#create-an-azure-ad-test-user)** - Criar um utilizador de teste em Azure AD.
1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - Ativando o utilizador de teste para utilizar o único sinal no lado Azure.
1. **[Criar o utilizador de teste em Jira](#create-the-test-user-also-in-jira)** - Criar um utilizador de teste de contraparte em Jira para o utilizador de teste Azure.
1. **[Teste um único sinal -](#test-single-sign-on)** Verifique se a configuração funciona.

### <a name="configure-the-azure-ad-enterprise-application-for-single-sign-on"></a>Configure o pedido de empresa Azure AD para um único sign-on

Nesta secção, configura-se o único sinal de inscrição no portal Azure.

Para configurar o único sign-on com a SAML SSO para a Jira por resolução GmbH, execute os seguintes passos:

1. No [portal Azure,](https://portal.azure.com/)no **sSO SAML** acabado de ser criado para Jira por resolução a aplicação da empresa GmbH, selecione **Single sign-on** no painel esquerdo.

    ![Configurar link único de inscrição](common/select-sso.png)

2. Para **Selecionar um único método de inscrição,** selecione o modo **SAML** para ativar uma única s ativação.

    ![Único modo de seleção de s-on](common/select-saml-option.png)

3. Em seguida, clique no ícone **Editar** para abrir o diálogo **de configuração SAML básico.**

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **Configuração Básica SAML,** se pretender configurar a aplicação no modo iniciado pelo **IDP,** efetue os seguintes passos:

    ![Screenshot que mostra a secção "Configuração Básica S A M L" com as caixas de texto "Identifier" e "Answer U R L" realçadas e o botão "Save" selecionado.](common/idp-intiated.png)

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão: `https://<server-base-url>/plugins/servlet/samlsso`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://<server-base-url>/plugins/servlet/samlsso`

    c. Clique **em Definir URLs adicionais** e execute o seguinte passo, se desejar configurar a aplicação no modo **iniciado** SP:

    ![SAML SSO para Jira por resolução GmbH Domain e URLs informações únicas de súmito](common/metadata-upload-additional-signon.png)

    Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão:  `https://<server-base-url>/plugins/servlet/samlsso`

    > [!NOTE]
    > Para o Identificador, URL de resposta e URL de inscrição, substitua **\<server-base-url>** o URL base da sua instância Jira. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure. Se tiver algum problema, contacte-nos na [SAML SSO para a Jira através da resolução da equipa de apoio ao cliente da GmbH.](https://www.resolution.de/go/support)

4. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** faça o download do **Metadados XML da Federação** e guarde-o para o seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

### <a name="configure-the-saml-sso-plugin-of-your-jira-instance"></a>Configure o plugin SSO SAML da sua instância Jira 

1. Numa janela diferente do navegador web, inscreva-se na sua instância Jira como administrador.

2. Passe por cima da engrenagem no lado direito e clique em **Gerir aplicações** .
    
    ![Screenshot que mostra uma seta apontando para o ícone "Cog" e "Gerir aplicações" selecionadas a partir do drop-down.](./media/samlssojira-tutorial/addon1.png)

3. Se for redirecionado para a página De Acesso ao Administrador, insira a **Palavra-passe** e clique no botão **Confirmar.**

    ![Screenshot que mostra a página "Acesso ao Administrador".](./media/samlssojira-tutorial/addon2.png)

4. Jira normalmente redireciona-o para o mercado atlassiano. Caso contrário, clique em **Localizar novas aplicações** no painel esquerdo. Procure por **SAML Single Sign On (SSO) para JIRA** e clique no botão **Instalar** para instalar o plugin SAML.

    ![Screenshot que mostra a página "Atlassian Marketplace for JIRA" com uma seta apontando para o botão "Instalar" para a aplicação "S A M L Single Sign On (S O) Jira, S A M L/S O".](./media/samlssojira-tutorial/store.png)

5. A instalação plugin irá arrancar. Quando estiver pronto, clique no botão **Fechar.**

    ![Screenshot que mostra o diálogo "Instalar".](./media/samlssojira-tutorial/store-2.png)

    ![Screenshot que mostra o "Instalado e pronto para ir!" diálogo com o botão "Fechar" selecionado.](./media/samlssojira-tutorial/store-3.png)

6. Em seguida, clique em **Gerir** .

    ![Screenshot que mostra a aplicação "S A M L Single Sign On (S O) Jira, S A M L/S S O" com o botão "Gerir" selecionado.](./media/samlssojira-tutorial/store-4.png)
    
8. Em seguida, clique em **Configurar** para configurar o plugin acabado de instalar.

    ![Screenshot que mostra a página "Gerir apps", com o botão "Configurar" selecionado para a aplicação "S A M L SingleSignOn for Jira".](./media/samlssojira-tutorial/store-5.png)

9. No assistente **de configuração do plugin singleSignOn SAML,** clique em **Adicionar novo IdP** para configurar a Azure AD como um novo Fornecedor de Identidade.

    ![A screenshot mostra a página "Welcome", com o botão "Adicionar novo I d P" selecionado.](./media/samlssojira-tutorial/addon4.png) 

10. Na página Escolha a sua página **de Fornecedor de Identidade SAML,** execute os seguintes passos:

    ![Screenshot que mostra a página "Escolha o seu Fornecedor de Identidade S A M L" com as caixas de texto "I d P Type" e "Name" destacadas, e o botão "Seguinte" selecionado.](./media/samlssojira-tutorial/addon5a.png)
 
    a. Desagrafe **o AD AD** como o tipo IdP.
    
    b. Adicione o **nome** do Fornecedor de Identidade (por exemplo, Azure AD).
    
    c. Adicione uma **descrição** (opcional) do Fornecedor de Identidade (por exemplo, Azure AD).
    
    d. Clique em **Seguinte** .
    
11. Na página **de configuração do fornecedor de identidade,** clique em **Seguinte** .
 
    ![Screenshot que mostra a página "Configuração do fornecedor de identidade".](./media/samlssojira-tutorial/addon5b.png)

12. Na página **de Metadados IdP de Importação SAML,** execute os seguintes passos:

    ![Screenshot que mostra a página "Import S A M L I d P Metadados" com a ação "Select Metadata X M L File" selecionada.](./media/samlssojira-tutorial/addon5c.png)

    a. Clique no botão **De ficheiro Select Metadas XML** e escolha o ficheiro **Federação de Metadados XML** que descarregou anteriormente.

    b. Clique no botão **Importar.**
     
    c. Aguarde brevemente até que a importação tenha sucesso.  
     
    d. Clique no botão **Seguinte.**
    
13. Na página de id do utilizador e na página **de transformação,** clique no botão **Seguinte.**

    ![Screenshot que mostra a página "Atributo e transformação do utilizador I D" com o botão "Seguinte" selecionado.](./media/samlssojira-tutorial/addon5d.png)
    
14. Na página de **criação e atualização** do Utilizador, clique em **Guardar & Seguinte** para guardar as definições.
    
    ![Screenshot que mostra a página "Criação e atualização do utilizador" com o botão "Save & Next" selecionado.](./media/samlssojira-tutorial/addon6a.png)
    
15. Na página **Test your settings,** clique em **"Saltar & configurar manualmente** para saltar o teste do utilizador por enquanto. Isto será realizado na secção seguinte e requer algumas configurações no portal Azure.
    
    ![Screenshot que mostra a página "Testar as suas definições" com o botão "Saltar teste & configurar manualmente" selecionado.](./media/samlssojira-tutorial/addon6b.png)
    
16. Clique **em OK** para saltar o aviso.
    
    ![Screenshot que mostra o diálogo de aviso com o botão "O K" selecionado.](./media/samlssojira-tutorial/addon6c.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD

O objetivo desta secção é criar um utilizador de teste no portal Azure chamado Britta Simon. Com o utilizador, irá testar um único sinal de s-on.

1. No portal Azure, no painel esquerdo, selecione **Azure Ative Directory** , selecione **Utilizadores** , e, em seguida, selecione **Todos os utilizadores** .

    ![Os links "Utilizadores e grupos" e "Todos os utilizadores"](common/users.png)

2. Escolha **novo utilizador** na parte superior do ecrã.

    ![Novo botão de utilizador](common/new-user.png)

3. Nas propriedades do **Utilizador,** execute os seguintes passos:

    ![A caixa de diálogo do utilizador](common/user-properties.png)

    a. No campo **Nome,** insira **Britta Simon.**
  
    b. No campo **nome do utilizador,** insira <b>BrittaSimon@contoso.com</b> .

    c. Selecione a caixa **de verificação de palavra-passe Show** e, em seguida, anote o valor que é apresentado na caixa palavra-passe.

    d. Clique em **Criar** .

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, adicione Britta Simon à aplicação da empresa, que lhe permite usar um único sign-on.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações** . 

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de candidaturas, procure a aplicação da empresa que criou no início deste tutorial. Se está a seguir os passos do tutorial, chama-se **SAML SSO para Jira por resolução GmbH.** Se lhe deu outro nome, procure por esse nome.

    ![O SSO SAML para Jira por resolução Ligação GmbH na lista de Aplicações](common/all-applications.png)

3. No painel esquerdo, clique em **Utilizadores e grupos.**

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. **Selecione Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição de adição](common/add-assign-user.png)

5. No diálogo **de Utilizadores e grupos,** selecione **Britta Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de algum valor de função na afirmação DE SAML, em seguida, no diálogo **'Fun',** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.

7. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

### <a name="create-the-test-user-also-in-jira"></a>Crie o utilizador de teste também em Jira

Para permitir que os utilizadores da Azure AD inscrevam-se na SAML SSO para a Jira por resolução GmbH, devem ser alojadas na SAML SSO para a Jira por resolução GmbH. Para o caso deste tutorial, você tem que fazer o provisionamento à mão. No entanto, existem também outros modelos de provisionamento disponíveis para o plugin SSO SAML por resolução, por exemplo, o provisionamento **just in time.** Consulte a sua documentação na [SAML SSO por resolução GmbH](https://wiki.resolution.de/doc/saml-sso/latest/all). Se tiver alguma dúvida sobre o mesmo, contacte o suporte de [resolução](https://www.resolution.de/go/support).

**Para obter manualmente uma conta de utilizador, execute os seguintes passos:**

1. Inscreva-se na instância Jira como administradora.

2. Passe por cima da engrenagem e selecione **a gestão do utilizador.**

   ![Screenshot que mostra uma seta apontando para o ícone "Cog" com "Gestão do utilizador" selecionada a partir do drop-down.](./media/samlssojira-tutorial/user1.png)

3. Se for redirecionado para a página 'Acesso ao Administrador', insira a **Palavra-passe** e clique no botão **Confirmar.**

    ![Screenshot que mostra a página "Acesso ao Administrador" com a caixa de texto "Password" realçada.](./media/samlssojira-tutorial/user2.png) 

4. Na secção de **separador de gestão do Utilizador,** clique em **criar utilizador.**

    ![Screenshot que mostra o separador "Gestão do Utilizador" com o botão "Criar utilizador" selecionado.](./media/samlssojira-tutorial/user3-new.png) 

5. Na página de diálogo **"Criar novo utilizador",** execute os seguintes passos. Tem de criar o utilizador exatamente como no Azure AD:

    ![Adicionar Empregado](./media/samlssojira-tutorial/user4-new.png) 

    a. Na caixa de texto **do endereço de e-mail,** digite o endereço de e-mail do utilizador:  <b>BrittaSimon@contoso.com</b> .

    b. Na caixa de texto **'Nome Completo',** escreva o nome completo do utilizador: **Britta Simon** .

    c. Na caixa de texto do nome de **utilizador,** digite o endereço de e-mail do utilizador: <b>BrittaSimon@contoso.com</b> . 

    d. Na caixa de texto **da palavra-passe,** introduza a palavra-passe do utilizador.

    e. Clique **em Criar utilizador** para terminar a criação do utilizador.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no SSO SAML para Jira por resolução, o azulejo gmbH no Painel de Acesso, deverá ser automaticamente inscrito no SSO SAML para Jira por resolução gmbH para a qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

Também pode testar um único sinal de s-on, se navegar para `https://<server-base-url>/plugins/servlet/samlsso` . Substitua **\<server-base-url>** o URL base da sua instância Jira.


## <a name="enable-single-sign-on-redirection-for-jira"></a>Ativar uma única reorientação de sinal para Jira

Como indicado na secção anterior, existem atualmente duas formas de ativar a única inscrição. Quer utilizando o **portal Azure,** quer utilizando **um link especial para a sua instância Jira** . O plugin SSO SAML por resolução Também permite ativar um único sinal de acesso **simplesmente a qualquer URL que indique a sua instância Jira** .

No essencial, todos os utilizadores que acedam a Jira serão redirecionados para o único sinal após ativar uma opção no plugin.

Para ativar o redirecionamento SSO, faça o seguinte no **seu exemplo Jira** :

1. Aceda à página de configuração do plugin SSO SAML em Jira.
1. Clique em **Redirecionamento** no painel esquerdo.

   ![Screenshot parcial da página de configuração do plugin singleSignOn Jira SAML realçando a ligação de redirecionamento na navegação esquerda.](./media/samlssojira-tutorial/ssore1.png)

1. Córique **Ativar o redirecionamento SSO** .

   ![Screenshot parcial da página de configuração do plugin singleSignOn Jira SAML destacando a caixa de verificação selecionada "Enable SSO Redirect".](./media/samlssojira-tutorial/ssore2.png) 

1. Prima o botão **'Guardar definições'** no canto superior direito.

Depois de ativar a opção, ainda pode chegar ao nome de utilizador/senha se a opção **Enable nosso** for assinalada navegando para `https://\<server-base-url>/login.jsp?nosso` . Como sempre, substitua **\<server-base-url>** o URL base.


## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](../conditional-access/overview.md)