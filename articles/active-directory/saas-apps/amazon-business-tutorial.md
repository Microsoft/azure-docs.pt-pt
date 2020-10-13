---
title: 'Tutorial: Integração do Azure Ative Directory com a Amazon Business Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e a Amazon Business.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/16/2019
ms.author: jeedes
ms.openlocfilehash: 659cca6979a8d8be7d12c49fe01a9d0a5d7ac58e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91713761"
---
# <a name="tutorial-integrate-amazon-business-with-azure-active-directory"></a>Tutorial: Integrar o Negócio Amazon com o Azure Ative Directory

Neste tutorial, você vai aprender a integrar o Amazon Business com o Azure Ative Directory (Azure AD). Quando integra o [Amazon Business](https://www.amazon.com/b2b/info/amazon-business?layout=landing) com a Azure AD, pode:

* Controle em Azure AD que tem acesso à Amazon Business.
* Permita que os seus utilizadores sejam automaticamente inscritos no Amazon Business com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode ter um mês de teste gratuito [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Uma assinatura única ativada pela Amazon Business (SSO). Vá à página amazon [Business](https://www.amazon.com/business/register/org/landing?ref_=ab_reg_mlp) para criar uma conta Amazon Business.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em uma conta Amazon Business existente.

* Amazon Business apoia **SP e IDP** iniciado SSO
* Amazon Business suporta fornecimento de utilizadores **just in time**

## <a name="adding-amazon-business-from-the-gallery"></a>Adicionar Amazon Business da galeria

Para configurar a integração do Amazon Business em Azure AD, precisa adicionar o Amazon Business da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Add a partir da secção **de galeria,** escreva **Amazon Business** na caixa de pesquisa.
1. Selecione **Amazon Business** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Configure e teste Azure AD SSO com a Amazon Business usando um utilizador de teste chamado **B.Simon**.

Para configurar e testar a Azure AD SSO com a Amazon Business, complete os seguintes passos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure a Amazon Business SSO](#configure-amazon-business-sso)** - para configurar as configurações de Sign-On única no lado da aplicação.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
5. **[Create Amazon Business test user](#create-amazon-business-test-user)** - ter uma contrapartida de B.Simon na Amazon Business que está ligada à representação AZure AD do utilizador.
6. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações da **Amazon Business,** encontre a secção **Gerir** e selecione **Single sign-on**.
1. Na página **de método de inscrição única,** selecione **SAML**.
1. Na **configuração single Sign-On com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **De Configuração Básica SAML,** se pretender configurar no modo iniciado pelo **IDP,** execute os seguintes passos:

    1. Na caixa de texto **identifier (Entity ID),** digite um URL utilizando um dos seguintes padrões:
    
       | URL | Região |
       |-|-|
       | `https://www.amazon.com`| América do Norte |
       | `https://www.amazon.co.jp`| Ásia Leste |
       | `https://www.amazon.de`| Europa |

    1. Na caixa de texto **URL de resposta,** digite um URL utilizando um dos seguintes padrões:
    
       | URL | Região |
       |-|-|
       | `https://www.amazon.com/bb/feature/sso/action/3p_redirect?idpid={idpid}`| América do Norte |
       | `https://www.amazon.co.jp/bb/feature/sso/action/3p_redirect?idpid={idpid}`| Ásia Leste |
       | `https://www.amazon.de/bb/feature/sso/action/3p_redirect?idpid={idpid}`| Europa |

       > [!NOTE]
       > O valor URL de resposta não é real. Atualize este valor com o URL de resposta real. Você receberá o `<idpid>` valor da secção de configuração SSO da Amazon Business, que é explicada mais tarde no tutorial. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. Se pretender configurar a aplicação **no** modo iniciado sp, terá de adicionar o URL completo fornecido na configuração amazon Business ao **URL de inscrição na** secção **UrLs adicional.**

1. A imagem que se segue mostra a lista de atributos predefinidos. Editar os atributos clicando no ícone **Editar** na secção **Atributos & Reclamações** do Utilizador.

    ![A screenshot mostra atributos do utilizador & Reclamações com valores predefinidos, tais como givenname user.givenname e Emailaddress user.mail.](media/amazon-business-tutorial/map-attribute3.png)

1. Editar Atributos e copiar O valor do **Namespace** destes atributos no Bloco de Notas.

    ![A screenshot mostra atributos do utilizador & Reclamações com colunas para nome e valor de reclamação.](media/amazon-business-tutorial/map-attribute4.png)

1. Além de acima, a aplicação Amazon Business espera que alguns mais atributos sejam repercutidos na resposta da SAML. Na secção **"Atributos & Reclamações** do Utilizador no diálogo **'Reclamações de Grupo',** executa os seguintes passos:

    a. Clique na **caneta** ao lado **de Grupos devolvidos em reivindicação**.

    ![A Screenshot mostra atributos do utilizador & Reclamações com o ícone de Grupos devolvidos na reclamação selecionada.](./media/amazon-business-tutorial/config04.png)

    ![A screenshot mostra as Reclamações do Grupo com valores descritos neste procedimento.](./media/amazon-business-tutorial/config05.png)

    b. Selecione **Todos os Grupos** da lista de rádio.

    c. Selecione **o ID do Grupo** como atributo **Fonte**.

    d. Verifique personalizar o nome da caixa **de verificação de reclamação do grupo** e insira o nome do grupo de acordo com o requisito da Organização.

    e. Clique em **Guardar**.

1. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre **metadata XML** e selecione **Descarregamento** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

1. Na secção Configurar a **Amazon Business,** copie os URL(s) apropriados com base na sua exigência.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="configure-amazon-business-sso"></a>Configurar a Amazon Business SSO

1. Numa janela diferente do navegador, inscreva-se no site da empresa Amazon Business como administrador.

1. Clique no perfil do **utilizador** e selecione **Definições de Negócios**.

    ![Perfil de Utilizador](media/amazon-business-tutorial/user-profile.png)

1. No assistente de integração do **Sistema,** selecione **Single Sign-On (SSO)**.

    ![Início de Sessão Único (SSO)](media/amazon-business-tutorial/sso-settings.png)

1. No assistente **SSO configurar,** selecione o fornecedor de acordo com os requisitos da Organização e clique em **Seguinte**.

    ![Screenshot mostra Configurar S S O, com Microsoft Azure A D e Next selecionados.](media/amazon-business-tutorial/default-group1.png)
    
    > [!NOTE]
    > Embora o Microsoft ADFS seja uma opção listada, não funcionará com a Azure AD SSO.

1. No novo assistente de incumprimento da **conta de utilizador,** selecione o **Grupo Predefinido** e, em seguida, selecione **a Função de Compra Padrão** de acordo com a função do utilizador na sua Organização e clique em **Seguinte**.

    ![O Screenshot mostra novos incumprimentos da conta de utilizador com o Microsoft S S O, o Requisitioner e o Next selecionados.](media/amazon-business-tutorial/dafault-group2.png)

1. No assistente de **ficheiro de metadados,** clique em **Procurar** para carregar o ficheiro **Metadados XML,** que descarregou a partir do portal Azure e clique em **Upload**.

    ![A screenshot mostra o upload do seu ficheiro de metadados, que lhe permite navegar para um ficheiro x m l e carregá-lo.](media/amazon-business-tutorial/connection-data1.png)

1. Após o upload do ficheiro de metadados descarregado, os campos na secção **de dados de Ligação** povoar-se-ão automaticamente. Depois do clique **em Seguida**.

    ![O Screenshot mostra dados de conexão, onde pode especificar um identificador Azure A D, Login U R L e Certificado de Assinatura SAML.](media/amazon-business-tutorial/connection-data2.png)

1. No assistente de **declaração de atributos Do Upload,** clique em **Skip**.

    ![A screenshot mostra o Upload your Attribute statement, que lhe permite navegar para uma declaração de atributos, mas neste caso, selecione Skip.](media/amazon-business-tutorial/map-attribute1.png)

1. No assistente **de mapeamento do Atributo,** adicione os campos de requisito clicando na opção + Adicionar uma opção **de campo.** Adicione os valores de atributos, incluindo o espaço de nome, que copiou da secção **atributos do utilizador & do** portal Azure no campo  **SAML AttributeName,** e clique em **Seguinte**.

    ![O screenshot mostra o mapeamento do Atributo, onde pode editar os seus dados SAML de dados da Amazon.](media/amazon-business-tutorial/map-attribute2.png)

1. No assistente de dados de **ligação da Amazon,** clique em **Seguinte**.

    ![O screenshot mostra os dados de ligação da Amazon, onde pode clicar ao lado para continuar.](media/amazon-business-tutorial/amazon-connect.png)

1. Verifique o **estado** dos passos configurados e clique em **Iniciar testes**.

    ![A screenshot mostra detalhes de conexão S S O com a opção de iniciar os testes.](media/amazon-business-tutorial/sso-connection1.png)

1. No **assistente de ligação SSO de teste,** clique em **Teste**.

    ![A screenshot mostra a ligação test S S O com o botão de teste.](media/amazon-business-tutorial/sso-connection2.png)

1. No assistente **de URL iniciado pelo IDP,** antes de clicar em **Ativar,** copie o valor atribuído ao **idpid** e cole-o no parâmetro **idpid** na **secção de Configuração** DE **RESPOSTA SAML** no portal Azure.

    ![A screenshot mostra I D P iniciado U R L onde você pode obter um U R L necessário para testes e, em seguida, selecionar Ativar.](media/amazon-business-tutorial/sso-connection3.png)

1. No ativo está pronto para mudar para o assistente **SSO ativo,** verifique **se testei completamente sSO e estou pronto para ir ao vivo** na caixa de verificação e clicar na Switch para **ativo**.

    ![O Screenshot mostra o Are you ready to switch to ative S S O confirmation where you can select Switch to ative.](media/amazon-business-tutorial/sso-connection4.png)

1. Finalmente na secção de detalhes da **Ligação SSO** o **Estado** é apresentado como **Ativo**.

    ![A screenshot mostra detalhes de conexão S S O com um estado de Ative.](media/amazon-business-tutorial/sso-connection5.png)
    
    > [!NOTE]
    > Se pretender configurar a aplicação **no** modo iniciado sp, complete o passo seguinte, cole o URL de entrada de sinalização a partir da imagem acima na caixa de texto **URL sign-on** da secção **DeBS adicionais** no portal Azure. Utilize o seguinte formato:
    >
    > `https://www.amazon.<TLD>/bb/feature/sso/action/start?domain_hint=<uniqueid>`
    
### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD

Nesta secção, irá criar um utilizador de teste no portal Azure chamado B.Simon.

> [!NOTE]
> Os administradores precisam de criar os utilizadores de teste no seu inquilino, se necessário. Os passos seguintes mostram como criar um utilizador de teste.

1. A partir do painel esquerdo no portal Azure, selecione **Azure Ative Directory**, selecione **Utilizadores**, e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo **nome do utilizador,** insira o username@companydomain.extension . Por exemplo, `B.Simon@contoso.com`.
   1. Selecione a caixa **de verificação de palavra-passe Show** e, em seguida, anote o valor que é apresentado na caixa **palavra-passe.**
   1. Clique em **Criar**.

### <a name="create-an-azure-ad-security-group-in-the-azure-portal"></a>Criar um Grupo de Segurança AZure AD no portal Azure

1. Clique no **Azure Ative Directory > Todos os Grupos**.

    ![O Screenshot mostra o menu do portal Azure com o Azure Ative Directory selecionado e todos os grupos selecionados no painel grupo.](./media/amazon-business-tutorial/all-groups-tab.png)

1. Clique **em Novo grupo:**

    ![A screenshot mostra o botão de grupo Novo.](./media/amazon-business-tutorial/new-group-tab.png)

1. Preencha o **tipo de grupo**, nome de **grupo,** **descrição do grupo,** **tipo de adesão**. Clique na seta para selecionar membros e, em seguida, procure ou clique no membro que gostaria de adicionar ao grupo. Clique em **Select** para adicionar os membros selecionados e, em seguida, clique em **Criar**.

    ![O Screenshot mostra o painel do Grupo com opções, incluindo selecionar membros e convidar utilizadores externos.](./media/amazon-business-tutorial/group-information.png)

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso ao Amazon Business.

1. No portal Azure, selecione **Aplicações empresariais**e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Amazon Business**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![Screenshot mostra Adicionar botão de utilizador.](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

    >[!NOTE]
    > Se não atribuir os utilizadores no AD Azure, obtém o seguinte erro.

    ![A screenshot mostra uma mensagem de erro em que não pode ser inscrito.](media/amazon-business-tutorial/assign-user.png)

### <a name="assign-the-azure-ad-security-group-in-the-azure-portal"></a>Atribuir o Grupo de Segurança Azure AD no portal Azure

1. No portal Azure, selecione **Aplicações empresariais**, selecione **Todas as aplicações,** em seguida, selecione **Amazon Business**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de candidaturas, escreva e selecione **Amazon Business.**

    ![O link Amazon Business na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no **utilizador Adicionar**.

    ![O painel de atribuição de adição](common/add-assign-user.png)

5. Procure o Grupo de Segurança que pretende utilizar e, em seguida, clique no grupo para o adicionar à secção 'Selecção' de membros. Clique **em Selecionar**e, em seguida, clique em **Atribuir**.

    ![Grupo de Segurança de Pesquisa](./media/amazon-business-tutorial/assign-group.png)

    > [!NOTE]
    > Verifique as notificações na barra de menu para ser notificado de que o Grupo foi atribuído com sucesso à aplicação Enterprise no portal Azure.

### <a name="create-amazon-business-test-user"></a>Criar utilizador de teste amazon business

Nesta secção, um utilizador chamado B.Simon é criado no Amazon Business. O Amazon Business suporta o fornecimento de utilizadores just-in-time, o que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir no Amazon Business, um novo é criado após a autenticação.

### <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Ao clicar no azulejo da Amazon Business no Painel de Acesso, deverá ser automaticamente inscrito no Amazon Business para o qual configura o SSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Recursos Adicionais

- [ Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e um único acesso ao Azure Ative Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
