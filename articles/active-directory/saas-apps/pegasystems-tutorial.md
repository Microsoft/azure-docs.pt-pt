---
title: 'Tutorial: Integração do Diretório Ativo Azure com a Pega Systems | Microsoft Docs'
description: Neste tutorial, você aprenderá a configurar um único sign-on entre Azure Ative Directory e Pega Systems.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/25/2021
ms.author: jeedes
ms.openlocfilehash: 4cd767736d6349199f4c82b00cb0b35db36cdb44
ms.sourcegitcommit: eb546f78c31dfa65937b3a1be134fb5f153447d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/02/2021
ms.locfileid: "99430142"
---
# <a name="tutorial-azure-active-directory-integration-with-pega-systems"></a>Tutorial: Integração do Diretório Ativo Azure com a Pega Systems

Neste tutorial, você vai aprender a integrar a Pega Systems com o Azure Ative Directory (Azure AD). Quando integrar a Pega Systems com AZure AD, pode:

* Controlo em Azure AD que tem acesso à Pega Systems.
* Capacitar os seus utilizadores a serem automaticamente inscritos na Pega Systems com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Assinatura ativada pela Pega Systems (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você vai configurar e testar Azure AD um único sinal de acesso em um ambiente de teste.

* A Pega Systems suporta sSO iniciado pelo SP e iniciado pelo IdP.

## <a name="add-pega-systems-from-the-gallery"></a>Adicione sistemas Pega da galeria

Para configurar a integração da Pega Systems no Azure AD, é necessário adicionar a Pega Systems da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria,** **digite Sistemas Pega** na caixa de pesquisa.
1. Selecione **a Pega Systems** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-pega-systems"></a>Configurar e testar Azure AD SSO para sistemas Pega

Configure e teste Azure AD SSO com a Pega Systems utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado na Pega Systems.

Para configurar e testar o Azure AD SSO com a Pega Systems, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure a Pega Systems SSO](#configure-pega-systems-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create Pega Systems test user](#create-pega-systems-test-user)** - para ter uma contraparte de B.Simon em Sistemas Pega que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração de aplicações **pega Systems,** encontre a secção **Gerir** e selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone de lápis para **configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na caixa de diálogo **de configuração de configuração básica SAML,** se pretender configurar a aplicação no modo iniciado pelo IdP, complete os seguintes passos.

    ![Caixa básica de diálogo de configuração SAML](common/idp-intiated.png)

    1. Na caixa **identifier,** introduza um URL neste padrão:

       `https://<customername>.pegacloud.io:443/prweb/sp/<instanceID>`

    1. Na caixa **URL de resposta,** introduza um URL neste padrão:

       `https://<customername>.pegacloud.io:443/prweb/PRRestService/WebSSO/SAML/AssertionConsumerService`

5. Se pretender configurar a aplicação no modo iniciado pelo SP, selecione **Definir URLs adicionais** e completar os seguintes passos.

    ![Pega Systems Domain e URLs informações únicas de súmis](common/both-advanced-urls.png)

    1. Na placa na caixa **URL,** introduza o sinal no valor URL.

    1. Na caixa **'Retransmissão Estado',** introduza um URL neste padrão: `https://<customername>.pegacloud.io/prweb/sso`

    > [!NOTE]
    > Os valores aqui fornecidos são espaços reservados. Você precisa usar o identificador real, URL de resposta, assinar em URL, e transmitir URL estado. Você pode obter o identificador e responder valores DE URL a partir de uma aplicação Pega, como explicado mais tarde neste tutorial. Para obter o valor do estado de retransmissão, contacte a equipa de suporte da [Pega Systems.](https://www.pega.com/contact-us) Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

6. A aplicação Pega Systems necessita que as afirmações DOL estejam num formato específico. Para os obter no formato correto, é necessário adicionar mapeamentos de atributos personalizados à configuração de atributos de token SAML. A imagem que se segue mostra os atributos predefinidos. Selecione o ícone **Editar** para abrir a caixa de diálogo **'Atributos do Utilizador':**

    ![Atributos do utilizador](common/edit-attribute.png)

7. Além dos atributos mostrados na imagem anterior, a aplicação Pega Systems requer mais alguns atributos para ser repercutido na resposta SAML. Na secção de **reclamações** do Utilizador da caixa de diálogo Atributos do **Utilizador,** complete os seguintes passos para adicionar estes atributosken SAML:

    
   - `uid`
   - `cn`
   - `mail`
   - `accessgroup`  
   - `organization`  
   - `orgdivision`
   - `orgunit`
   - `workgroup`  
   - `Phone`

    > [!NOTE]
    > Estes valores são específicos da sua organização. Fornecer os valores apropriados.

    1. **Selecione Adicionar nova reclamação** para abrir a caixa de diálogo **de reclamações** do utilizador::

    ![Selecione Adicionar nova reclamação](common/new-save-attribute.png)

    ![Gerir a caixa de diálogo de reclamações do utilizador](common/new-attribute-details.png)

    1. Na caixa **Nome,** insira o nome de atributo mostrado para esta linha.

    1. Deixe a caixa **namespace** vazia.

    1. Para a **Fonte**, selecione **Atributo**.

    1. Na lista **de atributos 'Fonte',** selecione o valor do atributo mostrado para esta linha.

    1. Selecione **OK**.

    1. Selecione **Guardar**.

8. Na **configuração single Sign-On com página SAML,** na secção Certificado de Assinatura **SAML,** selecione o link **de descarregamento** ao lado **do Metadados XML da Federação,** de acordo com os seus requisitos, e guarde o certificado no seu computador:

    ![Link de descarregamento de certificado](common/metadataxml.png)

9. Na secção **Configurar sistemas Pega,** copie os URLs apropriados, com base nos seus requisitos.

    ![Copiar os URLs de configuração](common/copy-configuration-urls.png)

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso à Pega Systems.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Pega Systems**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

### <a name="configure-pega-systems-sso"></a>Configurar a Pega Systems SSO

1. Para configurar um único sinal no lado da **Pega Systems,** inscreva-se no Portal Pega com uma conta de administração noutra janela do navegador.

2. Selecione **Criar**  >  o Serviço de Autenticação **SysAdmin**  >  :

    ![Selecione Serviço de Autenticação](./media/pegasystems-tutorial/admin.png)
    
3. Complete os seguintes passos no ecrã **do Serviço de Autenticação Create.**

    ![Criar ecrã de serviço de autenticação](./media/pegasystems-tutorial/admin1.png)

    1. Na lista **tipo,** selecione **SAML 2.0**.

    1. Na caixa **Nome,** insira qualquer nome (por exemplo, **Azure AD SSO).**

    1. Na caixa **de descrição curta,** introduza uma descrição.  

    1. Selecione **Criar e abrir**.
    
4. Na secção **de informações do Fornecedor de Identidade (IdP),** selecione **metadados de IdP de importação** e navegue no ficheiro de metadados que descarregou a partir do portal Azure. Clique **em Enviar** para carregar os metadados:

    ![Secção de informação do Fornecedor de Identidade (IdP)](./media/pegasystems-tutorial/admin2.png)
    
    A importação preencherá os dados do IdP, tal como mostrado aqui:

    ![Dados idP importados](./media/pegasystems-tutorial/idp.png)
    
6. Preencha os seguintes passos na secção **de definições do Fornecedor de Serviços (SP).**

    ![Definições de prestadores de serviços](./media/pegasystems-tutorial/sp.png)

    1. Copie o valor **de Identificação da Entidade** e cole-o na caixa de **identificação** na secção **Configuração DE SAML Básico** no portal Azure.

    1. Copie o valor **de localização do Serviço de Apoio ao Consumidor de Afirmação (ACS)** e cole-o na caixa **URL de resposta** na secção de **Configuração SAML Básica** no portal Azure.

    1. **Selecione A assinatura do pedido de desativação**.

7. Selecione **Guardar**.

### <a name="create-pega-systems-test-user"></a>Criar utilizador de teste de Sistemas Pega

Em seguida, precisa criar um utilizador chamado Britta Simon in Pega Systems. Trabalhe com a [equipa de suporte da Pega Systems](https://www.pega.com/contact-us) para criar utilizadores.

### <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

#### <a name="sp-initiated"></a>SP iniciado:

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para o Sinal de Sistemas Pega no URL onde pode iniciar o fluxo de login.  

* Vá diretamente ao URL de login da Pega Systems e inicie o fluxo de login a partir daí.

#### <a name="idp-initiated"></a>IDP iniciado:

* Clique em **Testar esta aplicação** no portal Azure e deverá ser automaticamente inscrito nos Sistemas Pega para os quais configura o SSO. 

Também pode utilizar o Microsoft My Apps para testar a aplicação em qualquer modo. Quando clicar no azulejo pega Systems nas Minhas Apps, se configurado no modo SP, será redirecionado para o sinal de aplicação na página para iniciar o fluxo de login e se configurado no modo IDP, deverá ser automaticamente inscrito nos Sistemas Pega para os quais configura o SSO. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="next-steps"></a>Passos seguintes

Uma vez configurado a Pega Systems, pode impor o controlo da sessão, o que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).