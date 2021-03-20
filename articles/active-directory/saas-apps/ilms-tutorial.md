---
title: 'Tutorial: Integração do Diretório Ativo Azure com | iLMS Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o iLMS.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/14/2019
ms.author: jeedes
ms.openlocfilehash: 7289fe2ec1f39679dcec95f1f48a6efa9ed0cdfc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92460347"
---
# <a name="tutorial-integrate-ilms-with-azure-active-directory"></a>Tutorial: Integrar o iLMS com o Azure Ative Directory

Neste tutorial, você vai aprender a integrar o iLMS com O Diretório Ativo Azure (Azure AD). Quando integra o iLMS com AZure AD, pode:

* Controlo em Azure AD que tem acesso a iLMS.
* Permitir que os seus utilizadores sejam automaticamente inscritos no iLMS com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode ter um mês de teste gratuito [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* subscrição única (SSO) ativada iLMS.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste. iLMS suporta **SP e IDP** iniciado SSO

## <a name="adding-ilms-from-the-gallery"></a>Adicionar iLMS da galeria

Para configurar a integração do iLMS no AD Azure, é necessário adicionar o iLMS da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria,** escreva **iLMS** na caixa de pesquisa.
1. Selecione **iLMS** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Configure e teste Azure AD SSO com iLMS usando um utilizador de teste chamado **Britta Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado no iLMS.

Para configurar e testar o Azure AD SSO com iLMS, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure iLMS SSO](#configure-ilms-sso)** - para configurar as definições de Sign-On única no lado da aplicação.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
5. **[Create iLMS test user](#create-ilms-test-user)** - para ter uma contraparte de Britta Simon em iLMS que está ligada à representação AZure AD do utilizador.
6. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **iLMS,** encontre a secção **Gerir** e selecione **'Único sinal de sismo'.**
1. Na página **de método de inscrição única,** selecione **SAML**.
1. Na **configuração single Sign-On com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na página **de Configuração Básica SAML,** se pretender configurar a aplicação no modo iniciado pelo **IDP,** insira os valores para os seguintes campos:

    a. Na caixa de texto **identifier,** cole o valor **identificador** que copia da secção **de Prestador de Serviços** de definições SAML no portal de administração iLMS.

    b. Na caixa de texto **URL de resposta,** cole o valor **Endpoint (URL)** que copia da secção de **Prestador de Serviços** de definições DE SAML no portal de administração iLMS tendo o seguinte padrão `https://www.inspiredlms.com/Login/<instanceName>/consumer.aspx`

1. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação **no** modo iniciado sp:

    Na caixa de texto **URL de entrada de sinais,** cole o valor **Endpoint (URL)** que copia da secção de Prestador de **Serviços** de definições DE SAML no portal de administração iLMS como `https://www.inspiredlms.com/Login/<instanceName>/consumer.aspx`

1. Para permitir o provisionamento do JIT, a sua aplicação iLMS espera as afirmações SAML num formato específico, o que requer que adicione mapeamentos de atributos personalizados à configuração de atributos de símbolo SAML. A imagem que se segue mostra a lista de atributos predefinidos. Clique **em Editar** o ícone para abrir o diálogo dos Atributos do Utilizador.

    > [!NOTE]
    > Tem de ativar **a Conta de Utilizador Não Reconhecida** no iLMS para mapear estes atributos. Siga as instruções [aqui](https://support.inspiredelearning.com/help/adding-updating-and-managing-users#just-in-time-provisioning-with-saml-single-signon) para obter uma ideia sobre a configuração dos atributos.

1. Além de acima, a aplicação iLMS espera que alguns mais atributos sejam repercutidos na resposta SAML. Na secção **'Reclamações** de Utilizador' no diálogo **'Atributos do Utilizador',** execute os seguintes passos para adicionar o atributoken SAML, tal como mostrado na tabela abaixo:

    | Name | Atributo de origem|
    | --------|------------- |
    | divisão | user.department |
    | region | user.state |
    | departamento | user.jobtitle |

    a. Clique **Em Adicionar nova reivindicação** para abrir o diálogo de reclamações do utilizador **Gerir.**

    b. Na caixa de texto **'Nome',** digite o nome do atributo indicado para esta linha.

    c. Deixe o **Espaço Namespace** em branco.

    d. Selecione Fonte como **Atributo**.

    e. A partir da lista **de atributos Source,** digite o valor de atributo mostrado para esta linha.

    f. Clique **em Ok**

    exemplo, Clique em **Guardar**.

1. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Metadadata XML** da Federação a partir das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

1. Na secção **Configurar iLMS,** copie os URL(s) apropriados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador de Azure Ad

    c. Logout URL

### <a name="configure-ilms-sso"></a>Configure iLMS SSO

1. Numa janela diferente do navegador web, inscreva-se no seu **portal de administração iLMS** como administrador.

2. Clique em **SSO:SAML** no **separador Definições** para abrir as definições DE SAML e executar os seguintes passos:

    ![A screenshot mostra o separador de definições I L M S onde pode selecionar S S O: SAML.](./media/ilms-tutorial/1.png)

3. Expandir a secção **de Prestador de Serviços** e copiar o valor **de Identificador** e **Ponto Final (URL).**

    ![A screenshot mostra configurações SAML onde pode obter os valores.](./media/ilms-tutorial/2.png) 

4. Na secção **Fornecedor de Identidade,** clique em **Metadados de Importação.**

5. Selecione o ficheiro **de metadados** da Federação descarregado a partir do portal Azure a partir da secção **certificado de assinatura SAML.**

    ![A screenshot mostra configurações SAML onde pode selecionar o ficheiro de metadados.](./media/ilms-tutorial/tutorial_ilms_ssoconfig1.png)

6. Se pretender permitir que o fornecimento de JIT crie contas iLMS para utilizadores não reconhecidos, siga abaixo os passos:

    a. Verifique **criar conta de utilizador não reconhecida**.

    ![Screenshot mostra Criar a opção de Conta de Utilizador não reconhecida.](./media/ilms-tutorial/tutorial_ilms_ssoconfig2.png)

    b. Mapear os atributos em AD Azure com os atributos em iLMS. Na coluna de atributos, especifique o nome dos atributos ou o valor predefinido.

    c. Vá ao separador **Regras empresariais** e execute os seguintes passos:

    ![O Screenshot mostra as definições de Regras empresariais onde pode introduzir as informações neste passo.](./media/ilms-tutorial/5.png)

    d. Consulte **Criar Regiões, Divisões e Departamentos não reconhecidos** para criar Regiões, Divisões e Departamentos que ainda não existem no momento da Assinatura Única.

    e. Verifique o perfil do utilizador da atualização durante o **sessão de sessão** para especificar se o perfil do utilizador está atualizado com cada Sessão de Sessão.

    f. Se for verificada a **atualização de Valores Em Branco para Campos Não Obrigatórios na** opção Perfil do Utilizador, os campos de perfil opcional que estão em branco após a entrada em sessão também farão com que o perfil iLMS do utilizador contenha valores em branco para esses campos.

    exemplo, Consulte **enviar e-mail** de notificação de erro e insira o e-mail do utilizador onde pretende receber o e-mail de notificação de erro.

7. Clique **em Guardar** o botão para guardar as definições.

    ![Captura de ecrã a mostrar o botão Guardar.](./media/ilms-tutorial/save.png)

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

Nesta secção, você permitirá que Britta Simon use Azure single sign-on, concedendo acesso ao iLMS.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **iLMS**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **Britta Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

### <a name="create-ilms-test-user"></a>Criar utilizador de teste iLMS

A aplicação suporta o fornecimento do utilizador a tempo e após a autenticação os utilizadores são criados automaticamente na aplicação. O JIT funcionará, se tiver clicado na caixa de verificação **de conta de utilizador Create Un-reconhecida** durante a definição de configuração SAML no portal de administração iLMS.

Se precisar de criar um utilizador manualmente, siga os passos abaixo:

1. Inscreva-se no site da empresa iLMS como administrador.

2. Clique em **Registar o utilizador** no **separador Utilizadores** para abrir a página **do Utilizador do Registo.**

   ![A screenshot mostra o separador I L M S onde pode selecionar o Utilizador registo.](./media/ilms-tutorial/3.png)

3. Na página **'Utilizador registo',** execute os seguintes passos.

    ![A screenshot mostra a página do Utilizador do Registo onde introduz as informações especificadas.](./media/ilms-tutorial/create_testuser_add.png)

    a. Na caixa de texto **"First Name",** escreva o primeiro nome como Britta.

    b. Na caixa de texto **do Último Nome,** escreva o sobrenome como Simon.

    c. Na caixa de texto **do ID de e-mail,** digite o endereço de e-mail do utilizador como BrittaSimon@contoso.com .

    d. Na **Região,** selecione o valor para a região.

    e. Na **divisão,** selecione o valor para a divisão.

    f. Na entrega do **Departamento,** selecione o valor para o departamento.

    exemplo, Clique em **Guardar**.

    > [!NOTE]
    > Pode enviar correio de registo para o utilizador selecionando enviar caixa de verificação **por Correio de Envio.**

### <a name="test-sso"></a>Teste SSO

Quando selecionar o azulejo iLMS no Painel de Acesso, deverá ser automaticamente inscrito no iLMS para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](../conditional-access/overview.md)