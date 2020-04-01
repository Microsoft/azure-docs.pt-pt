---
title: 'Tutorial: Integração do Diretório Ativo Azure com o Displayr [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Displayr.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: b739b4e3-1a37-4e3c-be89-c3945487f4c1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3cd1785595cf2f6b2401837780106f52fdc97e36
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "67103977"
---
# <a name="tutorial-integrate-displayr-with-azure-active-directory"></a>Tutorial: Integrar o Displayr com o Diretório Ativo Azure

Neste tutorial, aprenderá a integrar o Displayr com o Azure Ative Directory (Azure AD). Quando integrar o Displayr com o Azure AD, pode:

* Controlo em Azure AD que tem acesso ao Displayr.
* Ative que os seus utilizadores sejam automaticamente inscritos no Displayr com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Assinatura de inscrição única do displayr (SSO) ativada.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste. O displayr suporta **SP** iniciado SSO.

## <a name="adding-displayr-from-the-gallery"></a>Adicionando Displayr da galeria

Para configurar a integração do Displayr no Azure AD, precisa de adicionar o Displayr da galeria à sua lista de aplicações SaaS geridas.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No Add da secção **galeria,** digite **displayr** na caixa de pesquisa.
1. Selecione **Displayr** a partir do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Configure e teste Azure AD SSO com Displayr utilizando um utilizador de teste chamado **Britta Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no Displayr.

Para configurar e testar o Azure AD SSO com o Displayr, complete os seguintes blocos de construção:

1. **[Configure O SSO AD Azure](#configure-azure-ad-sso)** para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure](#configure-displayr)** o Displayr para configurar as definições SSO no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** para testar o único sign-on da Azure AD com britta Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** para permitir que Britta Simon utilize um único sinal de AD Azure.
5. **[Crie o utilizador](#create-displayr-test-user)** de teste displayr para ter uma contraparte de Britta Simon no Displayr que esteja ligada à representação azure AD do utilizador.
6. **[Teste sSO](#test-sso)** para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure,](https://portal.azure.com/)na página de integração da aplicação **Displayr,** encontre a secção **Gerir** e selecione **um único sinal .**
1. Na página **Select a Single sign-on,** selecione **SAML**.
1. No **Set-up Single Sign-On com** a página SAML, clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na secção **Basic SAML Configuration,** execute o seguinte passo:

    a. Na caixa de texto **de URL sign-on,** escreva um URL utilizando o seguinte padrão:`https://<YOURDOMAIN>.displayr.com`

    b. Na caixa de texto **identificador (Id da entidade),** digite um URL utilizando o seguinte padrão:`<YOURDOMAIN>.displayr.com`

    >[!NOTE]
    >Estes valores não são reais. Atualize estes valores com o sinal real no URL e identificador. Contacte a equipa de suporte do [Cliente Displayr](mailto:support@displayr.com) para obter estes valores. Também pode consultar os padrões mostrados na secção de Configuração SAML Básica no portal Azure.

1. Na **configuração de um único sign-on com** a página SAML, na secção Certificado de **Assinatura SAML,** encontre **certificado (Base64)** e selecione **Descarregar** para descarregar o certificado e guardá-lo no seu computador.

   ![O link de descarregamento do Certificado](common/certificatebase64.png)

1. A aplicação displayr espera as afirmações do SAML num formato específico, o que requer que adicione mapeamentos personalizados de atributos à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos. Clique no ícone **Editar** para abrir o diálogo de Atributos do Utilizador.

    ![image](common/edit-attribute.png)

1. Além de acima, a aplicação Displayr espera que poucos atributos sejam retransmitidos na resposta SAML. Na secção **de Atributos do Utilizador & Reclamações** no diálogo de reclamações do **grupo (pré-visualização),** execute os seguintes passos:

    a. Clique na **caneta** ao lado dos **Grupos devolvidos por reclamação**.

    ![image](./media/displayr-tutorial/config04.png)

    ![image](./media/displayr-tutorial/config05.png)

    b. Selecione **Todos os Grupos** da lista de rádio.

    c. Selecione **Atributo fonte** do ID do **grupo**.

    d. Verifique **Personalizar o nome da reivindicação do grupo.**

    e. Verifique os **grupos Emit como reivindicações de papéis.**

    f. Clique em **Guardar**.

1. Na secção **Displayr configuração,** copie os URL(s) adequados com base na sua exigência.

   ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="configure-displayr"></a>Configurar displayr

1. Para automatizar a configuração dentro do Displayr, é necessário instalar a extensão de **'Sign-in' de Aplicações Seguras,** clicando em **instalar a extensão**.

    ![Extensão das minhas aplicações](common/install-myappssecure-extension.png)

2. Depois de adicionar extensão ao navegador, clique no **'Displayr ' Configurar** irá direcioná-lo para a aplicação Displayr. A partir daí, forneça as credenciais de administração para assinar no Displayr. A extensão do navegador configurará automaticamente a aplicação para si e automatizará os passos 3-6.

    ![Configuração de configuração de configuração](common/setup-sso.png)

3. Se pretender configurar o Displayr manualmente, abra uma nova janela do navegador web e inscreva-se no site da empresa Displayr como administrador e execute os seguintes passos:

4. Clique em **Definições** e depois navegue para **Conta**.

    ![Configuração](./media/displayr-tutorial/config01.png)

5. Mude para **Definições** a partir do menu superior e desloque a página para clicar em **Configurar o Single Sign On (SAML)**.

    ![Configuração](./media/displayr-tutorial/config02.png)

6. Na página **Single Sign On (SAML),** execute os seguintes passos:

    ![Configuração](./media/displayr-tutorial/config03.png)

    a. Verifique a caixa **de ativação de um único sinal (SAML).**

    b. Copie o valor do **identificador** real a partir da secção básica de **configuração SAML** do Azure AD e cole-o na caixa de texto **emitente.**

    c. Na caixa de texto **LOGIN URL,** cola o valor do URL de **Login,** que copiou do portal Azure.

    d. Na caixa de texto **logout URL,** cola o valor do URL de **Logout,** que copiou do portal Azure.

    e. Abra o Certificado (Base64) no Bloco de Notas, copie o seu conteúdo e cole-o na caixa de texto **do Certificado.**

    f. **Os mapeamentos em grupo** são opcionais.

    g. Clique em **Guardar**.  

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD

Nesta secção, você vai criar um utilizador de teste no portal Azure chamado Britta Simon.

1. A partir do painel esquerdo no portal Azure, **selecione Azure Ative Directory**, selecione **Utilizadores**e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `Britta Simon`.  
   1. No campo de nome username@companydomain.extensiondo **Utilizador,** introduza o . Por exemplo, `BrittaSimon@contoso.com`.
   1. Selecione a caixa de verificação de **palavra-passe do Show** e, em seguida, escreva o valor que está apresentado na caixa **password.**
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, permitirá que Britta Simon utilize um único sign-on Azure, concedendo acesso ao Displayr.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Displayr**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **Britta Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

### <a name="create-displayr-test-user"></a>Criar utilizador de teste de displayr

Para ativar os utilizadores de Anúncios Azure, inscreva-se no Displayr, devendo ser aprovisionados no Displayr. No Displayr, o fornecimento é uma tarefa manual.

**Para fornecer uma conta de utilizador, execute os seguintes passos:**

1. Inscreva-se no Displayr como Administrador.

2. Clique em **Definições** e depois navegue para **Conta**.

    ![Configuração do visor](./media/displayr-tutorial/config01.png)

3. Mude para **Definições** a partir do menu superior e desloque a página até que a secção **utilizadores** clique em **Novo Utilizador**.

    ![Configuração do visor](./media/displayr-tutorial/config07.png)

4. Na página **New User,** execute os seguintes passos:

    ![Configuração do visor](./media/displayr-tutorial/config06.png)

    a. Em **Nome** da caixa de texto, introduza o nome de utilizador como **Brittasimon**.

    b. Na caixa de texto **por e-mail,** introduza o e-mail do utilizador como `Brittasimon@contoso.com`.

    c. Selecione a sua **adesão**ao Grupo apropriada .

    d. Clique em **Guardar**.

### <a name="test-sso"></a>Teste SSO

Quando selecionar o azulejo do Visor no Painel de Acesso, deve ser automaticamente inscrito no Visor para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
