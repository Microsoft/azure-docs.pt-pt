---
title: 'Tutorial: Integração do Active Directory do Azure com SmartDraw | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e SmartDraw.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 6f8fbbe8-c771-4fa1-9326-5a9dac991ace
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/13/2019
ms.author: jeedes
ms.openlocfilehash: 910e485586eb8c43399f6f0b6791ab654003a19d
ms.sourcegitcommit: 4133f375862fdbdec07b70de047d70c66ac29d50
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/15/2019
ms.locfileid: "58005015"
---
# <a name="tutorial-azure-active-directory-integration-with-smartdraw"></a>Tutorial: Integração do Active Directory do Azure com SmartDraw

Neste tutorial, saiba como integrar SmartDraw com o Azure Active Directory (Azure AD).
Integrar SmartDraw no Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao SmartDraw.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para SmartDraw (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com SmartDraw, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter a versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* SmartDraw logon único habilitado subscrição

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Suporta SmartDraw **SP e IDP** iniciada SSO

* Suporta SmartDraw **Just In Time** aprovisionamento de utilizadores

## <a name="adding-smartdraw-from-the-gallery"></a>Adicionando SmartDraw da Galeria

Para configurar a integração do SmartDraw com o Azure AD, terá de adicionar SmartDraw a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar SmartDraw a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **SmartDraw**, selecione **SmartDraw** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![SmartDraw na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com SmartDraw com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no SmartDraw deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com SmartDraw, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar SmartDraw Single Sign-On](#configure-smartdraw-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste SmartDraw](#create-smartdraw-test-user)**  - para ter um equivalente da Eduarda Almeida na SmartDraw que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com SmartDraw, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **SmartDraw** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, se desejar configurar a aplicação na **IDP** modo iniciado, o utilizador não tem de realizar qualquer passo dado que a aplicação já está pré-integrado com o Azure.

    ![SmartDraw domínio e URLs únicas início de sessão em informações](common/preintegrated.png)

5. Clique em **definir URLs adicionais** e executar o passo seguinte, se desejar configurar a aplicação na **SP** iniciada pelo modo:

    ![SmartDraw domínio e URLs únicas início de sessão em informações](common/metadata-upload-additional-signon.png)

    Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão:  `https://cloud.smartdraw.com/sso/saml/login/<domain>`

    > [!NOTE]
    > O valor de URL de início de sessão não é real. Atualizar o valor de URL de início de sessão com o início de sessão no URL real, que é explicado mais tarde no tutorial. Também pode consultar os padrões mostrados a **configuração básica de SAML** secção no portal do Azure.

6. Aplicação de SmartDraw espera que as asserções SAML num formato específico, o que requer a adição de mapeamentos de atributos personalizado à sua configuração de atributos de token SAML. Captura de ecrã seguinte mostra a lista de atributos predefinidos. Clique em **edite** ícone para abrir **atributos do utilizador** caixa de diálogo.

    ![image](common/edit-attribute.png)

7. Além dos acima, o aplicativo SmartDraw espera mais alguns atributos a serem passados na resposta SAML. No **afirmações de utilizador** secção sobre o **atributos de utilizador** caixa de diálogo, execute os seguintes passos para adicionar o atributo de token de SAML conforme mostrado na tabela a seguir: 

    | Name | Atributo de origem|
    | ---------------| --------------- |
    | FirstName | user.givenname |
    | LastName | user.surname |
    | Email | user.mail |
    | Grupos | user.groups |

    a. Clique em **Adicionar nova afirmação** para abrir o **afirmações de utilizador de gerir** caixa de diálogo.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Na **nome** caixa de texto, escreva o nome de atributo apresentado para essa linha.

    c. Deixe o **espaço de nomes** em branco.

    d. Selecione a origem de dado **atributo**.

    e. Partir do **atributo de origem** lista, digite o valor de atributo apresentado para essa linha.

    f. Clique em **Ok**

    g. Clique em **Guardar**.

8. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **XML de metadados de Federação**  entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/metadataxml.png)

9. Sobre o **configurar SmartDraw** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão

### <a name="configure-smartdraw-single-sign-on"></a>Configurar SmartDraw Single Sign-On

1. Numa janela do browser web diferente, início de sessão para SmartDraw como administrador.

2. Clique em **início de sessão único** em Gerir a sua licença SmartDraw.

    ![Configuração de SmartDraw](./media/smartdraw-tutorial/configure01.png)

3. Na página de configuração, execute os seguintes passos:

    ![Configuração de SmartDraw](./media/smartdraw-tutorial/configure02.png)

    a. Na **seu domínio (como acme.com)** caixa de texto, escreva o seu domínio.

    b. Copiar o **Url de início de sessão iniciada de SP será** para a sua instância e cole-a na caixa de texto do URL de início de sessão na **configuração básica de SAML** no portal do Azure.
    
    c. Na **grupos de segurança para permitir o acesso de SmartDraw** caixa de texto, tipo **todas as pessoas**.

    d. Na **seu Url de emissor de SAML** caixa de texto, cole o valor de **do Azure AD identificador** que copiou do portal do Azure.

    e. No bloco de notas, abra o ficheiro de metadados XML que transferiu a partir do portal do Azure, copie o seu conteúdo e, em seguida, cole-o para o **Your metadados de SAML** caixa.

    f. Clique em **Guardar configuração** 

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD 

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

1. No portal do Azure, no painel esquerdo, selecione **do Azure Active Directory**, selecione **utilizadores**e, em seguida, selecione **todos os utilizadores**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](common/users.png)

2. Selecione **novo utilizador** na parte superior do ecrã.

    ![Novo utilizador botão](common/new-user.png)

3. Nas propriedades do utilizador, execute os seguintes passos.

    ![A caixa de diálogo de utilizador](common/user-properties.png)

    a. Na **Name** campo introduza **BrittaSimon**.
  
    b. Na **nome de utilizador** tipo de campo **brittasimon@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione **palavra-passe de Show** caixa de verificação e, em seguida, anote o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para SmartDraw.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **SmartDraw**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **SmartDraw**.

    ![A ligação de SmartDraw na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-smartdraw-test-user"></a>Criar utilizador de teste SmartDraw

Nesta secção, um usuário chamado Eduarda Almeida é criado na SmartDraw. SmartDraw suporta o aprovisionamento de utilizadores de just-in-time, que está ativado por predefinição. Não existe nenhum item de ação para nesta secção. Se um utilizador já não existir no SmartDraw, é criado um novo após a autenticação.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico SmartDraw no painel de acesso, deve ser automaticamente sessão iniciada no SmartDraw para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [ Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicações e início de sessão único com o Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

