---
title: 'Tutorial: Integração do Active Directory do Azure com o T & E Express | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e T & E Express.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: B42374E5-2559-4309-8EF2-820BEE7EBB0C
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/03/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 334f5fa2309c44bebe8583f497fdaa3c7578e5ae
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57433421"
---
# <a name="tutorial-azure-active-directory-integration-with-te-express"></a>Tutorial: Integração do Active Directory do Azure com o T & E Express

Neste tutorial, saiba como integrar o T & E Express com o Azure Active Directory (Azure AD).

T & E Express a integração com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao T & E Express
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para o T & E Express (Single Sign-On) com as suas contas do Azure AD
- Pode gerir as suas contas num local central – o portal de gestão do Azure

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o T & E Express, precisa do seguinte:

- Uma subscrição do Azure
- Um T & E Express início de sessão único na subscrição ativado

> [!NOTE]
> Para testar os passos neste tutorial, recomendamos que não utilize um ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não deve utilizar o seu ambiente de produção, a menos que isso é necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, vai testar do Azure AD início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar T & E Express a partir da Galeria
1. Configuração e teste do Azure AD início de sessão único

## <a name="adding-te-express-from-the-gallery"></a>Adicionar T & E Express a partir da Galeria
Para configurar a integração de d & E Express com o Azure AD, terá de adicionar T & E Express a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar T & E Express a partir da galeria, execute os seguintes passos:**

1. Na  **[do Portal de gestão do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone. 

    ![Active Directory][1]

1. Navegue para **aplicações empresariais**. Em seguida, aceda a **todos os aplicativos**.

    ![Aplicações][2]
    
1. Clique em **adicionar** botão na parte superior da caixa de diálogo.

    ![Aplicações][3]

1. Na caixa de pesquisa, escreva **T & E Express**.

    ![Criar um utilizador de teste do Azure AD](./media/tyeexpress-tutorial/tutorial_tyeexpress_search.png)

1. No painel de resultados, selecione **T & E Express**e, em seguida, clique em **Add** botão para adicionar a aplicação.

    ![Criar um utilizador de teste do Azure AD](./media/tyeexpress-tutorial/tutorial_tyeexpress_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuração e teste do Azure AD início de sessão único
Nesta secção, configure e teste do Azure AD início de sessão único com o T & E Express com base num utilizador de teste chamado "Eduarda Almeida".

Para o início de sessão único funcione, o Azure AD precisa saber qual é o utilizador de contraparte no T & E Express a um utilizador no Azure AD. Em outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no T & E Express deve ser estabelecido.

Esta relação de ligação é estabelecida atribuindo o valor do **nome de utilizador** no Azure AD como o valor da **nome de utilizador** no T & E Express.

Para configurar e testar o Azure AD início de sessão único com o T & E Express, precisa concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configuring-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
1. **[Criar um utilizador de teste do Azure AD](#creating-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
1. **[Criar um utilizador de teste T & E Express](#creating-a-te-express-test-user)**  - para ter um equivalente da Eduarda Almeida na T & E Express que está ligado à representação de ela do Azure AD.
1. **[Atribuir o utilizador de teste do Azure AD](#assigning-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
1. **[Teste de início de sessão único](#testing-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do Azure AD início de sessão único

Nesta secção, pode ativar o Azure AD início de sessão único no portal de gestão do Azure e configurar início de sessão único em seu aplicativo T & E Express.

**Para configurar o Azure AD início de sessão único com o T & E Express, execute os seguintes passos:**

1. No portal de gestão do Azure, sobre o **T & E Express** página de integração de aplicação, clique em **início de sessão único**.

    ![Configurar o início de sessão único][4]

1. Sobre o **início de sessão único** caixa de diálogo, como **modo** selecione **baseado em SAML início de sessão** para ativar o início de sessão único.
 
    ![Configurar o início de sessão único](./media/tyeexpress-tutorial/tutorial_tyeexpress_samlbase.png)

1. Sobre o **T & URLs e domínio da Express E** secção, execute os seguintes passos:

    ![Configurar o início de sessão único](./media/tyeexpress-tutorial/tutorial_tyeexpress_url.png)

    a. Na **identificador** caixa de texto, digite o valor como: `https://<domain>.tyeexpress.com`

    b. Na **URL de resposta** caixa de texto, escreva um URL com o seguinte padrão: `https://<domain>.tyeexpress.com/authorize/samlConsume.aspx`

    > [!NOTE] 
    > Tenha em atenção que estes não são os valores reais. Terá de atualizar estes valores com o identificador real e o URL de resposta. Aqui iremos sugerir-lhe utilizar o valor único de cadeia de caracteres no identificador de. Contacte [equipa de suporte de d & E Express](http://www.tyeexpress.com/contacto.aspx) obter esses valores.

1. Sobre o **certificado de assinatura SAML** secção, clique em **XML de metadados** e, em seguida, guarde o ficheiro XML em seu computador.

    ![Configurar o início de sessão único](./media/tyeexpress-tutorial/tutorial_tyeexpress_certificate.png) 

1. Clique em **guardar** botão.

    ![Configurar o início de sessão único](./media/tyeexpress-tutorial/tutorial_general_400.png)

1. Para configurar o início de sessão único num **T & E Express** lado, o início de sessão para as horas e despesas express aplicação sem início de sessão único SAML sobre como utilizar as credenciais de administrador.

1. Sob o **administrador** separador, clique em **domínio SAML** para abrir a página de definições de SAML.

    ![Configurar o início de sessão único](./media/tyeexpress-tutorial/tye-SAML.png)

1. Selecione o **Activar(Activate)** opção partir **não** para **SI(Yes)**. Na **metadados de fornecedor de identidade** caixa de texto, cole os metadados XML que transferiu a partir do portal do Azure.

    ![Configurar o início de sessão único](./media/tyeexpress-tutorial/tyeAdmin.png)

1. Clique nas **Guardar(Save)** botão para guardar as definições.  


### <a name="creating-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD
O objetivo desta secção consiste em criar um utilizador de teste no portal do Azure Management chamado Eduarda Almeida.

![Criar utilizador do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. Na **portal de gestão do Azure**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![Criar um utilizador de teste do Azure AD](./media/tyeexpress-tutorial/create_aaduser_01.png) 

1. Aceda a **utilizadores e grupos** e clique em **todos os utilizadores** para apresentar a lista de utilizadores.
    
    ![Criar um utilizador de teste do Azure AD](./media/tyeexpress-tutorial/create_aaduser_02.png) 

1. Na parte superior da caixa de diálogo, clique em **Add** para abrir o **utilizador** caixa de diálogo.
 
    ![Criar um utilizador de teste do Azure AD](./media/tyeexpress-tutorial/create_aaduser_03.png) 

1. Sobre o **utilizador** caixa de diálogo página, execute os seguintes passos:
 
    ![Criar um utilizador de teste do Azure AD](./media/tyeexpress-tutorial/create_aaduser_04.png) 

    a. Na **Name** caixa de texto, tipo **BrittaSimon**.

    b. Na **nome de utilizador** caixa de texto, tipo a **endereço de e-mail** de BrittaSimon.

    c. Selecione **mostrar palavra-passe** e indique o valor da **palavra-passe**.

    d. Clique em **Criar**.
 
### <a name="creating-a-te-express-test-user"></a>Criar um utilizador de teste T & E Express

Para habilitar logon T & E Express de utilizadores do Azure AD, tem de ser aprovisionados no T & E Express.  
Em caso de T & E Express, o aprovisionamento é uma tarefa manual.

**Para aprovisionar contas de utilizador, execute os seguintes passos:**

1. Inicie sessão no site da sua empresa T & E Express como administrador.

1. Na etiqueta do administrador, clique em utilizadores para abrir a página mestra de utilizadores.

    ![Adicionar o funcionário](./media/tyeexpress-tutorial/tye-adminusers.png)

1. Na home page, clique em **+** para adicionar os utilizadores.

    ![Adicionar o funcionário](./media/tyeexpress-tutorial/tye-usershome.png)

1. Introduza todos os detalhes obrigatórios como pedido no formulário e clique no botão Guardar para guardar os detalhes.

    ![Adicionar o funcionário](./media/tyeexpress-tutorial/tye-usersadd.png)

    ![Adicionar o funcionário](./media/tyeexpress-tutorial/tye-userssave.png)


### <a name="assigning-the-azure-ad-test-user"></a>Atribuir o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso a T & E Express.

![Atribuir utilizador][200] 

**Para atribuir a Eduarda Almeida a T & E Express, execute os seguintes passos:**

1. No portal de gestão do Azure, abra a vista de aplicativos e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todos os aplicativos**.

    ![Atribuir utilizador][201] 

1. Na lista de aplicações, selecione **T & E Express**.

    ![Configurar o início de sessão único](./media/tyeexpress-tutorial/tutorial_tyeexpress_app.png) 

1. No menu à esquerda, clique em **utilizadores e grupos**.

    ![Atribuir utilizador][202] 

1. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** nos **adicionar atribuição** caixa de diálogo.

    ![Atribuir utilizador][203]

1. No **utilizadores e grupos** caixa de diálogo, selecione **Eduarda Almeida** na lista utilizadores.

1. Clique em **selecionar** botão **utilizadores e grupos** caixa de diálogo.

1. Clique em **atribua** botão **adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste de início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico T & E Express no painel de acesso, deve obter automaticamente com sessão iniciada para a sua aplicação T & E Express.

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)



<!--Image references-->

[1]: ./media/tyeexpress-tutorial/tutorial_general_01.png
[2]: ./media/tyeexpress-tutorial/tutorial_general_02.png
[3]: ./media/tyeexpress-tutorial/tutorial_general_03.png
[4]: ./media/tyeexpress-tutorial/tutorial_general_04.png

[100]: ./media/tyeexpress-tutorial/tutorial_general_100.png

[200]: ./media/tyeexpress-tutorial/tutorial_general_200.png
[201]: ./media/tyeexpress-tutorial/tutorial_general_201.png
[202]: ./media/tyeexpress-tutorial/tutorial_general_202.png
[203]: ./media/tyeexpress-tutorial/tutorial_general_203.png

