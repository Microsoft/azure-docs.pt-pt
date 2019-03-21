---
title: 'Tutorial: Integração do Active Directory do Azure com o Sedgwick CMS | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e Sedgwick CMS.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 957931e0-e426-47e7-9904-3ed98d3f504c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/09/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4c42b7010a6586e4845d3a58b9a47b01143f5b1d
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57904836"
---
# <a name="tutorial-azure-active-directory-integration-with-sedgwick-cms"></a>Tutorial: Integração do Active Directory do Azure com Sedgwick CMS

Neste tutorial, saiba como integrar o Sedgwick CMS com o Azure Active Directory (Azure AD).

Integrar o Sedgwick CMS no Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao Sedgwick CMS.
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para Sedgwick CMS (Single Sign-On) com as suas contas do Azure AD.
- Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Sedgwick CMS, terá dos seguintes itens:

- Uma subscrição do Azure
- Um Sedgwick CMS logon único habilitado subscrição

> [!NOTE]
> Para testar os passos neste tutorial, recomendamos que não utilize um ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode [obtenha uma avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, vai testar do Azure AD início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando Sedgwick CMS da Galeria
1. Configuração e teste do Azure AD início de sessão único

## <a name="adding-sedgwick-cms-from-the-gallery"></a>Adicionando Sedgwick CMS da Galeria
Para configurar a integração do Sedgwick CMS para o Azure AD, terá de adicionar Sedgwick CMS a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar Sedgwick CMS a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone. 

    ![O botão do Azure Active Directory][1]

1. Navegue para **aplicações empresariais**. Em seguida, aceda a **todos os aplicativos**.

    ![O painel de aplicações empresariais][2]
    
1. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo][3]

1. Na caixa de pesquisa, escreva **Sedgwick CMS**, selecione **Sedgwick CMS** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

    ![CMS Sedgwick na lista de resultados](./media/sedgwickcms-tutorial/tutorial_sedgwickcms_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com Sedgwick CMS baseado num utilizador de teste chamado "Eduarda Almeida".

Para o início de sessão único funcione, o Azure AD precisa saber qual é o utilizador de contraparte no Sedgwick CMS para um utilizador no Azure AD. Em outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Sedgwick CMS deve ser estabelecido.

No Sedgwick CMS, atribuir o valor do **nome de utilizador** no Azure AD como o valor da **nome de utilizador** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD início de sessão único com Sedgwick CMS, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
1. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
1. **[Criar um utilizador de teste de Sedgwick CMS](#create-a-sedgwick-cms-test-user)**  - para ter um equivalente da Eduarda Almeida no CMS Sedgwick que está ligado à representação de utilizador do Azure AD.
1. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
1. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, pode ativar o Azure AD início de sessão único no portal do Azure e configurar início de sessão único em seu aplicativo Sedgwick CMS.

**Para configurar o Azure AD início de sessão único com Sedgwick CMS, execute os seguintes passos:**

1. No portal do Azure, sobre o **Sedgwick CMS** página de integração de aplicação, clique em **início de sessão único**.

    ![Configurar a ligação de início de sessão única][4]

1. Sobre o **início de sessão único** caixa de diálogo, selecione **modo** como **baseado em SAML logon** para ativar o início de sessão único.
 
    ![Caixa de diálogo de início de sessão único](./media/sedgwickcms-tutorial/tutorial_sedgwickcms_samlbase.png)

1. Sobre o **Sedgwick CMS domínio e URLs** secção, execute os seguintes passos:

    ![Domínio de CMS Sedgwick e URLs únicas início de sessão em informações](./media/sedgwickcms-tutorial/tutorial_sedgwickcms_url.png)

    a. Na **identificador** caixa de texto, escreva o URL: 

    | |
    |--|
    | `expresspreview.sedgwickcms.net/voe/sso` |
    | `claimlookup.com/Voe/sso` |

    b. Na **URL de resposta** caixa de texto, escreva um URL com o seguinte padrão:
    
    | |
    |--|
    | `https://<subdomain>.sedgwickcms.net/voe/sso` |
    | `https://claimlookup.com/Voe/sso` |

    > [!NOTE] 
    > Estes valores não são reais. Atualize estes valores com o identificador real e o URL de resposta. Contacte [equipa de suporte de Sedgwick CMS](https://www.sedgwick.com/help) obter esses valores.
 

1. Sobre o **certificado de assinatura SAML** secção, clique em **XML de metadados** e, em seguida, guarde o ficheiro de metadados no seu computador.

    ![O link de download de certificado](./media/sedgwickcms-tutorial/tutorial_sedgwickcms_certificate.png) 

1. Clique em **guardar** botão.

    ![Configurar o botão único início de sessão em Guardar](./media/sedgwickcms-tutorial/tutorial_general_400.png)

1. Para configurar o início de sessão único num **Sedgwick CMS** lado, terá de enviar o transferido **XML de metadados** para [equipa de suporte de Sedgwick CMS](https://www.sedgwick.com/help). Se definir esta definição para que a ligação de SAML SSO definidas corretamente em ambos os lados.

> [!TIP]
> Agora pode ler uma versão concisa destas instruções dentro do [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir da **do Active Directory > aplicações empresariais** secção, basta clicar o **Single Sign-On** separador e a documentação do embedded através de acesso a  **Configuração** seção na parte inferior. Pode ler mais sobre a funcionalidade de documentação do embedded aqui: [Documentação do Azure AD incorporado]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

   ![Criar um utilizador de teste do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No portal do Azure, no painel esquerdo, clique nas **do Azure Active Directory** botão.

    ![O botão do Azure Active Directory](./media/sedgwickcms-tutorial/create_aaduser_01.png)

1. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos**e, em seguida, clique em **todos os utilizadores**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](./media/sedgwickcms-tutorial/create_aaduser_02.png)

1. Para abrir o **usuário** caixa de diálogo, clique em **Add** na parte superior a **todos os utilizadores** caixa de diálogo.

    ![Botão Adicionar](./media/sedgwickcms-tutorial/create_aaduser_03.png)

1. Na **utilizador** diálogo caixa, execute os seguintes passos:

    ![A caixa de diálogo de utilizador](./media/sedgwickcms-tutorial/create_aaduser_04.png)

    a. Na **Name** , escreva **BrittaSimon**.

    b. Na **nome de utilizador** , escreva o endereço de e-mail do utilizador Eduarda Almeida.

    c. Selecione o **mostrar palavra-passe** caixa de verificação e, em seguida, anote o valor que é apresentado na **palavra-passe** caixa.

    d. Clique em **Criar**.
  
### <a name="create-a-sedgwick-cms-test-user"></a>Criar um utilizador de teste Sedgwick CMS

Nesta secção, vai criar um usuário chamado Eduarda Almeida no Sedgwick CMS. Trabalhar com [equipa de suporte de Sedgwick CMS](https://www.sedgwick.com/help) para adicionar os utilizadores na plataforma Sedgwick CMS. Os utilizadores tem de ser criados e ativados antes de utilizar o início de sessão único.  

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único, concedendo acesso para Sedgwick CMS.

![Atribuir a função de utilizador][200] 

**Para atribuir a Eduarda Almeida para Sedgwick CMS, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicativos e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todos os aplicativos**.

    ![Atribuir utilizador][201] 

1. Na lista de aplicações, selecione **Sedgwick CMS**.

    ![A ligação de Sedgwick CMS na lista de aplicações](./media/sedgwickcms-tutorial/tutorial_sedgwickcms_app.png)  

1. No menu à esquerda, clique em **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"][202]

1. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** nos **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição][203]

1. No **utilizadores e grupos** caixa de diálogo, selecione **Eduarda Almeida** na lista utilizadores.

1. Clique em **selecionar** botão **utilizadores e grupos** caixa de diálogo.

1. Clique em **atribua** botão **adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico de Sedgwick CMS no painel de acesso, deve obter automaticamente com sessão iniciada para a sua aplicação Sedgwick CMS.
Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)



<!--Image references-->

[1]: ./media/sedgwickcms-tutorial/tutorial_general_01.png
[2]: ./media/sedgwickcms-tutorial/tutorial_general_02.png
[3]: ./media/sedgwickcms-tutorial/tutorial_general_03.png
[4]: ./media/sedgwickcms-tutorial/tutorial_general_04.png

[100]: ./media/sedgwickcms-tutorial/tutorial_general_100.png

[200]: ./media/sedgwickcms-tutorial/tutorial_general_200.png
[201]: ./media/sedgwickcms-tutorial/tutorial_general_201.png
[202]: ./media/sedgwickcms-tutorial/tutorial_general_202.png
[203]: ./media/sedgwickcms-tutorial/tutorial_general_203.png

