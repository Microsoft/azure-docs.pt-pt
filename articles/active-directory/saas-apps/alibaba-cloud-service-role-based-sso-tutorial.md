---
title: 'Tutorial: Integração do Active Directory do Azure com o serviço de Cloud Alibaba (SSO baseado em funções) | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e o serviço de Cloud Alibaba (SSO baseado em funções).
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3667841e-acfc-4490-acf5-80d9ca3e71e8
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/02/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8de2f7cb90e004673c59282a8023d55df364220a
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65140694"
---
# <a name="tutorial-azure-active-directory-integration-with-alibaba-cloud-service-role-based-sso"></a>Tutorial: Integração do Active Directory do Azure com o serviço de Cloud Alibaba (SSO baseado em funções)

Neste tutorial, saiba como integrar o serviço de Cloud Alibaba (SSO baseado em funções) no Azure Active Directory (Azure AD).
Integrar o serviço de Cloud Alibaba (SSO baseado em funções) no Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao serviço de Cloud Alibaba (SSO baseado em funções).
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para o serviço de Cloud de Alibaba (SSO baseado em funções) (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o serviço de Cloud Alibaba (SSO baseado em funções), terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter um [conta gratuita](https://azure.microsoft.com/free/)
* Serviço de Cloud Alibaba (SSO baseado em funções) início de sessão único de subscrição ativada

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Serviço de nuvem Alibaba (SSO baseado em funções) suporta **IDP** iniciada SSO

## <a name="adding-alibaba-cloud-service-role-based-sso-from-the-gallery"></a>Adicionar serviço de nuvem de Alibaba (baseado em funções SSO) partir da Galeria

Para configurar a integração do serviço em nuvem do Alibaba (SSO baseado em funções) no Azure AD, terá de Adicionar serviço de Cloud Alibaba (baseado em funções SSO) a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar serviço de Cloud Alibaba (baseado em funções SSO) a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **Alibaba Cloud Service (SSO baseado em funções)**, selecione **Alibaba Cloud Service (SSO baseado em funções)** no painel de resultados, em seguida, clique em **Add** botão para adicionar o aplicação.

    ![Serviço de Cloud Alibaba (SSO baseado em funções) na lista de resultados](common/search-new-app.png)

5. Sobre o **Alibaba Cloud Service (SSO baseado em funções)** página, clique em **propriedades** no painel de navegação da esquerda e copie o **ID de objeto** e guarde-o no seu computador para uso subseqüente.

    ![Configuração de propriedades](./media/alibaba-cloud-service-role-based-sso-tutorial/Properties.png)
    
## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configurar e testar o Azure AD início de sessão único com o serviço de Cloud Alibaba (baseado em funções SSO) com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no serviço de Cloud Alibaba (SSO baseado em funções) deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com o serviço de Cloud Alibaba (SSO baseado em funções), tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configure Role-Based início de sessão único no serviço de Cloud Alibaba](#configure-role-based-single-sign-on-in-alibaba-cloud-service)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar o serviço de Cloud Alibaba (SSO baseado em funções) início de sessão único](#configure-alibaba-cloud-service-role-based-sso-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste do serviço de Cloud Alibaba (SSO baseado em funções)](#create-alibaba-cloud-service-role-based-sso-test-user)**  - para ter um equivalente da Eduarda Almeida no serviço de Cloud de Alibaba (SSO baseado em funções) que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com o serviço de Cloud Alibaba (SSO baseado em funções), execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **Alibaba Cloud Service (SSO baseado em funções)** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, se tiver **ficheiro de metadados do fornecedor de serviços**, execute os seguintes passos:

    >[!NOTE]
    >Obterá os metadados do fornecedor de serviços nesta [URL](https://signin.alibabacloud.com/saml-role/sp-metadata.xml)

    a. Clique em **carregamento de ficheiro de metadados**.

    ![image](common/upload-metadata.png)

    b. Clique em **logótipo da pasta** para selecionar o ficheiro de metadados e clique em **carregar**.

    ![image](common/browse-upload-metadata.png)

    c. Depois do ficheiro de metadados é carregado com êxito, o **identificador** e **URL de resposta** automaticamente povoada na caixa de texto do serviço de Cloud Alibaba (SSO baseado em funções) secção de introdução de valores:

    ![image](common/idp-intiated.png)

    > [!Note]
    > Se o **identificador** e **URL de resposta** valores não obter preenchida automaticamente, em seguida, preencha os valores manualmente de acordo com seus requisitos.

5. Aplicação de serviço de nuvem Alibaba (SSO baseado em funções) espera que as asserções SAML num formato específico, o que requer a adição de mapeamentos de atributos personalizado à sua configuração de atributos de token SAML. Captura de ecrã seguinte mostra a lista de atributos predefinidos. Clique em **edite** ícone para abrir **atributos de utilizador** caixa de diálogo.

    ![image](common/edit-attribute.png)

6. Além dos acima, a aplicação de serviço de nuvem Alibaba (SSO baseado em funções) espera mais alguns atributos a serem passados na resposta SAML. No **afirmações de utilizador** secção sobre o **atributos de utilizador** caixa de diálogo, execute os seguintes passos para adicionar o atributo de token de SAML conforme mostrado na tabela a seguir:

    | Name | Espaço de nomes | Atributo de origem|
    | ---------------| ------------| --------------- |
    | Função | https://www.aliyun.com/SAML-Role/Attribute | user.assignedroles |
    | RoleSessionName | https://www.aliyun.com/SAML-Role/Attribute | user.userprincipalname |

    > [!NOTE]
    > Clique [aqui](https://docs.microsoft.com/azure/active-directory/develop/active-directory-enterprise-app-role-management) saber como configurar **função** no Azure AD

    a. Clique em **Adicionar nova afirmação** para abrir o **afirmações de utilizador de gerir** caixa de diálogo.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Na **nome** caixa de texto, escreva o nome de atributo apresentado para essa linha.

    c. Deixe o **espaço de nomes** em branco.

    d. Selecione a origem de dado **atributo**.

    e. Partir do **atributo de origem** lista, digite o valor de atributo apresentado para essa linha.

    f. Clique em **Guardar**.

7. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **XML de metadados de Federação**  entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/metadataxml.png)

8. Sobre o **configurar o serviço de nuvem de Alibaba (SSO baseado em funções)** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão

### <a name="configure-role-based-single-sign-on-in-alibaba-cloud-service"></a>Configurar com base em função de início de sessão único num serviço Alibaba Cloud

1. Inicie sessão para a Alibaba Cloud [consola de RAM](https://account.alibabacloud.com/login/login.htm?oauth_callback=https%3A%2F%2Fram.console.aliyun.com%2F%3Fspm%3Da2c63.p38356.879954.8.7d904e167h6Yg9) utilizando Account1.

2. No painel de navegação do lado esquerdo, selecione **SSO**.

3. Sobre o **SSO baseado em funções** separador, clique em **criar IdP**.

4. Na página apresentada, introduza `AAD` no campo de nome de IdP, introduza uma descrição na **nota** campo, clique em **carregar** para carregar o ficheiro de metadados de federação que transferiu anteriormente e clique em  **OK**.

5. Depois do IdP for criada com êxito, clique em **criar função de RAM**.

6. Na **nome da função de RAM** campo introduza `AADrole`, selecione `AAD` do **selecionar IdP** pendente lista e clique em OK.

    >[!NOTE]
    >Pode conceder permissão para a função, conforme necessário. Depois de criar o IdP e a função correspondente, recomendamos que guarde ARNs de IdP e a função para uma utilização posterior. Pode obter os ARNs na página de informações do IdP e a página de informações de função.

7. Associe a função de Alibaba Cloud RAM (AADrole) com o utilizador do Azure AD (u2): Para associar a função de RAM com o utilizador do Azure AD, tem de criar uma função no Azure AD através dos seguintes passos:

    a. Inicie sessão para o [do Azure AD Graph](https://developer.microsoft.com/graph/graph-explorer?spm=a2c63.p38356.879954.9.7d904e167h6Yg9).

    b. Clique em **modificar permissões** para obter as permissões necessárias para a criação de uma função.

    ![Configuração de gráfico](./media/alibaba-cloud-service-role-based-sso-tutorial/graph01.png)

    c. Selecione as seguintes permissões da lista e clique em **modificar permissões**, conforme mostrado na figura a seguir.

    ![Configuração de gráfico](./media/alibaba-cloud-service-role-based-sso-tutorial/graph02.png)

    >[!NOTE]
    >Depois das permissões são concedidas, inicie sessão no Explorador do gráfico novamente.

    d. Na página de API do Graph, selecione **Obtenha** na primeira lista pendente e **beta** na segunda lista pendente. Em seguida, introduza `https://graph.microsoft.com/beta/servicePrincipals` no campo ao lado de listas pendentes e clique em **executar consulta**.

    ![Configuração de gráfico](./media/alibaba-cloud-service-role-based-sso-tutorial/graph03.png)

    >[!NOTE]
    >Se estiver a utilizar vários diretórios, pode introduzir `https://graph.microsoft.com/beta/contoso.com/servicePrincipals` no campo da consulta.

    e. Na **pré-visualização de resposta** secção, extrair a propriedade appRoles do ' Principal de serviço "para uma utilização posterior.

    ![Configuração de gráfico](./media/alibaba-cloud-service-role-based-sso-tutorial/graph05.png)

    >[!NOTE]
    >Pode localizar a propriedade appRoles introduzindo `https://graph.microsoft.com/beta/servicePrincipals/<objectID>` no campo da consulta. Tenha em atenção que o `objectID` é o ID de objeto que a tiver copiado do Azure AD **propriedades** página.

    f. Voltar ao Explorador do gráfico, alterar o método de **Obtenha** para **aplicar o PATCH**, cole o seguinte conteúdo para o **corpo do pedido** secção e clique em **executar consulta** :
    ```
    { 
    "appRoles": [
        { 
        "allowedMemberTypes":[
            "User"
        ],
        "description": "msiam_access",
        "displayName": "msiam_access",
        "id": "41be2db8-48d9-4277-8e86-f6d22d35****",
        "isEnabled": true,
        "origin": "Application",
        "value": null
        },
        { "allowedMemberTypes": [
            "User"
        ],
        "description": "Admin,AzureADProd",
        "displayName": "Admin,AzureADProd",
        "id": "68adae10-8b6b-47e6-9142-6476078cdbce",
        "isEnabled": true,
        "origin": "ServicePrincipal",
        "value": "acs:ram::187125022722****:role/aadrole,acs:ram::187125022722****:saml-provider/AAD"
        }
    ]
    }
    ```
    > [!NOTE]
    > O `value` é ARNs de IdP e a função que criou na consola de RAM. Aqui, pode adicionar várias funções conforme necessário. Azure AD irá enviar o valor de uma destas funções, como o valor de afirmação na resposta SAML. No entanto, só é possível adicionar novas funções depois do `msiam_access` parte para a operação de correção. Para suavizar o processo de criação, recomendamos que utilize um gerador de ID, como o gerador de GUID, para gerar IDs em tempo real.

    g. Depois do ' Principal de serviço ' for corrigido com a função necessária, anexar a função com o utilizador do Azure AD (u2) ao seguir os passos de **atribua o utilizador de teste do Azure AD** secção do tutorial.

### <a name="configure-alibaba-cloud-service-role-based-sso-single-sign-on"></a>Configurar Alibaba Cloud Service (SSO baseado em funções) início de sessão único

Para configurar o início de sessão único num **Alibaba Cloud Service (SSO baseado em funções)** lado, terá de enviar o transferido **XML de metadados de Federação** e copiados URLs partir do portal do Azure para [ Equipa de suporte do serviço de Cloud Alibaba (SSO baseado em funções)](https://www.aliyun.com/service/). Se definir esta definição para que a ligação de SAML SSO definidas corretamente em ambos os lados.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD 

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

1. No portal do Azure, no painel esquerdo, selecione **do Azure Active Directory**, selecione **utilizadores**e, em seguida, selecione **todos os utilizadores**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](common/users.png)

2. Selecione **novo utilizador** na parte superior do ecrã.

    ![Novo utilizador botão](common/new-user.png)

3. Nas propriedades do utilizador, execute os seguintes passos.

    ![A caixa de diálogo de utilizador](common/user-properties.png)

    a. Na **Name** , insira **BrittaSimon**.
  
    b. Na **nome de utilizador** , digite `brittasimon@yourcompanydomain.extension`. Por exemplo, BrittaSimon@contoso.com

    c. Selecione **palavra-passe de Show** caixa de verificação e, em seguida, anote o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único, concedendo acesso ao serviço de nuvem Alibaba (SSO baseado em funções).

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **Alibaba Cloud Service (SSO baseado em funções)**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Alibaba Cloud Service (SSO baseado em funções)**.

    ![A ligação de serviço de Cloud Alibaba (SSO baseado em funções) na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Sobre o **utilizadores e grupos** separador, selecione u2 da lista de utilizadores e clique em **selecione**. Em seguida, clique em **atribuir**.

    ![Configuração de teste](./media/alibaba-cloud-service-role-based-sso-tutorial/test01.png)

6. Ver a função atribuída e testar o serviço de Cloud Alibaba (SSO baseado em funções).

    ![Configuração de teste](./media/alibaba-cloud-service-role-based-sso-tutorial/test02.png)

    >[!NOTE]
    >Depois de atribuir o utilizador (u2), a função criada automaticamente é anexada ao usuário. Se o ter criado várias funções, terá de anexar a função adequada para o usuário conforme necessário. Se pretender implementar SSO baseado em funções do Azure AD para várias contas de Alibaba Cloud, repita os passos anteriores.

### <a name="create-alibaba-cloud-service-role-based-sso-test-user"></a>Criar utilizador de teste do serviço de Cloud Alibaba (SSO baseado em funções)

Nesta secção, vai criar um usuário chamado Eduarda Almeida no serviço de Cloud Alibaba (SSO baseado em funções). Trabalhar com [equipa de suporte do serviço de Cloud Alibaba (SSO baseado em funções)](https://www.aliyun.com/service/) para adicionar os utilizadores na plataforma do serviço de Cloud Alibaba (SSO baseado em funções). Os utilizadores tem de ser criados e ativados antes de utilizar o início de sessão único.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Depois das configurações anteriores são concluídas, teste o serviço de nuvem de Alibaba (SSO baseado em funções) ao seguir estes passos:

1. No portal do Azure, vá para o **Alibaba Cloud Service (SSO baseado em funções)** página, selecione **início de sessão único**e clique em **teste**.

    ![Configuração de teste](./media/alibaba-cloud-service-role-based-sso-tutorial/test03.png)

2. Clique em **Iniciar sessão como o utilizador atual**.

    ![Configuração de teste](./media/alibaba-cloud-service-role-based-sso-tutorial/test04.png)

3. Na página de seleção de conta, selecione u2.

    ![Configuração de teste](./media/alibaba-cloud-service-role-based-sso-tutorial/test05.png)

4. É apresentada a página seguinte, que indica que o SSO baseado em funções é efetuada com êxito.

    ![Configuração de teste](./media/alibaba-cloud-service-role-based-sso-tutorial/test06.png)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

