---
title: 'Tutorial: Integração do Active Directory do Azure com vídeo 23 | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e o vídeo 23.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 5e73dd1d-3995-4a73-b9cf-1b2318d49cb3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/26/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ec0cfaaf0d4ae692581d63c7745660ffeacfb11f
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60439731"
---
# <a name="tutorial-azure-active-directory-integration-with-23-video"></a>Tutorial: Integração do Active Directory do Azure com vídeo 23

Neste tutorial, saiba como integrar a 23 de vídeo com o Azure Active Directory (Azure AD).

Integrar 23 vídeo com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao vídeo 23
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para o vídeo 23 (Single Sign-On) com as suas contas do Azure AD
- Pode gerir as suas contas num local central – portal do Azure

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com vídeo 23, terá dos seguintes itens:

- Uma subscrição do Azure
- Um 23 vídeo início de sessão único na subscrição ativado

> [!NOTE]
> Para testar os passos neste tutorial, recomendamos que não utilize um ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, vai testar do Azure AD início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando a 23 de vídeo da Galeria
2. Configuração e teste do Azure AD início de sessão único

## <a name="adding-23-video-from-the-gallery"></a>Adicionando a 23 de vídeo da Galeria
Para configurar a integração de 23 de vídeo com o Azure AD, terá de adicionar 23 de vídeo a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar 23 de vídeo a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)** , no painel de navegação esquerdo, clique em **Azure Active Directory** ícone. 

    ![Active Directory][1]

2. Navegue para **aplicações empresariais**. Em seguida, aceda a **todos os aplicativos**.

    ![Aplicações][2]
    
3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![Aplicações][3]

4. Na caixa de pesquisa, escreva **vídeo 23**.

    ![Criar um utilizador de teste do Azure AD](./media/23video-tutorial/tutorial_23video_search.png)

5. No painel de resultados, selecione **vídeo 23**e, em seguida, clique em **Add** botão para adicionar a aplicação.

    ![Criar um utilizador de teste do Azure AD](./media/23video-tutorial/tutorial_23video_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuração e teste do Azure AD início de sessão único
Nesta secção, configure e teste do Azure AD início de sessão único com vídeo 23 com base num utilizador de teste chamado "Eduarda Almeida."

Para o início de sessão único funcione, o Azure AD precisa saber qual é o utilizador de contraparte 23 de vídeo a um utilizador no Azure AD. Em outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado 23 vídeo tem de ser estabelecida.

Vídeo 23, atribuir o valor do **nome de utilizador** no Azure AD como o valor do **Username** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD início de sessão único com vídeo 23, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configuring-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#creating-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
3. **[Criar um utilizador de teste de vídeo 23](#creating-a-23-video-test-user)**  - para ter um equivalente da Eduarda Almeida 23 vídeo que está ligado à representação de utilizador do Azure AD.
4. **[Atribuir o utilizador de teste do Azure AD](#assigning-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Teste de início de sessão único](#testing-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do Azure AD início de sessão único

Nesta secção, pode ativar o Azure AD início de sessão único no portal do Azure e configurar início de sessão único na sua aplicação de vídeo 23.

**Para configurar o Azure AD início de sessão único com vídeo 23, execute os seguintes passos:**

1. No portal do Azure, sobre o **vídeo 23** página de integração de aplicação, clique em **início de sessão único**.

    ![Configurar o início de sessão único][4]

2. Sobre o **início de sessão único** caixa de diálogo, selecione **modo** como **baseado em SAML logon** para ativar o início de sessão único.
 
    ![Configurar o início de sessão único](./media/23video-tutorial/tutorial_23video_samlbase.png)

3. Sobre o **23 URLs de domínio de vídeo e** secção, execute os seguintes passos:

    ![Configurar o início de sessão único](./media/23video-tutorial/tutorial_23video_url.png)

    a. Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão: `https://<subdomain>.23video.com`

    b. Na **identificador** caixa de texto, escreva um URL com o seguinte padrão: `https://www.23video.com/saml/trust/<uniqueid>`

    > [!NOTE] 
    > Estes valores não são reais. Atualize estes valores com o URL de início de sessão e o identificador real. Contacte [23 equipa de suporte de cliente de vídeo](mailto:support@23company.com) obter esses valores. 
 
4. Sobre o **certificado de assinatura SAML** secção, clique em **certificado (Base64)** e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![Configurar o início de sessão único](./media/23video-tutorial/tutorial_23video_certificate.png) 

5. Clique em **guardar** botão.

    ![Configurar o início de sessão único](./media/23video-tutorial/tutorial_general_400.png)

6. Sobre o **23 de vídeo de configuração** secção, clique em **configurar vídeo de 23** para abrir **configurar início de sessão** janela. Cópia a **URL de fim de sessão, o ID de entidade de SAML e o SAML único início de sessão no URL do serviço** partir o **secção de referência rápida.**

    ![Configurar o início de sessão único](./media/23video-tutorial/tutorial_23video_configure.png) 

7. Para configurar o início de sessão único num **vídeo 23** lado, terá de enviar o transferido **certificado (Base64)** , **URL de fim de sessão, o ID de entidade de SAML e o SAML único início de sessão no URL do serviço**para [a equipa de suporte de vídeo 23](mailto:support@23company.com). 


> [!TIP]
> Agora pode ler uma versão concisa destas instruções dentro do [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir da **do Active Directory > aplicações empresariais** secção, basta clicar o **Single Sign-On** separador e a documentação do embedded através de acesso a  **Configuração** seção na parte inferior. Pode ler mais sobre a funcionalidade de documentação do embedded aqui: [Documentação do Azure AD incorporado]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD
O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

![Criar utilizador do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. Na **portal do Azure**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![Criar um utilizador de teste do Azure AD](./media/23video-tutorial/create_aaduser_01.png) 

2. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos** e clique em **todos os utilizadores**.
    
    ![Criar um utilizador de teste do Azure AD](./media/23video-tutorial/create_aaduser_02.png) 

3. Para abrir o **usuário** caixa de diálogo, clique em **Add** na parte superior da caixa de diálogo.
 
    ![Criar um utilizador de teste do Azure AD](./media/23video-tutorial/create_aaduser_03.png) 

4. Sobre o **utilizador** caixa de diálogo página, execute os seguintes passos:
 
    ![Criar um utilizador de teste do Azure AD](./media/23video-tutorial/create_aaduser_04.png) 

    a. Na **Name** caixa de texto, tipo **BrittaSimon**.

    b. Na **nome de utilizador** caixa de texto, tipo a **endereço de e-mail** de BrittaSimon.

    c. Selecione **mostrar palavra-passe** e indique o valor da **palavra-passe**.

    d. Clique em **Criar**.
 
### <a name="creating-a-23-video-test-user"></a>Criar um utilizador de teste de vídeo 23

O objetivo desta secção é criar um utilizador chamado Eduarda Almeida 23 de vídeo.

**Para criar um usuário chamado Eduarda Almeida vídeo 23, execute os seguintes passos:**

1. Inicie sessão site da sua empresa vídeo 23 como administrador.

2. Aceda a **definições**.
 
3. Na **usuários** secção, clique em **configurar**.
   
    ![Atribuir utilizador][400]

4. Clique em **adicionar um novo utilizador**. 
   
    ![Atribuir utilizador][401]

5. Na **convidar alguém para aderir a este site** secção, execute os seguintes passos:
   
    ![Atribuir utilizador][402]

    a. Na **endereços de email** caixa de texto, escreva o endereço de e-mail da Eduarda Almeida no Azure AD.  
 
    b. Clique em **adicione o utilizador**.   

### <a name="assigning-the-azure-ad-test-user"></a>Atribuir o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso a 23 de vídeo.

![Atribuir utilizador][200] 

**Para atribuir a Eduarda Almeida a 23 de vídeo, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicativos e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todos os aplicativos**.

    ![Atribuir utilizador][201] 

2. Na lista de aplicações, selecione **vídeo 23**.

    ![Configurar o início de sessão único](./media/23video-tutorial/tutorial_23video_app.png) 

3. No menu à esquerda, clique em **utilizadores e grupos**.

    ![Atribuir utilizador][202] 

4. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** nos **adicionar atribuição** caixa de diálogo.

    ![Atribuir utilizador][203]

5. No **utilizadores e grupos** caixa de diálogo, selecione **Eduarda Almeida** na lista utilizadores.

6. Clique em **selecionar** botão **utilizadores e grupos** caixa de diálogo.

7. Clique em **atribua** botão **adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste de início de sessão único

O objetivo desta secção consiste em testar a configuração de SSO do Azure AD através do painel de acesso.

Quando clica no mosaico de vídeo 23 no painel de acesso, deve obter automaticamente com sessão iniciada para a sua aplicação de vídeo 23. 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

<!--Image references-->

[1]: ./media/23video-tutorial/tutorial_general_01.png
[2]: ./media/23video-tutorial/tutorial_general_02.png
[3]: ./media/23video-tutorial/tutorial_general_03.png
[4]: ./media/23video-tutorial/tutorial_general_04.png

[100]: ./media/23video-tutorial/tutorial_general_100.png

[200]: ./media/23video-tutorial/tutorial_general_200.png
[201]: ./media/23video-tutorial/tutorial_general_201.png
[202]: ./media/23video-tutorial/tutorial_general_202.png
[203]: ./media/23video-tutorial/tutorial_general_203.png

[400]: ./media/23video-tutorial/tutorial_23video_10.png
[401]: ./media/23video-tutorial/tutorial_23video_11.png
[402]: ./media/23video-tutorial/tutorial_23video_12.png
