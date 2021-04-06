---
title: 'Tutorial: Azure Ative Directory integração única de sign-on (SSO) com iProva | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o iProva.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/19/2020
ms.author: jeedes
ms.openlocfilehash: 717696053a742abae6756655a15416ac81221144
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92459753"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-iprova"></a>Tutorial: Azure Ative Directory integração única (SSO) com iProva

Neste tutorial, você vai aprender a integrar o iProva com Azure Ative Directory (Azure AD). Quando integra o iProva com AZure AD, pode:

* Controlo em Azure AD que tem acesso ao iProva.
* Permita que os seus utilizadores sejam automaticamente inscritos no iProva com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Subscrição única ativa do iProva (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* IProva suporta SSO iniciado **pela SP**

* Uma vez configurado iProva, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se a partir do Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-iprova-from-the-gallery"></a>Adicionar iProva da galeria

Para configurar a integração do iProva no AD AZure, é necessário adicionar o iProva da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **da galeria,** escreva **iProva** na caixa de pesquisa.
1. Selecione **iProva** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-iprova"></a>Configurar e testar Azure AD único sign-on para iProva

Configure e teste Azure AD SSO com iProva usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado no iProva.

Para configurar e testar o Azure AD SSO com iProva, complete os seguintes blocos de construção:

1. **[Recupere as informações de configuração do iProva](#retrieve-configuration-information-from-iprova)** - como preparação para os próximos passos.
1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Create iProva test user](#create-iprova-test-user)** - para ter uma contraparte de B.Simon no iProva que está ligada à representação AZure AD do utilizador.
1. **[Configure iProva SSO](#configure-iprova-sso)** - para configurar as definições de Sign-On única no lado da aplicação.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="retrieve-configuration-information-from-iprova"></a>Recuperar informações de configuração do iProva

Nesta secção, obtém informações do iProva para configurar a Azure AD uma única súbula.

1. Abra um navegador web e vá à página **de informações SAML2** no iProva utilizando o seguinte padrão URL:

    ```https
    https://SUBDOMAIN.iprova.nl/saml2info
    https://SUBDOMAIN.iprova.be/saml2info
    ```

    ![Ver a página de informações do iProva SAML2](media/iprova-tutorial/iprova-saml2-info.png)

1. Deixe o separador do navegador aberto enquanto procede com os próximos passos em outro separador do navegador.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **iProva,** encontre a secção **Gerir** e selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **de Configuração Básica SAML,** execute os seguintes passos:

    a. Preencha a caixa URL de entrada de **inscrição** com o valor apresentado por trás do **URL de inscrição na** página de **informações iProva SAML2.** Esta página ainda está aberta no separador outro do navegador.

    b. Preencha a caixa **identifier** com o valor que é apresentado por trás da etiqueta **EntityID** na página **de informações iProva SAML2.** Esta página ainda está aberta no separador outro do navegador.

    c. Preencha a caixa **de URL de resposta** com o valor apresentado por trás do URL de **resposta** da etiqueta na página **de informações iProva SAML2.** Esta página ainda está aberta no separador outro do navegador.

1. A aplicação iProva espera as afirmações SAML num formato específico, o que requer que adicione mapeamentos de atributos personalizados à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos.

    ![image](common/default-attributes.png)

1. Além de acima, a aplicação iProva espera que alguns mais atributos sejam repercutidos na resposta SAML que são mostrados abaixo. Estes atributos também são pré-povoados, mas pode revê-los de acordo com os seus requisitos.

    | Name | Atributo de origem| Espaço de Nomes  |
    | ---------------| -------- | -----|
    | `samaccountname` | `user.onpremisessamaccountname`| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** clique no botão de cópia para copiar o Url de **metadados da Federação de Aplicações** e guarde-o no seu computador.

    ![O link de descarregamento de certificado](common/copy-metadataurl.png)

## <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD

Nesta secção, irá criar um utilizador de teste no portal Azure chamado B.Simon.

1. A partir do painel esquerdo no portal Azure, selecione **Azure Ative Directory**, selecione **Utilizadores**, e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo **nome do utilizador,** insira o username@companydomain.extension . Por exemplo, `B.Simon@contoso.com`.
   1. Selecione a caixa **de verificação de palavra-passe Show** e, em seguida, anote o valor que é apresentado na caixa **palavra-passe.**
   1. Clique em **Criar**.

## <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso ao iProva.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **iProva**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="create-iprova-test-user"></a>Criar utilizador de teste iProva

1. Inscreva-se no iProva utilizando a conta **administrador.**

2. Abra o **menu Go para** o menu.

3. Selecione **gestão de aplicações**.

4. Selecione **Utilizadores** no painel **de grupos de utilizadores e utilizadores.**

5. Selecione **Adicionar**.

6. Na caixa **username,** introduza o nome de utilizador do utilizador como `B.Simon@contoso.com` .

7. Na caixa **de nome completo,** insira um nome completo de utilizador como **B.Simon**.

8. Selecione a opção **Não senha (use um único sinal de acesso).**

9. Na caixa **de endereço de e-mail,** insira o endereço de e-mail do utilizador como `B.Simon@contoso.com` .

10. Percorra até ao fim da página e **selecione Terminar**.

## <a name="configure-iprova-sso"></a>Configurar iProva SSO

1. Inscreva-se no iProva utilizando a conta **administrador.**

2. Abra o **menu Go para** o menu.

3. Selecione **gestão de aplicações**.

4. Selecione **General** no painel **de definições do Sistema.**

5. Selecione **Editar**.

6. Desloque-se para baixo para **o controlo de acesso**.

    ![Configurações de controlo de acesso iProva](media/iprova-tutorial/iprova-accesscontrol.png)

7. Localizar a definição **Os utilizadores são automaticamente iniciados com as suas contas de rede,** e alterá-lo para **Sim, autenticação via SAML**. Agora aparecem opções adicionais.

8. Selecione **Configurar**.

9. Selecione **Seguinte**.

10. O iProva pergunta se pretende transferir dados da federação a partir de um URL ou carregá-lo a partir de um ficheiro. Selecione a opção **'A partir** de URL'.

    ![Baixar metadados Azure AD](media/iprova-tutorial/iprova-download-metadata.png)

11. Cole o URL de metadados que guardou no último passo da secção "Configurar Azure AD single sign-on".

12. Selecione o botão em forma de seta para descarregar os metadados do Azure AD.

13. Quando o download estiver concluído, aparece o **ficheiro de dados da Federação Válida de Confirmação.**

14. Selecione **Seguinte**.

15. Ignore a opção **de início de sessão** de teste por enquanto e selecione **Seguinte**.

16. Na **Claim para utilizar a** caixa drop-down, selecione **o nome do número de janelas**.

17. Selecione **Concluir**.

18. Volte agora ao ecrã **de definições gerais de Edição.** Desloque-se até à parte inferior da página e selecione **OK** para guardar a sua configuração.

## <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo iProva no Painel de Acesso, deverá ser automaticamente inscrito no iProva para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure ](./tutorial-list.md)

- [O que é o acesso à aplicação e um único acesso ao Azure Ative Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimente iProva com Azure AD](https://aad.portal.azure.com/)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [Como proteger o iProva com visibilidade e controlos avançados](/cloud-app-security/proxy-intro-aad)