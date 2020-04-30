---
title: 'Tutorial: Integração do Diretório Ativo Azure com o iLMS Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o iLMS.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: d6e11639-6cea-48c9-b008-246cf686e726
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 50097aec1c4a003d3494029e8f25bb13b564f207
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "68944029"
---
# <a name="tutorial-integrate-ilms-with-azure-active-directory"></a>Tutorial: Integrar o iLMS com o Diretório Ativo Azure

Neste tutorial, você vai aprender a integrar o iLMS com o Azure Ative Directory (Azure AD). Quando integrar o iLMS com a Azure AD, pode:

* Controlo em Azure AD que tem acesso ao iLMS.
* Ative que os seus utilizadores sejam automaticamente inscritos no iLMS com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode ter um mês de experiência gratuita [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* iLMS assinatura única ativada (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste. iLMS suporta **SP e IDP** iniciado SSO

## <a name="adding-ilms-from-the-gallery"></a>Adicionando iLMS da galeria

Para configurar a integração do iLMS em Azure AD, é necessário adicionar iLMS da galeria à sua lista de aplicações saaS geridas.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No **Add da** secção galeria, digite **iLMS** na caixa de pesquisa.
1. Selecione **iLMS** a partir do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Configure e teste Azure AD SSO com iLMS utilizando um utilizador de teste chamado **Britta Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no iLMS.

Para configurar e testar o Azure AD SSO com iLMS, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o ILMS SSO](#configure-ilms-sso)** - para configurar as definições de início de sessão individuais no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com Britta Simon.
4. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de AD Azure.
5. **[Crie um utilizador de teste iLMS](#create-ilms-test-user)** - para ter uma contrapartida da Britta Simon no iLMS que esteja ligada à representação do utilizador da AD Azure.
6. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **iLMS,** encontre a secção **Gerir** e selecione **um único sinal.**
1. Na página **Select a Single sign-on,** selecione **SAML**.
1. Na configuração do Single Sign-On com a página **SAML,** clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na página **basic SAML Configuração,** se desejar configurar a aplicação no modo iniciado **idp,** introduza os valores para os seguintes campos:

    a. Na caixa de texto **identifier,** cole o valor do **Identificador** que copia da secção de Fornecedor de **Serviços** das definições SAML no portal de administração iLMS.

    b. Na caixa de texto **URL resposta,** cole o valor **endpoint (URL)** que copia da secção de Fornecedor de **Serviços** das definições sAML no portal de administração iLMS com o seguinte padrão`https://www.inspiredlms.com/Login/<instanceName>/consumer.aspx`

1. Clique em **Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação no modo iniciado **por SP:**

    Na caixa de texto **de URL sign-on,** cole o valor **endpoint (URL)** que copia da secção de Fornecedor de **Serviços** das definições SAML no portal de administração iLMS como`https://www.inspiredlms.com/Login/<instanceName>/consumer.aspx`

1. Para ativar o fornecimento de JIT, a sua aplicação iLMS espera as afirmações do SAML num formato específico, o que requer que adicione mapeamentos personalizados de atributos à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos. Clique no ícone **Editar** para abrir o diálogo de Atributos do Utilizador.

    > [!NOTE]
    > Tem de ativar a **Create Un-recognized User Account** no iLMS para mapear estes atributos. Siga as instruções [aqui](https://support.inspiredelearning.com/help/adding-updating-and-managing-users#just-in-time-provisioning-with-saml-single-signon) para obter uma ideia sobre a configuração dos atributos.

1. Além de acima, a aplicação iLMS espera que poucos atributos sejam retransmitidos na resposta SAML. Na secção **Reivindicações** do Utilizador no diálogo **de Atributos** do Utilizador, execute os seguintes passos para adicionar atributo token SAML, conforme indicado no quadro abaixo:

    | Nome | Atributo fonte|
    | --------|------------- |
    | divisão | utilizador.departamento |
    | . | utilizador.estado |
    | departamento | user.jobtitle |

    a. Clique **Em adicionar nova alegação** para abrir o diálogo de reclamações do utilizador **Gerir.**

    b. Na caixa de texto **Name,** digite o nome do atributo mostrado para essa linha.

    c. Deixe o espaço de **nome em** branco.

    d. Selecione Origem como **Atributo**.

    e. Na lista de **atributos Fonte,** digite o valor do atributo mostrado para essa linha.

    f. Clique em **Ok**

    g. Clique em **Guardar**.

1. Na configuração de um único sign-on com a página **SAML,** na secção Certificado de **Assinatura SAML,** clique em **Baixar** para descarregar o **Federation Metadata XML** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/metadataxml.png)

1. Na secção **Configurar iLMS,** copie os URL(s) adequados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador Azure AD

    c. Logout URL

### <a name="configure-ilms-sso"></a>Configure iLMS SSO

1. Numa janela diferente do navegador web, inscreva-se no seu **portal de administração iLMS** como administrador.

2. Clique em **SSO:SAML** sob o separador **Definições** para abrir as definições do SAML e executar os seguintes passos:

    ![Configurar um único sinal](./media/ilms-tutorial/1.png)

3. Expandir a secção **prestador de serviços** e copiar o valor do **Identificador** e **Endpoint (URL).**

    ![Configurar um único sinal](./media/ilms-tutorial/2.png) 

4. Na secção **Fornecedor de Identidade,** clique em **Metadados de Importação**.

5. Selecione o ficheiro **de Metadados da Federação** descarregado do portal Azure a partir da secção certificado de assinatura **SAML.**

    ![Configurar um único sinal](./media/ilms-tutorial/tutorial_ilms_ssoconfig1.png)

6. Se pretender ativar o fornecimento de JIT para criar contas iLMS para utilizadores não reconhecidos, siga as seguintes etapas:

    a. Verifique **Criar conta de utilizador não reconhecida.**

    ![Configurar um único sinal](./media/ilms-tutorial/tutorial_ilms_ssoconfig2.png)

    b. Mapeie os atributos em Azure AD com os atributos no iLMS. Na coluna de atributos, especifique o nome dos atributos ou o valor predefinido.

    c. Vá ao separador **Regras de Negócios** e execute os seguintes passos:

    ![Configurar um único sinal](./media/ilms-tutorial/5.png)

    d. Consulte a **Create Un-recognized Regions, Divisions and Departments** para criar Regiões, Divisões e Departamentos que já não existem no momento do Único Sign-on.

    e. Verifique o perfil do **utilizador da atualização durante** o início de sessão para especificar se o perfil do utilizador está atualizado com cada início de sessão individual.

    f. Se a atualização dos **valores em branco para campos não obrigatórios na** opção de perfil de utilizador for verificada, os campos de perfil opcionais que estão em branco no momento do início também farão com que o perfil iLMS do utilizador contenha valores em branco para esses campos.

    g. Consulte o Email de **Notificação** de Erro de Envio e introduza o e-mail do utilizador onde pretende receber o email de notificação de erro.

7. Clique no botão **Guardar** para guardar as definições.

    ![Configurar um único sinal](./media/ilms-tutorial/save.png)

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

Nesta secção, permitirá que Britta Simon utilize um único sign-on Azure, concedendo acesso ao iLMS.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **iLMS**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **Britta Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

### <a name="create-ilms-test-user"></a>Criar o utilizador de teste iLMS

Suportes de aplicação Apenas a tempo de fornecimento de utilizadores e após a autenticação os utilizadores são criados automaticamente na aplicação. O JIT funcionará, se tiver clicado na caixa de verificação create **un-recognized da Conta utilizador** durante a definição de configuração do SAML no portal de administração iLMS.

Se precisar de criar um utilizador manualmente, siga os passos abaixo:

1. Inscreva-se no site da empresa iLMS como administrador.

2. Clique em **Registar o utilizador** no separador **Utilizador** para abrir a página do Utilizador **do Registo.**

   ![Adicionar Empregado](./media/ilms-tutorial/3.png)

3. Na página **Utilizador do Registo,** execute os seguintes passos.

    ![Adicionar Empregado](./media/ilms-tutorial/create_testuser_add.png)

    a. Na caixa de texto **First Name,** escreva o primeiro nome como Britta.

    b. Na caixa de texto **"Apelido",** escreva o sobrenome como Simon.

    c. Na caixa de texto **email ID,** digite BrittaSimon@contoso.como endereço de e-mail do utilizador como .

    d. Na **Região,** selecione o valor para a região.

    e. No dropdown **da Divisão,** selecione o valor para a divisão.

    f. No **departamento,** selecione o valor para o departamento.

    g. Clique em **Guardar**.

    > [!NOTE]
    > Pode enviar correio de registo ao utilizador selecionando caixa de verificação Enviar por correio de **registo.**

### <a name="test-sso"></a>Teste SSO

Quando selecionar o azulejo iLMS no Painel de Acesso, deve ser automaticamente inscrito no iLMS para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
