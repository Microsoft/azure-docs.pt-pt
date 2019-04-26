---
title: 'Tutorial: Integração do Active Directory do Azure com a nuvem JDA | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e JDA Cloud.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 06af825f-79ec-4de9-8851-c79d65d1e586
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/31/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 86f2dfaf281130115ff04ff84b413e224f54cfcf
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60268081"
---
# <a name="tutorial-azure-active-directory-integration-with-jda-cloud"></a>Tutorial: Integração do Active Directory do Azure com a nuvem JDA

Neste tutorial, saiba como integrar JDA Cloud no Azure Active Directory (Azure AD).

Integrar JDA Cloud no Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso à nuvem JDA.
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para Cloud JDA (Single Sign-On) com as suas contas do Azure AD.
- Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, consulte o artigo [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com JDA Cloud, terá dos seguintes itens:

- Uma subscrição do Azure
- Uma JDA Cloud logon único habilitado subscrição

> [!NOTE]
> Para testar os passos neste tutorial, recomendamos que não utilize um ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode [obtenha uma avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, vai testar do Azure AD início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar JDA Cloud a partir da Galeria
2. Configuração e teste do Azure AD início de sessão único

## <a name="adding-jda-cloud-from-the-gallery"></a>Adicionar JDA Cloud a partir da Galeria

Para configurar a integração da JDA Cloud com o Azure AD, terá de adicionar JDA Cloud a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar JDA Cloud a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone. 

    ![O botão do Azure Active Directory][1]

2. Navegue para **aplicações empresariais**. Em seguida, aceda a **todos os aplicativos**.

    ![O painel de aplicações empresariais][2]

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo][3]

4. Na caixa de pesquisa, escreva **JDA Cloud**, selecione **JDA Cloud** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

    ![Cloud JDA na lista de resultados](./media/jdacloud-tutorial/tutorial_jdacloud_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com nuvem de JDA com base num utilizador de teste chamado "Eduarda Almeida".

Para o início de sessão único funcione, o Azure AD precisa saber qual é o utilizador equivalente na JDA Cloud a um utilizador no Azure AD. Em outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado na JDA Cloud deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com JDA Cloud, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
3. **[Criar um utilizador de teste da JDA Cloud](#create-a-jda-cloud-test-user)**  - para ter um equivalente da Eduarda Almeida na Cloud de JDA que está ligado à representação de utilizador do Azure AD.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, pode ativar o Azure AD início de sessão único no portal do Azure e configurar início de sessão único na sua aplicação na Cloud de JDA.

**Para configurar o Azure AD início de sessão único com JDA Cloud, execute os seguintes passos:**

1. No portal do Azure, sobre o **JDA Cloud** página de integração de aplicativo, clique em **início de sessão único**.

    ![Configurar a ligação de início de sessão única][4]

2. Sobre o **início de sessão único** caixa de diálogo, selecione **modo** como **baseado em SAML logon** para ativar o início de sessão único.

    ![Caixa de diálogo de início de sessão único](./media/jdacloud-tutorial/tutorial_jdacloud_samlbase.png)

3. Sobre o **JDA Cloud domínio e URLs** secção, execute os seguintes passos, se desejar configurar a aplicação no **IDP** iniciada pelo modo:

    ![JDA Cloud domínio e URLs únicas início de sessão em informações](./media/jdacloud-tutorial/tutorial_jdacloud_url1.png)

    a. Na **identificador** caixa de texto, escreva um URL com o seguinte padrão: `https://<SUBDOMAIN>.jdadelivers.com`

    b. Na **URL de resposta** caixa de texto, escreva um URL com o seguinte padrão: `https://<SUBDOMAIN>.jdadelivers.com/sp/ACS.saml2`

4. Verifique **Mostrar definições de URL avançadas** e executar o passo seguinte, se desejar configurar a aplicação na **SP** iniciada pelo modo:

    ![JDA Cloud domínio e URLs únicas início de sessão em informações](./media/jdacloud-tutorial/tutorial_jdacloud_url2.png)

    Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão: `https://ssonp-dl2.jdadelivers.com/sp/startSSO.ping?PartnerIdpId=<SAML Entity ID>`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o identificador de real, a URL de resposta e o URL de início de sessão. Irá obter o **ID de entidade de SAML** partir do **seção de referência rápida** no **configuração de Cloud JDA** secção. Contacte [equipa de suporte de cliente de Cloud JDA](https://support.jda.com/) obter esses valores.

5. Sobre o **certificado de assinatura SAML** secção, clique em **XML de metadados** e, em seguida, guarde o ficheiro de metadados no seu computador.

    ![O link de download de certificado](./media/jdacloud-tutorial/tutorial_jdacloud_certificate.png) 

6. Clique em **guardar** botão.

    ![Configurar o botão único início de sessão em Guardar](./media/jdacloud-tutorial/tutorial_general_400.png)

7. Na **configuração de Cloud JDA** secção, clique em **configurar a Cloud de JDA** para abrir **configurar início de sessão** janela. Cópia a **ID de entidade de SAML** partir a **seção de referência rápida**.

    ![Configurar o início de sessão único](./media/jdacloud-tutorial/tutorial_jdacloud_configure.png)

8. Para configurar o início de sessão único num **JDA Cloud** lado, terá de enviar o transferido **XML de metadados** para [equipa de suporte de JDA Cloud](https://support.jda.com/). Se definir esta definição para que a ligação de SAML SSO definidas corretamente em ambos os lados.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

   ![Criar um utilizador de teste do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No portal do Azure, no painel esquerdo, clique nas **do Azure Active Directory** botão.

    ![O botão do Azure Active Directory](./media/jdacloud-tutorial/create_aaduser_01.png)

2. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos**e, em seguida, clique em **todos os utilizadores**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](./media/jdacloud-tutorial/create_aaduser_02.png)

3. Para abrir o **usuário** caixa de diálogo, clique em **Add** na parte superior a **todos os utilizadores** caixa de diálogo.

    ![Botão Adicionar](./media/jdacloud-tutorial/create_aaduser_03.png)

4. Na **utilizador** diálogo caixa, execute os seguintes passos:

    ![A caixa de diálogo de utilizador](./media/jdacloud-tutorial/create_aaduser_04.png)

    a. Na **Name** , escreva **BrittaSimon**.

    b. Na **nome de utilizador** , escreva o endereço de e-mail do utilizador Eduarda Almeida.

    c. Selecione o **mostrar palavra-passe** caixa de verificação e, em seguida, anote o valor que é apresentado na **palavra-passe** caixa.

    d. Clique em **Criar**.

### <a name="create-a-jda-cloud-test-user"></a>Criar um utilizador de teste JDA Cloud

Nesta secção, vai criar um usuário chamado Eduarda Almeida na JDA Cloud. Trabalhar com [equipa de suporte de JDA Cloud](https://support.jda.com/) para adicionar os utilizadores na plataforma da JDA Cloud. Os utilizadores tem de ser criados e ativados antes de utilizar o início de sessão único.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso à nuvem JDA.

![Atribuir a função de utilizador][200] 

**Para atribuir a Eduarda Almeida a JDA Cloud, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicativos e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todos os aplicativos**.

    ![Atribuir utilizador][201] 

2. Na lista de aplicações, selecione **JDA Cloud**.

    ![A ligação de JDA Cloud na lista de aplicações](./media/jdacloud-tutorial/tutorial_jdacloud_app.png)  

3. No menu à esquerda, clique em **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"][202]

4. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** nos **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição][203]

5. No **utilizadores e grupos** caixa de diálogo, selecione **Eduarda Almeida** na lista utilizadores.

6. Clique em **selecionar** botão **utilizadores e grupos** caixa de diálogo.

7. Clique em **atribua** botão **adicionar atribuição** caixa de diálogo.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico de JDA Cloud no painel de acesso, deve obter automaticamente com sessão iniciada para a sua aplicação na Cloud de JDA.
Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

<!--Image references-->

[1]: ./media/jdacloud-tutorial/tutorial_general_01.png
[2]: ./media/jdacloud-tutorial/tutorial_general_02.png
[3]: ./media/jdacloud-tutorial/tutorial_general_03.png
[4]: ./media/jdacloud-tutorial/tutorial_general_04.png

[100]: ./media/jdacloud-tutorial/tutorial_general_100.png

[200]: ./media/jdacloud-tutorial/tutorial_general_200.png
[201]: ./media/jdacloud-tutorial/tutorial_general_201.png
[202]: ./media/jdacloud-tutorial/tutorial_general_202.png
[203]: ./media/jdacloud-tutorial/tutorial_general_203.png
