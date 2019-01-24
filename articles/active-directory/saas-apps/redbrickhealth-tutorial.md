---
title: 'Tutorial: Integração do Active Directory do Azure com o estado de funcionamento RedBrick | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e o estado de funcionamento RedBrick.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 26290c65-9aa3-42ab-8ba5-901b14dc8e73
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 1/3/2019
ms.author: jeedes
ms.openlocfilehash: 66ff2e7c2f9fb71bcf54cc5946afed34628809b2
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2019
ms.locfileid: "54816692"
---
# <a name="tutorial-azure-active-directory-integration-with-redbrick-health"></a>Tutorial: Integração do Active Directory do Azure com o estado de funcionamento RedBrick

Neste tutorial, saiba como integrar o estado de funcionamento RedBrick com o Azure Active Directory (Azure AD).
Integrar o estado de funcionamento RedBrick no Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao estado de funcionamento RedBrick.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para o estado de funcionamento RedBrick (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o estado de funcionamento RedBrick, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter a versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* RedBrick estado de funcionamento de logon único habilitado subscrição

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.


* Suporta o estado de funcionamento redBrick **IDP** iniciada SSO




## <a name="adding-redbrick-health-from-the-gallery"></a>Adicionando o estado de funcionamento RedBrick da Galeria

Para configurar a integração do Estado de funcionamento RedBrick com o Azure AD, terá de adicionar RedBrick do Estado de funcionamento a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar RedBrick do Estado de funcionamento a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **estado de funcionamento RedBrick**, selecione **estado de funcionamento RedBrick** no painel de resultados, em seguida, clique em **adicionar** botão para adicionar a aplicação.

     ![Estado de funcionamento redBrick na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com o estado de funcionamento RedBrick com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no estado de funcionamento RedBrick deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com o estado de funcionamento RedBrick, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar RedBrick estado de funcionamento do início de sessão único](#configure-redbrick-health-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste de estado de funcionamento RedBrick](#create-redbrick-health-test-user)**  - para ter um equivalente da Eduarda Almeida no estado de funcionamento RedBrick que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com o estado de funcionamento RedBrick, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), no **estado de funcionamento RedBrick** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, execute os seguintes passos:

    ![RedBrick URLs de domínio de estado de funcionamento e único informações de início de sessão](common/idp-relay.png)

    a. Na **identificador** caixa de texto, escreva um URL: `https://www.redbrickhealth.com`

    b. Na **URL de resposta** caixa de texto, escreva um URL: `https://sso-intg.redbrickhealth.com/sp/ACS.saml2`

    Para o ambiente de produção: `https://sso.redbrickhealth.com/sp/ACS.saml2`

    c. Clique em **definir URLs adicionais**.

    d. Na **estado de reencaminhamento** caixa de texto, escreva um URL com o seguinte padrão: `https://api-sso2.redbricktest.com/identity/sso/nbound?target=https://vanity9-sso2.redbrickdev.com/portal&connection=<companyname>conn1`

    > [!NOTE]
    > Valor de estado de reencaminhamento não é real. Atualize este valor com o estado real do reencaminhamento. Contacte [equipa de suporte de cliente do Estado de funcionamento RedBrick](https://home.redbrickhealth.com/contact/) para obter este valor. Também pode consultar os padrões mostrados a **configuração básica de SAML** secção no portal do Azure.

5. Aplicação de estado de funcionamento redBrick espera que as asserções SAML num formato específico. Configure as seguintes declarações para esta aplicação. Pode gerir os valores destes atributos do **atributos de utilizador** secção na página de integração de aplicações. Sobre o **definido no início de sessão único com o SAML** página, clique em **editar** botão para abrir **atributos de utilizador** caixa de diálogo.

    ![image](common/edit-attribute.png)

6. No **afirmações de utilizador** secção sobre o **atributos de utilizador** caixa de diálogo, configurar o atributo de token de SAML conforme mostrado na imagem acima e execute os seguintes passos:

    | Nome | Atributo de origem|
    | ---------------| --------------- | --------- |
    | Nome principal | ********** |
    | id de cliente | ********** |
    | id de participante | ********** |

    > [!NOTE]
    > Estes valores são para fins de referência apenas. Tem de definir os atributos de acordo com o requisito da sua organização. Entre em contato com [equipa de suporte do Estado de funcionamento RedBrick](https://home.redbrickhealth.com/contact/) para obter mais informações sobre as declarações necessárias.

    a. Clique em **Adicionar nova afirmação** para abrir o **afirmações de utilizador de gerir** caixa de diálogo.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Na **nome** caixa de texto, escreva o nome de atributo apresentado para essa linha.

    c. Deixe o **espaço de nomes** em branco.

    d. Selecione a origem de dado **atributo**.

    e. Partir do **atributo de origem** lista, digite o valor de atributo apresentado para essa linha.

    f. Clique em **Ok**

    g. Clique em **Guardar**.

7. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **certificado (Base64)** entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/certificatebase64.png)

8. Sobre o **configurar o estado de funcionamento RedBrick** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Azure Ad Identifier

    c. URL de fim de sessão

### <a name="configure-redbrick-health-single-sign-on"></a>Configurar o estado de funcionamento RedBrick início de sessão único

Para configurar o início de sessão único num **estado de funcionamento RedBrick** lado, terá de enviar o transferido **certificado (Base64)** e adequadas copiados URLs a partir do portal do Azure para [suporte de estado de funcionamento RedBrick equipe](https://home.redbrickhealth.com/contact/). Se definir esta definição para que a ligação de SAML SSO definidas corretamente em ambos os lados.

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

Nesta secção, vai ativar Eduarda Almeida utilizar do Azure-início de sessão único, concedendo acesso RedBrick o estado de funcionamento.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **estado de funcionamento RedBrick**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **estado de funcionamento RedBrick**.

    ![A ligação de estado de funcionamento RedBrick na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-redbrick-health-test-user"></a>Criar utilizador de teste de estado de funcionamento RedBrick

Nesta secção, vai criar um usuário chamado Eduarda Almeida no estado de funcionamento RedBrick. Trabalhar com [equipa de suporte do Estado de funcionamento RedBrick](https://home.redbrickhealth.com/contact/) para adicionar os utilizadores na plataforma do Estado de funcionamento RedBrick. Os utilizadores tem de ser criados e ativados antes de utilizar o início de sessão único.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico de estado de funcionamento RedBrick no painel de acesso, deve ser automaticamente conectado para o estado de funcionamento RedBrick para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [ Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicações e início de sessão único com o Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

