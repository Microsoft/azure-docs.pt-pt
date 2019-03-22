---
title: 'Tutorial: Integração do Active Directory do Azure com a sintaxe CRM | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e o CRM de sintaxe.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 3331b9fc-ebc0-4a3a-9f7b-bf20ee35d180
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 450fcbcdfab8e90728fbbbd0068123768f3cef4a
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57993308"
---
# <a name="tutorial-azure-active-directory-integration-with-sugar-crm"></a>Tutorial: Integração do Active Directory do Azure com o CRM de sintaxe

Neste tutorial, saiba como integrar o CRM de sintaxe com o Azure Active Directory (Azure AD).

Integração de CRM de sintaxe com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao CRM de sintaxe
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para a sintaxe de CRM (Single Sign-On) com as suas contas do Azure AD
- Pode gerir as suas contas num local central – portal do Azure

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o CRM de sintaxe, terá dos seguintes itens:

- Uma subscrição do Azure
- Uma sintaxe CRM logon único habilitado subscrição

> [!NOTE]
> Para testar os passos neste tutorial, recomendamos que não utilize um ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, vai testar do Azure AD início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o CRM de sintaxe da Galeria
1. Configuração e teste do Azure AD início de sessão único

## <a name="adding-sugar-crm-from-the-gallery"></a>Adicionando o CRM de sintaxe da Galeria
Para configurar a integração de CRM de sintaxe para o Azure AD, terá de adicionar o CRM de sintaxe na Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar o CRM de sintaxe da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone. 

    ![Active Directory][1]

1. Navegue para **aplicações empresariais**. Em seguida, aceda a **todos os aplicativos**.

    ![Aplicações][2]
    
1. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![Aplicações][3]

1. Na caixa de pesquisa, escreva **sintaxe CRM**.

    ![Criar um utilizador de teste do Azure AD](./media/sugarcrm-tutorial/tutorial_sugarcrm_search.png)

1. No painel de resultados, selecione **sintaxe CRM**e, em seguida, clique em **Add** botão para adicionar a aplicação.

    ![Criar um utilizador de teste do Azure AD](./media/sugarcrm-tutorial/tutorial_sugarcrm_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuração e teste do Azure AD início de sessão único
Nesta secção, configure e teste do Azure AD início de sessão único com o CRM de sintaxe, com base num utilizador de teste chamado "Eduarda Almeida".

Para o início de sessão único funcione, o Azure AD precisa saber qual é o utilizador equivalente no CRM de sintaxe para um utilizador no Azure AD. Em outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado na sintaxe CRM tem de ser estabelecida.

Na sintaxe CRM, atribua o valor do **nome de utilizador** no Azure AD como o valor do **Username** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD início de sessão único com o CRM de sintaxe, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configuring-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
1. **[Criar um utilizador de teste do Azure AD](#creating-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
1. **[Criar um utilizador de teste de sintaxe CRM](#creating-a-sugar-crm-test-user)**  - para ter um equivalente da Eduarda Almeida no CRM de sintaxe que está ligado à representação de utilizador do Azure AD.
1. **[Atribuir o utilizador de teste do Azure AD](#assigning-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
1. **[Teste de início de sessão único](#testing-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do Azure AD início de sessão único

Nesta secção, pode ativar o Azure AD início de sessão único no portal do Azure e configurar início de sessão único na sua aplicação CRM de sintaxe.

**Para configurar o Azure AD início de sessão único com o CRM de sintaxe, execute os seguintes passos:**

1. No portal do Azure, sobre o **sintaxe CRM** página de integração de aplicativo, clique em **início de sessão único**.

    ![Configurar o início de sessão único][4]

1. Sobre o **início de sessão único** caixa de diálogo, selecione **modo** como **baseado em SAML logon** para ativar o início de sessão único.
 
    ![Configurar o início de sessão único](./media/sugarcrm-tutorial/tutorial_sugarcrm_samlbase.png)

1. Sobre o **sintaxe CRM domínio e URLs** secção, execute os seguintes passos:

    ![Configurar o início de sessão único](./media/sugarcrm-tutorial/tutorial_sugarcrm_url.png)

    Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão:
    
    | |
    |--|
    | `https://<companyname>.sugarondemand.com` |
    | `https://<companyname>.trial.sugarcrm` |

    > [!NOTE] 
    > O valor não é real. Atualize o valor com o URL de início de sessão real. Contacte [equipa de suporte de cliente de CRM sintaxe](https://support.sugarcrm.com/) para obter o valor. 
 
1. Sobre o **certificado de assinatura SAML** secção, clique em **certificado (Base64)** e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![Configurar o início de sessão único](./media/sugarcrm-tutorial/tutorial_sugarcrm_certificate.png) 

1. Clique em **guardar** botão.

    ![Configurar o início de sessão único](./media/sugarcrm-tutorial/tutorial_general_400.png)

1. Sobre o **sintaxe CRM configuração** secção, clique em **configurar CRM de sintaxe** para abrir **configurar início de sessão** janela. Cópia a **URL de fim de sessão e SAML único início de sessão no URL do serviço** partir o **secção de referência rápida.**

    ![Configurar o início de sessão único](./media/sugarcrm-tutorial/tutorial_sugarcrm_configure.png) 

1. Numa janela do browser web diferente, inicie sessão no site da sua empresa sintaxe CRM como administrador.

1. Aceda a **administrador**.
   
    ![Admin](./media/sugarcrm-tutorial/ic795888.png "Admin")

1. Na **Administration** secção, clique em **gestão de palavra-passe**.
   
    ![Administração](./media/sugarcrm-tutorial/ic795889.png "administração")

1. Selecione **ativar a autenticação SAML**.
   
    ![Administração](./media/sugarcrm-tutorial/ic795890.png "administração")

1. Na **autenticação SAML** secção, execute os seguintes passos:
   
    ![Autenticação SAML](./media/sugarcrm-tutorial/ic795891.png "autenticação SAML")  
 
    a. Na **URL de início de sessão** caixa de texto, cole o valor de **SAML único início de sessão no URL do serviço**, que copiou do portal do Azure.
  
    b. Na **URL de SLO** caixa de texto, cole o valor de **URL de fim de sessão**, que copiou do portal do Azure.
  
    c. Abra o certificado com codificação base 64 no bloco de notas, copie o conteúdo do mesmo para a área de transferência e, em seguida, cole o certificado completo para **certificado X.509** caixa de texto.
  
    d. Clique em **Guardar**.

> [!TIP]
> Agora pode ler uma versão concisa destas instruções dentro do [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir da **do Active Directory > aplicações empresariais** secção, basta clicar o **Single Sign-On** separador e a documentação do embedded através de acesso a  **Configuração** seção na parte inferior. Pode ler mais sobre a funcionalidade de documentação do embedded aqui: [Documentação do Azure AD incorporado]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD
O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

![Criar utilizador do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. Na **portal do Azure**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![Criar um utilizador de teste do Azure AD](./media/sugarcrm-tutorial/create_aaduser_01.png) 

1. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos** e clique em **todos os utilizadores**.
    
    ![Criar um utilizador de teste do Azure AD](./media/sugarcrm-tutorial/create_aaduser_02.png) 

1. Para abrir o **usuário** caixa de diálogo, clique em **Add** na parte superior da caixa de diálogo.
 
    ![Criar um utilizador de teste do Azure AD](./media/sugarcrm-tutorial/create_aaduser_03.png) 

1. Sobre o **utilizador** caixa de diálogo página, execute os seguintes passos:
 
    ![Criar um utilizador de teste do Azure AD](./media/sugarcrm-tutorial/create_aaduser_04.png) 

    a. Na **Name** caixa de texto, tipo **BrittaSimon**.

    b. Na **nome de utilizador** caixa de texto, tipo a **endereço de e-mail** de BrittaSimon.

    c. Selecione **mostrar palavra-passe** e indique o valor da **palavra-passe**.

    d. Clique em **Criar**.
 
### <a name="creating-a-sugar-crm-test-user"></a>Criar um utilizador de teste de sintaxe CRM

Para habilitar os utilizadores do Azure AD iniciar sessão na sintaxe CRM, tem de ser aprovisionados para CRM de sintaxe.

No caso de sintaxe CRM, o aprovisionamento é uma tarefa manual.

**Para Aprovisionar uma conta de utilizador, execute os seguintes passos:**

1. Inicie sessão no seu **sintaxe CRM** site da empresa como administrador.

1. Aceda a **administrador**.
   
    ![Admin](./media/sugarcrm-tutorial/ic795888.png "Admin")

1. Na **Administration** secção, clique em **gestão de utilizadores**.
   
    ![Administração](./media/sugarcrm-tutorial/ic795893.png "administração")

1. Aceda a **usuários \> criar novo utilizador**.
   
    ![Criar novo usuário](./media/sugarcrm-tutorial/ic795894.png "criar novo utilizador")

1. Sobre o **perfil de utilizador** separador, execute os seguintes passos:
   
    ![Novo utilizador](./media/sugarcrm-tutorial/ic795895.png "novo utilizador")

    a. Tipo de **nome de utilizador**, **Apelido**, e **endereço de e-mail** de um utilizador válido do Azure Active Directory para as caixas de texto relacionados.
  
1. Como **Status**, selecione **Active Directory**.

1. No separador de palavras-passe, execute os seguintes passos:
   
    ![Novo utilizador](./media/sugarcrm-tutorial/ic795896.png "novo utilizador")

    a. Escreva a palavra-passe na caixa de texto relacionada.

    b. Clique em **Guardar**.

>[!NOTE]
>Pode usar qualquer outras sintaxe CRM utilizador conta ferramentas de criação ou APIs fornecidas pelo CRM de sintaxe para aprovisionar contas de utilizador do AAD. 
> 

### <a name="assigning-the-azure-ad-test-user"></a>Atribuir o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso ao CRM de sintaxe.

![Atribuir utilizador][200] 

**Para atribuir a Eduarda Almeida a sintaxe CRM, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicativos e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todos os aplicativos**.

    ![Atribuir utilizador][201] 

1. Na lista de aplicações, selecione **sintaxe CRM**.

    ![Configurar o início de sessão único](./media/sugarcrm-tutorial/tutorial_sugarcrm_app.png) 

1. No menu à esquerda, clique em **utilizadores e grupos**.

    ![Atribuir utilizador][202] 

1. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** nos **adicionar atribuição** caixa de diálogo.

    ![Atribuir utilizador][203]

1. No **utilizadores e grupos** caixa de diálogo, selecione **Eduarda Almeida** na lista utilizadores.

1. Clique em **selecionar** botão **utilizadores e grupos** caixa de diálogo.

1. Clique em **atribua** botão **adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste de início de sessão único

O objetivo desta secção é testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico de CRM de sintaxe no painel de acesso, deve obter automaticamente com sessão iniciada para a sua aplicação CRM de sintaxe.

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)



<!--Image references-->

[1]: ./media/sugarcrm-tutorial/tutorial_general_01.png
[2]: ./media/sugarcrm-tutorial/tutorial_general_02.png
[3]: ./media/sugarcrm-tutorial/tutorial_general_03.png
[4]: ./media/sugarcrm-tutorial/tutorial_general_04.png

[100]: ./media/sugarcrm-tutorial/tutorial_general_100.png

[200]: ./media/sugarcrm-tutorial/tutorial_general_200.png
[201]: ./media/sugarcrm-tutorial/tutorial_general_201.png
[202]: ./media/sugarcrm-tutorial/tutorial_general_202.png
[203]: ./media/sugarcrm-tutorial/tutorial_general_203.png

