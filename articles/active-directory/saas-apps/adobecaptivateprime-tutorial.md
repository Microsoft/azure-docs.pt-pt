---
title: 'Tutorial: Integração do Diretório Ativo Azure com a Adobe Captivate Prime | Microsoft Docs'
description: Saiba como configurar um único sinal entre o Azure Ative Directory e o Adobe Captivate Prime.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/31/2021
ms.author: jeedes
ms.openlocfilehash: 87ce580e16d1ca3b90eb66562f06828d775b09ea
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2021
ms.locfileid: "106285688"
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-captivate-prime"></a>Tutorial: Integração do Azure Ative Directory com a Adobe Captivate Prime

Neste tutorial, você vai aprender a integrar Adobe Captivate Prime com Azure Ative Directory (Azure AD). Quando integrar o Adobe Captivate Prime com Azure AD, pode:

* Controlo em Azure AD que tem acesso ao Adobe Captivate Prime.
* Permita que os seus utilizadores sejam automaticamente inscritos no Adobe Captivate Prime com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Subscrição ativada pela Adobe Captivate Prime (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* Adobe Captivate Prime suporta **IDP** iniciado SSO.

> [!NOTE]
> O identificador desta aplicação é um valor fixo de cadeia para que apenas um caso possa ser configurado em um inquilino.

## <a name="add-adobe-captivate-prime-from-the-gallery"></a>Adicione Adobe Captivate Prime da galeria

Para configurar a integração da Adobe Captivate Prime em AD Azure, é necessário adicionar o Adobe Captivate Prime da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **da galeria,** **digite Adobe Captivate Prime** na caixa de pesquisa.
1. Selecione **Adobe Captivate Prime** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-adobe-captivate-prime"></a>Configurar e testar Azure AD SSO para Adobe Cativar Prime

Configure e teste Azure AD SSO com Adobe Captivate Prime usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado no Adobe Captivate Prime.

Para configurar e testar Azure AD SSO com Adobe Captivate Prime, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure o Adobe Captivar Prime SSO](#configure-adobe-captivate-prime-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create Adobe Captivate Prime test user](#create-adobe-captivate-prime-test-user)** - para ter uma contraparte de B.Simon em Adobe Captivate Prime que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração da aplicação **Adobe Captivate Prime,** encontre a secção **Gerir** e selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone de lápis para **configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na **configuração single Sign-On com página SAML,** execute os seguintes passos:

    a. Na caixa de texto **identifier,** digite o URL: `https://captivateprime.adobe.com`

    b. Na caixa de texto **URL de resposta,** digite o URL: `https://captivateprime.adobe.com/saml/SSO`

5. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Metadadata XML** da Federação a partir das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

6. Na secção **Configurar Adobe Cativar Prime,** copie os URL(s) apropriados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

7. Vá ao separador **Propriedades,** copie o **URL de acesso** ao Utilizador e cole-o no Bloco de Notas.

    ![O link de acesso ao utilizador](./media/adobecaptivateprime-tutorial/adobe.png)

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso ao Adobe Captivate Prime.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Adobe Captivate Prime**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-adobe-captivate-prime-sso"></a>Configurar Adobe Cativar Prime SSO

Para configurar um único sinal de acesso no **Adobe Captivate Prime** side, é necessário enviar o **Metadadata XML da Federação** descarregado, **o URL** de acesso ao utilizador copiado e urls copiados apropriados do portal Azure para a equipa de suporte Prime Da [Adobe Captivate.](mailto:captivateprimesupport@adobe.com) Eles definem esta definição para ter a ligação SSO SAML corretamente definida em ambos os lados.

### <a name="create-adobe-captivate-prime-test-user"></a>Criar o utilizador de teste De Adobe Captivate Prime

Nesta secção, cria-se um utilizador chamado Britta Simon em Adobe Captivate Prime. Trabalhe com a [equipa de suporte Da Adobe Captivate Prime](mailto:captivateprimesupport@adobe.com) para adicionar os utilizadores na plataforma Adobe Captivate Prime. Os utilizadores devem ser criados e ativados antes de utilizar uma única s ativação.

## <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções.

* Clique em Testar esta aplicação no portal Azure e deverá ser automaticamente inscrito no Adobe Captivate Prime para o qual configura o SSO.

* Pode utilizar as minhas apps do Microsoft. Quando clicar no azulejo Principal Do Adobe Captivate nas Minhas Apps, deverá ser automaticamente inscrito no Adobe Captivate Prime para o qual configura o SSO. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="next-steps"></a>Passos seguintes

Assim que configurar o Adobe Captivate Prime, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
