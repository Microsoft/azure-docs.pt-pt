---
title: 'Tutorial: Integração do Diretório Ativo Azure com a Palo Alto Networks - Admin UI [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e a Palo Alto Networks - Admin UI.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: a826eaec-15af-4c85-8855-8a3374d1efb9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 03/12/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9804a44a29f4540c28ec4e1eb6927e65af70218c
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83682957"
---
# <a name="tutorial-azure-active-directory-integration-with-palo-alto-networks---admin-ui"></a>Tutorial: Integração de Diretório Sonícola Azure com redes Palo Alto - Admin UI

Neste tutorial, aprende-se a integrar a Palo Alto Networks - Admin UI com o Azure Ative Directory (Azure AD).
Integrando as Redes Palo Alto - A Admin UI com a Azure AD proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso a Palo Alto Networks - Admin UI.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos na Palo Alto Networks - Admin UI (Single Sign-On) com as suas contas Azure AD.
* Você pode gerir suas contas em um local central - o portal Azure.

Se quiser saber mais detalhes sobre a integração de apps saaS com a Azure AD, consulte [o que é o acesso à aplicação e o único registo com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).
Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da AD Azure com a Palo Alto Networks - Admin UI, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver um ambiente de AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Palo Alto Networks - Assinatura única de assinatura ativada pela Admin UI

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o único sinal de Azure AD num ambiente de teste.

* Palo Alto Networks - Admin UI apoia **SP** iniciado SSO
* Palo Alto Networks - Admin UI suporta o provisionamento de utilizadores **justo no tempo**

## <a name="adding-palo-alto-networks---admin-ui-from-the-gallery"></a>Adicionar Redes Palo Alto - Admin UI da galeria

Para configurar a integração da Palo Alto Networks - Admin UI em Azure AD, você precisa adicionar Palo Alto Networks - Admin UI da galeria para a sua lista de aplicações geridas saaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No Add da secção **galeria,** **escreva Redes Palo Alto - Admin UI** na caixa de pesquisa.
1. Selecione **Palo Alto Networks - Admin UI** do painel de resultados e, em seguida, adicione a app. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Nesta secção, configura e testa o único sign-on azure ad com a Palo Alto Networks - Admin UI com base num utilizador de teste chamado **B.Simon**.
Para que o único início de sessão funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado nas Redes Palo Alto - A Admin UI tem de ser estabelecida.

Para configurar e testar o único sign-on azure ad com a Palo Alto Networks - Admin UI, você precisa completar os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    * **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
    * Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
1. **[Configure Palo Alto Networks - Admin UI SSO](#configure-palo-alto-networks---admin-ui-sso)** - para configurar as definições de inscrição únicas no lado da aplicação.
    * **[Create Palo Alto Networks - Admin UI test user](#create-palo-alto-networks---admin-ui-test-user)** - para ter uma contrapartida de B.Simon em Palo Alto Networks - Admin UI que está ligada à representação da AD Azure do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Nesta secção, permite o único sinal de entrada do Azure AD no portal Azure.

Para configurar o único sign-on azure ad com a Palo Alto Networks - Admin UI, execute os seguintes passos:

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações da Empresa De Internet **de Palo Alto - Administração UI,** selecione **Single sign-on**.

    ![Configurar um único link de sinalização](common/select-sso.png)

1. No diálogo **Select a Single sign-on,** selecione o modo **SAML/WS-Fed** para ativar um único sinal.

    ![Modo de seleção de sinal único](common/select-saml-option.png)

1. No **set single sign-on com** a página SAML, clique no ícone **Editar** para abrir o diálogo básico de **configuração SAML.**

    ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na secção **Basic SAML Configuration,** execute os seguintes passos:

    a. Na caixa de texto **de URL sign-on,** escreva um URL utilizando o seguinte padrão:`https://<Customer Firewall FQDN>/php/login.php`

    b. Na caixa **de identificador,** digite um URL utilizando o seguinte padrão:`https://<Customer Firewall FQDN>:443/SAML20/SP`

    c. Na caixa de texto **URL resposta,** digite o URL do Serviço de Consumidores de Afirmação (ACS) no seguinte formato:`https://<Customer Firewall FQDN>:443/SAML20/SP/ACS`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL, Identificador e Resposta real. Contacte a Equipa de Apoio ao [Cliente da Palo Alto - Admin UI](https://support.paloaltonetworks.com/support) para obter estes valores. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.
    >
    > A porta 443 é necessária no **Identificador** e no URL de **resposta,** uma vez que estes valores estão codificados na Firewall palo Alto. A remoção do número da porta resultará num erro durante o início de sessão se for removido.

    > A porta 443 é necessária no **Identificador** e no URL de **resposta,** uma vez que estes valores estão codificados na Firewall palo Alto. A remoção do número da porta resultará num erro durante o início de sessão se for removido.

1. A aplicação PureCloud by Genesys espera as afirmações do SAML num formato específico, o que requer que adicione mapeamentos personalizados de atributos à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos.

    ![image](common/default-attributes.png)

   > [!NOTE]
   > Como os valores do atributo são apenas exemplos, mapeie os valores apropriados para o nome de *utilizador* e *a administração.* Há outro atributo opcional, *o accessdomain,* que é usado para restringir o acesso administrativo a sistemas virtuais específicos na firewall.

1. Além de acima, a aplicação PureCloud by Genesys espera que poucos atributos sejam passados de volta na resposta SAML que são mostradas abaixo. Estes atributos também são pré-povoados, mas pode revê-los de acordo com os seus requisitos.

    | Name |  Atributo fonte|
    | --- | --- |
    | o nome de utilizador | user.userprincipalname |
    | adminrole | customadmin |
    | | |

    > [!NOTE]
    > Para obter mais informações sobre os atributos, consulte os seguintes artigos:
    > * [Perfil de função administrativa para a Admin UI (administrador)](https://www.paloaltonetworks.com/documentation/80/pan-os/pan-os/firewall-administration/manage-firewall-administrators/configure-an-admin-role-profile)
    > * [Domínio de acesso ao dispositivo para Admin UI (domínio de acesso)](https://docs.paloaltonetworks.com/pan-os/8-0/pan-os-web-interface-help/device/device-access-domain.html)

1. Na configuração de um único sign-on com a página **SAML,** na secção Certificado de **Assinatura SAML,** clique em **Baixar** para descarregar o **Federation Metadata XML** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/metadataxml.png)

1. Na **configuração das Redes Palo Alto - Secção De UI de Administrador,** copie os URL(s) adequados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador de anúncio sinuoso

    c. Logout URL

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD

Nesta secção, você vai criar um utilizador de teste no portal Azure chamado B.Simon.

1. A partir do painel esquerdo no portal Azure, **selecione Azure Ative Directory**, selecione **Utilizadores**e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo de **nome do Utilizador,** introduza o username@companydomain.extension . Por exemplo, `B.Simon@contoso.com`.
   1. Selecione a caixa de verificação de **palavra-passe do Show** e, em seguida, escreva o valor que está apresentado na caixa **password.**
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, você permitirá que B.Simon use o único sign-on Azure, concedendo acesso à Palo Alto Networks - Admin UI.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Palo Alto Networks - Admin UI**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

### <a name="configure-palo-alto-networks---admin-ui-sso"></a>Configure Palo Alto Networks - Admin UI SSO

1. Abra a Internet de Internet de Redes Palo Alto como administrador numa nova janela.

2. Selecione o separador **Dispositivo.**

    ![O separador dispositivo](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_admin1.png)

3. No painel esquerdo, selecione O Fornecedor de **Identidade SAML**e, em seguida, selecione **Import** para importar o ficheiro de metadados.

    ![Botão de ficheiro de metadados de importação](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_admin2.png)

4. Na janela de importação de perfil do servidor de identificação do **fornecedor SAML,** faça o seguinte:

    ![A janela "SAML Identificar o Perfil de Servidor do Fornecedor"](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_idp.png)

    a. Na caixa De Nome de **Perfil,** forneça um nome (por exemplo, **AzureAD Admin UI**).

    b. Em metadados do **Fornecedor de Identidade,** selecione **Browse**, e selecione o ficheiro metadata.xml que descarregou anteriormente a partir do portal Azure.

    c. Limpe a caixa de verificação de certificado de fornecedor de **identidade de validação.**

    d. Selecione **OK**.

    e. Para comprometer as configurações na firewall, selecione **Commit**.

5. No painel esquerdo, selecione O Fornecedor de **Identidade SAML,** e, em seguida, selecione o Perfil de Fornecedor de Identidade SAML (por exemplo, **AzureAD Admin UI**) que criou na etapa anterior.

    ![O perfil do fornecedor de identidade SAML](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_idp_select.png)

6. Na janela de perfil do servidor do fornecedor de **identidade SAML,** faça o seguinte:

    ![A janela "SAML Identity Provider Server Profile"](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_slo.png)
  
    a. Na caixa URL do Fornecedor de **Identidade SLO,** substitua o URL SLO previamente importado pelo seguinte URL:`https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0`
  
    b. Selecione **OK**.

7. No Palo Alto Networks Firewall's Admin UI, selecione **Device**, e, em seguida, selecione **Funções de Administrador**.

8. Selecione o botão **Adicionar.**

9. Na janela De Perfil de **Papel administrador,** na caixa **nome,** forneça um nome para o papel de administrador (por exemplo, **fwadmin).** O nome do papel do administrador deve corresponder ao nome de atributo da SAML Admin Role que foi enviado pelo Fornecedor de Identidade. O nome e valor do administrador foram criados na secção **Deatributos** do Utilizador no portal Azure.

    ![Configure Palo Alto Networks Admin Role](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_adminrole.png)
  
10. No UI de Administração da Firewall, selecione **Dispositivo**, e, em seguida, selecione **Perfil de Autenticação**.

11. Selecione o botão **Adicionar.**

12. Na janela Perfil de **Autenticação,** faça o seguinte: 

    ![A janela "Perfil de Autenticação"](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_authentication_profile.png)

    a. Na caixa **Nome,** forneça um nome (por exemplo, **AzureSAML_Admin_AuthProfile).**

    b. Na lista de drop-down **do Tipo,** selecione **SAML**. 

    c. Na lista de abandono do perfil do **servidor idP,** selecione o perfil de servidor de servidor de identidade SAML apropriado (por exemplo, **AzureAD Admin UI**).

    c. Selecione a caixa de verificação **de logout single ativa.**

    d. Na caixa de atribuição de **funções de administrador,** introduza o nome do atributo (por exemplo, **administrador).**

    e. Selecione o separador **Avançado** e, em seguida, em **Lista de Permitir,** selecione **Adicionar**.

    ![O botão Adicionar no separador Avançado](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_allowlist.png)

    f. Selecione a caixa de verificação **All** ou selecione os utilizadores e grupos que podem autenticar com este perfil.  
    Quando um utilizador autentica, a firewall corresponde ao nome de utilizador ou grupo associado sintetizador com as entradas desta lista. Se não adicionar entradas, nenhum utilizador pode autenticar.

    g. Selecione **OK**.

13. Para permitir que os administradores utilizem o SSO **Device**SAML utilizando o Azure, selecione  >  **Configuração**do dispositivo . No painel de **configuração,** selecione o separador **'Gestão'** e, em seguida, em definições de **autenticação,** selecione o botão **Definições** ("engrenagem").

    ![O botão Definições](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_authsetup.png)

14. Selecione o perfil de autenticação SAML que criou na janela do Perfil de Autenticação (por exemplo, **AzureSAML_Admin_AuthProfile).**

    ![O campo perfil de autenticação](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_authsettings.png)

15. Selecione **OK**.

16. Para comprometer a configuração, selecione **Commit**.

### <a name="create-palo-alto-networks---admin-ui-test-user"></a>Criar redes Palo Alto - Utilizador de teste de UI Admin

Palo Alto Networks - Admin UI suporta o fornecimento de utilizadores just-in-time. Se um utilizador ainda não existir, é automaticamente criado no sistema após uma autenticação bem sucedida. Não é necessária qualquer ação para criar o utilizador.

### <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Ao clicar nas Redes Palo Alto - Entrada De UI de Administrador no Painel de Acesso, deve ser automaticamente inscrito nas Redes Palo Alto - Admin UI para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente redes Palo Alto - Admin UI com Azure AD](https://aad.portal.azure.com/)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Como proteger as Redes Palo Alto - Admin UI com visibilidade e controlos avançados](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)