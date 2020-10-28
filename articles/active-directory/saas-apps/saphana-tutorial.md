---
title: 'Tutorial: Integração do Azure Ative Directory com a SAP HANA Microsoft Docs'
description: Saiba como configurar um único sinal entre o Azure Ative Directory e o SAP HANA.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/27/2018
ms.author: jeedes
ms.openlocfilehash: 748566cf5b25157fcb8a2938a3f4bd307c930874
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92895168"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-hana"></a>Tutorial: Integração do Diretório Ativo Azure com SAP HANA

Neste tutorial, aprende-se a integrar o SAP HANA com o Azure Ative Directory (Azure AD).
A integração da SAP HANA com a Azure AD proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso a SAP HANA.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no SAP HANA (Sign-On Único) com as suas contas AD Azure.
* Pode gerir as suas contas numa localização central - o portal Azure.

Se quiser saber mais detalhes sobre a integração da aplicação SaaS com o Azure AD, consulte o que é o acesso à [aplicação e o único acesso ao Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AD da Azure com o SAP HANA, precisa dos seguintes itens:

- Uma subscrição do Azure
- Uma subscrição SAP HANA que é um único sign-on (SSO) ativado
- Um caso HANA que está a decorrer em qualquer IaaS público, no local, Azure VM, ou SAP grandes instâncias em Azure
- A interface web da Administração XSA, bem como o HANA Studio instalado na instância HANA

> [!NOTE]
> Não recomendamos a utilização de um ambiente de produção da SAP HANA para testar os passos neste tutorial. Teste a integração primeiro no ambiente de desenvolvimento ou encenação da aplicação e, em seguida, utilize o ambiente de produção.

Para testar os passos neste tutorial, siga estas recomendações:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura ativada única SAP HANA

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* SAP HANA apoia **IDP** iniciado SSO
* SAP HANA suporta provisão de utilizadores **just-in-time**

## <a name="adding-sap-hana-from-the-gallery"></a>Adicionando SAP HANA da galeria

Para configurar a integração do SAP HANA em Azure AD, precisa adicionar o SAP HANA da galeria à sua lista de aplicações geridas pelo SaaS.

**Para adicionar SAP HANA da galeria, execute os seguintes passos:**

1. No **[portal Azure](https://portal.azure.com)** , no painel de navegação esquerdo, clique no ícone **Azure Ative Directory.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em Novo botão de **aplicação** no topo do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, **escreva SAP HANA** , selecione **SAP HANA** do painel de resultados e clique em Adicionar o botão **Adicionar** a aplicação.

     ![SAP HANA na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, configura e testa o Azure AD com um único sign-on com SAP HANA baseado num utilizador de teste chamado **Britta Simon** .
Para um único sinal de trabalho, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado no SAP HANA.

Para configurar e testar o Azure AD com um único sinal de acesso com o SAP HANA, é necessário completar os seguintes blocos de construção:

1. **[Configure Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o auto-sign-on SAP HANA](#configure-sap-hana-single-sign-on)** - para configurar as definições de Sign-On únicas no lado da aplicação.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
5. **[Create SAP HANA test user](#create-sap-hana-test-user)** - para ter uma contraparte de Britta Simon em SAP HANA que está ligada à representação AZure AD do utilizador.
6. **[Teste um único sinal](#test-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, você ativa a Azure AD um único sinal no portal Azure.

Para configurar o Azure AD com o SAP HANA, execute os seguintes passos:

1. No [portal Azure](https://portal.azure.com/), na página de integração da aplicação **SAP HANA,** selecione **Single sign-on** .

    ![Configurar link único de inscrição](common/select-sso.png)

2. No diálogo do **método de inscrição única,** selecione o modo **SAML/WS-Fed** para ativar um único sinal de súplica.

    ![Único modo de seleção de s-on](common/select-saml-option.png)

3. Na **configuração single Sign-On com página SAML,** clique em **Editar** o ícone para abrir o diálogo **básico de configuração SAML.**

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na **configuração single Sign-On com página SAML,** execute os seguintes passos:

    ![Informações únicas de súmula de base de HANA e URLs](common/idp-intiated.png)

    a. Na caixa de texto **do identificador,** digite o seguinte: `HA100`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://<Customer-SAP-instance-url>/sap/hana/xs/saml/login.xscfunc`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de identificação e resposta real. Contacte [a equipa de suporte do cliente SAP HANA](https://cloudplatform.sap.com/contact.html) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

5. A aplicação SAP HANA espera as afirmações DOL num formato específico. Configure os seguintes pedidos para este pedido. Pode gerir os valores destes atributos a partir da secção **Atributos** do Utilizador na página de integração de aplicações. Na **configuração single Sign-On com** a página SAML, clique no botão **Editar** para abrir o diálogo **de Atributos do Utilizador.**

    ![Screenshot que mostra a secção "Atributos do utilizador" com o ícone "Editar" selecionado.](common/edit-attribute.png)

6. Na secção **de atributos** do Utilizador no diálogo **"Atributos & Reclamações** do Utilizador", execute os seguintes passos:
 
    a. Clique **em Editar o ícone** para abrir o diálogo de reclamações do utilizador **Gerir.**

    ![Screenshot que mostra o diálogo "Atributos do Utilizador & Reclamações" com o ícone "Editar" selecionado.](./media/saphana-tutorial/tutorial_usermail.png)

    ![image](./media/saphana-tutorial/tutorial_usermailedit.png)

    b. Na lista **de Transformação,** selecione **ExtractMailPrefix()** .

    c. Na lista **do Parâmetro 1,** selecione **user.mail** .

    d. Clique em **Guardar** .

7. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Metadadata XML** da Federação a partir das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

### <a name="configure-sap-hana-single-sign-on"></a>Configurar SAP HANA Single Sign-On

1. Para configurar um único sinal no lado SAP HANA, inscreva-se na sua **Consola Web HANA XSA**  indo para o respetivo ponto final HTTPS.

    > [!NOTE]
    > Na configuração padrão, o URL redireciona o pedido para um ecrã de inscrição, que requer as credenciais de um utilizador autenticado da base de dados SAP HANA. O utilizador que se inscreve deve ter permissões para executar tarefas de administração DAL.

2. Na Interface Web XSA, aceda ao **Fornecedor de Identidade SAML.** A partir daí, selecione o **+** botão na parte inferior do ecrã para visualizar o painel **de Informação do Fornecedor de Identidade Adicionar.** Em seguida, tome os seguintes passos:

    ![Adicionar Fornecedor de Identidade](./media/saphana-tutorial/sap1.png)

    a. No painel **De Informação do Fornecedor de Identidade Adicionar,** cole o conteúdo do Metadadata XML (que descarregou a partir do portal Azure) na caixa **de metadados.**

    ![Screenshot que mostra o painel "Adicionar Informação do Fornecedor de Identidade" com as caixas "Metadados" e "Nome" em destaque.](./media/saphana-tutorial/sap2.png)

    b. Se o conteúdo do documento XML for válido, o processo de análise extrai as informações necessárias para os campos **Assunto, ID da Entidade e Emitente** na área geral do ecrã de **dados.** Também extrai a informação necessária para os campos de URL na área do ecrã **destino,** por exemplo, os campos **DE URL base e URL singleSignOn (*).**

    ![Adicionar definições de Fornecedor de Identidade](./media/saphana-tutorial/sap3.png)

    c. Na caixa **Nome** da área de ecrã **de Dados Gerais,** insira um nome para o novo fornecedor de identidade SSO SAML.

    > [!NOTE]
    > O nome do IDP SAML é obrigatório e deve ser único. Aparece na lista de IDPs SAML disponíveis que é apresentada quando seleciona o SAML como o método de autenticação para aplicações SAP HANA XS a utilizar. Por exemplo, pode fazê-lo na área do ecrã de **autenticação** da ferramenta Administração de Artefactos XS.

3. **Selecione Guardar** para guardar os detalhes do fornecedor de identidade SAML e adicionar o novo IDP SAML à lista de IDPs SAML conhecidos.

    ![Botão Guardar](./media/saphana-tutorial/sap4.png)

4. No Estúdio HANA, dentro das propriedades do sistema do **separador Configuração,** filtrar as definições por **saml** . Em seguida, ajuste o **assertion_timeout** de **10 segundos** para **120 segundos** .

    ![assertion_timeout definição](./media/saphana-tutorial/sap7.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD 

O objetivo desta secção é criar um utilizador de teste no portal Azure chamado Britta Simon.

1. No portal Azure, no painel esquerdo, selecione **Azure Ative Directory** , selecione **Utilizadores** , e, em seguida, selecione **Todos os utilizadores** .

    ![Os links "Utilizadores e grupos" e "Todos os utilizadores"](common/users.png)

2. Selecione **Novo utilizador** na parte superior do ecrã.

    ![Novo botão de utilizador](common/new-user.png)

3. Nas propriedades do Utilizador, execute os seguintes passos.

    ![A caixa de diálogo do utilizador](common/user-properties.png)

    a. No campo **Nome** entra **BrittaSimon** .
  
    b. No tipo de campo **nome de utilizador** **brittasimon \@ yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione Mostrar caixa de verificação de **palavra-passe** e, em seguida, anotar o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar** .

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permite que Britta Simon use Azure single sign-on, concedendo acesso a SAP HANA.

1. No portal Azure, selecione **Aplicações empresariais** , selecione **Todas as aplicações,** em seguida, selecione **SAP HANA** .

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de candidaturas, escreva e selecione **SAP HANA** .

    ![O link SAP HANA na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos** .

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição de adição](common/add-assign-user.png)

5. No diálogo **de Utilizadores e grupos** selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de qualquer valor de função na afirmação SAML, então no diálogo **'Fun's Select** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.

7. No diálogo **'Adicionar Atribuição'** clique no botão **'Atribuir'.**

### <a name="create-sap-hana-test-user"></a>Criar utilizador de teste SAP HANA

Para permitir que os utilizadores da Azure AD entrem no SAP HANA, deve forerá-los em SAP HANA.
O SAP HANA suporta **o provisionamento just-in-time** , que é ativado por padrão.

Se precisar de criar um utilizador manualmente, tome os seguintes passos:

>[!NOTE]
>Pode alterar a autenticação externa que o utilizador utiliza. Podem autenticar-se com um sistema externo como o Kerberos. Para obter informações detalhadas sobre identidades externas, contacte o seu [administrador de domínio.](https://cloudplatform.sap.com/contact.html)

1. Abra o [Estúdio SAP HANA](https://help.sap.com/viewer/a2a49126a5c546a9864aae22c05c3d0e/2.0.01/en-us) como administrador e, em seguida, ative o DB-User para SAML SSO.

    ![Criar utilizador](./media/saphana-tutorial/sap5.png)

2. Selecione a caixa de verificação invisível à esquerda da **SAML** e, em seguida, selecione o link **Configure.**

3. **Selecione Adicionar** para adicionar o IDP SAML.  Selecione o IDP SAML apropriado e, em seguida, selecione **OK** .

4. Adicione a **Identidade Externa** (neste caso, BrittaSimon) ou escolha **Qualquer** . Em seguida, selecione **OK** .

   > [!Note]
   > Se a caixa de verificação **Any** não for selecionada, então o nome de utilizador em HANA precisa de corresponder exatamente o nome do utilizador na UPN antes do sufixo de domínio. (Por exemplo, BrittaSimon@contoso.com torna-se BrittaSimon em HANA.)

5. Para efeitos de teste, atribua todas as funções **XS** ao utilizador.

    ![Atribuição de funções](./media/saphana-tutorial/sap6.png)

    > [!TIP]
    > Deve dar permissões que sejam adequadas apenas para os seus casos de utilização.

6. Salve o utilizador.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo SAP HANA no Painel de Acesso, deverá ser automaticamente inscrito no SAP HANA para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](../conditional-access/overview.md)