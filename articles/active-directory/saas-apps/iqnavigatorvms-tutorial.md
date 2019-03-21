---
title: 'Tutorial: Integração do Active Directory do Azure com IQNavigator VMS | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e IQNavigator VMS.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: a8a09b25-dfa5-4c31-aea2-53bf1853b365
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f5a0700a63d21d089573f757716e08fb03665b28
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58164999"
---
# <a name="tutorial-azure-active-directory-integration-with-iqnavigator-vms"></a>Tutorial: Integração do Active Directory do Azure com IQNavigator VMS

Neste tutorial, saiba como integrar IQNavigator VMS no Azure Active Directory (Azure AD).

Integrar IQNavigator VMS no Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso às IQNavigator VMS
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para VMS de IQNavigator (Single Sign-On) com as suas contas do Azure AD
- Pode gerir as suas contas num local central – portal do Azure

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com IQNavigator VMS, precisa do seguinte:

- Uma subscrição do Azure
- Uma IQNavigator VMS logon único habilitado subscrição

> [!NOTE]
> Para testar os passos neste tutorial, recomendamos que não utilize um ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter uma avaliação de um mês aqui [oferta de avaliação](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, vai testar do Azure AD início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar IQNavigator VMS a partir da Galeria
1. Configuração e teste do Azure AD início de sessão único

## <a name="adding-iqnavigator-vms-from-the-gallery"></a>Adicionar IQNavigator VMS a partir da Galeria
Para configurar a integração de IQNavigator VMS no Azure AD, terá de adicionar IQNavigator VMS a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar IQNavigator VMS a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone. 

    ![Active Directory][1]

1. Navegue para **aplicações empresariais**. Em seguida, aceda a **todos os aplicativos**.

    ![Aplicações][2]
    
1. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![Aplicações][3]

1. Na caixa de pesquisa, escreva **IQNavigator VMS**.

    ![Criar um utilizador de teste do Azure AD](./media/iqnavigatorvms-tutorial/tutorial_iqnavigatorvms_search.png)

1. No painel de resultados, selecione **IQNavigator VMS**e, em seguida, clique em **Add** botão para adicionar a aplicação.

    ![Criar um utilizador de teste do Azure AD](./media/iqnavigatorvms-tutorial/tutorial_iqnavigatorvms_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuração e teste do Azure AD início de sessão único
Nesta secção, configure e teste do Azure AD início de sessão único com IQNavigator VMS com base num utilizador de teste chamado "Eduarda Almeida".

Para o início de sessão único funcione, o Azure AD precisa saber qual é o utilizador de contraparte no IQNavigator VMS para um utilizador no Azure AD. Em outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado em IQNavigator VMS deve ser estabelecido.

Nas VMS de IQNavigator, atribuir o valor do **nome de utilizador** no Azure AD como o valor da **nome de utilizador** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD início de sessão único com IQNavigator VMS, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configuring-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
1. **[Criar um utilizador de teste do Azure AD](#creating-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
1. **[Criar um utilizador de teste de VMS de IQNavigator](#creating-a-iqnavigator-vms-test-user)**  - para ter um equivalente da Eduarda Almeida nas VMS de IQNavigator que está ligado à representação de utilizador do Azure AD.
1. **[Atribuir o utilizador de teste do Azure AD](#assigning-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
1. **[Teste de início de sessão único](#testing-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do Azure AD início de sessão único

Nesta secção, pode ativar o Azure AD início de sessão único no portal do Azure e configurar início de sessão único em seu aplicativo IQNavigator VMS.

**Para configurar o Azure AD início de sessão único com IQNavigator VMS, execute os seguintes passos:**

1. No portal do Azure, sobre o **IQNavigator VMS** página de integração de aplicação, clique em **início de sessão único**.

    ![Configurar o início de sessão único][4]

1. Sobre o **início de sessão único** caixa de diálogo, selecione **modo** como **baseado em SAML logon** para ativar o início de sessão único.

    ![Configurar o início de sessão único](./media/iqnavigatorvms-tutorial/tutorial_iqnavigatorvms_samlbase.png)

1. Sobre o **IQNavigator VMS domínio e URLs** secção, execute os seguintes passos:

    ![Configurar o início de sessão único](./media/iqnavigatorvms-tutorial/tutorial_iqnavigatorvms_url.png)

    a. Na **identificador** caixa de texto, escreva o URL:`iqn.com`

    b. Na **URL de resposta** caixa de texto, escreva um URL com o seguinte padrão: `https://<subdomain>.iqnavigator.com/security/login?client_name=https://sts.window.net/<instance name>`

1. Verifique **Mostrar definições de URL avançadas**, executar o passo seguinte:

    ![Configurar o início de sessão único](./media/iqnavigatorvms-tutorial/tutorial_iqnavigatorvms_url1.png)

    Na **estado de reencaminhamento** caixa de texto, escreva um URL com o seguinte padrão:`https://<subdomain>.iqnavigator.com`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o estado real do URL de resposta e de reencaminhamento. Contacte [equipa de suporte de cliente de VMS IQNavigator](https://www.beeline.com/iqn-product-support/) obter esses valores.

1. Sobre o **certificado de assinatura SAML** secção, clique no botão de cópia para copiar **Url de metadados de Federação de aplicação** e cole-o no bloco de notas.
    
    ![Configurar o início de sessão único](./media/iqnavigatorvms-tutorial/tutorial_metadataurl.png)

1. Aplicação de IQNavigator esperar o valor do identificador de utilizador exclusivo na afirmação de identificador de nome. Cliente pode mapear o valor correto para a afirmação de identificador de nome. Neste caso estamos mapeou o utilizador. UserPrincipalName para o efeito de demonstração. Mas, de acordo com as definições da organização deve mapear o valor correto para o mesmo.

    ![Configurar o início de sessão único](./media/iqnavigatorvms-tutorial/tutorial_iqnavigatorvms_attribute.png)

1. Clique em **guardar** botão.

    ![Configurar o início de sessão único](./media/iqnavigatorvms-tutorial/tutorial_general_400.png)

1. Sobre o **IQNavigator configuração de VMS** secção, clique em **configurar VMS de IQNavigator** para abrir **configurar início de sessão** janela. Cópia a **URL de fim de sessão, o ID de entidade de SAML e o SAML único início de sessão no URL do serviço** partir o **secção de referência rápida.**

    ![Configurar o início de sessão único](./media/iqnavigatorvms-tutorial/tutorial_iqnavigatorvms_configure.png)

1. Para configurar o início de sessão único num **IQNavigator VMS** lado, terá de enviar o **Url de metadados de Federação de aplicação**, **URL de fim de sessão, o ID de entidade de SAML e o SAML único início de sessão no URL do serviço**para [equipa de suporte de VMS de IQNavigator](https://www.beeline.com/iqn-product-support/). Se definir esta definição para que a ligação de SAML SSO definidas corretamente em ambos os lados.

### <a name="creating-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD
O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

![Criar utilizador do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. Na **portal do Azure**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![Criar um utilizador de teste do Azure AD](./media/iqnavigatorvms-tutorial/create_aaduser_01.png) 

1. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos** e clique em **todos os utilizadores**.
    
    ![Criar um utilizador de teste do Azure AD](./media/iqnavigatorvms-tutorial/create_aaduser_02.png)

1. Para abrir o **usuário** caixa de diálogo, clique em **Add** na parte superior da caixa de diálogo.

    ![Criar um utilizador de teste do Azure AD](./media/iqnavigatorvms-tutorial/create_aaduser_03.png)

1. Sobre o **utilizador** caixa de diálogo página, execute os seguintes passos:
 
    ![Criar um utilizador de teste do Azure AD](./media/iqnavigatorvms-tutorial/create_aaduser_04.png) 

    a. Na **Name** caixa de texto, tipo **BrittaSimon**.

    b. Na **nome de utilizador** caixa de texto, tipo a **endereço de e-mail** de BrittaSimon.

    c. Selecione **mostrar palavra-passe** e indique o valor da **palavra-passe**.

    d. Clique em **Criar**.

### <a name="creating-an-iqnavigator-vms-test-user"></a>Criar um utilizador de teste IQNavigator VMS

O objetivo desta secção é criar um usuário chamado Eduarda Almeida no IQNavigator VMS. Trabalhar com [equipa de suporte de VMS de IQNavigator](https://www.beeline.com/iqn-product-support/) para adicionar os utilizadores na conta IQNavigator VMS.

### <a name="assigning-the-azure-ad-test-user"></a>Atribuir o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único, concedendo acesso às IQNavigator VMS.

![Atribuir utilizador][200]

**Para atribuir a Eduarda Almeida a IQNavigator VMS, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicativos e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todos os aplicativos**.

    ![Atribuir utilizador][201]

1. Na lista de aplicações, selecione **IQNavigator VMS**.

    ![Configurar o início de sessão único](./media/iqnavigatorvms-tutorial/tutorial_iqnavigatorvms_app.png)

1. No menu à esquerda, clique em **utilizadores e grupos**.

    ![Atribuir utilizador][202]

1. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** nos **adicionar atribuição** caixa de diálogo.

    ![Atribuir utilizador][203]

1. No **utilizadores e grupos** caixa de diálogo, selecione **Eduarda Almeida** na lista utilizadores.

1. Clique em **selecionar** botão **utilizadores e grupos** caixa de diálogo.

1. Clique em **atribua** botão **adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste de início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico de VMS de IQNavigator no painel de acesso, deve obter automaticamente com sessão iniciada para a sua aplicação IQNavigator VMS.
Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

<!--Image references-->

[1]: ./media/iqnavigatorvms-tutorial/tutorial_general_01.png
[2]: ./media/iqnavigatorvms-tutorial/tutorial_general_02.png
[3]: ./media/iqnavigatorvms-tutorial/tutorial_general_03.png
[4]: ./media/iqnavigatorvms-tutorial/tutorial_general_04.png

[100]: ./media/iqnavigatorvms-tutorial/tutorial_general_100.png

[200]: ./media/iqnavigatorvms-tutorial/tutorial_general_200.png
[201]: ./media/iqnavigatorvms-tutorial/tutorial_general_201.png
[202]: ./media/iqnavigatorvms-tutorial/tutorial_general_202.png
[203]: ./media/iqnavigatorvms-tutorial/tutorial_general_203.png

