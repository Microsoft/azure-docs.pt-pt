---
title: 'Tutorial: Integração do Azure Ative Directory com a SAP Analytics Cloud Microsoft Docs'
description: Saiba como configurar um único sinal entre o Azure Ative Directory e o SAP Analytics Cloud.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/17/2019
ms.author: jeedes
ms.openlocfilehash: 84b0a761b52baca4ae1ab81b3c67f18f6b14db92
ms.sourcegitcommit: e15c0bc8c63ab3b696e9e32999ef0abc694c7c41
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/16/2020
ms.locfileid: "97608838"
---
# <a name="tutorial-integrate-sap-analytics-cloud-with-azure-active-directory"></a>Tutorial: Integre a nuvem de análise SAP com diretório ativo Azure

Neste tutorial, você vai aprender a integrar a SAP Analytics Cloud com o Azure Ative Directory (Azure AD). Quando integrar a NUVEM Analítica SAP com AZure AD, pode:

* Controle em Azure AD que tem acesso à SAP Analytics Cloud.
* Permita que os seus utilizadores sejam automaticamente inscritos na SAP Analytics Cloud com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Subscrição ativada pela SAP Analytics Cloud (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* SAP Analytics Cloud suporta **SSO** iniciado sp

## <a name="adding-sap-analytics-cloud-from-the-gallery"></a>Adicionando NUVEM SAP Analytics da galeria

Para configurar a integração da SAP Analytics Cloud em Azure AD, precisa de adicionar a NUVEM SAP Analytics da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria,** **escreva A nuvem Analítica SAP** na caixa de pesquisa.
1. Selecione **SAP Analytics Cloud** do painel de resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Configure e teste Azure AD SSO com SAP Analytics Cloud usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado na NUVEM Analítica SAP.

Para configurar e testar O Azure AD SSO com a NUVEM SAP Analytics, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o SAP Analytics Cloud SSO](#configure-sap-analytics-cloud-sso)** - para configurar as definições de Sign-On únicas no lado da aplicação.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
5. **[Crie o utilizador](#create-sap-analytics-cloud-test-user)** de teste SAP Analytics Cloud - para ter uma contraparte de B.Simon na NUVEM Analítica SAP que está ligada à representação AD AD do utilizador.
6. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração da aplicação **SAP Analytics Cloud,** encontre a secção **Gerir** e selecione **Single sign-on**.
1. Na página **de método de inscrição única,** selecione **SAML**.
1. Na **configuração single Sign-On com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** insira os valores para os seguintes campos:

    a. Na caixa de texto **URL, digite** um URL utilizando o seguinte padrão:

    - `https://<sub-domain>.sapanalytics.cloud/`
    - `https://<sub-domain>.sapbusinessobjects.cloud/`

    b. Na caixa de texto **identifier (Entity ID),** digite um URL utilizando o seguinte padrão:

    - `<sub-domain>.sapbusinessobjects.cloud`
    - `<sub-domain>.sapanalytics.cloud`

    > [!NOTE] 
    > Os valores nestes URLs são apenas para demonstração. Atualize os valores com o URL de inscrição real e URL do identificador. Para obter o URL de inscrição, contacte a equipa de suporte do [SAP Analytics Cloud Client](https://help.sap.com/viewer/product/SAP_BusinessObjects_Cloud/release/). Pode obter o URL do identificador descarregando os metadados SAP Analytics Cloud a partir da consola de administração. Isto é explicado mais tarde no tutorial.

4. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre o **Metadados XML da Federação** e selecione **Descarregamento** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

6. Na secção Configurar a **nuvem DE Análise SAP,** copie os URL(s) apropriados com base no seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="configure-sap-analytics-cloud-sso"></a>Configurar SAP Analytics Cloud SSO

1. Numa janela diferente do navegador web, inscreva-se no site da empresa SAP Analytics Cloud como administrador.

2. Selecione   >  a Administração do **Sistema de**  >  Menus .
    
    ![Selecione Menu, em seguida, Sistema, e depois Administração](./media/sapboc-tutorial/config1.png)

3. No separador **Segurança,** selecione o ícone **Editar** (caneta).
    
    ![No separador Segurança, selecione o ícone Editar](./media/sapboc-tutorial/config2.png)  

4. Para **método de autenticação**, selecione **SAML Single Sign-On (SSO)**.

    ![Selecione Sign-On Única SAML para o método de autenticação](./media/sapboc-tutorial/config3.png)  

5. Para descarregar os metadados do fornecedor de serviços (Passo 1), selecione **Baixar**. No ficheiro de metadados, encontre e copie o valor **de ENTITYID.** No portal Azure, no diálogo **de configuração SAML básico,** cole o valor na caixa **do Identificador.**

    ![Copiar e colar o valor de entityID](./media/sapboc-tutorial/config4.png)  

6. Para carregar os metadados do prestador de serviços (Passo 2) no ficheiro que descarregou a partir do portal Azure, nos **metadados do Fornecedor de Identidade de Upload**, selecione **Upload**.  

    ![Nos metadados do Fornecedor de Identidade de Upload, selecione Upload](./media/sapboc-tutorial/config5.png)

7. Na lista **de Atributos do Utilizador,** selecione o atributo do utilizador (Passo 3) que pretende utilizar para a sua implementação. Este utilizador atribui mapas ao fornecedor de identidade. Para introduzir um atributo personalizado na página do utilizador, utilize a opção **de Mapeamento SAML personalizado.** Ou, pode selecionar **o Email** ou **o USER ID** como o atributo do utilizador. No nosso exemplo, selecionamos **o Email** porque mapeamos a reclamação do identificador de utilizador com o atributo **userprincipalname** na secção **Atributos & Reclamações** do Utilizador no portal Azure. Isto fornece um e-mail exclusivo do utilizador, que é enviado para a aplicação SAP Analytics Cloud em todas as respostas SAML bem sucedidas.

    ![Selecione Atributo do Utilizador](./media/sapboc-tutorial/config6.png)

8. Para verificar a conta com o fornecedor de identidade (Passo 4), na caixa **de Credencial de Login (Email),** insira o endereço de e-mail do utilizador. Em seguida, **selecione Verificar Conta**. O sistema adiciona credenciais de inscrição na conta de utilizador.

    ![Introduza e-mail e selecione Verificar Conta](./media/sapboc-tutorial/config7.png)

9. Selecione o ícone **Guardar.**

    ![Salvar ícone](./media/sapboc-tutorial/save.png)

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso à SAP Analytics Cloud.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **SAP Analytics Cloud**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

### <a name="create-sap-analytics-cloud-test-user"></a>Criar utilizador de teste de nuvem analítica SAP

Os utilizadores de AZure AD devem ser a provisionados na NUVEM Analítica SAP antes de poderem iniciar sação na SAP Analytics Cloud. Na NUVEM Analítica SAP, o provisionamento é uma tarefa manual.

Para a provisionar uma conta de utilizador:

1. Inscreva-se no site da empresa SAP Analytics Cloud como administrador.

2. Selecione   >    >  **Utilizadores** de Segurança do Menu .

    ![Adicionar Empregado](./media/sapboc-tutorial/user1.png)

3. Na página **Utilizadores,** para adicionar novos dados do utilizador, selecione **+** . 

    ![Adicionar página de Utilizadores](./media/sapboc-tutorial/user4.png)

    Em seguida, complete os seguintes passos:

    1. Na caixa **de ID do UTILIZADOR,** introduza o ID do utilizador, como **B**.

    1. Na caixa **FIRST NAME,** insira o primeiro nome do utilizador, como **B**.

    1. Na **caixa NAME,** insira o último nome do utilizador, como **Simão.**

    1. Na caixa **DISPLAY NAME,** insira o nome completo do utilizador, como **B.Simon**.

    1. Na caixa **de E-MAIL,** insira o endereço de e-mail do utilizador, como `b.simon@contoso.com` .

    1. Na página **'Selecionar peças',** selecione a função adequada para o utilizador e, em seguida, selecione **OK**.

        ![Selecionar função](./media/sapboc-tutorial/user3.png)

    1. Selecione o ícone **Guardar.**

### <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo da Nuvem Analítica SAP no Painel de Acesso, deverá ser automaticamente inscrito na Nuvem Analítica SAP para a qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)