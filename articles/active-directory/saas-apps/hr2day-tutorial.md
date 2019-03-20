---
title: 'Tutorial: Integração do Active Directory do Azure com HR2day por Merces | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e HR2day por Merces.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 853d08c9-27b1-48d4-b8e7-3705140eb67f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/24/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 378aab82fac5298c3785f752478e3bfc3c6e325b
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58103150"
---
# <a name="tutorial-azure-active-directory-integration-with-hr2day-by-merces"></a>Tutorial: Integração do Active Directory do Azure com HR2day por Merces

Neste tutorial, saiba como integrar HR2day por Merces com o Azure Active Directory (Azure AD).

Integrar HR2day por Merces no Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao HR2day por Merces.
- Pode permitir que os utilizadores automaticamente obter a sessão iniciada no HR2day por Merces com as suas contas do Azure AD.
- Pode gerir as suas contas num local central, o portal do Azure.

Para obter mais informações sobre a integração de aplicações SaaS com o Azure AD, consulte [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com HR2day por Merces, terá dos seguintes itens:

- Uma subscrição do Azure AD.
- Um HR2day por Merces início de sessão único ativar a subscrição.

> [!NOTE]
> Não é recomendado utilizar um ambiente de produção para testar os passos neste tutorial.

Para testar os passos neste tutorial, siga as seguintes recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Obter um [avaliação gratuita de um mês do Azure AD](https://azure.microsoft.com/pricing/free-trial/) se ainda não o tiver.  

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, vai testar do Azure AD início de sessão único num ambiente de teste. O cenário que está descrito aqui consiste em dois blocos de construção principais:

1. Adicionando HR2day por Merces da galeria.
1. Configuração e teste do Azure AD início de sessão único.

## <a name="add-hr2day-by-merces-from-the-gallery"></a>Adicionar HR2day por Merces a partir da Galeria
Para configurar a integração do HR2day por Merces com o Azure AD, adicione HR2day por Merces a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar HR2day por Merces a partir da galeria, siga os passos seguintes:**

1. Na [portal do Azure](https://portal.azure.com), no painel de navegação esquerdo, selecione a **Azure Active Directory** ícone. 

    ![Active Directory][1]

1. Aceda a **aplicações empresariais**. Em seguida, aceda a **todos os aplicativos**.

    ![Aplicações][2]
    
1. Para adicionar uma nova aplicação, selecione o **nova aplicação** botão na parte superior da caixa de diálogo.

    ![Aplicações][3]

1. Na caixa de pesquisa, escreva **HR2day por Merces**.

    ![Criar um utilizador de teste do Azure AD](./media/hr2day-tutorial/tutorial_hr2daybymerces_search.png)

1. No painel de resultados, selecione **HR2day por Merces**e, em seguida, selecione a **Add** botão para adicionar a aplicação.

    ![Criar um utilizador de teste do Azure AD](./media/hr2day-tutorial/tutorial_hr2daybymerces_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único
Nesta secção, configurar e testar o Azure AD início de sessão único com HR2day por Merces com base num utilizador de teste chamado "Eduarda Almeida."

Para o início de sessão único funcione, o Azure AD precisa saber que o utilizador de contraparte no HR2day por Merces for para um utilizador no Azure AD. Em outras palavras, tem de estabelecer uma ligação entre um utilizador do Azure AD e o utilizador relacionado no HR2day por Merces.

HR2day por Merces, atribuir os **nome de utilizador** no Azure AD para **Username** para estabelecer a relação.

Para configurar e testar o Azure AD início de sessão único com HR2day por Merces, tem de concluir os seguintes blocos de construção:

1. Configure o Azure AD início de sessão único: Permita que os utilizadores utilizar esta funcionalidade.
1. Crie um utilizador de teste do Azure AD: Teste do Azure AD início de sessão único com Eduarda Almeida.
1. Crie um HR2day por utilizador de teste Merces: Crie um equivalente da Eduarda Almeida HR2day por Merces que está ligado à representação de utilizador do Azure AD.
1. Atribua o utilizador de teste do Azure AD: Ative a Eduarda Almeida utilizar o Azure AD início de sessão único.
1. Testar início de sessão único: Certifique-se de que se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, pode ativar do Azure AD início de sessão único no portal do Azure e configurar o início de sessão único na sua HR2day pelo aplicativo Merces.

**Para configurar o Azure AD início de sessão único com HR2day por Merces, siga os passos seguintes:**

1. No portal do Azure, sobre o **HR2day por Merces** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar o início de sessão único][4]

1. Para ativar o início de sessão único, na **início de sessão único** caixa de diálogo, selecione **modo** como **baseado em SAML logon**.
 
    ![Configurar o início de sessão único](./media/hr2day-tutorial/tutorial_hr2daybymerces_samlbase.png)

1. Na **HR2day por Merces domínio e URLs** secção, siga os passos seguintes:

    ![Configurar o início de sessão único](./media/hr2day-tutorial/tutorial_hr2daybymerces_url.png)

    a. Na **URL de início de sessão** caixa, escreva um URL ao utilizar o seguinte padrão: `https://<tenantname>.force.com/<instancename>`.

    b. Na **identificador** caixa, escreva um URL ao utilizar o seguinte padrão: `https://hr2day.force.com/<companyname>`.

    > [!NOTE] 
    > Estes valores não são reais. Atualize estes valores com o URL de início de sessão real e o identificador. Contacte os [HR2day pela equipa de suporte de cliente Merces](mailto:servicedesk@merces.nl) obter esses valores. 
 


1. Sobre o **certificado de assinatura SAML** secção, selecione **Certificate(Base64)** e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![Configurar o início de sessão único](./media/hr2day-tutorial/tutorial_hr2daybymerces_certificate.png) 

1. Esta secção descreve como permitir que os utilizadores autenticar para HR2day por Merces com suas contas no Azure AD. Eles fazem-lo utilizando o Federação baseia-se no protocolo SAML.

    Sua HR2day pelo aplicativo Merces espera que as asserções SAML num formato específico, o que requer a adição de mapeamentos de atributos personalizados para o token SAML. Captura de ecrã seguinte mostra um exemplo disso. 

    ![Configurar o início de sessão único](./media/hr2day-tutorial/tutorial_hr2day_00.png)
    
   > [!NOTE]
   >  Antes de poder configurar a asserção de SAML, tem de contactar o [HR2day pela equipa de suporte de cliente Merces](mailto:servicedesk@merces.nl) e pedir o valor do atributo de identificador exclusivo para o seu inquilino. Vai precisar deste valor para concluir os passos na secção seguinte. 

1. Na **início de sessão único** caixa de diálogo a **atributos de utilizador** secção, configure o atributo de token SAML, conforme mostrado na imagem seguinte. Em seguida, siga os passos seguintes.
    
      | Nome de atributo    |   Valor do atributo |  
    | ------------------- | -------------------- |    
    | ATTR_LOGINCLAIM | `join([mail],"102938475Z","@"` |
    
      a. Para abrir o **adicionar atributo** caixa de diálogo, selecione **adicionar atributo**.

    ![Configurar o início de sessão único](./media/hr2day-tutorial/tutorial_attribute_04.png)

    ![Configurar o início de sessão único](./media/hr2day-tutorial/tutorial_attribute_05.png)

    b. Na **Name** , escreva **ATTR_LOGINCLAIM**.

    c. Partir do **valor** lista, selecione **Join()**.

    d. Partir do **String1** lista, selecione **user.mail**.

    e. Para **String2**, escreva o identificador exclusivo que é fornecido pela sua equipa de HR2day.

    f. Na **separador** , escreva **\@**.
    
    g. Selecione **Ok**.

1. Selecione o botão **Guardar**.

    ![Configurar o início de sessão único](./media/hr2day-tutorial/tutorial_general_400.png)

1. Na **HR2day pela configuração Merces** secção, selecione **configurar HR2day por Merces** para abrir o **configurar início de sessão** janela. Copiar o **URL de fim de sessão**, **ID de entidade de SAML**, e **SAML único início de sessão no URL do serviço** do **referência rápida** secção.

    ![Configurar o início de sessão único](./media/hr2day-tutorial/tutorial_hr2daybymerces_configure.png) 

1. Para configurar o SSO para a sua aplicação, entre em contato com o [HR2day pela equipa de suporte de cliente Merces](mailTo:servicedesk@merces.nl). Anexar o transferido **Certificate(Base64)** ficheiro para o seu e-mail. Também fornecem a **URL de fim de sessão**, **ID de entidade de SAML**, e **SAML único início de sessão no URL do serviço** para que possa ser configurados para integração de SSO.

    > [!NOTE]
    >Mencionar para a equipe de Merces que esta integração precisa o ID de entidade a ser definido com o padrão **https://hr2day.force.com/INSTANCENAME**.

    > [!TIP]
    >Agora pode ler uma versão concisa destas instruções dentro do [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir da **do Active Directory** > **aplicações empresariais** secção, selecione o **Single Sign-On** separador. Em seguida, aceder a documentação do embedded através do **configuração** seção na parte inferior. Pode ler mais sobre a funcionalidade de documentação do embedded na [documentação do embedded do Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
   > 

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD
O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

![Criar utilizador do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, siga os passos seguintes:**

1. Na **portal do Azure**, no painel de navegação esquerdo, selecione a **Azure Active Directory** ícone.

    ![Criar um utilizador de teste do Azure AD](./media/hr2day-tutorial/create_aaduser_01.png) 

1. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos**e, em seguida, selecione **todos os utilizadores**.
    
    ![Criar um utilizador de teste do Azure AD](./media/hr2day-tutorial/create_aaduser_02.png) 

1. Para abrir o **usuário** caixa de diálogo, selecione **Add** na parte superior da caixa de diálogo.
 
    ![Criar um utilizador de teste do Azure AD](./media/hr2day-tutorial/create_aaduser_03.png) 

1. Na **utilizador** diálogo caixa, siga os passos seguintes:
 
    ![Criar um utilizador de teste do Azure AD](./media/hr2day-tutorial/create_aaduser_04.png) 

    a. Na **Name** , escreva **BrittaSimon**.

    b. Na **nome de utilizador** , escreva o **endereço de e-mail** de BrittaSimon.

    c. Selecione **mostrar palavra-passe**e, em seguida, anote a palavra-passe.

    d. Selecione **Criar**.
 
### <a name="create-an-hr2day-by-merces-test-user"></a>Criar um HR2day por utilizador de teste Merces

O objetivo desta secção é criar um utilizador chamado Eduarda Almeida na HR2day por Merces. Para adicionar os utilizadores na conta HR2day, trabalhar com o [HR2day pela equipa de suporte de cliente Merces](mailto:servicedesk@merces.nl). 

> [!NOTE]
> Se precisar de criar manualmente um utilizador, entre em contato com o [HR2day pela equipa de suporte de cliente Merces](mailto:servicedesk@merces.nl).

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único, concedendo acesso para HR2day por Merces.

![Atribuir utilizador][200] 

**Para atribuir a Eduarda Almeida a HR2day por Merces, siga os passos seguintes:**

1. No portal do Azure, abra a vista de aplicações, vá para a vista de diretório e, em seguida, aceda a **aplicações empresariais**. Em seguida, selecione **todos os aplicativos**.

    ![Atribuir utilizador][201] 

1. Na lista de aplicações, selecione **HR2day por Merces**.

    ![Configurar o início de sessão único](./media/hr2day-tutorial/tutorial_hr2daybymerces_app.png) 

1. No menu à esquerda, selecione **utilizadores e grupos**.

    ![Atribuir utilizador][202] 

1. Selecione o **adicionar** botão. Em seguida, na **adicionar atribuição** caixa de diálogo, selecione **utilizadores e grupos**.

    ![Atribuir utilizador][203]

1. Na **utilizadores e grupos** caixa de diálogo a **utilizadores** lista, selecione **Eduarda Almeida**.

1. Clique nas **selecione** botão.

1. Na **adicionar atribuição** caixa de diálogo, selecione **atribuir**.
    
### <a name="test-single-sign-on"></a>Testar o início de sessão único

O objetivo desta secção é testar a configuração do Azure AD única início de sessão utilizando o painel de acesso.  

Quando seleciona o HR2day por Merces mosaico no painel de acesso, que automaticamente obter sessão iniciada no seu HR2day pelo aplicativo Merces.

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações de SaaS no Azure Active Directory](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)



<!--Image references-->

[1]: ./media/hr2day-tutorial/tutorial_general_01.png
[2]: ./media/hr2day-tutorial/tutorial_general_02.png
[3]: ./media/hr2day-tutorial/tutorial_general_03.png
[4]: ./media/hr2day-tutorial/tutorial_general_04.png

[100]: ./media/hr2day-tutorial/tutorial_general_100.png

[200]: ./media/hr2day-tutorial/tutorial_general_200.png
[201]: ./media/hr2day-tutorial/tutorial_general_201.png
[202]: ./media/hr2day-tutorial/tutorial_general_202.png
[203]: ./media/hr2day-tutorial/tutorial_general_203.png

