---
title: 'Tutorial: Integração do Diretório Ativo Azure com a Nuvem De Análise SAP [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o SAP Analytics Cloud.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 6c5e44f0-4e52-463f-b879-834d80a55cdf
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1fd01c86086c7491d22f655fcba5da237286412f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "68347793"
---
# <a name="tutorial-integrate-sap-analytics-cloud-with-azure-active-directory"></a>Tutorial: Integrar a Nuvem De Análise SAP com diretório Ativo Azure

Neste tutorial, você vai aprender a integrar a Nuvem de Análise SAP com o Diretório Ativo Azure (Azure AD). Quando integrar a Nuvem De Análise SAP com a AD Azure, pode:

* Controle em Azure AD que tem acesso a SAP Analytics Cloud.
* Ative que os seus utilizadores sejam automaticamente inscritos na SAP Analytics Cloud com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* SAP Analytics Cloud única subscrição ativada por SSO.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.

* SAP Analytics Cloud suporta **SP** iniciado SSO

## <a name="adding-sap-analytics-cloud-from-the-gallery"></a>Adicionando sap Analytics Cloud da galeria

Para configurar a integração da Nuvem De Análise SAP em Azure AD, você precisa adicionar SAP Analytics Cloud da galeria à sua lista de aplicações saaS geridas.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No **Add da** secção galeria, digite **SAP Analytics Cloud** na caixa de pesquisa.
1. Selecione **SAP Analytics Cloud** a partir do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Configure e teste Azure AD SSO com SAP Analytics Cloud utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado na Nuvem de Análise SAP.

Para configurar e testar o Azure AD SSO com a Nuvem De Análise SAP, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o SSO de Nuvem de Análise SAP](#configure-sap-analytics-cloud-sso)** Analytics - para configurar as definições de Início único no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
4. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
5. **[Crie](#create-sap-analytics-cloud-test-user)** o utilizador de teste SAP Analytics Cloud - para ter uma contrapartida de B.Simon na Nuvem De Análise SAP que está ligada à representação do utilizador da AD Azure.
6. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **da SAP Analytics Cloud,** encontre a secção **Gerir** e selecione **um único sinal .**
1. Na página **Select a Single sign-on,** selecione **SAML**.
1. Na configuração do Single Sign-On com a página **SAML,** clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na secção **Basic SAML Configuration,** introduza os valores para os seguintes campos:

    a. No **Sign on URL** text box, digite um URL utilizando o seguinte padrão:

    | |
    |-|-|
    | `https://<sub-domain>.sapanalytics.cloud/` |
    | `https://<sub-domain>.sapbusinessobjects.cloud/` |

    b. Na caixa de texto **identificador (Id da entidade),** digite um URL utilizando o seguinte padrão:
    
    | |
    |-|-|
    | `<sub-domain>.sapbusinessobjects.cloud` |
    | `<sub-domain>.sapanalytics.cloud` |

    > [!NOTE] 
    > Os valores nestes URLs são apenas para demonstração. Atualize os valores com o URL de inscrição real e o URL identificador. Para obter o URL de inscrição, contacte a equipa de suporte ao [Cliente Da Nuvem De Análise SAP](https://help.sap.com/viewer/product/SAP_BusinessObjects_Cloud/release/). Pode obter o URL identificador descarregando os metadados SAP Analytics Cloud da consola de administração. Isto é explicado mais tarde no tutorial.

4. Na configuração de um único sign-on com a página **SAML,** na secção certificado de **assinatura SAML,** encontre **metadados da Federação XML** e selecione **Descarregar** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/metadataxml.png)

6. Na secção Configurar a **Cloud SAP Analytics,** copie os URL(s) apropriados com base no seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="configure-sap-analytics-cloud-sso"></a>Configure SAP Analytics Cloud SSO

1. Numa janela diferente do navegador web, inscreva-se no site da sua empresa SAP Analytics Cloud como administrador.

2. Selecione **Menu** > **System** > **Administration**.
    
    ![Selecione Menu, em seguida, Sistema, e, em seguida, Administração](./media/sapboc-tutorial/config1.png)

3. No separador **'Segurança',** selecione o ícone **Editar** (caneta).
    
    ![No separador 'Segurança', selecione o ícone Editar](./media/sapboc-tutorial/config2.png)  

4. Para **o método de autenticação,** selecione **SAML Single Sign-On (SSO)**.

    ![Selecione SAML Single Sign-On para o método de autenticação](./media/sapboc-tutorial/config3.png)  

5. Para descarregar os metadados do fornecedor de serviços (Passo 1), selecione **Download**. No ficheiro de metadados, encontre e copie o valor **id da entidade.** No portal Azure, no diálogo básico de **configuração SAML,** cola o valor na caixa **de identificação.**

    ![Copiar e colar o valor id iD da entidade](./media/sapboc-tutorial/config4.png)  

6. Para fazer o upload dos metadados do fornecedor de serviços (Passo 2) no ficheiro que descarregou do portal Azure, sob os metadados do Fornecedor de **Identidade de Upload,** selecione **Upload**.  

    ![Sob os metadados do Fornecedor de Identidade de Upload, selecione Upload](./media/sapboc-tutorial/config5.png)

7. Na lista de **Atributos** do Utilizador, selecione o atributo do utilizador (Passo 3) que pretende utilizar para a sua implementação. Este utilizador atribui mapas ao fornecedor de identidade. Para introduzir um atributo personalizado na página do utilizador, utilize a opção **de Mapeamento SAML Personalizado.** Ou, pode selecionar o **Email** ou o **ID do utilizador** como atributo do utilizador. No nosso exemplo, selecionamos **o Email** porque mapeámos a alegação do identificador de utilizador com o atributo principal do nome de **utilizador** na secção **Atributos de Utilizador & Reclamações** no portal Azure. Isto fornece um e-mail único de utilizador, que é enviado para a aplicação SAP Analytics Cloud em todas as respostas SAML bem sucedidas.

    ![Selecionar Atributo do Utilizador](./media/sapboc-tutorial/config6.png)

8. Para verificar a conta com o fornecedor de identidade (Passo 4), na caixa **De Login Credencial (Email),** introduza o endereço de e-mail do utilizador. Em seguida, selecione **Verificar conta**. O sistema adiciona credenciais de insessão à conta de utilizador.

    ![Insira o e-mail e selecione Verificar Conta](./media/sapboc-tutorial/config7.png)

9. Selecione o ícone **Guardar.**

    ![Salvar o ícone](./media/sapboc-tutorial/save.png)

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

Nesta secção, permitirá que b.Simon utilize um único sign-on Azure, concedendo acesso à Cloud SAP Analytics.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **SAP Analytics Cloud**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

### <a name="create-sap-analytics-cloud-test-user"></a>Criar o utilizador de teste SAP Analytics Cloud

Os utilizadores de AD Azure devem ser aprovisionados no SAP Analytics Cloud antes de poderem iniciar sessão no SAP Analytics Cloud. Na Nuvem De Análise SAP, o provisionamento é uma tarefa manual.

Para fornecer uma conta de utilizador:

1. Inscreva-se no site da empresa SAP Analytics Cloud como administrador.

2. Selecione**Utilizadores de****Segurança do** >  **Menu** > .

    ![Adicionar Empregado](./media/sapboc-tutorial/user1.png)

3. Na página **Utilizadores,** para adicionar novos **+** detalhes do utilizador, selecione . 

    ![Adicionar página de Utilizadores](./media/sapboc-tutorial/user4.png)

    Em seguida, complete os seguintes passos:

    a. Na caixa DE IDENTIFICAÇÃO DO **UTILIZADOR,** introduza o ID do utilizador do utilizador, como **B**.

    b. Na caixa **FIRST NAME,** introduza o primeiro nome do utilizador, como **B**.

    c. Na caixa **DE APELIDO,** introduza o último nome do utilizador, como **Simon**.

    d. Na caixa **DISPLAY NAME,** introduza o nome completo do utilizador, como **B.Simon**.

    e. Na caixa **E-MAIL,** introduza o endereço `b.simon@contoso.com`de e-mail do utilizador, como .

    f. Na página **Select Roles,** selecione a função adequada para o utilizador e, em seguida, selecione **OK**.

      ![Selecionar função](./media/sapboc-tutorial/user3.png)

    g. Selecione o ícone **Guardar.**

### <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo SAP Analytics Cloud no Painel de Acesso, deve ser automaticamente inscrito na Nuvem de Análise SAP para a qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

