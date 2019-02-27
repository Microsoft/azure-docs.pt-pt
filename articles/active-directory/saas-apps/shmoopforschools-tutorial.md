---
title: 'Tutorial: Integração do Active Directory do Azure com Shmoop para instituições de ensino | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e Shmoop para instituições de ensino.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 1d75560a-55b3-42e9-bda1-92b01c572d8e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/23/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4d980bf28fb9248cc88c4b0802817e85992ef9ee
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56869705"
---
# <a name="tutorial-azure-active-directory-integration-with-shmoop-for-schools"></a>Tutorial: Integração do Active Directory do Azure com Shmoop para escolas

Neste tutorial, saiba como integrar Shmoop para instituições de ensino com o Azure Active Directory (Azure AD).

Integrar Shmoop para instituições de ensino com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso para Shmoop para instituições de ensino.
- Pode permitir que os utilizadores automaticamente obter a sessão iniciada no Shmoop para instituições de ensino com as suas contas do Azure AD.
- Pode gerir as suas contas num local central, o portal do Azure.

Para obter mais informações sobre a integração de aplicações SaaS com o Azure AD, consulte [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Shmoop para instituições de ensino, terá dos seguintes itens:

- Uma subscrição do Azure
- Um Shmoop para instituições de ensino logon único habilitado subscrição

> [!NOTE]
> Não é recomendado utilizar um ambiente de produção para testar os passos neste tutorial.

Para testar os passos neste tutorial, recomendamos:

- Usando sua evironment de produção apenas se for necessário.
- Obter um [durante um mês avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/) se ainda não tiver um ambiente de avaliação do Azure AD.

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, vai testar do Azure AD início de sessão único num ambiente de teste. O cenário que é descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando Shmoop para instituições de ensino da Galeria
2. Configuração e teste do Azure AD início de sessão único

## <a name="add-shmoop-for-schools-from-the-gallery"></a>Adicionar Shmoop para instituições de ensino a partir da Galeria
Para configurar a integração do Shmoop para instituições de ensino com o Azure AD, terá de adicionar Shmoop para instituições de ensino a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar Shmoop para instituições de ensino a partir da galeria, siga os passos seguintes:**

1. Na [portal do Azure](https://portal.azure.com), no painel esquerdo, selecione a **Azure Active Directory** ícone. 

    ![O botão do Azure Active Directory][1]

2. Aceda a **aplicações empresariais**. Em seguida, aceda a **todos os aplicativos**.

    ![O painel de aplicações empresariais][2]
    
3. Para adicionar uma nova aplicação, selecione o **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo][3]

4. Na caixa de pesquisa, escreva **Shmoop para instituições de ensino**. Selecione **Shmoop para instituições de ensino** nos resultados, em seguida, selecione a **Add** botão para adicionar a aplicação.

    ![Shmoop para instituições de ensino na lista de resultados](./media/shmoopforschools-tutorial/tutorial_shmoopforschools_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com Shmoop para escolas com base num utilizador de teste chamado "Eduarda Almeida."

Para o início de sessão único funcione, o Azure AD precisa saber que o utilizador de contraparte no Shmoop para instituições de ensino for para um utilizador no Azure AD. Em outras palavras, tem de estabelecer uma ligação entre um utilizador do Azure AD e o utilizador relacionado no Shmoop para instituições de ensino.

Para configurar e testar o Azure AD início de sessão único com Shmoop para instituições de ensino, conclua os seguintes blocos de construção:

1. [Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on) para permitir aos utilizadores utilizar esta funcionalidade.
2. [Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user) para testar o Azure AD início de sessão único com Eduarda Almeida.
3. [Criar um utilizador de teste Shmoop para instituições de ensino](#create-a-shmoop-for-schools-test-user) ter um equivalente da Eduarda Almeida na Shmoop para instituições de ensino que está ligado a representação do Azure AD do utilizador.
4. [Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user) para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. [Testar início de sessão único](#test-single-sign-on) para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, pode ativar o Azure AD início de sessão único no portal do Azure e configurar início de sessão único em seu aplicativo Shmoop para instituições de ensino.

**Para configurar o Azure AD início de sessão único com Shmoop para instituições de ensino, execute os seguintes passos:**

1. No portal do Azure, sobre o **Shmoop para instituições de ensino** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única][4]

2. Na **início de sessão único** caixa de diálogo, no menu suspenso sob **modo de início de sessão único**, selecione **baseado em SAML logon**.
 
    ![Caixa de diálogo de início de sessão único](./media/shmoopforschools-tutorial/tutorial_shmoopforschools_samlbase.png)

3. Na **Shmoop para instituições de ensino com o domínio e URLs** secção, siga os passos seguintes:

    ![Configurar o início de sessão único](./media/shmoopforschools-tutorial/tutorial_shmoopforschools_url.png)

    a. Na **URL de início de sessão** caixa, escreva um URL com o seguinte padrão: `https://schools.shmoop.com/public-api/saml2/start/<uniqueid>`

    b. Na **identificador** caixa, escreva um URL com o seguinte padrão: `https://schools.shmoop.com/<uniqueid>`

    > [!NOTE] 
    > Estes valores não são reais. Atualize estes valores com o URL de início de sessão real e o identificador. Contacte os [equipa de suporte de cliente de Shmoop para instituições de ensino](mailto:support@shmoop.com) obter esses valores. 
 
4. O aplicativo Shmoop para instituições de ensino espera que as asserções SAML num formato específico. Configure as seguintes declarações para esta aplicação. Pode gerir os valores destes atributos do **atributos de utilizador** secção na página de integração de aplicativo. Captura de ecrã seguinte mostra como configurar as asserções:

    ![Configurar o início de sessão único](./media/shmoopforschools-tutorial/tutorial_attribute.png)

    > [!NOTE]
    > Shmoop para instituição de ensino suporta duas funções para os utilizadores: **Professor** e **estudante**. Configure estas funções no Azure AD para que os utilizadores podem ser atribuídos as funções adequadas. Para compreender como configurar funções no Azure AD, veja [gerir o acesso com RBAC e o portal do Azure](../../role-based-access-control/role-assignments-portal.md).
    
5. Na **atributos de utilizador** secção a **início de sessão único** diálogo caixa, configure o atributo de token SAML, conforme mostrado na imagem anterior.  Em seguida, siga os passos seguintes:

    | Nome de atributo | Valor do atributo |
    | -------------- | --------------- |
    | função           | user.assignedroles |

    a. Para abrir o **adicionar atributo** caixa de diálogo, selecione **adicionar atributo**.
    
    ![Configurar o início de sessão único](./media/shmoopforschools-tutorial/tutorial_attribute_04.png)
    
    ![Configurar o início de sessão único](./media/shmoopforschools-tutorial/tutorial_attribute_05.png)
    
    b. Na **nome** , escreva o nome de atributo que é mostrado para essa linha.
    
    c. Partir do **valor** , selecione o valor do atributo que é mostrado para essa linha.

    d. Deixe o **espaço de nomes** caixa vazia.
    
    e. Selecione **Ok**.

6. Selecione o botão **Guardar**.

    ![Configurar o início de sessão único](./media/shmoopforschools-tutorial/tutorial_general_400.png)

7. Sobre o **certificado de assinatura SAML** secção, clique no botão de cópia para copiar **Url de metadados de Federação de aplicação** e cole-o no bloco de notas.

    ![O link de download de certificado](./media/shmoopforschools-tutorial/tutorial_shmoopforschools_certificate.png)

8. Para configurar o início de sessão único no **Shmoop para instituições de ensino** lado, terá de enviar o **Url de metadados de Federação de aplicação** para o [Shmoop para instituições de ensino a equipa de suporte](mailto:support@shmoop.com).

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção é criar um utilizador de teste chamado Eduarda Almeida no portal do Azure.

   ![Criar um utilizador de teste do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, siga os passos seguintes:**

1. No portal do Azure, no painel esquerdo, selecione o **do Azure Active Directory** botão.

    ![O botão do Azure Active Directory](./media/shmoopforschools-tutorial/create_aaduser_01.png)

2. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos**. Em seguida, selecione **todos os utilizadores**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](./media/shmoopforschools-tutorial/create_aaduser_02.png)

3. Para abrir o **utilizador** caixa de diálogo, selecione **Add** na parte superior do **todos os utilizadores** caixa de diálogo.

    ![Botão Adicionar](./media/shmoopforschools-tutorial/create_aaduser_03.png)

4. Na **utilizador** diálogo caixa, siga os passos seguintes:

    ![A caixa de diálogo de utilizador](./media/shmoopforschools-tutorial/create_aaduser_04.png)

    a. Na **Name** , escreva **BrittaSimon**.

    b. Na **nome de utilizador** , escreva o endereço de e-mail do utilizador Eduarda Almeida.

    c. Selecione o **mostrar palavra-passe** caixa de verificação e, em seguida, anote o valor que é apresentado na **palavra-passe** caixa.

    d. Selecione **Criar**.
 
### <a name="create-a-shmoop-for-schools-test-user"></a>Criar um utilizador de teste Shmoop para escolas

O objetivo desta secção é criar um usuário chamado Eduarda Almeida no Shmoop para instituições de ensino. Shmoop para instituições de ensino suporta o aprovisionamento de just-in-time, que está ativado por predefinição. Não existe nenhum item de ação para nesta secção. Se ainda não existir um novo utilizador, é criada durante a tentativa de acessar Shmoop para instituições de ensino.

>[!NOTE]
>Se precisar de criar manualmente um utilizador, entre em contato com o [equipa de suporte de Shmoop para instituições de ensino](mailto:support@shmoop.com).

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para Shmoop para instituições de ensino.

![Atribuir a função de utilizador][200] 

**Para atribuir a Eduarda Almeida a Shmoop para instituições de ensino, siga os passos seguintes:**

1. No portal do Azure, abra a vista de aplicações. Em seguida, aceda a **aplicações empresariais** na vista de diretório.  Em seguida, selecione **todos os aplicativos**.

    ![Atribuir utilizador][201] 

2. Na lista de aplicações, selecione **Shmoop para instituições de ensino**.

    ![A ligação de Shmoop para instituições de ensino na lista de aplicações](./media/shmoopforschools-tutorial/tutorial_shmoopforschools_app.png)  

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"][202]

4. Selecione o **adicionar** botão. Em seguida, na **adicionar atribuição** caixa de diálogo, selecione **utilizadores e grupos**.

    ![O painel Adicionar atribuição][203]

5. Na **utilizadores e grupos** caixa de diálogo, selecione **Eduarda Almeida** na lista utilizadores.

6. Na **utilizadores e grupos** caixa de diálogo, clique nas **selecione** botão. 

7. Na **adicionar atribuição** caixa de diálogo, selecione a **atribuir** botão.
    
### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar seu única início de sessão em configuração do Azure AD utilizando o painel de acesso.

Quando seleciona a **Shmoop para instituições de ensino** mosaico no painel de acesso, deve obter automaticamente a sessão iniciada sua aplicação Shmoop para instituições de ensino.

Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais para saber como integrar aplicações SaaS com o Azure Active Directory](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

<!--Image references-->

[1]: ./media/shmoopforschools-tutorial/tutorial_general_01.png
[2]: ./media/shmoopforschools-tutorial/tutorial_general_02.png
[3]: ./media/shmoopforschools-tutorial/tutorial_general_03.png
[4]: ./media/shmoopforschools-tutorial/tutorial_general_04.png

[100]: ./media/shmoopforschools-tutorial/tutorial_general_100.png

[200]: ./media/shmoopforschools-tutorial/tutorial_general_200.png
[201]: ./media/shmoopforschools-tutorial/tutorial_general_201.png
[202]: ./media/shmoopforschools-tutorial/tutorial_general_202.png
[203]: ./media/shmoopforschools-tutorial/tutorial_general_203.png

