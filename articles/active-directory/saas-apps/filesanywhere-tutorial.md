---
title: 'Tutorial: Integração do Active Directory do Azure com FilesAnywhere | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e FilesAnywhere.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/17/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 54cbcb19e400f08e1e1d8e97afaa2e2e1cff133c
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/13/2019
ms.locfileid: "56205839"
---
# <a name="tutorial-azure-active-directory-integration-with-filesanywhere"></a>Tutorial: Integração do Active Directory do Azure com FilesAnywhere

Neste tutorial, saiba como integrar FilesAnywhere com o Azure Active Directory (Azure AD).

Integrar FilesAnywhere no Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao FilesAnywhere
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para FilesAnywhere (Single Sign-On) com as suas contas do Azure AD
- Pode gerir as suas contas num local central – o portal de gestão do Azure

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com FilesAnywhere, terá dos seguintes itens:

- Uma subscrição do Azure
- Um FilesAnywhere início de sessão único na subscrição ativado


> [!NOTE]
> Para testar os passos neste tutorial, recomendamos que não utilize um ambiente de produção.


Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não deve utilizar o seu ambiente de produção, a menos que isso é necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, vai testar do Azure AD início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando FilesAnywhere da Galeria
1. Configuração e teste do Azure AD início de sessão único


## <a name="adding-filesanywhere-from-the-gallery"></a>Adicionando FilesAnywhere da Galeria
Para configurar a integração do FilesAnywhere com o Azure AD, terá de adicionar FilesAnywhere a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar FilesAnywhere a partir da galeria, execute os seguintes passos:**

1. Na  **[do Portal de gestão do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone. 

    ![Active Directory][1]

1. Navegue para **aplicações empresariais**. Em seguida, aceda a **todos os aplicativos**.

    ![Aplicações][2]
    
1. Clique em **adicionar** botão na parte superior da caixa de diálogo.

    ![Aplicações][3]

1. Na caixa de pesquisa, escreva **FilesAnywhere**.

    ![Criar um utilizador de teste do Azure AD](./media/filesanywhere-tutorial/tutorial_FilesAnywhere_search.png)

1. No painel de resultados, selecione **FilesAnywhere**e, em seguida, clique em **Add** botão para adicionar a aplicação.

    ![Criar um utilizador de teste do Azure AD](./media/filesanywhere-tutorial/tutorial_FilesAnywhere_addfromgallery.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuração e teste do Azure AD início de sessão único
Nesta secção, configure e teste do Azure AD início de sessão único com FilesAnywhere com base num utilizador de teste chamado "Eduarda Almeida".

Para o início de sessão único funcione, o Azure AD precisa saber qual é o utilizador de contraparte no FilesAnywhere a um utilizador no Azure AD. Em outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no FilesAnywhere deve ser estabelecido.

Esta relação de ligação é estabelecida atribuindo o valor do **nome de utilizador** no Azure AD como o valor da **Username** no FilesAnywhere.

Para configurar e testar o Azure AD início de sessão único com FilesAnywhere, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configuring-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
1. **[Criar um utilizador de teste do Azure AD](#creating-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
1. **[Criar um utilizador de teste FilesAnywhere](#creating-a-filesanywhere-test-user)**  - para ter um equivalente da Eduarda Almeida na FilesAnywhere que está ligado à representação de ela do Azure AD.
1. **[Atribuir o utilizador de teste do Azure AD](#assigning-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
1. **[Teste de início de sessão único](#testing-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do Azure AD início de sessão único

Nesta secção, pode ativar o Azure AD início de sessão único no portal de gestão do Azure e configurar início de sessão único em seu aplicativo FilesAnywhere.

**Para configurar o Azure AD início de sessão único com FilesAnywhere, execute os seguintes passos:**

1. No portal de gestão do Azure, sobre o **FilesAnywhere** página de integração de aplicação, clique em **início de sessão único**.

    ![Configurar o início de sessão único][4]

1. Sobre o **início de sessão único** caixa de diálogo, como **modo** selecione **baseado em SAML início de sessão** para ativar o início de sessão único.
 
    ![Configurar o início de sessão único](./media/filesanywhere-tutorial/tutorial_FilesAnywhere_samlbase.png)

1. Sobre o **FilesAnywhere domínio e URLs** secção, se desejar configurar a aplicação no **IDP iniciada pelo modo**:

    ![Configurar o início de sessão único](./media/filesanywhere-tutorial/tutorial_filesanywhere_url.png)
    
    a. Na **URL de resposta** caixa de texto, escreva um URL com o seguinte padrão: `https://<company name>.filesanywhere.com/saml20.aspx?c=215`
> [!NOTE]
> Tenha em atenção que o valor **215** é um **clientid** e é apenas um exemplo. Tem de substituí-lo com o valor de ID de cliente real.

1. Sobre o **FilesAnywhere domínio e URLs** secção, se desejar configurar a aplicação no **SP iniciada pelo modo**, execute os seguintes passos:
    
    ![Configurar o início de sessão único](./media/filesanywhere-tutorial/tutorial_filesanywhere_url1.png)

    a. Clique nas **Mostrar definições de URL avançadas** opção

    b. Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão: `https://<sub domain>.filesanywhere.com/`

    > [!NOTE] 
    > Tenha em atenção que estes não são os valores reais. Terá de atualizar estes valores com o URL de início de sessão e o URL de resposta real. Contacte [equipa de suporte de FilesAnywhere](mailto:support@FilesAnywhere.com) obter esses valores. 

1. Aplicação de FilesAnywhere Software espera que as asserções SAML num formato específico. Configure as seguintes declarações para esta aplicação. Pode gerir os valores destes atributos da "**atributos de utilizador**" secção na página de integração de aplicações. Captura de ecrã seguinte mostra um exemplo disso.
    
    ![Configurar o início de sessão único](./media/filesanywhere-tutorial/tutorial_filesanywhere_attribute.png)
    
    Quando os utilizadores se inscreve com FilesAnywhere recebem o valor de **clientid** atributo partir [FilesAnywhere equipe](mailto:support@FilesAnywhere.com). É necessário adicionar o atributo "Id de cliente" com o valor exclusivo fornecido pelo FilesAnywhere. Todos esses atributos mostrados acima são necessários.
    > [!NOTE] 
    > Tenha em atenção que o valor **2331** dos **clientid** é apenas um exemplo. Tem de fornecer o valor real.


1. No **atributos de utilizador** secção sobre o **início de sessão único** caixa de diálogo, configurar o atributo de token de SAML conforme mostrado na imagem acima e execute os seguintes passos:
    
    | Nome do Atributo | Valor do Atributo |
    | ---------------| --------------- |    
    | ID de cliente | *"uniquevalue"* |

    a. Clique em **adicionar atributo** para abrir o **adicionar atributo** caixa de diálogo.

    ![Configurar o início de sessão único](./media/filesanywhere-tutorial/tutorial_FilesAnywhere_04.png)

    ![Configurar o início de sessão único](./media/filesanywhere-tutorial/tutorial_FilesAnywhere_05.png)
    
    b. Na **nome** caixa de texto, escreva o nome de atributo apresentado para essa linha.
    
    c. Partir do **valor** lista, digite o valor de atributo apresentado para essa linha.
    
    d. Clique em **Ok**

1. Clique em **guardar** botão.

    ![Configurar o início de sessão único](./media/filesanywhere-tutorial/tutorial_general_400.png)

1. Sobre o **certificado de assinatura SAML** secção, clique em **certificado (Base64)** e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![Configurar o início de sessão único](./media/filesanywhere-tutorial/tutorial_FilesAnywhere_certificate.png) 

1. Sobre o **FilesAnywhere configuração** secção, clique em **configurar FilesAnywhere** para abrir **configurar início de sessão** janela.

    ![Configurar o início de sessão único](./media/filesanywhere-tutorial/tutorial_FilesAnywhere_configure.png) 

    ![Configurar o início de sessão único](./media/filesanywhere-tutorial/tutorial_FilesAnywhere_configuresignon.png)

1.  Para obter a configuração de SSO completa para a sua aplicação, FilesAnywhere final, contacte [equipa de suporte de FilesAnywhere](mailto:support@FilesAnywhere.com) e forneça o URL de certificado e o único início de sessão (SSO) de assinatura de tokens SAML transferido.

### <a name="creating-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD
O objetivo desta secção consiste em criar um utilizador de teste no portal do Azure Management chamado Eduarda Almeida.

![Criar utilizador do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. Na **portal de gestão do Azure**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![Criar um utilizador de teste do Azure AD](./media/filesanywhere-tutorial/create_aaduser_01.png) 

1. Aceda a **utilizadores e grupos** e clique em **todos os utilizadores** para apresentar a lista de utilizadores.
    
    ![Criar um utilizador de teste do Azure AD](./media/filesanywhere-tutorial/create_aaduser_02.png) 

1. Na parte superior da caixa de diálogo, clique em **Add** para abrir o **utilizador** caixa de diálogo.
 
    ![Criar um utilizador de teste do Azure AD](./media/filesanywhere-tutorial/create_aaduser_03.png) 

1. Sobre o **utilizador** caixa de diálogo página, execute os seguintes passos:
 
    ![Criar um utilizador de teste do Azure AD](./media/filesanywhere-tutorial/create_aaduser_04.png) 

    a. Na **Name** caixa de texto, tipo **BrittaSimon**.

    b. Na **nome de utilizador** caixa de texto, tipo a **endereço de e-mail** de BrittaSimon.

    c. Selecione **mostrar palavra-passe** e indique o valor da **palavra-passe**.

    d. Clique em **Criar**. 



### <a name="creating-a-filesanywhere-test-user"></a>Criar um utilizador de teste FilesAnywhere

Aplicativo oferece suporte apenas no tempo de aprovisionamento de utilizador e de utilizadores de autenticação serão criados no aplicativo automaticamente. 


### <a name="assigning-the-azure-ad-test-user"></a>Atribuir o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para FilesAnywhere.

![Atribuir utilizador][200] 

**Para atribuir a Eduarda Almeida a FilesAnywhere, execute os seguintes passos:**

1. No portal de gestão do Azure, abra a vista de aplicativos e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todos os aplicativos**.

    ![Atribuir utilizador][201] 

1. Na lista de aplicações, selecione **FilesAnywhere**.

    ![Configurar o início de sessão único](./media/filesanywhere-tutorial/tutorial_FilesAnywhere_app.png) 

1. No menu à esquerda, clique em **utilizadores e grupos**.

    ![Atribuir utilizador][202] 

1. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** nos **adicionar atribuição** caixa de diálogo.

    ![Atribuir utilizador][203]

1. No **utilizadores e grupos** caixa de diálogo, selecione **Eduarda Almeida** na lista utilizadores.

1. Clique em **selecionar** botão **utilizadores e grupos** caixa de diálogo.

1. Clique em **atribua** botão **adicionar atribuição** caixa de diálogo.
    


### <a name="testing-single-sign-on"></a>Teste de início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico FilesAnywhere no painel de acesso, deve obter automaticamente sessão iniciada em seu aplicativo FilesAnywhere.


## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)



<!--Image references-->

[1]: ./media/FilesAnywhere-tutorial/tutorial_general_01.png
[2]: ./media/FilesAnywhere-tutorial/tutorial_general_02.png
[3]: ./media/FilesAnywhere-tutorial/tutorial_general_03.png
[4]: ./media/FilesAnywhere-tutorial/tutorial_general_04.png

[100]: ./media/FilesAnywhere-tutorial/tutorial_general_100.png

[200]: ./media/FilesAnywhere-tutorial/tutorial_general_200.png
[201]: ./media/FilesAnywhere-tutorial/tutorial_general_201.png
[202]: ./media/FilesAnywhere-tutorial/tutorial_general_202.png
[203]: ./media/FilesAnywhere-tutorial/tutorial_general_203.png
