---
title: 'Tutorial: Integração de Diretórios Ativos Azure com a Amazon Business [ Negócios] Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Amazon Business.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: b2597a66-afd9-4f11-b14b-646b597bb6c2
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/16/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8218b3dbe09e5ce7e6c28e1084b26c6eec4a16ca
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83773056"
---
# <a name="tutorial-integrate-amazon-business-with-azure-active-directory"></a>Tutorial: Integrar o Negócio da Amazon com o Diretório Ativo Azure

Neste tutorial, você vai aprender a integrar o Amazon Business com o Azure Ative Directory (Azure AD). Quando integrar o [Amazon Business](https://www.amazon.com/b2b/info/amazon-business?layout=landing) com a Azure AD, pode:

* Controlo em Azure AD que tem acesso à Amazon Business.
* Permita que os seus utilizadores sejam automaticamente inscritos na Amazon Business com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode ter um mês de experiência gratuita [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Uma subscrição única (SSO) da Amazon Business. Vá à página [do Amazon Business](https://www.amazon.com/business/register/org/landing?ref_=ab_reg_mlp) para criar uma conta Amazon Business.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO numa conta existente da Amazon Business.

* Amazon Business apoia **SP e IDP** iniciadoS SSO
* Amazon Business suporta fornecimento de utilizadores **justo no tempo**

## <a name="adding-amazon-business-from-the-gallery"></a>Adicionar o Amazon Business da galeria

Para configurar a integração do Amazon Business em Azure AD, você precisa adicionar amazon Business da galeria à sua lista de aplicações geridas saaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No Add da secção **de galeria,** digite **o Amazon Business** na caixa de pesquisa.
1. Selecione **Amazon Business** a partir do painel de resultados e, em seguida, adicione a app. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Configure e teste Azure AD SSO com a Amazon Business usando um utilizador de teste chamado **B.Simon**.

Para configurar e testar o Azure AD SSO com a Amazon Business, complete os seguintes passos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o Amazon Business SSO](#configure-amazon-business-sso)** - para configurar as definições de Entrada Única no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
4. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
5. **[Crie o utilizador](#create-amazon-business-test-user)** de testes da Amazon Business - para ter uma contrapartida da B.Simon na Amazon Business que está ligada à representação do utilizador da AD Azure.
6. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **da Amazon Business,** encontre a secção **Gerir** e selecione **single sign-on**.
1. Na página **Select a Single sign-on,** selecione **SAML**.
1. Na configuração do Single Sign-On com a página **SAML,** clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na secção **Basic SAML Configuration,** se desejar configurar no modo iniciado **idp,** execute os seguintes passos:

    1. Na caixa de texto **identificador (Id** da entidade), digite um URL utilizando um dos seguintes padrões:
    
       | | |
       |-|-|
       | `https://www.amazon.com`| América do Norte |
       | `https://www.amazon.co.jp`| Ásia Leste |
       | `https://www.amazon.de`| Europa |

    1. Na caixa de texto **URL resposta,** digite um URL utilizando um dos seguintes padrões:
    
       | | |
       |-|-|
       | `https://www.amazon.com/bb/feature/sso/action/3p_redirect?idpid={idpid}`| América do Norte |
       | `https://www.amazon.co.jp/bb/feature/sso/action/3p_redirect?idpid={idpid}`| Ásia Leste |
       | `https://www.amazon.de/bb/feature/sso/action/3p_redirect?idpid={idpid}`| Europa |

       > [!NOTE]
       > O valor url de resposta não é real. Atualize este valor com o URL de Resposta real. Você receberá o `<idpid>` valor da secção de configuração Amazon Business SSO, o que é explicado mais tarde no tutorial. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

1. Se pretender configurar a aplicação no modo iniciado por **SP,** terá de adicionar o URL completo fornecido na configuração do Amazon Business ao URL de início de **sessão** na secção **DeSet adicionais URLs.**

1. A imagem que se segue mostra a lista de atributos predefinidos. Editar os atributos clicando no ícone **Editar** na secção **Atributos do Utilizador & Reivindicações.**

    ![Atributos](media/amazon-business-tutorial/map-attribute3.png)

1. Editar Atributos e copiar o valor **do espaço** de nome destes atributos no Bloco de Notas.

    ![Atributos](media/amazon-business-tutorial/map-attribute4.png)

1. Além de acima, a aplicação Amazon Business espera que poucos atributos sejam retransmitidos na resposta saml. Na secção **de Atributos de utilizador & Reclamações** no diálogo reivindicações do **grupo,** execute os seguintes passos:

    a. Clique na **caneta** ao lado dos **Grupos devolvidos por reclamação**.

    ![image](./media/amazon-business-tutorial/config04.png)

    ![image](./media/amazon-business-tutorial/config05.png)

    b. Selecione **Todos os Grupos** da lista de rádio.

    c. Selecione **ID do grupo** como **atributo fonte**.

    d. Verifique Personalizar o nome da caixa de verificação de **reclamações do grupo** e insira o nome do grupo de acordo com o requisito da sua Organização.

    e. Clique em **Guardar**.

1. Na configuração de um único sinal com página **SAML,** na secção Certificado de **Assinatura SAML,** encontre **Metadados XML** e selecione **Download** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/metadataxml.png)

1. Na secção Configurar o **Amazon Business,** copie os URL(s) adequados com base na sua exigência.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="configure-amazon-business-sso"></a>Configure Amazon Business SSO

1. Numa janela diferente do navegador web, inscreva-se no site da sua empresa Amazon Business como administrador.

1. Clique no Perfil do **Utilizador** e selecione **Definições de Negócio**.

    ![Perfil de Utilizador](media/amazon-business-tutorial/user-profile.png)

1. No assistente de integrações do **Sistema,** selecione **Single Sign-On (SSO)**.

    ![Início de Sessão Único (SSO)](media/amazon-business-tutorial/sso-settings.png)

1. No assistente **SSO configurar,** selecione o fornecedor de acordo com os seus requisitos organizacionais e clique em **Next**.

    ![Grupo padrão](media/amazon-business-tutorial/default-group1.png)
    
    > [!NOTE]
    > Embora o Microsoft ADFS seja uma opção listada, não funcionará com o Azure AD SSO.

1. Na nova conta de **utilizador,** selecione o **Grupo Predefinido** e, em seguida, selecione **'Predefinido'** de acordo com a função do utilizador na sua Organização e clique em **Next**.

    ![Grupo padrão](media/amazon-business-tutorial/dafault-group2.png)

1. No Upload do seu assistente de **ficheiros de metadados,** clique em **Navegar** para carregar o ficheiro **Metadata XML,** que descarregou a partir do portal Azure e clique em **Upload**.

    ![Dados de Ligação](media/amazon-business-tutorial/connection-data1.png)

1. Depois de carregar o ficheiro de metadados descarregado, os campos na secção de dados de **Ligação** serão preenchidos automaticamente. Depois desse clique **em Next**.

    ![Dados de Ligação](media/amazon-business-tutorial/connection-data2.png)

1. No upload do seu assistente de **declaração de atributo,** clique em **Skip**.

    ![Atributos](media/amazon-business-tutorial/map-attribute1.png)

1. No assistente de **mapeamento do Atributo,** adicione os campos de exigência clicando na **opção de campo + Adicione.** Adicione os valores do atributo, incluindo o espaço de nome, que copiou a partir dos **Atributos** do Utilizador & secção de Reclamações do portal Azure no campo **SAML AttributeName,** e clique **em Seguinte**.

    ![Atributos](media/amazon-business-tutorial/map-attribute2.png)

1. No assistente de dados de **ligação da Amazon,** clique em **Next**.

    ![Ligação](media/amazon-business-tutorial/amazon-connect.png)

1. Verifique o **Estado** dos passos configurados e clique em **Iniciar testes**.

    ![Ligação](media/amazon-business-tutorial/sso-connection1.png)

1. No **teste SSO Connection** wizard, clique em **Test**.

    ![Ligação](media/amazon-business-tutorial/sso-connection2.png)

1. No **idp iniciado URL** wizard, antes de clicar em **Ativar,** copie o valor que é atribuído a **idpid** e cole no parâmetro **idpid** no URL de **resposta** na secção **basic SAML Configuração** no portal Azure.

    ![Ligação](media/amazon-business-tutorial/sso-connection3.png)

1. No está pronto para mudar para o assistente **SSO ativo,** verificar **se testei totalmente o SSO e estou pronto para ir ao vivo** e clicar na Switch para **ativo**.

    ![Ligação](media/amazon-business-tutorial/sso-connection4.png)

1. Finalmente, na secção de detalhes da **Ligação SSO,** o **Estado** é mostrado como **Ativo**.

    ![Ligação](media/amazon-business-tutorial/sso-connection5.png)
    
    > [!NOTE]
    > Se pretender configurar a aplicação no modo iniciado por **SP,** complete o seguinte passo, colhe o URL de início de sinal a partir da imagem acima na caixa de texto url **signon** da secção **de URLs adicionais set** no portal Azure. Utilize o seguinte formato:
    >
    > `https://www.amazon.<TLD>/bb/feature/sso/action/start?domain_hint=<uniqueid>`
    
### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD

Nesta secção, você vai criar um utilizador de teste no portal Azure chamado B.Simon.

> [!NOTE]
> Os administradores precisam de criar os utilizadores de teste no seu inquilino, se necessário. Os passos seguintes mostram como criar um utilizador de teste.

1. A partir do painel esquerdo no portal Azure, **selecione Azure Ative Directory**, selecione **Utilizadores**e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo de **nome do Utilizador,** introduza o username@companydomain.extension . Por exemplo, `B.Simon@contoso.com`.
   1. Selecione a caixa de verificação de **palavra-passe do Show** e, em seguida, escreva o valor que está apresentado na caixa **password.**
   1. Clique em **Criar**.

### <a name="create-an-azure-ad-security-group-in-the-azure-portal"></a>Criar um Grupo de Segurança AD Azure no portal Azure

1. Clique no **Diretório Ativo do Azure > todos os grupos**.

    ![Criar um Grupo de Segurança AD Azure](./media/amazon-business-tutorial/all-groups-tab.png)

1. Clique em **novo grupo**:

    ![Criar um Grupo de Segurança AD Azure](./media/amazon-business-tutorial/new-group-tab.png)

1. Preencha o **tipo de grupo,** **nome do grupo,** **descrição do grupo,** **tipo de membro**. Clique na seta para selecionar membros, em seguida, procure ou clique no membro que irá adicionar ao grupo. Clique em **Select** para adicionar os membros selecionados e, em seguida, clique em **Criar**.

    ![Criar um Grupo de Segurança AD Azure](./media/amazon-business-tutorial/group-information.png)

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, você permitirá que B.Simon use o único sign-on Azure, concedendo acesso ao Amazon Business.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Amazon Business**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

    >[!NOTE]
    > Se não atribuir os utilizadores no Anúncio Azure, obtém o seguinte erro.

    ![Ligação Adicionar Utilizador](media/amazon-business-tutorial/assign-user.png)

### <a name="assign-the-azure-ad-security-group-in-the-azure-portal"></a>Atribuir o Grupo de Segurança Azure AD no portal Azure

1. No portal Azure, selecione **Aplicações Empresariais**, selecione **Todas as aplicações,** em seguida, selecione **Amazon Business**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, digite e selecione **Amazon Business**.

    ![O link Amazon Business na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no **utilizador Adicionar**.

    ![O painel de atribuição adicionar](common/add-assign-user.png)

5. Procure o Grupo de Segurança que pretende utilizar e clique no grupo para adicioná-lo à secção de membros Select. Clique em **Selecionar**e, em seguida, clique em **Atribuir**.

    ![Grupo de Segurança de Pesquisa](./media/amazon-business-tutorial/assign-group.png)

    > [!NOTE]
    > Verifique as notificações na barra de menus a ser notificada de que o Grupo foi atribuído com sucesso à aplicação Enterprise no portal Azure.

### <a name="create-amazon-business-test-user"></a>Criar o utilizador de teste amazon business

Nesta secção, um utilizador chamado B.Simon é criado na Amazon Business. A Amazon Business suporta o fornecimento de utilizadores just-in-time, que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir no Amazon Business, um novo é criado após a autenticação.

### <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Ao clicar no azulejo Amazon Business no Painel de Acesso, deverá ser automaticamente inscrito no Amazon Business para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e a inscrição única com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
