---
title: 'Tutorial: Integração do Diretório Ativo Azure com DigiCert | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o DigiCert.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/11/2021
ms.author: jeedes
ms.openlocfilehash: 1c7befc0b42cfa3460a1c9b375a95b97e3e65746
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100558429"
---
# <a name="tutorial-azure-active-directory-integration-with-digicert"></a>Tutorial: Integração do Diretório Ativo Azure com DigiCert

Neste tutorial, você vai aprender a integrar DigiCert com Azure Ative Directory (Azure AD). Quando integrar o DigiCert com a AD Azure, pode:

* Controlo em Azure AD que tem acesso ao DigiCert.
* Ative os seus utilizadores a serem automaticamente inscritos no DigiCert com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Subscrição única (SSO) ativada pela DigiCert.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* DigiCert suporta **IDP** iniciado SSO.

## <a name="add-digicert-from-the-gallery"></a>Adicione DigiCert da galeria

Para configurar a integração do DigiCert no AD Azure, é necessário adicionar o DigiCert da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar da secção **da galeria,** **escreva DigiCert** na caixa de pesquisa.
1. Selecione **DigiCert** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-digicert"></a>Configurar e testar Azure AD SSO para DigiCert

Configure e teste Azure AD SSO com DigiCert usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado no DigiCert.

Para configurar e testar Azure AD SSO com DigiCert, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure o DigiCert SSO](#configure-digicert-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create DigiCert test user](#create-digicert-test-user)** - para ter uma contraparte de B.Simon em DigiCert que está ligada à representação AD AD Azure do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração da aplicação **DigiCert,** encontre a secção **Gerir** e selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone de lápis para **configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **de Configuração Básica SAML,** execute os seguintes passos:

    Na caixa de texto **identifier,** digite o URL:  `https://www.digicert.com/sso`

5. A aplicação DigiCert espera as afirmações DOL num formato específico. Configure os seguintes pedidos para este pedido. Pode gerir os valores destes atributos a partir da secção **Atributos** do Utilizador na página de integração de aplicações. Na **configuração single Sign-On com** a página SAML, clique no botão **Editar** para abrir o diálogo **de Atributos do Utilizador.**

    ![Screenshot que mostra a secção "Atributos do Utilizador" com o botão "Editar" selecionado.](common/edit-attribute.png)

6. Na secção **'Reclamações** de Utilizador' no diálogo **'Atributos do Utilizador',** edite as alegações utilizando o **ícone Editar** ou adicione as alegações utilizando **adicionar uma nova alegação** para configurar o atributo de ficha SAML como mostrado na imagem acima e executar os seguintes passos: 

    | Name |  Atributo de origem|
    | ---------------| --------------- |
    | nameidentifier | user.userprincipalname |
    | empresa | < > de código de empresa |
    | digicertrole | CanAccessCertCentral |

    > [!Note]
    > O valor do atributo da **empresa** não é real. Atualize este valor com o código real da empresa. Para obter o valor da **empresa** atribua contacte [a equipa de suporte digiCert.](mailto:support@digicert.com)

    a. Clique **Em Adicionar nova reivindicação** para abrir o diálogo de reclamações do utilizador **Gerir.**

    ![Screenshot que mostra a secção "Reclamações do Utilizador" com os botões "Adicionar nova reivindicação" e "Guardar" realçados.](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Na caixa de texto **'Nome',** digite o nome do atributo indicado para esta linha.

    c. Deixe o **Espaço Namespace** em branco.

    d. Selecione Fonte como **Atributo**.

    e. A partir da lista **de atributos Source,** digite o valor de atributo mostrado para esta linha.

    f. Clique **em Ok**

    exemplo, Clique em **Guardar**.

7. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Metadadata XML** da Federação a partir das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

8. Na secção **Configurar DigiCert,** copie os URL(s) apropriados de acordo com o seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso ao DigiCert.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **DigiCert**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-digicert-sso"></a>Configure DigiCert SSO

Para configurar um único sign-on no lado **DigiCert,** você precisa enviar o **metdata XML da Federação** descarregado e URLs copiados apropriados do portal Azure para a equipa de [suporte DigiCert](mailto:support@digicert.com). Eles definem esta definição para ter a ligação SSO SAML corretamente definida em ambos os lados.

### <a name="create-digicert-test-user"></a>Criar utilizador de teste DigiCert

Nesta secção, cria-se um utilizador chamado Britta Simon em DigiCert. Trabalhe com a [equipa de suporte DigiCert](mailto:support@digicert.com) para adicionar os utilizadores na plataforma DigiCert. Os utilizadores devem ser criados e ativados antes de utilizar uma única s ativação.

## <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções.

* Clique em Testar esta aplicação no portal Azure e deverá ser automaticamente inscrito no DigiCert para o qual configura o SSO.

* Pode utilizar as minhas apps do Microsoft. Quando clicar no azulejo DigiCert nas Minhas Apps, deverá ser automaticamente inscrito no DigiCert para o qual configura o SSO. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="next-steps"></a>Passos seguintes

Uma vez configurado o DigiCert, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
