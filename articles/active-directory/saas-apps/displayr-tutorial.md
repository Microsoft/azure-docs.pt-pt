---
title: 'Tutorial: Integração do Diretório Ativo Azure com o Displayr | Microsoft Docs'
description: Saiba como configurar um único sinal de inscrição entre o Azure Ative Directory e o Displayr.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/20/2019
ms.author: jeedes
ms.openlocfilehash: 3cb6ee3162c70d2d07c4868ae90ecc54bd489966
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98622495"
---
# <a name="tutorial-integrate-displayr-with-azure-active-directory"></a>Tutorial: Integrar o Displayr com o Azure Ative Directory

Neste tutorial, você vai aprender a integrar o Displayr com o Azure Ative Directory (Azure AD). Quando integrar o Displayr com AZure AD, pode:

* Controlo em Azure AD que tem acesso ao Displayr.
* Ativar os seus utilizadores para serem automaticamente inscritos no Displayr com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Subscrição única ativada pelo Displayr (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste. Displayr suporta SSO iniciado **SP.**

## <a name="adding-displayr-from-the-gallery"></a>Adicionar Displayr da galeria

Para configurar a integração do Displayr no AZure AD, é necessário adicionar o Displayr da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria,** **escreva Displayr** na caixa de pesquisa.
1. Selecione **o Displayr** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Configure e teste Azure AD SSO com Displayr usando um utilizador de teste chamado **Britta Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado no Displayr.

Para configurar e testar o Azure AD SSO com o Displayr, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configurar o Visor](#configure-displayr)** para configurar as definições SSO no lado da aplicação.
3. **[Crie um utilizador de teste AZure AD](#create-an-azure-ad-test-user)** para testar o Azure AD com Britta Simon.
4. **[Atribua ao utilizador de teste AZure AD](#assign-the-azure-ad-test-user)** para permitir que a Britta Simon utilize um único sinal de Ad AD.
5. Crie o utilizador de **[teste Displayr](#create-displayr-test-user)** para ter uma contraparte de Britta Simon no Displayr que está ligada à representação AD AD do utilizador.
6. **[Teste SSO](#test-sso)** para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração da aplicação **Do Visor,** encontre a secção **'Gerir'** e selecione **'Único sinal de sção'.**
1. Na página **de método de inscrição única,** selecione **SAML**.
1. Na **configuração Single Sign-On com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **de Configuração Básica SAML,** execute o seguinte passo:

    a. Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão: `https://<YOURDOMAIN>.displayr.com`

    b. Na caixa de texto **identifier (Entity ID),** digite um URL utilizando o seguinte padrão:`<YOURDOMAIN>.displayr.com`
    
    c. Na caixa de texto **URL de resposta,** escreva `https://app.displayr.com/Login/ProcessSamlResponse` .
    
    d. Clique em **Guardar**.

    >[!NOTE]
    >Estes valores não são reais. Atualize estes valores com o sinal real no URL e no identificador. Contacte [a equipa de suporte do Cliente do Displayr](mailto:support@displayr.com) para obter estes valores. Também pode consultar os padrões indicados na secção de Configuração BÁSICA SAML no portal Azure.

1. Na **configuração Single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre **o Certificado (Base64)** e selecione **Descarregamento** para descarregar o certificado e guardá-lo no seu computador.

   ![O link de descarregamento de certificado](common/certificatebase64.png)

1. A aplicação do Displayr espera as afirmações DE SAML num formato específico, o que requer que adicione mapeamentos de atributos personalizados à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos. Clique **em Editar** o ícone para abrir o diálogo dos Atributos do Utilizador.

   ![Screenshot que mostra a secção "Atributos do Utilizador" com o ícone "Editar" em destaque.](common/edit-attribute.png)

1. Além de acima, a aplicação Do Displayr espera que alguns mais atributos sejam repercutidos na resposta SAML. Na secção **"Atributos & Reclamações** do Utilizador no diálogo **'Pré-visualização)** do Grupo, execute os seguintes passos:

   a. Clique **em Adicionar uma reivindicação de grupo**.

      ![Screenshot que mostra a janela "Group Claims (Preview) com definições selecionadas.](./media/displayr-tutorial/config05.png)

   b. Selecione **Todos os Grupos** da lista de rádio.

   c. Selecione **Atributo de Origem** do **ID do Grupo**.

   d. Verifique **personalizar o nome da reclamação do grupo**.

   e. Verifique **os grupos Emit como reivindicações de funções**.

   f. Clique em **Guardar**.

1. Na secção **'Set-up Displayr',** copie os URL(s) apropriados com base no seu requisito.

   ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="configure-displayr"></a>Visor configurar

1. Para automatizar a configuração dentro do Displayr, tem de instalar a extensão do **navegador 'As aplicações' Secure Sign-in** clicando **em instalar a extensão**.

    ![Extensão das minhas aplicações](common/install-myappssecure-extension.png)

2. Depois de adicionar extensão ao navegador, clique no **Displayr de configuração** irá direcioná-lo para a aplicação Displayr. A partir daí, forneça as credenciais de administração para assinar no Displayr. A extensão do navegador configurará automaticamente a aplicação para si e automatizará os passos 3-6.

    ![Configuração de configuração](common/setup-sso.png)

3. Se pretender configurar o Displayr manualmente, abra uma nova janela do navegador web e inscreva-se no site da empresa Displayr como administrador e execute os seguintes passos:

4. Clique em **Definições** e, em seguida, navegue para **conta.**

    ![Screenshot que mostra o ícone "Definições" e "Conta" selecionados.](./media/displayr-tutorial/config01.png)

5. Altere para **Definições** a partir do menu superior e desloque a página para clicar **em Configurar o Único Sinal (SAML)**.

    ![Screenshot que mostra o separador "Definições" selecionado e a ação "Configurar único sinal on (S A M L)" selecionada.](./media/displayr-tutorial/config02.png)

6. Na página **Single Sign On (SAML),** execute os seguintes passos:

    ![Configuração](./media/displayr-tutorial/config03.png)

    a. Verifique **a caixa de sinal único ativa (SAML).**

    b. Copie o valor do **identificador** real a partir da secção **de Configuração SAML Básica** do AD AD e cole-o na caixa de texto do **emitente.**

    c. Na caixa de texto **URL de login,** cole o valor do URL de **login,** que copiou do portal Azure.

    d. Na caixa de texto **URL logout,** cole o valor do **URL logout,** que copiou do portal Azure.

    e. Abra o Certificado (Base64) no Bloco de Notas, copie o seu conteúdo e cole-o na caixa de texto **do Certificado.**

    f. **Os mapeamentos de grupo** são opcionais.

    exemplo, Clique em **Guardar**.  

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD

Nesta secção, irá criar um utilizador de teste no portal Azure chamado Britta Simon.

1. A partir do painel esquerdo no portal Azure, selecione **Azure Ative Directory**, selecione **Utilizadores**, e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `Britta Simon`.  
   1. No campo **nome do utilizador,** insira o username@companydomain.extension . Por exemplo, `BrittaSimon@contoso.com`.
   1. Selecione a caixa **de verificação de palavra-passe Show** e, em seguida, anote o valor que é apresentado na caixa **palavra-passe.**
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permitirá que Britta Simon use a Azure single sign-on, concedendo acesso ao Displayr.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Displayr**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **Britta Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

### <a name="create-displayr-test-user"></a>Criar utilizador de teste displayr

Para ativar os utilizadores Azure AD, inscreva-se no Displayr, devem ser adustados no Displayr. No Displayr, o provisionamento é uma tarefa manual.

**Para obter uma conta de utilizador, execute os seguintes passos:**

1. Inscreva-se no Displayr como administrador.

2. Clique em **Definições** e, em seguida, navegue para **conta.**

    ![Screenshot que mostra o ícone "Definições (engrenagem)" com "Conta" selecionada.](./media/displayr-tutorial/config01.png)

3. Mude para **Definições** a partir do menu superior e desloque-se para baixo da página, até que a secção **de Utilizadores** clique em **Novo Utilizador**.

    ![Screenshot que mostra o separador "Definições" com "Utilizadores" realçado e o botão "Novo Utilizador" selecionado.](./media/displayr-tutorial/config07.png)

4. Na página Novo **Utilizador,** execute os seguintes passos:

    ![Configuração do displayr](./media/displayr-tutorial/config06.png)

    a. Na caixa de texto **Name,** insira o nome do utilizador como **Brittasimon**.

    b. Na caixa de texto **por e-mail,** insira o e-mail do utilizador como `Brittasimon@contoso.com` .

    c. Selecione a **sua adesão** ao Grupo apropriada .

    d. Clique em **Guardar**.

### <a name="test-sso"></a>Teste SSO

Quando selecionar o azulejo do Visor no Painel de Acesso, deverá ser automaticamente inscrito no Visor para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](../conditional-access/overview.md)
