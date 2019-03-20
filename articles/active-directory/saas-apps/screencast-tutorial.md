---
title: 'Tutorial: Integração do Active Directory do Azure com Matic Screencast-S | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e Matic Screencast-S.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 525ad47d-5488-40e2-aeaf-ae6183745682
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/21/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 941a02ef9ab3d623d34c8dc6996040f6d9e44859
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58096848"
---
# <a name="tutorial-azure-active-directory-integration-with-screencast-o-matic"></a>Tutorial: Integração do Active Directory do Azure com Matic Screencast-S

Neste tutorial, saiba como integrar Screencast-O Matic com o Azure Active Directory (Azure AD).

Integrar Screencast-O Matic no Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao Matic Screencast-S.
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para Screencast-S-Matic (Single Sign-On) com as suas contas do Azure AD.
- Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Matic Screencast-S, precisa do seguinte:

- Uma subscrição do Azure
- Um Screencast-O Matic início de sessão único de subscrição ativada

> [!NOTE]
> Para testar os passos neste tutorial, recomendamos que não utilize um ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode [obtenha uma avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, vai testar do Azure AD início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar Screencast-O Matic a partir da Galeria
2. Configuração e teste do Azure AD início de sessão único

## <a name="adding-screencast-o-matic-from-the-gallery"></a>Adicionar Screencast-O Matic a partir da Galeria
Para configurar a integração do Screencast-O Matic para o Azure AD, terá de adicionar Screencast-O Matic partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar Screencast-O Matic a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone. 

    ![O botão do Azure Active Directory][1]

2. Navegue para **aplicações empresariais**. Em seguida, aceda a **todos os aplicativos**.

    ![O painel de aplicações empresariais][2]
    
3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo][3]

4. Na caixa de pesquisa, escreva **Matic Screencast-S**, selecione **Matic Screencast-S** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

    ![Screencast-S-Matic na lista de resultados](./media/screencast-tutorial/tutorial_screencast_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com Screencast-S-Matic com base num utilizador de teste chamado "Eduarda Almeida".

Para o início de sessão único funcione, o Azure AD precisa saber qual é o utilizador de contraparte no Screencast-O Matic a um utilizador no Azure AD. Em outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Screencast-S-Matic deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com Matic Screencast-S, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
3. **[Criar um utilizador de teste Matic Screencast-S](#create-a-screencast-o-matic-test-user)**  - para ter um equivalente da Eduarda Almeida no Screencast-S-Matic que está ligado à representação de utilizador do Azure AD.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, pode ativar o Azure AD início de sessão único no portal do Azure e configurar início de sessão único em seu aplicativo Matic Screencast-S.

**Para configurar o Azure AD início de sessão único com Matic Screencast-S, execute os seguintes passos:**

1. No portal do Azure, sobre o **Matic Screencast-S** página de integração de aplicação, clique em **início de sessão único**.

    ![Configurar a ligação de início de sessão única][4]

2. Sobre o **início de sessão único** caixa de diálogo, selecione **modo** como **baseado em SAML logon** para ativar o início de sessão único.
 
    ![Caixa de diálogo de início de sessão único](./media/screencast-tutorial/tutorial_screencast_samlbase.png)

3. Sobre o **Screencast-O Matic domínio e URLs** secção, execute os seguintes passos:

    ![Screencast-O Matic domínio e URLs únicas início de sessão em informações](./media/screencast-tutorial/tutorial_screencast_url.png)

    Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão: `https://screencast-o-matic.com/<InstanceName>`

    > [!NOTE] 
    > O valor de URL de início de sessão não é real. Atualize o valor com o URL de início de sessão real. Contacte [equipa de suporte de cliente Matic Screencast-S](mailto:support@screencast-o-matic.com) para obter o valor. 
 
4. Sobre o **certificado de assinatura SAML** secção, clique em **XML de metadados** e, em seguida, guarde o ficheiro de metadados no seu computador.

    ![O link de download de certificado](./media/screencast-tutorial/tutorial_screencast_certificate.png) 

5. Clique em **guardar** botão.

    ![Configurar o botão único início de sessão em Guardar](./media/screencast-tutorial/tutorial_general_400.png)

6. Numa janela do browser web diferente, inicie sessão no Screencast-S-Matic como administrador.

7. Clique em **subscrição**.

    ![A subscrição](./media/screencast-tutorial/tutorial_screencast_sub.png)

8. Sob **página de acesso** secção, clique em **configuração**.

    ![O acesso](./media/screencast-tutorial/tutorial_screencast_setup.png)

9. Sobre o **página de configuração de acesso**, execute os seguintes passos:

   * Sob **URL de acesso** secção, escreva o seu instancename na caixa de texto especificada.

     ![O acesso](./media/screencast-tutorial/tutorial_screencast_access.png)

   * Selecione **utilizador de domínio exigir** sob **restrição de utilizador de SAML (opcional)** secção.

   * Sob **carregar ficheiro de XML de metadados de IDP**, clique em **Escolher ficheiro** para carregar os metadados que transferiu a partir do portal do Azure.

   * Clique em **OK**. 

     ![O acesso](./media/screencast-tutorial/tutorial_screencast_save.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

   ![Criar um utilizador de teste do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No portal do Azure, no painel esquerdo, clique nas **do Azure Active Directory** botão.

    ![O botão do Azure Active Directory](./media/screencast-tutorial/create_aaduser_01.png)

2. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos**e, em seguida, clique em **todos os utilizadores**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](./media/screencast-tutorial/create_aaduser_02.png)

3. Para abrir o **usuário** caixa de diálogo, clique em **Add** na parte superior a **todos os utilizadores** caixa de diálogo.

    ![Botão Adicionar](./media/screencast-tutorial/create_aaduser_03.png)

4. Na **utilizador** diálogo caixa, execute os seguintes passos:

    ![A caixa de diálogo de utilizador](./media/screencast-tutorial/create_aaduser_04.png)

    a. Na **Name** , escreva **BrittaSimon**.

    b. Na **nome de utilizador** , escreva o endereço de e-mail do utilizador Eduarda Almeida.

    c. Selecione o **mostrar palavra-passe** caixa de verificação e, em seguida, anote o valor que é apresentado na **palavra-passe** caixa.

    d. Clique em **Criar**.
 
### <a name="create-a-screencast-o-matic-test-user"></a>Criar um utilizador de teste Matic Screencast-S

O objetivo desta secção é criar um usuário chamado Eduarda Almeida no Matic Screencast-S. Matic screencast-S suporta o aprovisionamento just-in-time, que está por predefinição, ativada. Não existe nenhum item de ação para nesta secção. Um novo utilizador é criado durante uma tentativa de aceder Screencast-O Matic, se não existir ainda.

>[!Note]
>Se precisar de criar manualmente um utilizador, contacte [equipa de suporte de cliente Matic Screencast-S](mailto:support@screencast-o-matic.com).

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar do Azure-início de sessão único, concedendo acesso para Matic Screencast-S.

![Atribuir a função de utilizador][200] 

**Para atribuir a Eduarda Almeida a Matic Screencast-S, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicativos e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todos os aplicativos**.

    ![Atribuir utilizador][201] 

2. Na lista de aplicações, selecione **Matic Screencast-S**.

    ![A ligação Screencast-O Matic na lista de aplicações](./media/screencast-tutorial/tutorial_screencast_app.png)  

3. No menu à esquerda, clique em **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"][202]

4. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** nos **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição][203]

5. No **utilizadores e grupos** caixa de diálogo, selecione **Eduarda Almeida** na lista utilizadores.

6. Clique em **selecionar** botão **utilizadores e grupos** caixa de diálogo.

7. Clique em **atribua** botão **adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico Screencast-O Matic no painel de acesso, deve obter automaticamente com sessão iniciada para a sua aplicação Matic Screencast-S.
Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)



<!--Image references-->

[1]: ./media/screencast-tutorial/tutorial_general_01.png
[2]: ./media/screencast-tutorial/tutorial_general_02.png
[3]: ./media/screencast-tutorial/tutorial_general_03.png
[4]: ./media/screencast-tutorial/tutorial_general_04.png

[100]: ./media/screencast-tutorial/tutorial_general_100.png

[200]: ./media/screencast-tutorial/tutorial_general_200.png
[201]: ./media/screencast-tutorial/tutorial_general_201.png
[202]: ./media/screencast-tutorial/tutorial_general_202.png
[203]: ./media/screencast-tutorial/tutorial_general_203.png

