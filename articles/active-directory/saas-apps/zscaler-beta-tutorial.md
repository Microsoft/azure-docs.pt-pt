---
title: 'Tutorial: Integração do Azure Ative Directory com a Zscaler Beta Microsoft Docs'
description: Saiba como configurar um único sinal entre o Azure Ative Directory e o Zscaler Beta.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/24/2019
ms.author: jeedes
ms.openlocfilehash: f342371ec065c4fb60c492c4354c6f8c717c9bb8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88546029"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-beta"></a>Tutorial: Integração do Azure Ative Directory com zscaler Beta

Neste tutorial, aprende-se a integrar a Zscaler Beta com o Azure Ative Directory (Azure AD).
Quando integrar a Zscaler Beta com AZure AD, pode:

* Controle em Azure AD que tem acesso a Zscaler Beta.
* Permitir que os seus utilizadores sejam automaticamente inscritos na Zscaler Beta com as suas contas AD Azure. Este controlo de acesso chama-se único sinal de acesso (SSO).
* Gerencie as suas contas numa localização central utilizando o portal Azure.

Para obter mais informações sobre software como integração de aplicações de serviço (SaaS) com Azure AD, consulte [o que é o acesso à aplicação e o único acesso ao Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AZure AD com a Zscaler Beta, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Uma subscrição Zscaler Beta que utiliza um único sinal de acesso.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* A Zscaler Beta suporta sSO iniciado pelo SP.
* A Zscaler Beta suporta o fornecimento de utilizadores just-in-time.

## <a name="add-zscaler-beta-from-the-azure-marketplace"></a>Adicione Zscaler Beta do Azure Marketplace

Para configurar a integração da Zscaler Beta no AD AZure, adicione zscaler Beta do Azure Marketplace à sua lista de aplicações geridas saaS.

Para adicionar Zscaler Beta do Azure Marketplace, siga estes passos.

1. No [portal Azure,](https://portal.azure.com)no painel de navegação à esquerda, selecione **Azure Ative Directory**.

    ![Botão de diretório ativo Azure](common/select-azuread.png)

2. Vá às **aplicações da Enterprise**e, em seguida, selecione **Todas as aplicações**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar uma nova aplicação, selecione **Nova aplicação** na parte superior da caixa de diálogo.

    ![Novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, **insira Zscaler Beta**. Selecione **Zscaler Beta** do painel de resultados e, em seguida, selecione **Adicionar**.

     ![Zscaler Beta na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, configura e testa o Azure AD com Zscaler Beta com base no utilizador de teste Britta Simon.
Para um único sinal de sação a funcionar, estabeleça uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado em Zscaler Beta.

Para configurar e testar o Azure AD com zscaler Beta, complete os seguintes blocos de construção:

- [Configurar o Azure AD um único sinal para](#configure-azure-ad-single-sign-on) permitir que os seus utilizadores utilizem esta funcionalidade.
- [Configurar o sinal único de Zscaler Beta](#configure-zscaler-beta-single-sign-on) para configurar as definições de inscrição única no lado da aplicação.
- [Crie um utilizador de teste AZure AD](#create-an-azure-ad-test-user) para testar o Azure AD com Britta Simon.
- [Atribua ao utilizador de teste AZure AD](#assign-the-azure-ad-test-user) para permitir que a Britta Simon utilize um único sinal de Ad AD.
- [Crie um utilizador de teste Beta Zscaler](#create-a-zscaler-beta-test-user) para ter uma contraparte de Britta Simon em Zscaler Beta que está ligada à representação AD AZure do utilizador.
- [Teste um único sinal para](#test-single-sign-on) verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, você ativa a Azure AD um único sinal no portal Azure.

Para configurar o Azure AD com Zscaler Beta, siga estes passos.

1. No [portal Azure](https://portal.azure.com/), na página de integração da aplicação **Zscaler Beta,** selecione **Single sign-on**.

    ![Configurar link único de inscrição](common/select-sso.png)

2. Na caixa de diálogo **de método de inscrição única,** selecione o modo **SAML/WS-Fed** para ativar um único sinal de entrada.

    ![Único modo de seleção de s-on](common/select-saml-option.png)

3. Na **configuração single Sign-On com página SAML,** selecione **Editar** para abrir a caixa de diálogo **de configuração DE SAML básica.**

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **De Configuração Básica SAML,** siga este passo:

    ![Zscaler Beta domínio e URLs informações únicas de súmis](common/sp-intiated.png)

    - Na caixa url do Signo na caixa **DE URL,** insira o URL utilizado pelos seus utilizadores para iniciar sing à sua aplicação Zscaler Beta.

    > [!NOTE]
    > O valor não é real. Atualizar o valor com o sinal real no valor URL. Para obter o valor, contacte a [equipa de suporte ao cliente Zscaler Beta.](https://www.zscaler.com/company/contact)

5. A aplicação Zscaler Beta espera as afirmações SAML num formato específico. Tem de adicionar mapeamentos de atributos personalizados à configuração de atributos de ficha SAML. A imagem que se segue mostra a lista de atributos predefinidos. Selecione **Editar** para abrir a caixa de diálogo **'Atributos do Utilizador'.**

    ![Caixa de diálogo de atributos do utilizador](common/edit-attribute.png)

6. A aplicação Zscaler Beta espera que mais alguns atributos sejam repercutidos na resposta DAL. Na secção **de reclamações** do Utilizador na caixa de diálogo Atributos do **Utilizador,** siga estes passos para adicionar o atributoken SAML, como mostrado na tabela seguinte.
    
    | Nome | Atributo de origem | 
    | ---------------| --------------- |
    | membroOf  | user.assignedroles |

    a. **Selecione Adicione nova reclamação** para abrir a caixa de diálogo **de reclamações** do utilizador Gerir.

    ![Caixa de diálogo de reclamações do utilizador](common/new-save-attribute.png)

    ![Gerir a caixa de diálogo de reclamações do utilizador](common/new-attribute-details.png)

    b. Na caixa **Nome,** insira o nome de atributo mostrado para esta linha.

    c. Deixe a caixa **Namespace** em branco.

    d. Para **Fonte**, selecione **Atributo**.

    e. A partir da lista **de atributos Fonte,** insira o valor de atributo mostrado para esta linha.

    f. Selecione **OK**.

    exemplo, Selecione **Guardar**.

    > [!NOTE]
    > Para aprender a configurar papéis em Azure AD, consulte [Configure a reivindicação do papel](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-app-role-management).

7. Na **configuração single Sign-On com página SAML,** na secção Certificado de Assinatura **SAML,** selecione **Descarregamento** para descarregar o **Certificado (Base64)**. Guarde no seu computador.

    ![Link de descarregamento de certificado](common/certificatebase64.png)

8. Na secção **Beta configurar Zscaler,** copie os URLs de que necessita para os seus requisitos:

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    - URL de Inicio de Sessão
    - Identificador de Azure Ad
    - Logout URL

### <a name="configure-zscaler-beta-single-sign-on"></a>Configurar zscaler beta single sign-on

1. Para automatizar a configuração dentro da Zscaler Beta, instale **a extensão do navegador 'As aplicações' Secure Sign-in** selecionando **instalar a extensão**.

    ![Extensão das minhas apps](common/install-myappssecure-extension.png)

2. Depois de adicionar a extensão ao navegador, selecionar **Configurar A Versão Zscaler** direciona-o para a aplicação Zscaler Beta. A partir daí, forneça as credenciais de administração para iniciar snus na Zscaler Beta. A extensão do navegador configura automaticamente a aplicação para si e automatiza os passos 3 a 6.

    ![Configuração de configuração](common/setup-sso.png)

3. Para configurar o Zscaler Beta manualmente, abra uma nova janela do navegador web. Inscreva-se no site da sua empresa Zscaler Beta como administrador e siga estes passos.

4. Aceda **Administration**às Definições de  >  **Autenticação de**  >  **Autenticação**administrativa e siga estes passos.
   
    ![Administration](./media/zscaler-beta-tutorial/ic800206.png "Administração")

    a. No **tipo de autenticação,** selecione **SAML**.

    b. Selecione **Configurar SAML**.

5. Na janela **EditAR SAML,** siga estes passos: 
            
    ![Gerir utilizadores & autenticação](./media/zscaler-beta-tutorial/ic800208.png "Gerir utilizadores & autenticação")
    
    a. Na caixa URL do **Portal SAML,** cole no **URL de Login** que copiou do portal Azure.

    b. Na caixa atributo **'Atributo's Login,** insira **o NameID**.

    c. Na caixa **de Certificado SSL Público,** selecione **Upload** para carregar o certificado de assinatura Azure SAML que descarregou a partir do portal Azure.

    d. **Alternar Ativar o fornecimento automático da SAML**.

    e. Na caixa atributo do nome do **utilizador,** **introduza o nome do ecrã** Se pretender ativar a autoprovisão DA SAML para os atributos do nome de visualização.

    f. Na caixa atributo Atributo Nome de **Grupo,** introduza **o membroOf** se pretender ativar a autoprovisão DA SAML para os atributos dos membros.

    exemplo, Na caixa **atributo De Nome do Departamento,** **insira o departamento** se quiser ativar a autoprovisionação DA SAML para atributos do departamento.

    h. Selecione **Guardar**.

6. Na página de diálogo de autenticação do **utilizador configurar,** siga estes passos:

    ![Menu de ativação e botão Ativar](./media/zscaler-beta-tutorial/ic800207.png)

    a. Passe por cima do menu **de ativação** na parte inferior esquerda.

    b. **Selecione Ativar**.

## <a name="configure-proxy-settings"></a>Configurar definições de proxy
Para configurar as definições de procuração no Internet Explorer, siga estes passos.

1. Iniciar **o Internet Explorer**.

2. Selecione **as opções** de Internet do menu **Ferramentas** para abrir a caixa de diálogo **opções de Internet.** 
    
     ![Caixa de diálogo de opções de internet](./media/zscaler-beta-tutorial/ic769492.png "Opções de Internet")

3. Selecione o separador **Ligações.** 
  
     ![Separador Ligações](./media/zscaler-beta-tutorial/ic769493.png "Ligações")

4. Selecione **as definições DE LAN** para abrir a caixa de diálogo de **definições da Rede local de Área (LAN).**

5. Na secção do **servidor Proxy,** siga estes passos: 
   
    ![Secção de servidor proxy](./media/zscaler-beta-tutorial/ic769494.png "Servidor proxy")

    a. Selecione o Servidor De procuração para a sua caixa **de verificação LAN.**

    b. Na caixa **de endereços,** insira **o gateway. Zscaler Beta.net.**

    c. Na caixa do **Porto,** insira **80**.

    d. Selecione o servidor de procuração bypass para a caixa de verificação **de endereços locais.**

    e. Selecione **OK** para fechar a caixa de diálogo **de definições da Rede local (LAN).**

6. Selecione **OK** para fechar a caixa de diálogo **opções de Internet.**

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD 

Crie um utilizador de teste no portal Azure chamado Britta Simon.

1. No portal Azure, no painel esquerdo, selecione **Utilizadores do Diretório Ativo Azure**  >  **Users**  >  **Todos os utilizadores**.

    ![Utilizadores e todos os links dos utilizadores](common/users.png)

2. Selecione **Novo utilizador** na parte superior do ecrã.

    ![Novo botão de utilizador](common/new-user.png)

3. Na caixa de diálogo **do Utilizador,** siga estes passos:

    ![Caixa de diálogo do utilizador](common/user-properties.png)

    a. Na caixa **Nome,** **insira BrittaSimon**.
  
    b. Na caixa **Nome de utilizador**, introduza `brittasimon@yourcompanydomain.extension`. Um exemplo é BrittaSimon@contoso.com.

    c. Selecione a caixa **de verificação de senha show.** Anota o valor que exibe na caixa **de palavra-passe.**

    d. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Permita que Britta Simon utilize o Azure single sign-on, concedendo acesso à Zscaler Beta.

1. No portal Azure, selecione **aplicações Enterprise**  >  **Todas as aplicações**  >  **Zscaler Beta**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de candidaturas, insira e selecione **Zscaler Beta**.

    ![Ligação Beta Zscaler na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![Ligação de utilizadores e grupos](common/users-groups-blade.png)

4. Selecione **Adicionar utilizador**. Na caixa de diálogo **de atribuição de** adicionar, selecione **Utilizadores e grupos**.

    ![Adicione botão de utilizador](common/add-assign-user.png)

5. Na caixa de diálogo **de Utilizadores e grupos,** selecione o utilizador como **Britta Simon** da lista. Em seguida, escolha **Selecione** na parte inferior do ecrã.

    ![Caixa de diálogo de utilizadores e grupos](./media/zscaler-beta-tutorial/tutorial_zscalerbeta_users.png)

6. Na caixa de diálogo **Select Role,** selecione a função de utilizador apropriada na lista. Em seguida, escolha **Selecione** na parte inferior do ecrã.

    ![Selecione caixa de diálogo de função](./media/zscaler-beta-tutorial/tutorial_zscalerbeta_roles.png)

7. Na caixa de diálogo **'Adicionar Atribuição',** selecione **Atribuir**.

    ![Adicionar caixa de diálogo de atribuição](./media/zscaler-beta-tutorial/tutorial_zscalerbeta_assign.png)

### <a name="create-a-zscaler-beta-test-user"></a>Criar um utilizador de teste Beta Zscaler

Nesta secção, a utilizadora Britta Simon é criada em Zscaler Beta. A Zscaler Beta suporta **o fornecimento de utilizadores just-in-time**, o que é ativado por padrão. Não há nada que possa fazer nesta secção. Se um utilizador já não existir no Zscaler Beta, um novo é criado após a autenticação.

>[!Note]
>Para criar um utilizador manualmente, contacte a [equipa de suporte Zscaler Beta](https://www.zscaler.com/company/contact).

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Teste a sua configuração de sinal único AD AD através do Painel de Acesso.

Quando selecionar o azulejo Beta Zscaler no Painel de Acesso, deverá ser automaticamente inscrito na Versão Zscaler para a qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)
- [O que é Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

