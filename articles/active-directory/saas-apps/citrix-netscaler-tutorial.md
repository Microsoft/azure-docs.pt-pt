---
title: 'Tutorial: Integração do Active Directory do Azure com o Citrix Netscaler | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e o Citrix Netscaler.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: af501bd0-8ff5-468f-9b06-21e607ae25de
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: ca5fec00d264bb50c75338d36ec793946f495bff
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58227452"
---
# <a name="tutorial-azure-active-directory-integration-with-citrix-netscaler"></a>Tutorial: Integração do Active Directory do Azure com o Citrix Netscaler

Neste tutorial, saiba como integrar o Citrix Netscaler com o Azure Active Directory (Azure AD).
Integrar o Citrix Netscaler no Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao Citrix Netscaler.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para Citrix Netscaler (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o Citrix Netscaler, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter a versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Citrix Netscaler logon único habilitado subscrição

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Oferece suporte a Citrix Netscaler **SP** iniciada SSO

* Oferece suporte a Citrix Netscaler **Just In Time** aprovisionamento de utilizadores

## <a name="adding-citrix-netscaler-from-the-gallery"></a>Adicionando o Citrix Netscaler da Galeria

Para configurar a integração do Citrix Netscaler no Azure AD, terá de adicionar Citrix Netscaler a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar Citrix Netscaler a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **Citrix Netscaler**, selecione **Citrix Netscaler** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![Citrix Netscaler na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com o Citrix Netscaler com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Citrix Netscaler deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com o Citrix Netscaler, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar a Citrix Netscaler início de sessão único](#configure-citrix-netscaler-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste do Citrix Netscaler](#create-citrix-netscaler-test-user)**  - para ter um equivalente da Eduarda Almeida na Citrix Netscaler que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com o Citrix Netscaler, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **Citrix Netscaler** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, execute os seguintes passos:

    ![Citrix Netscaler domínio e URLs únicas início de sessão em informações](common/sp-identifier-reply.png)

    a. Na **iniciar sessão no URL** caixa de texto, escreva um URL com o seguinte padrão: `https://<<Your FQDN>>/CitrixAuthService/AuthService.asmx`
    
    b. Na **identificador (ID de entidade)** caixa de texto, escreva um URL com o seguinte padrão: `https://<<Your FQDN>>`

    c. Na **URL de resposta (URL do serviço de consumidor de asserção)** caixa de texto, escreva um URL com o seguinte padrão: `https://<<Your FQDN>>/CitrixAuthService/AuthService.asmx`
    
    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o início de sessão real URL e o identificador. Contacte [equipa de suporte de cliente do Citrix Netscaler](https://www.citrix.com/contact/technical-support.html) obter esses valores. Também pode consultar os padrões mostrados a **configuração básica de SAML** secção no portal do Azure.

    > [!NOTE]
    > Para começar a trabalhar de SSO, estes URLs devem ser acessíveis a partir de sites públicos. Tem de ativar a firewall ou outras definições de segurança no lado do Netscaler para enble do Azure AD para publicar o token no URL ACS configurado.

5. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **XML de metadados de Federação**  entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/metadataxml.png)

6. Sobre o **configurar a Citrix Netscaler** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão

### <a name="configure-citrix-netscaler-single-sign-on"></a>Configurar a Citrix Netscaler início de sessão único

1. Numa janela do browser web diferente, início de sessão no seu inquilino do Citrix Netscaler como administrador.

2. Certifique-se de que o **versão de Firmware do NetScaler = NS12.1: Criar 48.13.nc**.

    ![Configurar o início de sessão único](./media/citrix-netscaler-tutorial/configure01.png)

3. Sobre o **VPN Virtual Server** página, execute os seguintes passos:

     ![Configurar o início de sessão único](./media/citrix-netscaler-tutorial/configure02.png)

    a. Configurar as definições do Gateway **ICA só** como **verdadeiro**.
    
    b. Definir **ativar autenticação** como **verdadeiro**.
    
    c. **DTLS** é opcional.
    
    d. Certifique-se **SSLv3** como **desativado**.

4. Um personalizado **cifras SSL** grupo é criado para atingir A + no https://www.ssllabs.com conforme mostrado abaixo:

    ![Configurar o início de sessão único](./media/citrix-netscaler-tutorial/configure03.png)

5. Sobre o **configurar o servidor de autenticação SAML** página, execute os seguintes passos:

      ![Configurar o início de sessão único](./media/citrix-netscaler-tutorial/configure04.png)

    a. Na **nome** caixa de texto, escreva o nome do seu servidor.

    b. Na **URL de redirecionamento** caixa de texto, cole o valor de **URL de início de sessão** que copiou do portal do Azure.

    c. Na **URL de fim de sessão único** caixa de texto, cole o valor de **URL de fim de sessão** que copiou do portal do Azure.

    d. Na **nome do certificado de IDP**, clique nas **"+"** iniciar sessão para adicionar o certificado que transferiu do portal do Azure. Após ser carregada, selecione o certificado na lista pendente.

    e. Seguir mais campos tem de ser definido nesta página

      ![Configurar o início de sessão único](./media/citrix-netscaler-tutorial/configure24.png)

    f. Selecione **solicitado o contexto de autenticação** como **exata**.

    g. Selecione **algoritmo de assinatura** como **RSA-SHA256**.

    h. Selecione **resumir o método** como **SHA256**.

    i. Verifique **impor o nome de utilizador**.

    j. Clique em **OK**

6. Para configurar o **sessão perfil**, execute os seguintes passos:

    ![Configurar o início de sessão único](./media/citrix-netscaler-tutorial/configure06.png)

    a. Na **nome** caixa de texto, escreva o nome do seu perfil de sessão.

    b. Sobre o **experiência de cliente** separador, faça as alterações, conforme mostrado na captura de ecrã abaixo.

    c. Continuar a fazer as alterações no **guia General** conforme mostrado abaixo e clique em **OK**

    ![Configurar o início de sessão único](./media/citrix-netscaler-tutorial/configure07.png)

    d. Sobre o **aplicativos publicados** separador, faça as alterações, conforme mostrado na captura de ecrã abaixo e clique em **OK**.

    ![Configurar o início de sessão único](./media/citrix-netscaler-tutorial/configure08.png)

    e. Sobre o **Security** separador, faça as alterações, conforme mostrado na captura de ecrã abaixo e clique em **OK**.

    ![Configurar o início de sessão único](./media/citrix-netscaler-tutorial/configure09.png)

7. Tornar as ligações de ICA ligar na porta de confiabilidade de sessão **2598** como mostra a captura de ecrã abaixo.

    ![Configurar o início de sessão único](./media/citrix-netscaler-tutorial/configure10.png)

8. Sobre o **SAML** secção, adicione o **servidores** conforme mostrado na captura de ecrã abaixo.

    ![Configurar o início de sessão único](./media/citrix-netscaler-tutorial/configure11.png)

9. Sobre o **SAML** secção, adicione o **políticas** conforme mostrado na captura de ecrã abaixo.

     ![Configurar o início de sessão único](./media/citrix-netscaler-tutorial/configure12.png)

10. Na **definições globais** página, vá para o **então sem cliente acesso** secção.

    ![Configurar o início de sessão único](./media/citrix-netscaler-tutorial/configure13.png)

11. Sobre o **configuração** separador, execute os seguintes passos:

    ![Configurar o início de sessão único](./media/citrix-netscaler-tutorial/configure14.png)

    a. Selecione **permitir domínios**.

    b. Na **nome de domínio** caixa de texto, selecione o domínio.

    c. Clique em **OK**.

12. Tornar o **Vitrine** definições a **Recetor para Web Sites** conforme mostrado na captura de ecrã abaixo:

    ![Configurar o início de sessão único](./media/citrix-netscaler-tutorial/configure15.png)

13. Sobre o **gerir métodos de autenticação - Corp** pop-up, execute os seguintes passos:

    ![Configurar o início de sessão único](./media/citrix-netscaler-tutorial/configure16.png)

    a. Selecione **nome de utilizador e palavra-passe**.

    b. Selecione **pass-through do Gateway do NetScaler**.

    c. Clique em **OK**.

14. Sobre o **configurar domínios fidedignos** pop-up, execute os seguintes passos:

    ![Configurar o início de sessão único](./media/citrix-netscaler-tutorial/configure17.png)

    a. Selecione **apenas domínios fidedignos**.

    b. Clique em **Add** para adicionar o seu domínio na **domínios fidedignos** caixa de texto.

    c. Selecione o domínio predefinido da sua **domínio predefinido** lista.

    d. Selecione **Mostrar a lista de domínios na página de início de sessão**.

    e. Clique em **OK**.

15. Sobre o **gerir Gateways do NetScaler** pop-up, execute os seguintes passos:

    ![Configurar o início de sessão único](./media/citrix-netscaler-tutorial/configure18.png)

    a. Clique em **Add** para adicionar os Gateways do NetScaler no **NetScaler Gateways** caixa de texto.

    b. Clique em **Fechar**.

16. Sobre o **Settings do Vitrine** separador, execute os seguintes passos:

    ![Configurar o início de sessão único](./media/citrix-netscaler-tutorial/configure19.png)

    a. Na **nome a apresentar** caixa de texto, escreva o nome de Gateway do NetScaler.

    b. Na **URL de Gateway do NetScaler** caixa de texto introduza o URL do Gateway NetScaler.

    c. Selecione **utilização ou de função** como **autenticação e HDX encaminhamento**.

    d. Clique em **OK**.

17. Sobre o **autoridade de pedido de suporte de proteger Vitrine** separador, execute os seguintes passos:

    ![Configurar o início de sessão único](./media/citrix-netscaler-tutorial/configure20.png)

    a. Clique em **Add** botão para adicionar seu **do proteger o pedido de URL da autoridade** na caixa de texto.

    b. Selecione **ativar a confiabilidade de sessão**.

    c. Clique em **OK**.

18. Sobre o **definições de autenticação de loja** separador, execute os seguintes passos:

    ![Configurar o início de sessão único](./media/citrix-netscaler-tutorial/configure21.png)

    a. Selecione seu **versão**.

    b. Selecione **tipo de início de sessão** como **domínio**.

    c. Introduza o seu **URL de retorno de chamada**.

    d. Clique em **OK**.

19. Sobre o **Vitrine implementar Citrix Receiver** separador, execute os seguintes passos:

    ![Configurar o início de sessão único](./media/citrix-netscaler-tutorial/configure22.png)

    a. Selecione **opção de implementação** como **Recetor de utilização para HTML5, se o Recetor local não está disponível**.

    b. Clique em **OK**.

20. Sobre o **gerir Beacons** pop-up, execute os seguintes passos:

    ![Configurar o início de sessão único](./media/citrix-netscaler-tutorial/configure23.png)

    a. Selecione o **sinalização interna** como **utilize o URL do serviço**.

    b. Clique em **Add** para adicionar o seu URL na **externo beacons** caixa de texto.

    c. Clique em **OK**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD 

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

1. No portal do Azure, no painel esquerdo, selecione **do Azure Active Directory**, selecione **utilizadores**e, em seguida, selecione **todos os utilizadores**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](common/users.png)

2. Selecione **novo utilizador** na parte superior do ecrã.

    ![Novo utilizador botão](common/new-user.png)

3. Nas propriedades do utilizador, execute os seguintes passos.

    ![A caixa de diálogo de utilizador](common/user-properties.png)

    a. Na **Name** campo introduza **BrittaSimon**.
  
    b. Na **nome de utilizador** tipo de campo **brittasimon@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione **palavra-passe de Show** caixa de verificação e, em seguida, anote o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso a Citrix Netscaler.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **Citrix Netscaler**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Citrix Netscaler**.

    ![A ligação de Citrix Netscaler na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-citrix-netscaler-test-user"></a>Criar utilizador de teste do Citrix Netscaler

Nesta secção, um usuário chamado Eduarda Almeida é criado no Citrix Netscaler. Citrix Netscaler suporta o aprovisionamento de utilizadores de just-in-time, que está ativado por predefinição. Não existe nenhum item de ação para nesta secção. Se um utilizador já não existir no Citrix Netscaler, é criado um novo após a autenticação.

>[!NOTE]
>Se precisar de criar manualmente um utilizador, terá de contactar o [equipa de suporte de cliente do Citrix Netscaler](https://www.citrix.com/contact/technical-support.html).

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico da Citrix Netscaler no painel de acesso, deve ser automaticamente sessão iniciada no Citrix Netscaler para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [ Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicações e início de sessão único com o Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

