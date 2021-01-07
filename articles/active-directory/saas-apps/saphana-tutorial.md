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
ms.date: 12/27/2020
ms.author: jeedes
ms.openlocfilehash: f6066997b5a63a9ffefc1371851c7200d7655c9c
ms.sourcegitcommit: f6f928180504444470af713c32e7df667c17ac20
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/07/2021
ms.locfileid: "97962606"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-hana"></a>Tutorial: Integração do Diretório Ativo Azure com SAP HANA

Neste tutorial, você vai aprender a integrar SAP HANA com Azure Ative Directory (Azure AD). Quando integrar o SAP HANA com AZure AD, pode:

* Controlo em Azure AD que tem acesso a SAP HANA.
* Permita que os seus utilizadores sejam automaticamente inscritos no SAP HANA com as suas contas AD AZure.
* Gerencie as suas contas numa localização central - o portal Azure.

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

> [!NOTE]
> O identificador desta aplicação é um valor fixo de cadeia para que apenas um caso possa ser configurado em um inquilino.


## <a name="adding-sap-hana-from-the-gallery"></a>Adicionando SAP HANA da galeria

Para configurar a integração do SAP HANA em Azure AD, precisa adicionar o SAP HANA da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **da galeria,** **escreva SAP HANA** na caixa de pesquisa.
1. Selecione **SAP HANA** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-sap-hana"></a>Configure e teste Azure AD SSO para SAP HANA

Configure e teste Azure AD SSO com SAP HANA usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado no SAP HANA.

Para configurar e testar o Azure AD SSO com o SAP HANA, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
2. **[Configure SAP HANA SSO](#configure-sap-hana-sso)** - para configurar as definições de Sign-On única no lado da aplicação.
    1. **[Create SAP HANA test user](#create-sap-hana-test-user)** - para ter uma contraparte de Britta Simon em SAP HANA que está ligada à representação AZure AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração da aplicação **SAP HANA,** encontre a secção **Gerir** e selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone de lápis para **configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** insira os valores para os seguintes campos:

    Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:  `https://<Customer-SAP-instance-url>/sap/hana/xs/saml/login.xscfunc`

    > [!NOTE]
    > O valor URL de resposta não é real. Atualizar o valor com o URL de resposta real. Contacte [a equipa de suporte do cliente SAP HANA](https://cloudplatform.sap.com/contact.html) para obter os valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

5. A aplicação SAP HANA espera as afirmações DOL num formato específico. Configure os seguintes pedidos para este pedido. Pode gerir os valores destes atributos a partir da secção **Atributos** do Utilizador na página de integração de aplicações. Na **configuração single Sign-On com** a página SAML, clique no botão **Editar** para abrir o diálogo **de Atributos do Utilizador.**

    ![Screenshot que mostra a secção "Atributos do utilizador" com o ícone "Editar" selecionado.](common/edit-attribute.png)

6. Na secção **de atributos** do Utilizador no diálogo **"Atributos & Reclamações** do Utilizador", execute os seguintes passos:
 
    a. Clique **em Editar o ícone** para abrir o diálogo de reclamações do utilizador **Gerir.**

    ![Screenshot que mostra o diálogo "Atributos do Utilizador & Reclamações" com o ícone "Editar" selecionado.](./media/saphana-tutorial/tutorial_usermail.png)

    ![image](./media/saphana-tutorial/tutorial_usermailedit.png)

    b. Na lista **de Transformação,** selecione **ExtractMailPrefix()**.

    c. Na lista **do Parâmetro 1,** selecione **user.mail**.

    d. Clique em **Guardar**.

7. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Metadadata XML** da Federação a partir das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso a SAP HANA.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **SAP HANA**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-sap-hana-sso"></a>Configurar SAP HANA SSO

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

4. No Estúdio HANA, dentro das propriedades do sistema do **separador Configuração,** filtrar as definições por **saml**. Em seguida, ajuste o **assertion_timeout** de **10 segundos** para **120 segundos**.

    ![assertion_timeout definição](./media/saphana-tutorial/sap7.png)


### <a name="create-sap-hana-test-user"></a>Criar utilizador de teste SAP HANA

Para permitir que os utilizadores da Azure AD entrem no SAP HANA, deve forerá-los em SAP HANA.
O SAP HANA suporta **o provisionamento just-in-time**, que é ativado por padrão.

Se precisar de criar um utilizador manualmente, tome os seguintes passos:

>[!NOTE]
>Pode alterar a autenticação externa que o utilizador utiliza. Podem autenticar-se com um sistema externo como o Kerberos. Para obter informações detalhadas sobre identidades externas, contacte o seu [administrador de domínio.](https://cloudplatform.sap.com/contact.html)

1. Abra o [Estúdio SAP HANA](https://help.sap.com/viewer/a2a49126a5c546a9864aae22c05c3d0e/2.0.01/en-us) como administrador e, em seguida, ative o DB-User para SAML SSO.

    ![Criar utilizador](./media/saphana-tutorial/sap5.png)

2. Selecione a caixa de verificação invisível à esquerda da **SAML** e, em seguida, selecione o link **Configure.**

3. **Selecione Adicionar** para adicionar o IDP SAML.  Selecione o IDP SAML apropriado e, em seguida, selecione **OK**.

4. Adicione a **Identidade Externa** (neste caso, BrittaSimon) ou escolha **Qualquer**. Em seguida, selecione **OK**.

   > [!Note]
   > Se a caixa de verificação **Any** não for selecionada, então o nome de utilizador em HANA precisa de corresponder exatamente o nome do utilizador na UPN antes do sufixo de domínio. (Por exemplo, BrittaSimon@contoso.com torna-se BrittaSimon em HANA.)

5. Para efeitos de teste, atribua todas as funções **XS** ao utilizador.

    ![Atribuição de funções](./media/saphana-tutorial/sap6.png)

    > [!TIP]
    > Deve dar permissões que sejam adequadas apenas para os seus casos de utilização.

6. Salve o utilizador.

### <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções.

* Clique em Testar esta aplicação no portal Azure e deverá ser automaticamente inscrito no SAP HANA para o qual configura o SSO

* Pode utilizar as minhas apps do Microsoft. Quando clicar no azulejo SAP HANA nas Minhas Apps, deverá ser automaticamente inscrito no SAP HANA para o qual configura o SSO. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)


## <a name="next-steps"></a>Passos seguintes

Uma vez configurado o SAP HANA, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).