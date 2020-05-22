---
title: 'Tutorial: Azure Ative Diretório integração individual (SSO) com easySSO para Confluência [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o EasySSO para confluência.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 959019be-c7f6-41cc-b0ff-c5927ce64a74
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 05/18/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8a0ac6d86c1ef4fb8f2f5b75ac47f1a25e2cda0b
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83749453"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-easysso-for-confluence"></a>Tutorial: Azure Ative Diretório integração de inscrição única (SSO) com easySSO para Confluência

Neste tutorial, você vai aprender a integrar easySSO para confluência com o Azure Ative Directory (Azure AD). Quando integrar a EasySSO para confluência com a AD Azure, pode:

* Controlo em Azure AD que tem acesso à EasySSO para confluência.
* Ative que os seus utilizadores sejam automaticamente inscritos na EasySSO para confluência com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* EasySSO para a subscrição ativada por um único sinal de confluência (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.

* EasySSO para Confluence suporta **SP e IDP** iniciado SSO
* EasySSO para Confluence suporta o provisionamento de utilizadores **justo no tempo**
* Assim que configurar o EasySSO para confluência, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se a partir do Acesso Condicional. [Saiba como impor o controlo](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)da sessão com o Microsoft Cloud App Security .

## <a name="adding-easysso-for-confluence-from-the-gallery"></a>Adicionar easySSO para confluência da galeria

Para configurar a integração da EasySSO para confluência em Azure AD, precisa adicionar easySSO para confluência da galeria à sua lista de aplicações saaS geridas.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No **Add da** secção galeria, digite **EasySSO para Confluence** na caixa de pesquisa.
1. Selecione **EasySSO para confluência** a partir do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-easysso-for-confluence"></a>Configure e teste azure AD único sign-on para EasySSO para Confluência

Configure e teste Azure AD SSO com EasySSO para confluência utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado na EasySSO para confluência.

Para configurar e testar o Azure AD SSO com a EasySSO para confluência, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
    1. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
1. **[Configure easySSO para Confluence SSO](#configure-easysso-for-confluence-sso)** - para configurar as definições de inscrição únicas no lado da aplicação.
    1. Criar o **[EasySSO para o utilizador](#create-easysso-for-confluence-test-user)** do teste de confluência - para ter uma contrapartida de B.Simon na EasySSO para confluência que esteja ligada à representação do utilizador da AD Azure.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página **easySSO para** integração de aplicações de confluência, encontre a secção **Gerir** e selecione **um único sinal.**
1. Na página **de método de inscrição, selecione** **SAML**.
1. No **set single sign-on com** a página SAML, clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na secção **Basic SAML Configuration,** caso deseje configurar a aplicação no modo iniciado do **IDP,** introduza os valores para os seguintes campos:

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão:`https://<server-base-url>/plugins/servlet/easysso/saml`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:`https://<server-base-url>/plugins/servlet/easysso/saml`

1. Clique em **Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação no modo iniciado **por SP:**

    Na caixa de texto **de URL sign-on,** escreva um URL utilizando o seguinte padrão:`https://<server-base-url>/`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de identificação, resposta real e URL de sinalização. Contacte a [EasySSO para](mailto:support@techtime.co.nz) obter estes valores. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

1. EasySSO para aplicação de confluência espera que as afirmações do SAML num formato específico, o que requer que adicione mapeamentos personalizados de atributos à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos.

    ![image](common/default-attributes.png)

1. Além de acima, a easySSO para a aplicação De Confluence espera que poucos atributos sejam retransmitidos na resposta SAML que são mostradas abaixo. Estes atributos também são pré-povoados, mas pode revê-los de acordo com os seus requisitos.

    | Name |  Atributo fonte|
    | --------------- | --------- |
    | urn:oid:2.16.840.1.113730.3.1.241 | user.displayname |
    | urna:oid:2.5.4.42 | user.givenname |
    | urn:oid:2.5.4.4.4 | utilizador.sobrenome |
    | urna:oid:0.9.2342.19200300.100.1.1 | user.userprincipalname|
    | urna:oid:0.9.2342.19200300.100.1.3 | user.userprincipalname |
    | | |

1. No **set single sign-on com** a página SAML, na secção Certificado de **Assinatura SAML,** clique no botão de cópia para copiar o Url de **Metadados da Federação** da Aplicação e guarde-o no seu computador.

    ![O link de descarregamento do Certificado](common/copy-metadataurl.png)

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

Nesta secção, permitirá que b.Simon utilize um único sign-on Azure, concedendo acesso à EasySSO para confluência.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **EasySSO para Confluência**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

## <a name="configure-easysso-for-confluence-sso"></a>Configure easySSO para Confluence SSO

1. Assine o seu EasySSO para a confluência com privilégios do Administrador e navegue para a secção **'Gerir Apps'.**

    ![Gerir Aplicações](./media/easysso-for-confluence-tutorial/jira-admin-1.png)

1. Clique no **EasySSO**.

    ![Fácil SSO](./media/easysso-for-confluence-tutorial/jira-admin-2.png)

1. Selecione a opção **SAML.** Isto irá levá-lo à secção de configuração SAML.

    ![SAML](./media/easysso-for-confluence-tutorial/jira-admin-3.png)

1. Selecione o separador **Certificados** na parte superior e será apresentado com o seguinte ecrã e localizará **o Certificado (Base64)** ou **ficheiro de metadados** que guardou nos passos anteriores da configuração **Azure AD SSO.** Tem as seguintes opções sobre como proceder:

    ![URL de metadados](./media/easysso-for-confluence-tutorial/jira-admin-4.png)

    a. Utilize o Ficheiro de Metadados da Federação de **Aplicações** que descarregou para ficheirolocal no seu computador. **Selecione botão de** rádio upload e siga o diálogo de ficheiro supérre específico do seu sistema operativo

    **OU**

    b. Abra o Ficheiro de Metadados da Federação de **Aplicações** para ver o conteúdo (em qualquer editor de texto simples) do ficheiro e copiá-lo para a área de cópia. Selecione a opção **de entrada** e o conteúdo da pasta no campo de texto.

    **OU**

    c. Configuração totalmente manual. Abra o Certificado da Federação de Aplicações **(Base64)** para ver o conteúdo (em qualquer editor de texto simples) do ficheiro e copiá-lo para a área de cópia. Cola-o no campo de texto dos certificados de **assinatura idp token.** Em seguida, navegue para o separador **Geral** e preencha os campos DE URL de **Ligação POST** e **id da entidade** com os respetivos valores para URL de **Login** e **identificador de AD Azure** que guardou anteriormente.

1. Clique no botão **Guardar** na parte inferior da página. Verá que o conteúdo dos ficheiros Metadados ou Certificados está analisado nos campos de configuração. EasySSO para configuração de confluência está completo.

1. Para obter a melhor experiência de teste, navegue para **olhar & sinta** o separador e verifique a opção **SAML Login Button.** Isto permitirá um botão separado no ecrã de login Jira especificamente para testar a sua integração Azure AD SAML de ponta a ponta. Também pode deixar este botão ligado e configurar a sua colocação, cor e tradução para o modo de produção.

    ![Olhe & Sentir](./media/easysso-for-confluence-tutorial/jira-admin-5.png)

    > [!NOTE]
    > Caso tenha algum problema, contacte a equipa de [suporte da EasySSO.](mailto:support@techtime.co.nz)

### <a name="create-easysso-for-confluence-test-user"></a>Criar easySSO para utilizador de teste de confluência

Nesta secção, um utilizador chamado B.Simon é criado na EasySSO para confluência. EasySSO para Confluence suporta o fornecimento de utilizadores just-in-time, que é **desativado** por padrão. Para ativar o fornecimento do utilizador, tem de verificar explicitamente o utilizador sobre a opção de **login bem sucedida** na secção geral da configuração de plugin easySSO. Se um utilizador já não existir na EasySSO para confluência, um novo é criado após a autenticação.

No entanto, se não pretender ativar o fornecimento automático de utilizadores no primeiro login do utilizador, os utilizadores devem existir nos Diretórios de Utilizador de backend que o EasySSO para a confluência faz uso, como lDAP ou Atlassian Crowd.

![Aprovisionamento de utilizadores](./media/easysso-for-confluence-tutorial/jira-admin-6.png)

## <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo EasySSO para confluência no Painel de Acesso, deve ser automaticamente inscrito no EasySSO para confluência para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e a inscrição única com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente easySSO para confluência com Azure AD](https://aad.portal.azure.com/)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Como proteger a EasySSO para confluência com visibilidade e controlos avançados](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

