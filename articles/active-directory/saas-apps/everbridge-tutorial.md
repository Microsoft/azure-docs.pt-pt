---
title: 'Tutorial: Integração do Azure Ative Directory com a Everbridge | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Everbridge.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/27/2021
ms.author: jeedes
ms.openlocfilehash: 6e281931eb4646e09bb9aa3226ed7d0735c84e3f
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101643784"
---
# <a name="tutorial-azure-active-directory-integration-with-everbridge"></a>Tutorial: Integração do Azure Ative Directory com a Everbridge

Neste tutorial, aprende-se a integrar a Everbridge com o Azure Ative Directory (Azure AD).
Quando integrar o Everbridge com a AD Azure, pode:

* Controlo em Azure AD que tem acesso a Everbridge.
* Permita que os seus utilizadores sejam automaticamente inscritos na Everbridge com as suas contas AD Azure. Este controlo de acesso chama-se único sinal de acesso (SSO).
* Gerencie as suas contas numa localização central utilizando o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da AD Azure com a Everbridge, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Uma assinatura Everbridge que usa um único sinal de acesso.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* Everbridge apoia SSO iniciado pelo IDP.

## <a name="add-everbridge-from-the-gallery"></a>Adicione Everbridge da Galeria

Para configurar a integração da Everbridge no AD Azure, precisa adicionar everbridge da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Add da secção **da galeria,** escreva **Everbridge** na caixa de pesquisa.
1. Selecione **Everbridge** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-everbridge"></a>Configurar e testar Azure AD SSO para Everbridge

Configure e teste Azure AD SSO com Everbridge usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado em Everbridge.

Para configurar e testar a Azure AD SSO com a Everbridge, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure o Everbridge SSO](#configure-everbridge-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create Everbridge test user](#create-everbridge-test-user)** - para ter uma contraparte de B.Simon em Everbridge que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração da aplicação **Everbridge,** encontre a secção **Gerir** e selecione **Single sign-on**.
1. Na página **de método de inscrição única,** selecione **SAML**.
1. Na **configuração single Sign-On com página SAML,** clique no ícone de lápis para **configuração SAML básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

    >[!NOTE]
    >Configure a aplicação como portal do gestor *ou* como portal de membros no portal Azure e no portal Everbridge.

4. Para configurar a aplicação **Everbridge** como **portal de gerente da Everbridge,** na secção De **Configuração Básica SAML,** siga estes passos:

    ![Everbridge domínio e URLs informações únicas de súmis](common/idp-intiated.png)

    a. Na caixa **identifier,** introduza um URL que siga o padrão.
    `https://sso.everbridge.net/<API_Name>`

    b. Na caixa **URL de resposta,** introduza um URL que segue o padrão.
    `https://manager.everbridge.net/saml/SSO/<API_Name>/alias/defaultAlias`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com os valores reais de IDENTIFICAÇÃO e URL de resposta. Para obter estes valores, contacte a equipa de apoio da [Everbridge.](mailto:support@everbridge.com) Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

5. Para configurar a aplicação **Everbridge** como portal membro da **Everbridge,** na secção **De Configuração Básica SAML,** siga estes passos:

  * Se pretender configurar a aplicação no modo iniciado pelo IDP, siga estes passos:

     ![Everbridge domínio e URLs informações únicas de início de súps para o modo iniciado pelo IDP](common/idp-intiated.png)

    a. Na caixa **de identificador,** introduza um URL que segue o padrão `https://sso.everbridge.net/<API_Name>/<Organization_ID>`

    b. Na caixa **URL de resposta,** insira um URL que segue o padrão `https://member.everbridge.net/saml/SSO/<API_Name>/<Organization_ID>/alias/defaultAlias`

   * Se pretender configurar a aplicação no modo iniciado pelo SP, selecione **Definir URLs adicionais** e siga este passo:

     ![Everbridge domínio e URLs informações únicas de início para o modo iniciado pelo SP](common/both-signonurl.png)

     a. Na **caixa de URL** sign in, insira um URL que segue o padrão `https://member.everbridge.net/saml/login/<API_Name>/<Organization_ID>/alias/defaultAlias?disco=true`

     > [!NOTE]
     > Estes valores não são reais. Atualize estes valores com o identificador real, URL de resposta e assine os valores de URL. Para obter estes valores, contacte a equipa de apoio da [Everbridge.](mailto:support@everbridge.com) Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

6. Na **configuração single Sign-On com página SAML,** na secção certificado de assinatura **SAML,** selecione **Descarregue** para descarregar o **Metadadata XML da Federação**. Guarde no seu computador.

    ![Link de descarregamento de certificado](common/metadataxml.png)

7. Na secção **Configurar Everbridge,** copie os URLs de que necessita para os seus requisitos:

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso a Everbridge.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Everbridge**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

### <a name="configure-everbridge-sso"></a>Configurar Everbridge SSO

Para configurar o SSO em **Everbridge** como uma aplicação **do portal de gerentes da Everbridge,** siga estes passos.
 
1. Numa janela diferente do navegador, inscreva-se no Everbridge como administrador.

1. No menu em cima, selecione o **separador Definições.** Em **Segurança**, selecione **Single Sign-On**.
   
     ![Configurar o início de sessão único](./media/everbridge-tutorial/sso.png)
   
     a. Na caixa **Nome,** insira o nome do fornecedor de identificador. Um exemplo é o nome da sua empresa.
   
     b. Na caixa **API Name,** insira o nome da API.
   
     c. Selecione **Escolha o Ficheiro** para carregar o ficheiro de metadados que descarregou a partir do portal Azure.
   
     d. Para **a Localização de Identidade SAML,** selecione **Identidade está no elemento identificador de nome da declaração do sujeito**.
   
     e. Na caixa **URL do Fornecedor de Identidade,** cole o valor URL de **login** que copiou do portal Azure.
   
     f. Para **o prestador de serviços iniciado, o pedido de ligação**, selecione HTTP **Redirect**.

     exemplo, Selecione **Guardar**.

### <a name="configure-everbridge-as-everbridge-member-portal-sso"></a>Configure Everbridge como porta membro da Everbridge SSO

Para configurar um único sign-on em **Everbridge** como um **portal membro da Everbridge,** envie o **Metdata XML da Federação** descarregado para a [equipa de suporte everbridge](mailto:support@everbridge.com). Eles definem esta definição para ter a ligação SSO SAML corretamente definida em ambos os lados.

### <a name="create-everbridge-test-user"></a>Criar utilizador de teste everbridge

Nesta secção, cria-se a utilizadora de testes Britta Simon em Everbridge. Para adicionar utilizadores na plataforma Everbridge, trabalhe com a equipa de apoio da [Everbridge.](mailto:support@everbridge.com) Os utilizadores devem ser criados e ativados em Everbridge antes de utilizar uma única s ativação. 

### <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções.

* Clique em Testar esta aplicação no portal Azure e deverá ser automaticamente inscrito no Everbridge para o qual configura o SSO.

* Pode utilizar as minhas apps do Microsoft. Quando clicar no azulejo everbridge nas Minhas Apps, deverá ser automaticamente inscrito no Everbridge para o qual configura o SSO. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](../user-help/my-apps-portal-end-user-access.md)

## <a name="next-steps"></a>Passos seguintes

Uma vez configurado Everbridge, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).