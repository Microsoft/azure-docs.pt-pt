---
title: 'Tutorial: Integração do Active Directory do Azure com Zahájena | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e Zahájena.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: c3546eab-96b5-489b-a309-b895eb428053
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/3/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b4be5087af70e10e5a73ea2a183a25b326aea664
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57433472"
---
# <a name="tutorial-azure-active-directory-integration-with-servicechannel"></a>Tutorial: Integração do Active Directory do Azure com Zahájena

Neste tutorial, saiba como integrar Zahájena com o Azure Active Directory (Azure AD).

Integrar Zahájena no Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao Zahájena
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para Zahájena (Single Sign-On) com as suas contas do Azure AD
- Pode gerir as suas contas num local central – o portal de gestão do Azure

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Zahájena, terá dos seguintes itens:

- Uma subscrição do Azure
- Um Zahájena início de sessão único na subscrição ativado

> [!NOTE]
> Para testar os passos neste tutorial, recomendamos que não utilize um ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não deve utilizar o seu ambiente de produção, a menos que isso é necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, vai testar do Azure AD início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando Zahájena da Galeria
1. Configuração e teste do Azure AD início de sessão único

## <a name="adding-servicechannel-from-the-gallery"></a>Adicionando Zahájena da Galeria
Para configurar a integração do Zahájena com o Azure AD, terá de adicionar Zahájena a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar Zahájena a partir da galeria, execute os seguintes passos:**

1. Na  **[do Portal de gestão do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone. 

    ![Active Directory][1]

1. Navegue para **aplicações empresariais**. Em seguida, aceda a **todos os aplicativos**.

    ![Aplicações][2]
    
1. Clique em **adicionar** botão na parte superior da caixa de diálogo.

    ![Aplicações][3]

1. Na caixa de pesquisa, escreva **Zahájena**.

    ![Criar um utilizador de teste do Azure AD](./media/servicechannel-tutorial/tutorial-servicechannel_000.png)

1. No painel de resultados, selecione **Zahájena**e, em seguida, clique em **Add** botão para adicionar a aplicação.

    ![Criar um utilizador de teste do Azure AD](./media/servicechannel-tutorial/tutorial-servicechannel_2.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuração e teste do Azure AD início de sessão único
Nesta secção, configure e teste do Azure AD início de sessão único com Zahájena com base num utilizador de teste chamado "Eduarda Almeida".

Para o início de sessão único funcione, o Azure AD precisa saber qual é o utilizador de contraparte no Zahájena a um utilizador no Azure AD. Em outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Zahájena deve ser estabelecido.

Esta relação de ligação é estabelecida atribuindo o valor do **nome de utilizador** no Azure AD como o valor da **Username** no Zahájena.

Para configurar e testar o Azure AD início de sessão único com Zahájena, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configuring-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
1. **[Criar um utilizador de teste do Azure AD](#creating-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
1. **[Criar um utilizador de teste Zahájena](#creating-a-servicechannel-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
1. **[Atribuir o utilizador de teste do Azure AD](#assigning-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
1. **[Teste de início de sessão único](#testing-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do Azure AD início de sessão único

Nesta secção, pode ativar o Azure AD início de sessão único no portal de gestão do Azure e configurar início de sessão único em seu aplicativo Zahájena.

**Para configurar o Azure AD início de sessão único com Zahájena, execute os seguintes passos:**

1. No portal de gestão do Azure, sobre o **Zahájena** página de integração de aplicação, clique em **início de sessão único**.

    ![Configurar o início de sessão único][4]

1. Sobre o **início de sessão único** caixa de diálogo, como **modo** selecione **baseado em SAML início de sessão** para ativar o início de sessão único.
 
    ![Configurar o início de sessão único](./media/servicechannel-tutorial/tutorial-servicechannel_01.png)

1. Sobre o **Zahájena domínio e URLs** secção, execute os seguintes passos:

    ![Configurar o início de sessão único](./media/servicechannel-tutorial/tutorial-servicechannel_urls.png)

    a. Na **identificador** caixa de texto, digite o valor como: `http://adfs.<domain>.com/adfs/service/trust`

    b. Na **URL de resposta** caixa de texto, escreva um URL com o seguinte padrão: `https://<customer domain>.servicechannel.com/saml/acs`

    > [!NOTE] 
    > Tenha em atenção que estes não são os valores reais. Terá de atualizar estes valores com o identificador real e o URL de resposta. Aqui iremos sugerir-lhe utilizar o valor único de cadeia de caracteres no identificador de. Contacte [equipa de suporte de Zahájena](https://servicechannel.zendesk.com/hc/en-us) obter esses valores.

1. Seu aplicativo Zahájena espera que as asserções SAML num formato específico, o que requer a adição de mapeamentos de atributos personalizado à sua configuração de atributos de token SAML. Captura de ecrã seguinte mostra um exemplo disso. **NameIdentifier (identificador de utilizador)** é a afirmação apenas obrigatória e o valor predefinido é **user.userprincipalname** mas Zahájena espera que isso seja mapeado com **user.mail**. Se estiver a planear ativar o aprovisionamento de utilizador do Just In Time, em seguida, deve adicionar as seguintes declarações conforme mostrado abaixo. **Função** afirmação precisa ser mapeado para **user.assignedroles** que contém a função do utilizador.  

    Pode consultar o guia de Zahájena [aqui](https://servicechannel.zendesk.com/hc/en-us/articles/217514326-Azure-AD-Configuration-Example) para obter mais orientações sobre afirmações.
    
    ![Configurar o início de sessão único](./media/servicechannel-tutorial/tutorial_servicechannel_attribute.png)

    > [!NOTE] 
    > Ver [gerir o acesso com RBAC e o portal do Azure](../../role-based-access-control/role-assignments-portal.md) para saber como configurar **função** no Azure AD.

1. Na **atributos de utilizador** secção, clique em **ver e editar todos os outros atributos de utilizador** e definir os atributos.

    | Nome do Atributo | Valor do Atributo |
    | --- | --- |    
    | Função| user.assignedroles |

    a. Clique em **adicionar atributo** para abrir o **adicionar atributo** caixa de diálogo.

    ![Configurar o início de sessão único](./media/servicechannel-tutorial/tutorial_servicechannel_04.png)

    ![Configurar o início de sessão único](./media/servicechannel-tutorial/tutorial_servicechannel_05.png)
    
    b. Na **nome** caixa de texto, escreva o nome de atributo apresentado para essa linha.
    
    c. Partir do **valor** lista, digite o valor de atributo apresentado para essa linha.
    
    d. Clique em **Ok**
    
1. Sobre o **certificado de assinatura SAML** secção, clique em **certificado (Base64)** e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![Configurar o início de sessão único](./media/servicechannel-tutorial/tutorial-servicechannel_05.png) 

1. Clique em **Guardar**.

    ![Configurar o início de sessão único](./media/servicechannel-tutorial/tutorial_general_400.png)

1. Sobre o **Zahájena configuração** secção, clique em **configurar Zahájena** para abrir **configurar início de sessão** janela. Tenha em atenção o **ID de entidade de SAML** partir do **referência rápida** secção.

1. Para configurar o início de sessão único num **Zahájena** lado, terá de enviar o transferido **certificado (Base64)** e **ID de entidade de SAML** para [Zahájena equipa de suporte](https://servicechannel.zendesk.com/hc/en-us). Eles irão configurar isso para ter a ligação de SAML SSO definidas corretamente em ambos os lados.

### <a name="creating-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD
O objetivo desta secção consiste em criar um utilizador de teste no portal do Azure Management chamado Eduarda Almeida.

![Criar utilizador do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. Na **portal de gestão do Azure**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![Criar um utilizador de teste do Azure AD](./media/servicechannel-tutorial/create_aaduser_01.png) 

1. Aceda a **utilizadores e grupos** e clique em **todos os utilizadores** para apresentar a lista de utilizadores.
    
    ![Criar um utilizador de teste do Azure AD](./media/servicechannel-tutorial/create_aaduser_02.png) 

1. Na parte superior da caixa de diálogo, clique em **Add** para abrir o **utilizador** caixa de diálogo.
 
    ![Criar um utilizador de teste do Azure AD](./media/servicechannel-tutorial/create_aaduser_03.png) 

1. Sobre o **utilizador** caixa de diálogo página, execute os seguintes passos:
 
    ![Criar um utilizador de teste do Azure AD](./media/servicechannel-tutorial/create_aaduser_04.png) 

    a. Na **Name** caixa de texto, tipo **BrittaSimon**.

    b. Na **nome de utilizador** caixa de texto, tipo a **endereço de e-mail** de BrittaSimon.

    c. Selecione **mostrar palavra-passe** e indique o valor da **palavra-passe**.

    d. Clique em **Criar**. 

### <a name="creating-a-servicechannel-test-user"></a>Criar um utilizador de teste Zahájena

Aplicativo oferece suporte apenas no tempo de aprovisionamento de utilizador e de utilizadores de autenticação serão criados no aplicativo automaticamente. Para o aprovisionamento de utilizador completo, entre em contato com [Zahájena a equipa de suporte](https://servicechannel.zendesk.com/hc/en-us)

### <a name="assigning-the-azure-ad-test-user"></a>Atribuir o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para Zahájena.

![Atribuir utilizador][200] 

**Para atribuir a Eduarda Almeida a Zahájena, execute os seguintes passos:**

1. No portal de gestão do Azure, abra a vista de aplicativos e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todos os aplicativos**.

    ![Atribuir utilizador][201] 

1. Na lista de aplicações, selecione **Zahájena**.

    ![Configurar o início de sessão único](./media/servicechannel-tutorial/tutorial-servicechannel_app01.png) 

1. No menu à esquerda, clique em **utilizadores e grupos**.

    ![Atribuir utilizador][202] 

1. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** nos **adicionar atribuição** caixa de diálogo.

    ![Atribuir utilizador][203]

1. No **utilizadores e grupos** caixa de diálogo, selecione **Eduarda Almeida** na lista utilizadores.

1. Clique em **selecionar** botão **utilizadores e grupos** caixa de diálogo.

1. Clique em **atribua** botão **adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste de início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico Zahájena no painel de acesso, deve obter automaticamente sessão iniciada em seu aplicativo Zahájena.

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)


<!--Image references-->

[1]: ./media/servicechannel-tutorial/tutorial_general_01.png
[2]: ./media/servicechannel-tutorial/tutorial_general_02.png
[3]: ./media/servicechannel-tutorial/tutorial_general_03.png
[4]: ./media/servicechannel-tutorial/tutorial_general_04.png

[100]: ./media/servicechannel-tutorial/tutorial_general_100.png

[200]: ./media/servicechannel-tutorial/tutorial_general_200.png
[201]: ./media/servicechannel-tutorial/tutorial_general_201.png
[202]: ./media/servicechannel-tutorial/tutorial_general_202.png
[203]: ./media/servicechannel-tutorial/tutorial_general_203.png
