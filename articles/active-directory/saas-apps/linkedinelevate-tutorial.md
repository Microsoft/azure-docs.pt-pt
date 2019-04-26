---
title: 'Tutorial: Integração do Active Directory do Azure com o LinkedIn elevar | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e o LinkedIn elevar.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 2ad9941b-c574-42c3-bd0f-5d6ec68537ef
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6ca8e537f261b59fb4e069d47d24e21abbdeca46
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60260031"
---
# <a name="tutorial-azure-active-directory-integration-with-linkedin-elevate"></a>Tutorial: Integração do Active Directory do Azure com o LinkedIn elevar

Neste tutorial, saiba como integrar o LinkedIn elevar com o Azure Active Directory (Azure AD).

Integrar o LinkedIn elevar no Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao elevar o LinkedIn
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para elevar o LinkedIn (Single Sign-On) com as suas contas do Azure AD
- Pode gerir as suas contas num local central – o portal de gestão do Azure

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com elevar o LinkedIn, terá dos seguintes itens:

- Uma subscrição do Azure
- Um LinkedIn elevar início de sessão único na subscrição ativado

> [!NOTE]
> Para testar os passos neste tutorial, recomendamos que não utilize um ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não deve utilizar o seu ambiente de produção, a menos que isso é necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, vai testar do Azure AD início de sessão único num ambiente de teste.
O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o LinkedIn elevar da Galeria
1. Configuração e teste do Azure AD início de sessão único

## <a name="adding-linkedin-elevate-from-the-gallery"></a>Adicionando o LinkedIn elevar da Galeria
Para configurar a integração do LinkedIn elevar para o Azure AD, terá de adicionar LinkedIn elevar partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar a LinkedIn elevar a partir da galeria, execute os seguintes passos:**

1. Na  **[do Portal de gestão do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![Active Directory][1]

1. Navegue para **aplicações empresariais**. Em seguida, aceda a **todos os aplicativos**.

    ![Aplicações][2]

1. Clique em **adicionar** botão na parte superior da caixa de diálogo.

    ![Aplicações][3]

1. Na caixa de pesquisa, escreva **LinkedIn elevar**. No painel de resultados, clique em **LinkedIn elevar** para adicionar a aplicação.

    ![Criar um utilizador de teste do Azure AD](./media/linkedinelevate-tutorial/tutorial-linkedinElevate_000.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuração e teste do Azure AD início de sessão único
Nesta secção, configure e teste do Azure AD início de sessão único com o LinkedIn, efetuar a elevação com base num utilizador de teste chamado "Eduarda Almeida".

Para o início de sessão único funcione, o Azure AD precisa saber qual é o utilizador de contraparte no LinkedIn elevar a um utilizador no Azure AD. Em outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no LinkedIn elevar deve ser estabelecido.

Esta relação de ligação é estabelecida atribuindo o valor do **nome de utilizador** no Azure AD como o valor da **Username** no LinkedIn elevar.

Para configurar e testar o Azure AD início de sessão único com o LinkedIn elevar, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configuring-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
1. **[Criar um utilizador de teste do Azure AD](#creating-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
1. **[Criar um utilizador de teste LinkedIn elevar](#creating-a-linkedin-elevate-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
1. **[Atribuir o utilizador de teste do Azure AD](#assigning-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
1. **[Teste de início de sessão único](#testing-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do Azure AD início de sessão único

Nesta secção, pode ativar o Azure AD início de sessão único no portal de gestão do Azure e configurar início de sessão único em seu aplicativo de elevar o LinkedIn.

**Para configurar o Azure AD início de sessão único com o LinkedIn elevar, execute os seguintes passos:**

1. No portal de gestão do Azure, sobre o **LinkedIn elevar** página de integração de aplicação, clique em **início de sessão único**.

    ![Configurar o início de sessão único][4]

1. Sobre o **início de sessão único** caixa de diálogo, como **modo** selecione **baseado em SAML início de sessão** para ativar o início de sessão único.

    ![Configurar o início de sessão único](./media/linkedinelevate-tutorial/tutorial-linkedin_01.png)

1. Numa janela do browser web diferente, início de sessão no seu inquilino de elevar o LinkedIn como administrador.

1. Na **Centro de contas**, clique em **definições globais** sob **definições**. Além disso, selecione **elevação – elevar o teste de AAD** na lista pendente.

    ![Configurar o início de sessão único](./media/linkedinelevate-tutorial/tutorial_linkedin_admin_01.png)

1. Clique em **ou clique aqui para carregar e copiar campos individuais do formulário** e copie **Id de entidade** e **Url de acesso de consumidor de asserção (ACS)**

    ![Configurar o início de sessão único](./media/linkedinelevate-tutorial/tutorial_linkedin_admin_03.png)

1. No Portal do Azure, em **domínio elevar do LinkedIn e URLs**, execute os seguintes passos para configurar o SSO no **IdP iniciada** modo

    ![Configurar o início de sessão único](./media/linkedinelevate-tutorial/tutorial_linkedin_signon_01.png)

    a. Na **identificador** caixa de texto, introduza o **ID de entidade** copiados a partir do Portal do LinkedIn 

    b. Na **URL de resposta** caixa de texto, introduza o **Url de acesso de consumidor de asserção (ACS)** copiados a partir do Portal do LinkedIn

1. Se quiser configurar o SSO no **SP iniciada**, em seguida, clique na opção de definição de mostrar URL avançado na secção de configuração e configurar o início de sessão no URL com o seguinte padrão:

    `https://www.linkedin.com/checkpoint/enterprise/login/<AccountId>?application=elevate&applicationInstanceId=<InstanceId>` 

    ![Configurar o início de sessão único](./media/linkedinelevate-tutorial/tutorial_linkedin_signon_02.png) 

1. A aplicação de elevar o LinkedIn espera que as asserções SAML num formato específico, o que requer a adição de mapeamentos de atributos personalizado à sua configuração de atributos de token SAML. Captura de ecrã seguinte mostra um exemplo disso. O valor predefinido **identificador de utilizador** é **user.userprincipalname** mas LinkedIn elevar espera que isso seja mapeado com o endereço de e-mail do utilizador. Para que pode usar **user.mail** atributo da lista ou utilize o valor do atributo adequado com base na configuração da sua organização.

    ![Configurar o início de sessão único](./media/linkedinelevate-tutorial/updateusermail.png)

1. Na **atributos de utilizador** secção, clique em **ver e editar todos os outros atributos de utilizador** e definir os atributos. Tem de adicionar outra declaração com o nome **departamento** e o valor tem de ser mapeada para **user.department**.

    | Nome do Atributo | Valor do Atributo |
    | --- | --- |
    | Departamento| user.department |

      ![Criar um utilizador de teste do Azure AD](./media/linkedinelevate-tutorial/userattribute.png)

      a. Clique em adicionar atributo para abrir a página de detalhes do atributo adicionar atributo department, conforme mostrado abaixo-

      ![Criar um utilizador de teste do Azure AD](./media/linkedinelevate-tutorial/adduserattribute.png)

      b. Clique em **Ok** para guardar o atributo.

      c. Altere o nome do atributo **emailaddress** ao **e-mail**.

1. Sobre o **certificado de assinatura SAML** secção, clique em **XML de metadados** e, em seguida, guarde o ficheiro XML em seu computador.

    ![Configurar o início de sessão único](./media/linkedinelevate-tutorial/tutorial-linkedinElevate_certificate.png) 

1. Clique em **Guardar**.

    ![Configurar o início de sessão único](./media/linkedinelevate-tutorial/tutorial_general_400.png)

1. Aceda a **definições de administração do LinkedIn** secção. Carregue o ficheiro XML que acabou de transferir do portal do Azure ao clicar na opção de arquivo XML de carregar.

    ![Configurar o início de sessão único](./media/linkedinelevate-tutorial/tutorial_linkedin_metadata_03.png)

1. Clique em **no** para ativar o SSO. Estado SSO será alterado de **não ligado** para **ligado**

    ![Configurar o início de sessão único](./media/linkedinelevate-tutorial/tutorial_linkedin_admin_05.png)

### <a name="creating-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD
O objetivo desta secção consiste em criar um utilizador de teste no portal do Azure Management chamado Eduarda Almeida.

![Criar utilizador do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. Na **portal de gestão do Azure**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![Criar um utilizador de teste do Azure AD](./media/linkedinelevate-tutorial/create_aaduser_01.png) 

1. Aceda a **utilizadores e grupos** e clique em **todos os utilizadores** para apresentar a lista de utilizadores.

    ![Criar um utilizador de teste do Azure AD](./media/linkedinelevate-tutorial/create_aaduser_02.png) 

1. Na parte superior da caixa de diálogo, clique em **Add** para abrir o **utilizador** caixa de diálogo.

    ![Criar um utilizador de teste do Azure AD](./media/linkedinelevate-tutorial/create_aaduser_03.png) 

1. Sobre o **utilizador** caixa de diálogo página, execute os seguintes passos:

    ![Criar um utilizador de teste do Azure AD](./media/linkedinelevate-tutorial/create_aaduser_04.png) 

    a. Na **Name** caixa de texto, tipo **BrittaSimon**.

    b. Na **nome de utilizador** caixa de texto, tipo a **endereço de e-mail** de BrittaSimon.

    c. Selecione **mostrar palavra-passe** e indique o valor da **palavra-passe**.

    d. Clique em **Criar**.

### <a name="creating-a-linkedin-elevate-test-user"></a>Criar um utilizador de teste elevar o LinkedIn

A aplicação de elevar LinkedIn suporta apenas em tempo de aprovisionamento de utilizador e de utilizadores de autenticação serão criados no aplicativo automaticamente. Sobre o administrador de definições de página no flip portal LinkedIn elevar o comutador **automaticamente atribuir licenças** para Active Directory apenas em tempo de aprovisionamento e isso irão também atribuir uma licença ao utilizador. Elevar o LinkedIn também suporta o aprovisionamento automático de utilizadores, pode encontrar mais detalhes [aqui](linkedinelevate-provisioning-tutorial.md) sobre como configurar o aprovisionamento automático de utilizadores.

   ![Criar um utilizador de teste do Azure AD](./media/linkedinelevate-tutorial/LinkedinUserprovswitch.png)

### <a name="assigning-the-azure-ad-test-user"></a>Atribuir o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único, concedendo acesso para elevar o LinkedIn.

![Atribuir utilizador][200] 

**Para atribuir a Eduarda Almeida para elevar o LinkedIn, execute os seguintes passos:**

1. No portal de gestão do Azure, abra a vista de aplicativos e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todos os aplicativos**.

    ![Atribuir utilizador][201]

1. Na lista de aplicações, selecione **LinkedIn elevar**.

    ![Configurar o início de sessão único](./media/linkedinelevate-tutorial/tutorial-linkedinElevate_0001.png) 

1. No menu à esquerda, clique em **utilizadores e grupos**.

    ![Atribuir utilizador][202] 

1. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** nos **adicionar atribuição** caixa de diálogo.

    ![Atribuir utilizador][203]

1. No **utilizadores e grupos** caixa de diálogo, selecione **Eduarda Almeida** na lista utilizadores.

1. Clique em **selecionar** botão **utilizadores e grupos** caixa de diálogo.

1. Clique em **atribua** botão **adicionar atribuição** caixa de diálogo.

### <a name="testing-single-sign-on"></a>Teste de início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico elevar o LinkedIn, no painel de acesso, deve obter a página de início de sessão do Azure e no após o logon com êxito, deverá obter na sua aplicação de elevar o LinkedIn.

## <a name="additional-resources"></a>Recursos adicionais

* [Tutorial: Configurar o LinkedIn elevar para aprovisionamento automático de utilizadores no Azure Active Directory](linkedinelevate-provisioning-tutorial.md)
* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)
* [Configurar o aprovisionamento do utilizador](linkedinelevate-provisioning-tutorial.md)

<!--Image references-->

[1]: ./media/linkedinElevate-tutorial/tutorial_general_01.png
[2]: ./media/linkedinElevate-tutorial/tutorial_general_02.png
[3]: ./media/linkedinElevate-tutorial/tutorial_general_03.png
[4]: ./media/linkedinElevate-tutorial/tutorial_general_04.png

[100]: ./media/linkedinElevate-tutorial/tutorial_general_100.png

[200]: ./media/linkedinElevate-tutorial/tutorial_general_200.png
[201]: ./media/linkedinElevate-tutorial/tutorial_general_201.png
[202]: ./media/linkedinElevate-tutorial/tutorial_general_202.png
[203]: ./media/linkedinElevate-tutorial/tutorial_general_203.png
