---
title: 'Tutorial: Integração do Azure Ative Directory com a Palo Alto Networks - Admin UI / Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e a Palo Alto Networks - Admin UI.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/10/2020
ms.author: jeedes
ms.openlocfilehash: 73b7b57aad43eea8e8d592d437185ca5c7e8a666
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91304662"
---
# <a name="tutorial-azure-active-directory-integration-with-palo-alto-networks---admin-ui"></a>Tutorial: Integração do Diretório Ativo Azure com a Palo Alto Networks - Admin UI

Neste tutorial, aprende-se a integrar a Palo Alto Networks - Admin UI com o Azure Ative Directory (Azure AD).
Integrar as Redes Palo Alto - ADmin UI com Azure AD proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso a Palo Alto Networks - Admin UI.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos nas Redes Palo Alto - Admin UI (Single Sign-On) com as suas contas AD Azure.
* Pode gerir as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AZure AD com a Palo Alto Networks - Admin UI, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Palo Alto Networks - Subscrição única ativada por Admin UI

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* Palo Alto Networks - Admin UI suporta **SP** iniciado SSO
* Palo Alto Networks - Admin UI suporta provisão de utilizadores **just in time**

## <a name="adding-palo-alto-networks---admin-ui-from-the-gallery"></a>Adicionar Palo Alto Networks - Admin UI da galeria

Para configurar a integração da Palo Alto Networks - Admin UI em Azure AD, é necessário adicionar a Palo Alto Networks - Admin UI da galeria à sua lista de aplicações geridas para o SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Add a partir da secção **de galeria,** **digite Palo Alto Networks - Admin UI** na caixa de pesquisa.
1. Selecione **Palo Alto Networks - Admin UI** do painel de resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso"></a>Configurar e testar Azure AD SSO

Nesta secção, configura e testa o Azure AD com a Palo Alto Networks - Admin UI com base num utilizador de teste chamado **B.Simon**.
Para um único sinal de saúde a funcionar, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado em Palo Alto Networks - Admin UI.

Para configurar e testar o Azure AD com a Palo Alto Networks - Admin UI, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    * Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    * **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure as Redes Palo Alto - Administrador UI SSO](#configure-palo-alto-networks---admin-ui-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    * **[Criar a Palo Alto Networks - utilizador de testes Admin UI](#create-palo-alto-networks---admin-ui-test-user)** - para ter uma contrapartida de B.Simon em Palo Alto Networks - Admin UI que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração de aplicações **Palo Alto Networks - Admin UI,** encontre a secção **Gerir** e selecione um único sinal de **saúde**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **de Configuração Básica SAML,** execute os seguintes passos:

    a. Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão: `https://<Customer Firewall FQDN>/php/login.php`

    b. Na caixa **identifier,** digite um URL utilizando o seguinte padrão: `https://<Customer Firewall FQDN>:443/SAML20/SP`

    c. Na caixa de texto **URL de resposta,** digite o URL do Serviço de Apoio ao Consumidor de Afirmação (ACS) no seguinte formato: `https://<Customer Firewall FQDN>:443/SAML20/SP/ACS`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de Sign-On real, identifier e responder URL. Contacte [a Palo Alto Networks - Equipa de suporte ao cliente da Admin UI](https://support.paloaltonetworks.com/support) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.
    >
    > A porta 443 é necessária no **Identificador** e no **URL de resposta,** uma vez que estes valores são codificados na Firewall de Palo Alto. A remoção do número da porta resultará num erro durante o início de sessão se for removido.

    > A porta 443 é necessária no **Identificador** e no **URL de resposta,** uma vez que estes valores são codificados na Firewall de Palo Alto. A remoção do número da porta resultará num erro durante o início de sessão se for removido.

1. A aplicação Palo Alto UI espera as afirmações SAML num formato específico, o que requer que adicione mapeamentos de atributos personalizados à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos.

    ![image](common/default-attributes.png)

   > [!NOTE]
   > Como os valores do atributo são apenas exemplos, mapear os valores adequados para *o nome* de utilizador e *adminrole*. Existe outro atributo opcional, *o accessdomain*, que é usado para restringir o acesso de administração a sistemas virtuais específicos na firewall.

1. Além de acima, a aplicação Palo Alto Networks - Admin UI espera que alguns mais atributos sejam repercutidos na resposta SAML que são mostrados abaixo. Estes atributos também são pré-povoados, mas pode revê-los de acordo com os seus requisitos.

    | Nome |  Atributo de origem|
    | --- | --- |
    | nome de utilizador | user.userprincipalname |
    | adminrole | customadmin |
    | | |

    > [!NOTE]
    > O valor _do adminrole_ deve ser o mesmo que o nome de função configurado nas **Redes Palo Alto,** como mencionado no passo 9. 

    > [!NOTE]
    > Para obter mais informações sobre os atributos, consulte os seguintes artigos:
    > * [Perfil de função administrativa para Admin UI (administrador)](https://www.paloaltonetworks.com/documentation/80/pan-os/pan-os/firewall-administration/manage-firewall-administrators/configure-an-admin-role-profile)
    > * [Domínio de acesso ao dispositivo para Admin UI (accessdomain)](https://docs.paloaltonetworks.com/pan-os/8-0/pan-os-web-interface-help/device/device-access-domain.html)

1. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Metadadata XML** da Federação a partir das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

1. Na secção Configurar as Redes Palo Alto - Administração **UI,** copie os URL(s) apropriados de acordo com o seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso a Palo Alto Networks - Admin UI.

1. No portal Azure, selecione **Aplicações empresariais**e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Palo Alto Networks - Admin UI**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-palo-alto-networks---admin-ui-sso"></a>Configure Redes Palo Alto - Administrador UI SSO

1. Abra o Palo Alto Networks Firewall Admin UI como administrador numa nova janela.

2. Selecione o **separador Dispositivo.**

    ![O separador dispositivo](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_admin1.png)

3. No painel esquerdo, selecione **O Fornecedor de Identidade SAML**e, em seguida, **selecione Import** para importar o ficheiro de metadados.

    ![O botão de ficheiro de metadados de importação](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_admin2.png)

4. Na janela **de importação de perfil do servidor do fornecedor DE identificação SAML,** faça o seguinte:

    ![A janela "SAML Identifi provider server profile import"](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_idp.png)

    a. Na caixa Nome de **Perfil,** forneça um nome (por exemplo, **AzureAD Admin UI).**

    b. Em **Metadados do Fornecedor de Identidade**, selecione **Browse**e selecione o ficheiro metadata.xml que descarregou anteriormente a partir do portal Azure.

    c. Limpe a caixa **de verificação do Certificado de Fornecedor de Identidade Validado.**

    d. Selecione **OK**.

    e. Para comprometer as configurações na firewall, **selecione Commit**.

5. No painel esquerdo, selecione **O Fornecedor de Identidade SAML,** e, em seguida, selecione o Perfil de Fornecedor de Identidade SAML (por exemplo, **AzureAD Admin UI**) que criou no passo anterior.

    ![O perfil do fornecedor de identidade SAML](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_idp_select.png)

6. Na janela **do perfil do servidor do fornecedor de identidade SAML,** faça o seguinte:

    ![A janela "SAML Identity Provider Server Profile"](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_slo.png)
  
    a. Na caixa **URL do Fornecedor de Identidade SLO,** substitua o URL SLO anteriormente importado pelo seguinte URL: `https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0`
  
    b. Selecione **OK**.

7. No Palo Alto Networks Firewall's Admin UI, selecione **Dispositivo**e, em seguida, selecione **Funções de Administração**.

8. Selecione o botão **Adicionar.**

9. Na janela **Perfil de Função Admin,** na caixa **Nome,** forneça um nome para a função de administrador (por exemplo, **fwadmin).** O nome da função do administrador deve corresponder ao nome de atributo SAML Admin que foi enviado pelo Fornecedor de Identidade. O nome e valor da função do administrador foram criados na secção **Atributos do Utilizador** no portal Azure.

    ![Configure palo alto networks função administrador](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_adminrole.png)
  
10. No UI de administração da Firewall, selecione **Dispositivo**e, em seguida, selecione **o Perfil de Autenticação**.

11. Selecione o botão **Adicionar.**

12. Na janela Perfil de **Autenticação,** faça o seguinte: 

    ![A janela "Perfil de Autenticação"](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_authentication_profile.png)

    a. Na caixa **Nome,** forneça um nome (por exemplo, **AzureSAML_Admin_AuthProfile).**

    b. Na lista de drop-down **tipo,** selecione **SAML**. 

    c. Na lista de down-down do perfil do **servidor IdP,** selecione o perfil apropriado do Servidor do Fornecedor de Identidade SAML (por exemplo, **AzureAD Admin UI**).

    c. Selecione a caixa de verificação **De logoto único** ativar.

    d. Na caixa **de atributos Admin Role,** introduza o nome do atributo (por exemplo, **adminrole).**

    e. Selecione o separador **Avançado** e, em seguida, em **Lista de Permitir,** selecione **Adicionar**.

    ![O botão Adicionar no separador Avançado](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_allowlist.png)

    f. Selecione a caixa de verificação **All** ou selecione os utilizadores e grupos que podem autenticar com este perfil.  
    Quando um utilizador autentica, a firewall corresponde ao nome de utilizador ou grupo associado com as entradas nesta lista. Se não adicionar entradas, nenhum utilizadores poderá autenticar.

    exemplo, Selecione **OK**.

13. Para permitir que os administradores utilizem o SSO SAML utilizando o Azure, selecione **Configuração do Dispositivo**  >  **Setup**. No painel **de configuração,** selecione o **separador Gestão** e, em seguida, em **Definições de Autenticação**, selecione o botão **Definições** ("gear").

    ![O botão Definições](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_authsetup.png)

14. Selecione o perfil de autenticação SAML que criou na janela Perfil de Autenticação (por exemplo, **AzureSAML_Admin_AuthProfile).**

    ![O campo de Perfil de Autenticação](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_authsettings.png)

15. Selecione **OK**.

16. Para comprometer a configuração, **selecione Comprometa .**

### <a name="create-palo-alto-networks---admin-ui-test-user"></a>Criar redes Palo Alto - Utilizador de testes de Admin UI

Palo Alto Networks - Admin UI suporta o fornecimento de utilizadores just-in-time. Se um utilizador ainda não existir, este encontra-se automaticamente criado no sistema após uma autenticação bem sucedida. Não é necessária qualquer ação da sua parte para criar o utilizador.

## <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

1. Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para Palo Alto Networks - Admin UI Url de inscrição, onde pode iniciar o fluxo de login. 

2. Vá diretamente à Palo Alto Networks - Admin UI Sign-on URL e inicie o fluxo de login a partir daí.

3. Pode utilizar o Microsoft Access Panel. Quando clicar nas Redes Palo Alto - Azulejo UI Admin no Painel de Acesso, deverá ser automaticamente inscrito nas Redes Palo Alto - Administração UI para as quais configura o SSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)


## <a name="next-steps"></a>Passos Seguintes

Uma vez configurar a Palo Alto Networks - Admin UI pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).