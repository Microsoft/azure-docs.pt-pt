---
title: 'Tutorial: Integração do Active Directory do Azure com o Qlik sentido Enterprise | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e Qlik sentido empresarial.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 8c27e340-2b25-47b6-bf1f-438be4c14f93
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/17/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 54690386b3654952cc2292963c9bf01dc65e4315
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57875219"
---
# <a name="tutorial-azure-active-directory-integration-with-qlik-sense-enterprise"></a>Tutorial: Integração do Active Directory do Azure com o Qlik sentido Enterprise

Neste tutorial, saiba como integrar o Qlik sentido empresarial no Azure Active Directory (Azure AD).
Integrar o Qlik sentido empresarial no Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao Qlik sentido empresarial.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para a empresa de sentido Qlik (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Qlik sentido empresarial, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter a versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Qlik sentido empresarial logon único habilitado subscrição

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Suporta Qlik sentido Enterprise **SP** iniciada SSO

## <a name="adding-qlik-sense-enterprise-from-the-gallery"></a>Adicionando Qlik sentido empresarial da Galeria

Para configurar a integração do Qlik sentido Enterprise no Azure AD, terá de adicionar Qlik sentido empresarial a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar Qlik sentido empresarial a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **Qlik sentido empresarial**, selecione **Qlik sentido empresarial** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![Qlik sentido Enterprise na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com Qlik sentido empresarial com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado Qlik sentido empresa deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com Qlik sentido empresarial, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar Qlik sentido Enterprise Single Sign-On](#configure-qlik-sense-enterprise-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste Qlik sentido Enterprise](#create-qlik-sense-enterprise-test-user)**  - para ter um equivalente da Eduarda Almeida Qlik sentido empresa que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com Qlik sentido empresarial, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **Qlik sentido empresarial** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, execute os seguintes passos:

    ![Qlik sentido empresarial domínio e URLs únicas início de sessão em informações](common/sp-identifier-reply.png)

    a. Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão: `https://<Qlik Sense Fully Qualifed Hostname>:4443/azure/hub`

    b. Na **identificador** caixa de texto, escreva um URL com o seguinte padrão:

    | |
    |--|
    | `https://<Qlik Sense Fully Qualifed Hostname>.qlikpoc.com`|
    | `https://<Qlik Sense Fully Qualifed Hostname>.qliksense.com`|
    | |

    c. Na **URL de resposta** caixa de texto, escreva um URL com o seguinte padrão:

    `https://<Qlik Sense Fully Qualifed Hostname>:4443/samlauthn/`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de início de sessão real, do identificador e o URL de resposta, que são explicado posteriormente neste tutorial ou contacte [equipa de suporte de Qlik sentido Enterprise Client](https://www.qlik.com/us/services/support) obter esses valores.

5. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **XML de metadados de Federação**  entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/metadataxml.png)

### <a name="configure-qlik-sense-enterprise-single-sign-on"></a>Configurar Qlik Sense Enterprise início de sessão único

1. Prepare o ficheiro XML de metadados de Federação para que pode carregar que para o servidor Qlik Sense.

    > [!NOTE]
    > Antes de carregar os metadados do IdP para o servidor de Qlik Sense, o ficheiro tem de ser editadas para remover as informações para garantir a operação adequada entre o Azure AD e Qlik Sense server.

    ![QlikSense][qs24]

    a. Abra o ficheiro de FederationMetaData.xml que transferiu a partir do portal do Azure num editor de texto.

    b. Procure o valor **RoleDescriptor**.  Existem quatro entradas (dois pares de abertura e fecho de marcas de elemento).

    c. Elimine entre as etiquetas de RoleDescriptor e todas as informações do ficheiro.

    d. Guarde o ficheiro e mantê-lo nas proximidades para utilização posterior neste documento.

2. Navegue para o Qlik sentido Qlik Management Console (QMC) como um utilizador que pode criar as configurações de virtual proxy.

3. No QMC, clique nas **Virtual Proxies** item de menu.

    ![QlikSense][qs6]

4. Na parte inferior do ecrã, clique nas **criar novo** botão.

    ![QlikSense][qs7]

5. É apresentado o ecrã de edição de proxy Virtual.  No lado direito da tela é um menu para tornar as opções de configuração visível.

    ![QlikSense][qs9]

6. Com a opção de menu de identificação marcada, introduza as informações de identificação para a configuração de proxy virtual do Azure.

    ![QlikSense][qs8]  

    a. O **Descrição** campo é um nome amigável para a configuração de virtual proxy.  Introduza um valor para uma descrição.

    b. O **prefixo** campo identifica o ponto de extremidade do virtual proxy para ligar ao Qlik Sense com o Azure AD início de sessão único.  Introduza um nome de prefixo exclusivo para este proxy virtual.

    c. **Tempo limite de inatividade de sessão (minutos)** é o tempo limite para as ligações através deste proxy virtual.

    d. O **nome de cabeçalho do cookie de sessão** é o nome do cookie armazenar o identificador de sessão para a sessão de Qlik Sense um utilizador recebe após a autenticação com êxito.  Este nome tem de ser exclusivo.

7. Clique na opção de menu de autenticação para o tornar visível.  É apresentado o ecrã de autenticação.

    ![QlikSense][qs10]

    a. O **modo de acesso anônimo** pendente determina se os utilizadores anónimos podem aceder Qlik Sense através do proxy virtual.  A opção predefinida não é nenhum utilizador anónimo.

    b. O **método de autenticação** suspenso determina o esquema de autenticação do proxy virtual irá utilizar.  Selecione SAML da lista pendente.  Mais opções aparecem como resultado.

    c. Na **campo URI de anfitrião SAML**, os utilizadores de nome de anfitrião introduzirem para aceder ao Qlik Sense através deste proxy virtual SAML de entrada.  O nome do anfitrião é o uri do servidor Qlik Sense.

    d. Na **ID de entidade SAML**, introduza o mesmo valor que introduziu para o campo URI de anfitrião SAML.

    e. O **metadados de SAML IdP** é o ficheiro editado anteriormente na **Editar metadados de Federação a partir de configuração do Azure AD** secção.  **Antes de carregar os metadados de IdP, o ficheiro tem de ser editado** remover as informações para garantir a operação adequada entre o Azure AD e Qlik Sense server.  **Consulte as instruções acima se o ficheiro ainda não foi possível editá-lo.**  Se o ficheiro foi editado, clique no botão Procurar e selecione o ficheiro de metadados editado carregá-lo para a configuração de virtual proxy.

    f. Introduza a referência do atributo de nome ou o esquema para o atributo SAML que representa a **UserID** do Azure AD envia para o servidor de Qlik Sense.  Informações de referência de esquema estão disponíveis na configuração de postagem de ecrãs da aplicação do Azure.  Para utilizar o atributo de nome, introduza `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.

    g. Introduza o valor para o **diretório de utilizador** que será anexado aos utilizadores quando fizer a autenticação de servidor Qlik Sense através do Azure AD.  Valores embutidos em código tem de ser rodeados por **quadrado parêntesis Retos []**.  Para utilizar um atributo enviado na asserção de SAML do Azure AD, introduza o nome do atributo nesta caixa de texto **sem** Parênteses Retos.

    h. O **algoritmo de assinatura de SAML** define o certificado de fornecedor (neste servidor Qlik Sense maiúsculas) do serviço de assinatura para a configuração de virtual proxy.  Se o servidor de Qlik Sense utiliza um certificado fidedigno gerado usando o Microsoft RSA avançada e o fornecedor de criptografia AES, altere o algoritmo de assinatura de SAML para **SHA-256**.

    i. A seção de mapeamento do atributo SAML permite atributos adicionais, como grupos para ser enviado para Qlik Sense para utilização em regras de segurança.

8. Clique nas **BALANCEAMENTO de carga** opção de menu para torná-lo visível.  É apresentado o ecrã de balanceamento de carga.

    ![QlikSense][qs11]

9. Clique nas **adicionar novo nó de servidor** botão, o nó de motor selecione ou nós Qlik Sense enviará sessões para fins de balanceamento de carga e, clique no **Add** botão.

    ![QlikSense][qs12]

10. Clique na opção de menu avançado para torná-lo visível. É apresentado o ecrã avançado.

    ![QlikSense][qs13]

    A lista de permissões de anfitrião identifica os nomes de anfitrião são aceites quando ligar ao servidor Qlik Sense.  **Introduza o nome de anfitrião os utilizadores irão especificar quando ligar ao servidor Qlik Sense.** O nome do anfitrião é o mesmo valor que o uri de anfitrião SAML sem a https://.

11. Clique nas **aplicar** botão.

    ![QlikSense][qs14]

12. Clique em OK para aceitar a mensagem de aviso que indica a proxies ligados para o proxy virtual serão reiniciados.

    ![QlikSense][qs15]

13. No lado direito da tela, é apresentado o menu de itens associados.  Clique nas **Proxies** opção de menu.

    ![QlikSense][qs16]

14. É apresentado o ecrã de proxy.  Clique nas **Link** na parte inferior para ligar um proxy para o proxy virtual.

    ![QlikSense][qs17]

15. Selecione o nó de proxy que irá suportar esta ligação de virtual proxy e clique nas **Link** botão.  Depois de associar, o proxy será listado sob proxies associados.

    ![QlikSense][qs18]
  
    ![QlikSense][qs19]

16. Após cerca de cinco a dez segundos, será apresentada a mensagem de QMC atualizar.  Clique nas **QMC atualizar** botão.

    ![QlikSense][qs20]

17. Quando atualiza o QMC, clique nas **Virtual proxies** item de menu. A nova entrada de virtual proxy SAML está listada na tabela na tela.  Único clique na entrada virtual proxy.

    ![QlikSense][qs51]

18. Na parte inferior do ecrã, irá ativar o botão de metadados de transferir SP.  Clique nas **SP transferir metadados** botão para guardar os metadados para um ficheiro.

    ![QlikSense][qs52]

19. Abra o ficheiro de metadados de sp.  Observe a **entityID** entrada e o **AssertionConsumerService** entrada.  Estes valores são equivalentes para o **identificador**, **iniciar sessão no URL** e o **URL de resposta** na configuração da aplicação do Azure AD. Cole esses valores no **Qlik sentido empresarial domínio e URLs** secção na configuração da aplicação do Azure AD Se eles não correspondem, em seguida, deve substituir o Assistente de configuração de aplicações do Azure AD.

    ![QlikSense][qs53]

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD 

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

1. No portal do Azure, no painel esquerdo, selecione **do Azure Active Directory**, selecione **utilizadores**e, em seguida, selecione **todos os utilizadores**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](common/users.png)

2. Selecione **novo utilizador** na parte superior do ecrã.

    ![Novo utilizador botão](common/new-user.png)

3. Nas propriedades do utilizador, execute os seguintes passos.

    ![A caixa de diálogo de utilizador](common/user-properties.png)

    a. Na **Name** campo introduza **BrittaSimon**.
  
    b. Na **nome de utilizador** tipo de campo **brittasimon\@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione **palavra-passe de Show** caixa de verificação e, em seguida, anote o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar do Azure-início de sessão único, concedendo acesso para Qlik sentido empresarial.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **Qlik sentido empresarial**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, escreva e selecione **Qlik sentido empresarial**.

    ![A ligação de Qlik sentido Enterprise na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-qlik-sense-enterprise-test-user"></a>Criar utilizador de teste Qlik sentido empresarial

Nesta secção, vai criar um usuário chamado Eduarda Almeida no Qlik sentido empresarial. Trabalhar com [equipa de suporte da empresa de sentido Qlik](https://www.qlik.com/us/services/support) para adicionar os utilizadores na plataforma Qlik sentido empresarial. Os utilizadores tem de ser criados e ativados antes de utilizar o início de sessão único.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico Qlik sentido empresarial no painel de acesso, deve ser automaticamente conectado para a empresa de sentido Qlik, para que configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

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

