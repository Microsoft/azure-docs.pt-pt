---
title: 'Tutorial: Azure Ative Directory integração única de sign-on (SSO) com o Prisma Cloud SSO | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Prisma Cloud SSO.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/08/2021
ms.author: jeedes
ms.openlocfilehash: d92ca345b0800523d86ce7b42220209a1f237103
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "101651435"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-prisma-cloud-sso"></a>Tutorial: Azure Ative Directory integração única (SSO) com o Prisma Cloud SSO

Neste tutorial, você vai aprender a integrar Prisma Cloud SSO com Azure Ative Directory (Azure AD). Quando integrar o Prisma Cloud SSO com Azure AD, pode:

* Controle em Azure AD que tem acesso a Prisma Cloud SSO.
* Permita que os seus utilizadores sejam automaticamente inscritos no Prisma Cloud SSO com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Assinatura ativada por Prisma Cloud SSO (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Prisma Cloud SSO suporta SSO iniciado **iDP.**

* O Prisma Cloud SSO suporta o fornecimento do utilizador **Just In Time.**

## <a name="add-prisma-cloud-sso-from-the-gallery"></a>Adicione Prisma Cloud SSO da galeria

Para configurar a integração do Prisma Cloud SSO em AD Azure, precisa adicionar o Prisma Cloud SSO da galeria à sua lista de aplicações geridas para o SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **da galeria,** **digite Prisma Cloud SSO** na caixa de pesquisa.
1. Selecione **Prisma Cloud SSO** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-prisma-cloud-sso"></a>Configurar e testar Azure AD SSO para Prisma Cloud SSO

Configure e teste Azure AD SSO com Prisma Cloud SSO usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado no Prisma Cloud SSO.

Para configurar e testar O Azure AD SSO com o Prisma Cloud SSO, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure o Prisma Cloud SSO](#configure-prisma-cloud-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create Prisma Cloud SSO test user](#create-prisma-cloud-sso-test-user)** - para ter uma contraparte de B.Simon em Prisma Cloud SSO que está ligada à representação AD Azure do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração da aplicação **Prisma Cloud SSO,** encontre a secção **Gerir** e selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone de lápis para **configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. No **set-on único com** a página SAML, insira os valores para os seguintes campos:

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão: `https://app2.prismacloud.io/customer/<CUSTOMERID>`

    b. Os valores **url de resposta** são fixos e já pré-povoados no portal Azure. Tem de selecionar o URL apropriado de acordo com o seu requisito.

    > [!NOTE]
    > O valor do Identificador não é real. Atualize o valor com o identificador real. Contacte [a equipa de suporte do Cliente Prisma Cloud SSO](mailto:support@paloaltonetworks.com) para obter o valor. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre **o Certificado (Base64)** e selecione **Descarregamento** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

1. Na secção Configurar o **SSO da Nuvem prisma,** copie os URL(s) apropriados com base no seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso ao Prisma Cloud SSO.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Prisma Cloud SSO**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-prisma-cloud-sso"></a>Configurar prisma cloud Sso

Para configurar um único sign-on no lado **Do SSO da Nuvem de Prisma,** é necessário enviar o Certificado descarregado **(Base64)** e URLs copiados apropriados do portal Azure para a equipa de [suporte Do Prisma Cloud SSO.](mailto:support@paloaltonetworks.com) Eles definem esta definição para ter a ligação SSO SAML corretamente definida em ambos os lados.

### <a name="create-prisma-cloud-sso-test-user"></a>Criar utilizador de teste SSO em Nuvem de Prisma

Nesta secção, um utilizador chamado B.Simon é criado no Prisma Cloud SSO. O Prisma Cloud SSO suporta o provisionamento just-in-time, que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir no Prisma Cloud SSO, um novo é criado quando tenta aceder ao Prisma Cloud SSO.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções.

* Clique em Testar esta aplicação no portal Azure e deverá ser automaticamente inscrito no Prisma Cloud SSO para o qual configura o SSO.

* Pode utilizar as minhas apps do Microsoft. Quando clicar no azulejo Prisma Cloud SSO nas Minhas Apps, deverá ser automaticamente inscrito no Prisma Cloud SSO para o qual configura o SSO. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](../user-help/my-apps-portal-end-user-access.md)

## <a name="next-steps"></a>Passos seguintes

Uma vez configurado o Prisma Cloud SSO, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).