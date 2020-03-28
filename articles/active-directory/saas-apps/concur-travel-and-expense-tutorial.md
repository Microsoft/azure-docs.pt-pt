---
title: 'Tutorial: Azure Ative Diretório integração individual (SSO) com Concur Travel and Expense [ Integração de Incur Travel and Expense ] Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e a Concur Travel and Expense.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 6401ace4-71c0-4778-8b15-900f2f5f0c4c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/10/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9dddd9f6904aa5ef7840850792aeabf04666dddc
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "72373425"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-concur-travel-and-expense"></a>Tutorial: Azure Ative Directory integração de inscrição única (SSO) com concur Travel and Expense

Neste tutorial, você aprenderá a integrar a Concur Travel and Expense com o Azure Ative Directory (Azure AD). Quando integrar a Concur Travel and Expense com a Azure AD, pode:

* Controlo em Azure AD que tem acesso a Viagens e Despesas Da Concur.
* Permita que os seus utilizadores sejam automaticamente inscritos na Concur Travel and Expense com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Assinatura de Viagem e Despesas.
* Uma função de "Administrador da Empresa" na sua conta de utilizador da Concur. Pode testar se tem o acesso certo indo para [a Ferramenta de Self-Service Concur SSO](https://www.concursolutions.com/nui/authadmin/ssoadmin). Se não tiver acesso, contacte o gestor de suporte da Concur ou implemente o projeto. 

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa O SSO Azure AD.

* Concur Travel and Expense suporta **IDP** e **SP** iniciado SSO
* Concur Travel and Expense suporta testes De SSO em ambiente de produção e implementação 

> [!NOTE]
> O identificador desta aplicação é um valor fixo de cadeia para cada uma das três regiões: EUA, EMEA e China. Assim, apenas uma instância pode ser configurada para cada região em um inquilino. 

## <a name="adding-concur-travel-and-expense-from-the-gallery"></a>Adicionar concur viagens e despesas da galeria

Para configurar a integração da Concur Travel and Expense em Azure AD, você precisa adicionar Concur Travel and Expense da galeria à sua lista de aplicações geridas saaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No Add da secção **galeria,** digite **Viagem e Despesas Concur** na caixa de pesquisa.
1. Selecione **Concur Travel and Expense** do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-concur-travel-and-expense"></a>Configure e teste Azure AD único signo para Viagem e Despesas Concur

Configure e teste Azure AD SSO com Concur Travel and Expense utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado em Viagens e Despesas concur.

Para configurar e testar o Azure AD SSO com a Concur Travel and Expense, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
    1. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
1. **[Configure concur Travel and Expense SSO](#configure-concur-travel-and-expense-sso)** - para configurar as definições de inscrição únicas no lado da aplicação.
    1. **[Crie o utilizador do teste de viagem e despesa da Concur](#create-concur-travel-and-expense-test-user)** - para ter uma contrapartida de B.Simon em Viagens e Despesas Concur que esteja ligada à representação da AD Azure do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações de **viagem e despesa sintetiza** a secção **Gerir** e selecione **um único sinal.**
1. Na página **de método de inscrição, selecione** **SAML**.
1. No **set single sign-on com** a página SAML, clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na secção **de Configuração Básica saml,** a aplicação está pré-configurada no modo iniciado **idp** e os URLs necessários já estão pré-povoados com o Azure. O utilizador precisa de guardar a configuração clicando no botão **Guardar.**

    > [!NOTE]
    > O identificador (ID da entidade) e o URL de Resposta (URL do Serviço de Consumidor de Afirmação) são específicos da região. Selecione com base no datacenter da sua entidade Concur. Se não conhecer o datacenter da sua entidade Concur, contacte o suporte da Concur. 

5. Na configuração do Single Sign-On com a página **SAML,** clique no ícone de edição/caneta para **o Atributo do Utilizador** para editar as definições. O Identificador de Utilizador Único precisa de corresponder login_id ao utilizador da Concur. Normalmente, deve alterar o **nome do utilizador para** **user.mail**.

    ![Editar atributo do utilizador](common/edit-attribute.png)

6. Na configuração de um único sessão com a página **SAML,** na secção Certificado de **Assinatura SAML,** encontre **metadados da Federação XML** e selecione **Download** para descarregar os metadados e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/metadataxml.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD

Nesta secção, você vai criar um utilizador de teste no portal Azure chamado B.Simon.

1. A partir do painel esquerdo no portal Azure, **selecione Azure Ative Directory**, selecione **Utilizadores**e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo de nome username@companydomain.extensiondo **Utilizador,** introduza o . Por exemplo, `B.Simon@contoso.com`.
   1. Selecione a caixa de verificação de **palavra-passe do Show** e, em seguida, escreva o valor que está apresentado na caixa **password.**
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, você permitirá que B.Simon use o único sign-on Azure, concedendo acesso a Concur Travel and Expense.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Concur Travel and Expense**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

## <a name="configure-concur-travel-and-expense-sso"></a>Configure Concur Travel and Expense SSO

1. Para configurar um único login no lado de **Viagem e Despesas da Concur,** precisa de carregar o XML de **Metadados da Federação** descarregado para a Ferramenta de [Self-Service Concur SSO](https://www.concursolutions.com/nui/authadmin/ssoadmin) e iniciar sessão com uma conta com a função "Administrador da Empresa". 

1. Clique em **Adicionar**.
1. Introduza um nome personalizado para o seu IDP, por exemplo "Azure AD (EUA)". 
1. Clique em **Upload XML File** e anexe **os Metadados da Federação XML** que descarregou anteriormente.
1. Clique em **Adicionar Metadados** para guardar a alteração.

    ![Screenshot da ferramenta de self-service Concur SSO](./media/concur-travel-and-expense-tutorial/add-metadata-concur-self-service-tool.png)

### <a name="create-concur-travel-and-expense-test-user"></a>Criar o utilizador do teste de viagem e despesa da Concur

Nesta secção, cria-se um utilizador chamado B.Simon em Viagens e Despesas Concur. Trabalhe com a equipa de suporte da Concur para adicionar os utilizadores na plataforma Concur Travel and Expense. Os utilizadores devem ser criados e ativados antes de utilizar um único sinal. 

> [!NOTE]
> B.Simon's Concur login id precisa de combinar com o identificador único de B.Simon no Azure AD. Por exemplo, se o identificador único Azure AD `B.Simon@contoso.com`de B.Simon é . B.Simon's Concur login id `B.Simon@contoso.com` também precisa ser. 

## <a name="configure-concur-mobile-sso"></a>Configure Concur Mobile SSO
Para ativar o SSO móvel Concur, tem de dar **URL de acesso**ao utilizador da equipa de suporte Concur . Siga os passos abaixo para obter URL de acesso ao **utilizador** a partir de Azure AD:
1. Ir a **aplicações da Enterprise**
1. Clique **em Viajar e Despesas concur**
1. Clique em **Propriedades**
1. Copy **User access URL** e dar este URL para suporte A cur

> [!NOTE]
> A opção self-service para configurar o SSO não está disponível, por isso trabalhe com a equipa de suporte da Concur para ativar o SSO móvel. 

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Concur Travel and Expense no Painel de Acesso, deverá ser automaticamente inscrito na Concur Travel and Expense para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e a inscrição única com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente a Concur Travel and Expense com a Azure AD](https://aad.portal.azure.com/)

