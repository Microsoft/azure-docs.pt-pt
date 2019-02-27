---
title: 'Tutorial: Integração do Active Directory do Azure com o da Palo Alto Networks - interface do Usuário de administrador | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e da Palo Alto Networks - interface do Usuário de administrador.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: a826eaec-15af-4c85-8855-8a3374d1efb9
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/02/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 82560d0767b6865dded3e14e661fe89b7132ab95
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56869858"
---
# <a name="tutorial-azure-active-directory-integration-with-palo-alto-networks---admin-ui"></a>Tutorial: Integração do Active Directory do Azure com o da Palo Alto Networks - interface do Usuário de administrador

Neste tutorial, saiba como integrar da Palo Alto Networks - interface do Usuário de administrador no Azure Active Directory (Azure AD).
Integração da Palo Alto Networks - interface do Usuário de administrador com o Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso da Palo Alto Networks - interface do Usuário de administrador.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para Palo Alto Networks - Admin da interface do Usuário (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com da Palo Alto Networks - interface do Usuário de administrador, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter a versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Da Palo Alto Networks - interface do Usuário de administrador logon único habilitado subscrição

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Suporta a interface do Usuário do administrador da Palo Alto Networks - **SP** iniciada SSO
* Suporta a interface do Usuário do administrador da Palo Alto Networks - **Just In Time** aprovisionamento de utilizadores

## <a name="adding-palo-alto-networks---admin-ui-from-the-gallery"></a>Adição da Palo Alto Networks - interface do Usuário de administração da Galeria

Para configurar a integração da Palo Alto Networks - interface do Usuário de administrador no Azure AD, terá de adicionar da Palo Alto Networks - Admin da interface do Usuário da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar da Palo Alto Networks - interface do Usuário de administração da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **Palo Alto Networks - interface do Usuário de administrador**, selecione **Palo Alto Networks - interface do Usuário de administrador** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![Da Palo Alto Networks - interface do Usuário de administrador na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configurar e testar o Azure AD início de sessão único da Palo Alto Networks - interface do Usuário de administração com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único para o trabalho, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado da Palo Alto Networks - interface do Usuário de administrador tem de ser estabelecida.

Para configurar e testar o Azure AD início de sessão único da Palo Alto Networks - interface do Usuário de administrador, precisa concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar da Palo Alto Networks - Admin da interface do Usuário início de sessão único](#configure-palo-alto-networks---admin-ui-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar da Palo Alto Networks - utilizador de teste de interface do Usuário de administrador](#create-palo-alto-networks---admin-ui-test-user)**  - para ter um equivalente da Eduarda Almeida na Palo Alto Networks - interface do Usuário de administrador que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único da Palo Alto Networks - interface do Usuário de administrador, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **Palo Alto Networks - interface do Usuário de administrador** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, execute os seguintes passos:

    ![Da Palo Alto Networks - domínio de interface do Usuário de administrador e URLs únicas início de sessão em informações](common/sp-identifier-reply.png)

    a. Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão: `https://<Customer Firewall FQDN>/php/login.php`

    b. Na **identificador** caixa, escreva um URL com o seguinte padrão: `https://<Customer Firewall FQDN>:443/SAML20/SP`

    c. Na **URL de resposta** caixa de texto, escreva o URL de serviço de consumidor de asserção (ACS) no seguinte formato: `https://<Customer Firewall FQDN>:443/SAML20/SP/ACS`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de início de sessão, identificador e o URL de resposta real. Contacte [Palo Alto Networks - equipa de suporte de cliente de interface do Usuário administrativo](https://support.paloaltonetworks.com/support) obter esses valores. Também pode consultar os padrões mostrados a **configuração básica de SAML** secção no portal do Azure.

5. Da Palo Alto Networks - aplicação de interface do Usuário de administrador espera que as asserções SAML num formato específico. Configure as seguintes declarações para esta aplicação. Pode gerir os valores destes atributos do **atributos de utilizador** secção na página de integração de aplicações. Sobre o **definido no início de sessão único com o SAML** página, clique em **editar** botão para abrir **atributos de utilizador** caixa de diálogo.

    ![image](common/edit-attribute.png)

   > [!NOTE]
   > Como os valores de atributo são apenas exemplos, mapear os valores adequados para *nome de utilizador* e *adminrole*. Há outro atributo opcional, *accessdomain*, que é utilizado para restringir o acesso de administrador para sistemas virtuais específicos na firewall.
   >

6. No **afirmações de utilizador** secção sobre o **atributos de utilizador** caixa de diálogo, configurar o atributo de token de SAML conforme mostrado na imagem acima e execute os seguintes passos:

    | Name |  Atributo de origem|
    | --- | --- |
    | o nome de utilizador | user.userprincipalname |
    | adminrole | customadmin |
    | | |

    a. Clique em **Adicionar nova afirmação** para abrir o **afirmações de utilizador de gerir** caixa de diálogo.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Na **nome** caixa de texto, escreva o nome de atributo apresentado para essa linha.

    c. Deixe o **espaço de nomes** em branco.

    d. Selecione a origem de dado **atributo**.

    e. Partir do **atributo de origem** lista, digite o valor de atributo apresentado para essa linha.

    f. Clique em **Ok**

    g. Clique em **Guardar**.

    > [!NOTE]
    > Para obter mais informações sobre os atributos, consulte os artigos seguintes:
    > * [Perfil de função administrativa para a interface do Usuário de administrador (adminrole)](https://www.paloaltonetworks.com/documentation/80/pan-os/pan-os/firewall-administration/manage-firewall-administrators/configure-an-admin-role-profile)
    > * [Domínio de acesso do dispositivo para a interface do Usuário de administrador (accessdomain)](https://www.paloaltonetworks.com/documentation/80/pan-os/web-interface-help/device/device-access-domain)

7. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **XML de metadados de Federação**  entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/metadataxml.png)

8. Sobre o **configurar da Palo Alto Networks - interface do Usuário de administrador** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Azure Ad Identifier

    c. URL de fim de sessão

### <a name="configure-palo-alto-networks---admin-ui-single-sign-on"></a>Configurar da Palo Alto Networks - interface do Usuário de administrador início de sessão único

1. Abra a interface de Usuário de administrador de Firewall de redes da Palo Alto como um administrador numa nova janela.

2. Selecione o **dispositivo** separador.

    ![O separador de dispositivo](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_admin1.png)

3. No painel esquerdo, selecione **fornecedor de identidade de SAML**e, em seguida, selecione **importar** para importar o ficheiro de metadados.

    ![O botão Importar do ficheiro de metadados](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_admin2.png)

4. Na **SAML identificar fornecedor de perfil de importação do servidor** janela, efetue o seguinte procedimento:

    ![A janela de "SAML identificar fornecedor perfil importação do servidor"](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_idp.png)

    a. Na **nome do perfil** caixa, indique um nome (por exemplo, **IU de Admin do AzureAD**).
    
    b. Sob **metadados de fornecedor de identidade**, selecione **procurar**e selecione o arquivo METADATA XML que transferiu anteriormente no portal do Azure.
    
    c. Limpar o **Validar certificado do fornecedor de identidade** caixa de verificação.
    
    d. Selecione **OK**.
    
    e. Para consolidar as configurações na firewall, selecione **consolidação**.

5. No painel esquerdo, selecione **fornecedor de identidade de SAML**e, em seguida, selecione o perfil de fornecedor de identidade de SAML (por exemplo, **IU de Admin do AzureAD**) que criou no passo anterior.

    ![O perfil de fornecedor de identidade SAML](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_idp_select.png)

6. Na **perfil de servidor de fornecedor de identidade de SAML** janela, efetue o seguinte procedimento:

    ![A janela de "Perfil do servidor de fornecedor de identidade SAML"](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_slo.png)
  
    a. Na **URL de SLO do fornecedor de identidade** caixa, substitua o URL de SLO importado anteriormente com o seguinte URL: `https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0`
  
    b. Selecione **OK**.

7. Na IU do Admin da Palo Alto redes Firewall, selecione **dispositivo**e, em seguida, selecione **funções de administrador**.

8. Selecione o **adicionar** botão.

9. Na **perfil de função de administrador** janela, na **nome** caixa, indique um nome para a função de administrador (por exemplo, **fwadmin**). O nome da função de administrador deve corresponder o nome de atributo de função de administrador de SAML que lhe foi enviado pelo fornecedor de identidade. O nome da função de administrador e o valor que foram criados no **atributos de utilizador** secção no portal do Azure.

    ![Configurar a função de administrador de redes da Palo Alto](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_adminrole.png)
  
10. Na IU de administração do Firewall, selecione **dispositivo**e, em seguida, selecione **perfil de autenticação**.

11. Selecione o **adicionar** botão.

12. Na **perfil de autenticação** janela, efetue o seguinte procedimento: 

    ![A janela de "Perfil de autenticação"](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_authentication_profile.png)

    a. Na **Name** caixa, indique um nome (por exemplo, **AzureSAML_Admin_AuthProfile**).

    b. Na **tipo** na lista pendente, selecione **SAML**. 

    c. Na **perfil de servidor do IdP** lista pendente, selecione o perfil adequado do servidor do fornecedor de identidade de SAML (por exemplo, **IU de Admin do AzureAD**).

    c. Selecione o **ativar a fim de sessão único** caixa de verificação.

    d. Na **atributos de função de administrador** , introduza o nome de atributo (por exemplo, **adminrole**).

    e. Selecione o **avançadas** separador e, em **lista de permissões**, selecione **Add**.

    ![Botão Adicionar na guia avançado](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_allowlist.png)

    f. Selecione o **todos os** caixa de verificação ou selecione os utilizadores e grupos que podem autenticar-se com este perfil.  
    Quando um utilizador é autenticado, o firewall corresponde o nome de utilizador associado ou o grupo em relação as entradas desta lista. Se não adicionar entradas, os utilizadores não podem autenticar.

    g. Selecione **OK**.

13. Para permitir que os administradores utilizem SAML SSO ao utilizar o Azure, selecione **dispositivo** > **configuração**. Na **programa de configuração** painel, selecione o **gestão** separador e, em **definições de autenticação**, selecione o **definições** botão ("engrenagem") .

    ![O botão de definições](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_authsetup.png)

14. Selecione o perfil de autenticação SAML que criou na janela do perfil de autenticação (por exemplo, **AzureSAML_Admin_AuthProfile**).

    ![O campo de perfil de autenticação](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_authsettings.png)

15. Selecione **OK**.

16. Para consolidar a configuração, selecione **consolidação**.

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

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para Palo Alto Networks - interface do Usuário de administrador.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **da Palo Alto Networks - interface do Usuário de administrador**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Palo Alto Networks - interface do Usuário de administrador**.

    ![Da Palo Alto Networks - ligação de interface do Usuário de administrador na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-palo-alto-networks---admin-ui-test-user"></a>Criar da Palo Alto Networks - utilizador de teste de interface do Usuário de administrador

Da Palo Alto Networks - interface do Usuário de administrador suporta o aprovisionamento de utilizador de just-in-time. Se um utilizador já não existir, será criado automaticamente no sistema após uma autenticação com êxito. Não é necessária ação do utilizador para criar o utilizador.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica em da Palo Alto Networks - mosaico de interface do Usuário de administrador no painel de acesso, deve ser automaticamente conectado para Palo Alto Networks - interface do Usuário de administrador para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
