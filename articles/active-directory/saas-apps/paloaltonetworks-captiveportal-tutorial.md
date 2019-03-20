---
title: 'Tutorial: Integração do Active Directory do Azure com o Portal de cativas da Palo Alto redes | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e o Portal de cativas de redes da Palo Alto.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 67a0b476-2305-4157-8658-2ec3625850d5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/25/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0a62aa573d49ccdd237e692b63a6fda0dd83d52a
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57888302"
---
# <a name="tutorial-azure-active-directory-integration-with-palo-alto-networks-captive-portal"></a>Tutorial: Integração do Active Directory do Azure com o Portal de cativas de redes da Palo Alto

Neste tutorial, saiba como integrar o Portal de cativas da Palo Alto redes com o Azure Active Directory (Azure AD).

Obtém as seguintes vantagens quando integrar o Portal de cativas da Palo Alto redes com o Azure AD:

* No Azure AD, pode controlar quem tem acesso ao Portal de cativas de redes da Palo Alto.
* Pode assinar automaticamente sessão dos utilizadores no Portal de cativas redes da Palo Alto (início de sessão único), utilizando as contas de utilizador do Azure AD.
* Pode gerir as suas contas num, local central, o portal do Azure.

Para saber mais sobre o software como uma integração de aplicação de serviço (SaaS) com o Azure AD, veja [início de sessão único para aplicações no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Pré-requisitos

Para integrar o Azure AD com o Portal de cativas de redes da Palo Alto, precisa do seguinte:

* Uma subscrição do Azure Active Directory. Se não tiver o Azure AD, pode obter um [versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).
* Um Portal cativas de redes da Palo Alto início de sessão único (SSO)-ativada a subscrição.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

Portal de cativas da Palo Alto redes suporta estes cenários:

* **IDP iniciada pelo início de sessão único**
* **Aprovisionamento de utilizadores de just-in-time**

## <a name="add-palo-alto-networks-captive-portal-from-the-gallery"></a>Adicionar cativas de redes da Palo Alto Portal a partir da Galeria

Para começar a utilizar, na galeria, adicione cativas de redes da Palo Alto Portal à sua lista de aplicações de SaaS geridas:

1. Na [portal do Azure](https://portal.azure.com), no menu da esquerda, selecione **Azure Active Directory**.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Selecione **aplicações empresariais** > **todas as aplicações**.

    ![A opção de aplicações empresariais no menu](common/enterprise-applications.png)

3. Selecione **Nova aplicação**.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, introduza **cativas de redes da Palo Alto Portal**. Nos resultados da pesquisa, selecione **Palo Alto Networks - Portal cativas**e, em seguida, selecione **Add**.

     ![Da Palo Alto Networks - Portal cativas na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Configure e teste do Azure AD início de sessão único com o Portal de cativas de redes da Palo Alto com base num utilizador de teste com o nome *Eduarda Almeida*. Para o início de sessão único funcione, tem de estabelecer uma relação entre um utilizador do Azure AD e o mesmo utilizador no Portal de cativas de redes da Palo Alto. 

Para configurar e testar o Azure AD início de sessão único com o Portal de cativas da Palo Alto redes, conclua as seguintes tarefas:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**: Permitir que o utilizador utilizar esta funcionalidade.
2. **[Configurar Portal de cativas da Palo Alto redes início de sessão único](#configure-palo-alto-networks-captive-portal-single-sign-on)**: Configure as definições de início de sessão únicas no aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**: Teste do Azure AD início de sessão único com o usuário *Eduarda Almeida*.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**: Defina Eduarda Almeida para utilizar o Azure AD início de sessão único.
5. **Criar um utilizador de teste do Portal de cativas da Palo Alto redes**: Criar um utilizador de contraparte *Eduarda Almeida* da Palo Alto redes Portal cativas que está ligada ao utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**: Certifique-se de que a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Em primeiro lugar, ative o Azure AD início de sessão único no portal do Azure:

1. Na [portal do Azure](https://portal.azure.com/), na **Palo Alto Networks - Portal cativas** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Na **selecionar um método de início de sessão único** painel, selecione **SAML**.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Na **definir a segurança de início de sessão único com o SAML** painel, selecionar o lápis **editar** ícone.

    ![O ícone de edição penciled](common/edit-urls.png)

4. Na **configuração básica de SAML** painel, conclua os seguintes passos:

    ![Painel da Palo Alto redes cativas Portal SAML configuração básica](common/idp-intiated.png)

   1. Para **identificador**, introduza um URL com o padrão `https://<customer_firewall_host_name>/SAML20/SP`.

   2. Para **URL de resposta**, introduza um URL com o padrão `https://<customer_firewall_host_name>/SAML20/SP/ACS`.

      > [!NOTE]
      > Atualize os valores de marcador de posição neste passo com o identificador real e URLs de resposta. Para obter os valores reais, contacte [equipa de suporte da Palo Alto redes cativas Portal cliente](https://support.paloaltonetworks.com/support).

5. Na **certificado de assinatura SAML** secção, junto a **XML de metadados de Federação**, selecione **transferir**. Guarde o ficheiro transferido no seu computador.

    ![Ligação de transferência do XML de metadados de Federação](common/metadataxml.png)

### <a name="configure-palo-alto-networks-captive-portal-single-sign-on"></a>Configurar Portal de cativas da Palo Alto redes início de sessão único

Em seguida, configure o início de sessão único no Portal de cativas da Palo Alto redes:

1. Numa janela do browser diferente, inicie sessão no site da Palo Alto Networks como um administrador.

2. Selecione o **dispositivo** separador.

    ![O separador de dispositivo da Palo Alto Networks Web site](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin1.png)

3. No menu, selecione **fornecedor de identidade de SAML**e, em seguida, selecione **importação**.

    ![O botão Importar](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin2.png)

4. Na **importação de perfil do servidor de fornecedor de identidade da SAML** diálogo caixa, conclua os seguintes passos:

    ![Configurar da Palo Alto Networks início de sessão único](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin3.png)

    1. Para **nome do perfil**, introduza um nome, como **AzureAD CaptivePortal**.
    
    2. Junto a **metadados de fornecedor de identidade**, selecione **procurar**. Selecione o arquivo METADATA XML que transferiu no portal do Azure.
    
    3. Selecione **OK**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD 

Em seguida, crie um utilizador de teste com o nome *Eduarda Almeida* no portal do Azure:

1. No portal do Azure, selecione **do Azure Active Directory** > **utilizadores** > **todos os utilizadores**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](common/users.png)

2. Selecione **novo utilizador**.

    ![O novo botão de utilizador](common/new-user.png)

3. Na **utilizador** painel, conclua os seguintes passos:

    ![A caixa de diálogo de utilizador](common/user-properties.png)

    1. Para **Name**, introduza **BrittaSimon**.
  
    2. Para **nome de utilizador**, introduza **BrittaSimon\@\<your_company_domain\>**. Por exemplo, **BrittaSimon\@contoso.com**.

    3. Para **palavra-passe**, introduza uma palavra-passe. Recomendamos que mantenha um registo da palavra-passe que introduzir. Pode selecionar o **mostrar palavra-passe** caixa de verificação para apresentar a palavra-passe.

    4. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Em seguida, conceda acesso ao Portal cativas de redes da Palo Alto modo Eduarda Almeida pode utilizar o Azure início de sessão único:

1. No portal do Azure, selecione **aplicações empresariais** > **todas as aplicações**.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, introduza **Palo Alto Networks - Portal cativas**e, em seguida, selecione a aplicação.

    ![Da Palo Alto Networks - ligação de Portal cativas na lista de aplicações](common/all-applications.png)

3. No menu, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Selecione **adicionar utilizador**. Em seguida, na **adicionar atribuição** painel, selecione **utilizadores e grupos**.

    ![O painel de atribuição de adicionar](common/add-assign-user.png)

5. Na **utilizadores e grupos** painel, na **utilizadores** lista, selecione **Eduarda Almeida**. Selecione **selecione**.

6. Adicionar um valor de função para a asserção de SAML, o **selecionar função** painel, selecione a função relevante para o utilizador. Selecione **selecione**.

7. Na **adicionar atribuição** painel, selecione **atribuir**.

### <a name="create-a-palo-alto-networks-captive-portal-test-user"></a>Criar um utilizador de teste do Portal de cativas de redes da Palo Alto

Em seguida, crie um utilizador com o nome *Eduarda Almeida* no Portal de cativas de redes da Palo Alto. Portal de cativas de redes da Palo Alto suporta o aprovisionamento de utilizadores de just-in-time, que está ativado por predefinição. Não precisa de concluir eventuais tarefas nesta secção. Se um utilizador já não existe no Portal de cativas de redes da Palo Alto, é criado um novo após a autenticação.

> [!NOTE]
> Se quiser criar manualmente um utilizador, entre em contato com o [equipa de suporte da Palo Alto redes cativas Portal cliente](https://support.paloaltonetworks.com/support).

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Portal de cativas de redes da Palo Alto é instalado atrás da firewall numa VM do Windows. Para testar o início de sessão único no Portal de cativas de redes da Palo Alto, inicie sessão para a VM do Windows utilizando o protocolo RDP (Remote Desktop). Na sessão do RDP, abra um browser e aceda a qualquer Web site. O URL de SSO é aberto e lhe for pedido para se autenticar. Quando a autenticação for concluída, pode acessar os Web sites.

## <a name="additional-resources"></a>Recursos adicionais

Para obter mais informações, veja estes artigos:

- [Tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Início de sessão único para aplicações no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Acesso condicional no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

