---
title: 'Tutorial: Integração do Azure Ative Directory com a Qlik Sense Enterprise Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e a Qlik Sense Enterprise.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/03/2020
ms.author: jeedes
ms.openlocfilehash: 4995f813bbfe7ef368f83085cdd61ab97fa330e8
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2020
ms.locfileid: "92108529"
---
# <a name="tutorial-integrate-qlik-sense-enterprise-with-azure-active-directory"></a>Tutorial: Integre a Qlik Sense Enterprise com o Azure Ative Directory

Neste tutorial, você vai aprender a integrar qlik Sense Enterprise com Azure Ative Directory (Azure AD). Quando integrar a Qlik Sense Enterprise com a Azure AD, pode:

* Controlo em Azure AD que tem acesso à Qlik Sense Enterprise.
* Capacitar os seus utilizadores a serem automaticamente inscritos na Qlik Sense Enterprise com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode ter um mês de teste gratuito [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Qlik Sense Enterprise assinatura única (SSO) ativada.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste. 
* A Qlik Sense Enterprise apoia a **SP** iniciou a SSO.
* Qlik Sense Enterprise suporta **provisão just-in-time**

* Assim que configurar a Qlik Sense Enterprise, pode impor o controlo de Sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se a partir do Acesso Condicional. [Saiba como impor o controlo da sessão com a Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-qlik-sense-enterprise-from-the-gallery"></a>Adicionando Qlik Sense Enterprise da galeria

Para configurar a integração da Qlik Sense Enterprise em Azure AD, é necessário adicionar a Qlik Sense Enterprise da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Add a partir da secção **da galeria,** **escreva Qlik Sense Enterprise** na caixa de pesquisa.
1. Selecione **Qlik Sense Enterprise** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Configure e teste Azure AD SSO com qlik Sense Enterprise usando um utilizador de teste chamado **Britta Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado na Qlik Sense Enterprise.

Para configurar e testar a Azure AD SSO com a Qlik Sense Enterprise, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    * Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
    * **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
1. **[Configure qlik Sense Enterprise SSO](#configure-qlik-sense-enterprise-sso)** - para configurar as definições de Sign-On única no lado da aplicação.
    * **[Create Qlik Sense Enterprise test user](#create-qlik-sense-enterprise-test-user)** - para ter uma contraparte de Britta Simon na Qlik Sense Enterprise que está ligada à representação AD AD Azure do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração da aplicação **Qlik Sense Enterprise,** encontre a secção **Gerir** e selecione **Single sign-on**.
1. Na página **de método de inscrição única,** selecione **SAML**.
1. Na **configuração single Sign-On com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na página **de Configuração Básica SAML,** insira os valores para os seguintes campos:

    a. Na caixa de texto **url de entrada de sinais,** digite um URL utilizando o seguinte padrão: `https://<Fully Qualified Domain Name>:443{/virtualproxyprefix}/hub`

    b. Na caixa de texto **identifier,** digite um URL utilizando um dos seguintes padrões:

    ```http
    https://<Fully Qualified Domain Name>.qlikpoc.com
    https://<Fully Qualified Domain Name>.qliksense.com
    ```

    c. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:

    `https://<Fully Qualified Domain Name>:443{/virtualproxyprefix}/samlauthn/`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de Sign-On real, identifier e URL de resposta, que são explicados mais tarde neste tutorial ou contacte a [equipa de suporte do Cliente da Qlik Sense Enterprise](https://www.qlik.com/us/services/support) para obter estes valores. A porta padrão para os URLs é 443, mas pode personalizá-la de acordo com as suas necessidades da Organização.

1. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre o **Metadados XML** da Federação a partir das opções dadas de acordo com o seu requisito e guarde-o no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

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

Nesta secção, você permitirá que Britta Simon use a Azure single sign-on, concedendo acesso à Qlik Sense Enterprise.

1. No portal Azure, selecione **Aplicações empresariais**e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Qlik Sense Enterprise**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **Britta Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-qlik-sense-enterprise-sso"></a>Configurar Qlik Sense Enterprise SSO

1. Prepare o ficheiro XML dos metadados da Federação para que possa fazer o upload para o servidor Qlik Sense.

    > [!NOTE]
    > Antes de enviar os metadados IdP para o servidor Qlik Sense, o ficheiro precisa de ser editado para remover informações para garantir o funcionamento adequado entre o servidor Azure AD e Qlik Sense.

    ![A screenshot mostra uma janela visual studio code com o ficheiro Federação Metadados X M L.][qs24]

    a. Abra o ficheiro FederationMetaData.xml, que descarregou do portal Azure num editor de texto.

    b. Procure o valor **RoleDescriptor**.  Existem quatro entradas (dois pares de etiquetas de elementos de abertura e fecho).

    c. Elimine as tags RoleDescriptor e todas as informações entre o ficheiro.

    d. Guarde o ficheiro e guarde-o por perto para utilização posterior neste documento.

2. Navegue para a Consola de Gestão Qlik Sense Qlik (QMC) como um utilizador que pode criar configurações de procuração virtual.

3. No QMC, clique no item do menu **Virtual Proxies.**

    ![A screenshot mostra proxies virtuais selecionados a partir do SISTEMA CONFIGURAR.][qs6]

4. Na parte inferior do ecrã, clique no botão **Criar novo.**

    ![A screenshot mostra a nova opção Criar.][qs7]

5. Aparece o ecrã de edição por procuração virtual.  No lado direito do ecrã encontra-se um menu para tornar visíveis as opções de configuração.

    ![A screenshot mostra identificação selecionada a partir de Propriedades.][qs9]

6. Com a opção de menu identificação verificada, insira as informações de identificação para a configuração de procuração virtual Azure.

    ![A screenshot mostra editar a secção de identificação de procuração virtual onde pode introduzir os valores descritos.][qs8]  

    a. O campo **Descrição** é um nome amigável para a configuração de procuração virtual.  Insira um valor para uma descrição.

    b. O campo **prefixo** identifica o ponto final de procuração virtual para ligar ao Qlik Sense com o Azure AD Single Sign-On.  Introduza um nome prefixo único para este representante virtual.

    c. **O tempo de inatividade da sessão (minutos)** é o tempo limite para ligações através deste proxy virtual.

    d. O **nome do cabeçalho do cookie session** é o nome de cookies que armazena o identificador de sessão para a sessão Qlik Sense que um utilizador recebe após a autenticação bem sucedida.  Este nome tem de ser exclusivo.

7. Clique na opção do menu autenticação para torná-lo visível.  O ecrã de autenticação aparece.

    ![A screenshot mostra editar a secção de autenticação por procuração virtual onde pode introduzir os valores descritos.][qs10]

    a. O modo de **acesso anónimo** determina se os utilizadores anónimos podem aceder ao Qlik Sense através do proxy virtual.  A opção predefinida é Nenhum utilizador anónimo.

    b. O método de **autenticação** determina o esquema de autenticação que o representante virtual utilizará.  Selecione SAML da lista de drop-down.  Mais opções aparecem como resultado.

    c. No **campo URI anfitrião SAML,** insira os utilizadores do nome anfitrião para aceder ao Qlik Sense através deste proxy virtual SAML.  O nome anfitrião é o uri do servidor Qlik Sense.

    d. No **ID da entidade SAML,** insira o mesmo valor introduzido para o campo URI anfitrião SAML.

    e. Os **metadados DO IdP SAML** são o ficheiro editado anteriormente na secção **de Metadados da Federação de Edição a partir da secção de Configuração AD AZure.**  **Antes de carregar os metadados IdP, o ficheiro precisa de ser editado** para remover informações para garantir o funcionamento adequado entre o servidor AZure AD e Qlik Sense.  **Consulte as instruções acima se o ficheiro ainda não tiver sido editado.**  Se o ficheiro tiver sido editado clique no botão Procurar e selecione o ficheiro de metadados editado para o enviar para a configuração de procuração virtual.

    f. Introduza o nome de atributo ou referência de esquema para o atributo SAML que representa o **UserID** Azure AD envia para o servidor Qlik Sense.  As informações de referência do Schema estão disponíveis na configuração pós-configuração dos ecrãs de aplicações Azure.  Para utilizar o atributo de nome, insira `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` .

    exemplo, Introduza o valor do **diretório** do utilizador que será anexado aos utilizadores quando autenticarem o servidor Qlik Sense através do Azure AD.  Os valores codificados devem ser cercados por **parênteses quadrados []**.  Para utilizar um atributo enviado na afirmação Azure AD SAML, insira o nome do atributo nesta caixa de texto **sem** suportes quadrados.

    h. O **algoritmo de assinatura SAML** define o certificado de serviço (neste caso, o servidor Qlik Sense) para a configuração de procuração virtual.  Se o servidor Qlik Sense utilizar um certificado de confiança gerado utilizando o Microsoft Enhanced RSA e o AES Cryptographic Provider, altere o algoritmo de assinatura SAML para **SHA-256**.

    i. A secção de mapeamento de atributos SAML permite que atributos adicionais como grupos sejam enviados para qlik Sense para uso nas regras de segurança.

8. Clique na opção **de menu LOAD BALANCING** para torná-lo visível.  Aparece o ecrã de equilíbrio de carga.

    ![O Screenshot mostra o ecrã de edição de procuração virtual para BALANCEAMENTO DE CARGA, onde pode selecionar Adicionar novo nó de servidor.][qs11]

9. Clique no botão de nó do novo servidor do **servidor,** selecione o nó do motor ou os nós que o Qlik Sense enviará sessões para efeitos de equilíbrio de carga e clique no botão **Adicionar.**

    ![A screenshot mostra os nós do servidor Add para carregar o equilíbrio no botão de diálogo onde pode adicionar servidores.][qs12]

10. Clique na opção de menu Avançado para torná-lo visível. O ecrã avançado aparece.

    ![A screenshot mostra o ecrã virtual proxy Advanced edit.][qs13]

    A lista de anfitriões permite que os nomes hospedeiros sejam aceites ao ligar-se ao servidor Qlik Sense.  **Introduza o nome de anfitrião que os utilizadores especificarão ao ligar-se ao servidor Qlik Sense.** O nome anfitrião é o mesmo valor que o anfitrião uri da SAML sem o https://.

11. Clique no botão **Aplicar.**

    ![A imagem mostra o botão Aplicar.][qs14]

12. Clique em OK para aceitar a mensagem de aviso que os proxies ligados ao proxy virtual serão reiniciados.

    ![A screenshot mostra as alterações de Aplicação à mensagem de confirmação de procuração virtual.][qs15]

13. No lado direito do ecrã, aparece o menu de itens associados.  Clique na opção **menu Proxies.**

    ![A screenshot mostra Proxies selecionados a partir de itens associados.][qs16]

14. O ecrã de procuração aparece.  Clique no botão **Link** na parte inferior para ligar um proxy ao representante virtual.

    ![A imagem mostra o botão Link.][qs17]

15. Selecione o nó de procuração que irá suportar esta ligação de procuração virtual e clique no botão **Link.**  Após a ligação, o representante será listado sob os proxies associados.

    ![A screenshot mostra serviços de procuração selecionados.][qs18]
  
    ![A screenshot mostra proxies associados na caixa de diálogo de itens associados por procuração virtual.][qs19]

16. Após cerca de cinco a dez segundos, aparecerá a mensagem QMC refresh.  Clique no botão **Refresh QMC.**

    ![A screenshot mostra a mensagem A sua sessão terminou.][qs20]

17. Quando o QMC atualizar, clique no item do menu **Virtual proxies.** A nova entrada virtual de procuração SAML está listada na tabela do ecrã.  Clique apenas na entrada de procuração virtual.

    ![A screenshot mostra proxies virtuais com uma única entrada.][qs51]

18. Na parte inferior do ecrã, o botão de metadados DE descarregamento SP será ativado.  Clique no botão **de metadados DE descarregamento SP** para guardar os metadados num ficheiro.

    ![A screenshot mostra o botão de metadados Download S P.][qs52]

19. Abra o ficheiro de metadados sp.  Observe a entrada **de ENTIDADEID** e a entrada **do Serviço de Afirmação.**  Estes valores são equivalentes ao **Identificador**, **Assine no URL** e o URL de **resposta** na configuração da aplicação AD Azure. Cole estes valores na secção **Qlik Sense Enterprise Domain e URLs** na configuração da aplicação AD Azure se não estiverem em correspondência, então deve substituí-los no assistente de configuração da App AD Azure.

    ![O Screenshot mostra um editor de texto simples com um EntityDescriptor com entidadeID e AssertionConsumerService chamados.][qs53]

### <a name="create-qlik-sense-enterprise-test-user"></a>Criar utilizador de teste Qlik Sense Enterprise

A Qlik Sense Enterprise suporta **o fornecimento just-in-time**, os utilizadores adicionados automaticamente ao repositório 'USERS' da Qlik Sense Enterprise à medida que utilizam a funcionalidade SSO. Além disso, os clientes podem usar o QMC e criar um UDC (User Directory Connector) para utilizadores pré-povoadores na Qlik Sense Enterprise a partir do seu LDAP de eleição, como Ative Directory, entre outros.

### <a name="test-sso"></a>Teste SSO

Quando selecionar o azulejo Qlik Sense Enterprise no Painel de Acesso, deverá ser automaticamente inscrito na Qlik Sense Enterprise para a qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](/azure/active-directory/manage-apps/what-is-single-sign-on) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

<!--Image references-->

[qs6]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_06.png
[qs7]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_07.png
[qs8]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_08.png
[qs9]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_09.png
[qs10]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_10.png
[qs11]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_11.png
[qs12]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_12.png
[qs13]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_13.png
[qs14]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_14.png
[qs15]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_15.png
[qs16]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_16.png
[qs17]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_17.png
[qs18]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_18.png
[qs19]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_19.png
[qs20]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_20.png
[qs24]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_24.png
[qs51]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_51.png
[qs52]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_52.png
[qs53]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_53.png
