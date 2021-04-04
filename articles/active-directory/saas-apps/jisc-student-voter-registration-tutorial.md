---
title: 'Tutorial: Azure Ative Directory integração única de sign-on (SSO) com o Registo de Eleitores de Estudantes jisc | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Jisc Student Voter Registration.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/26/2019
ms.author: jeedes
ms.openlocfilehash: 445fcefc3c7ff5dc077c4b5992cb921f3ffafb55
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92459502"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-jisc-student-voter-registration"></a>Tutorial: Azure Ative Directory single sign-on (SSO) integração com Jisc Student Voter Registration

Neste tutorial, você aprenderá a integrar o Jisc Student Voter Registration com Azure Ative Directory (Azure AD). Quando integrar o Registo de Eleitor de Estudantes jisc com Azure AD, pode:

* Controlo em Azure AD que tem acesso ao Registo de Eleitores de Estudantes jisc.
* Permita que os seus utilizadores sejam automaticamente inscritos no Jisc Student Voter Registration com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Jisc Student Voter Registration single sign-on (SSO) enabled subscrição.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Jisc Student Registration apoia **SP** iniciado SSO
* O registo de eleitores de estudantes jisc suporta o fornecimento de utilizadores **just in time**

## <a name="adding-jisc-student-voter-registration-from-the-gallery"></a>Adicionar inscrição de eleitor de estudante jisc da galeria

Para configurar a integração do Jisc Student Voter Registration em Azure AD, é necessário adicionar o Registo de Eleitor de Estudante Jisc da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **da galeria,** digite **o Registo de Eleitor de Estudante Jisc** na caixa de pesquisa.
1. Selecione **Jisc Student Voter Registration** do painel de resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-jisc-student-voter-registration"></a>Configurar e testar Azure AD único sign-on para Jisc Student Voter Registration

Configure e teste Azure AD SSO com Jisc Student Voter Registration usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado no Registo de Eleitores de Estudantes jisc.

Para configurar e testar a Azure AD SSO com o Jisc Student Voter Registration, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    * Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    * **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure o SSO de Registo de Eleitores de Estudantes jisc](#configure-jisc-student-voter-registration-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    * **[Create Jisc Student Voter Registration user](#create-jisc-student-voter-registration-test-user)** - para ter uma contrapartida de B.Simon no Jisc Student Voter Registration que está ligada à representação Azure AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure,](https://portal.azure.com/)na página de integração do **pedido de inscrição de eleitores do estudante Jisc,** encontre a secção **Gerir** e selecione um **único sinal**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** insira os valores para o seguinte campo:

    Na caixa de texto **URL de entrada de inscrição,** digite um URL:  `https://www.studentvoterregistration.ac.uk/consent`

1. A aplicação de Registo de Eleitores de Estudantes jisc espera as afirmações DO SAML num formato específico, o que requer que adicione mapeamentos de atributos personalizados à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos.

    ![image](common/default-attributes.png)

1. Além de acima, o pedido de inscrição de eleitores de estudante jisc espera que alguns mais atributos sejam repercutidos na resposta SAML que são mostrados abaixo. Estes atributos também são pré-povoados, mas pode revê-los de acordo com os seus requisitos.

    | Name |  Atributo de origem|
    | ---------|  --------- |
    | código postal | user.postalcode |
    | Identificador de utilizador único | user.objectid |

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre o **Metadados XML da Federação** e selecione **Descarregue** para descarregar o certificado e guarde-o no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

1. Na secção **De Inscrição de Eleitores de Estudante jisc,** copie os URL(s) apropriados com base na sua exigência.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso ao Registo de Eleitores de Estudante jisc.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Jisc Student Voter Registration**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-jisc-student-voter-registration-sso"></a>Configure Jisc Student Registr SSO

Para configurar um único sign-on no lado **do Registo de Eleitores de Estudantes jisc,** você precisa enviar o **metdata XML da Federação** descarregado e URLs copiados apropriados do portal Azure para a equipa de apoio ao [registo de eleitores de estudante jisc](mailto:studentvote@jisc.ac.uk). Eles definem esta definição para ter a ligação SSO SAML corretamente definida em ambos os lados.

### <a name="create-jisc-student-voter-registration-test-user"></a>Criar utilizador de teste de recenseamento eleitoral de estudante jisc

Nesta secção, um utilizador chamado B.Simon é criado no Jisc Student Voter Registration. O Registo de Eleitores de Estudantes jisc suporta o provisionamento do utilizador just-in-time, que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir no Registo de Eleitor de Estudante jisc, um novo é criado após a autenticação.

## <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo do Registo de Eleitores de Estudante jisc no Painel de Acesso, deverá ser automaticamente inscrito no Registo de Eleitor de Estudante jisc para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure ](./tutorial-list.md)

- [O que é o acesso à aplicação e um único acesso ao Azure Ative Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimente o Registo de Eleitor de Estudante jisc com Azure AD](https://aad.portal.azure.com/)