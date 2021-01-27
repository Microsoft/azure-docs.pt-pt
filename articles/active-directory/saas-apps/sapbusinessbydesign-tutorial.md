---
title: 'Tutorial: Integração do Azure Ative Directory com a SAP Business ByDesign | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o SAP Business ByDesign.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/22/2021
ms.author: jeedes
ms.openlocfilehash: e8f1bff50c5f163894392a9e5d08cc8451d835fa
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/27/2021
ms.locfileid: "98896414"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-business-bydesign"></a>Tutorial: Integração do Azure Active Directory com SAP Business ByDesign

Neste tutorial, você vai aprender a integrar o SAP Business ByDesign com o Azure Ative Directory (Azure AD). Quando integrar o SAP Business ByDesign com a Azure AD, pode:

* Controle em Azure AD que tem acesso ao SAP Business ByDesign.
* Permita que os seus utilizadores sejam automaticamente inscritos no SAP Business ByDesign com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* A assinatura ativada pela SAP Business ByDesign (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* SAP Business ByDesign apoia **SSO** iniciado sp

## <a name="add-sap-business-bydesign-from-the-gallery"></a>Adicione o SAP Business ByDesign da galeria

Para configurar a integração do SAP Business ByDesign no AD Azure, é necessário adicionar o SAP Business ByDesign da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria,** **escreva SAP Business ByDesign** na caixa de pesquisa.
1. Selecione **SAP Business ByDesign** do painel de resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso"></a>Configurar e testar Azure AD SSO

Configure e teste Azure AD SSO com SAP Business ByDesign usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado no SAP Business ByDesign.

Para configurar e testar a Azure AD SSO com a SAP Business ByDesign, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure a SAP Business ByDesign SSO](#configure-sap-business-bydesign-sso)** - para configurar as definições de Sign-On única no lado da aplicação.
    1. **[Create SAP Business ByDesign test user](#create-sap-business-bydesign-test-user)** - para ter uma contraparte de Britta Simon em SAP Business ByDesign que está ligada à representação AD Ad Azure do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração de aplicações **SAP Business ByDesign,** encontre a secção **Gerir** e selecione um único sinal **de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone de lápis para **configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **de Configuração Básica SAML,** execute os seguintes passos:

    a. Na caixa de texto **URL, digite** um URL utilizando o seguinte padrão: `https://<servername>.sapbydesign.com`

    b. Na caixa de texto **identifier (Entity ID),** digite um URL utilizando o seguinte padrão: `https://<servername>.sapbydesign.com`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o sinal real no URL e no identificador. Contacte a [equipa de suporte do CLIENTE DA SAP Business ByDesign](https://www.sap.com/products/cloud-analytics.support.html) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

5. A aplicação SAP Business ByDesign espera as afirmações DOL num formato específico. Configure os seguintes pedidos para este pedido. Pode gerir os valores destes atributos a partir da secção **Atributos** do Utilizador na página de integração de aplicações. Na **configuração single Sign-On com** a página SAML, clique no botão **Editar** para abrir o diálogo **de Atributos do Utilizador.**

    ![image1](common/edit-attribute.png)

6. Clique no ícone **Editar** para editar o valor do **identificador nome**.

    ![image2](media/sapbusinessbydesign-tutorial/mail-prefix1.png)

7. Na secção **Gerir reclamações do utilizador,** execute os seguintes passos:

    ![image3](media/sapbusinessbydesign-tutorial/mail-prefix2.png)

    a. Selecione **a Transformação** como **Fonte**.

    b. Na lista de abandonos de **transformação,** selecione **ExtractMailPrefix()**.

    > [!NOTE]
    > Por byd predefinido utiliza o formato NameID **não especificado** para mapeamento do utilizador. ByD mapeia o NameID de afirmações SAML no Alias do Utilizador ByD. Adicionalmente ByD suporta o nome ID formato **emailAddress**. Neste caso, a ByD mapeia o NameID da afirmação SAM no endereço de e-mail do utilizador ByD dos dados de contacto do colaborador byD. Para mais detalhes, pode consultar [este blog SAP.](https://blogs.sap.com/2017/05/24/single-sign-on-sso-with-sap-business-bydesign/)

8. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Metadadata XML** da Federação a partir das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

9. Na secção **set up SAP Business ByDesign,** copie os URL(s) apropriados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso ao SAP Business ByDesign.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **SAP Business ByDesign**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.

1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-sap-business-bydesign-sso"></a>Configurar o negócio da SAP bydesign SSO

1. Inscreva-se no seu portal SAP Business ByDesign com direitos de administrador.

2. Navegue para a **Tarefa Comum de Aplicação e Gestão do Utilizador** e clique no **separador Fornecedor de Identidade.**

3. Clique em **Novo Fornecedor de Identidade** e selecione o ficheiro XML de metadados que descarregou a partir do portal Azure. Ao importar os metadados, o sistema envia automaticamente o certificado de assinatura e certificado de encriptação necessários.

    ![Configurar Sign-On1 Individuais](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_54.png)

4. Para incluir o **URL do Serviço ao Consumidor de Afirmação** no pedido SAML, selecione Incluir URL de Serviço ao Consumidor de **Afirmação**.

5. Clique **em Ativar um único sinal de inscrição**.

6. Guarde as alterações.

7. Clique no **separador 'O Meu Sistema'.**

    ![Configurar Sign-On2 Individuais](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_52.png)

8. No **sinal AD Azure Na** caixa de texto URL, cole o valor URL do **Login,** que copiou a partir do portal Azure.

    ![Configurar Sign-On3 Individuais](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_53.png)

9. Especificar se o empregado pode escolher manualmente entre iniciar sessão com iD do utilizador e senha ou SSO selecionando a **Seleção manual do Fornecedor de Identidade**.

10. Na secção **URL SSO,** especifique o URL que deve ser usado pelo empregado para assinar no sistema.
    Na lista de despedimentos do URL enviada para o número de colaboradores, pode escolher entre as seguintes opções:

    **URL não-SSO**

    O sistema envia apenas a URL normal do sistema para o empregado. O empregado não pode assinar usando SSO, e deve usar senha ou certificado em vez disso.

    **SSO URL**

    O sistema envia apenas o URL SSO para o empregado. O empregado pode inscrever-se usando SSO. O pedido de autenticação é redirecionado através do IdP.

    **Seleção Automática**

    Se o SSO não estiver ativo, o sistema envia o URL normal do sistema ao empregado. Se o SSO estiver ativo, o sistema verifica se o empregado tem uma palavra-passe. Se estiver disponível uma palavra-passe, tanto o URL SSO como o URL não-SSO são enviados ao empregado. No entanto, se o empregado não tiver senha, apenas o URL SSO é enviado ao empregado.

11. Guarde as alterações.

### <a name="create-sap-business-bydesign-test-user"></a>Criar utilizador de teste SAP Business ByDesign

Nesta secção, cria-se um utilizador chamado Britta Simon no SAP Business ByDesign. Por favor, trabalhe com a equipa de [suporte do CLIENTE SAP Business ByDesign](https://www.sap.com/products/cloud-analytics.support.html) para adicionar os utilizadores na plataforma SAP Business ByDesign. 

> [!NOTE]
> Certifique-se de que o valor NameID deve coincidir com o nome de utilizador na plataforma SAP Business ByDesign.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

1. Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para o URL de inscrição de assinatura do NEGÓCIO SAP, onde pode iniciar o fluxo de login. 

2. Vá diretamente ao URL de inscrição do SAP Business ByDesign e inicie o fluxo de login a partir daí.

3. Pode utilizar as minhas apps do Microsoft. Quando clicar no azulejo DODesign DO SAP Business nas Minhas Apps, este será redirecionado para o URL de assinatura de assinatura DO NEGÓCIO SAP. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="next-steps"></a>Próximos passos

* Uma vez configurado o SAP Business ByDesign, pode impor controlos de sessão, que protegem a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. Os controlos de sessão estendem-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
