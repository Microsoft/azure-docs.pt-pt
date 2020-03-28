---
title: 'Tutorial: Azure Ative Diretório integração individual (SSO) com iProva [ iProva] Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o iProva.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1eaeef9b-4479-4a9f-b1b2-bc13b857c75c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 03/19/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 98458f8be162d0903f5ea0d1f7d4651d46f78e8e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "80048441"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-iprova"></a>Tutorial: Azure Ative Diretório integração individual (SSO) com iProva

Neste tutorial, você vai aprender a integrar o iProva com o Azure Ative Directory (Azure AD). Quando integrar o iProva com a Azure AD, pode:

* Controlo em Azure AD que tem acesso ao iProva.
* Ative que os seus utilizadores sejam automaticamente inscritos no iProva com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* iProva única subscrição ativada por si.000.000.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.

* iProva suporta **SP** iniciado SSO

* Assim que configurar o iProva, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se a partir do Acesso Condicional. [Saiba como impor o controlo](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)da sessão com o Microsoft Cloud App Security .

## <a name="adding-iprova-from-the-gallery"></a>Adicionando iProva da galeria

Para configurar a integração do iProva em Azure AD, é necessário adicionar iProva da galeria à sua lista de aplicações geridas do SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No Add da secção **galeria,** digite **iProva** na caixa de pesquisa.
1. Selecione **iProva** no painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-iprova"></a>Configure e teste Azure AD único signo para iProva

Configure e teste Azure AD SSO com iProva utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no iProva.

Para configurar e testar o Azure AD SSO com iProva, complete os seguintes blocos de construção:

1. **[Recupere informações de configuração do iProva](#retrieve-configuration-information-from-iprova)** - como preparação para os próximos passos.
1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
1. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
1. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
1. **[Crie o utilizador](#create-iprova-test-user)** do teste iProva - para ter uma contraparte de B.Simon no iProva que esteja ligada à representação da AD Azure do utilizador.
1. **[Configure iProva SSO](#configure-iprova-sso)** - para configurar as definições de início de sessão individuais no lado da aplicação.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="retrieve-configuration-information-from-iprova"></a>Recuperar informações de configuração do iProva

Nesta secção, obtém informações do iProva para configurar o Único Sign-on Azure AD.

1. Abra um navegador web e vá para a página de **informações SAML2** no iProva usando o seguinte padrão de URL:

    | | |
    |-|-|
    | `https://SUBDOMAIN.iprova.nl/saml2info`|
    | `https://SUBDOMAIN.iprova.be/saml2info`|
    | | |

    ![Ver a página de informações iProva SAML2](media/iprova-tutorial/iprova-saml2-info.png)

1. Deixe o separador do navegador aberto enquanto prossegue com os próximos passos em outro separador de navegador.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **iProva,** encontre a secção **Gerir** e selecione **um único sinal.**
1. Na página **de método de inscrição, selecione** **SAML**.
1. No **set single sign-on com** a página SAML, clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na secção **Basic SAML Configuration,** execute os seguintes passos:

    a. Encha a caixa **de URL sign-on** com o valor apresentado por trás do URL de **inscrição** na página de **informação iProva SAML2.** Esta página ainda está aberta no seu outro separador de navegador.

    b. Encha a caixa **de identificação** com o valor que está exposto por trás da etiqueta **EntityID** na página de **informação iProva SAML2.** Esta página ainda está aberta no seu outro separador de navegador.

    c. Encha a caixa **de URL** de resposta com o valor apresentado por trás do URL de **resposta** da etiqueta na página de **informação iProva SAML2.** Esta página ainda está aberta no seu outro separador de navegador.

1. A aplicação iProva espera que as afirmações do SAML num formato específico, o que requer que adicione mapeamentos personalizados de atributos à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos.

    ![image](common/default-attributes.png)

1. Além de acima, a aplicação iProva espera que poucos atributos sejam retransmitidos na resposta SAML que são mostradas abaixo. Estes atributos também são pré-povoados, mas pode revê-los de acordo com os seus requisitos.

    | Nome | Atributo fonte| Espaço de nomes  |
    | ---------------| -------- | -----|
    | `samaccountname` | `user.onpremisessamaccountname`| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|

1. No **set single sign-on com** a página SAML, na secção Certificado de **Assinatura SAML,** clique no botão de cópia para copiar o Url de **Metadados da Federação** da Aplicação e guarde-o no seu computador.

    ![O link de descarregamento do Certificado](common/copy-metadataurl.png)

## <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD

Nesta secção, você vai criar um utilizador de teste no portal Azure chamado B.Simon.

1. A partir do painel esquerdo no portal Azure, **selecione Azure Ative Directory**, selecione **Utilizadores**e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo de nome username@companydomain.extensiondo **Utilizador,** introduza o . Por exemplo, `B.Simon@contoso.com`.
   1. Selecione a caixa de verificação de **palavra-passe do Show** e, em seguida, escreva o valor que está apresentado na caixa **password.**
   1. Clique em **Criar**.

## <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, permitirá que b.Simon utilize um único sign-on Azure, concedendo acesso ao iProva.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **iProva**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

## <a name="create-iprova-test-user"></a>Criar o utilizador de teste iProva

1. Inscreva-se no iProva utilizando a conta **'Administrador'.**

2. Abra o menu **Ir para o** menu.

3. Selecione **gestão de aplicações.**

4. Selecione **Utilizadores** no painel **utilizadores e grupos** de utilizadores.

5. Selecione **Adicionar**.

6. Na caixa **username,** introduza o `B.Simon@contoso.com`nome de utilizador do utilizador como .

7. Na caixa de **nome Completo,** introduza um nome completo de utilizador como **B.Simon**.

8. Selecione a opção **Não palavra-passe (utilize uma única assinatura).**

9. Na caixa **de endereços de e-mail,** introduza o endereço de e-mail do utilizador como `B.Simon@contoso.com`.

10. Desloque-se até ao fim da página e selecione **Terminar**.

## <a name="configure-iprova-sso"></a>Configure iProva SSO

1. Inscreva-se no iProva utilizando a conta **'Administrador'.**

2. Abra o menu **Ir para o** menu.

3. Selecione **gestão de aplicações.**

4. Selecione **General** no painel de definições do **Sistema.**

5. Selecione **Editar**.

6. Percorra para baixo até o controlo de **acesso**.

    ![definições de controlo de acesso iProva](media/iprova-tutorial/iprova-accesscontrol.png)

7. Encontre a definição **Os utilizadores são automaticamente iniciados com as suas contas**de rede e alteram-na para **Sim, autenticação via SAML**. Opções adicionais agora aparecem.

8. Selecione **Configurar**.

9. Selecione **Next**.

10. IProva pergunta se deseja descarregar dados da federação a partir de um URL ou carregá-lo a partir de um ficheiro. Selecione a opção **From URL.**

    ![Baixar metadados da AD Azure](media/iprova-tutorial/iprova-download-metadata.png)

11. Colhe o URL de metadados que guardou no último passo da secção "Configure Azure AD single sign-on".

12. Selecione o botão em forma de seta para descarregar os metadados do Azure AD.

13. Quando o download estiver concluído, aparece o ficheiro De dados válido da **Federação Descarregada.**

14. Selecione **Next**.

15. Ignore a opção de **login** do Teste por enquanto e selecione **Next**.

16. Na **Reivindicação de utilizar a** caixa drop-down, selecione o nome da **conta do windows**.

17. Selecione **Concluir**.

18. Agora volta ao ecrã de **definições gerais da Edição.** Desloque-se até à parte inferior da página e selecione **OK** para salvar a sua configuração.

## <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo iProva no Painel de Acesso, deverá ser automaticamente inscrito no iProva para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e a inscrição única com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente iProva com Azure AD](https://aad.portal.azure.com/)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Como proteger o iProva com visibilidade e controlos avançados](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)