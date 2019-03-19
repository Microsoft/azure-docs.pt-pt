---
title: 'Tutorial: Integração do Active Directory do Azure com a área de trabalho pelo Facebook | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e à área de trabalho pelo Facebook.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 30f2ee64-95d3-44ef-b832-8a0a27e2967c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/12/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 27173c8beeecf2be43e80f59df8907952734c06b
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57900884"
---
# <a name="tutorial-azure-active-directory-integration-with-workplace-by-facebook"></a>Tutorial: Integração do Active Directory do Azure com a área de trabalho pelo Facebook

Neste tutorial, saiba como integrar à área de trabalho pelo Facebook com o Azure Active Directory (Azure AD).
Integrar à área de trabalho pelo Facebook com o Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso à área de trabalho pelo Facebook.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para a área de trabalho pelo Facebook (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com à área de trabalho pelo Facebook, precisa do seguinte:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter a versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Área de trabalho pelo Facebook logon único habilitado subscrição

> [!NOTE]
> Para testar os passos neste tutorial, recomendamos que não utilize um ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

> [!NOTE]
> Facebook tem dois produtos, à área de trabalho padrão (gratuito) e Premium à área de trabalho (paga). Nenhum inquilino à área de trabalho Premium pode configurar a integração de SCIM e SSO com outras implicações de custo ou licenças necessárias. SSO e SCIM não estão disponíveis em instâncias à área de trabalho padrão.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Oferece suporte a área de trabalho pelo Facebook **SP** iniciada SSO
* Oferece suporte a área de trabalho pelo Facebook **just-in-time de aprovisionamento**
* Oferece suporte a área de trabalho pelo Facebook  **[aprovisionamento automático de utilizador](workplacebyfacebook-provisioning-tutorial.md)**

## <a name="adding-workplace-by-facebook-from-the-gallery"></a>Adicionar à área de trabalho pelo Facebook a partir da Galeria

Para configurar a integração de área de trabalho pelo Facebook com o Azure AD, terá de adicionar à área de trabalho pelo Facebook a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar à área de trabalho pelo Facebook a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **à área de trabalho pelo Facebook**, selecione **à área de trabalho pelo Facebook** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![Área de trabalho pelo Facebook na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configurar e testar o Azure AD início de sessão único à área de trabalho pelo Facebook com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado na área de trabalho pelo Facebook deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único à área de trabalho pelo Facebook, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar área de trabalho pelo Facebook início de sessão único](#configure-workplace-by-facebook-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar área de trabalho por utilizador de teste do Facebook](#create-workplace-by-facebook-test-user)**  - para ter um equivalente da Eduarda Almeida na área de trabalho pelo Facebook, que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único à área de trabalho pelo Facebook, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **à área de trabalho pelo Facebook** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, execute os seguintes passos:

    ![Área de trabalho ao domínio do Facebook e URLs únicas início de sessão em informações](common/sp-identifier.png)

    a. Na **iniciar sessão no URL** caixa de texto, escreva um URL com o seguinte padrão: `https://<instancename>.facebook.com`

    b. Na **identificador (ID de entidade)** caixa de texto, escreva um URL com o seguinte padrão: `https://www.facebook.com/company/<instanceID>`

    > [!NOTE] 
    > Estes valores não são o real. Atualize estes valores com o URL de início de sessão e o identificador real. Consulte a página de autenticação do Dashboard à área de trabalho da empresa para os valores corretos para sua Comunidade local de trabalho.

5. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **certificado (Base64)** entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/certificatebase64.png)

6. Sobre o **configurar à área de trabalho pelo Facebook** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Azure Ad Identifier

    c. URL de fim de sessão

### <a name="configure-workplace-by-facebook-single-sign-on"></a>Configurar área de trabalho pelo Facebook Single Sign-On

1. Numa janela do browser web diferente, início de sessão à sua área de trabalho por site de empresa do Facebook como administrador.
  
    > [!NOTE]
    > Como parte do processo de autenticação SAML, à área de trabalho pode utilizar cadeias de caracteres de consulta de até 2,5 quilobytes de tamanho para passar parâmetros para o Azure AD.

2. Na **painel de administração**, aceda ao **segurança** separador.

    ![Painel de administração](./media/workplacebyfacebook-tutorial/tutorial-workplace-by-facebook-configure01.png)

3. Sob **autenticação** separador, selecione **início de sessão único (SSO)** e execute os seguintes passos:

    ![Separador de autenticação](./media/workplacebyfacebook-tutorial/tutorial-workplace-by-facebook-configure02.png)

    a. Na **URL de SAML** caixa de texto, cole o valor de **URL de início de sessão**, que copiou do portal do Azure.

    b. Na **caixa de texto do URI de emissor de SAML**, cole o valor de **Azure Ad identificador**, que copiou do portal do Azure.

    c. Na **redirecionamento de fim de sessão de SAML** (opcional), cole o valor de **URL de fim de sessão**, que copiou do portal do Azure.

    d. Abra sua **certificado com codificação base 64** no bloco de notas transferido a partir do portal do Azure, copie o conteúdo do mesmo para a área de transferência e, em seguida, cole-os para o **certificado SAML** caixa de texto.

    e. Cópia a **URL de público-alvo** para a sua instância e cole-o na **identificador (ID de entidade)** caixa de texto no **configuração básica de SAML** secção no portal do Azure.

    f. Cópia a **URL de destinatário** para a sua instância e cole-o na **iniciar sessão no URL** caixa de texto no **configuração básica de SAML** secção no portal do Azure.

    g. Desloque para baixo da seção e clique nas **SSO de teste** botão. O resultado é uma janela de pop-up que aparece com página de início de sessão do Azure AD apresentados. Introduza as credenciais em como normal para autenticar.

    **Resolução de problemas:** Certifique-se de que o endereço de e-mail a ser devolvido do Azure AD é o mesmo que tiver iniciado sessão com a conta de área de trabalho.

    h. Quando o teste estiver concluído com êxito, desloque-se para a parte inferior da página e clique nas **guardar** botão.

    i. Todos os utilizadores usem à área de trabalho serão agora apresentados com página de início de sessão do Azure AD para autenticação.

4. **Redirecionamento de fim de sessão de SAML (opcional)** -

    Pode optar por configurar, opcionalmente, um Url de fim de sessão SAML, que podem ser usados para apontar para a página de fim de sessão do Azure AD. Quando esta definição está ativada e configurada, o utilizador já não será direcionado para a página de fim de sessão de área de trabalho. Em vez disso, o utilizador será redirecionado para o url que foi adicionado na definição de redirecionamento de fim de sessão de SAML.

### <a name="configuring-reauthentication-frequency"></a>Configurar a frequência de reautenticação

Pode configurar à área de trabalho para solicitar uma verificação SAML cada dia, três dias, semana, duas semanas, meses ou nunca.

> [!NOTE]
> O valor mínimo para a verificação SAML em aplicativos móveis está definido para uma semana.

Também pode forçar um SAML repor para todos os utilizadores com o botão: Exigir a autenticação de SAML para todos os utilizadores agora.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

1. No portal do Azure, no painel esquerdo, selecione **do Azure Active Directory**, selecione **utilizadores**e, em seguida, selecione **todos os utilizadores**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](common/users.png)

2. Selecione **novo utilizador** na parte superior do ecrã.

    ![Novo utilizador botão](common/new-user.png)

3. Nas propriedades do utilizador, execute os seguintes passos.

    ![A caixa de diálogo de utilizador](common/user-properties.png)

    a. Na **Name** campo introduza **BrittaSimon**.
  
    b. Na **nome de utilizador** tipo de campo **brittasimon\@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione **palavra-passe de Show** caixa de verificação e, em seguida, anote o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso à área de trabalho pelo Facebook.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **à área de trabalho pelo Facebook**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, escreva e selecione **à área de trabalho pelo Facebook**.

    ![Área de trabalho pelo Facebook link na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-workplace-by-facebook-test-user"></a>Criar área de trabalho por utilizador de teste do Facebook

Nesta secção, um usuário chamado Eduarda Almeida é criado na área de trabalho pelo Facebook. Área de trabalho pelo Facebook suporta o aprovisionamento de just-in-time, que está ativado por predefinição.

Não existe nenhuma ação para nesta secção. Se um utilizador não existir na área de trabalho pelo Facebook, uma nova é criada quando tentar aceder à área de trabalho pelo Facebook.

>[!Note]
>Se precisar de criar manualmente um utilizador, contacte o [à área de trabalho pela equipa de suporte de cliente do Facebook](https://workplace.fb.com/faq/)

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Ao clicar o local de trabalho ao mosaico do Facebook no painel de acesso, deve ser automaticamente conectado à área de trabalho pelo Facebook para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Configurar o aprovisionamento do utilizador](workplacebyfacebook-provisioning-tutorial.md)
