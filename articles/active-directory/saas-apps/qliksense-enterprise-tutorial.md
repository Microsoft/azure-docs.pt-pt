---
title: 'Tutorial: Integração de Diretório Sonérório Ativo Azure com a Qlik Sense Enterprise [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e a Qlik Sense Enterprise.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 8c27e340-2b25-47b6-bf1f-438be4c14f93
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 03/03/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ec9349d8ed330a00a64922a44f99910f9eeeb0df
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "79136453"
---
# <a name="tutorial-integrate-qlik-sense-enterprise-with-azure-active-directory"></a>Tutorial: Integrar a Qlik Sense Enterprise com o Diretório Ativo Azure

Neste tutorial, você vai aprender a integrar a Qlik Sense Enterprise com o Azure Ative Directory (Azure AD). Quando integrar a Qlik Sense Enterprise com a Azure AD, pode:

* Controlo em Azure AD que tem acesso à Qlik Sense Enterprise.
* Permita que os seus utilizadores sejam automaticamente inscritos na Qlik Sense Enterprise com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode ter um mês de experiência gratuita [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Qlik Sense Enterprise única subscrição ativada (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste. 
* A Qlik Sense Enterprise apoia **o SP** iniciado SSO.
* Qlik Sense Enterprise apoia **o provisionamento just-in-time**

* Assim que configurar a Qlik Sense Enterprise, pode impor o controlo da Sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se a partir do Acesso Condicional. [Saiba como impor o controlo de sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-qlik-sense-enterprise-from-the-gallery"></a>Adicionando Qlik Sense Enterprise da galeria

Para configurar a integração da Qlik Sense Enterprise em Azure AD, você precisa adicionar Qlik Sense Enterprise da galeria à sua lista de aplicações geridas saaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No Add da secção **da galeria,** **digite a Qlik Sense Enterprise** na caixa de pesquisa.
1. Selecione **Qlik Sense Enterprise** a partir do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Configure e teste Azure AD SSO com Qlik Sense Enterprise usando um utilizador de teste chamado **Britta Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado na Qlik Sense Enterprise.

Para configurar e testar o Azure AD SSO com a Qlik Sense Enterprise, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    * **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com Britta Simon.
    * Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de AD Azure.
1. **[Configure Qlik Sense Enterprise SSO](#configure-qlik-sense-enterprise-sso)** - para configurar as definições de início de sessão individuais no lado da aplicação.
    * **[Crie o utilizador](#create-qlik-sense-enterprise-test-user)** de teste Qlik Sense Enterprise - para ter uma contrapartida da Britta Simon na Qlik Sense Enterprise que está ligada à representação do utilizador da AD Azure.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **da Qlik Sense Enterprise,** encontre a secção **Gerir** e selecione **um único sinal.**
1. Na página **Select a Single sign-on,** selecione **SAML**.
1. Na configuração do Single Sign-On com a página **SAML,** clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na página **basic SAML Configuração,** introduza os valores para os seguintes campos:

    a. Na caixa de texto **"Sign-on URL",** escreva um URL utilizando o seguinte padrão:`https://<Fully Qualified Domain Name>:443{/virtualproxyprefix}/hub`

    b. Na caixa de texto **do identificador,** digite um URL utilizando um dos seguintes padrões:

    | |
    |--|
    | `https://<Fully Qualified Domain Name>.qlikpoc.com`|
    | `https://<Fully Qualified Domain Name>.qliksense.com`|
    | |

    c. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:

    `https://<Fully Qualified Domain Name>:443{/virtualproxyprefix}/samlauthn/`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL, Identificador e Resposta real, que são explicados mais tarde neste tutorial ou contacte a equipa de suporte do [Cliente da Qlik Sense Enterprise](https://www.qlik.com/us/services/support) para obter estes valores. A porta padrão para os URLs é de 443, mas pode personalizá-la de acordo com a sua organização.

1. Na configuração de um único sign-on com a página **SAML,** na secção certificado de **assinatura SAML,** encontre **metadados da Federação XML** a partir das opções dadas de acordo com o seu requisito e guarde-o no seu computador.

    ![O link de descarregamento do Certificado](common/metadataxml.png)

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

Nesta secção, você permitirá que Britta Simon use o único sign-on Azure, concedendo acesso à Qlik Sense Enterprise.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Qlik Sense Enterprise**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **Britta Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

## <a name="configure-qlik-sense-enterprise-sso"></a>Configure Qlik Sense Enterprise SSO

1. Prepare o ficheiro XML de Metadados da Federação para que possa enviar isso para o servidor Qlik Sense.

    > [!NOTE]
    > Antes de enviar os metadados idp para o servidor Qlik Sense, o ficheiro precisa de ser editado para remover informações para garantir o bom funcionamento entre o servidor Azure AD e Qlik Sense.

    ![QlikSense][qs24]

    a. Abra o ficheiro FederationMetaData.xml, que descarregou do portal Azure num editor de texto.

    b. Procure o valor **RoleDescriptor**.  Existem quatro entradas (dois pares de tags de elementos de abertura e fecho).

    c. Elimine as tags RoleDescriptor e todas as informações entre o ficheiro.

    d. Guarde o ficheiro e guarde-o para ser utilizado mais tarde neste documento.

2. Navegue para a Qlik Sense Qlik Management Console (QMC) como um utilizador que pode criar configurações de procuração virtual.

3. No QMC, clique no item do menu **Virtual Proxies.**

    ![QlikSense][qs6]

4. Na parte inferior do ecrã, clique no novo botão **Criar.**

    ![QlikSense][qs7]

5. Aparece o ecrã de edição de proxy Virtual.  No lado direito do ecrã está um menu para tornar visíveis as opções de configuração.

    ![QlikSense][qs9]

6. Com a opção de menu de identificação verificada, introduza as informações de identificação para a configuração de procuração virtual Azure.

    ![QlikSense][qs8]  

    a. O campo **Descrição** é um nome amigável para a configuração de procuração virtual.  Insira um valor para uma descrição.

    b. O campo **Prefix** identifica o ponto final de procuração virtual para ligar ao Qlik Sense com o Azure AD Single Sign-On.  Introduza um nome de prefixo único para este representante virtual.

    c. O tempo limite de **inatividade da sessão (minutos)** é o tempo limite para as ligações através deste proxy virtual.

    d. O nome do **cabeçalho** do cookie Session é o nome do cookie que armazena o identificador de sessão para a sessão Qlik Sense que um utilizador recebe após a autenticação bem sucedida.  Este nome tem de ser exclusivo.

7. Clique na opção do menu de autenticação para torná-lo visível.  Aparece o ecrã de Autenticação.

    ![QlikSense][qs10]

    a. A queda do modo de **acesso Anónimo** determina se os utilizadores anónimos podem aceder ao Qlik Sense através do proxy virtual.  A opção predefinida é nenhum utilizador anónimo.

    b. O método de **autenticação** determina o esquema de autenticação que o representante virtual irá utilizar.  Selecione SAML na lista de lançamentos.  Mais opções aparecem como resultado.

    c. No campo URI do **hospedeiro SAML,** insira os utilizadores de nome de anfitrião para aceder ao Qlik Sense através deste proxy virtual SAML.  O nome de anfitrião é o uri do servidor Qlik Sense.

    d. No ID da **entidade SAML,** insira o mesmo valor introduzido no campo URI do hospedeiro SAML.

    e. Os **metadados SAML IDP** são o ficheiro editado anteriormente na secção de Metadados da Federação de Edição a partir da secção de **Configuração AD Azure.**  **Antes de carregar os metadados idp, o ficheiro precisa de ser editado** para remover informações para garantir o bom funcionamento entre o servidor Azure AD e Qlik Sense.  **Consulte as instruções acima se o ficheiro ainda não tiver sido editado.**  Se o ficheiro tiver sido editado clique no botão Browse e selecione o ficheiro de metadados editado para o enviar para a configuração de procuração virtual.

    f. Introduza o nome do atributo ou referência de esquema para o atributo SAML que representa o **UserID** Azure AD envia para o servidor Qlik Sense.  A informação de referência do Schema está disponível na configuração de post de ecrãs de aplicações Do Azure.  Para utilizar o atributo `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`de nome, introduza .

    g. Introduza o valor para o **diretório** do utilizador que será anexado aos utilizadores quando autenticarem o servidor Qlik Sense através do Azure AD.  Os valores codificados devem ser rodeados por **suportes quadrados []**.  Para utilizar um atributo enviado na afirmação Azure AD SAML, introduza o nome do atributo nesta caixa de texto **sem** parênteses quadrados.

    h. O algoritmo de **assinatura SAML** define o fornecedor de serviços (neste caso, o servidor Qlik Sense) assinando para a configuração de procuração virtual.  Se o servidor Qlik Sense utilizar um certificado de confiança gerado utilizando o Microsoft Enhanced RSA e o AES Cryptographic Provider, altere o algoritmo de assinatura SAML para **SHA-256**.

    i. A secção de mapeamento de atributos SAML permite que atributos adicionais como grupos sejam enviados para Qlik Sense para uso em regras de segurança.

8. Clique na opção de menu **BALANCEING LOAD** para torná-lo visível.  Aparece o ecrã de equilíbrio de carga.

    ![QlikSense][qs11]

9. Clique no **botão de nó** do novo servidor, selecione o nó do motor ou os nós qlik Sense enviarão sessões para fins de equilíbrio de carga e clique no botão **Adicionar.**

    ![QlikSense][qs12]

10. Clique na opção de menu Avançado para torná-lo visível. O ecrã avançado aparece.

    ![QlikSense][qs13]

    O Anfitrião permite que a lista identifique nomes de anfitriões que são aceites quando se conectam ao servidor Qlik Sense.  **Introduza o nome de anfitrião que os utilizadores especificarão quando ligarem ao servidor Qlik Sense.** O nome de anfitrião é o mesmo valor que o hospedeiro hospedeiro sem o https://.

11. Clique no botão **Aplicar.**

    ![QlikSense][qs14]

12. Clique em OK para aceitar a mensagem de aviso que os proxies ligados ao proxy virtual serão reiniciados.

    ![QlikSense][qs15]

13. No lado direito do ecrã, aparece o menu de itens associados.  Clique na opção de menu **Proxies.**

    ![QlikSense][qs16]

14. O ecrã proxy aparece.  Clique no botão **Link** na parte inferior para ligar um proxy ao proxy virtual.

    ![QlikSense][qs17]

15. Selecione o nó proxy que irá suportar esta ligação de procuração virtual e clique no botão **Link.**  Após a ligação, o representante será listado sob proxies associados.

    ![QlikSense][qs18]
  
    ![QlikSense][qs19]

16. Após cerca de cinco a dez segundos, aparecerá a mensagem Refresh QMC.  Clique no botão **Refresh QMC.**

    ![QlikSense][qs20]

17. Quando o QMC se refrescar, clique no item do menu de **proxies virtuais.** A nova entrada de procuração virtual SAML está listada na tabela no ecrã.  Clique na entrada virtual de procuração.

    ![QlikSense][qs51]

18. Na parte inferior do ecrã, o botão de metadados SP de download será ativado.  Clique no botão de **dados SP de download** para guardar os metadados para um ficheiro.

    ![QlikSense][qs52]

19. Abra o ficheiro de metadados sp.  Observe a entrada **da entidade ID** e a entrada Do Serviço de **CréditoConsumerService.**  Estes valores são equivalentes ao **Identificador,** **Sign on URL** e o URL de **resposta** na configuração da aplicação Azure AD. Colhe estes valores na secção De domínio e **URLs** da Empresa Qlik Sense na configuração da aplicação Azure AD se não estiverem a corresponder, então deve substituí-los no assistente de configuração da App AD Azure.

    ![QlikSense][qs53]

### <a name="create-qlik-sense-enterprise-test-user"></a>Criar o utilizador de teste Qlik Sense Enterprise

A Qlik Sense Enterprise suporta o **fornecimento just-in-time**, os utilizadores adicionados automaticamente ao repositório 'USERS' da Qlik Sense Enterprise à medida que utilizam a funcionalidade SSO. Além disso, os clientes podem usar o QMC e criar um UDC (User Directory Connector) para utilizadores pré-povoados na Qlik Sense Enterprise a partir do seu LDAP de eleição, como Ative Directory, e outros.

### <a name="test-sso"></a>Teste SSO

Ao selecionar o azulejo Qlik Sense Enterprise no Painel de Acesso, deve ser automaticamente inscrito na Qlik Sense Enterprise para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](/azure/active-directory/manage-apps/what-is-single-sign-on) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

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
